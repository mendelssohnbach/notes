---
tags:
  - DB
---

# DuckDB入門

- [DuckDB in Action](https://github.com/duckdb-in-action/examples)
- [examples repository](https://github.com/duckdb-in-action/examples)

## CLI

[リリースページ](https://github.com/duckdb/duckdb/releases) より最新版をインストールする。

```terminal
$ wget https://github.com/duckdb/duckdb/releases/download/v1.5.3/duckdb_cli-linux-amd64.zip
$ unzip duckdb_cli-linux-amd64.zip
$ ./duckdb --version
v1.5.3 (Variegata) 14eca11bd9

# パスの通ったディレクトリに移動
$ mv duckdb /home/jack/.local/bin/
```

## CLIを使う

```terminal
$ ./duckdb
DuckDB v1.5.3 (Variegata)
Enter ".help" for usage hints.
```

`.quit` または `.exit` で終了する。

### SQL文

プロンプト `memory D` に続いて入力する。

```sql
memory D SELECT v.\* FROM VALUES (1), (3), (3), (7) AS v;
┌───────┐
│ col0  │
│ int32 │
├───────┤
│     1 │
│     3 │
│     3 │
│     7 │
└───────┘
```

### ドットコマンド

- CLIだけで使える
- 末尾に `;` が不要

### 引数

構文
: `./duckdb <options> FINE_NAME <command name>`

```terminal
-- クエリ結果をJSON形式で出力
$ ./duckdb -json -c 'SELECT v.* FROM VALUES (1), (3), (3), (7) AS v;'
[{"col0":1},
{"col0":3},
{"col0":3},
{"col0":7}]
```

## 拡張システム

利用可能なすべての拡張機能のリストを表示する。

```sql
memory D DESCRIBE
         SELECT *
         FROM duckdb_extensions();
```

```sql
-- インストール済み拡張機能の一覧
memory D SELECT extension_name, loaded, installed
         FROM duckdb_extensions()
         ORDER BY installed DESC, loaded DESC;
```

`INSTALL` : 拡張機能のインストール
`LOAD` : 拡張機能の読み込み

`LOAD` は自動的に行われるように変更された？

インストール上のファイルに対してクエリ実行できるようにする。

```sql
-- インストールとロード
memory D INSTALL httpfs;
memory D LOAD httpfs;
-- 確認
memory D SELECT extension_name, loaded, installed
         FROM duckdb_extensions()
         ORDER BY installed DESC, loaded DESC;
┌──────────────────┬─────────┬───────────┐
│  extension_name  │ loaded  │ installed │
│     varchar      │ boolean │  boolean  │
├──────────────────┼─────────┼───────────┤
│ httpfs           │ true    │ true      │

```

拡張機能のインストールパスを調べる

```sql
-- loaded/installed ともに true かつ path が表示される
memory D FROM duckdb_extensions()
         SELECT loaded, installed, install_path
         WHERE extension_name  ='httpfs';
┌─────────┬───────────┬────────────────────────────────────────────────────────┐
│ loaded  │ installed │                      install_path                      │
│ boolean │  boolean  │                        varchar                         │
├─────────┼───────────┼────────────────────────────────────────────────────────┤
│ true    │ true      │ /home/jack/.duckdb/extensions/v1.5.3/linux_amd64/httpf │
│         │           │ s.duckdb_extension                                     │
└─────────┴───────────┴────────────────────────────────────────────────────────┘
```

## CSVファイル

- 拡張子が `.csv` であること
- URLは途中で改行してはいけない
- URLは `'` シングルクォーテーションまたは `"` ダブルクォーテーションで囲むこと

[データセット](htt
ps://github.com/bnokoro/Data-Science/blob/master/countries%20of%20the%20world.csv)

```sql
-- データソースは途中で改行してはだめ
memory D SELECT count(*)
         FROM 'https://github.com/bnokoro/Data-Science/blob/master/countries%20of%20the%20world.csv';
┌──────────────┐
│ count_star() │
│    int64     │
├──────────────┤
│         1464 │
└──────────────┘
```

`read_csv_auto` : URLを short url に変換し拡張子が `.csv` でない場合

```sql
emory D SELECT count(*)
         FROM read_csv_auto("https://x.gd/Wi9hs");
memory D SELECT count(*)
         FROM read_csv_auto('https://x.gd/Wi9hs');
```

### 結果モード

結果の表示は、複数のモードから選択できる。

構文
: `.help mode`

利用可能なモード一覧

```sql
$ emory D .help mode
.mode MODE ?TABLE?    Set output mode
  MODE is one of:
    ascii             Columns/rows delimited by 0x1F and 0x1E
    box               Tables using unicode box-drawing characters
    csv               Comma-separated values
    column            Output in columns. (See .width)
    ...
```

列数の多いファイルには **行ベース** をモードを用いる。

```sql
memory D .mode line -- lineモードに切り替え
memory D SELECT *
         FROM read_csv_auto('https://bit.ly/3KoiZR0')
         LIMIT 1;
                           Country = Afghanistan
                            Region = ASIA (EX. NEAR EAST)
                        Population = 31056997
                    Area (sq. mi.) = 647500
...

memory D .mode json
memory D SELECT *
         FROM read_csv_auto('https://bit.ly/3KoiZR0')
         LIMIT 1;
[{"Country":"Afghanistan ","Region":"ASIA (EX. NEAR EAST) ...}]

-- ドッドコマンドとコメントを同一行に書くとがエラーになる
memory D .mode line -- mode change
Invalid Command Error:
Invalid usage of command '.mode'

Usage: '.mode MODE ?TABLE?'

-- コメントとドットコマンドは改行すること
memory D /* line mode を切り替え */
memory D .mode line
memory D SELECT *
         FROM read_csv_auto('https://bit.ly/3KoiZR0')
         LIMIT 1;
```

```sql
memory D
memory D -- 国の数、最大人口、平均面積を求める
memory D .mode duckbox
memory D SELECT count(*) AS countries,
         max(Population) AS max_population,
         round(avg(cast("Area (sq. mi.)" AS decimal))) As avgArea
         FROM read_csv_auto("https://bit.ly/3KoiZR0");
┌───────────┬────────────────┬──────────┐
│ countries │ max_population │ avgArea  │
│   int64   │     int64      │  double  │
├───────────┼────────────────┼──────────┤
│       227 │     1313973713 │ 598227.0 │
```

csv形式での保存

バックスラッシュが認識できないので1行入力する。

```terminal
# 西ヨーロッパ諸国の人口、出生率、死亡率を問い合わせ、csvファイルに保存
$ ./duckdb -csv -s "SELECT Country, Population, Birthrate, Deathrate FROM read_csv_auto('https://bit.ly/3KoiZR0') WHERE trim(region) = 'WESTERN EUROPE'" > western_europe.csv

# 確認
$ wc -l western_europe.csv
29 western_europe.csv
$ head -n 3 western_europe.csv
Country,Population,Birthrate,Deathrate
Andorra ,71201,"8,71","6,25"
Austria ,8192880,"8,74","9,76"
```

parquet形式での保存の場合は、 `COPY ... TO ...` 句を使う。

```terminal
$ ./duckdb -s "COPY (    SELECT Country, Population, Birthrate, Deathrate FROM read_csv_auto('https://bit.ly/3KoiZR0') WHERE trim(region) = 'WESTERN EUROPE'
    ) TO 'western_europe.parquet' (FORMAT PARQUET)"

# 確認
$ file western_europe.parquet
western_europe.parquet: Apache Parquet
./duckdb -s "FROM 'western_europe.parquet' LIMIT 5"
```

# SQLクエリの実行

エネルギー消費スキーマ

readingsテーブル

| カラム名  | データ型     | 属性/キー |
| :-------- | :----------- | :-------- |
| system_id | INTEGER      | PK, FK    |
| read_on   | TIMESTAMP    | PK        |
| power     | DECIMAL(8,3) |           |

systemsテーブル

| カラム名 | データ型 | 属性/キー |
| :------- | :------- | :-------- |
| id       | INTEGER  | PK        |
| name     | VARCHAR  |           |

pricesテーブル

| カラム名    | データ型     | 属性/キー |
| :---------- | :----------- | :-------- |
| id          | INTEGER      | PK        |
| value       | DECIMAL(5,2) |           |
| valid_from  | DATE         |           |
| valid_until | DATE         |           |

## データ定義言語クエリ

- データセットを取り込む前にデータ定義言語クエリ(DDL)を使ってスキーマを定義する
- `CREATE TABLE` 文で新しいテーブルを作成する
- `ALTER TABLE` 文で既存のテーブルを変更する

### CREATE TABLE文

```sql
-- systems テーブルを作成
-- V01__Create_table_systems.sql
CREATE TABLE IF NOT EXISTS systems (
    id          INTEGER PRIMARY KEY,
    name        VARCHAR(128) NOT NULL
);
```

```sql
-- readings テーブルを作成
-- V02__Create_table_readings.sql
CREATE TABLE IF NOT EXISTS readings (
    system_id   INTEGER NOT NULL,
    read_on     TIMESTAMP NOT NULL,
    power       DECIMAL(10,3) NOT NULL
            DEFAULT 0 CHECK(power >= 0), -- デフォルト値: 0 CHECKにて0以上
    PRIMARY KEY (system_id, read_on),
    FOREIGN KEY (system_id)
            REFERENCES systems(id)
);
```

```sql
-- readings テーブルを作成
-- V03__Create_table_prices.sql
CREATE SEQUENCE IF NOT EXISTS prices_id
    INCREMENT BY 1 MINVALUE 10; -- 開始値: 10 増分値: 1

CREATE TABLE IF NOT EXISTS prices (
    id          INTEGER PRIMARY KEY
                    DEFAULT(nextval('prices_id')),
    value       DECIMAL(5,2) NOT NULL,
    valid_from  DATE NOT NULL,
    CONSTRAINT prices_uk UNIQUE (valid_from)
);
```

### ALTER TABLE文

`ALTER TABLE` により変化するスキーマ要件に合わせて、データ型の変更や列の削除、リネームを行う。

```sql
ALTER TABLE prices
ADD COLUMN IF NOT EXISTS valid_until DATE;
```

`CREATE TABLE ... AS` テーブルを複製して、テーブルを作成する。 `LIMIT 0` とするとデータを含めずにテーブルを作成する。

```sql
CREATE TABLE prices_duplicate AS
SELECT * FROM prices;
```

### CREATE VIEW文

`CREATE VIEW` クエリのビューを定義する。

```sql
-- システムと日ごとの発電量を表示するビュー
CREATE OR REPLACE VIEW v_power_per_day AS
SELECT system_id,
    date_trunc('day', read_on) AS day,
    round(sum(power) / 4 / 1000, 2) AS kWh,
FROM readings
GROUP BY system_id, day;
```

### DESCRIBE文

`DESCRIBE` スキーマに対してクエリを実行する。

```sql
memory D DESCRIBE readings;
┌────────────────────────────────────────┐
│                readings                │
│                                        │
│ system_id integer   not null           │
│ read_on   timestamp not null           │
│ power     decimal   not null default 0 │
└────────────────────────────────────────┘
```

```sql
-- 特定の列のサブセットに対してDESCRIBE実行
memory D DESCRIBE SELECT read_on, power FROM readings;
┌──────────────────────────────────────┐
│               readings               │
│                                      │
│ read_on timestamp not null           │
│ power   decimal   not null default 0 │
└──────────────────────────────────────┘
```

## データ操作言語クエリ

データ操作言語クエリ(DML)は、データの読み取り、データの挿入、削除、変更を行う。

### INSERT文

`INSERT` データを作成する。

```sql
-- 主キー制約で 1 は一度しか使えない。
-- 列の順序に依存していいる
INSERT INTO prices
VALUES (1, 11.59, '2018-12-01', '2019-01-01');

-- 重複した主キー値を指定
memory D INSERT INTO prices
         VALUES (1, 11.59, '2018-12-01', '2019-01-01');
Constraint Error:
Duplicate key "id: 1" violates primary key constraint.
```

`ON CONFLICT` 非標準SQL句、競合を回避する。

`DO NOTHING` 対象列をデフォルトで主キーを対象にする。

```sql
-- エラーなく実行可能
INSERT INTO prices
VALUES (1, 11.59, '2018-12-01', '2019-01-01')
ON CONFLICT DO NOTHING;

-- 重複データの登録は回避されている
memory D SELECT * FROM prices;
┌───────┬──────────────┬────────────┬─────────────┐
│  id   │    value     │ valid_from │ valid_until │
│ int32 │ decimal(5,2) │    date    │    date     │
├───────┼──────────────┼────────────┼─────────────┤
│     1 │        11.59 │ 2018-12-01 │ 2019-01-01  │
└───────┴──────────────┴────────────┴─────────────┘
```

```sql
-- 主キーの生成をシーケンス関数にまかせる
-- 列の順番を宣言する
INSERT INTO prices(value, valid_from, valid_until)
VALUES (11.47, '2019-01-01', '2019-02-01'),
       (11.35, '2019-02-01', '2019-03-01'),
       (11.23, '2019-03-01', '2019-04-01'),
       (11.11, '2019-04-01', '2019-05-01'),
       (10.95, '2019-05-01', '2019-06-01');
```

```sql
-- 新しい価格で古い価格を置き換える
INSERT INTO prices(value, valid_from, valid_until)
VALUES (11.47, '2019-01-01', '2019-02-01')
-- 主キー制約とユニークキー制約どちらを使うか指定が必要
ON CONFLICT (valid_from)
DO UPDATE SET value = excluded.value;

-- 確認
memory D SELECT * FROM prices WHERE valid_from = '2019-01-01';
┌───────┬──────────────┬────────────┬─────────────┐
│  id   │    value     │ valid_from │ valid_until │
│ int32 │ decimal(5,2) │    date    │    date     │
├───────┼──────────────┼────────────┼─────────────┤
│    10 │        11.40 │ 2019-01-01 │ 2019-02-01  │
└───────┴──────────────┴────────────┴─────────────┘
```

```sql
-- データ挿入前
memory D SELECT count(id) FROM prices;
┌───────────┐
│ count(id) │
│   int64   │
├───────────┤
│         6 │
└───────────┘

-- データ挿入
INSERT INTO prices(value, valid_from, valid_until)
SELECT * FROM 'prices.csv' src;

-- データ挿入前
memory D SELECT count(id) FROM prices;
┌───────────┐
│ count(id) │
│   int64   │
├───────────┤
│        25 │
└───────────┘
```

```sql
-- Amazon S3 のリモートデータの概略を調べる
memory D DESCRIBE SELECT * FROM
         'https://oedi-data-lake.s3.amazonaws.com/pvdaq/csv/systems.csv';
┌───────────────────────────────────────────────────────────────┐
│ https://oedi-data-lake.s3.amazonaws.com/pvdaq/csv/systems.csv │
│                                                               │
│ system_id                                             bigint  │
│ system_public_name                                    varchar │
│ site_id                                               bigint  │
│ site_public_name                                      varchar │
│ site_location                                         varchar │
│ site_latitude                                         double  │
│ site_longitude                                        double  │
│ site_elevation                                        double  │
└───────────────────────────────────────────────────────────────┘
```

```sql
-- テーブルから一意な行の集合を挿入
INSERT INTO systems(id, name)
SELECT DISTINCT system_id, system_public_name
FROM 'https://oedi-data-lake.s3.amazonaws.com/pvdaq/csv/systems.csv'
ORDER BY system_id ASC;
```

```sql
-- サイトが閉鎖された
INSERT INTO readings (system_id, read_on, power)
SELECT SiteId, "Date-Time",
    CASE
        WHEN ac_power < 0 OR ac_power IS NULL THEN 0
        ELSE ac_power END
FROM read_csv_auto( -- 以下のサイトは存在しない
  'https://developer.nrel.gov/api/pvdaq/v3/data_file?' ||
  'api_key=DEMO_KEY&system_id=34&year=2019'
);
```

```sql
-- 正誤情報より
-- 測定値群のダウンロードと取り込み
INSERT INTO readings(system_id, read_on, power)
SELECT system_id, measured_on,
    CASE
        WHEN ac_power_hw__2695 < 0 OR ac_power_hw__2695 IS NULL THEN 0
        ELSE ac_power_hw__2695 END AS ac_power
FROM read_csv_auto(
  's3://oedi-data-lake/pvdaq/csv/pvdata/system_id=34/year=2019/*/*/*.csv',
  union_by_name=True);

-- 確認
memory D SELECT count(system_id) FROM readings;
┌──────────────────┐
│ count(system_id) │
│      int64       │
├──────────────────┤
│            46041 │
└──────────────────┘
```

```sql
-- システム34の2019年のサンプルを10件
memory D SELECT * FROM readings WHERE date_trunc('day', read_on) = '2019-08-26' AND power <> 0 LIMIT 10;
┌───────────┬─────────────────────┬───────────────┐
│ system_id │       read_on       │     power     │
│   int32   │      timestamp      │ decimal(10,3) │
├───────────┼─────────────────────┼───────────────┤
│        34 │ 2019-08-26 06:30:00 │      1700.000 │
│        34 │ 2019-08-26 06:45:00 │      3900.000 │
│        34 │ 2019-08-26 07:00:00 │      8300.000 │
│        34 │ 2019-08-26 07:15:00 │     14100.000 │
│        34 │ 2019-08-26 07:30:00 │     18400.000 │
│        34 │ 2019-08-26 07:45:00 │     24900.000 │
│        34 │ 2019-08-26 08:00:00 │     32400.000 │
│        34 │ 2019-08-26 08:15:00 │     40900.000 │
│        34 │ 2019-08-26 08:30:00 │     47500.000 │
│        34 │ 2019-08-26 08:45:00 │     48600.000 │
└───────────┴─────────────────────┴───────────────┘
```

```sql
-- 日毎の電力量の合計
SELECT * FROM v_power_per_day WHERE day = '2019-08-26';
┌───────────┬─────────────────────┬────────┐
│ system_id │         day         │  kWh   │
│   int32   │      timestamp      │ double │
├───────────┼─────────────────────┼────────┤
│        34 │ 2019-08-26 00:00:00 │  716.9 │
└───────────┴─────────────────────┴────────┘
```

### データのマージ

`ON CONFLICT DO UPDATE` 新しいデータを既存のデータにマージする。

```sql
INSERT INTO readings(system_id, read_on, power)
    VALUES (10, '2023-06-05 13:00:00', 4000);
-- 確認
memory D SELECT * FROM readings WHERE system_id = 10;
┌───────────┬─────────────────────┬───────────────┐
│ system_id │       read_on       │     power     │
│   int32   │      timestamp      │ decimal(10,3) │
├───────────┼─────────────────────┼───────────────┤
│        10 │ 2023-06-05 13:00:00 │      4000.000 │
└───────────┴─────────────────────┴───────────────┘

-- 主キー重複した場合は、新しい値に更新する
-- system_idが重複
INSERT INTO readings(system_id, read_on, power)
    VALUES(10, '2023-06-05 13:00:00', 3000)
ON CONFLICT(system_id, read_on) DO UPDATE -- アクション指定
SET power = CASE
    WHEN power = 0 THEN excluded.power  -- 元の列はエイリアス excludedとして参照
    ELSE (power + excluded.power) / 2 END;
-- 確認 power列が書き換えられた
memory D SELECT * FROM readings WHERE system_id = 10;
┌───────────┬─────────────────────┬───────────────┐
│ system_id │       read_on       │     power     │
│   int32   │      timestamp      │ decimal(10,3) │
├───────────┼─────────────────────┼───────────────┤
│        10 │ 2023-06-05 13:00:00 │      3500.000 │
└───────────┴─────────────────────┴───────────────┘
```

### DELETE文

```sql
-- 15分刻み以外のデータを削除
DELETE FROM readings
WHERE date_part('minute', read_on) NOT IN (0, 15, 30, 45);
```

### SELECT文

[DuckDB公式: SELECT](https://duckdb.org/docs/lts/sql/statements/select)

```sql
-- データセットに対して理解がふかまるまで LIMIT句を使う
SELECT *
FROM prices
LIMIT 2;
┌───────┬──────────────┬────────────┬─────────────┐
│  id   │    value     │ valid_from │ valid_until │
│ int32 │ decimal(5,2) │    date    │    date     │
├───────┼──────────────┼────────────┼─────────────┤
│     1 │        11.59 │ 2018-12-01 │ 2019-01-01  │
│    10 │        11.47 │ 2019-01-01 │ 2019-02-01  │
└───────┴──────────────┴────────────┴─────────────┘

-- DuckDB方言SQL
memory D FROM prices LIMIT 2;
┌───────┬──────────────┬────────────┬─────────────┐
│  id   │    value     │ valid_from │ valid_until │
│ int32 │ decimal(5,2) │    date    │    date     │
├───────┼──────────────┼────────────┼─────────────┤
│     1 │        11.59 │ 2018-12-01 │ 2019-01-01  │
│    10 │        11.47 │ 2019-01-01 │ 2019-02-01  │
└───────┴──────────────┴────────────┴─────────────┘
```

DuckDB方言SQLでは、'SELECT \*' の場合、 `FORM` から書き始めることができる。

**WHERE** 句

```sql
FROM prices
WHERE valid_from BETWEEN
  '2020-01-01' AND '2020-12-31';
```

**GROUP BY** 句

| 集約関数の例             | 機能                                             |
| :----------------------- | :----------------------------------------------- |
| `list`                   | リスト構造に集約する                             |
| `any_value`              | 非グループ化列から任意の値を選択する             |
| `first`                  | 順序付けられた結果から最初の値を選択する         |
| `last`                   | 順序付けられた結果から最後の値を選択する         |
| `arg_max`                | 最大行を探し、この行に対応する別の列値を選択する |
| `arg_min`                | 最小行を探し、この行に対応する別の列値を選択する |
| `bit_and/bit_or/bit_xor` | 集合に対してビット演算を行う                     |

[一般的な集計関数](https://duckdb.org/docs/current/sql/functions/aggregates#general-aggregate-functions)

```sql
-- 2019年と2020年の最高価格と最低価格を求める
SELECT date_part('year', valid_from) AS year, -- date_partでvalid_from列(年月日)から年を抽出
    min(value) AS minimum_price,
    max(value) AS maximum_price
FROM prices
WHERE year BETWEEN 2019 AND 2020
GROUP BY year -- 年で集約
ORDER BY year ASC;
┌───────┬───────────────┬───────────────┐
│ year  │ minimum_price │ maximum_price │
│ int64 │ decimal(5,2)  │ decimal(5,2)  │
├───────┼───────────────┼───────────────┤
│  2019 │          9.97 │         11.47 │
│  2020 │          8.60 │          9.87 │
└───────┴───────────────┴───────────────┘
```

**VALUE** 句

```sql
-- 3列1行を定義
memory D VALUES (1, 2, 3);
┌───────┬───────┬───────┐
│ col0  │ col1  │ col2  │
│ int32 │ int32 │ int32 │
├───────┼───────┼───────┤
│     1 │     2 │     3 │
└───────┴───────┴───────┘

-- 2列3行を定義
memory D VALUES (1, 2), (3, 4), (5, 6);
┌───────┬───────┐
│ col0  │ col1  │
│ int32 │ int32 │
├───────┼───────┤
│     1 │     2 │
│     3 │     4 │
│     5 │     6 │
└───────┴───────┘

-- 2列1行で複合値の定義
memory D VALUES ((1, 2), (3,3));
┌──────────────────────────┬──────────────────────────┐
│           col0           │           col1           │
│ struct(integer, integer) │ struct(integer, integer) │
├──────────────────────────┼──────────────────────────┤
│ (1, 2)                   │ (3, 3)                   │
└──────────────────────────┴──────────────────────────┘
```

`FROM` 句で `VALUES` 句を使う場合は、型指定と列名を指定できる。

```sql
-- 3つの列2行を指定
SELECT *
FROM (VALUES
  (1, 'Row 1', now()),
  (2, 'Row 2', now()),
) t (id, name, arbitrary_column_name);
┌───────┬─────────┬──────────────────────────────┐
│  id   │  name   │    arbitrary_column_name     │
│ int32 │ varchar │   timestamp with time zone   │
├───────┼─────────┼──────────────────────────────┤
│     1 │ Row 1   │ 2026-06-03 15:22:09.53231+09 │
│     2 │ Row 2   │ 2026-06-03 15:22:09.53231+09 │
└───────┴─────────┴──────────────────────────────┘
```

**JOIN** 句

`ON` と `USING` の違い

| 項目             | JOIN ... ON                          | JOIN ... USING                        |
| :--------------- | :----------------------------------- | :------------------------------------ |
| 列名の条件       | 異なる列名でも結合できる             | 全く同じ列名である必要がある          |
| 演算子           | >, <, LIKE なども使える              | =（等価結合）しか使えない             |
| 複数条件         | AND や OR で複雑に繋げられる         | 複数列の等価結合のみ指定できる        |
| SELECT \* 時の列 | 結合キーが両方のテーブル分出力される | 結合キーは1つにまとめられて出力される |

`INNER JOIN` と `FULL JOIN` の違い

| 項目                   | INNER JOIN（内部結合）                   | FULL JOIN（完全外部結合）                    |
| :--------------------- | :--------------------------------------- | :------------------------------------------- |
| 一致しないデータの扱い | 一致するレコードのみ出力する             | 一致しないレコードもすべて出力する           |
| 片方にしかないデータ   | 完全に除外される                         | 相手側の列は NULL として出力される           |
| 出力される行数         | 両方に存在するデータのみのため少なくなる | すべてのデータが含まれるため多くなる         |
| 主なユースケース       | 共通するデータだけを厳密に抽出したいとき | データの不一致や抜け漏れをチェックしたいとき |
