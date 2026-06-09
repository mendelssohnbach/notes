---
tags:
  - awk
---

# シェル芸に効く

[「シェル芸」に効く！ AWK処方箋](https://www.seshop.com/product/detail/22011)

# さわってみよう

## パターンとアクション

- パターンを省略した場合: 全てのレコードに対してアクションを実行
- アクションを省略した場合: パターンにマッチしたレコードを表示
- パターン/アクション共に省略: 何も表示しない

## 真偽の判定

| 条件式   | 判定 |
| :------- | :--- |
| `0`      | 偽   |
| 空文字   | 偽   |
| 上記以外 | 真   |

```terminal
# アクションがない
# 数値0がパターンの場合
$ echo "foo" | awk '0'  # 何も表示されない
# 数値4がパターンの場合
$ echo "foo" | awk '4'
foo
# 文字列の0がパターンの場合
$ echo "foo" | awk '0""' # 数値の後に "" で文字列扱い
foo
# 文字列がパターンの場合
$ echo "foo" | awk '"bar"'
foo
```

## 比較演算子、マッチング演算子

```terminal
$ echo "foo" | awk '$0 == "foo"'
foo
# 真の場合 1 が返される
$ echo "foo" | awk '{ print $0 == "foo" }'
1
# 偽の場合 0 が返される
$ echo "foo" | awk '{ print $0 != "foo" }'
0
```

正規表現は `~ /STRING/` と記述する。

```terminal
# マッチング演算子
$ echo "foo" | awk '$0 ~ /foo/'
foo
# 正規表現に限り `$0 ~` を省略できる
$ echo "foo" | awk '/foo/'
foo

# 真の場合 1 が返される
$ echo "foo" | awk '{print $0 ~ /foo/}'
$ echo "foo" | awk '{print /foo/}' # 省略表記
1
# 偽の場合 0 が返される
$ echo "foo" | awk '{print $0 ~ !/foo/}'
$ echo "foo" | awk '{print !/foo/}' # 省略表記
0
```

## 代入演算子

破壊的代入が可能である。

```terminal
$ echo "a b c" | awk '{$2 = "B"; print $0}'
a B c
# 文字列は " でクォートする
$ echo "a b c" | awk '{$1 = "えー"; $2 = "β"; $3 = "C"; print $0}'
えー β C
```

削除の場合は、代入の戻り値は左辺値である。

```terminal
# 削除
$ echo "a b c" | awk '{$2 = ""; print $0}'
a  c
# 代入の戻り値が代入の真偽でない場合
$ echo "a b c" | awk '$2 = ""' # 何も返されない
# 代入値を確認
$ echo "a b c" | awk '{print $2 = "B"}'
B
$ echo "a b c" | awk '{print $2 = ""}' # 何も返されない
$ echo "a b c" | awk '!($2 = "")'
a  c
```

## 変数

| 組み込み変数 | 機能         | フルネーム       |
| :----------- | :----------- | :--------------- |
| `NR`         | レコード数   | Number of Record |
| `NF`         | フィールド数 | Number of Field  |

```terminal
$ echo "a b c" | awk 'NF == 3'
a b c
$ echo "\n a \n b \n \n" | awk 'NF'
\n a \n b \n \n # 改行が認識されない
$ echo -e "\n a \n b \n \n" | awk 'NF' # -e オプションが必要
 a
 b
$ echo -e "¥n a ¥n b ¥n ¥n" | awk 'NF'
```

## 関数

関数の戻り値は1つ

`length` : 文字数を返す

```terminal
$ echo -e "\n a \n b \n \n" | awk 'length'
a
 b
  # スペースが出力される


```

`sub()` : 置換に成功した1を返し、失敗時に0を返す

`gsub()` : 置換に成功した個数を数で返す

```terminal
$ echo "a b c" | awk '{print sub(/a/, "")}' # aの置換に成功する
$ echo "a b c" | awk '{print gsub(/a/, "")}' # aを何個置換できるか
1
$ echo "ab ba ab" | awk '$0 = gsub(/ab/, "")'
2
# 置換に失敗する場合は0を返すが0は偽なのでなにも返さない
$ echo "ab ba ab" | awk '$0 = gsub(/ac/, "")'
# ダブルクォートを使って失敗した結果数を返す
$ echo "ab ba ab" | awk '$o = gsub(/ac/, "") ""'
0
```

`split` : 分割個数を戻す

```terminal
# 3分割の1つ目を表示
$ echo "ab ba ab" | awk 'split($0, arr) {print arr[1]}'
ab
# 3分割の2つ目を表示
$ echo "ab ba ab" | awk 'split($0, arr) {print arr[1]}'
ba
```

# 行操作がカギ

行=レコードと呼ぶ  
組込変数 **RS** (Record Separator) で区切られたチャンクをレコードとして扱う。デフォルトでは改行が区切り。

## 直接行を指定

```terminal
# seqコマンドで1-10までの連続値を改行付きで作成
# N行目を出力
$ seq 1 10 | awk 'NR == 1 {print $0}'
$ seq 1 10 | awk 'NR == 1'
1
$ seq 1 10 | awk 'NR == 3'
3
```

## 先頭10行を抜き出す

```terminal
# 組込変数NRが10以下で真となるものを返す
$ seq 5 100 | awk 'NR <= 10'
5
6
...
13
14
# 組込変数NRが10以上かつ12以下で真となるものを返す
$ seq 5 100 | awk 'NR >= 10 && NR <= 12'
14
15
16

# headコマンドとの挙動の違い
$ seq 1 10000000 | head > /dev/null
$ echo ${PIPESTATUS[@]}
141 0
# max値まで処理後にパイプに渡さ、すべての行に対してawkが走る
$ seq 1 10000000 | awk 'NR <= 10' > /dev/null
$ echo ${PIPESTATUS[@]}
0 0
```

## 末尾10行を抜き出す

```terminal
# 行数分だけ配列が作成される
$ seq 1 100 | awk '{a[NR] = $0} END {for (i = NR -10; i <= NR; i++) print a[i]}'
# リングバッファを使う。作成される配列は10個
$ seq 1 100 | awk '{a[NR % 10] = $0} END {for (i = NR + 1; i <= NR + 10; i++) print a[i % 10]}'
91
...
99
100

# tacコマンドで逆順のawkにパイプ
# 結果を再びtacで元の並び順に戻す
$ seq 1 100 | tac | awk 'NR <= 10' | tac
```

## 範囲指定

- `&&` : 両方ともに真
- `||` : どちらかが真

```sql
$ seq 1 100 | awk 'NR >= 10 && NR <= 20'
10
...
19
20
$ seq 1 100 | awk 'NR == 10 || NR == 20 || NR == 30'
10
20
30
```

## 範囲指定演算子

`,` : 範囲指定演算子

```sql
$ seq 1 100 | awk 'NR == 14, NR == 17'
14
15
16
17
$ seq 1 10 | awk 'NR % 2 == 0, NR % 3 == 0'
2
3
4
5
6
8
9
10
```

## マッチ行を抜き出す

```sql
# 2または3にマッチ
$ seq 1 10 | awk '/[23]/'
2
3
# 7が先に出力されない
$ seq 1 10 | awk '$0 ~ /[23]/'
$ eq 1 10 | awk '/[72]/' # 省略形
2
7
```

## マッチしない行を抜き出す

`!` : 否定演算子

```terminal
$ seq 1 10 | awk '!/[23]/'
1
4
5
6
7
8
9
10
```

## psコマンドとの連携

```terminal
-- すべてのプロセスの中から awk 行を抜き出す
-- grep が結果に混入するのを防止
-- 1列名(プロセスID)を表示
$ ps ax | grep 'awk' | grep -v 'grep' | awk '{print $1}'
414709
# 正規表現を使って書き換え
$ ps ax | awk '/[a]wk/ {print $1}'
$ ps ax | awk '/awk/ {print $1}' # 同じ意味
```

## 1行目からマッチした行まで

```terminal
# topコマンドをバッチモードで1回実行
# ^$=空行で偽となり先頭部分を表示
$ top -b -n 1 | awk 'NR == 1, /^$/'
top - 18:55:22 up 6 days,  3:13,  1 user,  load average: 1.60, 1.42, 1.16
Tasks: 367 total,   2 running, 365 sleeping,   0 stopped,   0 zombie
%Cpu(s): 12.5 us,  9.4 sy,  0.0 ni, 76.6 id,  1.6 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  15664.7 total,    536.5 free,   8408.9 used,   8516.6 buff/cache
MiB Swap:   4096.0 total,   4021.3 free,     74.6 used.   7255.8 avail Mem
```

## マッチした行から最後まで

```terminal
$ top -b -n 1 | awk '/^$/, 0'

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
2242479 jack      20   0 1448.3g 436540 170960 S  30.8   2.7  60:04.00 chrome
...
2422484 jack      20   0    5264   2392   2244 S   0.0   0.0   0:00.00 awk
```

## getline

**getline**

- 引数をとる
- その引数に値をわたし真偽値を返す
- **NR** が定義されないことがある
- `close()` 文を使う
- `-` 記号はファイル名の一種で標準入力を示す

```awk
# nr.awk
BEGIN {
  while (getline < "-" > 0) {
    print "NR = " NR;
  }
  close("-");
}

# Number Record はすべて0
# 自分で行番号をカウントすること
$ seq 1 10 | awk -f nr.awk
NR = 0
...
NR = 0

BEGIN {
  while (getline < "-" >0) {
    print "NR = " ++nr;
  }
  close("-");
}

$ seq 1 10 | awk -f nr.awk
NR = 1
...
NR = 10
```

# 列を操る基礎テクニック

## フィールドとは

- 1つのレコードを組込変数 **FS** (Field Separator) で分割したチャンク
- レコードの先頭から $1 $2 と分割される
- **NF** (Number of Filed) 現在処理中のレコードのフィールド数
- レコード末尾から $NF, $(NF-1) と定義される

| 文節1   | 文節2   | 文節3   | 文節4 |
| :------ | :------ | :------ | :---- |
| This    | is      | a       | pen   |
| $1      | $2      | $3      | $4    |
| $(NF-3) | $(NF-2) | $(NF-1) | $NF   |

`,` 区切りを扱う

```terminal
$ echo "a,b,c" | awk -F',' '{print $2}'
b
$ echo "a,b,c" | awk -F',' '{print $(NF -1)}'
b
```

```terminal
# TAB記号+文字列
$ echo "  a b c" | awk '{print $2}'
b
# スペース+TAB記号+文字列
# 区切り記号として スペース、タブ
$ echo "  a b c" | awk -F'[ \t]+' '{print $2}'
a
# TAB記号+スペース+文字列
$ echo "  a b c" | awk -F'[ \t]+' '{print $2}'
a
# 組込変数 FS のデフォルトはスペース1つ
$ echo "  a b c" | awk -F' ' '{print $2}'
b
```

## 指定フィールドを抜き出す

```terminal
$ echo "a b c" | awk '{print $2}'
b
# 代入値が真であれば {print $0} を省略可能
$ echo "a b c" | awk '$0 = $2'
b
# 0が代入値となり、偽。printを指定しないと
$ echo "0 1 2" | awk '$0 = $1' # 何も表示されない
$ echo "0 1 2" | awk '{print $1}'
0
```

正規表現の利用

| 組込変数    | 可否 |
| :---------- | :--- |
| 組込変数 RS | 否   |
| 組込変数 FS | 可   |

```terminal
# 区切り記号として スペース、コロン、スラッシュ
$ echo "2030/01/15 12:30:47" | awk -F'[ :/]' '{print $2}'
01
$ echo "2030/01/15 12:30:47" | awk -F'[ :/]' '{print $NF}'
47
```

## 指定フィールドを消す

```terminal
$ echo "a b c" | awk '{$2 = ""; print $0}'
a  c # スペースを2つ含んでいる
$ echo "a b c" | awk '!($2 = "")'
a  c
# 必要なフィールド番号を列挙
$ echo "a b c" | awk '{print $1, $3}'
a c
```

## 指定範囲でフィールドを抜き出す

`for` または `while` を用いる

```terminal
$ echo "a b c" | awk '{for (i = 2; i <= NF; i++) a = a OFS $i; print a}'
 b c # 行頭にスペースを含む
$ echo "a b c" | awk '{i = 2; while (i <= NF) a = a OFS $(i++); print a}'
 b c
```

## フィールドの計算

横方向の集計を行う。

```
$ echo "1 2 3" | awk '{for (i = 1; i <= NF; i++) sum += $i; print sum}'
6
# スペースを+記号に置換: 1+2+3
# テキスト形式の計算式を実行
$ echo "1 2 3" | tr ' ' '+' | bc
6
```

```terminal
# BMIを計算
$ echo "54 170" | awk '{print $1 / ($2 / 100) ^ 2}'
18.6851
```

## フィールド再構築

`$1 = $1` : この記法を **フィールド再構築** と呼ぶ

```terminal
$ echo "a,b,c" | awk -F',' -v OFS=' ' '$1 = $1'
a b c
$ echo "a,b,c" | awk -F',' -v OFS=' ' '{$1 = $1; print}'
a b c
# OFSで \ バックスラッシュを使うときは、エスケープが必要
$ echo "path/to/directory/files/file" | awk -F'/' -v OFS='\\' '{$1 = $1; print}'
path\to\directory\files\file
```

# 文字列関数

## 文字列の抜き出し

`substr()` : 文字目の N文字目から抜き出す

```terminal
# 2文字目から抜き出す
$ echo 'abcde' | awk '{print substr($0, 2)}'
$ echo 'abcde' | awk '{print substr($0, 2)}'
# echo "abcde" | awk '$0 = substr($0, 2)' # 短縮形
bcde

# 第3引数で抜き出す文字数を指定
$ echo 'abcde' | awk '{print substr($0, 2, 2)}'
bc
```

`index()` : 文字列に含まれる任意の文字以降を取り出し、その位置を返す。

```
$ echo 'abcde' | awk '{print substr($0, index($0, "b"))}'
bcde
$ echo 'abcdbe' | awk '{print substr($0, index($0, "b"))}'
bcdbe
```

## 文字列の検索

`match()` : 第1引数に指定した文字列に対して、第2引数に正規表現を指定する。組込変数 **RSTART** にマッチした最初の位置、組込変数 **RLENGTH** にマッチした長さを格納する。

```terminal
$ echo 'abcde' | awk 'match($0, /b.*/)'
abcde
$ echo 'abcde' | awk 'match($0, /b.*/) {print RSTART, RLENGTH}'
2 4
# 正規表現にマッチした部分だけ抜き出す
$ echo 'abcde' | awk 'match($0, /b.*/) {print substr($0, RSTART, RLENGTH)}'
bcde
# grepコマンドで書き換え
$ echo 'abcde' | grep -o 'b.*'
bcde
```

## 置換

`sub()` : 最初にマッチした文字列のみ置換

`gsub()` : マッチしたすべての文字列を置換

```terminal
# sub関数による部分置換
$ echo 'abcde' | awk '{sub(/./, "A"); print $0}'
Abcde
$ echo 'abcade' | awk '{sub(/./, "A"); print $0}'
Abcade

# gsub関数による全部置換
$ echo 'abcde' | awk '{gsub(/./, "A"); print $0}'
AAAAA
```

## 連結

```terminal
$ echo 'abcde' | awk '{print $0 "fghij"}'
abcdefghij
```

`sprintf()` と `printf()` を用いたフォーマット指定

```terminal
$ echo 'abcde' | awk '{print sprintf("%s%s", $0, "fghij")}'
abcdefghij
$ echo 'abcde' | awk '{printf("%s%s\n", $0, "fghij")}'
abcdefghij
```

## 大文字小文字変換

`toupper()` ; 大文字に変換、 `tolower()` : 小文字に変換

```terminal
$ echo 'abcde' | awk '{print toupper($0)}'
ABCDE
$ echo 'ABCDE' | awk '{print tolower($0)}'
abcde

# 大文字小文字を無視した条件分岐を行う
$ echo 'Awk' | awk 'tolower($0) == "awk"'
Awk
```

## 区切り

`;;` : 区切り 動作しない

```terminal
$ seq 1 5 | awk '/1/ ;; /2/'
awk: line 1: syntax error at or near ;
```

## 四則演算と数値演算関数

数字は倍精度浮動書数点数として扱われる。

```terminal
$ echo '1 2 3 4 5' | awk '{print $1 + $2 - $3 * $4 / $5}'
0.6
```

自作関数 `calc()` を `bash_aliases` に登録して利用する。

```terminal
# ~/.bash_aliases
calc() {
	awk 'BEGIN {print $*}'
}
$ source ~/.bashrc
$ calc '1 + 2 - 3 * 4 / 5'
0.6
```

## 三角関数

`sin()` と `cons()` はあるが `tan()` はない。角度の単位はラジアンです。

```terminal
$ awk 'BEGIN {print sin(1) / cos(1)}'
1.55741
$ calc 'sin(1) / cos(1)'
1.55741
$ awk 'BEGIN {print 90 * 3.14 / 180}'
1.57
```

`atan2()` : 円周率を計算する。

```terminal
$ awk 'BEGIN {print atan2(0, -0)}'
3.14159
# 90度のラジアンを求める
$ awk 'BEGIN {print 90 * atan2(0, -0) / 180}'
1.5708
```

## 対数、指数

`log()` : 対数関数

```terminal
# 100は10の何乗か
$ awk 'BEGIN {print log(100) / log(10)}'
2
```

`exp()` : 指数関数

```terminal
$ awk 'BEGIN {print exp(1)}'
2.71828
```

## 乱数

`rand()` : 乱数関数

```terminal
$ awk 'BEGIN {print rand()}'
0.462047
$ awk 'BEGIN {print rand()}'
0.972689

# サイコロを作る
$ awk 'BEGIN {print int(rand() * 6) + 1}'
3
$ wk 'BEGIN {print int(rand() * 6) + 1}'
6
```

`srand()` : 乱数のシードを初期化する。

```terminal
$ awk 'BEGIN {srand(); print int(rand() * 6) + 1}'
5
$ awk 'BEGIN {print srand() * srand()}'
3171827169947655168 # 1970年1月1日からの経過秒数
```

## 整数化

`int()` : 整数部を表示する。

```terminal
$ awk 'BEGIN {print int(3.1415)}'
3

# 四捨五入
$ awk 'BEGIN {a = 1.5; print int(a + 0.5)}'
2
```

## 2進数に起因する問題

```terminal
# 2進数では70.21=70.2099なので 70.20 扱い
$ awk 'BEGIN {print int(70.21 * 100)}'
7020
# perlなどの他言語でも同様
$ perl -e 'print int(70.21 * 100) . "\n"'
7020
```

## どこまで計算できるか

53ビットまで正しく扱える。

```terminal
$ awk 'BEGIN {printf("%d\n", 2 ^ 53 - 1)}'
9007199254740991
# 2のN乗-1は必ず奇数になるので正しくない。
$ awk 'BEGIN {printf("%d\n", 2 ^ 54 - 1)}'
18014398509481984
```
