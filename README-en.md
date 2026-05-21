[한국어](README.md) | [English](README-en.md)

# Kubernetes Study Lab

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=flat-square&logo=yaml&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Markdown](https://img.shields.io/badge/Markdown-000000?style=flat-square&logo=markdown&logoColor=white)

A hands-on Kubernetes learning repository for notes, manifests, experiments, and operational documentation.

## Table of Contents

- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Key Design Decisions](#key-design-decisions)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Study Roadmap](#study-roadmap)
- [License](#license)

## Overview

This repository separates Kubernetes study material from runnable practice assets. The goal is to keep conceptual notes, cluster experiments, and reusable manifests organized as the study scope grows.

The project is structured around three phases:

| Phase | Focus | Output |
| --- | --- | --- |
| Foundations | Pods, Deployments, Services, ConfigMaps, Secrets | Concept notes and basic manifests |
| Operations | Ingress, storage, scheduling, rollout, troubleshooting | Operational runbooks and lab records |
| Production Readiness | Security, observability, GitOps, Helm, CI/CD | Reusable examples and architecture notes |

## System Architecture

```text
Kubernetes Study Lab
├── docs/         -> conceptual notes, guides, references
├── labs/         -> hands-on practice records by topic
├── manifests/    -> Kubernetes YAML examples and reusable resources
├── scripts/      -> local helper scripts for practice workflows
└── assets/       -> diagrams, screenshots, and supporting media
```

## Tech Stack

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![kubectl](https://img.shields.io/badge/kubectl-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=flat-square&logo=yaml&logoColor=white)

| Category | Tools |
| --- | --- |
| Container Orchestration | Kubernetes |
| CLI | kubectl |
| Local Runtime | Docker, optional local Kubernetes distributions |
| Documentation | Markdown |
| Configuration | YAML |

## Key Design Decisions

| Decision | Reason |
| --- | --- |
| Separate docs and labs | Notes explain concepts, labs preserve hands-on execution history. |
| Keep manifests outside labs | Reusable YAML should be easy to find without reading lab logs. |
| Track empty directories with `.gitkeep` | The learning workspace is visible from the first commit. |
| Ignore local cluster credentials | kubeconfigs, certificates, and secret manifests should not be committed. |

## Getting Started

### Prerequisites

- Git
- Docker or another container runtime
- kubectl
- A local or remote Kubernetes cluster

### Clone

```bash
git clone https://github.com/lsh1215/Kubernetes.git
cd Kubernetes
```

### Suggested Workflow

```bash
# Check cluster access
kubectl cluster-info

# Explore manifests
find manifests -type f

# Record each practice session under labs/
```

## Project Structure

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

## Study Roadmap

- [ ] Kubernetes architecture and core objects
- [ ] Workload resources: Pod, ReplicaSet, Deployment, StatefulSet, DaemonSet
- [ ] Service discovery and networking
- [ ] ConfigMap, Secret, and environment configuration
- [ ] Volumes and persistent storage
- [ ] Ingress and traffic routing
- [ ] Scheduling, probes, rollout, and autoscaling
- [ ] Troubleshooting and debugging workflows
- [ ] Security, RBAC, and policy basics
- [ ] Observability and GitOps practices

## Documentation Model

This repository separates human-readable study documents from long-term AI memory.

| Location | Role |
| --- | --- |
| `docs/study-log/` | Daily study flow, meaningful questions, experiment results, unresolved points |
| `docs/concepts/` | Stable concept documents with root-cause context and references included |
| `docs/deep-dives/` | Broad research, comparisons, history, and architecture tradeoffs spanning multiple concepts |
| `docs/labs/` | Hands-on practice records, commands, observations, errors, and fixes |
| `manifests/` | Runnable Kubernetes YAML examples |
| `omx_wiki/` | User-created AI-oriented long-term memory |

## License

This repository is currently maintained as a personal study project. Add a license before reusing or distributing the content publicly.
