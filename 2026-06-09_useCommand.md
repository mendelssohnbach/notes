---
tags:
  - Ubuntu
---

# ハードウェア監視

監視項目を CSVファイルに記録する。

```terminal
# -u: cpu使用率 -r: メモリ使用率 -d: ディスクI/O
# プロセス毎に5秒おきに120回、計10分間記録する
$ pidstat -u -r -d 5 120 > system_log.csv
```

```terminal
# -t: タイムスタンプ
# システム全体を5秒おきに120回、計10分間記録する
$ vmstat -t 5 120 > vmstat_log.csv
```

## 複数ファイルの作成

`{}` で囲み、必要なファイル名を `,` で列挙。スペースは不要

```terminal
$ touch routes/{index.js,booksRouter.js}
```

## treeコマンド

ディレクトリ内容を表示する際に無視するディレクトリ/ファイル名を指定

`-I` : 無視するディレクトリ/ファイル名

```terminal
$ tree . -L 2 -I <無視するディレクトリ名> ignore
$ tree . -L 2 -I node_modules ignore
```

## SESSION_SECRET

セッションシークレットキーの作成

```terminal
$ openssl rand -hex 32
```
