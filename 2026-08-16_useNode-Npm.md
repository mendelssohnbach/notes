---
tags:
  - Dev
---

## NPM

'type: "module` をデフォルトにする

```terminal
$ cd ~
$ micro .npm-init.js
```

`.npm-init.js`

```js
module.exports = {
  name: prompt('package name', process.cwd().split('/').pop()),
  version: '1.0.0',
  description: '',
  main: 'index.js',
  scripts: {
    test: 'echo "Error: no test specified" && exit 1',
  },
  type: 'module',
  keywords: [],
  author: '',
  license: 'ISC',
};
```

動作確認

```terminal
$ npm init -y
```

コマンドで解決するには

```terminal
$ npm init -y && npm pkg set type=module
```
