---
tags:
  - JavaScript
---

# Node.jsプロジェクト

[書籍](https://www.oreilly.co.jp/books/9784814401673/)
[リポジトリ](https://github.com/oreilly-japan/nodejs-projects-ja)
[写経リポジトリ](https://github.com/mendelssohnbach/nodejs-projects-ja)

## Tips

`npm init`
: Node.js プロジェクトの初期化。 `type` は `module` とする

```json
  "type": "module"
}
```

標準ライブラリの import がコード補完されない場合の対策

```terminal
$ npm install eslint --save-dev
$ npx eslint --init
$ npm install --save-dev @types/node
```

### audit fix

`4 vulnerabilities (2 high, 2 critical)`
: 脆弱性の警告

```terminal
$ npm audit fix
```

**sqlite3** はインストール時にビルド処理が行われる。この処理を許可する

```terminal
$ npm install-scripts approve sqlite3
```

### Scripts

`node --watch`
: ファイルの変更を検知しプロセスを自動起動する

`node --env-file=.env`
: `.env` ファイルを読み込む

`node --env-file-if-exists=.env`
: `.env` ファイルが存在しなくてもエラーを出さずに処理を続行

```json
{
  "scripts": {
    "dev": "node --watch index.js",
    "start": "node --env-file=.env index.js",
    "start:noError": "node --env-file-if-exists=.env index.js"
  }
}
```

### `.env` ファイルの利用例

```env
PORT=3000
DB_HOST=localhost
```

```js
// 追加の require('dotenv') などは不要
const port = process.env.PORT;
const dbHost = process.env.DB_HOST;

console.log(`サーバーはポート ${port} で起動しました。`);
console.log(`データベース接続先: ${dbHost}`);
```

## 2章

Node.js を実行したディレクトリ直下にファイルが作成される。

```terminal
# 下の場合は、 `node_projects` ディレクトリの直下に作成される。
$ /home/jack/WorkSpaces/labs/node_projects
$ node chapter02/2_3/index.js

# 下の場合は、 `2_3` ディレクトリの直下に作成される。
$ /home/jack/WorkSpaces/labs/node_projects/chapter02/2_3
```

```js
// index.js
import { writeText } from './writeText.js';

writeText();

// writeFile.js
import { writeFileSync } from 'fs';

function writeText() {
  const content = 'Test content!';

  try {
    writeFileSync('./test.txt', content);
    console.log('Success!');
  } catch (err) {
    console.error(err);
  }
}

export { writeText };
```

## デバッグ

| ショートカット | 機能                 | 説明                                                           |
| :------------- | :------------------- | :------------------------------------------------------------- |
| F5             | 開始/続行            | デバッグを開始する。停止中に押すと次のブレークポイントまで実行 |
| F9             | ブレークポイント切替 | 現在の行にブレークポイントをON/OFF                             |
| F10            | ステップオーバー     | 1行だけ実行。関数呼び出しがあっても中には入らない              |
| F11            | ステップイン         | 1行実行。関数呼び出しがあれば、その関数の中に入っていく        |
| Shift+F11      | ステップアウト       | 今いる関数の残りを一気に実行し、呼び出し元に戻る               |

## 2章 CLIツール

`.vscode/launch.json` : `integratedTerminal` ターミナルツールのデバッグでの設定

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug index.js",
      "program": "${workspaceFolder}/chapter02/index.js",
      "console": "integratedTerminal"
    }
  ]
}
```

1行のアロー関数の壁数をデバッグする際は、複数行に書き換えることを推奨

```js
# bat debug
const readLineAsync = (message) => new Promise((resolve) => readline.question(message, resolve));

# good debub
const readLineAsync = (message) => {
  console.log('[DEBUG] message = ', message); // ここで中身を確認できます
  return new Promise((resolve) => readline.question(message, resolve));
};
```

[`statSync](https://nodejs.org/api/fs.html#fsstatsyncpath-options)

- 引数 (path): ファイルパスを指定します。文字列（<string>）、バイナリデータ（<Buffer>）、URLオブジェクト（<URL>）のいずれかで指定できます。
- オプション (options): 省略可能なオブジェクトです。
  - bigint: true にすると、ファイルサイズなどの数値を通常のNumberではなく、より大きな桁数を扱える BigInt 型で返してくれます。
  - throwIfNoEntry: false にすると、ファイルが存在しないときにエラー（例外）を投げず、代わりに undefined を返してくれます。
- 戻り値 (Returns): <fs.Stats> という「ファイルの属性情報（メタデータ）が詰まったオブジェクト」を返します。

```js
/** @type {string} CSVファイルの保存パス */
const path = './writeContact.csv';
/** @type {boolean} ファイルが存在し、かつ空ではないかどうかの判定フラグ */
// fs.existsSync(path)
// ファイルまたはフォルダが、指定したパスに存在するかをチェック: true/false
// かつ
// fs.statSync(path).size > 0
// ファイルのサイズが 0 バイトより大きいかをチェック: true/false
// statSync: ファイルのメタ情報を取得
const fileExistsAndNotEmpty = fs.existsSync(path) && fs.statSync(path).size > 0;
```

[`toISOString()`](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)

- `toISOString` は JavaScriptの組込機能。ドキュメントは **MDN** 。
- ISO8601 に基づく簡略化形式
- 文字列長さ: 24文字または27文字
  - 24文字: YYYY-MM-DDTHH:mm:ss.sssZ
  - 27文字: ±YYYYYY-MM-DDTHH:mm:ss.sssZ

### 正規表現

`/\S+@\S+\.\S+/`

| 正規表現 | 意味                                             |
| :------- | :----------------------------------------------- |
| `/.../`  | 正規表現リテラル(正規表現パターンのクォート文字) |
| `\$+`    | 空白以外の文字が1文字以上続く                    |
| `@`      | @リテラル                                        |
| `\.`     | ドットリテラル                                   |

`/^\d+$/`

| 正規表現 | 意味                          |
| :------- | :---------------------------- |
| `^`      | 文字列の先頭                  |
| `d`      | 数字1文字(0-9)                |
| `+`      | 直前の文字の1回以上の繰り返し |
| `$`      | 文字列の末尾                  |

## リファクタリング分析

`promptGet.js`（バリデーション・`Person`データモデル・CSV永続化・CLI入出力・アプリケーションループが1ファイルに同居している状態）

### 判断視点

1. **責務（単一責任）**
   異なる関心事（バリデーション／データモデル／CSV永続化／CLI入出力／アプリケーションフロー）は変更理由が異なる。バリデーションルールの変更とCSV出力形式の変更は無関係なので、責務単位で切るのが素直。

2. **I/O依存 vs 純粋ロジック**
   `emailRegex`/`numberRegex` によるバリデーションは入力さえあればテストできる純粋関数。一方 `prompt`（標準入力）や `csv-writer`（ファイル書き込み）は副作用を伴うI/O層。分離すると純粋ロジック部分が単体テストしやすくなる。

3. **依存の向き**
   ドメイン（`Person`）が特定のI/Oライブラリ（`csv-writer`）を直接知っている状態は結合が強い。永続化ロジックを外に出せば、`Person`はデータ構造に専念でき、保存先の差し替えなどの変更にも強くなる。

## 3章　Webサーバーの作成

![イベントループをブロックする方法](assets/2026-07-13-11-53-11.png)

- 関数コールバッグ: 同時的に実行される。コールバック関数がブロックング要素を含んでいる場合
- 非同期関数: ほとんどの関数は非同期実行。コールバックが登録され完了が待機する場合 -　高負荷タスク: 高負荷計算タスク/データベース呼び出しはワーカスレッドを通じて行われる。イベントループ負担は軽くなる

### セマンティックバージョニング

```json
"dependencies": {
  "fastify": "^5.10.0"
}
```

| 記号      | 意味             | 位置   | 許可されるバージョン範囲   | 許可されない範囲             |
| :-------- | :--------------- | :----- | :------------------------- | :--------------------------- |
| `^5.10.0` | マイナーまで更新 | 真ん中 | 5.10.0 以上 〜 6.0.0 未満  | 6.0.0 以上（破壊的変更あり） |
| `~5.10.0` | パッチだけ更新   | 右端   | 5.10.0 以上 〜 5.11.0 未満 | 5.11.0 以上（機能追加あり）  |

### JavaScriptオブジェクト

JavaScriptオブジェクトの配列リテラルの使い方。JSONオブジェクトとは違う

```js
export default [
  {
    name: 'Broccoli Pie',
    description: 'A green pie with an earthy crust',
    translation: `素朴な風味のクラストを使った、緑色のパイ`,
    cost: 12.99,
  },
  ...,
];
```

### CURLを使った確認

curl コマンドを使った確認: `-G` は GETの意味

```terminal
$ curl -G http://localhost:3000/menu
[{"name":"Broccoli Pie","description":"A green pie with an earthy crust","translation":"素朴な風味のクラストを使った、緑色のパイ","cost":12.99},...]
```

### ESJの利用

- `import ejs from 'ejs'` : EJSテンプレートエンジンのインポート
- `import fastifyView from '@fastify/view'` : Fastify用のviewプラグインをインポート

```js
import fastifyView from '@fastify/view';
import ejs from 'ejs';

...

// EJSをテンプレートエンジンとして設定
app.register(fastifyView, {
  engine: {
    ejs: ejs,
  },
});
```

単純なページを表示する例:

`reply.view` を使って views フォルダー内の index.ejs ページを表示

```js
app.get('/', (req, reply) => {
  reply.view('views/index.ejs', { name: "What's Fare is Fair" });
});
```

データ定義ファイルを指定し、ページを表示する例:

`reply.view` を使って menuItems データ渡して、views フォルダー内の .menu.ejs ページを表示

```js
app.get('/menu', (req, reply) => {
  reply.view('views/menu.ejs', { menuItems });
});
```

ルート定義時にデータを直接定義すると共に、テータファイルを指定し、ページを表示する例:

operatingHours と day のデータを渡して、views フォルダー内の .hours.ejs ページを表示

```js
app.get('/hours', (req, reply) => {
  const days = ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'];
  reply.view('views/hours.ejs', { operatingHours, days });
});
```

各ルートで `async` 関数を利用しない理由

- **EJS** の描画処理自体は非常に高速
- **Fastify** が同期的な返却をサポートしている

### EJSテンプレートエンジン

- `<body>...</body>` : メインコンテンをこの中に書く
- `<%= %>` : HTML内にコンテンツを表示
- `<%= name %>` : レストラン名を表示
- `<% ... { %>...<% }%>` : JavaScriptを実行。　`=` は省略
- `<%# ... %>` : コメント

```ejs
<body>
    <h1>Welcome to <%= name %></h1>
</body>
</html>

<% for (let item of menuItems) { %>
        <li>
            <strong><%= item.name %></strong>
            <span><%= item.description %></span>
            <span><%= item.cost %></span>
        </li>
    <% }%>
```

### 静的アセット配信

- プロジェクトのルートレベルに `public` フォルダーを作成する
- `index.js` に `@fastify/static` をインポート
- ESJファイルから必要静的アセットファイルを使う

## データベース

- SQLデータベース: 固定のスキーマを使用
- NoSQLデータベース: 柔軟なドキュメント構造を許可

| 概念         | SQLデータベース            |
| :----------- | :------------------------- |
| スキーマ     | データベース構造の定義     |
| テーブル     | 構造化されたレコードの集合 |
| コレクション | 該当なし(テーブルを使用)   |
| ドキュメント | 該当なし(レコードを使用)   |

[@seald-io/nedb](https://www.npmjs.com/package/@seald-io/nedb)
: ブラウザ向けの組み込み型永続NoSQLデータベースまたはインメモリデータベース

インストール

```terminal
$ npm install @seald-io/nedb
$ npm install -D typescript @types/node tsx
```

`tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

CRUD操作サンプルコード

```ts
import Datastore from '@seald-io/nedb';

// 1. 扱うデータの型定義 (NeDBが自動付与する _id はオプショナルにしておく)
interface User {
  _id?: string;
  name: string;
  age: number;
  role: 'admin' | 'user'; // リテラル型で制限
}

async function main() {
  // 2. ジェネリクス <User> を指定してデータストアを初期化
  const db = new Datastore<User>({
    filename: './users.db',
    autoload: true,
  });

  console.log('--- 1. Create ---');
  // 型チェックが働くため、タイポや型違いを防げます
  const newUser: User = { name: '山田太郎', age: 28, role: 'admin' };

  // 挿入されたドキュメントには必ず _id が含まれる型として返ってきます
  const doc = await db.insertAsync(newUser);
  console.log('挿入成功、ID:', doc._id);

  console.log('\n--- 2. Read ---');
  // 検索結果も User[] 型として推論されます
  const admins = await db.findAsync({ role: 'admin' });
  admins.forEach((user) => {
    console.log(`${user.name} (${user.age}才)`); // user.name の補完が効きます
  });

  console.log('\n--- 3. Update ---');
  // $set の中身も型安全に検証されます
  await db.updateAsync({ name: '山田太郎' }, { $set: { age: 29 } }, {});

  console.log('\n--- 4. Delete ---');
  await db.removeAsync({ name: '山田太郎' }, {});
}

main().catch(console.error);
```

```terminal
// 実行
$ npx tsx index.ts
```

### MongoDB

`compose.ymal`

```yml
services:
  mongodb:
    image: mongo:latest
    container_name: mongodb_labs
    ports:
      - '27017:27017'
    volumes:
      - mongo_data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: password123

volumes:
  mongo_data:
```

`.dockerignore`

```txt
node_modules
npm-debug.log
.git
.gitignore
.env
.dockerignore
Dockerfile
*.log
```

バックグランド起動と確認

```terminal
$ docker compose up -d
[+] up 14/14
 ✔ Image mongo:latest          Pulled                                                                       23.9s
 ✔ Network chapter04_default   Created                                                                       0.1s
 ✔ Volume chapter04_mongo_data Created                                                                       0.0s
 ✔ Container mongodb_labs      Started

$ docker compose ps
NAME           IMAGE          COMMAND                   SERVICE   CREATED          STATUS          PORTS
mongodb_labs   mongo:latest   "docker-entrypoint.s…"   mongodb   22 seconds ago   Up 20 seconds   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp
2026-07-16-07-09-08.png
```

#### 拡張機能

[MongoDB拡張機能](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)

![MongoDBアイコン](assets/2026-07-16-07-09-08.png)をクリックして操作

1. Connectボタンをクリック
   ![Connect](assets/2026-07-16-06-57-25.png)
2. 画面上部の入力ボックスに以下の文字列をコピー
   - `mongodb://root:password123@localhost:27017/`
     ![入力ボックス](assets/2026-07-16-07-01-28.png)

### bcrypt

`bcrypt` インストール時に以下のメッセージがでる。

npm warn allow-scripts `npm install-scripts ls` を実行して確認するか、`npm install-scripts approve <pkg>` を実行して許可してください。

```terminal
$ npm install bcrypt@^6.0.0
...
npm warn allow-scripts Run `npm install-scripts ls` to review, or `npm install-scripts approve <pkg>` to allow.
$ npm install-scripts approve bcrypt
```

`bcrypt.hasSync`
: ハッシュ化するための関数。文字列をハッシュ化する機能とプレーンテキストとハッシュ化文字列の照合機能

強度10の設定（ストレッチング回数）を使って暗号化し、毎回違う暗号化結果になるように、内部で自動生成されるランダムなデータで暗号計算を2の10乗回繰り返す

変数 `hash` の長さは常に60

```js
import bcrypt from 'bcrypt';

const hash = bcrypt.hashSync(password, 10);
console.log(hash.length);
```

違う文字列となるか確認

```js
const password = 'test1234';
const hashFirst = bcrypt.hashSync(password, 10);
const hashSecond = bcrypt.hashSync(password, 10);

console.log(hashFirst === hashSecond); // false
```

### データレイアウト

マスターパスワードハッシュとパスワードのコレクションのデータレイアウト

```json
{
  "_id": "<ObjectId>",
  "type": "auth",
  "hash": "u904u32jewr043j0vn340h034rev43r3ce"
}
```

パスワードコレクションのデータレイアウト

```json
{
  "_id": "<ObjectId>",
  "passwords": [
    {
      "source": "google",
      "password": "chromium2000"
    },
    {
      "source": "facebook",
      "password": "metacoolp3rson"
    }
  ]
}
```

`mongodb://localhost:27017`
: MongoDBローカルに接続するためのエンドポイント

```terminal
$ docker compose ps
NAME           IMAGE          COMMAND                   SERVICE   CREATED        STATUS        PORTS
mongodb_labs   mongo:latest   "docker-entrypoint.s…"   mongodb   33 hours ago   Up 33 hours   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp
```

`!!hashedPassword`

- 値を明示的に真偽値に変換
- ダブルパンという
- データがある場合: true
- データがない場合: false
- 元の値 `{type : 'auth' }` は \*\*Truthy
- 1つ目の `!` で反転させ false になる
- 2つ目の `!` でさらに反転させ true になる

Flasyな値

- false, null, undefined, 0, -0, 0n(Bigintのゼロ), ""(空文字), Nan(Not a Number)

`await authCollection.findOne({ type: "auth" })`

- type が auth であることがわかる理由は？
- 31行目で `await authCollection.insertOne({ type: "auth", hash })` と定義されている

`process.exit(1)` : プログラム終了時の成否を表す終了コード

- 0: 正常終了
- 1: 異常終了

`findOne({ type: 'auth' })`
: 1件のみ検索する

`insertOne({ type: 'auth', hash })`
: 登録保存されるデータは1件のみに制限

`bcrypt.compare(password, hash)`
: プレーンテキストパスワードとハッシュ化パスワードを検証する

- 第1引数: ユーザーが入力した生の文字列（プレーンテキスト）
- 第2引数: データベース等に保存されているハッシュ化された文字列

```js
const compareHashedPassword = async (password) => {
  const { hash } = await authCollection.findOne({ type: 'auth' });
  if (!hash) {
    throw new Error('No stored hash found.');
  }
  return await bcrypt.compare(password, hash);
};
```

`{ $set: { password } }`

- 更新演算子
- フィールド名とデータベースへの命令を区別するために付加するルール
- `password` フィールドのみ更新せよとの意味

`findOneAndUpdate`
: 指定した条件に一致するドキュメントを検索し、値を更新して返す

### MongoDB接続の標準手順

1. クラスのインポートとインスタンス化

```js
import { MongoClient } from 'mongodb';
const dbUrl = 'mongodb://localhost:27017'; // 接続先URL
const client = new MongoClient(dbUrl); // インスタンス化
```

2. サーバーへの接続確

```js
await client.connect();
```

3. データベースとコレクションの取得

```js
const db = client.db('データベース名'); // データベースの取得
const collection = db.collection('コレクション名'); // コレクションの取得
```

4. エラーハンドリング

```js
try {
  // 接続とコレクション取得の処理
} catch (error) {
  console.error('エラーログ:', error);
  process.exit(1); // 必要に応じてアプリを終了
}
```

## コンテンツアグリゲーター

`await fetch(url)`
: 非同期で指定URLへGETリクエストを行う

`response.text()`
:　レスポンスボディ全体を文字列で読み取りより鳥、標準出力する

```js
const main = async () => {
  const url = 'https://www.bonappetit.com/feed/recipes-rss-feed/rss';
  const response = await fetch(url);
  // text() を使ってレスポンスボディ全体を文字列でより鳥、標準出力する
  console.log(await response.text());
```

`console.table(results)`
: 配列やオブジェクトをテーブル形式で出力。index は0始まりで変更不可能

```terminal
node index.js
Recipes RSS Feed Nov 2021
┌─────────┬──────────────────────────────────────────────────────────────┬─────────────────────────────────────────────────────────────────────────┐
│ (index) │ title                                                        │ link                                                                    │
├─────────┼──────────────────────────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────┤
│ 0       │ 'Steak With Dashi Greens for One'                            │ 'https://www.bonappetit.com/recipe/steak-dashi-greens-for-one'          │
│ 1       │ 'Polka-Dot Grilled Cheese'                                   │ 'https://www.bonappetit.com/recipe/polka-dot-grilled-cheese'            │
│ 2       │ '19 Snacks and Sips for Watching the 2026 World Cup'         │ 'https://www.bonappetit.com/gallery/world-cup-2026-recipes'             │
│ 3       │ 'Halo-Halo (Filipino Shaved Ice)'                            │ 'https://www.bonappetit.com/recipe/halo-halo'                           │

```

`parseURL(url)`

- メソッドである
- 信とXML解析を一括で処理する
- URLからRSS/Atomフィードを取得・解析しJSONオブジェクトへ自動変換する

例: RSSフィードのCMLを取得解析し、 title と items に分割代入する

```js
const main = async () => {
  const url = 'https://www.bonappetit.com/feed/recipes-rss-feed/rss';
  const { title, items } = await parser.parseURL(url);
};
```

`Promise.all`
: 配列内のすべての処理を同時にスタートさせ、すべてが終了した瞬間に結果をまとめて取得

- 処理時間の圧倒的短縮
- 結果は元の順番である
- 1つでも失敗すると、即座に全体がエラーになる
- すでの始まっている複数の処理の完了を1つの窓口でまとめて待つ

`{ sigint: true }`
: アプリケーション終了を有効にする

```js
import promptModule from 'prompt-sync';

const prompt = promptModule({ sigint: true });
```

## 図書館API

**URI**
: リソースを指揮月するための識別子。 **URL** はこれの一種

**ステートレス**
: 過去の状態を記憶せず、1回限りの独立した処理として毎回やり取りを行う通信方式

**トップレベル awit**

- async 関数で囲むことなくファイルの最上位で直接 await キーワードを使用できる機能

```js
await app.listen({ port: PORT, host: '0.0.0.0' });
```

| メリット                 | デメリット             |
| :----------------------- | :--------------------- |
| コードが簡潔になる       | 起動処理ブロックの伝搬 |
| 初期化の順序を保証できる | デッドロックの危険性   |
| 動的インポートが容易     | 古い環境での日互換性   |

### Fastify

プライグイン関数

| 機能     | Fastifyのプラグイン                  | Expressのミドルウェア                  | Expressのルーター                |
| :------- | :----------------------------------- | :------------------------------------- | :------------------------------- |
| 主な役割 | ルートや機能のカプセル化・構造化     | リクエスト/レスポンスの処理・書き換え  | ルートのグループ化・共通パス設定 |
| スコープ | 独立したスコープ（カプセル化）を持つ | アプリケーション全体または特定のルート | 特定のパス配下のルート群         |
| 拡張性   | デコレータ等でシステム自体を拡張可能 | req や res オブジェクトを拡張          | ルートのバインドのみ             |

シンプルな **Fastify** サーバー

```js
import Fastify from 'fastify';

const app = Fastify();
const PORT = 3000;

try {
  await app.listen({ port: PORT, host: '0.0.0.0' });
  console.log(`Listening at http://localhost${PORT}`);
} catch (err) {
  console.error(err);
  process.exit(1);
}
```

**@fastify/formbody**
: `application/x-www-form-urlencoded` コンテツパーサー

- 主にHTMLフォーム送信に使われるデータ形式

`@fastify/formbody*` をミドルウェアとして登録

```js
import formbody from '@fastify/formbody';
await app.register(formbody);
```

**GET** リクエストの処理

```js
app.get('/', async (_request, reply) => {
  reply.send({ message: 'ok' });
});
```

```terminal
$ url -G http://localhost:3000
{"message":"ok"}
```

プロジェクトのディレクトリ構造

```terminal
├── index.js
├── package-lock.json
├── package.json
└── routes
    ├── booksRouter.js
    └── index.js
```

`reply`
: クライアントへ返すHTTPリクエストを管理・操作するためのコアオブジェクト

`reply.send(book)` : [リファレンス](https://fastify.dev/docs/latest/Reference/Reply/#senddata)
: book オブジェクトを JSONオブジェクト形式で送信

- Buffer,stream,string,undefined,Errorのいずれでもない全ての値を JSON シリアライズする

```js
// book オブジェクトを JSONオブジェクト形式で送信
reply.send(book);
```

ルーティングの書き方は、以下の記述で `/books` になる

```js
// index.js
fastify.register(booksRouter, { prefix: '/books' });

// booksRouter.js
fastify.get('/:id', async (request, reply) => {
```

ルート `./routes/index.js` を `/api` に登録

```js
import routes from './routes/index.js';

// ルートを /api に登録
await app.register(routes, { prefix: '/api' });
```

`const book = { id }` : `{}` 波括弧で囲まれているのでオブジェクトである。

```js
const book = { id };
```

### ルートの登録

子ルートに登録し、それを親ルートにも登録する

booksルートに登録 : `routes/index.js`

```
import booksRouter from './booksRouter.js';

async function routes(fastify, _opts) {
  fastify.register(booksRouter, { prefix: '/books' });
}

export def
```

トップルートに登録 : `index.js`

```js
import routes from './routes/index.js';

// ルート `./routes/index.js` を `/api` に登録
await app.register(routes, { prefix: '/api' });
```

### crulを使ったテスト

GETリクエストのテスト

```terminal
$ curl http://localhost:3000/api/books/42

{"id":"42"}
```

POSTリクエストのテスト

```terminal
$ curl http://localhost:3000/api/books/ -d "title=Frankenstein" -d "author=Mary Shelley"
$ curl -X POST -d \
  'title=Frankenstein&author=Mary Shelley' http://localhost:3000/api/books/

{"title":"Frankenstein","author":"Mary Shelley"}
```

PUTリクエストのテスト

```terminal
$ curl -X PUT http://localhost:3000/api/books/42 -d "title=Frankenstein" -d "author=Mary Shelley"
$ curl -X PUT -d \
  'title=Frankenstein&author=Mary Shelley' http://localhost:3000/api/books/42

{"id":"42"}
```

DELETEリクエストのテスト

```terminal
$ curl -X PUT http://localhost:3000/api/books/42 -d "title=Frankenstein" -d "author=Mary Shelley"
$ curl -X DELETE http://localhost:3000/api/books/42

{"id":"42"}
```

### SQLite

**sequelize**
: Postgres、MySQL、MariaDB、SQLite、DB2、Microsoft SQL Server、Snowflakeに対応した、使いやすいPromiseベースのNode.js ORMツール

プロジェクトの構成

```terminal
$ tree . -L 2 -I node_modules/
.
├── db
│   ├── config.js
│   └── database.selite
├── index.js
├── modles
│   └── book.js
├── package-lock.json
├── package.json
└── routes
    ├── booksRouter.js
    └── index.js
```

[Sequelizeクラスのリファレンス](https://sequelize.org/api/v6/class/src/sequelize.js~sequelize)

`dialect`
: 使用するデータベースの種類を指定

```js
const db = new Sequelize({
  dialect: 'sqlite',
  storage: './db/database.sqlite',
});
```

`authenticate()`
: SQLite3はファイルデータベスのため、認証情報(ユーザー名、パスワード、ホスト名、ポート番号)が不要

- ユーザー認証を行っていない
- 指定された `storage` に正常にアクセス/読み書きできるかテストを行っている

```js
  dialect: 'sqlite',
  storage: './db/database.sqlite',
  ...
});

try {
  await db.authenticate();
}
```

`export default { Sequelize }`
: `import` によって宣言されたライブラリは `export` できる

```js
import { Sequelize } from 'sequelize';

export default {
  Sequelize,
  db,
};
```

`define(...)`
: DBテーブルを定義

[sequelise.define](https://sequelize.org/docs/v6/core-concepts/model-basics/#using-sequelizedefine)

```js
// db を使ってBookモデルを定義
const Book = db.define(
  'Book',
  {
    title: {
      // title フィールドを重複する値を持たない文字列として定義
      type: Sequelize.STRING,
      unique: true,
    },
    author: {
      // author フィールドを文字列として定義
      type: Sequelize.STRING,
    },
    count: {
      // count フィールドを製図鵜方として定義
      type: Sequelize.INTEGER,
      defaultValue: 0,
    },
  },
  {}
);
```

**CRUD操作**

- 失敗する場合があるので、常に `try ... catch` 文の中で宣言する
- DBアクセスを伴うので常に非同期(`async ... awai`)で処理する

`const book = await Book.findByPk(id)`
: Read プライマリーキーによってBookを検索

`const book = await Book.create({title, author})`
: Create titleフィールド/authorフィールドを使って新しいBookレコードを作成

`const book = await Book.update({title, author}, {where:: { id }})`
: Update プライマリーキーによってBookを検索し、idに合致するtitleフィールド/authorフィールドを更新

`const book = await Book.destroy({where: { id }})`
: Delete プライマリーキーによってBookを検索し、idに合致するレコードをデータベースから削除

```js
async function booksRouter(fastify, _opts) {
  fastify.get('/:id', async (request, reply) => {
    const { id } = request.params;
    try {
      const book = await Book.findByPk(id);
      reply.send(book);
    } catch (e) {
      console.error('Error occurred:', e.message);
      reply.send(e);
    }
  });

  fastify.put('/:id', async (request, reply) => {
    const { id } = request.params;
    const { title, author } = request.body;
    try {
      const book = await Book.update(
        { title, author },
        {
          where: { id },
        }
      );
      reply.send(book);
    } catch (e) {
      console.error('Error occurred: ', e.message);
      reply.send(e);
    }
  });

  fastify.delete('/:id', async (request, reply) => {
    const { id } = request.params;

    try {
      const book = await Book.destroy({
        where: { id },
      });
      reply.send(book);
    } catch (e) {
      console.error('Error occurred: ', e.message);
      reply.send(e);
    }
  });

  fastify.post('/', async (request, reply) => {
    const { title, author } = request.body;
    const book = await Book.create(title, author);
    try {
      const book = { title, author };
      reply.send(book);
    } catch (e) {
      console.error('Error occurred:', e.message);
      reply.send(e);
    }
  });
}
```

## 自然言語処理

[spellchecker](https://www.npmjs.com/package/spellchecker)
スペルチェックライブラリ
[natural](https://www.npmjs.com/package/natural)
汎用的な自然言語処理ライブラリ
[stopword](https://www.npmjs.com/package/stopword)
入力テキストからストップワードを削除

### Dokcer

`Dockerfile`

```docker
FROM node:24.16.0-slim

WORKDIR /app

# パッケージの更新、ロケールとスペルチェッカーのインストール、ロケール設定の生成
RUN apt-get update && apt-get install -y \
    locales \
    hunspell-en-us \
    && rm -rf /var/lib/apt/lists/* \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8

# 環境変数の設定
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

CMD ["/bin/bash"]
```

`compose.yml`

```yml
services:
  ubuntu-node:
    build: .
    container_name: ubuntu_node_dev
    volumes:
      - .:/app
    working_dir: /app
    tty: true
    stdin_open: true
```

利用パッケージ

```terminal
$ docker exec -it ubuntu_node_dev npm list --depth=0
sentiment_journal@1.0.0 /app
├── @types/asciichart@1.5.8
├── @types/prompt@1.1.9
├── @types/sentiment@5.0.4
├── @types/spellchecker@3.5.2
├── @types/stopword@2.0.3
├── asciichart@1.5.25
├── natural@8.1.1
├── prompt@1.3.0
├── sentiment@5.0.2
├── sequelize@6.37.8
├── spellchecker@3.7.1
├── sqlite3@5.1.7
└── stopword@3.1.5
```

**spellchecker**

インストールスクリプトが `allow-scrits` によって実行を保留された

```terminal
$ npm install spellchecker@^3.7.1

added 3 packages, and audited 4 packages in 21s

found 0 vulnerabilities
npm warn allow-scripts 1 package has install scripts not yet covered by allowScripts:
npm warn allow-scripts   spellchecker@3.7.1 (install: node-gyp rebuild)
npm warn allow-scripts
npm warn allow-scripts Run `npm install-scripts ls` to review, or `npm install-scripts approve <pkg>` to allow.
```

**approve** : 許可する/承認する

```terminal
$ npm install-scripts approve spellchecker
Approved spellchecker:
  added spellchecker@3.7.1
```

TypeScriptサポート定義ライブラリ

```terminal
$ npm install -D @types/spellchecker
```

### コンテナの準備

`Dockerfile`

```docker
FROM node:24.16.0-slim

WORKDIR /app

# パッケージの更新、ロケールとスペルチェッカーのインストール、ロケール設定の生成
RUN apt-get update && apt-get install -y \
    locales \
    hunspell-en-us \
    && rm -rf /var/lib/apt/lists/* \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8

# 環境変数の設定
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

CMD ["/bin/bash"]
```

`compose.yml`

```yml
services:
  ubuntu-node:
    build: .
    container_name: ubuntu_node_dev
    volumes:
      - .:/app
    working_dir: /app
    tty: true
    stdin_open: true
```

`.dockerignore`

```gitignore
node_modules
npm-debug.log
.git
.gitignore
.env
.dockerignore
Dockerfile
*.log
```

Dockerイメージビルド後に新しいライブラリを追加した場合は、再びビルドが必要

```terminal
$ docker compose down
# 最新状態の強制取得/過去のビルドデータを利用しない
$ docker compose build --no-cache
$ docker compose up -d
# 現在のロケールを確認
$ docker compose exec ubuntu-node locale
```

起動中のコンテナのnpmパッケージリストを表示

```terminal
# コンテナ名を調べる
$ docker compose ps
NAME              IMAGE                   COMMAND                   SERVICE       CREATED         STATUS         PORTS
ubuntu_node_dev   chapter07-ubuntu-node   "docker-entrypoint.s…"   ubuntu-node   2 minutes ago   Up 2 minutes

# コンテナ名の npm リストを表示
$ docker exec -it ubuntu_node_dev npm list --depth=0
├── natural@8.1.1
├── spellchecker@3.7.1
└── stopword@3.1.5
```

`index.js`

```js
import SpellChecker from 'spellchecker';

const options = SpellChecker.getCorrectionsForMisspelling('grat');
console.log(options);
```

`LANG=en_US.UTF-8 node index` : スクリプトを実行する

```terminal
$ LANG=en_US.UTF-8 node index
Debugger listening on ws://127.0.0.1:33237/c8215b6a-1047-44a8-9655-7951ca5278f6
For help, see: https://nodejs.org/learn/getting-started/debugging
Debugger attached.
[
  'frat',  'gray',  'rat',
  'grate', 'great', 'grant',
  'groat', 'graft', 'grit',
  'gnat',  'goat',  'grad',
  'drat',  'gram',  'ghat'
]
Waiting for the debugger to disconnect...
```

### コード解説

[spellchecker](https://github.com/atom/node-spellchecker)

`getCorrectionsForMisspelling`
: サンプル文字列のスペル修正選択肢リストを取得

`isMisspelled(word)`
: 各単語のスペルが正しいかをチェック

```terminal
# Linux環境では書籍通りにならない
LANG=en_US.UTF-8 node index
I am fling grate
```

`WordTokenizer`
: アルファベット、数字、アンダースコア以外の文字で分割

```js
const tokenizer = new natural.WordTokenizer();
```

`PorterStemmer.stem`
: Porterステミングアルゴリズムを使って単語の語幹を処理

```js
for (let token of tokens) {
  const stem = natural.PorterStemmer.stem(token);
  stems.push(stem);
}
```

`start({})`
: 単純な文字列が渡されるのではない

- `allEmpyt` : 空の入力を許可するかどうか(true/false)
- `memory` : 過去の入力を記憶するかどうか(数値)
- 動作モードを指定するためにオブジェクトで渡す必要がある

```js
import prompt from 'prompt';

prompt.start({});
```

## メーケティングメーラー

[アプリ パスワードでログインする](https://support.google.com/accounts/answer/185833)
[アプリ　パスワードの作成、管理](https://myaccount.google.com/apppasswords)

- 安全性の低いアプリやデバイスにアカウントアクセスを許可する
- 16桁のパスコード
- 2段階プロセスが有効であること

アプリパスワードの作成

![アプリ名の入力](assets/2026-07-29-15-39-31.png)

![生成されたパスワード](assets/2026-07-29-15-46-49.png)

## メーラー

`createTransport`
: **nodemailer** から `createTransport` 関数を分割代入

```js
import { createTransport } from 'nodemailer';
```

`createTransport` のインスタンス定義
: gmail 利用時の設定

```js
// createTransport のインスタンスを割り当てる
const transporter = createTransport({
  service: 'gmail',
  auth: {
    user: 'yasuji.nakanishi.jp@gmail.com',
    pass: 'xxxx yyyy zzzz wwww',
  },
});
```

メールの送信アドレス、宛先アドレス、件名の設定

```js
const mailOptions = {
  // メールインテンスの設定
  from: 'yasuji.nakanishi.jp@gmail.com',
  to: 'mendelssohnusaic@gmail.com',
  subject: 'Welcome to Inn Box',
};
```

電子メールを送信する関数

```js
//
const sendMail = async () => {
  try {
    const info = await transporter.sendMail(mailOptions);
    console.log(`Email sent: ${info.response}`);
  } catch (e) {
    console.log(`An error occurred: ${e.message}`);
  }
};
```

実行と結果

```terminal
$ $ node index.js
Email sent: 250 2.0.0 OK  1785309080 d9443c01a7336-2d022bf8880sm6714635ad.66 - gsmtp
```

![受信したメール](assets/2026-07-29-16-24-05.png)

`app.register(formBody)`
: pplication/x-www-urlencoded データを含んだPOSTリクエストを処理するパーサーを登録

```js
import formBody from '@fastify/formbody';

await app.register(formBody);
```

動作確認

```terminal
$ $ node index.js
Server running at http://localhost:3000

# curl導通テスト後
Received <User Mail Address>@gmail.com
```

curlによるテスト

```terminal
$ curl -X POST -d "email=<User Mail Address>@gmail.com" http://localhost:3000/subscribe
{"message":"ok"}
```

## Webスクレイパー

### e type 型ガード

`'e' is of type 'unknown'`

- 任意の型を throw するために発生
- `instanceof Error` を用いて型ガードを行う

```js
try {
  ...
} catch (e) {
 // 'e' is of type 'unknown'.
  console.log('error', e.message);
}
```

`instanceof` を使ってErrorオブジェクトであることを確認

```js
try {
  ...
} catch (e) {
  if (e instanceof Error) {
    console.log('error', e.message);
  } else {
    console.log('unknown error', e);
  }
}
```

### throw

`{ cause: e })`
: `throw` でエラーを投げる場合の書き方

```js
} catch (e) {
  if (e instanceof Error) {
    throw new Error('Unable to hash password.', { cause:
e });
  } else {
    throw new Error('Occurred unknown Error', { cause:
e });
  }
}
```

### スクレイピング

`await fetch(URL)`
: 非同期で指定URLに対し GET リクエストを行う

`response.text`
: レスポンスをプレーンテキストに変換

```js
const URL = 'https://medium.com/tag/nodejs';

try {
  // fetch で指定URLに対して GET リクエスト
  const response = await fetch(URL);
  // レスポンスをプレーンテキストに変換
  const text = await response.text();
  console.log(text);
} catch (e) {
  // リクエスト中に発生したエラーをキャッチ
  if (e instanceof Error) {
    console.log('error', e.message);
  } else {
    console.log('unknown error', e);
  }
}
```

### エラー

```terminal
error Could not find Chrome (ver. 148.0.7778.97). This can occur if either 1. you did not perform an installation before running the script (e.g. `npx puppeteer browsers install chrome`)

# 解決せず
# npx puppeteer browsers install chrome
```

## アプリケーション認証

### formbody

`app.register(fastifyFormbody)`
: application/x-www-from-urlencoded のペイロード解析のために formbody プライグインを登録

```js
import fastifyFormbody from '@fastify/formbody';

await app.register(fastifyFormbody);
```

### Handlebars

**Handlebars** は、テンプレートエンジン。拡張子 **hbs**

- 変数を表示する: `<div>{{nameVar}}</div>`
- 条件式: `{{#if boolStatment}}`

`engine: { handlebars }`
: view プライグインを登録し、レンダリングエンジンとルートを定義

```js
import fastifyView from '@fastify/view';
import handlebars from 'handlebars';

await app.register(fastifyView, {
  engine: { handlebars },
  root: 'views',
});
```

`{{message}}`
: message 変数を表示

```hbs
<div class='col'>
  {{message}}
```

`href='/?page={{switchPage}}`
: href 属性にクエリパラメーターとして switchPage 変数を追加

```hbs
  <a href='/?page={{switchPage}}'> Click here</a>
</div>
```

`{{#if showExtraFields}} ... {{/if}}`
: 条件に応じて表示される Confirm Password フィールドを追加

```hbs
{{#if showExtraFields}}
  ...
{{/if}}
```

**Handlebars** ファイルの `showExrraFields` を `index.js` で利用

- signup キーは新規登録ページに必要なすべてのページ変数にマッピングされる
- login キーは新規登録ページに必要なすべてのページ変数にマッピングされる
- `route` / `switchPage` / `showExtraFields` の引数に注目

```js
const loginFormVars = {
  signup: {
    title: 'Sign up',
    message: 'Already have an account?',
    route: '/account',
    switchPage: 'login',
    showExtraFields: true,
  },
  login: {
    title: 'Log in',
    message: 'Need to create an account?',
    route: '/auth',
    switchPage: 'signup',
    showExtraFields: false,
  },
};
```

`{ page } = request.query`
: リクエストパラメーターから page キーを分割代入

```js
app.get('/', async (request, reply) => {
  const { page } = request.query;
```

`loginFormVars[page]`
: ブラケット記法。変数 page に入っている文字列をキーとしてオブジェクトからデータを取り出す

```js
const formVars = loginFormVars[page] || loginFormVars.signup;
```

`reply.view('index', formVars)`
: ページをレンダリング

```
  return reply.view('index', formVars);
});
```

`app.post('/account'`
: `/account` の POST ルートを作成

`{ username, password } = request.body`
: リクエストボディから username/password を分割代入

`users[username] = password`
: `username` をキーとして使い、 `password` を users オブジェクトに保存

```js
const users = {};

// 新しいユーザーのデータを受け取る /account の POST ルートを作成
app.post('/account', async (request, reply) => {
  // リクエストボディから username/password を分割代入
  const { username, password } = request.body;
  // username をキーとして使い、password を users オブジェクトに保存
  users[username] = password;
  // アカウントの作成をJSONレスポンスとして返す
  return reply.send({ message: 'Account created' });
});
```

`if (users[username] && users[username] === password)`
: ユーザーが存在し、かつパスワードが保存された値と一致した場合

```js
if (users[username] && users[username] === password) {
```

`db.authenticate`
: データベースに接続

```js
await db.authenticate();
```

### pasport

![Account モデルの概要](assets/2026-08-12-05-31-31.png)

**Account** モデルの責務

- インスタンスをデータベースに登録する方法を管理
- サーバーで認証する方法を管理
- データベースと動悸する方法を管理
- アカウント情報をシリアライズする
- **Sequelize** の Model クラスを拡張し、シームレスな接続を提供

**passport-local** は **passport** と連携しカスタム認証戦略を定義するのに役立つ

### パスワードのハッシュ化

![アカウントパスワードのハッシュ化の流れ](assets/2026-08-08-14-52-11.png)

- データベースが侵害されても漏洩するのはハッシュ値とソルト値のみ

| ライブラリ名 | 特徴                                  |
| :----------- | :------------------------------------ |
| argon2       | GPUクラッキングなどの耐性を持つ(推奨) |
| bcrypt       | ブルートフォース攻撃への耐性を持つ    |
| crypto.scrpt | 汎用的な鍵導出関数                    |

`class Account extends Sequelize.Model`
: Sequelize.Model を拡張した Account クラスを定義

```js
class Account extends Sequelize.Model {...}
```

`static async findByUsername(username)`
: ユーザー名でアカウントを検索するクラス関数

```js
static async findByUsername(username) {...}
```

`findOne({ where: { username: lowerCaseUsername } }`
: Sequalize の findOne クエリを実行し、単一のアカウントレコードを検索

```js
    return await this.findOne({ where: { username: lowerCaseUsername } });
  }
}
```

`['username']`

- ブラケット記法でキーがクォートされている場合は、プロパティ名を文字列としている
- クォートされていなければ、キーは変数名である

```js
account['username'] = account['username'].toLowerCase();
```

`Account.init`
: Account クラスを初期化

```js
Account.init( // スキーマ定義 )
```

データベースフィールドのスキーマ定義

```js
 // STRING 型の username フィールドの定義
 username: {
   type: Sequelize.STRING,
   allowNull: false,
   unique: true,
 },
 // TEXT 型の hash フィールドの定義
 hash: {
   type: Sequelize.TEXT,
   allowNull: false,
 },
 // STRING 型の salt フィールドの定義
 salt: {
   type: Sequelize.STRING,
   allowNull: false,
 },
```

`beforeCreate`
: Sequelize の組み込み関数。データベースに保存される前に実行さえる

```js
// beforeCreate フックを使って username を小文字に変換
Account.beforeCreate((account) => {
  account['username'] = account['username'].toLowerCase();
});
```

`sync()`
: Account モデルをデータベースと同期

```js
Account.sync();
```

`this.build({ username })`
: username フィールドを持つ Account の新しいインスタンスを作成

```js
const account = this.build({ username });
```

`account.save()`
: ハッシュ化したパスワードを保存

```js
return await account.save();
```

`rypto.randomBytes()`
: ランダムなバイトの集まりを生成

```js
const bufferBytes = crypto.randomBytes(32);
```

`toString('hex')`
: HEX に変換

```js
const salt = bufferBytes.toString('hex');
```

`pbkdf2Sync()`
: 同期的にハッシュ値を生成

```js
// ハッシュ化反復回数: 12000 バイト長: 64 ハッシュ化アルゴリズム: sha512
const hashRaw = crypto.pbkdf2Sync(password, salt, 12000, 64, 'sha512');
```

`static passportAuthenticate()`
: 認証戦略のコールバックを返す静的メソッド

```js
static passportAuthenticate() {
    return async (username, password, done) => {
      try {
        const account = await this.findByUsername(username);
        if (!account) {
          return done(null, false, { message: "User not found" });
        }

        const isValid = account.authenticate(password);
        if (isValid) {
          return done(null, account);
        } else {
          return done(null, false, { message: "Password incorrect" });
        }
      } catch (err) {
        return done(err);
      }
    };
  }
```

`static serializeUser(account, done}`
: ユーザーアカウントをシリアライズする静的メソッド

```js
static serializeUser(account, done) {
    const { username } = account;
    done(null, username);
  }
```

`static async deserializeUser(username, done)`
: ユーザーアカウントをデシリアライズする非同期メソッド

```js
static async deserializeUser(username, done) {
  try {
    const foundAccount = await this.findByUsername(username);
    if (!foundAccount) {
      // 一致するアカウントが見つからなければエラーを返す
      return done(new Error('User not found'));
    }
    // 見つかったアカウントを Passport.js の
    // セッションデシリアライズ関数に渡す
    done(null, foundAccount);
  } catch (err) {
    // データベースエラーが発生した場合は、コールバック関数 done に渡す
    done(err);
  }
```

`new LocalStrategy(this.passportAuthenticate())`
: passportAuthenticate クラスメソッドを使って新しい LocalStrategy を返す

```js
import { Strategy as LocalStrategy } from 'passport-local';

return new LocalStrategy(this.passportAuthenticate());
```

### Passport.js

**Fastify** 各種プラグインのインポート

```js
import fastifyCookie from '@fastify/cookie';
import fastifySession from '@fastify/session';
import fastifyPassport from '@fastify/passport';
```

`register(fastifyCookie)`
: cookie を解析するプラグインを登録

```js
await app.register(fastifyCookie);
```

`register(fastifySession, {...}`
: セッションプラグインを登録

```js
await app.register(fastifySession, {
  // 改ざんを防ぐための秘密鍵
  secret: process.env.APP_SECURE_KEY,
  // ユーザーのブラウザに保存する cookie
  cookie: {
    // true: HTTPS/false: HTTP
    secure: false,
    // cookie の有効期限: 24時間
    maxAge: 1000 * 60 * 60 * 24,
  },
  // 未初期化セッションはセッションデータを保存しない
  saveUninitialized: false,
  // セッションデータに変更のない場合は、保存しない
  resave: false,
});
```

`register(fastifyPassport.initialize())`
: Passport の初期化ミドルウェアを登録

```js
await app.register(fastifyPassport.initialize());
```

`register(fastifyPassport.secureSession())`
: Passport のセッションミドルウェアを secureSession() を介して登録

```js
await app.register(fastifyPassport.secureSession());
```

`registerUserSerializer(}'
: ユーザー名を直接返す非同期のシリアライズ関数を登録

```js
fastifyPassport.registerUserSerializer(async (user, request) => user.username);
```

`registerUserDeserializer(async (username, request) => {}`
: アカウントまたはエラーを投げる、非同期のデシリアライズ関数を登録

```js
fastifyPassport.registerUserDeserializer(async (username, request) => {});
```

`use('local', Account.genStrategy())`
: カスタムローカルログイン戦略を登録

```js
fastifyPassport.use('local', Account.genStrategy());
```

`app.post('/account', async (request, reply) => {...}`
: `/account` ルートを作成し、新規ユーザーを登録

```js
app.post('/account', async (request, reply) => {...});
```

`Account.register(username, password)`
: 認証情報(`username/password`)を使って新しいアカウントの登録を試みる

```js
await Account.register(username, password);
```

`reply.send({ message: 'Account created.' })`
: 登録に成功したら成功のメッセージを応答

```js
return reply.send({ message: 'Account created.' });
```

`reply.code(400).send({...})`
: JSON形式でエラーメッセージとステータスコード400を返す

```js
return reply.code(400).send({
  message: 'Account creation failed.',
  error: err.message,
});
```

### JWT

**JSON Web トークン**

- **頭文字をとって **JWT\*\*
- トークンベースのステートレスな認証システム
- **クレーム** とは、ユーザー識別や認可に必要な情報
- サーバーはユーザーの識別情報を含んだ署名付きトークンを生成
- トークンはリクエストの Authorization ヘッダー内でクライアントから送信

JWTモジュールのインポート
: ExtractJw ヘルパーは HTTP リクエストからトークンを抽出する。通常は Bearer スキームと使って Authorization ヘッダーからトークンを抽出する

```js
import { ExtractJwt, Strategy as JWTStrategy } from 'passport-jwt';
import jwt from 'jsonwebtoken';
```

シークレットキーの作成

```terminal
$ opnessl rand -hex 32
```

`static genJWTStrategy()`
: Passport の新しいJWT戦略を返す静的メソッド

```js
// Passport の新しいJWT戦略を返すメソッド
  static genJWTStrategy() {...}
```

`xtractJwt.fromAuthHeaderAsBearerToken()`
: リクエストの Authorization ヘッダーから JWT トークンを抽出

```js
jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
```

`secretOrKey: process.env.JWT_SECRET || 'SECRET_KEY'`
: トークンを検証するために安全なシークレットキーを使用

```js
secretOrKey: process.env.JWT_SECRET || 'SECRET_KEY';
```

`findByUsername(jwtPayload.username)`
: トークンのペイロード内のユーザ名と一致するアカウントをデータベースから検索

```js
const account = await this.findByUsername(jwtPayload.username);
```

`ignJWT() {...}`
: 署名付きトークンを生成するメソッド

```js
  signJWT() {
```

`jwt.sign({ username }, process.env.JWT_SECRET || 'SECRET_KEY')`
: アカウントの username がエンコードされた署名付き JWT を返す

```js
return jwt.sign({ username }, process.env.JWT_SECRET || 'SECRET_KEY');
```

### 動作確認

新しいアカウントを作成

```terminal
$ curl -X POST http://127.0.0.1:3000/account \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=alice&password=rzG1afc5Fdgj"
{"message":"Account created."}
```

不正な認証情報でログイン

```terminal
$ curl -X POST http://127.0.0.1:3000/auth \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=bob&password=rzG1afc5Fdgj"
{"message":"Authentication failed","error":"Invalid username or password"}
```

重複したアカウントを作成

```terminal
$ curl -X POST http://127.0.0.1:3000/account \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=alice&password=rzG1afc5Fdgj"
{"message":"Account creation failed.","error":"Account already exists"}
```

正しいアカウント/パスワードでログイン

```terminal
$ curl -X POST http://127.0.0.1:3000/auth \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=alice&password=rzG1afc5Fdgj"
{"message":"Logged in","username":"alice"}
```

既存のユーザーで認証

```terminal
$ curl -X POST http://localhost:3000/api/auth \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"rzG1afc5Fdgj"}'
{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiaWF0IjoxNzg2NjMyMDg0fQ.-wTWjqkvNJkuady4zd8-B7p3QFqJMJLSdiROhnxw7fc"}
```

トークンを使って、保護されたルートにアクセス

```terminal
$ curl http://localhost:3000/api/test \
    -H "Accept: application/json" \
    -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiaWF0IjoxNzg2NjMyMDg0fQ.-wTWjqkvNJkuady4zd8-B7p3QFqJMJLSdiROhnxw7fc"
{"status":"Authenticated."}
```

## キューシステム

### 待ち時間の模倣

`register(formbody)`
: URL エンコードされたフォーム送信を処理するための formbody プラグインを登録

```js
await app.register(formbody);
```

`for (let i = 0; i < 10_000_000_000; i++)`
: 長いループ処理を使ってブロッキング操作をシミュレート

```js
for (let i = 0; i < 10_000_000_000; i++) {}
```

`console.time() ... console.timeEnd()`
: 処理時間の計測

```js
console.time('Module Process');
...
console.timeEnd('Module Process');
```

### キューを使う

標準のライブラリには **Queue** 型がない。 `push()` や `shift()` などの組み込み **Array** メソッドを使って **FIFO** 方式を実装し、 **Queue** をシミュレートする

| 名称        | 読み方     | 役割                              |
| :---------- | :--------- | :-------------------------------- |
| **FIFO**    | フィフォ   | First-In/First-Out(先入れ先出し)  |
| **Enqueue** | エンキュー | 列の最後尾にデータを追加          |
| **Dequeue** | デキュー   | 列の先頭からデータを取り出し/削除 |

`coffeeQueue = []`
: キューを配列として初期化

```js
const coffeeQueue = [];
```

`push()`
: キューオブジェクトの coffeeQueue に drinkOrder を追加

```js
# First-In 先入れ
coffeeQueue.push(drinkOrder);
```

`console.log(coffeeQueue.length)`
: キューに含まれているアイテムの数を出力

```js
console.log(coffeeQueue.length);
```

`shift()`
: キューからアイテムを取り出し、 nextOrder に代入

```js
# First-Out 先出し
const nextOrder = coffeeQueue.shift();
```

動作確認

注文する

```terminal
# 6種類のカフェを注文 最初の注文は mcchiato
$ for drink in mcchiato latte espresso cappuccino "caffe misto" mericano; \
    do \
        curl -X POST \
        -H "Content-Type: application/json" \
        -d "{\"drinkOrder\":\"$drink\"}" \
        http://localhost:3000/order; \
    done
Drink order added to queueDrink order added to queueDrink order added to queueDrink order added to queueDrink order added to queueDrink order added to queue
```

注文数確認

```terminal
$ curl http://localhost:3000/order-count
6 drink orders in queue
```

納品する

```terminal
$ curl http://localhost:3000/process-order
# 最初に注文した mcchiato が返される
{"order":"mcchiato"}
```

納品後の注文数確認

```terminal
$ curl http://localhost:3000/order-count
5 drink orders in queue
```

### Redis

- パブリッシュ/スクライブメッセージシステム
- クライアント/サーバー間のリアルタイムメッセージングを可能にする
- サーバーに加えた変更を特定のチャンネルをサブスクライブしているクライアントうに即座にプッシュ
- パブリッシャー: 情報発信側/サブスクライバー: 情報を受け取る側

サブスクライブとは
: 特定のチャンネルに送られてくるメッセージをリアルタイムに受け取る機能

プロジェクトで利用するメソッド

`publish(channelName, message)`
: チャネルにメッセージをパブリッシュする。 channnelName 引数はチャンネルの名前、 message 引数は、パブリッシュされるメッセージ。

`subscribe(channelName)`
: チャネルをサブスクライブする。 channnelName 引数はチャンネルの名前。

`on(event, callback)`
: Redis クライアントのイベントリスナーを登録。 event 引数はリッスンするイベントの名前、 callback 引数はイベントが発生したときに呼び出される関数。

基本的なコード

```js
import { createClient } from 'redis';

const redisUrl = process.env.REDIS_URL || 'redis://localhost:6379';

const publisher = createClient({ url: redisUrl });
const subscriber = createClient({ url: redisUrl });

await publisher.connect();
await subscriber.connect();

await publisher.publish('orders', 'latte');
await subscriber.subscribe('orders', (message) => {
  console.log(`Recevied order: ${message}`);
});
```

`compose.yml`

```yml
services:
  redis:
    image: redis:7.2-alpine
    container_name: redis-local
    restart: always
    ports:
      - '6379:6379'
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

```terminal
$ docker compose up -d
$ docker compose ps
NAME          IMAGE              COMMAND                   SERVICE   CREATED         STATUS         PORTS
redis-local   redis:7.2-alpine   "docker-entrypoint.s…"   redis     2 minutes ago   Up 2 minutes   0.0.0.0:6379->6379/tcp, [::]:6379->6379/tcp
```

利用VSCode 拡張機能: cweijan.vscode-redis-client

[Database Client](https://github.com/cweijan/vscode-database-client)

1. インストール後、拡張機能の接続画面から新規作成（New Connection）を開き、以下を入力します。
2. Host: localhost
3. Port: 6379
4. Password: なし
5. 「Connect」または「Test Connection」を押して接続できれば完了です。

![Create Connection](assets/2026-08-14-15-27-10.png)

![Redis 選択](assets/2026-08-14-15-29-43.png)

![接続情報の入力](assets/2026-08-14-15-32-25.png)

- Host: 127.0.0.1
- Port: 6379

接続、保存のボタンを押すと上部にメッセージが表示される
![雪像と保存](assets/2026-08-14-15-38-21.png)

**Redis** データベースをクリックすると操作が行える
![データベースアイコン](assets/2026-08-14-15-42-51.png)

`process.env.REDIS_URL || 'redis://localhost:6379'`
: Redis サーバーの接続先 URL を宣言

```js
import { createClient } from 'redis';

const redisUrl = process.env.REDIS_URL || 'redis://localhost:6379';
```

`createClient({ url: redisUrl })`
: Redis クライアントをサブスクライバーとして作成

```js
const subscriber = createClient({ url: redisUrl });
```

`subscriber.connect()`
: Redis サブスクライバーサーバーに接続

```js
await subscriber.connect();
```

`createClient({ url: redisUrl })`
: Redis クライアントをパブリッシャーとして作成

```js
const publisher = createClient({ url: redisUrl });
```

`publisher.connect()`
: Redis パブリッシャーサーバーに接続

```
await publisher.connect();
```

メッセージの聞き逃しを防ぐために `subscriber` 設定後、 `publisher` を設定する

非同期処理を行い並列化・効率化する

```js
const redisUrl = process.env.REDIS_URL || 'redis://localhost:6379';

const publisher = createClient({ url: redisUrl });
const subscriber = createClient({ url: redisUrl });

// 両方同時に接続を開始する（効率的かつ順番を気にしなくて良い）
await Promise.all([publisher.connect(), subscriber.connect()]);
```

`publisher.publish('drink-order', drinkOrder)`
: チャネル drink-order に対して注文内容をパブリッシュ

```js
const { drinkOrder } = request.body;
await publisher.publish('drink-order', drinkOrder);
```

動作確認

```terminal
curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"drinkOrder":"cappuccino"}' \
    http://localhost:3000/order
```

```terminal
$ node index.js
Server listening on http://localhost3000
Received a new cappuccino order.
```

`publisher.publish( ... )`
: 構造化データをパブっリッシュ

```js
await publisher.publish(
  'drink-order',
  JSON.stringify({
    drink: 'latte',
    cost: 450,
    customer: 'Alice',
  }),
```

`JSON.parse(message)`
: JSONデータをパースしてサブスクライブ

```js
await subscriber.subscribe('drink-order', (message) => {
  const parsed = JSON.parse(message);
});
```

### RabbitMQ

- メッセージ機能のセットを提供
- メッセージのルーティング/応答確認/耐久性/永続キュー

### プロジェクトの概略

- 3つのエントリポイント
  - 注文を受け付ける(main server)
  - 注文を処理する(fulfillment server)
  - 分析を行う(analytics server)
- RabbitMQ の設定
  - 各サーバーのリッスンポートは重複禁止
- 各キュー間でデータを渡す

### Dockerコンテナの準備

```yml
services:
  rabbitmq:
    # 管理画面（Management Plugin）が含まれる公式イメージを使用
    image: rabbitmq:4.0-management
    container_name: rabbitmq-container
    ports:
      - '5672:5672' # RabbitMQのメッセージ通信用ポート
      - '15672:15672' # Web管理画面用のポート
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    restart: always

volumes:
  rabbitmq_data:
```

```terminal
$ docker compose up -d
```

![Create Connection](assets/2026-08-14-15-27-10.png)

![Rabbit MQ 選択](assets/2026-08-15-09-33-05.png)

![接続情報の入力](assets/2026-08-15-09-37-05.png)

- Host: localhost
- Port: 5672
- User: guest
- Password: guest

接続、保存のボタンを押すと上部にメッセージが表示される
![雪像と保存](assets/2026-08-14-15-38-21.png)

**Rabbit MQ** データベースをクリックすると操作が行える
![データベースアイコン](assets/2026-08-14-15-42-51.png)

`let channel, connection`
: RabbitMQ 用の channel/connection 変数を宣言

```js
let channel, connection;
```

`import amqp from 'amqplib'`
: AMQP ライブラリのインポート

```js
import amqp from 'amqplib';
```

`process.env.RABBITMQ_HOST`
: RabbitMQ ホストの定義

```js
const rabbitHost = process.env.RABBITMQ_HOST || 'localhost';
```

`amqp.connect('amqp://${rabbitHost}:5672')`
: RabbitMQ サーバーに接続し、変数 connection に割り当てる

```js
connection = await amqp.connect('amqp://${rabbitHost}:5672');
```

`connection.createChannel()`
: 新しい RabbitMQ チェネルを作成し、変数 channel に割り当てる

```js
channel = await connection.createChannel();
```

`channel.assertQueue('drink-order')`
: drink-order キューが存在することをチェック。なければキューを作成

```js
await channel.assertQueue('drink-order');
```

エラーの出し分け

```js
// 出し分けがおこなわれていないコード
async function connect() {
  try {
    const rabbitHost = process.env.RABBITMQ_HOST || 'localhost';
    // RabbitMQ サーバーに接続詞し、接続を connection に割当
    connection = await amqp.connect(`amqp://${rabbitHost}:5675`);
    // 新しい RabbitMQ チャネルを作成し、 channel に割当
    channel = await connection.createChannel();
    // drink-order キューの存在知を確認
    // 存在しない場合は、作成
    await channel.assertQueue('drink-order');
  } catch (err) {
    // 接続中またはチェネル作成中に発生したエラーを出力
    console.error(err);
  }
}
```

接続処理及び、チャネル作成とキューの確認処理に対し、それぞれのエラーを出し分ける

```js
// 出し分けが行われているコード
async function connect() {
  const rabbitHost = process.env.RABBITMQ_HOST || 'localhost';

  // 1. 接続処理
  try {
    connection = await amqp.connect(`amqp://${rabbitHost}:5672`);
  } catch (err) {
    console.error('【接続エラー】RabbitMQへの接続に失敗しました:', err);
    return; // 接続に失敗した場合は以降の処理を中断
  }

  // 2. チャンネル作成とキューの確認処理
  try {
    channel = await connection.createChannel();
    await channel.assertQueue('drink-order');
    console.log('Main app connected to RabbitMQ');
  } catch (err) {
    console.error('【チャンネルエラー】チャンネルの作成またはキューの確立に失敗しました:', err);
  }
}
```

`channel.sendToQueue( ... )`
: データをシリアル化し永続化オプションを付けてターゲットのキューに送信

`Buffer.from(JSON.stringify(data))`
: JavaScriptのオブジェクト/配列を JSON 形式に変換し、 Node.js が扱えるバイナリデータに変換

```js
await channel.sendToQueue('drink-order', Buffer.from(JSON.stringify(data)), {
  persistent: true,
});
```

`persistent: true`
: 永続化を有効にする

```js
achannel.sendToQueue(), {
  persistent: true,
});
```

`{ drinkOrder: order, cost, customer } = request.body`
: リクエストボディの drinkOrder は order とリネームする

```js
const { drinkOrder: order, cost, customer } = request.body;
```

`channel.consume()`
: リッスンするコンシューマーを設定

```js
channel.consume('drink-order', async (data) => { ... })
```

`JSON.parse(content.toString())`
: JSON形式の文字列を扱える形式(オブジェクト/配列など)に変換

```js
const { order, customer } = JSON.parse(content.toString());
```

`if (drinkMap[order] !== undefined)`
: 注文の種類が追跡マップに存在するチェック

`drinkMap[order]++`
: 存在していればインクリメント

```js
if (drinkMap[order] !== undefined) {
  drinkMap[order]++;
}
```

`channel.ack(data)`
: メッセージを受信したことを RabbitMQ に確認応答

```js
channel.ack(data);
```

### 動作確認

Main Server/Fulfillment Server/Analytics Server それぞれにターミナルを開き、3つのターミナルで以下を入力。

```terminal
$ npm start
```

4つめのターミナルで以下を入力

```terminal
$ curl -X POST -H "Content-Type: application/json" \
  -d '{"drinkOrder":"latte", "cost":"4.50","customer":"Alice Liddell"}' \
  http://localhost:3000/order
```

Main Server ターミナルの出力

```terminal
[Main Server]: Main app connected toRabbitMQ
[Mai Server]: Server running at http://localhost:3000
[Main Server]: Drink: latte is being processed for Alice Liddell
```

Fulfillment Server ターミナルの出力

```terminal
[Fulfillment Server]: Server running at http://localhost3001
[Fulfillment Server]: latte being fulfilled for Alice Liddell
```

Analytics Server ターミナルの出力

```terminal
Server running at http://localhost:3002
[Analytics Server]: latte being analyzed for Alice Liddell
```

### 分析機能

`Object.keys(drinkMap)`
: 指定したオブジェクトが持つプロパティ名だけを抜き出し、新しい配列を作る

```js
const drinkMap = { latte: 0, coffee: 0, cappuccino: 0 };
const drinkNames = Object.keys(drinkMap);
```

`reduce((...) => { ... },0)`
: 0は合計カウンターの初期値

```js
reduce((total, drinkName) => {
  return total + drinkMap[drinkName];
}, 0);
```

`|| 0`
: totalDrinkCount が0になった場合に0除算(NaN)を防ぐ

```js
const percentage = Math.floor((drinkMap[drinkName] / totalDrinkCount) * 100) || 0;
```

`drinkMap[drinkName] = 0`
: すべてのドリンクの数を0にリセット

```js
drinkNames.forEach((drinkName) => {
  drinkMap[drinkName] = 0;
});
```

### 動作確認

```terminal
$ curl -X POST -H "Content-Type: application/json" \
  -d '{"drinkOrder":"latte", "cost":"4.50","customer":"Jon Wexler"}' \
  http://localhost:3000/order

$ curl -X POST -H "Content-Type: application/json" \
  -d '{"drinkOrder":"cappuccino", "cost":"8.80","customer":"Alice Liddell"}' \
  http://localhost:3000/order

[Analytics Server]: latte being analyzed for Alice Liddell
Drink orders:  latte: 100%, coffee: 0%, cappuccino: 0%
Drink orders:  latte: 100%, coffee: 0%, cappuccino: 0%
...
[Analytics Server]: cappuccino being analyzed for Alice Liddell
Drink orders:  latte: 66%, coffee: 0%, cappuccino: 33%
Drink orders:  latte: 66%, coffee: 0%, cappuccino: 33%
...
```

## ブロックチェーン

![ブロックチェーンマーケットの視覚化](assets/2026-08-18-18-24-10.png)

### ブロックチェーンとは

ブロックチェーン
: ネットワーク上のシステムトラザクションを安全で透明性が高く、かつ変更不可能な方法で記録することを可能にする分散台帳技術

トラザクション
: ブロックチェーンネットワーク上のスマートコントラクトにリクエストを送ることでトラ座クションを開始し、台帳に詳細が記録される前にリクエストが変性される

スマートコントラクト
: システムプロセスのルールと条件を矯正する自己実行プログラム。スマートコントラクトはコード内に記述され、ブロックチェーンネットワーク上にデプロイされる

コンセンサス
: ブロックチェーンネットワーク上でシステムトラ座クションをを検証するプロセス。コンセンサスアルゴリズムよって行われ、ノードのネットワークがトラザクションの妥当性に合意する必要がある

ブロック
: 検証済みのシステムトラザクションの集まり。時間順にブロックチェーンネットワークに追加される

台帳
: ブロックチェーンネットワーク上のすべてのシステムトラザクションのデジタル記録。アイテムの詳細情報が含まれる。台帳は変更不可で透明性が高いため、記録されたトラザクションは改ざん・削除ができない

ノード
: 参加している端末の単位。データの検証/記録/転送などの役割に注目している。

ピア
:ブロックチェーンネットワークに参加しているコンピューター端末。ノード同士の対等な通信関係を指す概念

プルーフオブワーク
: 膨大な計算実績によって取引の正しさを証明し、データの改善を防ぐ仕組みのこと

マイニング
: 計算パズルを解くことで、ブロックチェーンに新しいブロックを追加するプロセスのこと

プログラムコードの概要
: Block/Blockchain/Transaction/MarketplaceNode の各クラスを含んだ基本的なコードを記述する

### Webサーバーの設定

`process.argv.slice(2)`
: コマンドライン引数から先頭の2要素を取得

```js
// コマンドライン引数を取得し、分割代入
// 最初の引数を変数 PORT に割当
let [PORT] = process.argv.slice(2);
```

`server.address().port`
: 変数 PORT の値が存在しない場合は、 fastify が割り当てる

```js
PORT = fastify.server.address().port;
```

動作確認。起動毎にポートが変わる。ポート番号を指定して起動すれば、番号がマッピングされる

```terminal
$ node index.js
[Marketplace]: Running on http://localhost:46051
[Marketplace]: Running on http://localhost:41031

$ node index.js 3000
[Marketplace]: Running on http://localhost:3000
```

![ノードをネットワークに追加](assets/2026-08-19-06-47-07.png)

`class MarketplaceNode { ... }`
: マーケットプレースネットワーク内の1つのノードを表すクラスを定義

`constructor(url, peers = [], blockchain) { ... }`
: 初期化関数

`this.broadcastSelf()`
: ノードをピアにブロードキャストする

```js
class MarketplaceNode {
  constructor(url, peers = [], blockchain) {
    this.url = url; // ノード自身のネットワークアドレス
    this.peers = peers; // ネットワーク上の他のノードのリスト
    this.blockchain = blockchain; // 共有ブロックチェーンのデータ
    this.balance = 50_000; // 初期資金残高
    this.songs = {}; // 楽曲コンテンツ
    this.broadcastSelf();
  }
}
```

`broadcast(path, data) { ... }`
: 各ピアへブロードキャスト送信を行う

```js
// path: データの送信先エンドポイント
// data: 新しい MarketplaceNode の登録などの変更を表す
async broadcast(path, data) { ... }
```

`peers.map(async (peer) => { ... }`
: MarketplaceNode のピア URL のリストを繰り返し処理

```js
this.peers.map(async (peer) => { ... }
```

`if (peer === this.url) return`
: ブロードキャストを実行しているノードの URL と一致するピア RUL をスキップ

```js
if (peer === this.url) return;
```

`axios.post(`${peer}/${path}`, data)`
: 指定されたパスに対して axios で POST リクエスト。ネットワーク内のピアに対しtえ同じエンドポイントにHTTP リクエストを送信する

```js
await axios.post(`${peer}/${path}`, data);
```

`broadcastSelf() { ... }`
: 関数定義

`broadcast('register-node', { url: this.url })`
: register-node パスとデータとしてのノードの URL を使って MarketplaceNode オブジェクトのbroadcast 関数を呼び出す

```js
// 他のピアノーどの register-node エンドポイントを呼び出し、
// データとして自身のマーケットプレースノード URI 情報を受け取り、
// ノードの peers リストに追加する
async broadcastSelf() {
  await this.broadcast('register-node', { url: this.url });
}
```

`registerNode(newNodeUrl) { ... }`
: 関数定義

`registerNode(newNodeUrl) { .. }`
: 新しいマーケットプレースノード URI 情報を受け取り、ノードの peers リストに追加。

```js
async registerNode(newNodeUrl) { ... }
```

`peers.push(newNodeUrl)`
: ノードの peers リストに newNodeUrl 値を追加

```js
this.peers.push(newNodeUrl);
```

`broadcast('sync-peers', { peers: this.peers })`
: sync-peers パスとデータとしてのノードの URL を使って、MarketplaceNode オブジェクトのbroadcast 関数を呼び出す

```js
await this.broadcast('sync-peers', { peers: this.peers });
```

`const initializeNode = () => { ... }`
: 現在のノードの URL を設定し、ピアノードのリストを初期化する関数

```js
const initializeNode = () => { ... }
```

`URL = process.env.NODE_URL || `http://localhost:${PORT}``
: 開発用の URL と動的に割り当てられた PORT 番号を変数 URL に割り当てる。変数 NODE_URL が設定されていれば、優先して割り当てる

```js
const URL = process.env.NODE_URL || `http://localhost:${PORT}`;
```

`process.env.INITIAL_PEERS !== undefined  ? process.env.INITIAL_PEERS.split(',').filter(Boolean)  : ['http://localhost:3000']`
: initialPeers 配列に http://localhost:3000 を URI に設定。環境変数 INITIAL_PEERS が定義されていれば、カンマ区切りでオーバーライド

```js
const initialPeers =
  process.env.INITIAL_PEERS !== undefined
    ? process.env.INITIAL_PEERS.split(',').filter(Boolean)
    : ['http://localhost:3000'];
```

`new MarketplaceNode(URL, initialPeers)`
: 定義された URL と initialPeers リストを使って、 MarketplaceNode の新しいインスタンスを作成する

```js
marketplaceNode = new MarketplaceNode(URL, initialPeers);
```

fastify.post('/register-node', async (request, reply) => { .. }`
: Fastify を使って /register-node という POST ルートを定義

```js
fastify.post('/register-node', async (request, reply) => { ... }
```

`{ url: newNodeUrl } = request.body`
: リクエストボディから newNodeUrl プロパティを url として抽出

```js
const { url: newNodeUrl } = request.body;
```

marketplaceNode.registerNode(newNodeUrl)
: 現在の marketplaceNode にノードを登録

```js
await marketplaceNode.registerNode(newNodeUrl);
```

`fastify.post('/sync-peers', async (request, reply) => { ... }`
: Fastify を使って /sync-peers という POST ルートを定義

```js
fastify.post('/sync-peers', async (request, reply) => { ... }
```

`{ peers } = request.body`
: リクエストボディからビア URL のリストを抽出

```js
const { peers } = request.body;
```

`marketplaceNode.peers = peers`
: ローカルのピアリストを置き換える

```js
marketplaceNode.peers = peers;
```

動作確認

```terminal
// 1番めのターミナル
$ node index.js 3000
PORT >>  3000
[Marketplace]: Running on http://localhost:3000P
```

```terminal
// 2番めのターミナル
$ node index.js
PORT >>  undefined
[Marketplace]: Running on http://localhost:43069
[sync-peers post root]: http://localhost:43069 synced http://localhost:3000,http://localhost:43069
```

```terminal
// 3番めのターミナル
$ node index.js
PORT >>  undefined
[Marketplace]: Running on http://localhost:34091
[sync-peers post root]: http://localhost:34091 synced http://localhost:3000,http://localhost:43069,http://localhost:34091
```

### ブロックチェーンのコーディング

![ブロックチェーンネットワークの視覚化](assets/2026-08-19-18-28-44.png)

![データモデル間の関係](assets/2026-08-19-18-33-19.png)

`class Block { ... }
:ブロックチェーン内のブロックを表すクラス定義

```js
class Block { ... }
```

`constructor(transactions, previousHash) { ... }`
: 新しい Block をインスタンス化するときにtransactions/previousHash の値を受け取る

```js
  constructor(transactions, previousHash) { ... }
```

Block クラスのプロパティを定義

nonce: 一度だけ使われる数値(Number Used Once)の略でノンス/ナンスと読む

```js
this.timestamp = Date.now(); // 現在時刻を割当
this.transactions = transactions; // 引数の transactions 配列を割当
this.previousHash = previousHash; // 直前のブロックのハッシュ値である previousHash を割当
this.nonce = 0; // 0で初回化。マイニング時に有効なバッシュを見つけるために使われう
this.hash = this.calculateHash(); // 新しく計算されたハッシュ値を割当
this.nextHash = null; // 開始値の null を割当
  }
}
```

`createHash('sha256')`
: createHash 関数を呼び出し sah256 アルゴリズムを使ってハッシュオブジェクトを作成

```js
createHash('sha256');
```

`update(this.previousHash + this.timestamp + JSON.stringify
(this.transactions) + this.nonce)`
: 入力の直前のハッシュ値/タイムスタンプ/トラザクションの配列/nonce値に基づいてハッシュ値を生成

```js
.update(this.previousHash + this.timestamp + JSON.stringify
(this.transactions) + this.nonce)
```

`digest('hex')`
: 生成されたハッシュ値を16進数に変換

```js
.digest('hex')
```

`class Blockchain { ... }`
: ブロック台帳を表す Blockchain クラスを定義

```js
class Blockchain { ... }
```

`constructor(chain, pendingTransactions = [])`
: 初期化時に最初のチェーンと保留トランザクションのリストを引数に取る

```js
  constructor(chain, pendingTransactions = []) { ... }
```

`this.chain = chain || [this.createGenesisBlock()]`
: チェーンが指定されなかった場合は、ジェネシスブロックを使ってチェーンを初期化

```js
this.chain = chain || [this.createGenesisBlock()];
```

`this.difficulty = 4`
: マイニングの難度を設定

```js
this.difficulty = 4;
```

`this.pendingTransactions = pendingTransactions`
: 次にマイニングされるブロックに追加されるのを待っているトラザクションを保存

```js
this.pendingTransactions = pendingTransactions;
```

`miningReward = 100`
: マイニング報酬を設定

```js
this.miningReward = 100;
```

`createGenesisBlock() { ... }`
:ブロックチェーンに保存する最初のブロックを返すメソッド

```js
createGenesisBlock() { ... }
```

`getLatestBlock() { ... }`
: チェーン内の最新のブロックを取得するメソッド

```js
getLatestBlock() { ... }
```

`chain[this.chain.length - 1]`
: 最新のブロックを取得

```js
return this.chain[this.chain.length - 1];
```

`new Blockchain()`
: Blockchain インスタンスを作成

```js
const blockchain = new Blockchain();
```

`marketplaceNode = new MarketplaceNode(URL, initialPeers, blockchain)`
: MarketplaceNode の新しいインスタンスを作成時の引数に変数 blockchain　を加える

```js
marketplaceNode = new MarketplaceNode(URL, initialPeers, blockchain);
```

`fastify.post('/sync-blockchain', async (request, reply) => { ... }`
: /sync-blockchain POST ルートを定義

```js
fastify.post('/sync-blockchain', async (request, reply) => {
```

`const { chain, songs } = request.body`
: リクエストボディから chain/song を分割代入

```js
const { chain, songs } = request.body;
```

`marketplaceNode.blockchain = new Blockchain( ... )`
: マーケットプレースのブロックチェーンを更新。受け取った chain と既存の保留トラ座クションを渡す

```js
marketplaceNode.blockchain = new Blockchain(chain, marketplaceNode.blockchain.pendingTransactions);
```

`marketplaceNode.songs = { ...marketplaceNode.songs, ...songs }`
: 受け取った楽曲リストをローカルノードの楽曲リストにマージ

```js
marketplaceNode.songs = { ...marketplaceNode.songs, ...songs };
```

ジェネシスブロックの同期確認

```terminal
$ node index.js 3000
PORT >>  3000
[Marketplace]: Running on http://localhost:3000
```

```terminal
$ node index.js
PORT >>  undefined
[Marketplace]: Running on http://localhost:41353
[sync-peers post root]: http://localhost:41353 synced http://localhost:3000,http://localhost:41353
Syncing blocks [{"timestamp":1787204208833,"transactions":[],"previousHash":null,"nonce":0,"hash":"5c696cba54bd313e7068c9ea5cfa04be82d184f9222067bd06d17024220b5737","nextHash":null}]
```

`'node:crypto'`
: Node.js ライブラリをインポートする場合は、 `node:` とすることが標準

```js
import { randomUUID } from 'node:crypto';
```

`class Transaction { ... }`
: Transaction クラスを定義

```js
class Transaction {
  constructor({ id, price, songTitle, expiration, recipient, sender, transactionType }) {
    this.id = id || randomUUID(); // トラザクションID
    this.price = price; // 価格
    this.songTitle = songTitle; // 楽曲名
    this.expiration = expiration; // 有効期限
    this.recipient = recipient; // 送信元
    this.sender = sender; // 送信先
    this.transactionType = transactionType; // トラザクションタイプ
  }
}
```

`async processTransaction(transactionHash) { ... }`
: 新しいトランザクション処理する非同期メソッドを定義

```js
async processTransaction(transactionHash) { ... }
```

`new Transaction(transactionHash)`
: 与えられたトランザクションデータから新しい Transaction オブジェクトをインスタンス化

```js
const transaction = new Transaction(transactionHash);
```

`transactionHash.id || transaction.id`
: ID のフォールバックを行う。引数 transactionHash にID が含まれていれば、これを優先し、そうでなければ新しく生成されたトランザクションの ID を使う

```js
const id = transactionHash.id || transaction.id;
```

`if (transaction.transactionType === 'BUY') { ... }`
: トランザクションタイプが BUY の場合に処理を続行

```js
    if (transaction.transactionType === 'BUY') { ... }
```

`this.balance < transaction.price`
: 購入を完了するために残高が十分化をチェック

```js
if (this.balance < transaction.price) return 'Insufficient balance';
```

`this.balance -= transaction.price`
: 購入者の残高から支払額を差し引く

```js
this.balance -= transaction.price;
```

`axios.post(`${transaction.recipient}/payment`, { ... }`
: 取引金額を添えて販売者ノードに支払いリクエストを POST 送信

```js
await axios.post(`${transaction.recipient}/payment`, { ... }.
```

`console.log({ bal: this.balance })`
: 価格を差し引いた後で胡軫された残高を出力

```js
console.log({ bal: this.balance });
```

`this.songs[id] = transaction`
: トランザクションの ID を使ってローカルストレージの songs にトランザクションを保存

```js
this.songs[id] = transaction;
```

`blockchain.pendingTransactions.push(transaction)`
: トランザクションをブロックチェーンの保留トランザクションのリストに追加

```js
this.blockchain.pendingTransactions.push(transaction);
```

`blockchain.pendingTransactions.length > 4`
: 保留トランザクションが5つ以上なら

```js
if (this.blockchain.pendingTransactions.length > 4) { ... }
```

`await this.mine()`
: 新しいブロックをマイニングする

```js
console.log(await this.mine());
```

`broadcastBlockchain()`
: 更新されたブロックチェーンをピアノードにブロードキャスト

```js
await this.broadcastBlockchain();
```

`return 'Processed transaction'`
: トランザクションの結果を示すメッセージを返す

```js
return 'Processed transaction';
```

`fastify.post('/payment', async (request, reply) => { ... }`
: /payment POST ルートの定義

```js
fastify.post('/payment', async (request, reply) => { ... }
```

` price } = request.body`
: リクエストボディから price を分割代入

```js
const { price } = request.body;
```

`marketplaceNode.balance += price`
: 受け取った金額だけノードの残高を増やす

```js
marketplaceNode.balance += price;
```

`marketplaceNode.processTransaction({ ... })`
: PAYMENT トランザクションとしてブロックチェーンに記録

```js
await marketplaceNode.processTransaction({
  price,
  sender: marketplaceNode.url,
  transactionType: 'PAYMENT',
});
```

`'0'.repeat(this.difficulty)`
: 難度に基づいてターゲットの接頭辞を設定

```js
const targetPrefix = '0'.repeat(this.difficulty);
```

`this.getLatestBlock()`
: 最新のブロックを取得

```js
const previousBlock = this.getLatestBlock();
```

`new Block(this.pendingTransactions, previousBlock.hash)`
: 保留トランザクションから新しいブロックを作成し、前のブロックにリンクする

```js
const newBlock = new Block(this.pendingTransactions, previousBlock.hash);
```

`while (newBlock.hash.substring(0, this.difficulty) !== targetPrefix) { ... }`
: マイニングの核心部分。設定された難易度の条件を満たすまで nonce 値を1つずつ増やして計算をやり直す

`newBlock.nonce++`
: ブロックの nonce をインクリメント

`newBlock.hash = newBlock.calculateHash()`
: ハッシュを再計算

```js
while (newBlock.hash.substring(0, this.difficulty) !== targetPrefix)
  newBlock.nonce++;
  newBlock.hash = newBlock.calculateHash();
}
```

`previousBlock.nextHash = newBlock.hash`
: 新しいブロックハッシュ値を前のブロックに保存

```js
previousBlock.nextHash = newBlock.hash;
```

`this.chain.push(newBlock)`
: 新しいブロックをブロックチェーンにプッシュ

```js
this.chain.push(newBlock);
```

`this.pendingTransactions = []`
: マイニングが完了したら保留トランザクションを初期

```js
this.pendingTransactions = [];
```

`price = this.blockchain.miningReward`
: マイニング報酬額を保存

```js
const price = this.blockchain.miningReward;
```

`reward = new Transaction({ ... })`
: マイニング報酬トランザクションを作成

```js
const reward = new Transaction({
  sender: this.peers[0], // 報酬の支払い元
  recipient: this.url, // 報酬の受け取りて
  price, // 報酬額
  transactionType: 'MINE', // 取引の種類
});
```

`this.blockchain.pendingTransactions.push(reward)`
:マイニングの報酬とて発生したトランザクション処理を保留中取引リストに追加

```js
this.blockchain.pendingTransactions.push(reward);
```

`blockchain.mineBlock()`
: マイニングプロセスをトリガー

```js
await this.blockchain.mineBlock();
```

`this.balance += price`
: マイナーの残高に報酬を追加

```js
this.balance += price;
```

`fastify.post('/sell', async (request, reply) => { ... }`
: /sell POST ルートを定義

```js
fastify.post('/sell', async (request, reply) => { ... }
```

`{ price, songTitle } = request.body`
: リクエストボディから price/songTitle を分割代入

```js
const { price, songTitle } = request.body;
```

`marketplaceNode.processTransaction({ ... })`
: トランザクション処理を SELL として処理

```js
await marketplaceNode.processTransaction({
  price, // 報酬額
  songTitle, // 楽曲のタイトル
  sender: marketplaceNode.url, // 報酬の支払い元
  transactionType: 'SELL', // 取引の種類
});
```

`fastify.post('/buy', async (request, reply) => { ... }`
:POST ルートの定義: 楽曲の購入ルート

```js
fastify.post('/buy', async (request, reply) => { ... }
```

`{ id } = request.body`
: リクエストボディ亜から楽曲id を分割代入

```js
const { id } = request.body;
```

`marketplaceNode.songs[id]`
: ローカルレジストリから楽曲を抽出

```js
const transaction = marketplaceNode.songs[id];
```

`marketplaceNode.processTransaction({ ... })`
:　楽曲とノードの詳細情報を使って BUY トラザクションを処理

```js
  const result = await marketplaceNode.processTransaction({
    id: transaction.id,
    price: transaction.price,
    songTitle: transaction.songTitle,
    expiration: transaction.expiration,
    recipient: transaction.sender,
    sender: marketplaceNode.url,
    transactionType: 'BUY',
  });
});
```

`Object.values(this.songs)`
: 保存された楽曲オブジェクトから値: 曲名だけを抽出

```js
Object.values(this.songs);
```

`.filter((transaction) => transaction.transactionType === 'SELL')`
: 最新のトラザクションタイプが SELL である楽曲の配列を返す

```js
.filter((transaction) => transaction.transactionType === 'SELL')
```

`.map(({ id, songTitle, price }) => [id, songTitle, price])`
: 絞り込んだデータから ID/songTitle/PRICE を配列に変換

```js
.map(({ id, songTitle, price }) => [id, songTitle, price])
```

`fastify.get('/songs', async (request, reply) => { ... }`
: 販売中の全楽曲を返す /songs GET ルートを定義

```js
fastify.get('/songs', async (request, reply) => { ... }
```

`reply.send({ songs: marketplaceNode.availableSongs() })`
: marketplaceNode に記録されている入手可能な楽曲リストを JSON 形式で返す

```js
reply.send({ songs: marketplaceNode.availableSongs() });
```

動作確認

```terminal
# terminal1
$ node index 3000
PORT >>  3000
[Marketplace]: Running on http://localhost:3000
```

```terminal
# terminal2
$ node index.js
PORT >>  undefined
[Marketplace]: Running on http://localhost:39201
[sync-peers post root]: http://localhost:39201 synced http://localhost:3000,http://localhost:39201
Syncing blocks [{"timestamp":1787307478947,"transactions":[],"previousHash":null,"nonce":0,"hash":"897c95346d86313239a735747592bb1653299eee54c5a652faa56054260f6bfb","nextHash":null}]
```

```terminal
# terminal3
# ノード port: 39201 に入金
$ curl -X POST http://localhost:37635/payment \
  -H "Content-Type: application/json" \
  -d '{"price": 100}'
{"message":"New balance 50100"}
```

```terminal
# terminal1 追加の出力
Syncing blocks [{"timestamp":1787307906441,"transactions":[],"previousHash":null,"nonce":0,"hash":"cdb070a8177a674f1687cd2d3021ab950116f594656ef8b26e8605add2f9a338","nextHash":null}]
```

```terminal
# terminal2 追加の出力
Syncing blocks [{"timestamp":1787307906441,"transactions":[],"previousHash":null,"nonce":0,"hash":"cdb070a8177a674f1687cd2d3021ab950116f594656ef8b26e8605add2f9a338","nextHash":null}]
```

```terminal
# terminal3
# ノード port: 3000 楽曲を出品
$ curl -X POST http://localhost:3000/sell \
  -H "Content-Type: application/json" \
  -d '{"price": 10, "songTitle": "Test Song"}'
{"message":"Song being listed"}
```

```terminal
# terminal2 追加の出力
Syncing blocks [{"timestamp":1787307906441,"transactions":[],"previousHash":null,"nonce":0,"hash":"cdb070a8177a674f1687cd2d3021ab950116f594656ef8b26e8605add2f9a338","nextHash":null}]
```

```terminal
# terminal3
# 出品中の楽曲一覧を確認 失敗
$ curl http://localhost:3000/songs
{"songs":[["53ab5094-f510-4862-bb41-d276cbd65812","Test Song",10]]}
```

```terminal
# terminal3
# 楽曲を購入する
$ curl -X POST http://localhost:37635/buy \
  -H "Content-Type: application/json" \
  -d '{"id": "53ab5094-f510-4862-bb41-d276cbd65812"}'
{"message":"Processed transaction"}
```

```terminal
# terminal1: 追加の出力
$ Syncing blocks [{"timestamp":1787307906441,"transactions":[],"previousHash":null,"nonce":0,"hash":"cdb070a8177a674f1687cd2d3021ab950116f594656ef8b26e8605add2f9a338","nextHash":null}]
```

```terminal
# terminal2 追加の出力
Syncing blocks [{"timestamp":1787307906441,"transactions":[],"previousHash":null,"nonce":0,"hash":"cdb070a8177a674f1687cd2d3021ab950116f594656ef8b26e8605add2f9a338","nextHash":null}]
{ bal: 50090 }
```
