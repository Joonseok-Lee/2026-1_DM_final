# 노트북 인덱스 & 파이프라인 (최종 정리)

> **★ 최종 모델 = nb24** (`24_final_success_model`) · 출력 `final_success_score.csv` · 검증 nb24 §24.4(상관+κ)+nb23. nb01~23은 과정, nb24가 결론.

전체 24개 노트북, 누설 제거·정직 검증 완료(전부 0 오류). 핵심 산출물은 **nb24(최종 단일 통합 모델)** + **nb21/22(통합점수·4사분면 진단)**.

## 노트북 순서 (01~24)

### A. 데이터 (01~03)
| # | 노트북 | 역할 |
|---|---|---|
| 01 | collect | 서울시 상권 6종 API + 팀 데이터 수집 |
| 02 | eda | 탐색·결측 점검(상위 50업종이 매출 99% 커버) |
| 03 | features | 피처 생성 → `features.csv` |

### B. 모델링·해석 (04~09)
| # | 노트북 | 역할 | 정직 결과 |
|---|---|---|---|
| 04 | models | 7종 비교 + high-vs-low 튜닝 | 이진 84% / **명확등급 90%** / 회귀 R²0.59 |
| 05 | ablation | 변수그룹 기여 | 외부변수 기여 ≈0(누설 제거 후) |
| 06 | interpret | 트리 규칙 | R² 0.42(트리)/0.54(RF) |
| 07 | feature_importance | 변수 중요도 | 1위 lunch_bias·age_entropy(누설 제거 후) |
| 08 | unsupervised | KMeans·PCA | 112변수 |
| 09 | map | 자치구 지도 | — |

### C. 평가기·검증 (10~19)
| # | 노트북 | 역할 |
|---|---|---|
| 10 | evaluator_gu | 자치구 입지 평가기(점포당 매출로 수정) |
| 11 | evaluator_point | 행정동 평가기 |
| 12~15 | validation/summary/deep/fix | 검증 여정·요약(누설·날조 교정 반영) |
| 16 | seoul_api_comparison | 서울시 상권 변화지표 vs 우리 모델(r≈0, 다른 차원) |
| 17 | external_validation | 폐업·KOSIS 외부검증(KOSIS는 출처미확인 표기) |
| 18 | presentation | 발표용(누설 캐비엇 추가) |
| 19 | strict_model_and_honest_framing | **누설 제거 정직 모델 정립**(이진 83.6%/R²0.64) |

### D. 창업기상도 통합·최종 (20~24)
| # | 노트북 | 역할 |
|---|---|---|
| 20 | sbiz_weather_comparison | 창업기상도 API vs 우리 모델(지표별 상관, 누설 제거 후에도 결론 동일) |
| 21 | integrated_location_score | **통합점수** = 매출 P(high) × 영업기간, 4사분면 |
| 22 | final_location_evaluation | 입지 추천·평가함수 |
| 23 | single_vs_fusion | **단일 통합 모델 vs 2-모델 융합** 비교(AUC 0.94 vs 0.79) |
| 24 | **final_success_model** | **★최종 주 산출물**: 단일 통합 모델(성공확률) + 융합 4사분면 진단 + 창업기상도 API 검증(상관+값일치도) |
| 25 | success_definition_sensitivity | '성공' 정의 7종 민감도(저폐업 정의는 예측 불가, 어떤 정의도 API와 약일치) |

## 최종 모델 구조
- **매출축**: 누설 제거 정직 분류모델 P(high) — high-vs-low+튜닝 **~90%**. 피처 확장(유동인구·직장인구·시설 상세, features.csv 164컬럼) — 정확도 기여 미미(+0.002, 천장은 라벨 노이즈).
- **생존축**: **평균 영업기간**(OPR_SALE_MT_AVRG) — 측정 지수, 신뢰도 r=0.94, 창업기상 survival과 r=0.47.
- **주 예측기(nb24)**: 입지 피처 → P(성공=고매출&장기영업), **AUC 0.94**(정체성 제외 0.90 — 실제 신호).
- **진단(nb21/22)**: 매출×생존 4사분면(②고위험·고수익 경고).
- **API 검증(상관+값일치도)**: 성공확률 vs 창업기상 — 종합/생존은 상관 r≈0 **및 값 일치도도 우연 수준(Cohen κ≈0, 이진 일치율 ~0.5)**, 경쟁력만 약한 일치(r0.12/κ0.10). → 우리 모델은 매출·경쟁력 차원, 공단 생존지수와 다름(상관·값 모두).

## 빌드 파이프라인 (재현 순서)
```
1) (base) features.csv 생성  →  scripts/add_flpop_features.py (유동인구 fl_ 피처 추가)  ※ 둘 다 필요
2) scripts/build_honest_model.py     → honest_predictions.csv (p_high_90 = 90% 매출모델)
3) scripts/build_sbiz_weather_table.py → sbiz_weather_gu.csv (창업기상도 캐시, 검증용)
4) 노트북 nb04~24 (앞에서부터)
```
환경: `~/anaconda3/bin/python` (pandas, sklearn 1.6).

## 폐기·대체된 산출물 (최종 미사용)
- `build_survival_model.py` / `survival_predictions.csv` — 폐업 ML예측(67%, 노이즈) → **평균 영업기간으로 대체**.
- `build_repro_model.py` — 창업기상도 distillation(지도학습 재현) → 사용자 지시로 폐기.
- 초기 누설 모델(94%/R²0.92) — 타깃 누설 산물로 폐기.

## 핵심 정직성 (감사 결과)
- 타깃 누설 제거(rent_to_sales 등 5개) → 진짜 성능 확정. 상세 `AUDIT_FINDINGS.md`.
- 모든 모델 GroupKFold/holdout 정직 검증, 강의범위(L03~L13) 내.


## 🔎 11~20 역할별 정리 (번호는 작업 순서라 주제가 섞여 있음 — 주제로 묶으면)
| 주제 | 노트북 | 비고 |
|---|---|---|
| **평가기** | 10(자치구), 11(행정동) | ⚠️ 상권 평균 기반, 개별 점포 예측 아님 |
| **검증** | 12(여정), 14(심화), 15(보완), 17(폐업·KOSIS) | 모델 약점·외부 대조 |
| **외부 API 비교** | 16(서울시 변화지표), 20(창업기상도) | 우리 모델 vs 외부지표 |
| **정직화·요약** | 19(누설제거 정직모델 ★), 13(중간요약), 18(발표·초기) | 13·18은 중간단계(최종 nb24) |
> 각 노트북 상단에 **[역할 태그]** 표시. 최종 결론은 **nb24**, 검증지표는 nb24 §24.4 + nb23.
