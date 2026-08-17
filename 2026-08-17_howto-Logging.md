---
tags:
  - Dev
---

## LogTape

インストール

```terminal
$ npm add @logtape/logtape
```

使い方: `index.js`

```js
import { configure, getConsoleSink, getLogger } from '@logtape/logtape';

// 🎨 ログレベルごとのANSIカラーマッピング
const colors = {
  info: '\x1b[32m', // 緑
  warning: '\x1b[33m', // 黄
  error: '\x1b[31m', // 赤
  reset: '\x1b[0m', // リセット
};

await configure({
  sinks: {
    // 🎨 コンソール用（自作フォーマッタで全体にANSIカラーを適用）
    console: getConsoleSink({
      formatter: (record) => {
        const time = new Date(record.timestamp).toISOString();
        const level = record.level.toUpperCase().padEnd(5);
        const category = `[${record.category.join(' · ')}]`;

        // 💡 構造化ロギング対応：メッセージ配列を文字列に結合する
        const message = record.message.join('');

        // 🎨 ログ全体の文字列を構築
        const rawLog = `${time} ${level} ${category} ${message}`;

        // 🎨 オブジェクトリテラルを用いた色分け処理
        const color = colors[record.level];

        // 色が存在すれば挟んで返し、なければそのままの文字列を返す
        return color ? `${color}${rawLog}${colors.reset}` : rawLog;
      },
    }),
  },

  loggers: [
    // 🌟 sinks に 'console' のみを指定
    { category: 'my-app', lowestLevel: 'debug', sinks: ['console'] },
    { category: ['logtape', 'meta'], lowestLevel: 'warning', sinks: ['console'] },
  ],
});

// 💡 変数をログに埋め込む（構造化ロギング）
const message = 'My message!';
const logger = getLogger(['my-app']);

logger.info`Hello World! Message is: ${message}`;
```
