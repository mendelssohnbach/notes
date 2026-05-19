---
tags:
  - Wayland
---

# 環境の判定

Wayland環境かX11環境かを判定する

```terminal
# Wayland環境では WAYLAND_DISPLAY が設定される
$ echo $WAYLAND_DISPLAY
wayland-0

# XWayland経由のアプリでは $DISPLAY が設定される
$ echo $DISPLAY
:0
```

# クリップボードツール

```terminal
# Wayland用のクリップボードツールをインストール
$ sudo apt install wl-clipboard -y

# クリップボードへのコピー（Wayland）
$ echo "hello world" | wl-copy

# クリップボードからのペースト（Wayland）
$ wl-paste
```
