# 창업 입지 성공률 모델 (2026-1 데이터마이닝 기말)

지도 위의 한 지점(상권/행정동/자치구)에 대해 **"그 자리에서 그 장사를 했을 때의 성공 확률"** 을
입지 피처만으로 예측하는 모델입니다. 서울시 상권 공공데이터 + 팀 수집 데이터(부동산·임대료,
가맹점·창업비·매출, 행정동 미시입지, 인구)를 결합해 만들었습니다.

> **★ 최종 모델 = `notebooks/24_final_success_model.ipynb`** · 출력 `data/processed/final_success_score.csv`
> nb01~23은 과정(수집·EDA·모델비교·검증), **nb24가 결론**입니다.

---

## 핵심 결과 (정직 검증 기준)

| 항목 | 값 |
|---|---|
| 성공 예측 분류기 AUC | **0.94** (입지 정체성 변수 제외 시 0.90 — 실제 신호) |
| 매출 분류 (high-vs-low + 튜닝) | 정확도 **~90%** |
| 회귀(매출) R² | **0.64** (정직 모델, nb19) |
| 생존축 | 평균 영업기간(OPR_SALE_MT_AVRG), 신뢰도 r=0.94 |

> ⚠️ **정직성 주의**: 초기에 나온 94% 정확도 / R² 0.92 는 **타깃 누설(target leakage)** 의 산물로 폐기했습니다.
> 누설 변수(rent_to_sales 등 5개)를 제거한 뒤의 **진짜 성능이 위 표**입니다. 전체 감사 내역은
> 발표/노트북 상단 캐비엇과 인덱스를 참고하세요.

---

## 저장소 구조

```
.
├── notebooks/                # 분석 노트북 25개 (01~25) + 발표자료/보고서
│   ├── 01_collect ~ 03_features      # 데이터 수집·EDA·피처
│   ├── 04_models ~ 09_map            # 모델 비교·해석·중요도·지도
│   ├── 10 ~ 19                       # 평가기·검증·정직화
│   ├── 20 ~ 24                       # 창업기상도 통합 · ★최종 모델(nb24)
│   ├── 25_success_definition_sensitivity
│   ├── 발표자료_창업입지평가.pdf
│   └── 보고서_5장6장.docx
├── data/
│   ├── raw/                   # 원천 데이터 (서울시 상권 API 6종 등)
│   ├── processed/            # 가공 산출물 (features.csv, final_success_score.csv ...)
│   ├── team1 ~ team4/        # 팀별 수집 데이터
│   └── ...
├── NOTEBOOK_INDEX.md         # 노트북별 상세 역할 / 파이프라인 설명
└── README.md
```

---

## 사용 방법

### 1. 클론 (Git LFS 필수)

대용량 CSV(`data/team3/team3_adstrd_selng.csv` 177MB 등)는 **Git LFS** 로 저장되어 있습니다.
LFS 없이 클론하면 실제 데이터 대신 포인터 텍스트만 받습니다.

```bash
# git-lfs 설치 (최초 1회)
brew install git-lfs        # macOS
#  또는  sudo apt install git-lfs   (Ubuntu)
git lfs install

# 클론 (LFS 파일 자동 다운로드)
git clone https://github.com/Joonseok-Lee/2026-1_DM_final.git
cd 2026-1_DM_final

# 이미 클론했는데 csv가 포인터로 보이면
git lfs pull
```

### 2. 환경

```bash
# Python 3.10+ (개발 환경: anaconda)
pip install pandas numpy scikit-learn==1.6 matplotlib seaborn joblib jupyter
jupyter lab        # 또는 jupyter notebook
```

> 모델 직렬화 파일(`.joblib`)은 **scikit-learn 1.6** 으로 저장되었습니다. 버전이 크게 다르면
> 재학습이 필요할 수 있습니다.

### 3. 노트북 실행 순서

노트북은 번호 순서대로 의존합니다. 결론만 보려면 **nb24** 부터 보세요.

```
01_collect → 02_eda → 03_features        # data/processed/features.csv 생성
04_models → 05_ablation → 06_interpret → 07_feature_importance
08_unsupervised → 09_map
10~19  (평가기 · 검증 · 정직화)
20~23  (창업기상도 비교 · 통합점수 · 융합)
24_final_success_model   ★ 최종 산출물 final_success_score.csv
```

전체 재현이 목적이 아니라면, 가공 산출물이 `data/processed/`에 이미 들어 있으므로
원하는 노트북을 바로 열어 실행할 수 있습니다.

---

## 주요 산출물 (data/processed/)

| 파일 | 내용 |
|---|---|
| `final_success_score.csv` | **★ 최종 성공 점수** (nb24) |
| `features.csv` | 입지 피처 테이블 (164컬럼) |
| `honest_predictions.csv` | 누설 제거 90% 매출 분류 결과 |
| `integrated_location_score.csv` | 통합점수(매출 P(high)×영업기간) · 4사분면 |
| `model_comparison.csv` | 7종 모델 비교 |
| `best_classifier_hgb.joblib` / `best_regressor_hgb.joblib` | 최종 학습 모델 |
| `data_dictionary.md` | 피처 사전 |

---

## 모델 구조 요약

- **매출축**: 누설 제거 정직 분류모델 P(high매출) — high-vs-low + 튜닝 ~90%.
- **생존축**: 상권 평균 영업기간(OPR_SALE_MT_AVRG) — 측정 지수(예측 아님), 신뢰도 r=0.94.
- **최종 예측기(nb24)**: 입지 피처 → P(성공 = 고매출 & 장기영업), **AUC 0.94**.
- **진단(nb21/22)**: 매출×생존 4사분면 (고위험·고수익 구간 경고).
- **외부 검증**: 소상공인 **창업기상도 API** 와 비교 — 우리 모델은 *매출·경쟁력* 차원으로,
  공단 *생존지수* 와는 상관·값 모두 낮음(다른 차원임을 확인).

자세한 노트북별 역할·파이프라인은 [`NOTEBOOK_INDEX.md`](NOTEBOOK_INDEX.md) 참고.

---

## 데이터 출처

- 서울 열린데이터광장 — 서울시 상권분석 서비스 (추정매출·점포·생존율·유동인구·직장인구·집객시설 등)
- 소상공인시장진흥공단 — 창업기상도 OpenAPI (검증용)
- 팀 수집: 부동산·임대료(team1), 가맹점·창업비·매출(team2), 행정동 미시입지(team3), 인구·소비(team4)

> 공공데이터 재배포 정책은 각 출처의 라이선스를 따릅니다. 학습/연구 목적의 기말 프로젝트 산출물입니다.
