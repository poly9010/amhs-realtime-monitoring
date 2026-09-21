# PHM 2018 이온 밀 식각 장비 예지보전 (Predictive Maintenance)

반도체 식각 장비(이온 밀, Ion Mill Etching Tool)의 센서 데이터를 이용해 **Flowcool 관련 3가지 고장 모드**를 사전에 감지하고, 고장까지 남은 시간(TTF, Time-To-Failure)을 예측하는 프로젝트입니다.

> 세메스(SEMES) 입사를 목표로 진행하는 12주 포트폴리오 프로젝트입니다. 현재 **1주차(환경 세팅)** 진행 중입니다.

## 프로젝트 목표

- 식각 장비의 Flowcool 관련 3가지 고장 모드(F1 누설 / F2 압력 과다 / F3 압력 하한 이탈) 사전 감지
- 고장까지 남은 시간(TTF) 예측
- 실시간 모니터링 대시보드 구현 (Streamlit)
- **정확도보다 오경보율(false alarm rate)과 리드타임(lead time)을 중시**하는 현장 관점의 평가

## 데이터

- [PHM Society 2018 Data Challenge](https://www.phmsociety.org/data-challenge) — Ion Mill Etching Tool
- 출처: NASA DASHlink, PHM Data Challenges
- 용량이 커서(수십 GB) 저장소에는 포함하지 않으며, 다운로드 방법은 [`data/README.md`](data/README.md)를 참고하세요.

## 폴더 구조

```
.
├── 00_프로젝트개요.md      # 프로젝트 목적/배경/12주 계획
├── 01_진행현황.md          # 주차별 진행 상황, 채팅 요약 누적
├── 02_데이터명세.md        # 데이터 컬럼/품질 점검 명세
├── 03_의사결정로그.md      # 주요 의사결정과 이유(면접 대비 핵심 자료)
├── 04_코드구조.md          # 코드/폴더 구조, 코딩 규칙
├── data/                  # 원본·가공 데이터 (git 제외, README만 포함)
├── notebooks/             # 단계별 분석 노트북
├── src/                   # 재사용 코드 (전처리, 특징 추출, 평가 등)
├── app/                   # Streamlit 대시보드
├── reports/figures/       # README/발표용 그래프
└── requirements.txt
```

## 진행 상황

자세한 주차별 체크리스트와 작업 이력은 [`01_진행현황.md`](01_진행현황.md), 주요 결정과 그 이유는 [`03_의사결정로그.md`](03_의사결정로그.md)에서 확인할 수 있습니다.

## 환경

- Python 3.11, macOS (Apple Silicon, 16GB RAM) 기준
- 대용량 데이터 처리: Parquet + DuckDB/Polars
- 주요 라이브러리: pandas, polars, duckdb, scikit-learn, lightgbm, shap, streamlit

```bash
pip install -r requirements.txt
```

---

*이 저장소는 진행 중인 포트폴리오 프로젝트입니다. 내용은 매주 업데이트됩니다.*
