# 25. 4.2: Installing Docker Kubernetes on EC2 (k3s)

- 영상: [Kubernetes for Beginners and Practical Use - Part 4.2: Installing Docker Kubernetes on EC2 (k3s)](https://www.youtube.com/watch?v=un4KTzueSl8)
- 길이: 3분 30초
- 정리 기준: 한국어 자막/스크립트 기반 요약. 원문 스크립트는 저장하지 않음.

## 이 챕터의 핵심

- EC2 서버에 실습 환경을 만들고 Docker/k3s를 설치하는 흐름을 다룬다.
- 로컬 실습을 넘어 실제 원격 서버에서 Kubernetes를 다루는 준비 단계다.
- 클라우드 인스턴스 접속, 설치, 클러스터 확인이 주요 흐름이다.

## 근원탐색 관점

로컬 환경만으로는 외부 접속, 서버 네트워크, 배포 환경의 감각을 얻기 어렵다. EC2와 k3s 조합은 작은 실제 서버에서 Kubernetes를 경험하게 해준다.

## 보기 전에 잡을 질문

- EC2 보안 그룹과 포트 노출을 확인한다.
- k3s 설치 후 kubectl로 노드 상태를 확인한다.

## 자막에서 확인한 키워드

- 서비스
- k3s

## 실습/명령어 포인트

- 영상 시청 중 나오는 명령어는 직접 실행하며 별도 lab 문서로 옮긴다.

## 다음에 연결할 문서 후보

- 개념이 안정화되면 `docs/concepts/`의 관련 문서에 반영한다.
- 직접 따라 한 결과는 `docs/labs/`에 별도 실습 기록으로 남긴다.
