---
tags:
  - Biome
---

## VSCode

`.vscode/settings.json`

```json
{
  // 1. すべての言語のデフォルトフォーマッターをBiomeに設定
  "editor.defaultFormatter": "biomejs.biome",

  // 2. ファイル保存時に自動フォーマットを実行
  "editor.formatOnSave": true,

  // 3. ファイル保存時にコードのアクション（インポート整理やクイックフィックス）を自動実行
  "editor.codeActionsOnSave": {
    "source.organizeImports.biome": "always", // インポート文の自動整理
    "source.fixAll.biome": "always" // 修正可能なLintエラーの自動修正
  }
}
```

## package.json

`package.json`

```json
{
  "scripts": {
    "check": "biome check",
    "fix": "biome check --write",
    "format": "biome format --write"
  }
}
```
