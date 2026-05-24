# Docker Internals Hands-on

이 문서는 [Docker Internals: namespace, cgroup, chroot, image layer](../deep-dives/docker-container-internals-44bits.md)를 따라가며 직접 실행해볼 실습 절차다.

## 전제

권장 환경:

- Linux VM 또는 Linux 서버
- Docker Engine
- `iproute2`
- `util-linux`
- root 권한 또는 `sudo`

주의:

- Docker Desktop for macOS/Windows는 내부 Linux VM 위에서 Docker가 실행된다. `/var/lib/docker`, bridge, container IP 접근 방식이 Linux 서버와 다를 수 있다.
- network namespace, veth, bridge 실습은 Linux 환경에서 하는 편이 가장 명확하다.
- 실습 후 cleanup을 하지 않으면 namespace, veth, container가 남을 수 있다.

## 1. Docker image와 container filesystem 차이 확인

```bash
docker pull ubuntu:18.04
docker run -it --name ubuntu-fs-a ubuntu:18.04 bash
```

컨테이너 안:

```bash
apt-get update
apt-get install -y git
git --version
exit
```

새 컨테이너:

```bash
docker run -it --name ubuntu-fs-b ubuntu:18.04 bash
git --version
exit
```

확인할 것:

- 첫 번째 컨테이너에는 `git`을 설치했다.
- 두 번째 컨테이너는 같은 `ubuntu:18.04` 이미지에서 시작했지만 `git`이 없다.
- 이유는 이미지가 바뀐 것이 아니라 첫 번째 컨테이너의 writable layer만 바뀌었기 때문이다.

정리:

```text
image = read-only base material
container = image + writable layer
```

Cleanup:

```bash
docker rm ubuntu-fs-a ubuntu-fs-b
```

## 2. docker diff로 변경분 보기

```bash
docker run -it --name diff-demo ubuntu:18.04 bash
```

컨테이너 안:

```bash
touch /hello /world
echo "changed" > /etc/demo-file
exit
```

호스트:

```bash
docker diff diff-demo
```

확인할 것:

- `A /hello`, `A /world`처럼 추가 파일이 보인다.
- 기존 파일을 바꾸면 `C`가 표시된다.
- 삭제하면 `D`가 표시된다.

Cleanup은 뒤의 commit 실습 후 진행한다.

## 3. docker commit으로 새 이미지 만들기

```bash
docker commit diff-demo ubuntu:hello-world
docker run --rm -it ubuntu:hello-world bash
```

컨테이너 안:

```bash
ls /
cat /etc/demo-file
exit
```

확인할 것:

- `/hello`, `/world`가 새 이미지에 포함된다.
- 새 이미지로 만든 컨테이너에서 `docker diff`를 실행하면 처음에는 변경분이 없다.

```bash
docker run -it --name committed-demo ubuntu:hello-world bash
exit
docker diff committed-demo
```

Cleanup:

```bash
docker rm diff-demo committed-demo
docker rmi ubuntu:hello-world
```

## 4. Dockerfile로 같은 변경 재현

작업 디렉터리:

```bash
mkdir -p /tmp/dockerfile-demo
cd /tmp/dockerfile-demo
```

`Dockerfile`:

```Dockerfile
FROM ubuntu:18.04
RUN touch /hello /world
RUN echo "changed" > /etc/demo-file
```

빌드:

```bash
docker build -t ubuntu:dockerfile-demo .
docker run --rm -it ubuntu:dockerfile-demo bash
```

확인할 것:

- `docker commit`으로 만들었던 상태를 Dockerfile로 재현할 수 있다.
- Dockerfile은 어떤 변경을 했는지 코드로 남긴다.

Cleanup:

```bash
docker rmi ubuntu:dockerfile-demo
rm -rf /tmp/dockerfile-demo
```

## 5. UTS namespace로 hostname 분리 확인

```bash
hostname
touch /tmp/utsns-demo
sudo unshare --uts=/tmp/utsns-demo hostname uts-demo
hostname
sudo nsenter --uts=/tmp/utsns-demo hostname
```

확인할 것:

- 호스트 hostname은 바뀌지 않는다.
- `/tmp/utsns-demo`로 들어간 UTS namespace 안에서는 hostname이 `uts-demo`로 보인다.
- 빈 파일은 namespace 내용 저장소가 아니라 namespace 참조를 붙잡는 경로다.

셸로 들어가기:

```bash
sudo nsenter --uts=/tmp/utsns-demo bash
hostname
exit
```

Cleanup:

```bash
sudo umount /tmp/utsns-demo 2>/dev/null || true
rm -f /tmp/utsns-demo
```

## 6. network namespace 만들기

```bash
sudo ip netns add direct_netns
ip netns list
sudo ip netns exec direct_netns ip -br link
```

확인할 것:

- 새 network namespace 안에는 기본적으로 loopback 정도만 보인다.
- 호스트의 `eth0`, `docker0` 같은 인터페이스는 보이지 않는다.

loopback 활성화:

```bash
sudo ip netns exec direct_netns ip link set lo up
sudo ip netns exec direct_netns ip -br link
```

## 7. veth pair로 host와 network namespace 연결

```bash
sudo ip link add veth0 type veth peer name veth1
ip -br link | grep veth
```

한쪽을 namespace로 이동:

```bash
sudo ip link set veth1 netns direct_netns
ip -br link | grep veth0
sudo ip netns exec direct_netns ip -br link
```

IP 부여:

```bash
sudo ip addr add 10.200.0.2/24 dev veth0
sudo ip netns exec direct_netns ip addr add 10.200.0.3/24 dev veth1
```

링크 활성화:

```bash
sudo ip link set veth0 up
sudo ip netns exec direct_netns ip link set veth1 up
```

통신 확인:

```bash
ping -c 3 10.200.0.3
sudo ip netns exec direct_netns ping -c 3 10.200.0.2
```

확인할 것:

- network namespace는 그냥 만들면 고립되어 있다.
- veth pair를 통해 host namespace와 직접 연결할 수 있다.
- Docker bridge 네트워크는 이 아이디어를 bridge로 확장한 구조다.

Cleanup:

```bash
sudo ip netns delete direct_netns
sudo ip link delete veth0 2>/dev/null || true
```

## 8. Docker port publishing 확인

```bash
docker run -d --name nginx-pub -p 8080:80 nginx
curl http://localhost:8080
```

호스트 listen 상태 확인:

```bash
ss -lntp | grep 8080 || true
docker port nginx-pub
docker inspect nginx-pub --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
```

확인할 것:

- 컨테이너 내부 Nginx는 container namespace의 80번 포트에서 listen한다.
- `-p 8080:80`은 host 8080을 container 80으로 publish한다.
- `0.0.0.0:8080`은 호스트가 모든 인터페이스에서 8080을 받는다는 의미다.

Cleanup:

```bash
docker rm -f nginx-pub
```

## 9. cgroup 제한 확인

```bash
docker run -d --name limited-nginx --memory=128m --cpus=0.5 nginx
docker stats limited-nginx
```

컨테이너 cgroup 정보는 환경에 따라 경로가 다르다. cgroup v2 시스템에서는 다음을 참고한다.

```bash
docker inspect limited-nginx --format '{{.Id}}'
find /sys/fs/cgroup -maxdepth 4 -name '*<container-id-prefix>*' 2>/dev/null
```

확인할 것:

- Docker 옵션은 컨테이너 프로세스에 대한 cgroup 설정으로 이어진다.
- 실제 제한은 Linux 커널이 적용한다.

Cleanup:

```bash
docker rm -f limited-nginx
```

## 10. OverlayFS 개념 확인

Docker storage driver:

```bash
docker info | grep -i 'Storage Driver'
```

Linux Docker Engine에서 `overlay2`를 쓰는 경우, 레이어 데이터는 보통 `/var/lib/docker/overlay2` 아래에 있다.

```bash
sudo ls /var/lib/docker/overlay2 | head
```

주의:

- `/var/lib/docker` 아래 파일을 직접 수정하지 않는다.
- 실습으로 읽어보는 것은 가능하지만, 로컬 이미지 저장소를 직접 고치면 Docker 상태가 깨질 수 있다.

OverlayFS 수동 실습:

```bash
mkdir -p /tmp/overlay-demo/{lower,upper,work,merged}
echo "from lower" > /tmp/overlay-demo/lower/hello.txt

sudo mount -t overlay overlay \
  -o lowerdir=/tmp/overlay-demo/lower,upperdir=/tmp/overlay-demo/upper,workdir=/tmp/overlay-demo/work \
  /tmp/overlay-demo/merged

cat /tmp/overlay-demo/merged/hello.txt
echo "changed in merged" | sudo tee /tmp/overlay-demo/merged/hello.txt

cat /tmp/overlay-demo/merged/hello.txt
cat /tmp/overlay-demo/lower/hello.txt
cat /tmp/overlay-demo/upper/hello.txt
```

확인할 것:

- `merged`에서 수정한 파일은 `upper`에 생긴다.
- `lower`는 원본을 유지한다.
- 이것이 copy-on-write 감각이다.

Cleanup:

```bash
sudo umount /tmp/overlay-demo/merged
rm -rf /tmp/overlay-demo
```

## 학습 완료 체크

다음 문장을 직접 설명할 수 있으면 이 실습의 목표를 달성한 것이다.

```text
Docker container는 host kernel 위의 process다.
namespace가 process의 view를 분리하고,
cgroup이 resource usage를 제한하고,
image layer와 OverlayFS가 root filesystem을 제공한다.
Docker port publishing은 host network namespace와 container network namespace를 연결한다.
Kubernetes Pod는 container가 아니라 Pod 단위로 network namespace를 공유한다.
```

