# PostgreSQL 複数列インデックス：設計と実用指針

## 概要

複数列インデックス（Multi-column Index、Composite Index）は、2つ以上のカラムを組み合わせて作成するインデックス方式であり、現代のデータベースアプリケーションにおいて検索性能の最適化に欠かせない技術です。PostgreSQLでは最大32カラムまでの複数列インデックスを作成可能ですが、実用的な設計には深い理解と慎重な計画が必要となります。本ノートでは、複数列インデックスの内部仕組み、カラム順序の重要性、実用上の設計指針について詳述し、データベース性能最適化の実践的知識を提供します。

## 複数列インデックスの基本原理

### 内部データ構造と辞書式順序

PostgreSQLの複数列B-treeインデックスは、複数キーでの辞書式順序（Lexicographic Order）によってデータを格納します。この構造は、電話帳や辞書の索引と類似した概念であり、第一キー、第二キー、第三キーの順で階層的にソートされます。

例えば、`(last_name, first_name, birth_date)`のインデックスでは、以下のような順序でデータが格納されます：

```
("Anderson", "Alice", "1990-01-01")
("Anderson", "Bob", "1988-12-10")
("Smith", "Alice", "1990-01-01")
("Smith", "Alice", "1995-03-15")
("Smith", "Bob", "1988-12-10")
("Wilson", "Charlie", "1992-07-20")
```

この構造により、左端のカラムから始まる部分的な検索条件でも効率的なバイナリサーチが可能となりますが、中間カラムのスキップや右端カラムのみでの検索では、インデックスの恩恵を十分に受けることができません。

### 左端プリフィックスの法則

複数列インデックスの最も重要な概念が「左端プリフィックスの法則」です。この法則により、インデックスが効率的に利用される検索パターンと、そうでないパターンが明確に分かれます。

**効率的に利用される検索パターン（インデックス: user_id, location_id, activity_date）：**

```sql
-- 全カラム指定：最も効率的なIndex Scan
SELECT * FROM user_activities 
WHERE user_id = 123 AND location_id = 456 AND activity_date = '2025-06-01';

-- 左端から連続指定：効率的なIndex Scan
SELECT * FROM user_activities 
WHERE user_id = 123 AND location_id = 456;

-- 左端カラムのみ：効率的なIndex Scan
SELECT * FROM user_activities 
WHERE user_id = 123;
```

**非効率な検索パターン：**

```sql
-- 中間カラムスキップ：user_idでのIndex Scan後、activity_dateの条件はFilter処理
SELECT * FROM user_activities 
WHERE user_id = 123 AND activity_date = '2025-06-01';

-- 右端カラムのみ：Full Table Scanまたは他のインデックス利用
SELECT * FROM user_activities 
WHERE location_id = 456;

-- 左端カラム未指定：インデックス未使用
SELECT * FROM user_activities 
WHERE location_id = 456 AND activity_date = '2025-06-01';
```

## カラム順序設計の実用的指針

### 選択性（Cardinality）による優先順位

複数列インデックスにおける最も基本的な設計原則は、選択性の高いカラム（ユニークに近い値を持つカラム）を左端に配置することです。選択性が高いほど、初期段階での絞り込み効果が大きくなり、後続の検索処理が効率化されます。

**選択性評価の実例：**

```sql
-- 各カラムの選択性を評価するクエリ
SELECT 
    'user_id' as column_name,
    COUNT(DISTINCT user_id)::float / COUNT(*) as selectivity,
    COUNT(DISTINCT user_id) as distinct_values
FROM user_activities
UNION ALL
SELECT 
    'location_id',
    COUNT(DISTINCT location_id)::float / COUNT(*),
    COUNT(DISTINCT location_id)
FROM user_activities;
```

一般的な選択性の目安として、0.9以上は高選択性、0.1以下は低選択性と判断できます。ただし、データ分布の偏りも考慮する必要があります。

### 検索頻度による優先順位

実際の業務アプリケーションでは、単独で検索されるカラムの頻度分析が重要です。PostgreSQLの`pg_stat_statements`拡張を活用して、実際のクエリパターンを分析し、使用頻度の高いカラムを左端に配置することで、より多くのクエリでインデックス効果を得られます。

**検索パターン分析の例：**

```sql
-- 頻出クエリパターンの分析
SELECT 
    query,
    calls,
    mean_exec_time,
    total_exec_time
FROM pg_stat_statements 
WHERE query LIKE '%user_activities%'
ORDER BY calls DESC;
```

### データ型とサイズ考慮

インデックスの物理的サイズとメモリ効率を考慮すると、以下の順序でカラムを配置することが推奨されます：

1. **固定長で小さなデータ型**（INTEGER、BIGINT、DATE）
2. **固定長で大きなデータ型**（NUMERIC、TIMESTAMP）
3. **可変長データ型**（VARCHAR、TEXT）
4. **バイナリデータ型**（BYTEA）

この順序により、インデックスページの効率的な利用とキャッシュ効果の向上が期待できます。

## 実用上の設計戦略

### カバーリングインデックス（Covering Index）の活用

カバーリングインデックスは、WHERE句の検索条件だけでなく、SELECT句で取得するカラムもインデックスに含める手法です。この設計により、PostgreSQLのIndex-Only Scanが実現され、テーブルへのアクセスを完全に回避できます。

```sql
-- カバーリングインデックスの例
CREATE INDEX idx_user_activities_covering 
ON user_activities (user_id, activity_date) 
INCLUDE (location_id, activity_type, duration);

-- Index-Only Scanが実行されるクエリ
SELECT location_id, activity_type, duration
FROM user_activities 
WHERE user_id = 123 AND activity_date = '2025-06-01';
```

カバーリングインデックスは検索性能を劇的に向上させますが、インデックスサイズの増大と更新性能への影響を慎重に評価する必要があります。

### 部分インデックスとの組み合わせ

特定の条件に絞った部分インデックス（Partial Index）と複数列インデックスを組み合わせることで、より効率的なインデックス設計が可能です。

```sql
-- アクティブユーザーのみを対象とした部分インデックス
CREATE INDEX idx_active_user_activities 
ON user_activities (user_id, activity_date, location_id)
WHERE is_active = true AND activity_date >= '2025-01-01';
```

部分インデックスは、インデックスサイズを大幅に削減し、特定の業務パターンに最適化された高速検索を実現します。

### インデックス統合による最適化

複数の単一カラムインデックスが存在する場合、これらを統合して複数列インデックスに変更することで、以下の効果が得られます：

1. **インデックス数の削減**：メンテナンス負荷の軽減
2. **更新性能の向上**：INSERT/UPDATE/DELETE時のインデックス更新コスト削減
3. **ストレージ効率の改善**：重複するインデックス構造の排除

ただし、統合により一部のクエリで性能劣化が発生する可能性があるため、事前の性能テストが不可欠です。

## 注意すべき実用上の問題

### 過度なインデックス作成による性能劣化

複数列インデックスは強力な最適化手法ですが、過度な作成は以下の深刻な問題を引き起こします：

**更新性能への影響**：INSERT、UPDATE、DELETE処理において、すべてのインデックスの更新が必要となるため、処理時間が線形に増加します。特に、バッチ処理や大量データ投入時には、インデックス更新がボトルネックとなる可能性があります。

**ストレージ消費量の急激な増加**：複数列インデックスは、含まれるカラム数とデータサイズに比例してストレージを消費します。特に、可変長文字列カラムを含む場合、インデックスサイズがテーブルサイズを上回ることもあります。

**統計情報の複雑化**：多数のインデックスが存在すると、PostgreSQLのクエリプランナーの判断が複雑化し、最適でない実行計画が選択されるリスクが増大します。

### PostgreSQL固有の制約事項

**インデックスサイズ制限**：PostgreSQLでは、単一インデックスエントリの最大サイズが約2700バイトに制限されています。長い文字列を含む複数列インデックスでは、この制限に抵触する可能性があります。

**TOAST化の影響**：大きなデータはTOASTテーブルに分離されるため、インデックス効率が大幅に低下します。特に、TEXT型やBYTEA型を含む複数列インデックスでは注意が必要です。

**式インデックスとの併用制約**：関数ベースインデックス（Expression Index）と複数列インデックスの組み合わせでは、予期しない性能特性を示す場合があります。

## モニタリングと継続的最適化

### インデックス使用状況の監視

インデックスの効果を定量的に評価するため、PostgreSQLの統計情報ビューを活用した継続的な監視が重要です。

```sql
-- インデックス使用統計の詳細確認
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan as scan_count,
    idx_tup_read as tuples_read,
    idx_tup_fetch as tuples_fetched,
    pg_size_pretty(pg_relation_size(indexrelid)) as index_size
FROM pg_stat_user_indexes 
ORDER BY idx_scan DESC;
```

**監視すべき指標：**
- `idx_scan`：インデックススキャンの実行回数
- `idx_tup_read`：インデックスから読み取られたタプル数
- `idx_tup_fetch`：実際に取得されたタプル数
- インデックスサイズ：ストレージ使用量

### 未使用インデックスの特定と削除

定期的に未使用インデックスを特定し、削除することで、データベースの健全性を維持できます。

```sql
-- 利用されていないインデックスの検出
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    pg_size_pretty(pg_relation_size(indexrelid)) as wasted_space
FROM pg_stat_user_indexes 
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

ただし、バッチ処理用のインデックスなど、統計期間外で使用されるインデックスもあるため、削除前には十分な調査が必要です。

### クエリプランの分析

`EXPLAIN (ANALYZE, BUFFERS)`コマンドを活用して、インデックスの実際の使用状況と効果を分析します。

```sql
-- インデックス使用状況の詳細分析
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
SELECT * FROM user_activities 
WHERE user_id = 123 AND activity_date = '2025-06-01';
```

分析ポイント：
- Index Scan vs Sequential Scanの選択
- Index-Only Scanの実現可否
- Buffer Hitsとキャッシュ効率
- 実際の実行時間と見積もり時間の比較

## 批判的視点・反証

### 複数列インデックスの過度な信頼への警告

データベース最適化において、複数列インデックスは強力な手法ですが、以下の批判的視点も考慮する必要があります：

**設計の複雑化リスク**：複数列インデックスの最適化に注力しすぎると、アプリケーション要件の変化に対する柔軟性が失われる可能性があります。特に、開発初期段階での過度な最適化は、後の仕様変更時に大きな制約となります。

**維持管理コストの増大**：複雑なインデックス設計は、新規開発者の理解負荷を増大させ、長期的な保守性を損なう可能性があります。シンプルなB-treeインデックスの組み合わせの方が、運用面で優位な場合もあります。

**ハードウェア進歩による効果の相対化**：近年のSSDの高速化やメモリ容量の増大により、インデックス最適化の効果が相対的に小さくなっている領域もあります。過度な最適化よりも、シンプルで理解しやすい設計を優先すべき場面があります。

### 代替手段との比較検討

複数列インデックスの導入前に、以下の代替アプローチとの比較検討が推奨されます：

**パーティショニング**：テーブル分割による検索範囲の物理的限定は、複数列インデックスよりも根本的な性能改善をもたらす場合があります。

**マテリアライズドビュー**：集計処理や複雑なJOINを事前計算することで、インデックス最適化以上の効果を得られる可能性があります。

**アプリケーション層での最適化**：データベース層での最適化よりも、アプリケーション層でのキャッシュやクエリ最適化の方が、開発効率と性能のバランスが良い場合があります。

## 関連ノート・リンク

- [[PostgreSQL_Hash_Index_原理とメリットデメリット]]
- [[B-tree Index最適化戦略]]
- [[PostgreSQL クエリプランナーとインデックス選択]]
- [[データベースパフォーマンスチューニング基礎]]
- [[PostgreSQL パーティショニング設計指針]]
- [[Index-Only Scan最適化手法]]
- [[PostgreSQL 統計情報とクエリ最適化]]

#database #postgresql #performance #database/index #design/database #database/optimization 
