+++
date = '2025-06-13T20:58:51+09:00'
draft = false
title = '[Airflow 기초 자동화 - Airflow → MLflow → FastAPI]'
categories = ['MLOps Pipeline', 'Airflow', 'MLflow', 'FastAPI']
+++

## 🧭 전체 흐름 예시

```
[AIRFLOW DAG 실행]
   ↓
[train_mlflow.py]
   - iris 모델 학습
   - 파라미터/메트릭 로깅
   - 모델 Registry 등록
   ↓
[promote_mlflow.py]
   - 최신 모델을 Production으로 전환
   ↓
[FastAPI]
   - models:/IrisModel/Production → 실시간 예측
```

👉 실습 코드는 [🔗 GitHub (Airflow + MLflow + FastAPI)](https://github.com/keonhoban/mlops-infra-labs/tree/main/airflow_mlflow_fastapi)

---

## ✅ [1단계] 프로젝트 기본 폴더 구조 설계

### 📁 1. 전체 디렉토리 구성도

```
mlops_project/
├── airflow/                       🛫 Airflow 설정 및 DAG 스케줄러
│   ├── dags/                      ← DAG 정의 디렉토리
│   │   └── train_with_mlflow.py   ← 학습 DAG (MLflow 연동)
│   ├── Dockerfile.airflow         ← Airflow용 Dockerfile
│   ├── requirements.txt           ← Airflow 의존성
│   └── .dockerignore
│
├── fastapi/              ⚡ FastAPI 예측 API 서버
│   ├── app/
│   │   └── main.py        ← 모델 서빙 엔드포인트
│   ├── Dockerfile.api     ← FastAPI용 Dockerfile
│   ├── requirements.txt   ← FastAPI 의존성
│   └── .dockerignore
│
├── ml_code/               🧠 ML 학습 및 프로모션 코드
│   ├── train_mlflow.py     ← 모델 학습 및 MLflow 로깅
│   └── promte_mlflow.py    ← 모델 프로모션 (Staging → Production)
│
├── mlflow_store/          🗂️ MLflow 저장소 경로 (볼륨)
│   ├── Dockerfile.mlflow   ← MLflow 서버 커스터마이징
│   ├── mlflow.db           ← Model Registry DB (sqlite)
│   ├── mlruns/             ← 실험 로그 디렉토리
│   ├── artifacts/          ← 모델 아티팩트 저장소
│   └── .dockerignore
│
├── docker-compose.yaml    🧩 전체 서비스 구성 정의
├── .env                   🔐 민감 정보 (.env로 분리)
├── README.md              📝 전체 프로젝트 문서화
├── .gitignore
└── .dockerignore

```

---

## ✅ [2단계] `docker-compose.yaml` 통합 구성

### 🧭 구성 목표

| 서비스명 | 설명 | 포트 |
| --- | --- | --- |
| `airflow` | DAG 실행 환경 (webserver/scheduler) | `8080` |
| `postgres` | Airflow 메타데이터 저장용 DB | 내부 통신 |
| `mlflow` | MLflow UI + Registry 기능 | `5000` |
| `fastapi` | 추론 API 서버 (모델 로딩) | `8000` |

- 이미지 사용시 주의 (UI만 제공하는 이미지 존재)

---

### 📄 `docker-compose.yaml` 전체 예시

```yaml
version: '3.8'

services:
  # 📦 PostgreSQL: Airflow 메타데이터 저장용 DB
  postgres:
    image: postgres:13
    container_name: postgres
    env_file:
      - .env  # ← 민감정보 분리 (아이디/비번)
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:  # ← 코드/데이터 공유 및 영속성 보장
      - postgres_data:/var/lib/postgresql/data  # ← DB 데이터 유지 (재시작 대비)

  # 🛫 Airflow: DAG 스케줄러 및 태스크 실행
  airflow:
    build:
      context: ./airflow           # → Airflow 전용 Dockerfile 경로
      dockerfile: Dockerfile.airflow
    container_name: airflow
    command: standalone           # → 로컬 테스트용 간단 실행 명령 
                      # (- Scheduler + Webserver + DB 초기화까지 자동으로 한번에 실행)
                      # (- 실무/운영에서는 airflow-webserver, airflow-scheduler 필드 분리)
    ports:
      - "8080:8080"               # → Airflow 웹 UI (localhost:8080)
    depends_on:
      - postgres                  # → DB가 먼저 올라와야 Airflow 시작 가능
    env_file:
      - .env
    environment:
      # Airflow 메타데이터 DB 연결 주소
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: ${AIRFLOW__CORE__SQL_ALCHEMY_CONN}
      # Airflow 예제 DAG 불러올지 여부
      AIRFLOW__CORE__LOAD_EXAMPLES: ${AIRFLOW__CORE__LOAD_EXAMPLES}  
      MLFLOW_TRACKING_URI: http://mlflow:5000  # → DAG 코드에서 MLflow 연동
    volumes:
      - ./airflow/dags:/opt/airflow/dags     # DAG 파일 mount
      - ./ml_code:/opt/airflow/ml_code       # 학습 코드 공유
      - ./mlflow_store:/mlflow               # 모델 저장소 공유

  # 🔬 MLflow: 실험 추적 + 모델 레지스트리 서버
  mlflow:
    build:
      context: ./mlflow_store                # 커스텀 Dockerfile 위치
      dockerfile: Dockerfile.mlflow
    ports:
      - "5000:5000"                          # → MLflow UI (localhost:5000)
    volumes:
      - ./mlflow_store:/mlflow              # 실험 로그 + DB + artifacts 저장
    environment:
      - MLFLOW_TRACKING_URI=http://0.0.0.0:5000  # 내부 컨테이너 기준 URI

  # ⚡ FastAPI: 모델 서빙 API
  fastapi:
    build:
      context: ./fastapi
      dockerfile: Dockerfile.api
    container_name: fastapi
    ports:
      - "8000:8000"                          # → 예측 API 엔드포인트 (localhost:8000)
    volumes:
      - ./fastapi/app:/app/app              # FastAPI app 디렉토리 mount
      - ./ml_code:/app/ml_code              # 학습/모델 코드 공유
      - ./mlflow_store:/mlflow              # 저장된 모델 불러오기 위한 mount

# 🗂️ 볼륨 정의 (Postgres DB 영속성 유지)
volumes:
  postgres_data:
```

---

## 🎁 추가로 해야 할 것

- Airflow 첫 실행 후엔 보통 **관리자 계정 생성**도 해줘야 함:

```bash
# airflow 컨테이너 접속
docker exec -it airflow bash

# 관리자 계정 생성
airflow users create \
  --username airflow \
  --password airflow \
  --firstname Keoho \
  --lastname Ban \
  --role Admin \
  --email airflow@example.com
```

---

### 🔁 [구축 Tip] **Airflow, FastAPI, MLflow 간 공유 볼륨 구조 확인**

| 공유 리소스 | 설명 |
| --- | --- |
| `./mlflow_store:/mlflow` (MLflow) | MLflow 서버가 쓰는 로그/모델 저장소 |
| `./mlflow_store:/mlflow` (Airflow) | 학습 후 모델 저장 위치 공유 |
| `./mlflow_store:/mlflow` (FastAPI) | 모델 추론 시 로드 경로 공유 |

➡ **경로 통일성**이 매우 중요함! 지금은 모두 `./mlflow`로 공유 (./mlflow 하위에 /mlruns 존재)

---

### ✅ [권장] `.env`로 민감 정보 분리하기

### 📄 1. `.env` 파일 작성

```
# .env

# PostgreSQL
POSTGRES_USER=airflow
POSTGRES_PASSWORD=airflow
POSTGRES_DB=airflow

# Airflow 환경 변수
AIRFLOW__CORE__SQL_ALCHEMY_CONN=postgresql+psycopg2://airflow:airflow@postgres/airflow
AIRFLOW__CORE__LOAD_EXAMPLES=False
```

> 👉 이 값들은 예시용이고, 실제로는 비밀번호나 DB 이름을 더 복잡하게 설정하는 게 좋음
> 

---

## ✅ [3단계] Dockerfile 구성

### 🐘 1. `Dockerfile.airflow` (Airflow 컨테이너)

> DAG 실행, ML 코드 실행, MLflow 로깅까지 가능한 환경을 만들자.
> 

📄 **`airflow/Dockerfile.airflow`**

```
FROM apache/airflow:2.8.1-python3.10

# 환경 변수 설정
ENV AIRFLOW_HOME=/opt/airflow

# Airflow 실행 사용자로 전환
USER airflow

# 필요한 추가 패키지 설치 (mlflow, scikit-learn 등)
RUN pip install --no-cache-dir \
    mlflow \
    scikit-learn \
    pandas
```

---

### ⚙️ `airflow/requirements.txt` 예시 (선택)

```
mlflow
scikit-learn
pandas
```

> ❗ 하지만 Dockerfile.airflow에서 직접 pip install 했기 때문에 꼭 필요하진 않지만, 관리용으로 두는 것도 좋음
> 

---

### 🚀 2. `Dockerfile.mlflow` (FastAPI 컨테이너)

📄 **`mlflow_store/Dockerfile.mlflow`**

```
FROM python:3.10-slim

# 시스템 도구 설치
RUN apt update && apt install -y curl sqlite3 git

# MLflow 설치
RUN pip install mlflow[extras] scikit-learn

# 작업 디렉토리
WORKDIR /mlflow

CMD ["mlflow", "server", \
     "--backend-store-uri=sqlite:///mlflow.db", \
     "--default-artifact-root=/mlflow/artifacts", \
     "--host=0.0.0.0", \
     "--port=5000", \
     "--serve-artifacts"]
```

> ❗ docker-compose.yaml 의 command 에서 진행안될시 사용
> 

---

### 🚀 3. `Dockerfile.api` (FastAPI 컨테이너)

📄 **`fastapi/Dockerfile.api`**

```
FROM python:3.10-slim

# 작업 디렉토리 설정
WORKDIR /app

# 앱 디렉토리 복사
COPY app /app/app

# 의존성 설치
COPY requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir --upgrade pip && \
    pip install -r /app/requirements.txt

# FastAPI 앱 실행 (uvicorn)
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

> ❗ 패키지 개수가 많다면 requirement를 COPY 하여 사용도 가능
> 

---

### ⚙️ `fastapi/requirements.txt`

```
fastapi
uvicorn
mlflow
scikit-learn
pydantic
```

> 필요 시 pandas, joblib, numpy도 추가 가능
> 

---

## 📁 FastAPI 구성 (`fastapi/app/main.py`)

```python
from fastapi import FastAPI
from pydantic import BaseModel
from typing import List
import mlflow.pyfunc
import pandas as pd

# 🎯 FastAPI 앱 인스턴스 생성
app = FastAPI()

# 📌 MLflow 추적 서버 URI 설정 (MLflow Tracking Server로 연결)
mlflow.set_tracking_uri("http://mlflow:5000")

# 🔄 Production 단계 모델 로드 (모델 레지스트리에서 이름 + 스테이지 기반 로드)
#     - "models:/IrisModel/Production" 형식
#     - 컨테이너 실행 시점에 모델이 이미 등록 & 프로덕션 상태여야 함
model = mlflow.pyfunc.load_model("models:/IrisModel/Production")

# 📦 입력 데이터 스키마 정의
#     - 클라이언트가 JSON으로 넘겨야 할 데이터 형태 명시
#     - pydantic으로 자동 유효성 검사 가능
class IrisInput(BaseModel):
    sepal_length: float
    sepal_width: float
    petal_length: float
    petal_width: float

# ✅ 헬스 체크용 기본 엔드포인트
@app.get("/")
def home():
    return {"message": "FastAPI + MLflow 예측 API"}

# 🔮 예측 요청 처리 엔드포인트
@app.post("/predict")
def predict(data: List[IrisInput]):
    # 1️⃣ 입력 데이터 → pandas DataFrame 변환 (= 2차원 구조 / 현재 1행 4열)
    input_df = pd.DataFrame([item.dict() for item in data])

    # 2️⃣ 모델 학습 시 사용했던 컬럼명으로 변경 및 데이터 순서 정렬 (스키마 맞춤)
    input_df.columns = [
        "sepal length (cm)",
        "sepal width (cm)",
        "petal length (cm)",
        "petal width (cm)"
    ]

    # 3️⃣ MLflow로부터 로드된 모델을 활용한 예측 수행 (= serving)
    preds = model.predict(input_df)

    # 4️⃣ 결과를 JSON 형태로 반환
    return {"predictions": preds.tolist()}
```

---

## ✅ [4단계] Airflow DAG + MLflow 연동

### 🎯 목표 요약

| 항목 | 내용 |
| --- | --- |
| DAG 역할 | PythonOperator로 ML 학습 스크립트 실행 |
| ML 코드 | `train_mlflow.py`및 `promote_mlflow.py` 를  실행하여 학습 및 승격 + MLflow 로깅  |
| 연동 결과 | 파라미터, 메트릭, 모델 저장 + Model Registry 등록후 스테이지 “production” 승격까지 자동화 |

---

## 🧪 1. ML 학습 코드 작성 (`ml_code/train_mlflow.py`)

```python
# ml_code/train_mlflow.py

import mlflow
import mlflow.sklearn
from sklearn.datasets import load_iris
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

def run_experiment():
  # 📍 MLflow 서버 주소 설정 (Tracking URI) 
  # (- docker-compose.yaml의 airflow에 environment로 MLFLOW_TRACKING_URI 존재시 생략 가능)
    mlflow.set_tracking_uri("http://mlflow:5000")  # 단, 로컬에서 진행시 필요

    # 🧪 실험 이름 설정 (없으면 자동 생성)
    mlflow.set_experiment("iris_experiment")

    # 🚀 새로운 실험 run 시작
    with mlflow.start_run() as run:
        # 📊 데이터 로딩
        data = load_iris()
        X, y = data.data, data.target

        # 🔍 모델 학습
        model = RandomForestClassifier(n_estimators=100, random_state=42)
        model.fit(X, y)
        preds = model.predict(X)

        # ✅ 성능 평가
        acc = accuracy_score(y, preds)

        # 📝 실험 정보 로깅
        mlflow.log_param("n_estimators", 100)
        mlflow.log_metric("accuracy", acc)

        # 📦 모델 저장 + 레지스트리 등록
        #    - `artifact_path`: 아티팩트 저장 디렉토리 이름
        #    - `registered_model_name`: 레지스트리에서 모델 이름으로 관리됨
        mlflow.sklearn.log_model(
            model,
            artifact_path="model",
            registered_model_name="IrisModel"
        )

        # 🖨️ 결과 출력
        print(f"✅ Run ID: {run.info.run_id}, Accuracy: {acc}")

# 🔧 직접 실행할 경우만 함수 실행
if __name__ == "__main__":
    run_experiment()
```

---

## 🧪 2. ML 학습 코드 작성 (`ml_code/promote_mlflow.py`)

```python
# ml_code/promote_mlflow.py

from mlflow.tracking import MlflowClient

def promote_model():
    # 🧭 MLflow 클라이언트 인스턴스 생성 (서버와 직접 통신)
    # (- docker-compose.yaml의 airflow에 environment로 MLFLOW_TRACKING_URI 존재함)
    client = MlflowClient()

    # 🔍 "IrisModel"의 등록된 모델 중 현재 어떤 Stage에도 배정되지 않은 최신 버전 가져오기
    latest_versions = client.get_latest_versions("IrisModel", stages=["None"])
    
    if not latest_versions:
        print("❗ 등록된 모델이 없습니다.")
        return

    # ⏫ 가장 최신 버전 선택 (= .version 을 통해 속성 값 추출 / .name or .stage 등 가능)
    latest_version = latest_versions[0].version

    # 🚀 해당 버전을 Production 단계로 전환
    client.transition_model_version_stage(
        name="IrisModel",
        version=latest_version,
        stage="Production"
    )

    # ✅ 결과 출력
    print(f"🚀 IrisModel version {latest_version} → Production 등록 완료")

# 🔧 Airflow나 CLI에서 호출될 수 있도록 함수화
```

---

## 🛠️ 2. Airflow DAG 작성 (`airflow/dags/train_with_mlflow.py`)

```python
# airflow/dags/train_with_mlflow.py

# 🔧 Airflow DAG 기본 구성
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime
import sys

# 📁 ml_code 디렉토리 경로 추가 (import 가능하도록 설정)
sys.path.append("/opt/airflow/ml_code")

# 📦 학습 함수와 승격 함수 import
from train_mlflow import run_experiment      # 모델 학습 및 등록
from promote_mlflow import promote_model    # 모델 스테이지 전환

# 🧾 DAG 기본 설정
default_args = {
    'start_date': datetime(2023, 1, 1),
    'retries': 1,  # 실패 시 재시도 횟수
}

# 📅 DAG 정의
with DAG(
    dag_id='train_with_mlflow',             # DAG 이름 (Airflow UI에 표시)
    default_args=default_args,
    schedule_interval=None,                 # 수동 실행 (스케줄 없음)
    catchup=False,                          # 과거 날짜 실행 안 함
    tags=['ml', 'mlflow'],                  # 태그 (필터링용)
) as dag:

    # 🧪 [1단계] 모델 학습 및 MLflow에 등록
    train_task = PythonOperator(
        task_id='run_training',
        python_callable=run_experiment,     # 실행할 함수 지정
    )

    # 🚀 [2단계] 최신 버전 모델을 Production으로 Promote
    promote_task = PythonOperator(
        task_id='promote_model_to_production',
        python_callable=promote_model,
    )

    # 📍 작업 실행 순서 정의: 학습 후 → 승격
    train_task >> promote_task
```

---

## 🔧 3. Docker 경로와 볼륨 정리 (복습)

| 컨테이너 | 로딩 경로 | 역할 |
| --- | --- | --- |
| Airflow | `/opt/airflow/ml_code/train_mlflow.py` | 학습 실행 |
| MLflow | `/mlflow/mlruns/` | 모델 저장소 |
| FastAPI | `/mlflow/mlruns/` | 모델 로드 |

➡ 로컬의 `/mlflow_store`를 **모든 컨테이너에 공통 mount**했기 때문에 문제없이 공유됨!

---

## ✅ 4. 실행 후 기대 결과

```bash
$ docker-compose up --build
```

Airflow 웹 UI에서 `train_with_mlflow` DAG 실행 →

✅ 모델이 학습되고

✅ `/mlflow/mlruns/`에 artifact가 저장되며

✅ `IrisModel`이 **Model Registry**에 등록됨
✅ `promote_mlflow.py` 실행을 통해, 등록된 모델을 **Staging** 또는 **Production** 단계로 자동 승격함

---

## 🔍 확인 방법

1. Airflow 웹 UI → `localhost:8080`
2. MLflow UI → `localhost:5000`
    - Experiments → `iris_experiment`
    - 모델 성능 로그 + `IrisModel` 등록 여부 확인 가능

---

### 🧪 테스트 예시

```bash
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sepal_length": 5.1,
      "sepal_width": 3.5,
      "petal_length": 1.4,
      "petal_width": 0.2
    },
    {
      "sepal_length": 6.0,
      "sepal_width": 2.9,
      "petal_length": 4.5,
      "petal_width": 1.5
    }
  ]'
```

> 예시 결과:
> 

```json
{
"predictions":[0,1]
}
```
