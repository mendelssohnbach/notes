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
