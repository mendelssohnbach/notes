---
tags:
  - Ubuntu
---

# NTPサーバーをNICTに設定

## 設定ファイルのバックアップ

```terminal
$ sudo cp /etc/systemd/timesyncd.conf /etc/systemd/timesyncd.conf.bak

# 確認
$ ls -la /etc/systemd/timesyncd.conf.bak
-rw-r--r-- 1 root root 1003 5月 22 18:36

```

## 設定ファイルの編集

```terminal
$ sudo nano /etc/systemd/timesyncd.conf
```

```txt
# NTPサーバーの指定
[Time]
NTP=ntp.nict.jp
FallbackNTP=ntp.ubuntu.com 0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org 3.pool.ntp.org

# 確認
$ tail -n 5 /etc/systemd/timesyncd.conf
#ConnectionRetrySec=30
#SaveIntervalSec=60
NTP=ntp.nict.jp
FallbackNTP=ntp.ubuntu.com 0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org 3.pool.ntp.org
```

## サービスの再起動と適用

```terminal
$ sudo systemctl restart systemd-timesyncd
```

## 同期状態の確認

```terminal
# 全体の有効化状態をチェック
$ timedatectl
               Local time: 金 2026-05-22 18:41:07 JST
           Universal time: 金 2026-05-22 09:41:07 UTC
                 RTC time: 金 2026-05-22 09:41:07
                Time zone: Asia/Tokyo (JST, +0900)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

# 同期の品質・詳細な通信状態の確認
$ timedatectl timesync-status
       Server: 61.205.120.130 (ntp.nict.jp)
Poll interval: 1min 4s (min: 32s; max 34min 8s)
         Leap: normal
      Version: 4
      Stratum: 1
    Reference: NICT
    Precision: 1us (-20)
Root distance: 0 (max: 5s)
       Offset: -42.646ms
        Delay: 9.483ms
       Jitter: 0
 Packet count: 1
    Frequency: -321.078ppm
```
