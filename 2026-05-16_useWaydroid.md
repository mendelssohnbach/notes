---
tags:
  - Android
---

## Waydroid

ウィンドウサイズを指定

```terminal
# 幅を指定
$ waydroid prop set persist.waydroid.width 810
# 高さを指定
w$ waydroid prop set persist.waydroid.height 1296
# コンテナの再起動
$ sudo systemctl restart waydroid-container
```

デフォルトに戻す

```terminal
$ waydroid prop set persist.waydroid.width ""
$ waydroid prop set persist.waydroid.height ""
$ sudo systemctl restart waydroid-container
```

ウィンドウの移動

`Super` キー + `マウスドラッグ`

ナイトモードのON/OFF

Waydroidを起動した後

```terminal
$ udo waydroid shell

# Nightモード無効化
:/ # cmd uimode night no

# Night有効化
cmd uimode night yes

:/ # exit
# アプリを再起動
```
