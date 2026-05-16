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

`Super` キー + `マウスドラッグ` : ウィンドウの移動
