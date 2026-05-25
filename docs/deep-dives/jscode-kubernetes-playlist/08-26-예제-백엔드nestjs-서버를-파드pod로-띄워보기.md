# 08. Nest.js 백엔드를 Pod로 실행하기

- 영상: [Nest.js 백엔드를 Pod로 실행하기](https://www.youtube.com/watch?v=n0y9ZHvhjic)
- 플레이리스트: [JSCODE Kubernetes 입문/실전](https://www.youtube.com/playlist?list=PLtUgHNmvcs6pBvcX0ilCncJRgBHNs40vX)
- 문서 성격: 이 문서는 영상의 한국어 자막/스크립트를 확인한 뒤 재구성한 학습 문서다. 원문 스크립트를 복제하지 않고, 영상 흐름을 따라 개념 설명, 그림, 실습 코드를 보강했다.

## 이 챕터에서 얻어야 할 것

Node/Nest.js 서버도 Pod 실행 모델로 동일하게 배포한다.

## 스크립트 기반 흐름 정리

- 영상은 Nest.js 서버 이미지를 Pod로 띄우며 백엔드 배포 패턴을 반복한다.
- Spring Boot와 비교하면 컨테이너 내부 런타임만 다르고 Kubernetes 리소스 구조는 같다.
- Pod 로그와 port-forward로 서버가 정상 실행되는지 검증한다.

## 근원탐색: 왜 이 개념이 필요한가

Kubernetes 입장에서 중요한 것은 프레임워크가 아니라 컨테이너 이미지와 프로세스 포트다. 이 반복 실습은 “이미지 + 포트 + Pod” 모델이 여러 언어/프레임워크에 공통 적용된다는 것을 확인한다.

## 구조 그림

```mermaid
flowchart LR
  Nest[Nest.js app] --> Image[Docker image]
  Image --> Pod[Pod]
  Pod --> Check[logs / port-forward / curl]
```

## 핵심 개념 해설

실습의 중심은 YAML을 외우는 것이 아니라 “이미지로 포장된 애플리케이션을 Pod라는 실행 단위로 올리고, 상태를 관찰하고, 임시 접근 경로로 검증한다”는 반복 패턴이다. 여기서 익힌 apply/get/describe/logs/port-forward 흐름이 이후 모든 리소스 학습의 기본 루틴이 된다.

## 실습 매니페스트/코드

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nest-backend
  labels:
    app: nest-backend
spec:
  containers:
    - name: app
      image: your-registry/nest-backend:latest
      ports:
        - containerPort: 3000
```

## 따라칠 명령어

```bash
kubectl apply -f nest-backend-pod.yaml
```

```bash
kubectl logs -f pod/nest-backend
```

```bash
kubectl port-forward pod/nest-backend 3000:3000
```

```bash
curl http://localhost:3000
```

## 확인 포인트

- 서버가 어떤 포트를 listen하는지 Dockerfile/앱 설정에서 확인한다.
- Pod 매니페스트의 containerPort와 실제 앱 포트가 불일치하지 않는지 확인한다.

## 영상 기반 상세 학습 노트

Nest.js 예제는 프레임워크가 바뀌어도 Pod 배포 흐름은 같다는 점을 확인한다. Node 앱은 빌드 산출물 경로와 실행 커맨드가 틀리기 쉬우므로 Dockerfile, 로그, listen 포트를 함께 확인해야 한다.

### 화면/구조를 문서로 옮기면

```mermaid
flowchart LR
  Nest[Nest.js source] --> Build[npm run build]
  Build --> Image[Node Docker image]
  Image --> Pod[Nest Pod:3000]
  Pod --> Logs[kubectl logs]
  Pod --> PF[port-forward 3000:3000]
```

### 실습할 때 같이 확인할 명령

```bash
docker build -t your-registry/nest-api:0.1.0 .
kubectl apply -f nest-pod.yaml
kubectl logs -f nest-pod
kubectl port-forward pod/nest-pod 3000:3000
```

### 이 장에서 꼭 남길 관찰

- 명령이 성공했는지보다 어떤 객체의 상태가 바뀌었는지 먼저 적는다.
- 실패가 나오면 `get -> describe -> logs/events` 순서로 원인을 좁힌다.
- 안정화된 설명은 나중에 `docs/concepts/`로 승격하고, 실제 실행 결과는 `docs/labs/`에 남긴다.

## 자주 헷갈리는 지점

- Pod의 `containerPort`는 Docker의 `-p` 같은 포트포워딩 설정이 아니다. 실제로는 프로세스가 해당 포트를 listen해야 한다.
- 영상의 이미지 이름과 포트는 실습 코드에 맞게 바뀔 수 있다. 실제로 따라칠 때는 Dockerfile과 애플리케이션 listen port를 먼저 확인한다.

## 다음 문서로 승격할 내용

- `docs/concepts/pod.md`에 Pod의 실행 단위, 네트워크 namespace, containerPort 오해를 정리한다.
