---
tags:
  - Google
---

CLI のインストール

```terminal
$ curl -fsSL https://antigravity.google/cli/install.sh | bash
```

確認

```terminal
$ agy --version
```

```
$ agy --help
Usage of agy:
  --add-dir                       Add a directory to the workspace (repeatable) (default [])
  -c                              Short alias for --continue
  --continue                      Continue the most recent conversation
  --conversation                  Resume a previous conversation by ID
  --dangerously-skip-permissions  Auto-approve all tool permission requests without prompting
  -i                              Short alias for --prompt-interactive
  --log-file                      Override CLI log file path
  -p                              Short alias for --print
  --print                         Run a single prompt non-interactively and print the response
  --print-timeout                 Timeout for print mode wait (default 5m0s)
  --prompt                        Alias for --print
  --prompt-interactive            Run an initial prompt interactively and continue the session
  --sandbox                       Run in a sandbox with terminal restrictions enabled

Available subcommands:
  changelog       Show changelog and release notes
  help            Show help for subcommands
  install         Configure environment paths and shell settings
  plugin          Manage plugins (install, uninstall, list, enable, disable)
  plugins         Alias for plugin
  update          Update CLI
```

```
--add-dir               ワークスペースにディレクトリを追加します (繰り返し使用可能) (デフォルト [])
-c                      --continue のショートカット
--continue              直前の会話を続行します
--conversation          ID を指定して以前の会話を再開します
--dangerously-skip-permissions ツールの権限要求をプロンプトなしで自動承認します
-i                      --prompt-interactive のショートカット
--log-file              CLI ログファイルのパスを上書きします
-p                      --print のショートカット
--print                 プロンプトを一度だけ非対話的に実行し、応答を出力します
--print-timeout         印刷モードの待機タイムアウト (デフォルト 5分0秒)
--prompt                --print のショートカット
--prompt-interactive    最初のプロンプトを対話的に実行し、セッションを続行します
--sandbox               端末制限を有効にしたサンドボックスで実行します

利用可能なサブコマンド:
changelog     変更履歴とリリースノートを表示します
help          サブコマンドのヘルプを表示します
install       環境パスとシェル設定を構成します
plugin        プラグインの管理プラグイン（インストール、アンインストール、一覧表示、有効化、無効化）
plugins       プラグインのエイリアス
update         CLI の更新
```
