+++
date = '2026-02-14T17:10:56+09:00'
draft = false
title = 'About'
+++

# MLOps / ML Platform Engineer

Production-grade **ML Platform을 설계하고 운영하는 플랫폼 엔지니어**입니다.

DevOps 2년 3개월(가비아) 운영 경험을 기반으로,

**GitOps 기반 dev/prod 분리형 Production-grade E2E ML Platform을 직접 설계·구축·검증했습니다.**

단순히 모델을 배포하는 시스템이 아니라,

**Data → Feature → Training → Registry → Deploy → Inference → Monitoring**

전 과정을 자동화하고,

운영 환경에서 필요한 **롤백 전략, 확장성, 관측 체계, 장애 대응 구조까지 포함한 ML Platform 아키텍처**를 구현했습니다.

---

# 🔎 Platform Snapshot (요약)

- **GitOps**: ArgoCD 기반 dev/prod 완전 분리 환경
- **Orchestration**: Airflow DAG 기반 E2E 자동화
- **Model Registry**: MLflow Registry + alias 기반 Hot Swap / Rollback
- **Serving**: Triton Inference Server + FastAPI reload 구조
- **Observability**: Prometheus / Grafana / Alertmanager 기반 운영 관측
- **Reproducibility**: Feature Store-lite(버전화 + latest 고정), Feast 검증
- **Deployment Strategy**: Promotion / Canary / Shadow 3-way 분기 + Triton RollingUpdate zero-downtime
- **Security & Resilience**: NetworkPolicy(4서비스) + ResourceQuota + Fail-open 에스컬레이션 + Contract Testing
- **Architecture**: Core / Baseline / Optional 분리형 플랫폼 구조
- **Proof System**: GitOps 경계 / runtime / optional attach-detach / observability 검증 문서화
- **부하 테스트**: 136 RPS, p95 553ms, 에러율 0% (k6, 100 VU, 3노드 클러스터)

👉 모든 구성은 GitHub 및 블로그에 **Proof 형태로 공개**되어 있습니다.

---

# 🚀 대표 프로젝트

## Production-grade E2E ML Platform

GitOps 기반 dev/prod 환경 분리와

Airflow–MLflow–Triton–FastAPI–Observability를 포함한 **통합 ML Platform 프로젝트**입니다.

이 프로젝트는 단순 구현이 아니라,

- GitOps 경계 구조
- Optional attach / detach 구조
- Serving Runtime 상태
- Observability 시스템
- 운영 문서 및 Proof 체계

를 **실제 실행 결과와 캡처 기반으로 검증한 플랫폼**입니다.

🔗 프로젝트 보기

https://keonhoban.github.io/mlops-journey/projects/mlops_pipeline_e2e/01/

---

# 🔬 기술 설계 탐색 및 검증 기록

아래 글들은 현재 ML Platform을 구성하기까지 진행했던

기술 설계 탐색 및 검증 기록입니다.

### Observability 설계

Prometheus / Grafana / Alertmanager 기반

ML Platform 관측 체계 설계 및 운영 구조

https://keonhoban.github.io/mlops-journey/projects/mlops_pipeline_observability/01/

### Triton Serving 구조

Triton Inference Server 기반

모델 서빙 및 alias 기반 hot swap 구조 설계

https://keonhoban.github.io/mlops-journey/projects/triton/01/

### Feature Store (Lite + Feast 검증)

Feature Store-lite 구조와

Feast 기반 Feature Store 검증 기록

https://keonhoban.github.io/mlops-journey/projects/feature_store/01/

---

# 🔗 GitHub

### GitOps Repository

GitOps 기반 ML Platform 인프라 코드

https://github.com/keonhoban/mlops-infra-gitops

### Airflow DAG

ML Pipeline Orchestration 코드

https://github.com/keonhoban/airflow-dags-dev

### MLOps Experiments

ML Platform 실험 및 검증 코드

https://github.com/keonhoban/mlops-infra-labs

---

# 🏗️ Architecture Philosophy

제가 구축한 플랫폼은 실험용 구성이 아니라,

**운영 환경을 전제로 설계된 ML Platform**입니다.

- Kubernetes 기반 ML Platform
- GitOps 기반 배포 및 환경 분리 전략
- MLflow Tracking + Registry 운영 구조
- MLflow alias 기반 모델 교체 및 runtime reload
- DAG 기반 자동 학습·배포 파이프라인
- 장애 대응을 고려한 롤백 및 상태 전이 설계
- Canary 배포 경로를 포함한 3-way 분기 + NetworkPolicy 기반 네트워크 격리
- 메트릭·로그·알람 기반 운영 관측 체계

“모델을 올린다”가 아니라,

**지속적으로 운영 가능한 플랫폼을 만든다**는 관점으로 설계했습니다.

---

# 💼 DevOps / SRE Experience

가비아 DevOps팀 (System Engineer)

2023.01 ~ 2025.03

대규모 메일·인프라 운영 및 자동화를 담당했습니다.

## 주요 경험

### 무중단 메일 마이그레이션

- 신규 서버 프로비저닝
- DNS / MX / SPF / DKIM 전환
- 원복 시나리오 설계
- 증분 이전 자동화

### SMTP 발송 우회 자동화

- Loop 방지 로직 설계
- 중복 우회 감지
- Ansible 기반 자동화
- 로그 자동 관리 체계

### CI/CD 및 배포 운영

- GitLab CI + Docker + Helm
- Health Check 표준화
- CloudFront / Route53 기반 트래픽 전환

### Kubernetes 운영

- 노드 증설 및 안전 투입 절차
- Cordon / 검증 기반 운영 전략

운영 자동화와 안정성을 중심으로 경험을 축적했습니다.

---

# 🛠 Tech Stack

### ML Platform

MLflow / Airflow / Triton / FastAPI

Feature Store-lite / Feast

Prometheus / Grafana / Alertmanager

### Infrastructure

Kubernetes / ArgoCD / Docker

AWS (Route53, CloudFront 등)

---

# 📜 Certifications

- AWS Solutions Architect – Professional
- AWS Solutions Architect – Associate
- 정보처리기사
- 리눅스 마스터 2급
- 네트워크 관리사

---

# 🎓 Education

컴퓨터공학 학사 (학점은행제, 4.2 / 4.5)

동의과학대학교 의무행정과 졸업

---

# 🎯 Direction

단순 모델 배포 엔지니어가 아니라,

**Production-grade ML Platform을 설계하고 운영 안정성을 개선하는 플랫폼 엔지니어**를 지향합니다.

---

# 📬 Contact

Email: [keonho0510@naver.com](mailto:keonho0510@naver.com)

GitHub: https://github.com/keonhoban
