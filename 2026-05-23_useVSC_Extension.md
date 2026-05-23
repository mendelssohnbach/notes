---
tags:
  - VSCode
---

# REST Client

- ファイル拡張子: `.http` または `.rest`
- 各リクエストは `###` で区切る
- コメントは行頭を `/`
- `Content-Type` 行は直前で改行が必要
- `{ ... }` リクエストボディ部は直前に空行が必要
- 変数宣言は `@` を使う
- 変数の利用時は `{}` は不要。自動で補完される
- 変数に代入する値は空白を含んでいてもクォートで囲まない

## GET リクエスト

```txt
GET https://jsonplaceholder.typicode.com/posts/1
```

## POST リクエスト

```txt
POST https://jsonplaceholder.typicode.com/posts
Content-Type: application/json

{
  "title": "example title",
  "body": "abc def ghi",
  "userId": 1
}
```

## 変数の利用1

```txt
@baseurl = https://jsonplaceholder.typicode.com
@userId = 1

GET {{baseurl}}/posts/{{userId}}
```

## 代入値に空白を含む場合

```txt
@json = Content-Type: application/json

POST {{baseurl}}/posts
{{json}}

{
  "title": "title2",
  "body": "body body body",
  "userId": 10
}
```
