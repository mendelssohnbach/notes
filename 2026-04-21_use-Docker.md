---
tags:
  - Docker
---

Dockerのインストール

```terminal
# Ubuntu専用
# 古いバージョンのDockerインストールをすべて削除する
$ sudo apt-get remove docker docker-engine docker.io containerd runc 2>/dev/null

# Docker公式のGPG鍵とリポジトリを追加する
$ sudo apt-get update
$ sudo install -m 0755 -d /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ sudo chmod a+r /etc/apt/keyrings/docker.gpg

$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Docker Engine、CLI、およびプラグインをインストールする
$ sudo apt-get update
$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# 実行ユーザーをdockerグループに追加する（sudoを不要にするため）
$ sudo usermod -aG docker $USER
$ newgrp docker
# 再起動する

# dockerが存在するか確認する
$ groups
... docker

# インストールを確認する
$ docker --version
$ docker compose version

# sudoコマンドなしでの起動を確認する
$ docker ps
```

Docker Desktopのバージョンアップ

```terminal
$ sudo apt-get install -y ~/Downloads/docker-desktop-amd64.deb
```

## Docker Builds の消去

```terminal
# 現在のビルド履歴を一覧表示
$ docker buildx history ls
# 特定のビルドを削除
$ docker buildx history rm <完全なBUILD ID>
# 削除できたか確認
$ docker buildx history ls
```

## リビルド

```terminal
$ docker compose down
# 最新状態の強制取得/過去のビルドデータを利用しない
$ docker compose build --no-cache
$ docker compose up -d


```

## ロケールの確認

```terminal
# 現在のロケールを確認
$ docker compose exec ubuntu-node locale
```

## インストールパッケージの一覧

**npm** の場合

```terminal
$ docker exec -it ubuntu_node_dev npm list --depth=0
# 親子関係を含めてパッケージの一覧を表示
$ docker exec -it ubuntu_node_dev pipdeptree

# Python pip パッケージの一覧を表示
$ docker exec -it ubuntu_node_dev pip list
```
