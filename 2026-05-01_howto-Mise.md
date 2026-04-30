---
tags:
  - Mise
---

言語とバージョンを指定してグローバルにインストール

```terminal
$ mise use -g node@24.6.0
```

確認

```terminal
$ node -v
v24.6.0
```

プロジェクトに適用

```terminal
$ mise use node@24.6.0
$ cat mise.toml
[tools]
node = "24.6.0"
```

管理下のリストを表示

```terminal
$ mise list | awk '{print $1, $2}'
go 1.25.1
node 22.16.0
node 24.2.0
node 24.6.0
pnpm 10.12.2
pnpm 10.14.0
rust 1.89.0
usage 2.1.1
```

不要なバージョンを削除

```terminal
$ mise uninstall node@22.17.0
```
