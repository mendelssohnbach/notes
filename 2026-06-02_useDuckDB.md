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
