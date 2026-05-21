[한국어](README.md) | [English](README-en.md)

# Kubernetes Study Lab

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=flat-square&logo=yaml&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Markdown](https://img.shields.io/badge/Markdown-000000?style=flat-square&logo=markdown&logoColor=white)

Kubernetes를 학습하며 개념 정리, 실습 기록, 매니페스트 예제, 운영 문서를 함께 관리하는 저장소입니다.

## 목차

- [개요](#개요)
- [구조](#구조)
- [기술 스택](#기술-스택)
- [설계 결정](#설계-결정)
- [시작하기](#시작하기)
- [프로젝트 구조](#프로젝트-구조)
- [학습 로드맵](#학습-로드맵)
- [라이선스](#라이선스)

## 개요

이 저장소는 Kubernetes 학습 문서와 실행 가능한 실습 자산을 분리해 관리합니다. 학습 범위가 커져도 개념 노트, 클러스터 실험, 재사용 가능한 YAML 매니페스트를 쉽게 찾을 수 있도록 구성했습니다.

학습은 세 단계로 확장합니다.

| 단계 | 초점 | 산출물 |
| --- | --- | --- |
| 기초 | Pod, Deployment, Service, ConfigMap, Secret | 개념 문서와 기본 매니페스트 |
| 운영 | Ingress, 스토리지, 스케줄링, 롤아웃, 트러블슈팅 | 운영 런북과 실습 기록 |
| 프로덕션 준비 | 보안, 관측성, GitOps, Helm, CI/CD | 재사용 예제와 아키텍처 문서 |

## 구조

```text
Kubernetes Study Lab
├── docs/         -> 개념 정리, 가이드, 레퍼런스
├── labs/         -> 주제별 실습 기록
├── manifests/    -> Kubernetes YAML 예제와 재사용 리소스
├── scripts/      -> 실습 보조 스크립트
└── assets/       -> 다이어그램, 스크린샷, 보조 자료
```

## 기술 스택

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![kubectl](https://img.shields.io/badge/kubectl-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=flat-square&logo=yaml&logoColor=white)

| 분류 | 도구 |
| --- | --- |
| 컨테이너 오케스트레이션 | Kubernetes |
| CLI | kubectl |
| 로컬 런타임 | Docker, 선택적 로컬 Kubernetes 배포판 |
| 문서화 | Markdown |
| 설정 | YAML |

## 설계 결정

| 결정 | 이유 |
| --- | --- |
| 문서와 실습 디렉토리 분리 | 개념 설명과 실행 기록의 목적이 다르기 때문입니다. |
| 매니페스트를 별도 관리 | 재사용 가능한 YAML을 실습 로그와 분리해 빠르게 찾기 위해서입니다. |
| 빈 디렉토리는 `.gitkeep`으로 추적 | 첫 커밋부터 학습 공간의 구조를 명확히 보여주기 위해서입니다. |
| 로컬 클러스터 인증 정보 제외 | kubeconfig, 인증서, Secret 매니페스트는 커밋하면 안 됩니다. |

## 시작하기

### 사전 준비

- Git
- Docker 또는 다른 컨테이너 런타임
- kubectl
- 로컬 또는 원격 Kubernetes 클러스터

### 클론

```bash
git clone https://github.com/lsh1215/Kubernetes.git
cd Kubernetes
```

### 권장 작업 흐름

```bash
# 클러스터 접근 확인
kubectl cluster-info

# 매니페스트 탐색
find manifests -type f

# 각 실습 기록은 labs/ 아래에 남깁니다
```

## 프로젝트 구조

```text
.
├── assets/
│   ├── diagrams/
│   └── images/
├── docs/
│   ├── concepts/
│   ├── deep-dives/
│   ├── labs/
│   └── study-log/
├── manifests/
│   ├── base/
│   └── examples/
└── scripts/
```

## 학습 로드맵

- [ ] Kubernetes 아키텍처와 핵심 오브젝트
- [ ] 워크로드 리소스: Pod, ReplicaSet, Deployment, StatefulSet, DaemonSet
- [ ] 서비스 디스커버리와 네트워킹
- [ ] ConfigMap, Secret, 환경 설정
- [ ] 볼륨과 영구 스토리지
- [ ] Ingress와 트래픽 라우팅
- [ ] 스케줄링, 프로브, 롤아웃, 오토스케일링
- [ ] 트러블슈팅과 디버깅 흐름
- [ ] 보안, RBAC, 정책 기초
- [ ] 관측성과 GitOps 실습

## 문서화 방식

이 저장소는 사람이 읽는 학습 문서와 AI가 다시 참고할 장기 기억을 분리합니다.

| 위치 | 역할 |
| --- | --- |
| `docs/study-log/` | 날짜별 학습 흐름, 의미 있는 질문, 실험 결과, 아직 모호한 점 |
| `docs/concepts/` | 안정화된 개념 문서. 근원탐색과 References를 문서 안에 포함 |
| `docs/deep-dives/` | 여러 개념을 관통하는 큰 조사, 비교, 역사, 설계 배경 |
| `docs/labs/` | 실습 기록, 명령어, 관찰 결과, 에러와 해결 과정 |
| `manifests/` | 실행 가능한 Kubernetes YAML 예제 |
| `omx_wiki/` | 사용자가 직접 생성하는 AI용 장기 기억 저장소 |

### 학습 자료

- [JSCODE Kubernetes Playlist Notes](docs/deep-dives/jscode-kubernetes-playlist/README.md): JSCODE 쿠버네티스 플레이리스트의 챕터별 사전 학습 노트

## 라이선스

현재는 개인 학습 프로젝트로 관리합니다. 콘텐츠를 공개적으로 재사용하거나 배포하기 전에 라이선스를 추가하세요.
