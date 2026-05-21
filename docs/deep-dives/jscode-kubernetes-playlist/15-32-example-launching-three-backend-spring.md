# 15. 3.2. [Example] Launching Three Backend (Spring

- 영상: [Kubernetes for Beginners and Practical Use - Part 3.2. [Example] Launching Three Backend (Spring ...](https://www.youtube.com/watch?v=TpXS309s-kA)
- 길이: 8분 48초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Deployment를 사용해 Spring Boot 백엔드 Pod 3개를 선언적으로 띄운다.
- 직접 Pod를 3개 만드는 방식보다 replicas 값 하나로 개수를 관리한다.
- selector와 labels가 Deployment가 관리할 Pod를 연결하는 핵심이다.

## 근원탐색 관점

운영자는 “3개가 떠 있어야 한다”는 의도를 선언하고 싶지, Pod 3개의 생명주기를 직접 돌보고 싶지는 않다.

## 보기 전에 잡을 질문

- replicas: 3 설정으로 Pod 3개가 생성되는지 확인한다.
- label과 selector가 맞지 않으면 관리가 깨진다는 점을 확인한다.

## 자막에서 확인한 키워드

- 파드
- 디플로이먼트
- 서비스

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
