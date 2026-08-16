---
tags:
  - Dev
---

# ライブラリモード

**Node.js** CLIツールのための環境構築。 **Bun** への以降を推奨。

```terminal
$ mkdir PROJECT && cd $_
$ npm init -y
$ npm install -D vite typescript @types/node
```

`tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"]
}
```

`vite.config.ts`

```ts
import { defineConfig } from 'vite';
import { resolve } from 'path';

export default defineConfig({
  build: {
    // CLI 向けにライブラリモードでビルド
    lib: {
      entry: resolve(__dirname, 'src/index.ts'),
      formats: ['es'], // Node.js で扱いやすい ESM 形式
      fileName: 'index',
    },
    rollupOptions: {
      // Node.js のネイティブモジュールや依存関係をバンドルから除外
      external: [
        /^node:/,
        'path',
        'fs',
        'crypto',
        // その他インストールするサードパーティ製パッケージがあればここに追加
      ],
    },
    target: 'node18', // 対象とする Node.js バージョン
    minify: false, // デバッグしやすくするため、必要に応じて無効化
  },
});
```

エントリポイントで `#!/usr/bin/env node` を宣言

```ts
#!/usr/bin/env node

import { argv } from 'node:process';

function main() {
  console.log('🚀 Vite 経由でビルドされた Node.js CLI ツールへようこそ！');

  // 引数の取得
  const args = argv.slice(2);
  if (args.length > 0) {
    console.log(`受け取った引数: ${args.join(', ')}`);
  } else {
    console.log('引数が指定されていません。');
  }
}

main();
```

`

# React Testing Library

## プロジェクト作成

```terminal
$ npm create vite@latest PROJECT_DIR -- --template react
$ cd PROJECT_DIR
$ npm install
```

`package.json` : `type` フィールドと `bin` フィールドを指定

```json
{
  "name": "my-vite-cli",
  "version": "1.0.0",
  "type": "module",
  "main": "./dist/index.js",
  "bin": {
    "my-vite-cli": "./dist/index.js"
  },
  "scripts": {
    "build": "vite build"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0"
  }
}
```

## Vitest

### インストール

**JavaScript** / **TypeScript** 共に同一のパッケージで対応できる。

```terminal
$ npm install -D vitest @testing-library/react @testing-library/jest-dom
$ npm install -D @testing-library/user-event jsdom
```

### 設定

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/setupTests.js',
  },
});
```

### セットアップファイルの作成

```js
# src/setupTests.js
import '@testing-library/jest-dom'
```

### スクリプトの追加

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "coverage": "vitest run --coverage"
  }
```

### TypeScriptへの対応

`tsconfig.app.json` の `types` キーの配列値に `"vitest/globals"` を追加する。

```json
// src/tsconfig.app.json
{
  "compilerOptions": {
    ...
    "types": ["vite/client", "vitest/globals"],
    ...
}
```

## Vitest

`describe()`
: テストをグループ化する。

`test()`
: 単一のテストを定義する。

`expect()`
: アサーションの作成に用いる。

## React Testing Library

## AAAパターン

Arrange（準備）→ Act（実行）→ Assert（検証） の3ステップで書く

```js
it('ユーザー名を表示する', () => {
  // Arrange — テストの準備
  const user = { name: '田中太郎', age: 25 };

  // Act — テスト対象の操作を実行
  const result = formatUserName(user);

  // Assert — 結果を検証
  expect(result).toBe('田中太郎 (25歳)');
});
```

### シンプルなコンポーネントのテスト

`render()`
: **React** 要素を受け取り、仮想DOMにレンダリングする。

`screen`
: レンダリングされた出力内の要素を検索するためのクエリメソッドを提供するオブジェクト。

### 一般的なアサーション

| アサーション                 | 機能                                                       |
| :--------------------------- | :--------------------------------------------------------- |
| `toBeInTheDocument()`        | 要素がドキュメント内に存在するかを確認                     |
| `toHaveTextContent(text)`    | 要素に指定されたテキストコンテンツが含まれていることを確認 |
| `toBeVisible()`              | 要素が現在ユーザに表示されているかを確認                   |
| `toBeDisabled()`             | フォーム要素が無効になっているかを確認                     |
| `toBeEnabled()`              | フォーム要素が有効になっているかを確認                     |
| `toHaveValue(value)`         | input/select/textarea各要素の現在の値を検証                |
| `toHaveBeenCalled()`         | モック関数が少なくとも1回呼び出されたか確認                |
| `oHaveBeenCalledTimes(n)`    | モック関数が正確にn回呼び出されたか確認                    |
| `toHaveBeenCalledWith(args)` | モック関数が特定の引数で呼び出されたか確認                 |

### ユーザーインタラクションのテスト

- `@testing-library/user-event` をインポートする
- `const user = userEvent.setup();` 操作をシミュレートするための初期化コード

インタラクティブなコンポーネントとテスト

```js
// src/components/Counter.jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}

export default Counter;
```

```js
// src/components/Counter.test.jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, test, expect } from 'vitest';
import Counter from './Counter';

describe('Counter', () => {
  test('starts with count of 0', () => {
    render(<Counter />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });

  test('increments count when increment button clicked', async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.click(screen.getByText('Increment'));

    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });

  test('decrements count when decrement button clicked', async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.click(screen.getByText('Decrement'));

    expect(screen.getByText('Count: -1')).toBeInTheDocument();
  });
});
```

### mockテスト

関数の疑似実装を用いてテスト対象のコンポーネントを依存関係から分離するテスト手法

- `import { vi } from 'vitest'` をインポートする
- `userEvent.setup()` イベント初期化後にモック関数を作成する
- `vi.fn()` でモック関数を作成する
- ユーザーイベントメソッドは、常に `async` (非同期)である

フォームコンポーネントとテスト

```js
// src/components/SearchBox.jsx
import { useState } from 'react';

function SearchBox({ onSearch }) {
  const [query, setQuery] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSearch(query);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Search..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <button type="submit">Search</button>
    </form>
  );
}

export default SearchBox;
```

```js
// src/components/SearchBox.test.jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, expect, vi } from 'vitest';
import SearchBox from './SearchBox';

describe('SearchBox', () => {
  test('calls onSearch with query when form submitted', async () => {
    const user = userEvent.setup();
    const mockOnSearch = vi.fn();

    render(<SearchBox onSearch={mockOnSearch} />);

    const searchInput = screen.getByPlaceholderText('Search...');
    const searchButton = screen.getByText('Search');

    await user.type(searchInput, 'React');
    await user.click(searchButton);

    expect(mockOnSearch).toHaveBeenCalledWith('React');
  });
});
```

### propsテスト

**props** を受け取るコンポーネントのテストは次のことを行う

- 異なるprops値で正しくレンダリングされること
- 欠落や向こうなどのエッジケースを適切に処理できるか確認

```js
// src/components/UserCard.jsx
function UserCard({ user, onEdit, onDelete }) {
  if (!user) {
    return <p>No user data available</p>;
  }

  return (
    <div className="user-card">
      <h3>{user.name}</h3>
      <p>Email: {user.email}</p>
      <p>Role: {user.role || 'Member'}</p>
      <button onClick={() => onEdit(user.id)}>Edit</button>
      <button onClick={() => onDelete(user.id)}>Delete</button>
    </div>
  );
}

export default UserCard;
```

```js
// src/components/UserCard.test.jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, test, expect, vi } from 'vitest';
import UserCard from './UserCard';

const mockUser = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
  role: 'Admin',
};

describe('UserCard', () => {
  test('renders user information correctly', () => {
    render(
      <UserCard
        user={mockUser}
        onEdit={vi.fn()}
        onDelete={vi.fn()}
      />
    );

    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('Email: john@example.com')).toBeInTheDocument();
    expect(screen.getByText('Role: Admin')).toBeInTheDocument();
  });

  test('shows default role when role not provided', () => {
    const userWithoutRole = { id: 2, name: 'Jane', email: 'jane@example.com' };
    render(
      <UserCard
        user={userWithoutRole}
        onEdit={vi.fn()}
        onDelete={vi.fn()}
      />
    );

    expect(screen.getByText('Role: Member')).toBeInTheDocument();
  });

  test('shows message when user is null', () => {
    render(
      <UserCard
        user={null}
        onEdit={vi.fn()}
        onDelete={vi.fn()}
      />
    );

    expect(screen.getByText('No user data available')).toBeInTheDocument();
  });
});
```

コールバックプロパティのテスト

- 内部状態を持つコンポーネントテストでは、目に見える変化をテストする

```js
describe('UserCard callbacks', () => {
  it('calls onEdit with user id when edit clicked', async () => {
    const user = userEvent.setup();
    const mockOnEdit = vi.fn();

    render(
      <UserCard
        user={mockUser}
        onEdit={mockOnEdit}
        onDelete={vi.fn()}
      />
    );

    await user.click(screen.getByText('Edit'));

    expect(mockOnEdit).toHaveBeenCalledTimes(1);
    expect(mockOnEdit).toHaveBeenCalledWith(1);
  });

  test('calls onDelete with user id when delete clicked', async () => {
    const user = userEvent.setup();
    const mockOnDelete = vi.fn();

    render(
      <UserCard
        user={mockUser}
        onEdit={vi.fn()}
        onDelete={mockOnDelete}
      />
    );

    await user.click(screen.getByText('Delete'));

    expect(mockOnDelete).toHaveBeenCalledWith(1);
  });
});
```
