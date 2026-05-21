# 20. 3.7. Updating to a New Server Version

- 영상: [Kubernetes for Beginners and Practical Use - 3.7. Updating to a New Server Version](https://www.youtube.com/watch?v=xJSkPfIYK70)
- 길이: 3분 43초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- Deployment를 통해 컨테이너 이미지 버전을 바꾸고 새 버전으로 업데이트한다.
- Pod를 한 번에 지우고 다시 만드는 대신 점진적으로 교체하는 롤아웃 개념을 접한다.
- 이미지 태그 관리와 업데이트 확인이 중요하다.

## 근원탐색 관점

서비스를 중단하지 않고 새 버전을 배포하려면 기존 인스턴스와 새 인스턴스를 안전하게 교체해야 한다. Deployment는 이 업데이트 과정을 리소스 수준에서 관리한다.

## 보기 전에 잡을 질문

- 이미지 태그를 변경하고 rollout 상태를 확인한다.
- 업데이트 전후 Pod 이름과 이미지 버전을 비교한다.

## 자막에서 확인한 키워드

- 디플로이먼트

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
