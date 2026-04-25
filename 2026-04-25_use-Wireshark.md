---
tags:
  - Wireshark
---

## インストール

```terminal
$ sudo apt update
$ sudo apt upgrade -y
# Wireshark（およびTShark）をインストール
$ sudo apt install wireshark -y
# パケットキャプチャ権限の設定
$ sudo dpkg-reconfigure wireshark-common
# Wiresharkグループにユーザーを追加する
sudo usermod -aG wireshark $USER
# ログアウトして再度ログイン
```

## 確認

```terminal
# グループに属しているか確認
$ groups | grep wireshark
... wireshark

# パケットキャプチャ機能が備わっていることを確認
$ getcap /usr/bin/dumpcap
usr/bin/dumpcap cap_net_admin,cap_net_raw=eip

# バージョン確認
$ wireshark --version | head -n 1
Wireshark 4.2.2 (Git v4.2.2 packaged as 4.2.2-1.1build3).
```
