# AI 기반 지반함몰 위험도 예측 실습 지침서

## 1. 실습 목표

이번 실습의 목표는 도시 인프라 데이터를 이용하여 특정 공간 단위의 지반함몰 위험도를 예측하는 것입니다.

단순히 모델을 실행하는 것이 아니라, 다음 과정을 함께 경험하는 것을 목표로 합니다.

- 건설환경공학 데이터를 AI 문제로 정의하기
- 관로, 공동, 밀집도 데이터를 이용한 위험도 예측 모델 만들기
- 원본 변수와 파생 변수의 차이 이해하기
- 이상치와 데이터 품질 문제가 모델 성능에 미치는 영향 확인하기
- 모델 결과를 도메인 관점에서 해석하기

## 2. 데이터 파일

실습 데이터 파일명:

```text
한남대학교_AI부트캠프_지반함몰_실습DB.xlsx
```

데이터의 각 행은 하나의 공간 단위, 즉 하나의 `location index`를 의미합니다.

목표 변수는 `risk`입니다.

```text
risk = 지반함몰 위험도 등급
0 = Low
1 = Medium
2 = High
```

## 3. 주요 컬럼 설명

| 구분 | 컬럼 | 설명 |
|---|---|---|
| ID | `id` | 공간 단위 식별자 |
| 상수도관 | `water_Y_5` ~ `water_Y_55` | 시공 시기별 상수도관 길이 |
| 상수도관 밀집도 | `water_Density` | 해당 영역의 상수도관 밀집도 |
| 하수도관 | `sewer_Y_5` ~ `sewer_Y_20` | 시공 시기별 하수도관 길이 |
| 하수도관 밀집도 | `sewer_total_density_km_km2` | 해당 영역의 하수도관 밀집도 |
| 공동 | `cavity_count` | 해당 영역에서 발견된 공동 개수 |
| 위험도 | `risk` | 예측해야 하는 위험도 등급 |

## 4. 파생 변수 설명

데이터에는 다음 파생 변수가 포함되어 있습니다.

| 컬럼 | 의미 |
|---|---|
| `total_water_length` | 해당 영역의 상수도관 전체 길이 합 |
| `total_sewer_length` | 해당 영역의 하수도관 전체 길이 합 |
| `pipe_total_density` | 상수도관 밀집도 + 하수도관 밀집도 |
| `water_sewer_ratio` | 상수도관 밀집도 / 하수도관 밀집도 |
| `old_pipe_ratio` | 노후 관로 길이 / 전체 관로 길이 |
| `recent_pipe_ratio` | 최근 5년 관로 길이 / 전체 관로 길이 |
| `cavity_density_interaction` | 공동 개수 x 전체 관로 밀집도 |

이 변수들은 단순한 원본 데이터보다 지반함몰 위험을 더 잘 설명할 수 있는지 확인하기 위해 포함되었습니다.

## 5. 실습 진행 순서

### Step 1. 데이터 불러오기

```python
import pandas as pd

df = pd.read_excel("한남대학교_AI부트캠프_지반함몰_실습DB.xlsx")
df.head()
```

먼저 데이터 크기, 컬럼명, 결측치 여부, 기초 통계량을 확인합니다.

```python
df.shape
df.info()
df.describe()
df["risk"].value_counts().sort_index()
```

### Step 2. 기본 변수만 사용한 모델 만들기

먼저 가장 단순한 모델을 만들어 봅니다.

입력 변수:

```text
water_Density
sewer_total_density_km_km2
```

목표 변수:

```text
risk
```

예시 코드:

```python
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix
from xgboost import XGBClassifier

X = df[["water_Density", "sewer_total_density_km_km2"]]
y = df["risk"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.25, random_state=42, stratify=y
)

model = XGBClassifier(
    n_estimators=200,
    max_depth=3,
    learning_rate=0.05,
    eval_metric="mlogloss",
    random_state=42
)

model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print("Accuracy:", accuracy_score(y_test, y_pred))
print(classification_report(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))
```

생각해볼 질문:

- 밀집도 2개만으로 위험도를 충분히 예측할 수 있는가?
- 어떤 등급에서 오분류가 많이 발생하는가?

### Step 3. 원본 변수 전체 사용하기

이번에는 시공 시기별 관로 길이, 밀집도, 공동 개수를 모두 사용합니다.

```python
raw_features = [
    "water_Y_5", "water_Y_10", "water_Y_15", "water_Y_20", "water_Y_25",
    "water_Y_30", "water_Y_35", "water_Y_40", "water_Y_45", "water_Y_50", "water_Y_55",
    "water_Density",
    "sewer_Y_5", "sewer_Y_10", "sewer_Y_15", "sewer_Y_20",
    "sewer_total_density_km_km2",
    "cavity_count"
]

X = df[raw_features]
y = df["risk"]
```

위와 같은 방식으로 모델을 다시 학습하고 성능을 비교합니다.

생각해볼 질문:

- 입력 변수를 늘렸을 때 성능이 좋아졌는가?
- 성능이 좋아졌다면, 어떤 정보가 추가되었기 때문이라고 볼 수 있는가?

### Step 4. 파생 변수까지 포함하기

이번에는 도메인 지식을 반영한 파생 변수를 함께 사용합니다.

```python
engineered_features = raw_features + [
    "total_water_length",
    "total_sewer_length",
    "pipe_total_density",
    "water_sewer_ratio",
    "old_pipe_ratio",
    "recent_pipe_ratio",
    "cavity_density_interaction"
]

X = df[engineered_features]
y = df["risk"]
```

다시 모델을 학습하고 성능을 비교합니다.

비교할 항목:

```text
Model A: 밀집도 2개만 사용
Model B: 원본 변수 전체 사용
Model C: 원본 변수 + 파생 변수 사용
```

### Step 5. 이상치 탐색

실제 연구 데이터에는 단위 오류, 입력 오류, 물리적으로 불가능한 값이 포함될 수 있습니다.

다음 코드를 이용해 이상한 값을 찾아봅니다.

```python
df.describe().T
```

음수 값이 존재하는지 확인합니다.

```python
numeric_df = df.select_dtypes(include="number")
negative_counts = (numeric_df < 0).sum()
negative_counts[negative_counts > 0]
```

상자그림으로 분포를 확인합니다.

```python
import matplotlib.pyplot as plt

cols = [
    "water_Density",
    "sewer_total_density_km_km2",
    "pipe_total_density",
    "cavity_count",
    "cavity_density_interaction"
]

df[cols].plot(kind="box", subplots=True, layout=(1, len(cols)), figsize=(18, 4))
plt.tight_layout()
plt.show()
```

생각해볼 질문:

- 특정 컬럼에 비정상적으로 큰 값이 있는가?
- 음수 값이 물리적으로 가능한가?
- 단위가 섞인 것으로 의심되는 값이 있는가?

### Step 6. 이상치 처리 후 다시 모델링

간단한 이상치 처리 예시는 다음과 같습니다.

```python
clean_df = df.copy()

feature_cols = engineered_features

for col in feature_cols:
    if col != "water_sewer_ratio":
        clean_df.loc[clean_df[col] < 0, col] = pd.NA

    lower = clean_df[col].quantile(0.01)
    upper = clean_df[col].quantile(0.99)
    clean_df[col] = clean_df[col].clip(lower, upper)

clean_df[feature_cols] = clean_df[feature_cols].fillna(clean_df[feature_cols].median())
```

이후 `clean_df`를 이용하여 모델을 다시 학습합니다.

생각해볼 질문:

- 이상치 처리 후 성능이 개선되었는가?
- 정확도뿐 아니라 precision, recall, F1-score도 함께 개선되었는가?
- 무조건 이상치를 제거하는 것이 항상 좋은가?

### Step 7. 변수 중요도 해석

XGBoost 모델의 변수 중요도를 확인합니다.

```python
import pandas as pd

importance = pd.DataFrame({
    "feature": feature_cols,
    "importance": model.feature_importances_
}).sort_values("importance", ascending=False)

importance.head(15)
```

시각화:

```python
importance.head(15).sort_values("importance").plot(
    kind="barh", x="feature", y="importance", figsize=(8, 6)
)
plt.tight_layout()
plt.show()
```

해석할 때는 다음 관점에서 생각합니다.

- 공동 개수와 관로 밀집도가 함께 높은 지역은 왜 위험할 수 있는가?
- 노후 관로 비율이 높은 지역은 왜 위험도가 높게 나타날 수 있는가?
- 최근 관로 비율은 위험도를 낮추는 방향으로 작용할 수 있는가?
- 모델이 중요하다고 판단한 변수가 실제 지반공학적으로도 타당한가?

## 6. 최종 제출 또는 발표 내용

각 팀 또는 개인은 다음 내용을 정리합니다.

1. 사용한 입력 변수 조합
2. 모델 종류와 주요 설정
3. 정확도, F1-score, confusion matrix
4. 이상치 탐색 결과
5. 이상치 처리 전후 성능 비교
6. 주요 변수 중요도
7. 지반함몰 위험도 관점에서의 해석

## 7. 권장 결론 형식

예시는 다음과 같습니다.

```text
본 실습에서는 상수도관 및 하수도관 정보, 공동 개수, 관로 밀집도 기반 파생 변수를 이용하여 지반함몰 위험도를 예측하였다.

단순 밀집도 변수만 사용한 모델보다, 시공 시기별 관로 정보와 파생 변수를 함께 사용한 모델에서 더 높은 예측 성능을 보였다.

특히 cavity_density_interaction, old_pipe_ratio, pipe_total_density와 같은 변수는 위험도 예측에 중요한 영향을 미쳤다.

이는 지반함몰 위험이 단일 요인보다 관로 노후도, 관로 밀집도, 공동 발생이 복합적으로 작용하는 문제임을 시사한다.
```

## 8. 주의사항

이 데이터는 교육용 실습 데이터입니다.

실제 지반함몰 위험도 평가는 더 많은 현장 조사 자료, 지반 조건, 지하수위, 도로 조건, 강우 자료, 보수 이력, 공동 탐사 결과 등을 함께 고려해야 합니다.

이번 실습의 핵심은 실제 연구와 유사한 흐름으로 데이터 기반 위험도 예측 과정을 경험하는 것입니다.
