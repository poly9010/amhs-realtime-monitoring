# 데이터 준비 가이드

원본 데이터는 저장소에 포함되어 있지 않습니다. 아래 순서대로 준비하면 프로젝트 코드를 그대로 실행할 수 있습니다.

---

## 1. 데이터 정보

| 항목 | 내용 |
|---|---|
| 데이터명 | Turbofan Engine Degradation Simulation Data Set (C-MAPSS) |
| 제공 | NASA Prognostics Center of Excellence (PCoE) |
| 구성 | 하위셋 4종(FD001~FD004) × 파일 3개 + 설명 문서 |
| 형식 | 공백 구분 텍스트(.txt), 헤더 없음 |

다운로드: NASA PCoE 데이터 저장소 또는 Kaggle의 미러 데이터셋에서 받을 수 있습니다.

---

## 2. 폴더 구조

```
data/
├── README.md
├── raw/                      # 원본 (git 제외)
│   ├── train_FD001.txt
│   ├── test_FD001.txt
│   ├── RUL_FD001.txt
│   ├── ... (FD002 ~ FD004)
│   └── readme.txt            # 원 제공 설명 문서
└── processed/                # 가공 결과 (git 제외)
    └── cmapss.parquet
```

---

## 3. 파일 구조

| 파일 | 내용 |
|---|---|
| `train_FD00X.txt` | 엔진별 **고장까지의 전체 운전 이력** |
| `test_FD00X.txt` | 엔진별 이력이 **고장 전 임의 시점에서 잘림** |
| `RUL_FD00X.txt` | test 각 엔진의 **마지막 시점 잔여수명 정답** (한 줄에 하나) |

### 컬럼 (26열, 헤더 없음)

| 순서 | 컬럼 | 설명 |
|---|---|---|
| 1 | `engine_id` | 엔진 번호 |
| 2 | `cycle` | 운전 사이클 번호 (1부터 증가) |
| 3~5 | `op1`, `op2`, `op3` | 운전조건 설정값 |
| 6~26 | `s1` ~ `s21` | 센서 측정값 |

### 로드 예시
```python
import pandas as pd

cols = ["engine_id", "cycle", "op1", "op2", "op3"] + [f"s{i}" for i in range(1, 22)]
train = pd.read_csv("data/raw/train_FD001.txt", sep=r"\s+", header=None, names=cols)
```

---

## 4. 하위셋 특성 [공식 문서 기준 — 직접 확인 필요]

| 셋 | 운전조건 | 고장모드 | train 엔진 | test 엔진 |
|---|---|---|---|---|
| FD001 | 1 | 1 (HPC 열화) | 100 | 100 |
| FD002 | 6 | 1 | 260 | 259 |
| FD003 | 1 | 2 (HPC, Fan) | 100 | 100 |
| FD004 | 6 | 2 | 249 | 248 |

> ⚠️ 공식 설명 문서와 실제 파일의 엔진 수가 다른 사례가 보고되어 있습니다.
> 아래 명령으로 **직접 확인한 뒤 이 표를 갱신**하세요.
> ```python
> train.engine_id.nunique(), test.engine_id.nunique(), len(open("RUL_FD001.txt").readlines())
> ```

---

## 5. 통합 테이블 생성

```bash
python src/load.py
```

결과: `data/processed/cmapss.parquet`

| 컬럼 | 설명 |
|---|---|
| `subset` | FD001 ~ FD004 |
| `split` | train / test |
| `engine_id`, `cycle` | 엔진 번호, 사이클 |
| `op1`~`op3`, `s1`~`s21` | 운전조건, 센서값 |
| `RUL` | train에만 계산해 부여 (라벨링 방식은 `src/labeling.py`) |

---

## 6. 사용 규칙
- **학습은 train, 실시간 재생 시연은 test** 데이터를 사용합니다.
- train 내부에서 검증셋을 나눌 때는 **엔진 단위**로 나눕니다. 같은 엔진의 사이클이 학습과 검증에 섞이면 성능이 과대평가됩니다.
- test 정답은 각 엔진의 **마지막 시점 RUL 하나**뿐입니다. 중간 사이클의 정답은 없으므로, 재생 중 예측값 평가는 마지막 시점 기준으로 합니다.
- `data/raw`, `data/processed`는 `.gitignore`에 등록되어 커밋되지 않습니다.

---

## 7. 확인 체크리스트 (1주차)
- [ ] 각 파일의 행 수 / 열 수 / 엔진 수
- [ ] RUL 파일 줄 수 = test 엔진 수인지
- [ ] 엔진별 사이클 길이 분포 (최소 / 중앙값 / 최대)
- [ ] `op1`~`op3` 값 분포 → 운전조건이 실제로 몇 종류인지
- [ ] 센서 21개 중 값이 거의 변하지 않는 센서 목록
- [ ] 결측·중복 여부
- [ ] 원 제공 `readme.txt` 설명과 실제 데이터의 차이

확인 결과는 `02_데이터명세.md`에 기록합니다.

---

## 출처
A. Saxena and K. Goebel (2008). *Turbofan Engine Degradation Simulation Data Set*, NASA Prognostics Center of Excellence (PCoE) Data Set Repository, NASA Ames Research Center.
