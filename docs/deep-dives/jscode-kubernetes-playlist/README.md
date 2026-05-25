# JSCODE Kubernetes Playlist Notes

JSCODE Kubernetes 입문/실전 플레이리스트를 영상 스크립트 기반으로 재구성한 학습 문서 모음이다.

원문 스크립트는 저장하지 않는다. 각 챕터는 영상 흐름을 바탕으로 개념 설명, Mermaid 구조 그림, 실습 YAML/명령어, 확인 포인트를 포함한다.

- 플레이리스트: [JSCODE Kubernetes 입문/실전](https://www.youtube.com/playlist?list=PLtUgHNmvcs6pBvcX0ilCncJRgBHNs40vX)
- 문서 수: 27개 챕터 + 이 인덱스

## 챕터 목록

| No | Chapter | Focus | Source |
| --- | --- | --- | --- |
| 01 | [강의 소개와 학습 경로](01-쿠버네티스-강의를-만들어봤습니다.md) | 쿠버네티스 입문/실전 강의의 전체 범위와 학습 순서를 잡는다. | [YouTube](https://www.youtube.com/watch?v=3VT7Suh9HIw) |
| 02 | [쿠버네티스란 무엇인가](02-11-what-is-kubernetes.md) | 쿠버네티스를 다수의 컨테이너를 배포, 확장, 관리하는 오픈소스 오케스트레이션 시스템으로 이해한다. | [YouTube](https://www.youtube.com/watch?v=_M4oY7aK9ck) |
| 03 | [Pod란 무엇인가](03-21-파드pod란.md) | Pod가 쿠버네티스에서 컨테이너를 감싸는 최소 실행 단위임을 이해한다. | [YouTube](https://www.youtube.com/watch?v=UJssa3nc0FQ) |
| 04 | [Nginx 웹 서버를 Pod로 실행하기](04-22-example-running-a-web-server-nginx-as-a-pod.md) | Nginx 컨테이너 이미지를 Pod로 실행하고 kubectl 기본 흐름을 익힌다. | [YouTube](https://www.youtube.com/watch?v=sUUWAOzib8M) |
| 05 | [Pod로 띄운 프로그램에 바로 접속할 수 없는 이유](05-23-why-can-t-i-connect-to-a-program-launched.md) | Pod 실행과 외부 접근은 별개의 문제이며, Pod IP는 기본적으로 클러스터 내부 주소임을 이해한다. | [YouTube](https://www.youtube.com/watch?v=tX7WTFtrI3Q) |
| 06 | [Spring Boot 백엔드를 Pod로 배포하기](06-24-example-deploying-a-backend-spring-boot-ser.md) | Spring Boot 애플리케이션도 컨테이너 이미지가 있으면 Pod로 동일하게 실행할 수 있음을 확인한다. | [YouTube](https://www.youtube.com/watch?v=P46VBBZ6Ldo) |
| 07 | [이미지 풀 정책과 이미지 없음 오류](07-25-이미지가-없다고-에러가-뜨는-이유-이미지-풀-정책.md) | ImagePullBackOff, ErrImagePull, imagePullPolicy가 왜 발생하는지 이해한다. | [YouTube](https://www.youtube.com/watch?v=tkdDEMGl0tQ) |
| 08 | [Nest.js 백엔드를 Pod로 실행하기](08-26-예제-백엔드nestjs-서버를-파드pod로-띄워보기.md) | Node/Nest.js 서버도 Pod 실행 모델로 동일하게 배포한다. | [YouTube](https://www.youtube.com/watch?v=n0y9ZHvhjic) |
| 09 | [정적 프론트엔드를 Pod로 실행하기](09-27-예제-프론트엔드html-css-nginx-서버를-파드pod로-띄워보기.md) | HTML/CSS 정적 파일을 Nginx 이미지로 서빙하는 프론트엔드 Pod 패턴을 이해한다. | [YouTube](https://www.youtube.com/watch?v=j-qy7tpTUo0) |
| 10 | [Next.js 프론트엔드를 Pod로 실행하기](10-28-example-running-a-frontend-nex.md) | Next.js처럼 서버 런타임이 있는 프론트엔드를 Pod로 배포하는 감각을 잡는다. | [YouTube](https://www.youtube.com/watch?v=vZbjjFNzeJ0) |
| 11 | [Spring Boot 서버 3개를 Pod로 직접 띄워보기](11-29-예제-백엔드spring-boot-서버-3개-띄워보기.md) | Pod를 직접 여러 개 만드는 방식의 한계를 체감하고 Deployment 필요성으로 연결한다. | [YouTube](https://www.youtube.com/watch?v=kzsEIZ3bIvc) |
| 12 | [Pod 디버깅 방법](12-210-보충-강의-파드pod-디버깅-하는-방법.md) | Pod가 실패했을 때 get, describe, logs, events로 원인을 좁히는 절차를 익힌다. | [YouTube](https://www.youtube.com/watch?v=btd7r1u1dsM) |
| 13 | [Pod 파트 명령어 정리](13-211-요약-지금까지-나온-명령어-정리.md) | Pod 실습에서 반복된 최소 kubectl 명령어를 정리한다. | [YouTube](https://www.youtube.com/watch?v=jw7ye7BuQUI) |
| 14 | [Deployment란 무엇인가](14-31-디플로이먼트deployment란.md) | Deployment가 Pod의 원하는 개수와 업데이트를 관리하는 상위 리소스임을 이해한다. | [YouTube](https://www.youtube.com/watch?v=n0c-SUP-p5Q) |
| 15 | [Deployment로 Spring Boot 서버 3개 실행하기](15-32-example-launching-three-backend-spring.md) | replicas를 통해 동일한 Spring Boot Pod 3개를 선언적으로 실행한다. | [YouTube](https://www.youtube.com/watch?v=TpXS309s-kA) |
| 16 | [Service란 무엇인가](16-33-서비스service란.md) | Service가 변하는 Pod 집합 앞에 안정적인 접근 지점을 제공한다는 점을 이해한다. | [YouTube](https://www.youtube.com/watch?v=ZUVdX8wk-wM) |
| 17 | [Service로 백엔드와 통신하기](17-34-예제-서비스service를-활용해-백엔드spring-boot-서버와-통신해보기.md) | Service를 통해 Spring Boot Deployment 뒤의 Pod들에 안정적으로 접근한다. | [YouTube](https://www.youtube.com/watch?v=80TJCAO5U4c) |
| 18 | [Deployment로 서버 개수 조절하기](18-35-디플로이먼트를-활용한-서버-개수-조절-방법.md) | replicas 값을 바꾸어 서버 수를 늘리고 줄이는 방식을 익힌다. | [YouTube](https://www.youtube.com/watch?v=2WudZUNrY2w) |
| 19 | [Self-Healing: 서버가 죽었을 때 자동 복구](19-36-서버가-죽었을-때-자동으로-복구하는-기능-self-healing.md) | Deployment가 원하는 Pod 개수를 유지하며 장애를 자동 복구하는 방식을 이해한다. | [YouTube](https://www.youtube.com/watch?v=gu9nSKpd4mA) |
| 20 | [새 서버 버전으로 업데이트하기](20-37-updating-to-a-new-server-version.md) | Deployment를 통해 컨테이너 이미지 버전을 교체하고 rollout을 관찰한다. | [YouTube](https://www.youtube.com/watch?v=xJSkPfIYK70) |
| 21 | [Deployment와 Service로 Nest.js 배포하기](21-38-예제-디플로이먼트-서비스를-활용해-백엔드nestjs-서버-띄워보기.md) | Nest.js 백엔드를 Deployment와 Service 조합으로 운영 형태에 가깝게 배포한다. | [YouTube](https://www.youtube.com/watch?v=vWEpYZl98j8) |
| 22 | [Deployment/Service 명령어 정리](22-39-요약-지금까지-나온-명령어-정리.md) | Deployment와 Service 파트에서 반복된 kubectl 명령을 사용 목적별로 정리한다. | [YouTube](https://www.youtube.com/watch?v=3spPR3gAiAo) |
| 23 | [Pod, Deployment, Service 개념 종합](23-310-요약-파드pod-디플로이먼트deployment-서비스service-개념-정리.md) | Pod, Deployment, Service를 실행-관리-접근 문제로 연결해서 이해한다. | [YouTube](https://www.youtube.com/watch?v=wcwxFv1HNt8) |
| 24 | [k3s란 무엇인가](24-41-what-is-k3s.md) | k3s를 가벼운 Kubernetes 배포판으로 이해하고 학습/소규모 환경에서의 용도를 파악한다. | [YouTube](https://www.youtube.com/watch?v=1vbrnkaZlk4) |
| 25 | [EC2에 Docker와 k3s 설치하기](25-42-installing-docker-kubernetes-on-ec2-k3s.md) | 원격 서버에 k3s 기반 Kubernetes 실습 환경을 준비한다. | [YouTube](https://www.youtube.com/watch?v=un4KTzueSl8) |
| 26 | [k3s에서 Nginx 웹 서버 배포하기](26-43-예제-디플로이먼트-서비스를-활용해-웹-서버nginx-띄워보기.md) | k3s 클러스터에서 Deployment와 Service로 Nginx 웹 서버를 배포한다. | [YouTube](https://www.youtube.com/watch?v=me3Q6CsOjyQ) |
| 27 | [쿠버네티스를 빠르게 배우는 방법](27-44-남들보다-훨씬-쿠버네티스를-빠르게-배우는-방법.md) | 입문 이후 학습을 어떻게 확장할지 정리한다. | [YouTube](https://www.youtube.com/watch?v=nkXgO4I1sso) |

## 읽는 방법

1. 영상을 보기 전에 해당 챕터의 `근원탐색`과 `구조 그림`을 먼저 읽는다.
2. 영상을 보면서 `따라칠 명령어`와 `실습 매니페스트/코드`를 직접 실행한다.
3. 실제 실행 결과와 에러는 `docs/labs/`에 남긴다.
4. 여러 번 반복해서 안정화된 개념은 `docs/concepts/`로 승격한다.

## 저작권/정리 기준

- 영상 스크립트 원문은 저장하지 않는다.
- 문서는 학습자가 다시 공부할 수 있도록 내용을 재구성한 독립 노트다.
- 코드 예시는 학습용 일반 Kubernetes 매니페스트로 작성했다.
