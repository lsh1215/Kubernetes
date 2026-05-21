# 04. 2.2. [Example] Running a Web Server (Nginx) as a Pod

- 영상: [Kubernetes for Beginners and Practical Use - 2.2. [Example] Running a Web Server (Nginx) as a Pod](https://www.youtube.com/watch?v=sUUWAOzib8M)
- 길이: 8분 38초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Nginx 웹 서버를 Pod로 띄우며 YAML 매니페스트와 kubectl 적용 흐름을 익힌다.
- Pod 생성 후 상태 확인, 상세 조회, 삭제 같은 기본 작업을 반복한다.
- 컨테이너 이미지를 지정하고 쿠버네티스가 해당 이미지를 기반으로 Pod를 실행하는 구조를 확인한다.

## 근원탐색 관점

로컬에서 Docker로 직접 실행하던 웹 서버를 클러스터가 관리하는 실행 단위로 옮기는 첫 실습이다.

## 보기 전에 잡을 질문

- Nginx Pod YAML을 작성한다.
- kubectl apply/get/describe/delete 흐름을 손에 익힌다.

## 자막에서 확인한 키워드

- 파드
- 디플로이먼트
- 서비스

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
