---
tags:
  - DB
---

バージョン確認

```terminal
$ psql -V
psql (PostgreSQL) 18.3 (Ubuntu 18.3-1.pgdg24.04+1)
```

## ログイン

### CLIログイン

```terminal
$ psql -h db -U postgres -d study
Password for user postgres:
study=#
```

| 短縮オプション | フルオプション | 意味           | 指定値   |
| :------------- | :------------- | :------------- | :------- |
| -h             | --host         | ホスト名       | db       |
| -U             | --username     | 接続ユーザー名 | postgres |
| -d             | --dbname       | データベース名 | study    |

### 拡張機能の設定

- パラメータ
  ![接続する](assets/image-0.png)

- ポート
  ![高度な接続設定 ポート](assets/image-1.png)

- SSL
  ![高度な接続設定 SSL](assets/image-2.png)

## psqlのプロンプト一覧

| プロンプト | 状態の意味                         | ユーザーがすべきこと                             |
| :--------- | :--------------------------------- | :----------------------------------------------- |
| study=#    | コマンド受付中（正常）             | 新しいSQLや「\」コマンドを入力する               |
| study-#    | SQLの入力途中（未完了）            | SQLの末尾に「;」を付けてEnterを押す              |
| study'#    | シングルクォーテーションの閉じ忘れ | 「'」を入力して文字列を閉じ「;」を付けて実行する |
| study"#    | ダブルクォーテーションの閉じ忘れ   | 「"」を入力して識別子を閉じ「;」を付けて実行する |
| study$#    | ドル記号（$$）の閉じ忘れ           | 「$$」を入力して囲みを閉じる                     |
| study\*#   | ブロックコメントの閉じ忘れ         | 「\*/」を入力してコメントアウトを終了させる      |

## psqlコマンド

ユーザー表示

```psql
# \du
                             List of roles
 Role name |                         Attributes
-----------+------------------------------------------------------------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS

study=# select USENAME from pg_user;
 usename
----------
 postgres
(1 row)
```

データベース一覧表示

```psql
=# \l
                                                    List of databases
   Name    |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+------------+------------+--------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 study     | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 template0 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/postgres          +
           |          |          |                 |            |            |        |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/postgres          +
           |          |          |                 |            |            |        |           | postgres=CTc/postgres
(4 rows)
```

新しいデータベースを作成し、確認

```psql
=# create database sample;
=# \l
                                                   List of databases
   Name    |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+------------+------------+--------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 sample    | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 study     | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 template0 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/postgres          +
           |          |          |                 |            |            |        |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/postgres          +
           |          |          |                 |            |            |        |           | postgres=CTc/postgres
(5 rows)
```

テーブルを作成

```psql
=# create table staff (id integer, name character varying(10));
CREATE TABLE
```

テーブル一覧を表示

```psql
-- 標準的なテーブルを表示
=# \dt
          List of tables
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | staff | table | postgres
(1 row)

- テーブルサイズやコメントを含んで詳しく表示
=# \dt+
                                     List of tables
 Schema | Name  | Type  |  Owner   | Persistence | Access method |  Size   | Description
--------+-------+-------+----------+-------------+---------------+---------+-------------
 public | staff | table | postgres | permanent   | heap          | 0 bytes |
(1 row)
```

データを追加

```psql
=# INSERT INTO staff (id, name) VALUES
study-# (1, '佐藤 健'),
(2, '鈴木 一郎'),
(3, '高橋 結衣'),
(4, '田中 太郎'),
(5, '伊藤 翼'),
(6, '渡辺 直美'),
(7, '岡田 准一'),
(8, '小林 麻央'),
(9, '加藤 浩次'),
(10, '木村 拓哉');
INSERT 0 10

- 確認
=# \dt+
                                       List of tables
 Schema | Name  | Type  |  Owner   | Persistence | Access method |    Size    | Description
--------+-------+-------+----------+-------------+---------------+------------+-------------
 public | staff | table | postgres | permanent   | heap          | 8192 bytes |
```

問い合わせ

```psql
=# SELECT * FROM staff;
 id |   name
----+-----------
  1 | 佐藤 健
  2 | 鈴木 一郎
  3 | 高橋 結衣
  4 | 田中 太郎
  5 | 伊藤 翼
  6 | 渡辺 直美
  7 | 岡田 准一
  8 | 小林 麻央
  9 | 加藤 浩次
 10 | 木村 拓哉
(10 rows)

=# SELECT id,  name FROM staff LIMIT 5;
 id |   name
----+-----------
  1 | 佐藤 健
  2 | 鈴木 一郎
  3 | 高橋 結衣
  4 | 田中 太郎
  5 | 伊藤 翼
(5 rows)

=# SELECT name FROM staff WHERE id BETWEEN 3 AND 6;
   name
-----------
 高橋 結衣
 田中 太郎
 伊藤 翼
 渡辺 直美
(4 rows)
```

CSVエクスポート

```psql
=# \copy staff TO '/workspace/export/staff_data.csv' WITH CSV HEADER;
COPY 10
```

エクスポート結果を確認

```terminal
$ cat export/staff_data.csv
id,name
1,佐藤 健
2,鈴木 一郎
3,高橋 結衣
4,田中 太郎
5,伊藤 翼
6,渡辺 直美
7,岡田 准一
8,小林 麻央
9,加藤 浩次
10,木村 拓哉
```

レコード削除

```psql
=# DELETE FROM staff WHERE id >= 7;
DELETE 4

-- 確認
=# SELECT * FROM staff;
 id |   name
----+-----------
  1 | 佐藤 健
  2 | 鈴木 一郎
  3 | 高橋 結衣
  4 | 田中 太郎
  5 | 伊藤 翼
  6 | 渡辺 直美
(6 rows)
```

全レコード削除し、リストア

```psql
=# DELETE FROM staff;
DELETE 6

-- 確認
=# SELECT * FROM staff;
 id | name
----+------
(0 rows)
```

CSVインポート

```psql
-- レコードリストア
=# \copy staff FROM './export/staff_data.csv' WITH CSV HEADER;
COPY 10

-- 確認
=# SELECT * FROM staff;
 id |   name
----+-----------
  1 | 佐藤 健
  2 | 鈴木 一郎
  3 | 高橋 結衣
  4 | 田中 太郎
  5 | 伊藤 翼
  6 | 渡辺 直美
  7 | 岡田 准一
  8 | 小林 麻央
  9 | 加藤 浩次
 10 | 木村 拓哉
(10 rows)
```

単一データベースのダンプ(バックアップ)

```terminal
$ pg_dump -h db -U postgres -d study -F c -f ./export/dump_db.dump
Password:
```

## コマンド

```psql
=# \l -- データベース一覧表示
=# \dt+ -- 詳細なテーブル一覧表示
=# CREATE DATABASE DATABASE_NAME;  -- 新しいデータベース作成
=# DROP DATABASE DATABASE_NAME;  -- データベース削除
=# SELECT current_database();  -- 現在のデータベース名表示
=# CREATE TABLE TABLE_NAME (COLUMN_NAME DATA_TYPE);  -- テーブル作成
=# DROP TABLE TABLE_NAME;  -- テーブル削除
```

## PL/pgSQL

**PL/pgSQL**
: SQLを拡張した手続き型プログラミング言語。

### 特徴

- 手続き処理を行える
- マルチプラットフォーム
- 高いパフォーマンス
- `SELECT` から呼び出し可能であること
- 関数本文は `$$` クォーテーションで囲むこと

ブロック構造

```
DECLARE
  宣言部
BEGIN
  実行部
EXCEPTION
  例外処理部
END;
```

CREATE FUNCTION
: 関数を作成定義する

REPLACE FUNCTION
: 既存の関数の戻り値を変更する

DROP FUNCTION
: `CREATE OR REPLACE FUNCTION` で作成した関数の戻り値を変更する

**PL/pgSQL** が利用できるか確認

```psql
y=# SELECT lanname FROM pg_language WHERE lanname='plpgsql';
 lanname
---------
 plpgsql
```

コード例を示す

```psql
study=# DO LANGUAGE plpgsql $$
study$# BEGIN
study$# RAISE NOTICE 'Hello PL/pgSQL';
study$# END;
study$# $$;
NOTICE:  Hello PL/pgSQL
DO
```

### CREATE FUNCTION

構文

```psql
CREATE OR REPLACE FUNCTION
  name ( [ argname ] argtype  [, ... ] ] )
  RETURN rettype as $$
  関数本文
$$
LANGUAGE lang_name
```

```psql
-- 関数本文は `$$` クォーテーションで囲むこと
-- hello 関数を定義
study=# CREATE FUNCTION hello()
study-# RETURNS text
study-# AS $$
study$# BEGIN
study$# RETURN 'Hello Postgres PL/pgSQL';
study$# END;
study$# $$
study-# LANGUAGE plpgsql;
CREATE FUNCTION

-- 定義した hello 関数を呼び出す
study=# SELECT hello();
          hello
-------------------------
 Hello Postgres PL/pgSQL
```

既存の関数一覧を表示

```psql
study=# \df
                       List of functions
 Schema | Name  | Result data type | Argument data types | Type
--------+-------+------------------+---------------------+------
 public | hello | text             |                     | func
```

### TDD

[pgTAP](https://pgtap.org/) を用いる。

```psql
-- pgTAP拡張機能をインストール
=# CREATE EXTENSION IF NOT EXISTS pgtap;
```

失敗するテストを書く

```psql
-- test_increment.sql
BEGIN;

-- 2つのテストを実行することを宣言
SELECT plan(2);

-- テストケース1：正の数 5 を渡したら 6 が返るべき
SELECT is(
    increment_if_positive(5),
    6,
    '正の数の場合は+1されて返ること'
);

-- テストケース2：負の数 -3 を渡したら -3 が返るべき
SELECT is(
    increment_if_positive(-3),
    -3,
    '負の数の場合はそのまま返ること'
);

-- テスト結果の集計
SELECT * FROM finish();
ROLLBACK;
```

テストを実行

```terminal
$ psql -d your_database -f test_increment.sql
# 結果は Red
# ERROR: function increment_if_positive(integer) does not exist
```

最小の実装を行う

```psql
-- test_increment.sql の BEGIN; 直後に以下を挿入
CREATE OR REPLACE FUNCTION increment_if_positive(val INT)
RETURNS INT AS $$
BEGIN
    IF val > 0 THEN
        RETURN val + 1;
    ELSE
        RETURN val;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

テスト再実行

```terminal
$ psql -d your_database -f test_increment.sql

ok 1 - 正の数の場合は+1されて返ること
ok 2 - 負の数の場合はそのまま返ること
# Green
```

リファクタリング

```psql
-- リファクタリング後の関数定義
CREATE OR REPLACE FUNCTION increment_if_positive(val INT)
RETURNS INT AS $$
BEGIN
    RETURN CASE WHEN val > 0 THEN val + 1 ELSE val END;
END;
$$ LANGUAGE plpgsql;
```

最終確認

```terminal
$ psql -d your_database -f test_increment.sql
# Green
```

### 簡易デバッグ

RAISE NOTE
: 標準出力へ出力する

`RAISE NOTE` をデバッグプリントの変わりに使う。

```psql
study=# DO $$
study$# DECLARE
study$#   v_user_name TEXT := '青木';
study$#   v_user_id INT := 999;
study$# BEGIN
study$#   -- メッセージを出力
study$#   RAISE NOTICE '処理を開始...';
study$#
study$#   -- 変数の値を埋め込んで出力
study$#   RAISE NOTICE 'ユーザー名: %, 名前: %', v_user_id, v_user_name;
study$# END $$;
NOTICE:  処理を開始...
NOTICE:  ユーザー名: 999, 名前: 青木
DO
```

`DO` が出力さえる理由は仕様だからです。 `DO $$` で開始された手続き型言語がエラーなく終了したので、出力された。

```psql
study=# CREATE OR REPLACE FUNCTION func2_4()
study-# RETURNS void AS $$
study$# BEGIN
study$#   RAISE NOTICE '吾輩の名前は%である。', 'ポチ';
study$#   RETURN;
study$# END;
study$# $$ LANGUAGE plpgsql;
CREATE FUNCTION
study=# SELECT func2_4();
NOTICE:  吾輩の名前はポチである。
 func2_4
---------

-- 関数の大文字小文字の区別はない
study=# SELECT FUNC2_4();
NOTICE:  吾輩の名前はポチである。
 func2_4
---------
```

`\i` : ファイルに保存したsqlを登録する

```psql
study-# \i sql/func2_4.sql
CREATE FUNCTION -- 作成に成功するとメッセージが表示される
```

登録した関数を利用する

```psql
study=# SELECT func2_4();
NOTICE:  吾輩の名前はポチである。
 func2_4
---------
```

### 変数の宣言と代入

```psql
CREATE OR REPLACE FUNCTION func3_1()
RETURNS INTEGER AS $$
  DECLARE
    var integer; -- 宣言部: 変数varをinteger型で宣言
    BEGIN
    var := 10; -- 実行部: 10を代入
    RETURN var;
  END;
$$ LANGUAGE plpgsql;
```

`NOT NULL` の利用

```psql
CREATE OR REPLACE FUNCTION func3_2()
RETURNS INTEGER AS $$
  DECLARE
    -- NOT NULL の場合は宣言と同時に値を代入する
    var INTEGER NOT NULL DEFAULT 2;
    var2 INTEGER NOT NULL := 8;
  BEGIN
    RETURN var + var2;
  END;
$$ LANGUAGE plpgsql;
```

引数を受け取る

```psql
CREATE OR REPLACE FUNCTION func3_3(var INTEGER)
RETURNS INTEGER AS $$
  BEGIN
    RETURN var;
  END;
$$ LANGUAGE plpgsql;

study=# \i ./sql/func3_3.sql
study=# SELECT func3_3(9);
 func3_3
---------
       9
```

```psql
CREATE OR REPLACE FUNCTION func3_4(var INTEGER)
RETURNS INTEGER AS $$
  DECLARE
    var ALIAS FOR $1; -- 1番めの引数を var として別名利用
  BEGIN
    RETURN var;
  END;
$$ LANGUAGE plpgsql;

-- 型違反の引数を渡す
study=# SELECT func3_4(10.25);
ERROR:  function func3_4(numeric) does not exist
LINE 1: SELECT func3_4(10.25);
               ^
HINT:  No function matches the given name and argument types. You might need to add explicit type casts.
```

### データ型

- スカラ型: PostgresSQLで用意されている型(文字列、数値、日付など)
- レコード型: レコードを保持する型
- カーソル型: カーソルを用いて1レコード毎に読み取る

`%TYPE`
: 指定したテーブルの列のデータ型を用いる。列のデータ型が変更されても変数の型が自動で従う。

```psql
-- テーブル定義
CREATE TABLE TYPE_SAMPLE(
    user_id: NUMERIC;
    user_name: TEXT,
    PRIMARY KEY (user_id)
);

-- テーブルからデータを取得して変数に代入
CREATE OR REPLACE FUNCTION func3_6()
RETURNS TEXT AS $$
  DECLARE
   -- TYPE_SAMPLEのuser_name列と同じデータ型に従う
    name TYPE_SAMPLE.user_name%TYPE;
  BEGIN
    name := 'sample text';
    RETURN name;
  END;
$$ LANGUAGE plpgsql;
```

`ROWTYPE`
: テーブルから最初の1レコードを保持する

```sql
-- 引数をidとする
-- テーブルを検索し、user_nameを取得する
CREATE OR REPLACE FUNCTION func3_7(NUMERIC)
RETURNS TEXT AS $$
    DECLARE
        id ALIAS FOR $1;
        -- id=$1 該当行の最初の1レコードを変数 `sample_row` に代入
        sample_row TYPE_SAMPLE%ROWTYPE;
    BEGIN
        SELECT * INTO sample_row FROM TYPE_SAMPLE WHERE user_id = id;
        RETURN sample_row.user_name;
    END;
$$ LANGUAGE plpgsql;
```

### 定数

`CONSTANT` で定数を定義する。

```psql
CREATE OR REPLACE FUNCTION func3_8(INTEGER)
RETURNS INTEGER AS $$
    DECLARE
        num ALIAS FOR $1;
        const_num CONSTANT INTEGER := 5; -- 定数を定義
        rtn INTEGER;
    BEGIN
        rtn := num * const_num;
        RETURN rtn;
    END;
$$ LANGUAGE plpgsql;

-- 関数登録
study=# \i ./sql/func3_8.sql
CREATE FUNCTION
-- 関数実行
study=# SELECT func3_8(10);
 func3_8
---------
      50
```

`func3_8` の現代的な記述に書き換える。 パラメータに名前を付け(名前付き引数)、 `num ALIAS FOR $1;` を置き換える。

```psql
-- 名前付き引数として定義
CREATE OR REPLACE FUNCTION func3_8(num INTEGER) -- 引数に直接名前をつける
RETURNS INTEGER AS $$
    DECLARE
        const_num CONSTANT INTEGER := 5;
        rtn INTEGER;
    BEGIN
        rtn := num * const_num;
        RETURN rtn;
    END;
$$ LANGUAGE plpgsql;
```

## 制御構造
