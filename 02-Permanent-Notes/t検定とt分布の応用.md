# t分布とt検定の実務応用――CI実行時間の例を中心に

## 1. 定義・概要

t分布は、母分散が未知でサンプルサイズが小さい場合に、標本平均の分布を記述するために使われる確率分布である。特に、サンプル数が30未満の小標本で、母集団が正規分布に従うと仮定できる場合に用いられる。2つのグループの平均値の差が統計的に有意かどうかを検定する際、t分布に基づくt検定が広く利用される。

## 2. t検定の理論と手順

### 2.1 等分散の仮定とは何か

等分散の仮定（homoscedasticity）とは、「2つのグループ（または複数のグループ）の母集団の分散が等しい（同じ大きさである）」という前提のことを指す。t検定では、2つのグループの平均値の差を検定する際に「両グループのデータのばらつき（分散）が同じである」と仮定する場合があり、これが等分散の仮定である。

例えば、v1.0とv1.1のCI実行時間の分布が、どちらも平均は異なるかもしれないが、ばらつきの大きさ（分散）は同じだとみなす、という前提である。この仮定が成り立つ場合は「等分散型t検定（Studentのt検定）」を使い、成り立たない場合は「ウェルチのt検定（等分散を仮定しない）」を使う。

実務では、グループごとに分散が異なることも多いため、等分散かどうかは事前にLevene検定やF検定などで確認することが推奨される。等分散の仮定が成り立たない場合に等分散型t検定を使うと、誤った結論に至るリスクがある。

要約すると、「等分散の仮定」とは「比較するグループの分散が等しい」という前提であり、t検定の種類や計算方法の選択に大きく関わる重要な統計的前提である。

### 2.2 2群の平均値差のt検定の手順

1. **帰無仮説と対立仮説の設定**
   - 帰無仮説（$H_0$）：2つのグループの母平均は等しい（差はない）
   - 対立仮説（$H_1$）：2つのグループの母平均は等しくない（差がある）
2. **t値の計算**
   - 各グループの平均値、標準偏差、サンプル数からt値を計算する。
   - 等分散仮定下では、プールされた標準偏差を用いる。
   - 等分散が仮定できない場合はウェルチのt検定を用いる。
3. **自由度の決定**
   - 等分散仮定下では $n_1 + n_2 - 2$。
4. **p値の算出と有意性判断**
   - t分布表や統計ソフトでp値を求め、有意水準と比較して帰無仮説を棄却するか判断する。

## 3. 実務応用例：CI実行時間のバージョン比較

ソフトウェアのv1.0とv1.1のCI実行時間に有意な差があるかを検定する場合も、上記のt検定手順をそのまま適用できる。各バージョンで複数回の実行時間を測定し、2群の平均値差のt検定を行うことで、バージョン間の性能差が統計的に有意かどうかを評価できる。

## 4. Pythonによるt検定の実装例

### 4.1 scipyを用いた実装

```python
import numpy as np
from scipy import stats

# v1.0とv1.1のCI実行時間データ（例）
v1_times = [120, 122, 119, 121, 123]
v1_1_times = [115, 117, 116, 114, 118]

t_stat, p_value = stats.ttest_ind(v1_times, v1_1_times, equal_var=False)
print(f"t値: {t_stat:.3f}")
print(f"p値: {p_value:.3f}")
```

### 4.2 statsmodelsを用いた実装

```python
import numpy as np
from statsmodels.stats.weightstats import ttest_ind

# v1.0とv1.1のCI実行時間データ（例）
v1_times = [120, 122, 119, 121, 123]
v1_1_times = [115, 117, 116, 114, 118]

t_stat, p_value, df = ttest_ind(v1_times, v1_1_times, usevar='unequal')
print(f't値: {t_stat:.3f}')
print(f'p値: {p_value:.3f}')
print(f'自由度: {df:.1f}')
```

- `usevar='unequal'` でウェルチのt検定（等分散を仮定しない）を実施できる。
- scipyと同様にp値で有意差を判定できる。

## 5. 批判的視点・反論

t分布やt検定の利用にはいくつかの注意点がある。
- 母集団分布の正規性や等分散性の仮定が成り立たない場合、t検定の結果は信頼できない。
- サンプルサイズが極端に小さい場合、t検定自体が不安定になる。
- CI実行時間のようなデータは外部要因によるばらつきや外れ値の影響を受けやすいため、分布の可視化やノンパラメトリック検定の検討も重要である。
- 確証バイアスを避けるため、他の統計手法やロバスト推定との比較・併用が推奨される。

## 6. 関連ノート・リンク
- [[t分布とは何か_その実用上の使い方]]
- [[信頼区間・正規分布仮定の理論と実務]]
- [[確証バイアス.md]]

#statistics #t検定 #CI #ソフトウェア #性能評価


[//begin]: # "Autogenerated link references for markdown compatibility"
[t分布とは何か_その実用上の使い方]: t%E5%88%86%E5%B8%83%E3%81%A8%E3%81%AF%E4%BD%95%E3%81%8B_%E3%81%9D%E3%81%AE%E5%AE%9F%E7%94%A8%E4%B8%8A%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9.md "t分布とは何か、その実用上の使い方"
[確証バイアス.md]: ../%E7%A2%BA%E8%A8%BC%E3%83%90%E3%82%A4%E3%82%A2%E3%82%B9.md "確証バイアス"
[//end]: # "Autogenerated link references"
