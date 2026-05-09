---
tags:
  - ESLint
---

# プラグイン

[eslint-plugin-import](https://github.com/import-js/eslint-plugin-import)
[import/no-restricted-paths](https://github.com/import-js/eslint-plugin-import/blob/main/docs/rules/no-restricted-paths.md)

- 特定のフォルダにインポートできるファイルを制限する。
- `target` （import先）から `from` （import元）への `import` を禁止する。

## インストール

```
$ npm install eslint-plugin-import --save-dev
```

## 機能

| フィールド | 説明                                                                                                                                                                                                                   |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| from       | 制限を設けたいソースの場所（ディレクトリまたはファイル）を指定します。これにより、特定の層からの不適切なimportを監視できます                                                                                           |
| target     | fromで指定されたソースからimportされるべきでないターゲットの場所を定義します。たとえば、ドメイン層のファイルがインフラストラクチャ層のファイルをimportしてはならない場合、ドメイン層のファイルをtargetとして指定します |
| message    | ルール違反時に表示されるエラーメッセージをカスタマイズします。このメッセージは開発者に対して問題点を具体的に説明し、アーキテクチャの設計原則を遵守するよう促します                                                     |

## 設定例

```js
// eslint.config.mjs
import eslint from '@eslint/js';
import * as importPlugin from 'eslint-plugin-import';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  // 基本的な推奨設定を適用
  eslint.configs.recommended,
  // TypeScript推奨設定（パーサーとプラグインが自動で設定される）
  ...tseslint.configs.recommended,

  {
    // TypeScriptファイルの設定
    files: ['**/*.ts'],
    plugins: {
      import: importPlugin,
    },
    settings: {
      'import/resolver': {
        typescript: {},
      },
    },
    rules: {
      // アンダースコアで始まる変数を未使用エラーから除外する設定
      '@typescript-eslint/no-unused-vars': [
        'error',
        {
          varsIgnorePattern: '^_', // アンダースコアで始まる変数を無視
          argsIgnorePattern: '^_', // アンダースコアで始まる引数を無視
          caughtErrorsIgnorePattern: '^_', // アンダースコアで始まるエラー変数を無視
        },
      ],

      'import/no-restricted-paths': [
        'error',
        {
          zones: [
            // Domain層が依存してはいけない領域
            //  ドメインのビジネスロジックの純粋性を保つため、外側の層への依存を禁止
            {
              from: './src/Application/**/*',
              target: './src/Domain/**/!(*.spec.ts|*.test.ts)',
              message:
                'Domain層でApplication層をimportしてはいけません。ドメインロジックはアプリケーションの詳細から独立している必要があります。',
            },
            {
              from: './src/Presentation/**/*',
              target: './src/Domain/**/!(*.spec.ts|*.test.ts)',
              message:
                'Domain層でPresentation層をimportしてはいけません。ドメインロジックはUI実装の詳細から独立している必要があります。',
            },
            {
              from: './src/Infrastructure/**/*!(test).ts',
              target: './src/Domain/**/!(*.spec.ts|*.test.ts)',
              message:
                'Domain層でInfrastructure層をimportしてはいけません。ドメインロジックはデータベースやフレームワークの技術的詳細から独立している必要があります。',
            },

            // Application層が依存してはいけない領域
            // アプリケーションの調整ロジックがプレゼンテーション層やインフラストラクチャ層の実装詳細に依存しないようにする
            {
              from: '/src/Presentation/**/*',
              target: '/src/Application/**/!(*.spec.ts|*.test.ts)',
              message:
                'Application層でPresentation層をimportしてはいけません。ユースケースはUI実装から独立して定義される必要があります。',
            },
            {
              from: './src/Infrastructure/**/*',
              target: './src/Application/**/!(*.spec.ts|*.test.ts)',
              message:
                'Application層でInfrastructure層を直接importしてはいけません。インターフェースを通じて依存性を逆転してください。',
            },
          ],
        },
      ],
    },
  }
);
```
