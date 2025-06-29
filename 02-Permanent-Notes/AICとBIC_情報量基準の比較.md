# AIC（赤池情報量規準）とBIC（ベイズ情報量規準）

## 概要
AIC（Akaike Information Criterion, 赤池情報量規準）とBIC（Bayesian Information Criterion, ベイズ情報量規準）は、統計モデルや時系列モデルの「良さ」を比較するための情報量基準である。どちらもモデルの当てはまりの良さと複雑さ（パラメータ数）のバランスを評価し、異なるモデル間で客観的に比較できる指標として広く用いられる。

## AICの定義と意味
- 数式：AIC = 2k − 2ln(L)
  - k：モデルのパラメータ数
  - L：モデルの最大化対数尤度（データへの当てはまりの良さ）
- AICが小さいほど「良いモデル」とみなされる（絶対値ではなく相対比較に使う）。
- パラメータ数が多いほど当てはまりは良くなるが、AICは複雑さにペナルティを課すことで過学習を防ぐ。

## BICの定義と意味
- 数式：BIC = k × ln(n) − 2ln(L)
  - n：サンプルサイズ
- BICはAICよりもパラメータ数へのペナルティが強く、特にサンプルサイズが大きい場合にシンプルなモデルを選びやすい。

## AICとBICの比較
| 指標 | ペナルティ | 推奨される場面 | 特徴 |
|------|------------|----------------|------|
| AIC  | 2k         | 予測重視      | やや複雑なモデルを選びやすい、過学習しにくい |
| BIC  | k×ln(n)    | 真のモデル構造推定重視 | サンプル数が多いとシンプルなモデルを選びやすい |

- **AIC**は「予測精度」を重視し、実務ではモデルの汎用性を重視する場合に使われる。
- **BIC**は「真のモデル構造の推定」を重視し、理論的なモデル選択やサンプル数が多い場合に有効。
- 両者で選ばれるモデルが異なることも多く、目的に応じて使い分けるのが望ましい。

## 反証的視点・注意点
- AIC/BICは「同じデータセット・同じ目的変数」に対してのみ有効。
- サンプルサイズが小さい場合はAICc（修正AIC）を使う方が望ましい。
- BICは「真のモデルが候補の中に含まれている」ことを仮定しているが、実務ではこの仮定が成り立たないことも多い。
- どちらも「予測精度」そのものを保証するものではなく、あくまでモデル選択の一助。

## 関連・リンク推奨
- [SARIMAによる時系列異常検知の基礎](SARIMAによる時系列異常検知の基礎)
- [定常性とは何か_その判定方法](定常性とは何か_その判定方法)

## タグ
#statistics/timeseries #モデル選択 #aic #bic #情報量基準 #statistics/models
