---
tags:
  - Github
---

# Projectsの利用

## プロジェクト機能の操作権限を追加する。

```terminal
$ gh auth refresh -s project

! First copy your one-time code: 4桁-4桁
# 4桁-4桁コードは使い捨てコードをブラウザにペースト
```

## プロジェクトを作成する

```terminal
# プロジェクトのタイトルを指定: --title "TITLE_NAME"
$ gh project create --owner "@me" --title "技術書読書：ログの教科書"
https://github.com/users/mendelssohnbach/projects/2
# 作成したプロジェクトのID番号が大切: 2
```

発行された番号「2」を環境変数にセット

```terminal
$ export PROJECT_NUM=2

# セットした環境変数の確認
$ echo $PROJECT_NUM
2
```

## タスクを登録する

タスク作成用のスクリプトを作成する

# エイリアスファイルに関数を追加

```terminal
cat << 'EOF' >> ~/.bash_aliases

# GitHub Project タスク作成用の自作関数

create_project_task() {
if [ -z "$1" ]; then
echo "エラー: タイトルを指定してください。(例: create_project_task \"タイトル\")"
return 1
fi

if [ -z "$PROJECT_NUM" ]; then
echo "エラー: 環境変数 PROJECT_NUM が設定されていません。"
return 1
fi

# 改行コードを含んだ変数定義（ネストを回避）

local body_text="### 💡 得られた知見"$'\n'"- "$'\n'"### ❓ 疑問点・深掘りしたいこと"$'\n'"- "

gh project item-create "$PROJECT_NUM" --owner "@me" \
    --title "$1" \
    --format json \
    --body "$body_text"
}
EOF
```

# 設定を現在のターミナルに即時反映

```terminal
$ source ~/.bash_aliases
```

```terminal
# タイトルを指定して関数を実行
create_project_task "テストタスク：正常に作成できるかな？"


# タスクを作成完了
{"id":"PVTI_...","title":"テストタスク：正常に作成できるかな？","type":"DraftIssue"}
Created item
```

作成済みのアイテムIDを表示する

```terminal
$ gh project item-list $PROJECT_NUM --owner "@me"

# 出力例
ID                                    TITLE                       TYPE   STATUS
PVTI_...A         第1章：ログの重要性と基本概念  Issue  Todo
PVTI_...B         第2章：ログの出力設計とフォーマット  Issue  Todo
PVTI_...C         第3章：ログの収集とストレージ  Issue  Todo
```

## プロジェクトをブラウザで開く

```terminal
$ gh project view $PROJECT_NUM --owner "@me" --web
```

## プロジェクトを削除

```terminal
$ gh project delete $PROJECT_NUM --owner "@me"

# 不要になった環境変数の削除
$ unset PROJECT_NUM
# セットした環境変数の確認
$ echo $PROJECT_NUM
 # 何も表示されない
```
