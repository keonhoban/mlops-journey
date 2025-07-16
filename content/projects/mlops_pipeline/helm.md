+++
date = '2025-07-15T17:55:05+09:00'
draft = false
title = '[MLOps 플랫폼 구축 : Airflow-MLflow-FastAPI (Helm)]'
categories = ['MLOps Pipeline', 'Airflow', 'MLflow', 'FastAPI', 'NFS', 'PostgreSQL', 'AWS', 'Kubernetes', 'Helm', 'Git']
+++

## 🧩 실전 시나리오 기반 구성 배경

> 이 프로젝트는 단순 실습을 넘어서, 실제 발생하는 다음과 같은 문제들을 해결하기 위한 MLOps 인프라 구축을 목표로 설계되었습니다.

- **여러 모델 실험 결과가 뒤섞여 추적이 어려운 문제**  
  → MLflow Tracking 서버 + PostgreSQL 메타데이터 저장소 구성

- **모델 파일 및 로그가 로컬에만 저장되어 협업 및 재현성이 떨어지는 문제**  
  → S3 기반 artifact store 구성 + pyfunc 기반 모델 서빙 구조 설계

- **수작업 DAG 등록, 모델 배포 등의 비효율적 운영 문제**  
  → Airflow + GitSync 연동으로 파이프라인 자동화 및 버전 관리 가능

- **모델 버전 업데이트 시 수동 재배포로 인한 운영 오류 발생 가능성**  
  → MLflow 모델 등록 → FastAPI에서 자동 로딩 + 핫스왑 테스트 구조 구현

- **로컬 개발 환경에 의존적인 ML 워크플로우**  
  → Kubernetes 기반 컨테이너 환경으로 전환하여 확장성과 유지보수성 확보

이러한 문제 해결을 목표로, **프로덕션 환경에서 즉시 적용 가능한 인프라 구성**을 실험적으로 구축하였습니다.  
본 프로젝트의 각 구성 요소는 단순 데모를 넘어서, **“왜 이 구성이 필요했는가”에 대한 맥락**을 함께 설명합니다.

---

## 📌 전체 시리즈 요약

| 순서 | 주제 |
| --- | --- |
| 1 | [🔗 인프라 사전 구성 (Kubernetes, NFS, PostgreSQL, S3 등)](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/01/) |
| 2 | [🔗 Secret/보안 구성 및 Kubernetes 연동](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/02/) |
| 3 | [🔗 MLflow Tracking 서버 및 Registry 구축](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/03/) |
| 4 | [🔗 Airflow DAG Git 연동 및 Secret 기반 구성](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/04/) |
| 5 | [🔗 FastAPI 모델 서빙 & MLflow 모델 자동 로딩](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/05/) |
| 6 | [🔗 Airflow + MLflow + FastAPI 연결을 통해 모델 핫스왑](https://keonhoban.github.io/mlops-journey/posts/mlops-pipeline-helm/06/) |

---

## 💡 지금까지 구현한 아키텍처 요약

![07](/mlops-journey/images/07.png)

## 🎯 실무 관점에서 강점

| 항목 | 내용 |
| --- | --- |
| 모델 실험 자동화 | Airflow DAG + MLflow 연동으로 다양한 모델 버전 학습 자동화 |
| 서빙 안정성 | FastAPI가 `Production` Stage 기준으로 모델 로드 → 무중단 핫스왑 가능 |
| 보안 구성 | AWS 인증 정보 및 DB 정보는 Secret으로 주입 |
| 인프라 이식성 | Helm + Docker + Kubernetes 기반 → 어디서든 이식 가능 |
| 실시간 추론 확인 | Ingress 기반 UI/Endpoint 연결 → 바로 curl 테스트 가능 |

---

## 🔍 회고

### ✅ 프로덕션 환경 고려

- 운영 가능한 MLOps 구조로 설계 (유지보수/보안 고려)
- AWS S3, PostgreSQL, Kubernetes, GitOps까지 현실 환경 가정하고 구성
- Secret 설계, Volume 마운트, GitSync, 커스텀 이미지 등 세세한 부분까지 설계 주도

### ✅ 자동화 기반 설계

- Airflow를 통해 모델 학습 → Registry 등록 → 서빙까지 자동화
- 모델 핫스왑 실험까지 성공적으로 구현

---

## 🧱 부족했던 점 & 보완 계획

| 항목 | 개선 포인트 |
| --- | --- |
| 모니터링 | Prometheus + Grafana로 서빙/실험 성능 모니터링 추가 필요 |
| 모델 테스트 자동화 | pytest + CI 파이프라인 구성으로 품질 확보 고려 |
| Kubeflow 연계 | Kubeflow Pipelines 및 Katib 등과의 비교 분석 예정 |
| Terraform 기반 전환 | Helm 구성 요소를 코드로 관리하는 Terraform 인프라 전환 계획 |

---

## ✨ 마치며

이번 시리즈는 단순한 학습이 아니라, 

**MLOps 인프라를 직접 설계하고 구현한 기록**입니다!

운영을 고려한 아키텍처 설계, 자동화된 실험 → 서빙 흐름 구축,

그리고 무엇보다 스스로 설계하고 검증하며 구성하여

custom 플랫폼을 계속 고도화 시켜나갈 수 있다는 것이 가장 큰 성과라고 생각합니다.

앞으로도 여러 실험이나 고도화 기록들 및 TS 내용으로 찾아뵙겠습니다 감사합니다!

---

## 🙌 프로젝트 GitHub 저장소

- GitHub 코드: [[Helm] Airflow + MLflow + FastAPI](https://github.com/keonhoban/mlops-infra-labs/tree/main/airflow_mlflow_fastapi_helm)
