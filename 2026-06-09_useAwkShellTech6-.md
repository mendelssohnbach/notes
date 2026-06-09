---
tags:
  - awk
---

[シェル芸」に効く！ AWK処方箋](https://www.seshop.com/product/detail/22011)

# 配列・連想配列

## 配列と連想配列

`for` で配列の個数を数える。

```terminal
$ awk 'BEGIN {
  fruits[1] = "Apple";
  fruits[2] = "Orange";
  fruits[3] = "Banana";

  for (i in fruits) {
    num_fruits++;
  }
  print num_fruits;
}'
3
```

`for` で配列を順番に読み出す。

```terminal
$ awk 'BEGIN {
  fruits[1] = "Apple";
  fruits[2] = "Orange";
  fruits[3] = "Banana";

  for (i = 1; i <= 3; i++) {
    print i, fruits[i];
  }
}'
1 Apple
2 Orange
3 Banana
```

`length()` 関数で配列の個数を数える。

```terminal
$ awk 'BEGIN {
  fruits[1] = "Apple";
  fruits[2] = "Orange";
  fruits[3] = "Banana";

  for (i = 1; i <= length(fruits); i++) {
    print i, fruits[i];
  }
}'
1 Apple
2 Orange
3 Banana
```

`for ~in` で連想配列を読み出す。

```terminal
$ awk 'BEGIN {
  price_of["Apple"] = 100;
  price_of["Orange"] = 200;
  price_of["Banana"] = 300;

  for (i in price_of) {
    print i, price_of[i];
  }
}'
Orange 200
Apple 100
Banana 300
```

型がないので文字列でも動作する。

```terminal
$ awk 'BEGIN {
  fruits["1"] = "Apple";
  fruits["2"] = "Orange";
  fruits["3"] = "Banana";

  for (i = 1; i <= length(fruits); i++) {
    print i, fruits[i];
  }
}'
1 Apple
2 Orange
3 Banana
```

## リストがない

`split()` 関数でリストを代用する。

```terminal
$ awk 'BEGIN {
  fruit_list = "Apple Orange Banana";
  num_fruits = split(fruit_list, fruits);

  for (i = 1; i <= num_fruits; i++) {
    print i, fruits[i];
  }
}'
1 Apple
2 Orange
3 Banana
```

## 多次元配列

多次元配列は存在しない。

```terminal
$ awk 'BEGIN {
  fruits[1, 1] = "Fuji Apple";
  fruits[1, 2] = "Tsugaru Apple";
  fruits[2, 1] = "Blood Orange";
  fruits[2, 2] = "Mikan";

  for (i = 1; i <= 2; i++) {
    for (j = 1; j <= 2; j++) {
      print i, j, fruits[i, j];
    }
  }
}'
1 1 Fuji Apple
1 2 Tsugaru Apple
2 1 Blood Orange
2 2 Mikan
```

## 配列が空か

```terminal
$ awk 'BEGIN {
  price_of["Apple"] = 100;
  price_of["Orange"] = 200;
  price_of["Banana"] = 300;

  if ("Kiwi" in price_of) {
    print "Kiwi is not found.";
  }
}'
# 何も出力されない

$ awk 'BEGIN {
  price_of["Apple"] = 100;
  price_of["Orange"] = 200;
  price_of["Banana"] = 300;

  if ("Banana" in price_of) {
    print "Banana is not found?";
  }
}'
Banana is not found?
```

## シェル芸で配列

重複なく出力する。

```terminal
$ echo -e "aaa\nbbb\naaa" | awk '!a[$0]++'
aaa
bbb

# 一般的なコマンドで書き換え
$ echo -e "aaa\nbbb\naaa" | sort | uniq
```

重複を抜き出す。

```terminal
$ echo -e "aaa\nbbb\naaa" | awk 'a[$0]++ == 1'
aaa

# 一般的なコマンドで書き換え
$ echo -e "aaa\nbbb\naaa" | sort | uniq -d
aaa
```

## 最新 GUN AWK の正規表現

7章-10章を GNU awk の記事なので未評価です。

```terminal
$ git clone 'git://git.savannah.gnu.org/gawk.git'
$ cd gawk
$ ./configure && make
# make install
```

# コマンドを作る

## レコードとフィールド

```terminal
# スペース具切りをカンマ区切りに変換する
$ echo 'a b c d' | awk 'BEGIN {OFS = ","} {$1 = $1; print $0}'
a,b,c,d
```

## パターンとアクション

- パターンがない場合は、全レコードに対してアクション(処理)が行われる
- アクションがない場合は、 `{print $0}` が省略されたものとして処理される

```terminal
# アクションとパターンを記述
$ seq 1 10 | awk '$0 ~ /1/ {print $0}'
# パターンのみを記述
$ seq 1 10 | awk '$0 ~ /1/'
# パターンのみかつ `$0 ~` を省略
seq 1 10 | awk '/1/'
1
10
```

## コマンド群を作る

```terminal
# catコマンドの移植
$ echo 'a b c d' | awk '{print $0}'
a b c d
$ echo 'a b c d' | awk '99'
a b c d
```

```terminal
$ cat sample.txt
a b
c d

$ awk '{print NR, $0}' sample.txt
1 a b
2 c d
```

```terminal
# cutコマンドの移植
# 第3フィールドを抜き出す
$ echo 'a b c d' | cut -d ' ' -f 3
c

$ echo 'a b c d' | awk '{print $3}'
c

# 第2フィールドから第4フィールドまで抜く出す
$ echo 'a b c d' | cut -d ' ' -f '2-4'
b c d
$ echo 'a b c d' | awk '{for (i = 2; i <= 4; i++) {printf("%s ", $i);}} END {print ""}'
b c d
```

```terminal
# headコマンドの移植
$ seq 1 100 | head > /dev/null
$ echo ${PIPESTATUS[@]}
0 0
$ seq 1 100 | awk 'NR <= 10 {print $0}' > /dev/null
$ echo ${PIPESTATUS[@]}
0 0
```

```terminal
# grepコマンドの移植
# 正規表現で 1 を含む文字列を抜き出す
$ seq 1 10 | awk '/1/'
1
10

$ echo 'a b c d' | grep -o 'a'
a
$ echo 'a b c d' | awk 'match($0, /a/) {print substr($0, RSTART, RLENGTH)}'
a
```

```terminal
# trコマンドの移植
$ echo 'a b c d' | awk -v before='a' -v after='A' '{sub(before, after, $0); print $0}'
A b c d
$ echo 'a b c d' | awk -v before='a' -v after='A' 'sub(before, after)'
A b c d
```

```terminal
# seqコマンドの移植
$ seq 1 10 > /dev/null
$ awk -v start=1 -v end=10 'BEGIN {i = start; while (i <= end) {print i++}}' > /dev/null
```

```
# wcコマンドの移植
$ echo 'This is a pen.' | wc -w
4
$ echo 'This is a pen.' | awk '{n += NF} END {print n}'
4
$ echo 'これはぺんです。' | wc -c
25
$ echo 'これはぺんです。' | awk '{print length($0)}'
25
```

```terminal
# bcコマンドの移植
$ echo '2 / 3' | bc
0
$ echo '2 / 3' | bc -l
.66666666666666666666
$ echo '2 / 3' | awk '{system("awk \047 BEGIN {print " $0"}\047")}'
0.666667
```

```terminal
# dateコマンドの移植
$ awk 'BEGIN {"date \047+%Y/%m/%d %H:%M:%S\047" | getline date; print date}'
2026/06/09 16:10:45
```

```terminal
# uniqコマンドの移植 重複を削除
$ echo -e 'c\na\nb\na' | awk '!a[$0]++ {print $0}'
c
a
b
```
