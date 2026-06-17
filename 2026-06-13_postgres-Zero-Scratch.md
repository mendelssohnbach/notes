---
tags:
  - DB
---

[ゼロからはじめるデータベース操作](https://www.shoeisha.co.jp/book/detail/9784798144450)
[正誤表](https://www.shoeisha.co.jp/book/detail/9784798144450)
[サンプル](https://www.shoeisha.co.jp/book/download/9784798144450)

# イントロダクション

ログイン

```terminal
$ psql -U postgres
```

ログインしているデータベースを知るには

- プロンプトを見る: 以下では `postgres` である
- `select current_database();` で調べる
- `\conninfo` で調べる
- `\l` で調べる

```psql
-- sqlを実行
postgres=# select current_database();
 current_database
------------------
 postgres

-- 接続情報で調べる
postgres-# \conninfo
            Connection Information
      Parameter       |         Value
----------------------+------------------------
 Database             | postgres
 Client User          | postgres
 Host                 | localhost
 Host Address         | 127.0.0.1
 Server Port          | 5432
 Options              |
 Protocol Version     | 3.0
 Password Used        | false
 GSSAPI Authenticated | false
 Backend PID          | 2769
 SSL Connection       | true
SSL Library          | OpenSSL
 SSL Protocol         | TLSv1.3
 SSL Key Bits         | 256
 SSL Cipher           | TLS_AES_256_GCM_SHA384
 SSL Compression      | false
 ALPN                 | postgresql
 Superuser            | on
 Hot Standby          | off
(19 rows)

-- データベースの一覧を表示
postgres-# \l
                                                 List of databases
   Name    |  Owner   | Encoding | Locale Provider | Collate |  Ctype  | Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+---------+---------+--------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           |
 shop      | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           |
 template0 | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           | =c/postgres          +
           |          |          |                 |         |         |        |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           | =c/postgres          +
           |          |          |                 |         |         |        |           | postgres=CTc/postgres
(4 rows)
```

学習用データベースの作成

```psql
=# create database shop;
-- 作成したら一旦終了する
=# \q
```

```terminal
-- 学習用データベースにログイン
$ psql -U postgres -d shop

-- プロンプトが shop と表示される
shop=#
```

# データベースとSQL

命令の3種類

| 略称 | 日本語名       | 役割                                   | 英語名                     |
| :--- | :------------- | :------------------------------------- | :------------------------- |
| DDL  | データ定義言語 | データベースやテーブルの作成/削除      | Data Definition Language   |
| DML  | データ操作言語 | テーブルの行の検索/変更                | Data Manipulation Language |
| DCL  | データ制御言語 | 変更の確定/取り消し/ユーザー権限の設定 | Data Control Language      |

**DDL**

| 命令   | 機能                             |
| :----- | :------------------------------- |
| CREATE | データベースやテーブルの作成     |
| DROP   | データベースやテーブルの削除     |
| ALTER  | データベースやテーブルの構成変更 |

**DML**

| 命令   | 機能     |
| :----- | :------- |
| SELECT | 行の検索 |
| INSERT | 行の登録 |
| UPDATE | 行の更新 |

**DCL**

| 命令     | 機能                                     |
| :------- | :--------------------------------------- |
| COMMIT   | データベースに対する変更の確定           |
| ROLLBACK | データベースに対して行った変更の取り消し |
| BRANT    | ユーザー操作の権限付与                   |
| REVOKE   | ユーザー操作の権限取り消し               |

## テーブルの作成

- `CREATE TABLE` で作成
- テーブル名/列名は使って良い文字は決まっている
- 列にはデータ型を指定する
  - 整数型/文字列型/日付型など
- テーブルには制約を設定できる
  - 主キー制約/NOT NULL制約

作成するターブルの内容

商品テーブル

| 商品ID | 商品名         | 商品分類     | 販売単価 | 仕入単価 | 登録日     |
| :----- | :------------- | :----------- | :------- | :------- | :--------- |
| 0001   | Tシャツ        | 衣服         | 1000     | 500      | 2009-09-20 |
| 0002   | 穴あけパンチ   | 事務用品     | 500      | 320      | 2009-09-11 |
| 0003   | カッターシャツ | 衣服         | 4000     | 2800     |            |
| 0004   | 包丁           | キッチン用品 | 3000     | 2800     | 2009-09-20 |
| 0005   | 圧力鍋         | キッチン用品 | 6800     | 5000     | 2009-01-15 |
| 0006   | フォーク       | キッチン用品 | 500      |          | 2009-09-20 |
| 0007   | おろしがね     | キッチン用品 | 880      | 790      | 2008-04-28 |
| 0008   | ボールペン     | 事務用品     | 100      |          | 2009-11-11 |

## データベースの作成

`CREATE DATABASE`
: データベースの作成

```psql
postgres=# CREATE DATABASE shop;
CREATE DATABASE
postgres=# \l
                                                 List of databases
   Name    |  Owner   | Encoding | Locale Provider | Collate |  Ctype  | Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+---------+---------+--------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           |
 shop      | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           |
 ...
```

## テーブルの作成

`CREATE TABLE`
: テーブルの作成

```sql
-- 構文
CREATE TABLE <テーブル名>
  (<列名1> <データ型> <列制約>,
  <列名2> <データ型> <列制約>,
  ...
  );
```

Shohin テーブルの作成

```sql
CREATE TABLE Shohin
(shohin_id     CHAR(4) NOT NULL,
 shohin_mei    VARCHAR(100) NOT NULL,
 shohin_bunrui VARCHAR(32) NOT NULL,
 hanbai_tanka  INTEGER ,
 shiire_tanka  INTEGER ,
 torokubi      DATE ,
     PRIMARY KEY (shohin_id));
CREATE TABLE
```

```psql
-- 確認
postgres=# \d Shohin;
                          Table "public.shohin"
    Column     |          Type          | Collation | Nullable | Default
---------------+------------------------+-----------+----------+---------
 shohin_id     | character(4)           |           | not null |
 shohin_mei    | character varying(100) |           | not null |
 shohin_bunrui | character varying(32)  |           | not null |
 hanbai_tanka  | integer                |           |          |
 shiire_tanka  | integer                |           |          |
 torokubi      | date                   |           |          |
Indexes:
    "shohin_pkey" PRIMARY KEY, btree (shohin_id)
```

### 命名ルール

データベース/テーブル/列の名前に使える文字

- 半角アルファベット
- 数字
- `_` (アンダーバー)
- 名前の1文字目は半角アルファベット

## データ型の指定

**CHAR**
: `CHAR(n)` 列に入れる最大文字長で固定型、最大長に達するまで半角スペースで埋められる

**VARCHAR**
: `VARCHAR(n)` 列に入れる最大文字長で可変長型

### 制約の設定

**NOT NULL** NULLセルを禁止

**PRIMARY KEY** 列を主キーとする

## テーブルの削除と変更

### テーブルの削除

`DROP TABLE`
: テーブルを削除

```sql
-- 構文
DROP TABLE <テーブル名>;
```

### テーブル定義の変更

`ALTER TABLE`
: テーブルの定義を変更する

```sql
-- 構文
ALTER TABLE <テーブル名> ADD COLUMN <列定義>;
-- 既存のテーブルに列を追加
ALTER TABLE <テーブル名> ADD COLUMN <追加列名> CHAR(8) NOT NULL;
-- 複数の列を追加する場合は {} が必要
ALTER TABLE <テーブル名> ADD COLUMN (
  <追加列名1> CHAR(8) NOT NULL,
  <追加列名2> VARCHAR(10)
);
```

```sql
-- shohin_mei_kana 列を追加
-- 可変長文字列 100桁
ALTER TABLE Shohin ADD COLUMN shohin_mei_kana VARCHAR(100);
```

```psql
-- 既存のテーブル定義
postgres=# \d Shohin;
                          Table "public.shohin"
    Column     |          Type          | Collation | Nullable | Default
---------------+------------------------+-----------+----------+---------
 shohin_id     | character(4)           |           | not null |
 shohin_mei    | character varying(100) |           | not null |
 shohin_bunrui | character varying(32)  |           | not null |
 hanbai_tanka  | integer                |           |          |
 shiire_tanka  | integer                |           |          |
 torokubi      | date                   |           |          |
Indexes:
    "shohin_pkey" PRIMARY KEY, btree (shohin_id)

-- テーブル定義変更
postgres=# ALTER TABLE Shohin ADD COLUMN shohin_mei_kana VARCHAR(100);
ALTER TABLE

-- 確認
postgres=# \d Shohin;
                           Table "public.shohin"
     Column      |          Type          | Collation | Nullable | Default
-----------------+------------------------+-----------+----------+---------
 shohin_id       | character(4)           |           | not null |
 shohin_mei      | character varying(100) |           | not null |
 shohin_bunrui   | character varying(32)  |           | not null |
 hanbai_tanka    | integer                |           |          |
 shiire_tanka    | integer                |           |          |
 torokubi        | date                   |           |          |
 shohin_mei_kana | character varying(100) |           |          |
Indexes:
    "shohin_pkey" PRIMARY KEY, btree (shohin_id)
```

列を削除

```sql
-- 構文
ALTER TABLE <テーブル名> DROP COLUMN <列名>;
```

```psql
-- shohin_mei_kana 列を削除
postgres=# ALTER TABLE Shohin DROP COLUMN shohin_mei_kana;
ALTER TABLE

-- 確認
postgres=# \d Shohin;
                          Table "public.shohin"
    Column     |          Type          | Collation | Nullable | Default
---------------+------------------------+-----------+----------+---------
 shohin_id     | character(4)           |           | not null |
 shohin_mei    | character varying(100) |           | not null |
 shohin_bunrui | character varying(32)  |           | not null |
 hanbai_tanka  | integer                |           |          |
 shiire_tanka  | integer                |           |          |
 torokubi      | date                   |           |          |
Indexes:
    "shohin_pkey" PRIMARY KEY, btree (shohin_id)
```

### データ登録

```sql
BEGIN TRANSACTION;

INSERT INTO shohin VALUES ('0001', 'Tシャツ', '衣服', 1000, 500, '2009-09-20');
...
INSERT INTO shohin VALUES ('0008', 'ボールペン', '事務用品', 100, NULL, '2009-11-11');

COMMIT;

-- psql 8件登録したので INSERT は8回表示される
BEGIN
INSERT 0 1
...
INSERT 0 1
COMMIT
```

`RENAME`
: テーブル名を変更する

```sql
-- 構文
ALTER TABLE <変更前テーブル名> RENAME TO <変更後テーブル名>;
```

# 検索の基本

## SELECT文の基本

```sql
-- 構文
SELECT <列名> FROM <テーブル名>;
```

```sql
postgres=# SELECT shohin_id, shohin_mei, shiire_tanka
FROM
Shohin;
 shohin_id |   shohin_mei   | shiire_tanka
-----------+----------------+--------------
 0001      | Tシャツ        |          500
...
(8 rows)
```

```sql
-- 日本語で別名を付ける
-- ダブルクォートで囲む
SELECT shohin_id AS "商品ID",
    shohin_mei AS "商品名",
    shiire_tanka AS "仕入単価"
  FROM
  Shohin;
```

### 定数を使う

```sql
-- 1列名: 商品という文字列定数
-- 2列目: 38という数値定数
-- 3列目: 2009-02-24という日付定数
SELECT '商品' AS mojiretu, 38 AS kazu, '2009-02-24' AS hizuke, shohin_id, shohin_mei
  FROM Shohin;
 mojiretu | kazu |   hizuke   | shohin_id |   shohin_mei
----------+------+------------+-----------+----------------
 商品     |   38 | 2009-02-24 | 0001      | Tシャツ
 ...
 商品     |   38 | 2009-02-24 | 0008      | ボールペン
(8 rows)
```

### 重複行を省く

`DISTINCT` キーワードは先頭の列に書く

```sql
SELECT DISTINCT shohin_bunrui, torokubi
  FROm Shohin;
```

### WHERE 句

```sql
-- WHERE 句の条件はシングルクォートで囲む
SELECT shohin_mei, shohin_bunrui
  FROM Shohin
  WHERE shohin_bunrui = '衣服';
```

## 算術演算子と比較演算子

### 算術演算子

```sql
SELECT shohin_mei, hanbai_tanka, hanbai_tanka * 2 AS "hanbai_tanka_X2"
  FROM Shohin;
```

### 比較演算子

```sql
-- hanbai_tanka が500に等しい
SELECT shohin_mei, shohin_bunrui
  FROM Shohin
  WHERE hanbai_tanka = 500;
```

```sql
-- hanbai_tanka が500に等しくない
SELECT shohin_mei, shohin_bunrui
  FROM Shohin
  WHERE hanbai_tanka <> 500;
```

```sql
-- hanbai_tanka が1000以上
SELECT shohin_mei, shohin_bunrui, hanbai_tanka
FROM Shohin
WHERE hanbai_tanka >= 1000;
```

```sql
-- torokubi が2009年9月27日より前
SELECT shohin_mei, torokubi
FROM Shohin
WHERE torokubi < '2009-09-27';
```

```sql
-- hanbai_tanka と shiire_tanka の差額が500以上
SELECT shohin_mei, hanbai_tanka, shiire_tanka
FROM Shohin
WHERE hanbai_tanka - shiire_tanka >= 500;
```

```sql
CREATE TABLE Chars
  (chr CHAR(3) NOT NULL,
  PRIMARY KEY (chr));

BEGIN TRANSACTION;
INSERT INTO Chars VALUES('1');
INSERT INTO Chars VALUES('2');
INSERT INTO Chars VALUES('3');
INSERT INTO Chars VALUES('10');
INSERT INTO Chars VALUES('11');
INSERT INTO Chars VALUES('222');
COMMIT;
```

```sql
-- 文字列型の順序は辞書式
SELECT chr FROM Chars WHERE chr > '2';
 chr
-----
 3
 222
(2 rows)
```

### NULLに比較演算子は使えない

`IS NULL` でNULLを選択できる

```sql
-- shiire_tanka が2800に等しい
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shiire_tanka = 2800;

-- shiire_tanka が2800に等しくない
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shiire_tanka <> 2800;

-- shiire_tanka がNULLである
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shiire_tanka IS NULL;

-- shiire_tanka がNULLでない
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shiire_tanka IS NOT NULL;
```

## 論理演算子

### NOT演算子

```sql
-- hanbai_tanka が1000以上でない
-- hanbai_tanka が1000未満
SELECT shohin_mei, shohin_bunrui, hanbai_tanka
FROM Shohin
WHERE NOT hanbai_tanka >= 1000;
```

### AND演算子/OR演算子

```sql
-- shohin_bunrui がキッチン用品に等しく
-- かつ hanbai_tanka が3000以上
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shohin_bunrui = 'キッチン用品'
  AND hanbai_tanka >= 3000;

-- shohin_bunrui がキッチン用品に等しいか
-- または hanbai_tanka が3000以上
SELECT shohin_mei, shiire_tanka
FROM Shohin
WHERE shohin_bunrui = 'キッチン用品'
  OR hanbai_tanka >= 3000;
```

### ()を使う

商品分類が事務用品かつ登録日が2009/9/11または2009/9/20

```sql
-- 間違ったクエリ
-- shohin_bunrui が事務用品かつ torokubi が2009-09-11
-- または torokubi が2009-09-20 を選択している
SELECT shohin_mei, shohin_bunrui, torokubi
FROM Shohin
WHERE shohin_bunrui = '事務用品'
  AND torokubi = '2009-09-11'
  or torokubi = '2009-09-20';
  shohin_mei  | shohin_bunrui |  torokubi
--------------+---------------+------------
 Tシャツ      | 衣服          | 2009-09-20
 穴あけパンチ | 事務用品      | 2009-09-11
 包丁         | キッチン用品  | 2009-09-20
 フォーク     | キッチン用品  | 2009-09-20
(4 rows) -- 衣服やキッチン用品が含まれた

-- torokubi を()で囲み優先順位を正す
SELECT shohin_mei, shohin_bunrui, torokubi
FROM Shohin
WHERE shohin_bunrui = '事務用品'
  AND (torokubi = '2009-09-11'
  or torokubi = '2009-09-20');
  shohin_mei  | shohin_bunrui |  torokubi
--------------+---------------+------------
 穴あけパンチ | 事務用品      | 2009-09-11
```

### 論理演算子と真理値

論理演算子 NOT/AND/OR の3つ。NULL値の真理値は不明となる

```sql
SELECT shohin_mei, torokubi
FROM Shohin
WHERE torokubi >= '2009-04-28';
```

```sql
SELECT *
FROM Shohin
WHERE shohin_mei > NULL;
```

# 集約と並べ替え

## 集約して検索

- 複数のレコードを集約して1レコードにまとめる
- 集約関数は **NULL** を除外する
- `COUNT(*)` で**NULL** を含めた行数を数える
- `DISTINCT` で重複を除外できる

### 集約関数

| 集約関数  | 機能                   |
| :-------- | :--------------------- |
| **COUNT** | レコードの行数を数える |
| **SUM**   | 数値列を合計する       |
| **AVG**   | 数値列を平均する       |
| **MAX**   | 列の最大値を求める     |
| **MIN**   | 列の最小値を求める     |

### 重複値を除外

`DISTINCT`

```sql
-- shohin_bunrui に登録されている種類を調べる
SELECT COUNT(DISTINCT shohin_bunrui)
FROM Shohin;

-- hanbai_tanka の合計
-- DISTINCTなし/ DISTINCTあり
SELECT SUM(hanbai_tanka), SUM(DISTINCT hanbai_tanka)
FROM Shohin;
```

## グループに分ける

`GROUP BY` : データをカテゴライズして分ける。 **WHERE** 語には使えない。

```sql
-- shohin_bunrui 毎の商品数
SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui;
```

### 集約にNULLがある場合

`NULL` もグループ化されて集約される。

```sql
-- shiire_tanka 毎にグループ化
SELECT shiire_tanka, COUNT(*)
FROM Shohin
GROUP BY shiire_tanka;
 shiire_tanka | count
--------------+-------
              |     2
          320 |     1
...
```

### WHERE 句

WHERE 句の条件でレコードが絞り込まれた後、集約される。

```sql
-- shohin_bunrui が衣服であるレコードを
-- shiire_tanka 毎にグループ化し、数える
SELECT shiire_tanka, COUNT(*)
FROM Shohin
WHERE shohin_bunrui = '衣服'
GROUP BY shiire_tanka;
 shiire_tanka | count
--------------+-------
          500 |     1
         2800 |     1
```

### 集約関数とGROUP BY句にまつわる間違い

- `SELECT` 句に書くことができるのは3つの要素
- `GROUP BY` 句で列の別名は使えない
- `GROUP BY` 句は結果を並び替えない
- `WHERE` 句には集約関数を使えない

`SELECT` 句に書くことができる要素

- 定数
- 集約関数
- `GROUP BY` 句で指定した列

## 集約結果に条件を指定

### HAVING 句

`HAVING` 集約したブループ化列に対して条件指定する場合に用いる

```sql
-- 構文
SELECT <列名1>, <列名2>, <列名3>, ...
FROM <テーブル名>
GROUP BY <列名1>, <列名2>, <列名3>, ...
-- HAVING 句は GROUP BY 句の後に書くこと
HAVING <グループの値に対する条件>;
```

```sql
-- GROUP BY の集約結果に対して
-- shohin_bunrui が2件の分類だけ調べるには？
SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui;
 shohin_bunrui | count
---------------+-------
 キッチン用品  |     4
 衣服          |     2
 事務用品      |     2
(3 rows)

SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui
HAVING COUNT(*) = 2;

-- COUNT(*) はなくても動作する
SELECT shohin_bunrui
FROM Shohin
GROUP BY shohin_bunrui
HAVING COUNT(*) = 2;
 shohin_bunrui
---------------
 衣服
 事務用品
 (2 rows)
```

```sql
-- hanbai_tanka の平均が2500以上のもの
SELECT shohin_bunrui, AVG(hanbai_tanka)
FROM Shohin
GROUP BY shohin_bunrui
HAVING AVG(hanbai_tanka) >= 2500;
```

```sql
-- SELECT 句の列名は別名を付けれる
SELECT shohin_bunrui, AVG(hanbai_tanka) AS avb_tanka
FROM Shohin
GROUP BY shohin_bunrui
HAVING AVG(hanbai_tanka) >= 2500;
```

```sql
-- エラー
-- HAVING 句の列名は別名を付けれない
SELECT shohin_bunrui, AVG(hanbai_tanka) AS avg_tanka
FROM Shohin
GROUP BY shohin_bunrui
HAVING AVG(hanbai_tanka) AS avg_tanka >= 2500;
ERROR: syntax error at or near "AS"
LINE 4: HAVING AVG(hanbai_tanka) AS avb_tanka >= 2500;

-- エラー
-- HAVING 句は別名だけで呼び出せいない
SELECT shohin_bunrui, AVG(hanbai_tanka) AS avg_tanka
FROM Shohin
GROUP BY shohin_bunrui
HAVING avg_tanka >= 2500;
ERROR:  column "avg_tanka" does not exist
LINE 4: HAVING avg_tanka >= 2500;
```

### HAVING 句に書ける要素

- 定数
- 集約関数
- `GROUP BY` 句に指定した列名

### WHERE 句に書いたほうが良い条件

`HAVING` 句より `WHERE` 句に書いたほうが良い条件

- 行に対する条件は `WHERE` 句に書く
- グループに対する条件は `HAVING` 句に書く
- パフォーマンスが `WHERE` 句のほうが優れている

望ましくない使い方

```sql
SELECT shohin_bunrui
FROM Shohin;
 shohin_bunrui
---------------
 ...
 キッチン用品
 事務用品
(8 rows)

-- グループ化した要素の含まれる値のみに絞り込める
SELECT shohin_bunrui
FROM Shohin
GROUP BY shohin_bunrui
-- shohin_bunrui列の値 キッチン用品は 行に対する条件
HAVING shohin_bunrui = 'キッチン用品';
 shohin_bunrui
---------------
 キッチン用品
```

```sql
EXPLAIN SELECT shohin_bunrui
FROM Shohin
GROUP BY shohin_bunrui
HAVING shohin_bunrui = 'キッチン用品';
                           QUERY PLAN
----------------------------------------------------------------
 Group  (cost=0.00..12.75 rows=1 width=82)
   ->  Seq Scan on shohin  (cost=0.00..12.75 rows=1 width=82)
         Filter: ((shohin_bunrui)::text = 'キッチン用品'::text)
(3 rows)
```

```sql
EXPLAIN SELECT shohin_bunrui
FROM Shohin
WHERE shohin_bunrui = 'キッチン用品';
                        QUERY PLAN
----------------------------------------------------------
 Seq Scan on shohin  (cost=0.00..12.75 rows=1 width=82)
   Filter: ((shohin_bunrui)::text = 'キッチン用品'::text)
(2 rows)
```

## 並び替え

`ORDER BY` 並び替え

```sql
-- ASC: 昇順。暗黙として ORDER BY の規定キーワード
SELECT shohin_mei, hanbai_tanka
FROM Shohin
ORDER BY hanbai_tanka ASC;

-- DESC: 降順
SELECT shohin_mei, hanbai_tanka
FROM Shohin
ORDER BY hanbai_tanka DESC;
```

### 複数並び替え

```sql
SELECT shohin_id, shohin_mei, hanbai_tanka, shiire_tanka
FROM Shohin
-- hanbai_tanka 昇順ソート後に shohin_id 昇順ソートされる
ORDER BY hanbai_tanka, shohin_id;
```

### ソートキーに別名を使う

実行順: `FROM` > `WHERE` > `GROUP BY` > `HAVING` > `SELECT` > `ORDER BY`

```sql
-- AS別名を ORDER BY 句で使う
SELECT shohin_id AS id, shohin_mei, hanbai_tanka AS ht, shiire_tanka
FROM Shohin
ORDER BY ht, id;

-- SELECT 句に含まれていない列でも ORDER BY 句で使える
SELECT shohin_mei, hanbai_tanka, shiire_tanka
FROM Shohin
ORDER BY shohin_id;

-- 集約関数も ORDER BY 句で使える
SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui
ORDER BY COUNT(*);
 shohin_bunrui | count
---------------+-------
 衣服          |     2
 事務用品      |     2
 キッチン用品  |     4
```

## 列番号

使うべきでない理由

- SQL文が読みにくい
  -- ANSI SQLで将来削除されるべき機能である

```sql
-- hanbai_tankaを降順、shohin_idを昇順で並び替える
SELECT shohin_id, shohin_mei, hanbai_tanka, shiire_tanka
FROM Shohin
ORDER BY 3 DESC, 1;
 shohin_id |   shohin_mei   | hanbai_tanka | shiire_tanka
-----------+----------------+--------------+--------------
 0005      | 圧力鍋         |         6800 |         5000
...
(8 rows)

SELECT shohin_id, shohin_mei, hanbai_tanka, shiire_tanka
FROM Shohin
ORDER BY hanbai_tanka DESC, shohin_id;
 shohin_id |   shohin_mei   | hanbai_tanka | shiire_tanka
-----------+----------------+--------------+--------------
 0005      | 圧力鍋         |         6800 |         5000
...
(8 rows)
```

# データの更新

## INSERT文

1回に1行を挿入する

```sql
-- 学習用データベースの作成
CREATE TABLE ShohinIns
(shohin_id CHAR(4) NOT NULL,
 shohin_mei VARCHAR(100) NOT NULL,
 shohin_bunrui VARCHAR(32) NOT NULL,
 hanbai_tanka INTEGER DEFAULT 0,
 shiire_tanka INTEGER ,
 torokubi DATE ,
     PRIMARY KEY (shohin_id));
```

## 基本構文

```sql
-- 構文
SELECT INTO <テーブル名> (列1, 列2, ...) VALUES (値1, 値2, ...);
```

```sql
INSERT INTO ShohinIns (
  shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi
) VALUES (
  '0001', 'Tシャツ', '衣服', 1000, 500, '2009-09-20'
);
INSERT 0 1 -- 成功するとpsqlから返される
```

### 列リストの省略

テーブルの前列に対して `INSERT` を行う場合は、列リストを省略できる

### NULLを挿入

`VALUES` 句の値リストに**NULL** を記述する

```sql
-- shiire_tanka をNULLとして登録
INSERT INTO ShohinIns (
shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi
) VALUES (
'0006', 'フォーク', 'キッチン用品', 500, NULL, '2009-09-20'
);

-- 確認
SELECT * FROM ShohinIns;
 shohin_id | shohin_mei | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+------------+---------------+--------------+--------------+------------
 0001      | Tシャツ    | 衣服          |         1000 |          500 | 2009-09-20
 0006      | フォーク   | キッチン用品  |          500 |              | 2009-09-20
```

### デフォルト値の挿入

- テーブル定義に `DEFAULT` 制約があること
- 明示的挿入: `VALUES` 句に `DEFAULT` キーワードを指定
- 暗黙的挿入: 列リストも `VALUES` からも省略

デフォルト値が指定されていいないかつ、 `NOT NULL` でない列では `NULL` が割り当てられる

```sql
-- 学習用データベースの作成
CREATE TABLE ShohinIns
(...
 hanbai_tanka INTEGER DEFAULT 0, -- デフォルト制約 値: 0
...
     PRIMARY KEY (shohin_id));
```

```sql
-- 明示的
-- hanbai_tanka を DEFAULT として挿入
INSERT INTO ShohinIns (
  shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi)
VALUES (
  '0007', 'おろしがね', 'キッチン用品', DEFAULT, 790, '2009-04-28'
);

-- 暗黙的
-- hanbai_tanka を 省略
INSERT INTO ShohinIns (
  shohin_id, shohin_mei, shohin_bunrui, shiire_tanka, torokubi)
-- VALUES の値も省略
VALUES (
  '0007', 'おろしがね', 'キッチン用品', 790, '2009-04-28'
);

-- 確認
SELECT * FROM ShohinIns WHERE shohin_id = '0007';
 shohin_id | shohin_mei | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+------------+---------------+--------------+--------------+------------
 0007      | おろしがね | キッチン用品  |            0 |          790 | 2009-04-28
(1 row)
```

### ほかのテーブルからコピー

- `SELECT` した結果をテーブルに `INSERT` する
- `WHERE` 句や `GROUP BY` 句も使える

```sql
-- ペースト用のテーブルを作成
CREATE TABLE ShohinCopy (
  shohin_id CHAR(4) NOT NULL,
  shohin_mei VARCHAR(100) NOT NULL,
  shohin_bunrui VARCHAR(32) NOT NULL,
  hanbai_tanka INTEGER,
  shiire_tanka INTEGER,
  torokubi DATE,
  PRIMARY KEY (shohin_id)
);
```

```sql
INSERT INTO ShohinCopy (
  shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi)
SELECT shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi
FROM Shohin;

-- 確認
SELECT COUNT(*) FROM ShohinCopy;
count
-------
     8
```

```sql
-- ShohinBunrui テーブル作成
CREATE TABLE ShohinBunrui (
  shohin_bunrui VARCHAR(32) NOT NULL,
  sum_hanbai_tanka INTEGER,
  sum_shiire_tanka INTEGER,
  PRIMARY KEY (shohin_bunrui)
);

INSERT INTO ShohinBunrui (
  shohin_bunrui, sum_hanbai_tanka, sum_shiire_tanka)
SELECT shohin_bunrui, SUM(hanbai_tanka), SUM(shiire_tanka)
FROM Shohin
GROUP BY shohin_bunrui;

-- 確認
SELECT * FROM ShohinBunrui;
 shohin_bunrui | sum_hanbai_tanka | sum_shiire_tanka
---------------+------------------+------------------
 キッチン用品  |            11180 |             8590
 衣服          |             5000 |             3300
 事務用品      |              600 |              320
(3 rows)
```

## 削除

### DROP TABLEとDELETE文

- `DROP TABLE` テーブルそのものを削除
- `DELETE` テーブルは残しすべての行を削除
  - `DELETE`文は`WHERE` 句のみ使える
  - `DELETE` 文には `GROUP BY` `HAVING` `ORDER BY` 句は使えない
- `TRUNCATE` で全行削除できる

### DELETE文の構文

```sql
-- 構文
DELETE FROM <テーブル名>;
```

```sql
DELETE FROM ShohinCopy;
DELETE 8 -- psqlが削除した行数を返す
```

### 削除対象を制限する

`WHERE` 句で条件を指定し削除対象を制限できる。これを **探索型DELETE** と呼ぶ

```sql
-- 構文
DELETE FROM <テーブル名> WHERE <条件>;
```

```sql
-- 削除前
SELECT * FROM ShohinCopy;
 shohin_id |   shohin_mei   | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+----------------+---------------+--------------+--------------+------------
...
 0008      | ボールペン     | 事務用品      |          100 |              | 2009-11-11
(8 rows)

-- hanbai_tanka が4000以上の行を削除
DELETE FROM ShohinCopy
WHERE hanbai_tanka >= 4000;
DELETE 2 -- psqlが削除した行数を返す

-- 削除後確認
 shohin_id |  shohin_mei  | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+--------------+---------------+--------------+--------------+------------
...
 0008      | ボールペン   | 事務用品      |          100 |              | 2009-11-11
(6 rows)
```

## データの更新

### 構文

`UPDATE` データを変更/更新する

```sql
UPDATE <テーブル名> SET <列名> = <式>;
```

```sql
SELECT * FROM Shohin;
 shohin_id |  shohin_mei  | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+--------------+---------------+--------------+--------------+------------
 0001      | Tシャツ      | 衣服          |         1000 |          500 | 2009-09-20
 0002      | 穴あけパンチ | 事務用品      |          500 |          320 | 2009-09-11
 0004      | 包丁         | キッチン用品  |         3000 |         2800 | 2009-09-20
 0006      | フォーク     | キッチン用品  |          500 |              | 2009-09-20
 0007      | おろしがね   | キッチン用品  |          880 |          790 | 2008-04-28
 0008      | ボールペン   | 事務用品      |          100 |              | 2009-11-11
(6 rows)
```

```sql
-- torokubi を全行2009/10/10に更新
UPDATE Shohin
SET torokubi = '2009-10-10';
```

### 条件指定

**探索型UPDATE** 条件を指定した `UPDATE`

```sql
-- 構文
UPDATE <テーブル名>
SET <列名> = <式>
WHERE <条件>;
```

```sql
-- shohin_bunrui がキッチン用品である行を hanbai_tanka を10倍にする
UPDATE Shohin
SET hanbai_tanka = hanbai_tanka * 10
WHERE shohin_bunrui = 'キッチン用品';
UPDATE 3 -- 更新された行数が返される
```

### NULLで更新

**NULLクリア** 列をNULLで更新すること

```sql
-- shohin_id=0008 の登録日をNULLクリア
UPDATE Shohin
SET torokubi = NULL
WHERE shohin_id = '0008';
UPDATE 1 -- 更新された行数が返される
```

### 複数列の更新

```sql
UPDATE Shohin
SET hanbai_tanka = hanbai_tanka * 10,
  shiire_tanka = shiire_tanka / 2
WHERE shohin_bunrui = 'キッチン用品';
UPDATE 3 -- 更新された行数が返される
```

## トランザクション

**トランザクション**

- セットで実行されるべき1つ以上の更新処理
- データ更新処理の確定や取り消しを管理できる
- 処理を終わらせるコマンドに **COMMIT** と **ROLLBACK** がある
- ACID特性を守ること
  - A: Atomicity(原子性)
  - C: Consistency(一貫性)
  - I: Isolation(独立性)
  - D: Durability(永続性)

### トランザクションとは

データベースに対する1つ以上の更新をまとめた名称

### トランザクションを作る

```sql
-- 構文
BEGIN TRANSACTION; -- トランザクション開始文;

DML文1, -- INSERT/UPDATE/DELETE
DML文2,
DML文3,
...

トランザクション終了文(COMMIT or ROLLBACK);
```

- **BEGIN TRANSACTION** 処理の開始
- **COMMIT** 処理の確定
- **ROLLBACK** 処理の取り消し

```sql
-- 確定処理
BEGIN TRANSACTION; -- トランザクション開始

  -- カッターシャツの販売単価を1000円値引き
  UPDATE Shohin
  SET hanbai_tanka = hanbai_tanka - 1000
  WHERE shohin_mei = 'カッターシャツ';

  -- Tシャツの販売単価を1000円値上げ
  UPDATE Shohin
  SET hanbai_tanka = hanbai_tanka + 1000
  WHERE shohin_mei = 'Tシャツ';

COMMIT; -- トランザクション確定
```

```sql
-- 取り消し処理
BEGIN TRANSACTION; -- トランザクション開始

  -- カッターシャツの販売単価を1000円値引き
  UPDATE Shohin
  SET hanbai_tanka = hanbai_tanka - 1000
  WHERE shohin_mei = 'カッターシャツ';

  -- Tシャツの販売単価を1000円値上げ
  UPDATE Shohin
  SET hanbai_tanka = hanbai_tanka + 1000
  WHERE shohin_mei = 'Tシャツ';

ROLLBACK; -- トランザクション取り消し
```

### ACID特性

原子性(Atomicity)
: トランザクション終了時に含まれていた更新処理はすべて実行されるか、すべて実行されない状態で終了すること

一貫性(Consistency)
: トランザクションに含まれる処理は、設定された制約(主キー制約、NOT NULL制約)を満たす

独立性(Isolation)
: トランザクション同士が互いに干渉を受けない

永続性(Durability)
: トランザクションが終了したら、その時点でデータの状態が保存される

# 複雑な問い合わせ

## ビュー

- 中に `SELECT` 文が保存される
- 複雑な集約を楽に行える
- `CREATE VIEW` で作成する
- `ORDER BY` 句は使えない -　更新には制限がる
- `DROP VIEW` で削除

### ビューとテーブル

- **ビュー** は実際のデータを保存しない
- **ビュー** は `SELECT` 文を保存する
- `SELECT` 文を実行し一時的に仮想テーブルを作る
- `SELECT` 文の使いまわしが可能
- データを保存しないので、保存容量を節約できる

### 作り方

```sql
CREATE VIEW ビュー名 (<ビューの列名1>, <ビューの列名2>, ...) AS <SELECT文>;
```

```sql
-- Shohin を元にビューを作成
-- shohin_bunrui 毎の集計商品数
CREATE VIEW ShohinSum (
  shohin_bunrui, cnt_shohin
) AS
SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui;
CREATE VIEW -- psqlから返されたメッセージ

-- 確認
SELECT shohin_bunrui, cnt_shohin
FROM ShohinSum; -- ビュー名指定
 shohin_bunrui | cnt_shohin
```

1. 最初にビューに定義した `SELECT` 文を実行
2. その結果に対して、ビューを `FROM` 句にした `SELECT' 文を実行

```sql
-- 既存のビューを元にビューを作成
-- パフォーマンス低下を招くので避ける
CREATE VIEW ShohinSumJim (
  shohin_bunrui, cnt_shohin
) AS
SELECT shohin_bunrui, cnt_shohin
FROM ShohinSum
WHERE shohin_bunrui = '事務用品';

-- 確認
SELECT * FROM ShohinSumJim;
```

### 制限事項

`ORDER BY` 句は使えない。行に順序はないという基本原則に違反するため

許される更新系のビュー

- `SELECT` 句に `DISTINCT` が含まれていない
- `FROM` 句には1テーブルのみ存在する
- `GROUP BY` 句を使っていない
- `HAVING` 句を使ってない

```sql
-- 元テーブル Shohin と完全一致の列を持つビューを定義
CREATE VIEW ShohinJim (
  shohin_id, shohin_mei, shohin_bunrui, hanbai_tanka, shiire_tanka, torokubi)
AS
SELECT *
FROM Shohin
WHERE shohin_bunrui = '事務用品';

--
INSERT INTO ShohinJim
VALUES (
  '0009', '印鑑', '事務用品', 95, 10, '2009-11-30'
);

-- 確認
SELECT * FROM  ShohinJim;
 shohin_id |  shohin_mei  | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+--------------+---------------+--------------+--------------+------------
 0002      | 穴あけパンチ | 事務用品      |          500 |          320 | 2009-09-11
 0008      | ボールペン   | 事務用品      |          100 |              | 2009-11-11
 0009      | 印鑑         | 事務用品      |           95 |           10 | 2009-11-30

 -- 元テーブルにも `INSERT` されている
 SELECT * FROM  Shohin;
 shohin_id |   shohin_mei   | shohin_bunrui | hanbai_tanka | shiire_tanka |  torokubi
-----------+----------------+---------------+--------------+--------------+------------
...
 0009      | 印鑑           | 事務用品      |           95 |           10 | 2009-11-30
(9 rows)
```

```sql
-- ShohinJim の定義を確認、元テーブルの定義がコピーされている
postgres=# \d ShohinJim
                         View "public.shohinjim"
    Column     |          Type          | Collation | Nullable | Default
---------------+------------------------+-----------+----------+---------
 shohin_id     | character(4)           |           |          |
 shohin_mei    | character varying(100) |           |          |
 shohin_bunrui | character varying(32)  |           |          |
 hanbai_tanka  | integer                |           |          |
 shiire_tanka  | integer                |           |          |
 torokubi      | date                   |           |          |
```

### 削除

```sql
-- 構文
DROP VIEW <ビュー名>;
```

```sql
-- 多段ビューの作成元ビューを削除する場合、以下のエラーが発生する
DROP VIEW ShohinSum;
ERROR:  cannot drop view ShohinSum because other objects depend on it
DETAIL:  view ShohinSumJim depends on view shohinSum
HINT:  Use DROP ... CASCADE to drop the dependent objects too.

-- `CASCADE` 句を付ける
DROP VIEW ShohinSum CASCADE;

-- 確認
\d
List of relations
Schema | Name | Type | Owner
--------+--------------+-------+----------
...
(2 rows)
```

## サブクエリ

- 使い捨てのビューである
- `SELECT` 文実行後に消去される
- 名前が必要
- **スカラサブクエリ** とは1行1列のみを返す

## サブクエリとビュー

```sql
-- 商品分類毎に商品数を集計するビュー
CREATE VIEW ShohinSum (shohin_bunrui, cnt_shohin)
AS
SELECT shohin_bunrui, COUNT(*)
FROM Shohin
GROUP BY shohin_bunrui;

--　ShohinSumビューを確認
SELECT shohin_bunrui, cnt_shohin
FROM ShohinSum;
```

サブクエリに書き換え

```sql
SELECT shohin_bunrui, cnt_shohin
FROM (
  -- ビュー定義のSELECT文をそのまま書く
  SELECT shohin_bunrui, COUNT(*) AS cnt_shohin
  FROM Shohin
  GROUP BY shohin_bunrui) AS ShohinSum; -- ShohinSum はサブクエリ名
```

多段サブクエリ

```sql
SELECT shohin_bunrui, cnt_shohin
FROM (
  -- 2番目に実行される
  SELECT *
  FROM (
    -- 1番目に実行される
    -- 商品分類毎に商品数を集計する
    SELECT shohin_bunrui, COUNT(*) AS cnt_shohin
    FROM Shohin
    GROUP BY shohin_bunrui
  ) AS ShohinSum
-- 別名 cnt_shohin が4に絞り込み
-- サブクエリ名は ShohinSum2
WHERE cnt_shohin = 4) AS ShohinSum2;
 shohin_bunrui | cnt_shohin
---------------+------------
 キッチン用品  |          4
(1 row)
```

### サブクエリの名前

処理内容を反映した名称を付けること

### スカラサブクエリ

**スカラサブクエリ**

- 必ず1行1列だけの戻り値を返す
- スカラの特性を活かして比較演算子の入力値として使える

### WHERE句でサブクエリ

販売単価が平均単価より高い商品を求める

```sql
-- 平均単価を求める スカラ値
SELECT AVG(hanbai_tanka)
FROM Shohin;

-- 平均単価を求めるクエリをサブクエリに組み込む
SELECT shohin_id, shohin_mei, hanbai_tanka
FROM Shohin
WHERE hanbai_tanka > (
  SELECT AVG(hanbai_tanka)
  FROM Shohin
);
 shohin_id |   shohin_mei   | hanbai_tanka
-----------+----------------+--------------
 0003      | カッターシャツ |         4000
 0004      | 包丁           |         3000
 0005      | 圧力鍋         |         6800
```

### スカラサブクエリを書ける場所

- `WHERE` 句
- 定数
- 列
- `GROUP BY` 句
- `HAVING` 句
- `ORDER BY` 句

```sql
-- SELECT句でスカラサブクエリを使う
SELECT
  shohin_id,
  shohin_mei,
  hanbai_tanka,
  (
    SELECT
      AVG(hanbai_tanka)
      FROM Shohin
  ) AS avg_tanka
FROM Shohin;
 shohin_id |   shohin_mei   | hanbai_tanka |       avg_tanka
-----------+----------------+--------------+-----------------------
 0001      | Tシャツ        |         1000 | 2097.5000000000000000
 0002      | 穴あけパンチ   |          500 | 2097.5000000000000000
...
(8 rows)
```

```sql
-- HAVING句でスカラサブクエリを使う
-- 商品分類毎の平均販売単価が
-- 商品全体の販売単価より高い商品部類を求める
SELECT shohin_bunrui, AVG(hanbai_tanka)
FROM Shohin
GROUP BY shohin_bunrui
HAVING AVG(hanbai_tanka) > (
  SELECT AVG(hanbai_tanka) FROM Shohin
);
 shohin_bunrui |          avg
---------------+-----------------------
 キッチン用品  | 2795.0000000000000000
 衣服          | 2500.0000000000000000
(2 rows)
```

### 注意点

サブクエリは複数行を返してはいけない

## 相関サブクエリ

- 小分けにしたブループ内での比較
- `GROUP BY` 句と同じように集合のカット機能がある
- 結合条件はサブクエリの中に書く

```sql
-- 商品分類毎に平均販売単価より高い商品

-- 商品分類別に平均価格を求める
SELECT AVG(hanbai_tanka)
FROM Shohin
GROUP BY shohin_bunrui;

-- 商品分類毎に平均販売単価を求める相関サブクエリ
SELECT
  shohin_bunrui, shohin_mei, hanbai_tanka
FROM Shohin AS S1
WHERE hanbai_tanka > (
  SELECT AVG(hanbai_tanka)
  FROM Shohin AS S2
  WHERE S1.shohin_bunrui = S2.shohin_bunrui
  GROUP BY shohin_bunrui
);
 shohin_bunrui |   shohin_mei   | hanbai_tanka
---------------+----------------+--------------
 事務用品      | 穴あけパンチ   |          500
 衣服          | カッターシャツ |         4000
 キッチン用品  | 包丁           |         3000
 キッチン用品  | 圧力鍋         |         6800
(4 rows)
```

SELECT shohin_bunrui, AVG(hanbai_tanka)
FROM Shohin
GROUP BY shohin_bunrui;

### 結合条件はサブクエリの中に書く

- サブクエリ内で付けられた相関名は、そのサブクエリ内でしか使えない
- 内側から外側は見えるが、外側から内側は見えない

```sql
SELECT
  shohin_bunrui, shohin_mei, hanbai_tanka
FROM Shohin AS S1 -- 相関名 S1
WHERE hanbai_tanka > (
  SELECT AVG(hanbai_tanka)
  -- 相関名 S2は()の外は見えない
  FROM Shohin AS S2 -- 相関名 S2
  WHERE S1.shohin_bunrui = S2.shohin_bunrui
  GROUP BY shohin_bunrui
);
```

# 関数、述語、CASE式
