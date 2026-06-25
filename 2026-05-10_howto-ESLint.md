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

# Reactでの利用

## インストールコマンド

```terminal
$ npm install --save-dev \
  eslint \
  @eslint/js \
  globals \
  typescript-eslint \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-plugin-react-refresh \
  prettier \
  eslint-config-prettier \
  cspell
```

## 設定ファイル

`eslint.config.js` の編集

```js
// =============================================================================
// eslint.config.js
//
// 【ESLint とは？】
//   コードの「書き方のミス」や「バグになりやすいパターン」を
//   自動で検出してくれるツールです。
//   例：console.log を書きっぱなしにしている、変数を使っていない、など。
//
// 【このファイルの役割】
//   ESLint に「どんなルールでコードをチェックするか」を教える設定ファイルです。
//   配列の中に設定オブジェクトを並べて書き、
//   ⚠️ 下に書いたものが上の設定を上書きします（順番が重要！）
// =============================================================================

// -----------------------------------------------------------------------------
// 外部パッケージの読み込み
// -----------------------------------------------------------------------------

// JavaScript の基本的な推奨ルールセット
import js from '@eslint/js';

// ブラウザ・Node.js などの「グローバル変数」の定義集
// 例：ブラウザなら window, document など
import globals from 'globals';

// TypeScript 用の ESLint ルールセット
import tseslint from 'typescript-eslint';

// Prettier（コード整形ツール）と ESLint のルールが衝突しないようにするパッケージ
// 例：Prettier はセミコロンを自動で付けるが、ESLint も同じルールを持つ場合に競合する
// → このパッケージが ESLint 側の競合ルールをすべて無効化してくれる
import eslintConfigPrettier from 'eslint-config-prettier';

// React のコードに特化したルールセット
// 例：JSX の書き方、React 特有の構文ミスを検出する
import react from 'eslint-plugin-react';

// React Hooks（useState, useEffect など）の使い方を検証するルールセット
// 例：useEffect の依存配列が正しく書かれているかチェックする
import reactHooks from 'eslint-plugin-react-hooks';

// Vite の HMR（ホットリロード）が正しく動くかチェックするルールセット
// HMR とは：コードを保存したときにブラウザを自動で更新する仕組み
import reactRefresh from 'eslint-plugin-react-refresh';

// =============================================================================
// ESLint の設定（配列形式で書く）
// 上から順に適用され、後に書いた設定が優先されます
// =============================================================================
export default [
  // ---------------------------------------------------------------------------
  // 1. チェック対象外にするフォルダの指定
  //
  //    dist/     : ビルド後の成果物が入るフォルダ（自分が書いたコードではないためスキップ）
  //    coverage/ : テストのカバレッジレポートが入るフォルダ（同上）
  // ---------------------------------------------------------------------------
  { ignores: ['dist/**', 'coverage/**'] },

  // ---------------------------------------------------------------------------
  // 2. TypeScript 推奨ルールの適用
  //
  //    「...」（スプレッド構文）で配列を展開しています。
  //    tseslint.configs.recommended には複数の設定オブジェクトが入っているため、
  //    配列の要素として展開して並べる必要があります。
  //
  //    主な内容：
  //    - TypeScript の型エラーになりやすいコードを検出
  //    - any 型の多用を警告
  //    - 未使用の変数を検出　など
  // ---------------------------------------------------------------------------
  ...tseslint.configs.recommended,

  // ---------------------------------------------------------------------------
  // 3. プロジェクト全体に適用するメインの設定ブロック
  // ---------------------------------------------------------------------------
  {
    // チェック対象にするファイルの拡張子
    // .js  : 通常の JavaScript
    // .jsx : JSX を含む JavaScript（React コンポーネントなど）
    // .ts  : TypeScript
    // .tsx : JSX を含む TypeScript（React + TypeScript のコンポーネントなど）
    files: ['**/*.{js,jsx,ts,tsx}'],

    // -------------------------------------------------------------------------
    // 言語・環境の設定
    // -------------------------------------------------------------------------
    languageOptions: {
      // 使用する JavaScript のバージョン（ES2020 = ES11）
      // 2020年以降に追加された構文（オプショナルチェーン ?. など）を使えるようにする
      ecmaVersion: 2020,

      // 実行環境のグローバル変数を定義
      // globals.browser を指定すると window, document, fetch などが使えるようになる
      // ※ globals.node にすると Node.js 用（process, require など）になる
      globals: globals.browser,

      parserOptions: {
        // JSX 構文（<Component /> のような書き方）を解析できるようにする
        ecmaFeatures: { jsx: true },
      },
    },

    // -------------------------------------------------------------------------
    // 追加プラグインの登録
    //
    // ESLint 本体にない追加ルールを使うための「拡張機能」です。
    // ここで登録するだけではルールは有効になりません。
    // 下の rules セクションで個別に設定する必要があります。
    // -------------------------------------------------------------------------
    plugins: {
      react, // "react/xxx" というルール名で使えるようになる
      'react-hooks': reactHooks, // "react-hooks/xxx" というルール名で使えるようになる
      'react-refresh': reactRefresh, // "react-refresh/xxx" というルール名で使えるようになる
    },

    // -------------------------------------------------------------------------
    // ルールの設定
    //
    // 各ルールには3段階の設定値があります：
    //   "off"   : ルールを無効にする
    //   "warn"  : 警告を表示するが、ビルドは止まらない（黄色い⚠️マーク）
    //   "error" : エラーを表示し、ESLint が失敗扱いになる（赤い❌マーク）
    // -------------------------------------------------------------------------
    rules: {
      // -----------------------------------------------------------------------
      // 推奨ルールの展開
      // -----------------------------------------------------------------------

      // JavaScript の基本的な推奨ルールをすべて適用する
      // 例：変数の未使用、== の代わりに === を使う、など
      ...js.configs.recommended.rules,

      // React Hooks の推奨ルールをすべて適用する
      // 例：useEffect の第2引数（依存配列）に必要な変数が抜けていないかチェック
      ...reactHooks.configs.recommended.rules,

      // -----------------------------------------------------------------------
      // React Refresh（Vite のホットリロード）のルール
      // -----------------------------------------------------------------------

      // コンポーネントのみをエクスポートするファイルかチェックする
      // Vite のホットリロードは、コンポーネントだけをエクスポートするファイルで
      // 正しく動作するため、このルールで検知する
      //
      // allowConstantExport: true
      //   → export const App = () => {} のような「定数エクスポート」も許可する
      'react-refresh/only-export-components': ['warn', { allowConstantExport: true }],

      // -----------------------------------------------------------------------
      // コーディングスタイルのルール
      // -----------------------------------------------------------------------

      // console.log などのデバッグ用コードを書きっぱなしにしないよう警告する
      // 本番コードに console.log が残っていると情報漏えいや動作の遅延につながることがある
      'no-console': 'warn',

      // コールバック関数はアロー関数で書くことを強制する
      // ❌ NG: setTimeout(function() { ... })
      // ✅ OK: setTimeout(() => { ... })
      'prefer-arrow-callback': 'error',

      // 関数は「関数式（変数に代入する形）」で書くことを強制する
      // allowArrowFunctions: true → アロー関数も関数式として許可する
      //
      // ❌ NG: function greet() { return 'hello'; }  （関数宣言）
      // ✅ OK: const greet = () => 'hello';           （アロー関数式）
      'func-style': ['error', 'expression', { allowArrowFunctions: true }],

      // -----------------------------------------------------------------------
      // コーディングエラー対策のルール（React 特有のミスを検知）
      // -----------------------------------------------------------------------

      // JSX で class 属性を使ったときにエラーにする
      // HTML では class="..." と書くが、JSX では className="..." と書く必要がある
      // この違いを自動で検知してくれる
      //
      // ❌ NG: <div class="container">
      // ✅ OK: <div className="container">
      //
      // ⚠️ ignore を設定すると検知できなくなるため設定しない
      'react/no-unknown-property': 'error',

      // リストを表示する際に key 属性が書かれているかチェックする
      // React でリスト（配列）を表示するとき、各要素に一意の key が必要
      // key がないと画面の再描画が非効率になったり、表示がおかしくなったりする
      //
      // ❌ NG:
      //   items.map(item => <li>{item}</li>)
      //
      // ✅ OK:
      //   items.map(item => <li key={item.id}>{item}</li>)
      'react/jsx-key': [
        'error',
        {
          // <>...</> の短縮構文（フラグメント）の中でも key のチェックをする
          checkFragmentShorthand: true,
          // {...item} のようなスプレッド展開をしているときも key のチェックをする
          checkKeyMustBeforeSpread: true,
          // 同じ key が重複して使われていたら警告する
          warnOnDuplicates: true,
        },
      ],
    },
  },

  // ---------------------------------------------------------------------------
  // 4. Prettier との競合ルールを無効化（⚠️ 必ず最後に書く）
  //
  //    Prettier はコードを自動整形するツールで、
  //    ESLint の一部のルールと同じ領域（インデント、セミコロンなど）を扱います。
  //    両方が有効だと「ESLint がエラーにする → Prettier が直す → ESLint がエラーにする」
  //    という無限ループが起きてしまいます。
  //
  //    eslint-config-prettier を最後に置くことで、
  //    Prettier と重複する ESLint のルールだけを無効化します。
  //    ※ これより上で設定したルールの一部が上書きされるため、必ず最後に書くこと
  // ---------------------------------------------------------------------------
  eslintConfigPrettier,
];
```

## スクリプトの準備

`package.json` の `scripts` に追加

```json
{
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "check:type": "tsc --noEmit",
    "check:lint": "eslint . --cache",
    "fix:lint": "pnpm check:lint --fix",
    "check:format": "prettier --check --cache \"./**/*.{ts,tsx}\"",
    "fix:format": "prettier --write --cache \"./**/*.{ts,tsx}\"",
    "check:spell": "cspell \"**/*\" --cache --gitignore"
  }
}
```
