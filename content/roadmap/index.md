---
title: "MLOps Learning Roadmap"
date: 2026-03-21
draft: false
---

## 이 블로그의 읽기 순서

> 이 로드맵은 블로그의 55편 포스트를 **5단계 학습 경로**로 정리한 것입니다.
> Level 1부터 순서대로 따라가면 Kubernetes 기초부터 프로덕션급 E2E ML 플랫폼까지 자연스럽게 이해할 수 있습니다.

---

## Level 1. 기초 — Kubernetes & Airflow 기본기 (10편)

기반 기술을 먼저 익힙니다. Kubernetes 리소스 구조와 Airflow DAG 작성법을 다룹니다.

### Kubernetes

1. [Kubernetes 1단계: Deployment & Service](/mlops-journey/posts/kubernetes/01/) — Pod 배포와 Service 노출 기본 개념
2. [Kubernetes 2단계: ConfigMap & Secret](/mlops-journey/posts/kubernetes/02/) — 설정 분리와 민감 정보 관리
3. [Kubernetes 3단계: Ingress & Nginx Controller](/mlops-journey/posts/kubernetes/03/) — 외부 트래픽 라우팅 구성
4. [Kubernetes 4단계: Helm](/mlops-journey/posts/kubernetes/04/) — 차트 기반 패키징과 배포
5. [Kubernetes 5단계: Prometheus + Grafana 모니터링](/mlops-journey/posts/kubernetes/05/) — 클러스터 메트릭 수집과 시각화

### Airflow

6. [Airflow 1단계: 로컬 환경에서 기본 DAG 실행](/mlops-journey/posts/airflow/01/) — DAG 구조와 로컬 실행 환경 구성
7. [Airflow 2단계: Python & Bash Operator + XCom](/mlops-journey/posts/airflow/02/) — Operator 종류와 Task 간 데이터 전달
8. [Airflow 3단계: ML 파이프라인 DAG 구성](/mlops-journey/posts/airflow/03/) — 학습 파이프라인을 DAG로 표현
9. [Airflow 4단계: BashOperator로 외부 학습 스크립트 실행](/mlops-journey/posts/airflow/04/) — 외부 Python 스크립트 호출 패턴
10. [Airflow 5단계: PythonOperator + MLflow Tracking 연동](/mlops-journey/posts/airflow/05/) — 실험 추적과 DAG 통합

---

## Level 2. 플랫폼 구축 — Helm 기반 MLOps 인프라 (7편)

Kubernetes 위에 MLflow·Airflow·FastAPI를 Helm으로 배포하고, 첫 모델 서빙까지 연결합니다.

1. [MLOps 플랫폼 구축 1단계: 인프라 설계 및 환경 준비](/mlops-journey/posts/mlops-pipeline-helm/01/) — NFS·PostgreSQL·S3 인프라 레이아웃
2. [MLOps 플랫폼 구축 2단계: S3 & PostgreSQL 연동 Secret 관리](/mlops-journey/posts/mlops-pipeline-helm/02/) — 외부 스토리지 연결과 시크릿 전략
3. [MLOps 플랫폼 구축 3단계: MLflow Helm 구성](/mlops-journey/posts/mlops-pipeline-helm/03/) — PostgreSQL + S3 백엔드 MLflow 배포
4. [MLOps 플랫폼 구축 4단계: Airflow GitSync + Secret 연동](/mlops-journey/posts/mlops-pipeline-helm/04/) — DAG Git 동기화와 외부 DB 연결
5. [MLOps 플랫폼 구축 5단계: FastAPI 서빙 및 핫스왑 구조](/mlops-journey/posts/mlops-pipeline-helm/05/) — MLflow 모델을 FastAPI로 서빙
6. [MLOps 플랫폼 구축 6단계: 실시간 모델 핫스왑 실험](/mlops-journey/posts/mlops-pipeline-helm/06/) — 무중단 모델 교체 검증
7. [[TS] Airflow 기초 자동화 트러블슈팅](/mlops-journey/posts/troubleshoot/01/) — Airflow → MLflow → FastAPI 연동 이슈 해결

---

## Level 3. 운영 고도화 — GitOps·롤백·보안 (12편)

프로덕션 수준의 운영 체계를 구축합니다. A/B 서빙, 롤백 자동화, CI/CD, 시크릿 관리까지 다룹니다.

1. [MLOps 운영 고도화 0단계: FastAPI A/B·Canary·Blue-Green 서빙 베이스](/mlops-journey/posts/mlops-platform-gitops/01/) — 멀티 모델 라우팅 기반 설계
2. [MLOps 운영 고도화 1단계: 핫스왑 고도화](/mlops-journey/posts/mlops-platform-gitops/02/) — NFS 기반 모델 교체 안정화
3. [MLOps 운영 고도화 2단계: Slack Alert 통합](/mlops-journey/posts/mlops-platform-gitops/03/) — 파이프라인 알림 자동화
4. [MLOps 운영 고도화 3단계: 모델 롤백 자동화](/mlops-journey/posts/mlops-platform-gitops/04/) — 스냅샷 기반 자동 롤백
5. [MLOps 운영 고도화 4단계: FastAPI 로그 안정화](/mlops-journey/posts/mlops-platform-gitops/05/) — 로그 유실 방지와 NFS 안정성
6. [MLOps 운영 고도화 5단계: Airflow 안정화 & FastAPI HTTPS](/mlops-journey/posts/mlops-platform-gitops/08/) — TLS 보안과 스케줄러 안정화
7. [MLOps 운영 고도화 6단계: GitOps 고도화](/mlops-journey/posts/mlops-platform-gitops/06/) — ArgoCD 동기화 전략 정교화
8. [MLOps 운영 고도화 7단계: ArgoCD Notifications 자동화](/mlops-journey/posts/mlops-platform-gitops/07/) — 배포 상태 Slack 자동 알림
9. [MLOps 운영 고도화 8단계: CI/CD 운영 자동화](/mlops-journey/posts/mlops-platform-gitops/09/) — GitHub Actions + Helm Lint 파이프라인
10. [MLOps 운영 고도화 9단계: 시크릿 관리 & 키 회전 자동화](/mlops-journey/posts/mlops-platform-gitops/10/) — SealedSecret + AWS 키 로테이션
11. [Airflow 6단계: KubernetesExecutor 선택 이유](/mlops-journey/posts/airflow/06/) — CeleryExecutor 대비 장단점 분석
12. [Airflow 7단계: DAG CI 구축](/mlops-journey/posts/airflow/07/) — test_dag_integrity + GitHub Actions 자동 검증

---

## Level 4. 관측성 & 서빙 — Monitoring·Triton·Feature Store (15편)

관측 가능한 플랫폼을 만들고, Triton 고성능 서빙과 Feature Store를 도입합니다.

### Observability & Data Pipeline

1. [Observability 1단계: kube-prometheus-stack + GitOps 구축](/mlops-journey/posts/mlops-platform-observability/01/) — Prometheus 스택 GitOps 배포
2. [Observability 2단계: Alertmanager Slack & 트러블슈팅](/mlops-journey/posts/mlops-platform-observability/02/) — 알림 라우팅과 장애 대응
3. [Observability 3단계: Prometheus/KSM/Kubelet 완전 분리 구조](/mlops-journey/posts/mlops-platform-observability/03/) — 메트릭 수집기 아키텍처 분리
4. [Observability 4단계: Loki/Promtail 로그 파이프라인 구축](/mlops-journey/posts/mlops-platform-observability/04/) — 중앙 집중 로그 수집
5. [Observability 5단계: 운영 중 실제 이슈 & 해결 과정](/mlops-journey/posts/mlops-platform-observability/05/) — 실전 트러블슈팅 사례
6. [Observability 6단계: FastAPI Dashboard & Alert Library](/mlops-journey/posts/mlops-platform-observability/06/) — 서빙 전용 대시보드 구축
7. [Observability 7단계: Data Pipeline 구축](/mlops-journey/posts/mlops-platform-observability/07/) — Airflow 기반 데이터 수집 파이프라인
8. [Observability 8단계: Data Pipeline 고도화](/mlops-journey/posts/mlops-platform-observability/08/) — Grafana + Loki 통합 모니터링

### Triton 서빙 플랫폼

9. [Triton 서빙 플랫폼 - Triton 구축](/mlops-journey/posts/triton/01/) — ONNX 모델 기반 Triton 배포
10. [Triton 서빙 플랫폼 - MLflow → Triton 자동 배포 파이프라인](/mlops-journey/posts/triton/02/) — 모델 레지스트리 연동 자동화
11. [Triton 서빙 플랫폼 - Alerting 운영 표준 매뉴얼](/mlops-journey/posts/triton/03/) — Triton 전용 알림 체계
12. [Triton 서빙 플랫폼 - dynamic_batching + instance_group](/mlops-journey/posts/triton/04/) — 배치 최적화와 인스턴스 튜닝

### Feature Store & MLflow

13. [Feature Store & Feast - Feature Store-lite](/mlops-journey/posts/mlops-platform-feature-store/01/) — 경량 Feature Store 설계
14. [Feature Store & Feast - Feast](/mlops-journey/posts/mlops-platform-feature-store/02/) — Feast 도입과 운영
15. [MLflow Model Registry: alias 기반 모델 버전 관리](/mlops-journey/posts/mlflow/01/) — promotion/shadow alias 전략

---

## Level 5. E2E 통합 — 전체 플랫폼 설계·검증 (11편)

지금까지 구축한 모든 컴포넌트를 하나의 플랫폼으로 통합하고, 설계 의도부터 성능 검증까지 다룹니다.

1. [GitOps 기반 E2E ML Platform - 설계 의도](/mlops-journey/posts/mlops-platform-e2e/01/) — 플랫폼 전체 설계 철학과 목표
2. [GitOps 기반 E2E ML Platform - 운영 경계](/mlops-journey/posts/mlops-platform-e2e/02/) — 레포·팀·책임 경계 정의
3. [GitOps 기반 E2E ML Platform - GitOps 구조](/mlops-journey/posts/mlops-platform-e2e/03/) — ArgoCD 멀티레이어 동기화
4. [GitOps 기반 E2E ML Platform - 토글 구조](/mlops-journey/posts/mlops-platform-e2e/04/) — 컴포넌트 탈부착 설계
5. [GitOps 기반 E2E ML Platform - 운영 환경 반영 제어](/mlops-journey/posts/mlops-platform-e2e/05/) — 환경별 설정 분리와 반영 전략
6. [GitOps 기반 E2E ML Platform - 모델 반영 제어](/mlops-journey/posts/mlops-platform-e2e/06/) — 품질 게이트와 자동 교체 흐름
7. [GitOps 기반 E2E ML Platform - 모델 등록/서빙 반영 분리](/mlops-journey/posts/mlops-platform-e2e/07/) — MLflow 등록과 Triton 서빙 분리
8. [GitOps 기반 E2E ML Platform - 실제 동작 확인](/mlops-journey/posts/mlops-platform-e2e/08/) — 전체 파이프라인 실행 검증
9. [GitOps 기반 E2E ML Platform - 운영 제어 구조](/mlops-journey/posts/mlops-platform-e2e/09/) — 보안·권한·시크릿 통합 관리
10. [GitOps 기반 E2E ML Platform - 운영 문서화](/mlops-journey/posts/mlops-platform-e2e/10/) — One Commit Flow와 운영 매뉴얼
11. [k6로 검증한 Triton + FastAPI 서빙 성능](/mlops-journey/posts/mlops-platform-e2e/11/) — 136 RPS, p95 553ms 부하 테스트 결과
