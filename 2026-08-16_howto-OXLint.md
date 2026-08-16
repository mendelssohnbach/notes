---
tags:
  - OXLint
---

## VSCode

[拡張機能](https://marketplace.visualstudio.com/items?itemName=oxc.oxc-vscode)

`.vscode/settings.json`

```json
{
  // 1. デフォルトのフォーマッタをOxcに指定
  "editor.defaultFormatter": "oxc.oxc-vscode",
  "editor.formatOnSave": true,

  // 2. 保存時に lint エラーの自動修正とフォーマットを実行
  "editor.codeActionsOnSave": {
    "source.fixAll.oxc": "always"
  },

  // 3. package.json の scripts と同じ挙動（Type-Aware / tsconfig 連携）をエディタ側にも適用
  "oxc.tsConfigPath": "./tsconfig.json"
}
```

## package.json

`package.json`

```json
{
  "scripts": {
    "lint": "oxlint --tsconfig ./tsconfig.json --type-aware ./src",
    "format": "oxfmt --write .",
    "typecheck": "tsgo --noEmit"
  }
}
```

`.oxlintrc.json`

```json
{
  "$schema": "https://docs.oxc.rs/schemas/oxlintrc.json",
  "plugins": ["typescript", "oxc"],
  "rules": {
    "eqeqeq": ["error", "always", { "null": "ignore" }],
    "no-console": ["error", { "allow": ["info", "warn", "error"] }],
    "no-debugger": "error",
    "no-unused-vars": [
      "warn",
      {
        "argsIgnorePattern": "^_",
        "varsIgnorePattern": "^_",
        "caughtErrorsIgnorePattern": "^_"
      }
    ],
    "typescript/no-floating-promises": "error",
    "typescript/no-namespace": "off",
    "import/no-cycle": "error",
    "prefer-template": "error",
    "no-template-curly-in-string": "error"
  }
}
```

`.oxfmtrc.json`

```json
{
  "$schema": "./node_modules/oxfmt/configuration_schema.json",
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "endOfLine": "lf",
  "experimentalSortImports": {
    "groups": [
      ["side-effect"],
      ["builtin"],
      ["external"],
      ["internal"],
      ["parent", "sibling", "index"]
    ],
    "sortSideEffects": false,
    "newlinesBetween": false,
    "ignoreCase": true
  }
}
```
