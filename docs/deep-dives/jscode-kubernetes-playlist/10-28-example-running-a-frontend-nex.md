# 10. 2.8. [Example] Running a Frontend (Nex

- 영상: [Kubernetes for Beginners and Practical Applications - Part 2.8. [Example] Running a Frontend (Nex...](https://www.youtube.com/watch?v=vZbjjFNzeJ0)
- 길이: 5분 56초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Next.js처럼 Node 런타임이 필요한 프론트엔드 서버를 Pod로 실행한다.
- 정적 Nginx 프론트엔드와 달리 애플리케이션 서버 프로세스가 떠 있어야 한다.
- Pod 관점에서는 이미지와 포트 설정을 통해 동일하게 배포한다.

## 근원탐색 관점

프론트엔드도 정적 파일만 있는 경우와 서버 런타임이 필요한 경우가 있다. 컨테이너화하면 이 차이를 이미지 내부 실행 방식으로 캡슐화할 수 있다.

## 보기 전에 잡을 질문

- Next.js 서버가 사용하는 포트를 확인한다.
- 정적 프론트엔드와 서버형 프론트엔드의 차이를 비교한다.

## 자막에서 확인한 키워드

- 파드

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
