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
