# 05. 2.3. Why Can't I Connect to a Program Launched

- 영상: [Kubernetes for Beginners and Practical Use - Part 2.3. Why Can't I Connect to a Program Launched ...](https://www.youtube.com/watch?v=tX7WTFtrI3Q)
- 길이: 7분 4초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Pod를 생성했다고 해서 외부에서 곧바로 접속할 수 있는 것은 아니다.
- Pod IP는 클러스터 내부 네트워크에 속하며, 로컬 PC나 외부 브라우저와 직접 연결되지 않는다.
- 초기 실습에서는 port-forward 같은 임시 연결 방식으로 Pod 내부 서비스를 확인한다.

## 근원탐색 관점

컨테이너가 실행되는 것과 네트워크로 노출되는 것은 별개의 문제다. 쿠버네티스는 실행 단위와 접근 경로를 분리해 네트워크 정책과 서비스 노출을 제어한다.

## 보기 전에 잡을 질문

- Pod IP와 localhost의 차이를 설명한다.
- kubectl port-forward가 임시 접근 수단이라는 점을 기억한다.

## 자막에서 확인한 키워드

- 파드

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
