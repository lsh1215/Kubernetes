# Docker Internals: namespace, cgroup, chroot, image layer

이 문서는 44bits의 Docker/컨테이너 관련 글과 현재 학습 대화 내용을 바탕으로 정리한 학습 노트다. 원문을 복제하지 않고, 나중에 다시 읽으며 직접 실습할 수 있도록 개념의 연결과 명령어 흐름을 재구성했다.

## Sources

- [도커 이미지 빌드와 Dockerfile 기초 commit, diff 그리고 Dockerfile](https://www.44bits.io/ko/post/building-docker-image-basic-commit-diff-and-dockerfile)
- [UTS 네임스페이스를 사용한 호스트네임 격리 컨테이너 네트워크 기초 1편](https://www.44bits.io/ko/post/container-network-1-uts-namespace)
- [[키워드] 리눅스 네임스페이스(Linux Namespace)란?](https://www.44bits.io/ko/keyword/linux-namespace)
- [ip로 직접 만들어보는 네트워크 네임스페이스와 브리지 네트워크 컨테이너 네트워크 기초 2편](https://www.44bits.io/ko/post/container-network-2-ip-command-and-network-namespace)
- [만들면서 이해하는 도커(Docker) 이미지의 구조 도커 이미지 빌드 원리와 OverlayFS](https://www.44bits.io/ko/post/how-docker-image-work)

## 먼저 잡을 큰 그림

컨테이너는 작은 가상 머신이 아니다. 더 정확히는 **Linux 커널 위에서 실행되는 프로세스**인데, 그 프로세스가 자기만의 작은 Linux 환경 안에 있는 것처럼 보이도록 여러 커널 기능을 조합한 것이다.

핵심 구성은 다음처럼 생각하면 된다.

```text
container
= process
+ namespaces
+ cgroups
+ root filesystem
+ image layers
+ security constraints
```

각 요소의 역할은 다르다.

| 구성 요소 | 해결하는 문제 | 핵심 감각 |
| --- | --- | --- |
| namespace | 프로세스가 보는 세계를 분리 | "내 PID 1, 내 localhost, 내 hostname, 내 /" |
| cgroup | 자원 사용량 제한/측정 | "CPU/메모리는 여기까지만" |
| chroot/pivot_root | 프로세스가 보는 루트 파일시스템 변경 | "이 디렉터리를 / 처럼 보라" |
| OverlayFS/union mount | 이미지 레이어를 하나의 파일시스템처럼 합성 | "읽기 전용 레이어 + 쓰기 레이어" |
| Docker image | 컨테이너 실행용 파일시스템과 메타데이터 | "실행 환경의 재현 가능한 재료" |

Kubernetes를 공부할 때도 이 기반이 중요하다. Kubernetes는 컨테이너를 직접 새로 발명한 것이 아니라, 컨테이너 런타임을 통해 이런 Linux 기능 위에서 Pod, Deployment, Service 같은 운영 추상화를 만든다.

## Chapter 1. Docker image는 무엇인가

Docker image는 VM image처럼 전체 머신 상태를 저장한 것이 아니다. 핵심은 **컨테이너가 루트 파일시스템으로 사용할 파일들의 집합과 실행 메타데이터**다.

이미지는 다음을 포함한다.

- `/bin`, `/etc`, `/usr`, `/app` 같은 파일시스템 내용
- 기본 실행 명령, 환경 변수, 작업 디렉터리 같은 메타데이터
- 여러 개의 읽기 전용 레이어

이미지는 다음을 포함하지 않는다.

- 실행 중인 프로세스의 메모리 상태
- 호스트 커널 자체
- 별도의 완전한 가상 머신 하드웨어

예를 들어 Ubuntu 호스트에서 Amazon Linux 이미지를 실행하면 컨테이너 안에서는 Amazon Linux처럼 보일 수 있다. 하지만 `uname`으로 확인되는 커널은 호스트의 Linux 커널이다. 이것이 컨테이너와 VM의 중요한 차이다.

```text
Host kernel: shared
Container rootfs: image-specific
```

즉 컨테이너의 "배포판처럼 보이는 성질"은 커널이 달라서가 아니라, 프로세스가 보는 root filesystem이 다르기 때문에 생긴다.

## Chapter 2. chroot는 컨테이너의 조상격 아이디어

`chroot`는 프로세스가 보는 `/`를 다른 디렉터리로 바꾸는 기능이다.

```text
host filesystem:
/tmp/rootfs/bin
/tmp/rootfs/etc

chroot process view:
/bin
/etc
```

이 기능만으로도 프로세스는 호스트 전체 파일시스템을 보는 대신 특정 디렉터리 아래만 자기 세계처럼 볼 수 있다.

하지만 `chroot`만으로 컨테이너가 되지는 않는다.

`chroot`가 해주는 것:

- 루트 디렉터리 변경

`chroot`가 해주지 않는 것:

- 네트워크 격리
- PID 격리
- hostname 격리
- CPU/메모리 제한
- 사용자/권한 격리
- 이미지 레이어 관리

그래서 컨테이너는 `chroot`의 아이디어를 포함하지만, 훨씬 더 많은 기능을 조합한다.

```text
container rootfs
-> chroot/pivot_root 계열로 / 처럼 보이게 함
-> mount namespace로 마운트 테이블 격리
-> pid/network/uts/user namespace로 다른 시스템 자원 격리
-> cgroup으로 자원 사용량 제한
```

## Chapter 3. namespace를 어떻게 생각해야 하는가

namespace는 **프로세스가 어떤 이름을 어떤 자원으로 해석하는지를 결정하는 범위**다.

여기서 name은 단순히 문자열 이름만 뜻하지 않는다. PID 번호, 파일 경로, 네트워크 인터페이스 이름, 포트 번호, hostname, UID/GID 같은 식별자를 넓게 포함한다.

예를 들어 `PID 1`이라는 이름은 namespace에 따라 다른 프로세스를 가리킬 수 있다.

```text
Host PID namespace:
PID 1 = systemd

Container PID namespace:
PID 1 = app process
```

`localhost:8080`도 마찬가지다.

```text
Host network namespace:
127.0.0.1:8080 = host network stack의 8080

Container network namespace:
127.0.0.1:8080 = container network stack의 8080
```

따라서 namespace는 "격리된 공간"이라고 이해해도 되지만, 더 정확히는 **식별자가 해석되는 커널 자원 공간**이다.

Linux namespace의 대표 종류:

| Namespace | 분리되는 것 |
| --- | --- |
| UTS | hostname, domain name |
| PID | 프로세스 ID 공간 |
| Network | 인터페이스, IP, 라우팅 테이블, 포트 공간 |
| Mount | 마운트 테이블, 파일시스템 view |
| IPC | System V IPC, POSIX message queue 등 |
| User | UID/GID 매핑 |
| Cgroup | cgroup 경로 view |

## Chapter 4. UTS namespace와 hostname

UTS는 `Unix Time-sharing System`의 약자다. 현대적으로 보면 hostname namespace라고 생각하는 편이 더 직관적이다.

UTS namespace는 프로세스가 보는 다음 값을 분리한다.

- hostname
- domainname

예를 들어 호스트의 hostname이 `host-a`여도, UTS namespace를 분리한 프로세스는 자기 hostname을 `container-a`처럼 볼 수 있다.

```text
Host UTS namespace:
hostname = host-a

New UTS namespace:
hostname = container-a
```

컨테이너 안에서 `hostname`을 실행했을 때 컨테이너 ID나 Pod 이름이 나오는 이유가 여기에 있다.

중요한 점:

- hostname은 IP 주소가 아니다.
- hostname은 시스템이 자신을 식별하기 위한 이름이다.
- 다른 시스템이 그 이름으로 접근하려면 DNS나 `/etc/hosts` 같은 이름 해석이 별도로 필요하다.

## Chapter 5. namespace의 영속화는 파일 저장이 아니다

`unshare` 실습에서 namespace를 저장하기 위한 빈 파일을 만들 수 있다. 이 표현은 오해를 만들기 쉽다.

namespace는 디스크에 저장되는 설정 파일이 아니다. namespace는 커널 안에 존재하는 객체이고, 기본적으로 그 namespace를 사용하는 프로세스가 살아 있는 동안 유지된다.

```text
namespace 생성
-> 프로세스가 사용
-> 마지막 프로세스와 마지막 참조가 사라짐
-> namespace 사라짐
```

그런데 나중에 같은 namespace에 다시 들어가고 싶으면 namespace 객체에 대한 참조를 남겨야 한다. 이때 빈 파일은 내용 저장소가 아니라 **namespace 참조를 bind mount할 손잡이**로 쓰인다.

```text
빈 파일 = namespace 데이터 파일이 아님
빈 파일 = namespace 참조를 붙잡아둘 경로
```

`ip netns add blue`가 만드는 `/var/run/netns/blue`도 같은 계열로 이해하면 된다. 이 파일에 네트워크 설정이 텍스트로 저장되는 것이 아니라, `blue`라는 network namespace로 들어가기 위한 참조점 역할을 한다.

## Chapter 6. network namespace는 어떻게 네트워크를 분리하는가

network namespace는 프로세스가 보는 네트워크 세계를 분리한다.

분리되는 것:

- network interface 목록
- IP 주소
- routing table
- ARP table
- firewall/NAT rule view
- port number space
- `localhost`

그래서 서로 다른 network namespace에서는 같은 포트 번호를 동시에 사용할 수 있다.

```text
Host namespace:
127.0.0.1:8080 사용 중

Container namespace:
127.0.0.1:8080 사용 가능
```

이는 포트 번호 공간이 namespace마다 따로 있기 때문이다.

하지만 network namespace를 새로 만들기만 하면 외부와 연결되지 않은 섬이다. 보통은 `veth pair`로 namespace를 연결한다.

```text
Host namespace
  veth0
    |
    | virtual ethernet pair
    |
Container namespace
  veth1 또는 eth0
```

`veth pair`는 가상 LAN 케이블 한 쌍처럼 동작한다. 한쪽으로 들어온 패킷은 다른 쪽으로 나온다.

Docker 기본 bridge 네트워크는 이 구조를 확장한 것이다.

```text
Host namespace

docker0 bridge
  ├── veth-container-a
  ├── veth-container-b
  └── veth-container-c

Container A netns
Container B netns
Container C netns
```

컨테이너에서 외부 인터넷으로 나갈 때는 보통 host namespace의 라우팅과 NAT를 거친다.

```text
container 172.x.x.x
-> veth
-> bridge
-> host routing
-> NAT
-> external network
```

## Chapter 7. Docker Compose의 ports가 필요한 이유

Docker/Compose 기본 모델에서는 컨테이너가 network namespace 단위다.

컨테이너 안에서 Nginx가 `0.0.0.0:80`에 떠 있어도, 그것은 컨테이너 namespace 안의 80번 포트다. 호스트의 `localhost:80`이 아니다.

```text
Container network namespace:
0.0.0.0:80 -> nginx process

Host network namespace:
localhost:80 -> unrelated
```

그래서 호스트에서 접근하려면 포트를 publish해야 한다.

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

의미:

```text
Host 0.0.0.0:8080
-> Docker port publishing/NAT
-> Container 172.x.x.x:80
-> nginx process
```

여기서 `0.0.0.0:8080`은 클라이언트가 접속하는 주소라기보다, 호스트가 모든 네트워크 인터페이스에서 8080 포트를 받겠다는 listen 의미다.

클라이언트는 보통 이렇게 접근한다.

```text
http://localhost:8080
http://127.0.0.1:8080
http://<host-ip>:8080
```

Kubernetes Pod와 비교하면 차이가 선명해진다.

```text
Docker/Compose:
Host port -> Container port
container가 network namespace 단위

Kubernetes Pod:
Local/Node/Service -> Pod port
Pod가 network namespace 단위
Pod 안 컨테이너들은 network namespace 공유
```

## Chapter 8. cgroup은 무엇을 제어하는가

cgroup은 `control group`의 줄임말이다. Linux 커널이 프로세스 그룹의 자원 사용량을 제한하고 측정하는 기능이다.

namespace가 시야를 나눈다면, cgroup은 사용량을 제한한다.

```text
namespace = what the process can see
cgroup    = how much resource the process can use
```

대표적으로 제어하는 자원:

- CPU
- memory
- disk I/O
- process count
- device access

Docker:

```bash
docker run --memory=512m --cpus=1 nginx
```

Kubernetes:

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```

Kubernetes의 `limits.memory`를 넘으면 컨테이너가 OOMKilled 될 수 있다. 그 아래에서는 컨테이너 런타임이 cgroup을 설정하고, 실제 제한은 Linux 커널이 적용한다.

## Chapter 9. docker diff와 docker commit

Docker image는 읽기 전용 레이어들의 조합이고, 컨테이너는 그 위에 쓰기 가능한 레이어를 하나 더 얹은 실행 상태다.

```text
container writable layer
read-only image layer N
read-only image layer N-1
...
read-only base layer
```

컨테이너에서 파일을 만들거나 수정하면 이미지가 바뀌는 것이 아니라, 컨테이너 전용 writable layer가 바뀐다.

`docker diff`는 기반 이미지와 현재 컨테이너 writable layer 사이의 차이를 보여준다.

```bash
docker diff <container>
```

출력 의미:

```text
A = added
C = changed
D = deleted
```

예를 들어 컨테이너 안에서 `/hello` 파일을 만들면 `docker diff`는 추가된 파일을 보여준다.

```bash
docker run -it ubuntu:18.04 bash
# in container
touch /hello /world

# in host
docker diff <container-id>
```

`docker commit`은 현재 컨테이너의 파일 변경분을 새 이미지로 저장한다.

```bash
docker commit <container-id> ubuntu:hello-world
```

이 방식은 이미지 원리를 이해하기에는 좋지만, 재현 가능한 빌드 방식으로는 Dockerfile이 더 적합하다.

`docker commit`의 한계:

- 어떤 명령으로 이미지가 만들어졌는지 이력이 불명확하다.
- 재현성이 낮다.
- 협업과 리뷰에 불리하다.

그래서 실무에서는 보통 Dockerfile을 사용한다.

## Chapter 10. Dockerfile은 commit 과정을 코드로 만든 것

Dockerfile은 컨테이너 파일시스템을 변경하는 과정을 선언적으로 기록한다.

예:

```Dockerfile
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y git
```

각 명령은 이미지 빌드 과정에서 파일시스템 변경을 만들고, 이 변경은 이미지 레이어가 된다.

```text
FROM ubuntu:18.04
-> base image layer

RUN apt-get update
-> apt metadata 변경 layer

RUN apt-get install -y git
-> git 설치 변경 layer
```

따라서 Dockerfile을 읽을 때는 "명령어 목록"이 아니라 **파일시스템 변경 레이어를 만드는 절차**로 읽어야 한다.

좋은 Dockerfile의 기본 방향:

- 재현 가능해야 한다.
- 불필요한 레이어와 캐시 파일을 줄여야 한다.
- 이미지에 필요한 것만 남겨야 한다.
- 수동 `docker commit`보다 Dockerfile로 기록해야 한다.

## Chapter 11. Union mount와 OverlayFS

컨테이너마다 전체 root filesystem을 복사하면 너무 느리고 무겁다. Docker image가 실용적인 이유는 레이어를 공유하기 때문이다.

Union mount는 여러 디렉터리/파일시스템을 겹쳐 하나의 디렉터리처럼 보이게 하는 기술이다.

```text
lower layer 1: base files
lower layer 2: package files
lower layer 3: app files
upper layer: container writes

merged view:
/
├── bin
├── etc
├── app
└── tmp
```

Linux에서 Docker가 자주 사용하는 구현체가 OverlayFS이고, Docker storage driver로는 `overlay2`를 흔히 본다.

OverlayFS의 핵심 개념:

| 구성 | 의미 |
| --- | --- |
| lowerdir | 읽기 전용 기반 레이어들 |
| upperdir | 변경 사항을 기록하는 쓰기 가능 레이어 |
| workdir | OverlayFS 내부 작업 디렉터리 |
| merged | 컨테이너가 실제로 보는 합쳐진 view |

파일 수정은 copy-on-write로 동작한다.

```text
1. lower layer에 /etc/app.conf가 있음
2. 컨테이너가 이 파일을 수정하려 함
3. 파일이 upper layer로 복사됨
4. upper layer의 복사본이 수정됨
5. merged view에서는 수정본이 보임
```

파일 삭제는 실제 lower layer 파일을 지우는 것이 아니라, upper layer에 "삭제된 것으로 보이라"는 표시를 남긴다. 이를 whiteout이라고 부른다.

이 구조 덕분에:

- 공통 base image layer를 여러 컨테이너가 공유할 수 있다.
- 컨테이너 생성이 빠르다.
- 이미지 빌드 캐시가 가능하다.
- 변경분만 새 레이어로 저장할 수 있다.

## Chapter 12. Kubernetes와 연결하기

Kubernetes에서 Pod를 이해할 때 Docker 기본 모델과의 차이를 기억해야 한다.

Docker/Compose 기본 실행:

```text
container A network namespace
container B network namespace
```

Kubernetes Pod:

```text
Pod network namespace
├── app container
└── sidecar container
```

같은 Pod 안 컨테이너들은 같은 network namespace를 공유한다.

```text
app container localhost == sidecar container localhost
```

그래서 같은 Pod 안에서는 컨테이너 간 통신에 `localhost`를 사용할 수 있다. 반대로 같은 포트를 동시에 열 수 없다.

```text
app: 8080 listen
sidecar: 8080 listen
-> conflict
```

이전 대화에서 정리한 포트포워딩 감각은 다음과 같다.

```text
Docker Compose:
host port -> container port

Kubernetes kubectl port-forward:
local port -> Pod port
```

Kubernetes에서 `containerPort`는 Docker의 `-p`처럼 실제 포트 publish를 만드는 설정이 아니다. 대부분 "이 컨테이너 프로세스가 이 포트를 사용할 예정"이라는 메타데이터에 가깝다. 실제로는 해당 프로세스가 그 포트를 listen하고 있어야 한다.

## 실습으로 확인할 핵심 질문

이 글을 읽으며 다음 질문에 직접 답할 수 있어야 한다.

1. 컨테이너는 왜 VM이 아니라 프로세스인가?
2. image는 왜 실행 상태가 아니라 파일시스템 재료인가?
3. `chroot`만으로 컨테이너가 부족한 이유는 무엇인가?
4. namespace의 `name`은 무엇을 의미하는가?
5. network namespace가 다르면 왜 같은 포트를 동시에 쓸 수 있는가?
6. Docker Compose에서 `ports`가 필요한 이유는 무엇인가?
7. Kubernetes Pod 안 컨테이너들이 같은 `localhost`를 공유하는 이유는 무엇인가?
8. cgroup은 namespace와 무엇이 다른가?
9. `docker diff`는 정확히 무엇과 무엇의 차이를 보여주는가?
10. `docker commit`보다 Dockerfile이 권장되는 이유는 무엇인가?
11. OverlayFS의 lowerdir, upperdir, merged는 각각 무엇인가?

## 다음 문서 승격 후보

학습이 안정화되면 다음 개념 문서로 분리할 수 있다.

- `docs/concepts/linux-namespace.md`
- `docs/concepts/cgroup.md`
- `docs/concepts/docker-image-layer.md`
- `docs/concepts/overlayfs.md`
- `docs/concepts/kubernetes-pod-network-namespace.md`

