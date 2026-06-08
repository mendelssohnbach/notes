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
