# 데이터 사전 — features.csv (v2)

| 변수 | 설명 | 구분 |
|---|---|---|
| `THSMON_SELNG_AMT` | 월매출(원) | 타깃 |
| `log_sales` | log(매출) | 타깃 회귀 |
| `sales_class` | 매출 3분위 | 타깃 분류 |
| `avg_ticket` | 매출÷건수 | 기존 가공 |
| `momentum` | 전분기 대비 매출 차 | 기존 가공 |
| `anchor_score` | 지하철·대학·병원·공공기관 가중합 | 기존 가공 |
| `lunch_bias` | 점심 매출 비중 | 기존 가공 |
| `weekend_bias` | 주말 매출 비중 | 기존 가공 |
| `age_entropy` | 연령 매출분포 엔트로피 | 기존 가공 |
| `peer_avg_sales` | 동일분기 업종 평균 매출 | 기존 가공 |
| `closure_density` | 폐업률 | 기존 가공 |
| `covid_phase` | 코로나 시기 0/1/2 | 기존 가공 |
| `competitor_density` | 점포수÷유동인구 | 기존 가공 |
| `season_amplitude` | 계절 변동성 | 기존 가공 |
| `rent_index_q` | 임대료지수 | 신규 외부 (team1) |
| `rent_growth_yoy` | 임대료 yoy | 신규 외부 (team1) |
| `vacancy_rate` | 공실률 | 신규 외부 (team1) |
| `margin_proxy` | 매출의 1% 재척도화(avg_sales×0.01) — 독립 마진 데이터 아님, 매출과 corr 0.998, 타깃 누설 위험 | 누설 위험 (team1) |
| `transaction_count_q` | 실거래 건수 | 신규 외부 (team1) |
| `avg_transaction_price` | 실거래 평균가 | 신규 외부 (team1) |
| `gu_pop_density` | 자치구 인구밀도 | 신규 외부 (team4) |
| `closure_count` | 연 폐업점포수 | 신규 외부 (team4) |
| `single_household` | 1인가구 — 전부 결측(원본 CSV이 2010년만 → 2020~2025와 조인 안 됨), 사용 불가 | 사용 불가 (team4) |
| `gu_foreign_ratio` | 외국인 비율 | 신규 파생 |
| `rent_to_sales` | 임대료지수÷log매출 | 신규 파생 |
| `vacancy_change_q` | 공실률 분기 변화 | 신규 파생 |
| `pop_density_log` | log(인구밀도) | 신규 파생 |
| `single_ratio` | 1인가구÷인구 | 신규 파생 |
| `closure_per_store` | 폐업÷점포 | 신규 파생 |
| `margin_zscore` | 마진 z-score (margin_proxy 기반 → 매출 누설 위험 동일) | 누설 위험 |

## 데이터 정직성 주의

- **변수 개수**: 117개 (운영용, operational) / 112개 (엄격 기준, strict — 누설 변수 제거).
- **`margin_proxy`**: 독립적인 외부 마진 데이터가 아니라 `avg_sales × 0.01`(매출의 단순 1% 재척도화)이다. 매출과 corr 0.998이므로 사실상 타깃 자체이며 **타깃 누설(target leakage) 위험**이 있다. `margin_zscore`도 이를 표준화한 것이라 동일 위험.
- **`single_household`**: 원본 CSV가 2010년 자료만 있어 2020~2025 데이터와 조인되지 않아 **전부 결측(100% NaN)** 이며 **사용 불가**.