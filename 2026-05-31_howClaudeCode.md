---
tags:
  - Claude
---

# Claude Code

解説サイト
[Claude Code入りのDockerイメージをDevContainerで動かす](https://qiita.com/sour23/items/a86a42a675e27853299b)

## Dockerイメージの作成

**Claude Code** 入りの **Docker** イメージを作成する。

適当な空ディレクトリをを作成し、 `Dockerfile` を作成する。

```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu24.04

USER vscode
RUN curl -fsSL https://claude.ai/install.sh | bash

ENV PATH=/home/vscode/.local/bin:$PATH
```

## ビルド

```terminal
$ docker build -t my-claudecode-devcontainer:local .
```

## 確認

```terminal
$ ocker images my-claudecode-devcontainer
REPOSITORY                   TAG       IMAGE ID       CREATED         SIZE
my-claudecode-devcontainer   local     0efb73020702   5 minutes ago   1.37GB
```

## イメージの利用

プロジェクトディレクトリにて `.devcontainer/devcontainer.json` を作成する。

```json
{
  "image": "my-claudecode-devcontainer:local",
  "remoteUser": "vscode",
  "containerUser": "vscode"
}
```

## ビルド済みイメージ

毎日3時に自動更新される。
[claudecode-devcontainer](https://github.com/fosawa/claudecode-devcontainer)

```json
{
  "image": "ghcr.io/fosawa/claudecode-devcontainer:latest",
  "remoteUser": "vscode",
  "containerUser": "vscode"
}
```

## 課題

`CLAUDE.md` や **Skills** **MCP** などが取り込まれていない。
