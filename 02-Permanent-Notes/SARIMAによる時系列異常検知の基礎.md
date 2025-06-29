# SARIMAによる時系列異常検知の基礎

## 概要
SARIMA（季節調整付き自己回帰和分移動平均モデル）は、時系列データの中でも季節性（周期的なパターン）を持つデータの解析や予測に特化した統計モデルである。ARIMAモデルに季節成分を加えた拡張版であり、売上や気温、センサ値など周期性を持つ現象の分析に広く用いられる。

## モデル構造
SARIMAは、非季節成分（ARIMA）と季節成分（SARIMAのS部分）から構成される。
- **非季節成分（ARIMA）**：自己回帰（AR）、和分（I）、移動平均（MA）
- **季節成分**：季節自己回帰（SAR）、季節和分（SI）、季節移動平均（SMA）

パラメータはSARIMA(p, d, q)(P, D, Q)[s]で表される。
- p, d, q：非季節成分の次数
  - **d（和分次数）**：トレンドなど非定常性を除去するために何回差分を取るかを示す。d=1なら1階差分、d=2なら2階差分を意味する。
- P, D, Q：季節成分の次数
  - **D（季節和分次数）**：季節性の非定常性を除去するために季節周期ごとに何回差分を取るかを示す。D=1なら1周期ごとに1階差分を取る。
- s：季節周期（例：12なら年周期、7なら週周期）

## SARIMA適用の前提条件

### 1. データの定常性
- 定常性（stationarity）とは、時系列データの平均や分散、自己共分散が時間とともに変化しない性質を指す。
- SARIMAは「和分（I, D）」や「季節和分（SI, SD）」によって非定常なデータを定常化することを前提とする。
- トレンドや季節性が強い場合は、適切な次数で差分を取る必要がある。

### 2. 季節性の存在
- SARIMAは季節性（周期的なパターン）が明確に存在するデータに適している。
- 季節周期（s）が明確でない場合や、季節性が弱い場合は、通常のARIMAモデルの方が適切な場合もある。

### 3. 外れ値や欠損値の処理
- 外れ値や欠損値が多いと、モデルのパラメータ推定や予測精度に悪影響を及ぼす。
- 事前に外れ値の除去や補間などの前処理が推奨される。

### 4. パラメータの適切な選定
- p, d, q, P, D, Q, s の各パラメータは、自己相関・偏自己相関プロットやAIC/BICなどの情報量基準を用いて適切に選定する必要がある。
- 過学習や未学習を避けるため、モデル選択は慎重に行うべきである。

### 5. 残差の正規性・無相関性
- モデル適合後、残差が白色雑音（自己相関がなく、正規分布に従う）であることが理想。
- 残差に自己相関が残る場合は、モデルの再検討が必要。

---

## 異常検知への応用
SARIMAは、時系列データの通常パターンをモデル化し、予測値と実測値の乖離（残差）が大きい箇所を異常とみなす手法に活用される。
1. SARIMAで時系列データを学習・予測
2. 予測値と実測値の差（残差）を算出
3. 残差が統計的に有意に大きい箇所を異常と判定

### 利点
- 季節性やトレンドを考慮した異常検知が可能
- シンプルな統計モデルで解釈性が高い

---

## 反証的視点・注意点
- 非線形な構造や突発的な変化（例：異常値、構造変化）にはSARIMAは弱い。
- 複数の季節性や複雑な周期性が混在する場合、単一のSARIMAでは十分に表現できないことがある。
- データ量が極端に少ない場合、パラメータ推定が不安定になる。

## 関連・リンク推奨

## タグ
#statistics/timeseries #異常検知 #sarima #arima #statistics/models
