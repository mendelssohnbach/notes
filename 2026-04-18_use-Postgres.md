---
tags:
  - DB
---

バージョン確認

```terminal
$ psql -V
psql (PostgreSQL) 18.3 (Ubuntu 18.3-1.pgdg24.04+1)
```

CLIログイン

```terminal
$ psql -h db -U postgres -d study
Password for user postgres:
study=#
```

![接続する](image.png)

![高度な接続設定 ポート](image-1.png)

![高度な接続設定 SSL](image-2.png)

ユーザー表示

```
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

単一データベースのバックアップ

```terminal
$ pg_dump -h db -U postgres -d study -F c -f ./export/dump_db.dump
Password:
```

テーブルを削除し、 **dump** よりリストアを試みたが操作がわからない。
