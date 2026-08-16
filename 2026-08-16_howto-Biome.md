---
tags:
  - Biome
---

## 準備

インストール

```terminal
$ npm install --save-dev --save-exact @biomejs/biome
```

## package.json

`package.json`

```json
  "scripts": {
    "check": "biome check --write .",
    "format": "biome format --write .",
    "lint": "biome lint --write .",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

## Biome

biome.json

```json
{
  "$schema": "https://biomejs.dev/schemas/2.5.8/schema.json",
  "vcs": {
    "enabled": false,
    "clientKind": "git",
    "useIgnoreFile": false
  },
  "files": {
    "ignoreUnknown": true
  },
  "formatter": {
    "enabled": true,
    "formatWithErrors": false,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineEnding": "lf",
    "lineWidth": 100,
    "attributePosition": "multiline",
    "useEditorconfig": true
  },
  "linter": {
    "enabled": true,
    "rules": {
      "preset": "recommended"
    }
  },
  "javascript": {
    "formatter": {
      "arrowParentheses": "always",
      "bracketSameLine": false,
      "bracketSpacing": true,
      "jsxQuoteStyle": "double",
      "quoteProperties": "asNeeded",
      "quoteStyle": "single",
      "semicolons": "always",
      "trailingCommas": "all",
      "attributePosition": "multiline"
    }
  },
  "html": {
    "formatter": {
      "indentScriptAndStyle": false,
      "selfCloseVoidElements": "always"
    }
  },
  "css": {
    "formatter": {
      "enabled": true,
      "indentStyle": "space",
      "indentWidth": 2,
      "lineWidth": 100
    },
    "linter": {
      "enabled": true
    },
    "parser": {
      "allowWrongLineComments": false
    }
  },
  "assist": {
    "enabled": true,
    "actions": {
      "source": {
        "organizeImports": "on"
      }
    }
  }
}
```

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
