# 06. 2.4. [Example] Deploying a Backend (Spring Boot) Ser

- 영상: [Kubernetes for Beginners and Practical Use - 2.4. [Example] Deploying a Backend (Spring Boot) Ser...](https://www.youtube.com/watch?v=P46VBBZ6Ldo)
- 길이: 7분 47초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Spring Boot 애플리케이션 이미지를 Pod로 실행하는 흐름을 다룬다.
- 백엔드 서버도 Nginx와 동일하게 이미지, 포트, Pod 상태 확인이라는 기본 패턴으로 배포된다.
- 애플리케이션 종류가 달라져도 Kubernetes 관점에서는 컨테이너 이미지와 실행 설정이 핵심이다.

## 근원탐색 관점

애플리케이션 런타임이 Java든 Nginx든 운영자는 동일한 배포 단위로 다루고 싶다. Pod는 이런 실행 방식의 차이를 컨테이너 이미지 뒤로 숨긴다.

## 보기 전에 잡을 질문

- Spring Boot 이미지 이름과 컨테이너 포트를 매니페스트에 반영한다.
- port-forward로 HTTP 응답을 확인한다.

## 자막에서 확인한 키워드

- 파드

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
