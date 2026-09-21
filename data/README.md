# 데이터 다운로드 방법

이 프로젝트는 [PHM Society 2018 Data Challenge](https://www.phmsociety.org/data-challenge) — Ion Mill Etching Tool 데이터를 사용합니다.

- 출처: NASA DASHlink > PHM Data Challenges > 2018 PHM Data Challenge
- 용량이 커서(원본 기준 수십 GB) 이 저장소에는 포함하지 않습니다.
- 다운로드 후 아래 구조로 배치하세요 (둘 다 `.gitignore`에 포함되어 git에는 올라가지 않습니다):

```
data/
├── raw/          # 원본 CSV
└── processed/    # Parquet 변환 및 요약 특징
```

자세한 컬럼 명세와 품질 점검 결과는 [`../02_데이터명세.md`](../02_데이터명세.md)를 참고하세요.
