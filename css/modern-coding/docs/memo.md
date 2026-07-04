# Webページデザイン

[作って学ぶ　HTML＆CSSモダンコーディング](https://book.mynavi.jp/ec/products/detail/id=124054)
[サポートサイト](https://book.mynavi.jp/supportsite/detail/9784839977115.html)
[訂正情報](https://book.mynavi.jp/files/user/support/9784839977115/9784839977115_teisei.html)
[レポジトリ](https://github.com/ebisucom/html-css-modern-coding)

`©` : まるしーで変換できる

## 画面サイズ

| 設定値        | デバイス                | 理由                                    |
| :------------ | :---------------------- | :-------------------------------------- |
| 576px         | 大型スマホ/横向きスマホ | Bootstrapなど主要なフレームワークが採用 |
| 768px         | 横向きタブレット        | iPdaのサイズ                            |
| 992px/1024px  | 小型PC                  | 古いノードPCのサイズ                    |
| 1200px/1400px | 一般的なPC              | 2カラム/3カラムレイアウトでの表示       |

## Webサービス

[レスポンシブ画像ジェネレータ](https://www.responsivebreakpoints.com/)
[JPGからWebP変換](https://convertio.co/ja/jpg-webp/)

## html

**Google** フォントの利用

```html
<link
  href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400&display=swap"
  rel="stylesheet"
/>
```

**font awesome** の利用

```html
<link
  href="https://use.fontawesome.com/releases/v5.15.3/css/all.css"
  integrity="sha384-SZXxX4whJ79/gErwcOYf+zWLeJdY/qpuqC4cAa9rOGUstPomtqpuNWT9wdPEn2fk"
  crossorigin="anonymous"
  rel="stylesheet"
/>
```

コンテナとアイテムの2重構造としデザイン配置を把握しやすくする

```html
<!-- 2重構造の外側のボックス -->
<div class="header-container w-container">
  <!-- 2重構造の内側のボックス -->
  <div class="site">
    <a href="index.html">
      <img
        src="img/logo.svg"
        alt="Boards"
        width="135"
        height="26"
      />
      <span class="sr-only">ホーム</span>
    </a>
  </div>
</div>

<!-- 2重構造の外側のボックス -->
<section class="hero">
  <!-- 2重構造の内側のボックス -->
  <div class="hero-container w-container">
    <h1>Stationary Services</h1>
    <p>便利な道具とサービスをお届けします</p>
    <a href="#">無料ではじめる</a>
  </div>
</section>
```

記事一覧は3重構造

```html
<!-- 記事一覧: 3重構造 -->
<!-- 全ての記事一覧を内包する外枠 -->
<section class="posts">
  <!-- 見出しや記事の横幅/左右の空白を調整する中枠 -->
  <div class="w-container">
    <h2>News Releases <span>最新情報</span></h2>
    <!-- 実際の記事を掲載する内枠 -->
    <div class="posts container">
      <article class="post">
        <a href="#">
          <figure>
            <img
              src="img/news01.jpg"
              alt=""
              width="1000"
              height="750"
            />
          </figure>
          <h3>スパンコール</h3>
          <p>キラキラと光を反射する装飾素材です。いつもの道具にアクセントをつ けます。</p>
        </a>
      </article>
    </div>
  </div>
</section>
```

装飾的な画像の場合は `alt` 属性は空でよい

```html
<img
  src="img/tool.jpg"
  alt=""
  width="1600"
  height="1260"
/>
```

## css

min()
: 比較関数。カンマ区切りで指定した値の中から最小になる値を返す

` width: min(92%, 1166px);`
: 92%の算出値が1166ピクセルより小さい場合は92%、大きい場合は1166pxを返す

```css
.w-container {
  width: min(92%, 1166px); /* 最大幅: 1166px */
  margin: auto; /* 左右の余白を均等にし中央寄せする */
}
```

`img` タグに対して `display: block` とすると画像の下に余計な余白が入るのを防ぐ

```css
img {
  display: block;
}
```

スクロールしてもヘッダーを画面上部に常に表示する

```css
.header {
  position: sticky;
  top: 0; /* 画面上辺に揃える */
  z-index: 10; /* 他のパーツよりも上に表示 */
}
```

`.hero` がgridコンテナの親要素で `.hero-container` がgiroコンテナ。 `.hero-container` で `height: 100%` とすることで親要素の高さに合わせる。親要素の高さはここでは、 `height: 650px`

`background-size: cover` 背景画像の横幅比を維持したまま、要素の表示領域全体を覆い尽くすように拡大または縮小する。 `img/hero.jpg` の幅1600px高さ1200px

```css
.hero {
  height: 650px;
  background-image: url(img/hero.jpg);
  background-position: center;
  background-size: cover;
}

.hero-container {
  display: grid;
  justify-items: center;
  align-content: center;
  height: 100%;
}
```

| cssプロパティ          | 機能               |
| :--------------------- | :----------------- |
| `justify-item; center` | ボックスを中央揃え |
| `text-align: center`   | テキストを中央揃え |

`clamp(最小値, 推奨値, 最大値)`

- 最小値: 画面が狭くなっても最小値未満にならない
- 推奨値: 画面幅の推奨値%を保つ
  - vw: ビューポート横幅
- 最大値: 画面が広くなっても最大値を超えない

`5vw` の求め方
: 68px÷1366x100=5vw(最大値÷サイト幅x100)

[Font-size Clamp Generator](https://clamp.font-size.app/)

```css
.hero h1 {
  font-size: clamp(48px, 5vw 68px);
}
```

`color: inherit` 親要素の色を引き継ぐ

```css
a {
  color: inherit;
}
```

`display: block` 幅と高さを自由に指定。ボタンのどこでもクリックに反応。上下の余白を正しく研鑽

`box-sizing: border-box;` 要素のサイズに `padding` と `border` を含む

```css
.btn {
  display: block;
  box-sizing: border-box;
}
```

`brightness(90%)` 明度を90%に指定。少し暗くなる

`contrast(120%)` コントラストを120%に指定。色にメリハリが出る

```css
a:hover {
  filter: brightness(90%) contrast(120%);
}
```

`max-width: 100%` 画像の幅を親要素に合わせる。オリジナルサイズ以上に拡大されるのを防ぐために `max-width` 指定。ここでの親要素の幅は可変で `width: min(92%, 1166px)`

`height: auto` で縦横比を維持

```css
img {
  max-width: 100%;
  height: auto;
}

.w-container {
  width: min(92%, 1166px); /* 最大幅: 1166px */
  margin: auto;
}
```

`padding: clamp(90px, 9vw, 120px) 0` 画面の横幅に合わせ上下の `padding` を動的に変化させる。左右の `padding` は0pxで固定

- 最小値: 90px
- 推奨値: 9vw(画面幅の9%)
- 最大値: 120px、左右の
- 計算式: 最大値÷画面幅x100

```css
.imgtext {
  padding: clamp(90px, 9vw, 120px) 0;
}
```

`.heading-decoration::after` 疑似要素。 `<h2 class="heading-decoration">日常のツールたち</h2>` タグの直後に新しく1つ要素を生成する。`content` プロパティを持たない疑似要素は画面に存在できない。 `content: ''` とすることで「中身は空っぽだがbox1つ作りだす。

`margin-top: 0.6em` 親要素のフォントサイス( ` font-size: clamp(30px, 3vw, 40px);` )の0.6倍

```css
.heading-decoration::after {
  display: block;
  content: '';
  width: 160px;
  height: 0;
  border-top: 1px solid #b72661;
  margin-top: 0.6em;
}
```

`.heading-decoration` クラスの直後の `p` タグ要素のみスタイルを適用

```css
.heading-decoration + p {...}
```

横幅768px以上でスタイルを適用

```css
@media (min-width: 768px) {
  ...
  }
```

横幅の占有率を指定する。テキスト1/3、イメージが2/3

`min-width: 17em` 現在のフォントサイズの17倍の幅に指定、結果として日本語環境なら17文字分に相当する

```css
.imgtext-container > .text {
  flex: 1;
  min-width: 17em;
}
.imgtext-container > .img {
  flex: 2;
}
```

`flex-direction: row-reverse` flexアイテムの行の並びを逆転

```css
.imgtext-container.reverse {
  flex-direction: row-reverse;
}
```

`.imgtext` クラスの直後の `.imgtext` クラスの上側余白を指定

```css
.imgtext + .imgtext {
  padding-top: 0;
}
```

`:root {}` htmlの最上位要素を指定

`--v-space: clamp(90px, 9vw, 120px)` サイト全体に提要される幅サイズを指定

`padding: var(--v-space) 0` 上下の余白に `--v-space` 幅を適用

```css
:root {
  --v-space: clamp(90px, 9vw, 120px);
}

.posts {
  padding: var(--v-space) 0;
  background-color: #f3f1ed;
}
```

`span` タグをブロックレベル要素に置き換える。デフォルでは `span` タグはインラインレベル要素

```css
.heading span {
  display: block;
}
```

`position: absolute` 親要素で宣言。小要素を通常の配置から切り離し浮いた状態にする

`position: absolute` 小要素で宣言。小要素を親要素の位置から絶対配置にする

`top: calc((var(--v-space) * -0.6em))` 親要素からの絶対配置位置

```css
.w-container {
  position: relative;
}

.heading {
  position: absolute;
  top: calc((var(--v-space) + 0.6em) * -1);
}
```

`display: grid` グリッドレイアウト

`@media (min-width: 768px) ` 768px未満は横2アイテム、768px以上は横3アイテム配置

`gap: 32px 25px` グリッドの行間32px/列間25pxの余白

```css
.posts-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 32px 25px;
}

@media (min-width: 768px) {
  .posts-container {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

`aspect-ratio: 3 / 2` 横幅 / 高さ。横幅3対高さ2の割合

`object-fit: cover` アスペクト比を崩さずに、中央を切り出す。 `aspect-ratio` との組み合わせで画像が歪んただり引き伸ばされるのを防ぐ

```css
.post img {
  aspect-ratio: 3 / 2;
  object-fit: cover;
  width: 100%;
}
```

`margin: 1em 0 0.5em` 上余白、左右余白、下余白の順番で宣言

```css
.post h3 {
  margin: 1em 0 0.5em;
}
```

`max-width: 20em` 最大幅を日本語横書き20文字とする

```css
.post p {
  max-width: 20em; /* 20文字分 */
  font-size: clamp(10px, 1.6vw, 14px);
  min-height: 0vw;
}
```

明度やコントラストの適用される範囲をリンク要素全体に適用するため。文字のみではなくブロック領域全体で有効になる

```css
a:hover {
  filter: brightness(90%) contrast(120%);
}

.post a {
  display: block;
}
```

`display: grid/place-items: center` Gridレイアウトで左右配置にし、中身を四角に対し上下左右の完全な中央に配置

`aspect-ratio: 1 / 1` 縦横比を1対1の正方形に固定

`clip-path: circle(50%)` 要素を半径50%の円形で切り抜く。これらの宣言で正方形が完全な正円になる

クリッピングパスの形状を指定する関数

| 関数      | 形状   |
| :-------- | :----- |
| inset()   | 四角形 |
| circle()  | 円形   |
| ellipse() | 楕円形 |
| polygon() | 多角形 |

[CSS clip-path maker](https://bennettfeely.com/clippy/)

```css
.footer-sns a {
  display: grid;
  place-items: center;
  width: 36px;
  aspect-ratio: 1 / 1;
  background-color: #cccccc;
  clip-path: circle(50%);
}
```

`aspect-ratio` プロパティに対応していないブラウザ向けのフォールバック

```css
@supports not (aspect-ratio: 1 / 1) {
  .footer-sns a {
    height: 36px;
  }
}
```

`flex-wrap: wrap` 要素が横一列に収まらない場合は自動で折返し複数行に並べる

`justify-content: center` 中央揃えにする

```css
.footer-menu {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
```

`.footer-container > .footer-sns` 親 `.footer-container` クラスの直下にある子 `.footer-sns` クラス

`grid-column: 2` 左から2列目に配置

`grid-row: 1 / 4` 上から数えて1行目から4行目の縦に配置

`justify-self: end` 横方向の右端に配置

`align-self: center` 縦方向の中央に配置

```css
.footer-container > .footer-sns {
  grid-column: 2;
  grid-row: 1 / 4;
  justify-self: end;
  align-self: center;
}
```

`.footer-container > .footer-site` 親 `footer-container` クラスの直下にある子 `footer-site` クラス

```css
.footer-container > .footer-site {
  margin-bottom: 20px;
}
```

`.footer-container > *:not(.footer-sns)` 親 `footer-container ` クラスの直下の 子 `footer-sns` クラスを除く全ての要素

`justify-self: start` 横方向の左端に配置

```css
.footer-container > *:not(.footer-sns) {
  justify-self: start;
}
```

`max-width: 100%` 画像が枠より大きい時は、枠に合わせて縮小し、枠より小さい時は、元のサイズにとどまる

`width: 100%` 画像の元のサイズに関係なく、強制的に枠の横幅まで拡大/縮小する

`object-fit: cover` `max-height: 400px` の縦400pxで固定し、縦横比を維持かつ画像を中央で切り出す

`margin-bottom: calc(var(--v-space) * 2 / 3)` 基本となる余白 'var(--v-space)' の3分の2のサイズを下余白に設定

```css
img {
  max-width: 100%;
}

.entry-img img {
  width: 100%;
  max-height: 400px;
  object-fit: cover;
  margin-bottom: calc(var(--v-space) * 2 / 3);
}
```

`max-width: 720px` 最大幅を720pxに設定

```css
.entry .w-container {
  max-width: 720px;
}
```

`:where()` カッコ内の要素に一括でスタイルを適用。詳細度を0にする

`revert` 元に戻す

`.entry-container:where(h1, h2, h3, h4, h5, h6, p, figure, ul)` 見出しなどの要素をブラウザ標準の値に戻す

`margin-top: revert` 外側上余白をブラウザ標準に戻す

`margin-bottom: revert` 外側下余白をブラウザ標準に戻す

`padding: revert` 内側上下左右余白をブラウザ標準に戻す

`list-style: revert` リストスタイルをブラウザ標準に戻す

```css
.entry-container:where(h1, h2, h3, h4, h5, h6, p, figure, ul) {
  margin-top: revert;
  margin-bottom: revert;
  padding: revert;
  list-style: revert;
}
```

`margin: 1.8em 0` 1行分の余白を挿入。1行分高さは `p { line-height }` の 1.8倍

```css
p {
  line-height: 1.8;
}

.entry-container p {
  margin: 1.8em 0;
}
```

`.entry-container > :first-child` 親 `.entry-container` 直下の最初の子 `first-child `

`.entry-container > :last-child` 親 `.entry-container` 直下の最後の子 `last-child`

```css
.entry-container > :first-child {
  margin-top: 0;
}

.entry-container > :last-child {
  margin-bottom: 0;
}
```

`padding: var(--v-space) 0` 上下に余白を設け、左右の余白は0

```css
:root {
  --v-space: clamp(90px, 9vw, 120px);
}

.plans {
  padding: var(--v-space) 0;
  background-color: #e9e5e9;
}
```

`plans-container` 親要素で `grid` レイアウトにする

`@media (min-width: 768px)` 768px以上は縦1列に上下に配置。768px未満は横に3列を等しい幅配置

`grid-template-columns: repeat(3, 1fr)` 768px以上の上下配置の具体的に3列を等分に配置

```css
.plans-container {
  display: grid;
  gap: 27px;
}

@media (min-width: 768px) {
  .plans-container {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

`.plan .desc` `plan` クラス要素の中にある `desc` クラス(子孫クラス)

`.plan .price` `plan` クラス要素の中にある `price` クラス(子孫クラス)

```css
.plan .desc {
  ...
}

.plan .price {
  ...
}
```

`width: auto` 横幅を自動調整する

```css
.plan .btn {
  width: auto;
}
```

`display: flex` 親要素 `plan` でflexレイアウト

`flex-direction: column` 縦並びに配置

`margin-top: auto` 子要素 `plan .price` クラスで上余白を自動調整する

文章量によりボタンの表示位置のずれ、下余白が生まれる。 `margin-top: auto` とすることで余剰スペースを上に割り当てる

```css
.plan {
  display: flex;
  flex-direction: column;
  ...
}

.plan .price {
  margin-top: auto;
  ...
}
```

### ハンバーガーメニュ

```html
<!-- Hamburger Menu : Event -->
<button
  class="navbtn"
  onClick="document.querySelector('html').classList.
toggle('open')"
>
  <!-- 3本線アイコン -->
  <i class="fas fa-bars"></i>
  <!-- X印アイコン -->
  <i class="fas fa-times"></i>
  <span class="sr-only">MENU</span>
</button>
<!-- Hamburger Menu -->
<nav class="nav">
  <ul>
    <li><a href="index.html">ホーム</a></li>
    <li><a href="content.html">サービス案内</a></li>
    <li><a href="#">お問い合わせ</a></li>
  </ul>
</nav>
```

`@media (max-width: 767px)` 画面幅が767px以下の時に有効

`position: fixed` 固定配置

`inset: 0` 要素の上下左右(top, right, bottom, left)の配置位置をすべて0に指定

`.nav` の設定で画面全体を薄い黒色で覆う。これをオーバーレイという

`.nav ul` にてメニュー文字を縦並びで表示

```css
@media (max-width: 767px) {
  .nav {
    position: fixed;
    inset: 100;
    z-index: 10;
    background-color: #4e483ae6;
  }

  .nav ul {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    gap: 40px;
    color: #ffffff;
  }
}
```

`button` タグがクリックされる毎に `open` を付け外す

```html
<button
  class="navbtn"
  onClick="document.querySelector('html').classList.toggle('open')"
></button>
```

`inset: 0 -100% 0 100%` 上: 0%/右: -100%/下: 0%/左: 100%。

- オーバーレイの右端を-100%の位置に移動
- オーバーレイの左端を100%の位置に移動
- 画面を右側に押し出して見えなくする

`transform: translate(-100%, 0)` `.nav` クラスの中心を原点に-100%スライドさせる。これでサイト全面を覆う

`position: fixed/overflow: hidden` オーバレイの下でページがスクロールするのを防止。防止範囲は `body` タグ領域

```css
@media (max-width: 767px) {
  .nav {
    inset: 0 -100% 0 100%;
  }

  .open .nav {
    transform: translate(-100%, 0);
  }

  .open body {
    position: fixed;
    overflow: hidden;
  }
```

`z-index: 110` `.open .navbtn` を手前に表示

- `nav { z-index: 100}` クリック時のオーバレイの z-index

`.open .navbtn` メニューが表示されているときに適用するためこのクラス指定が必要

```css
.open .navbtn {
  z-index: 110;
  color: #ffffff;
}
```

オーバーレイopen時

1. `.open .navbtn .fa-bars` セレクタで `display: none` を指定し3本線を非表示
2. `navbtn .fa-bars` セレクタで `display: revert` を指定しデフォルト設定に戻す

オーバーレイclose時

1. `.navbtn .fa-times` セレクタで `display: none` を指定しX印を非表示
2. `.open .navbtn .fa-times` セレクタで `display: revert` を指定しデフォルト設定に戻す

```css
.open .navbtn .fa-bars {
  display: none;
}

.navbtn .fa-bars {
  display: revert;
}

.navbtn .fa-times {
  display: none;
}
.open .navbtn .fa-times {
  display: revert;
}
```

`@media (min-width: 768px)` 画面幅が768px以上なら `.navbtn: display: none` で3本線アイコンを非表示

```css
@media (min-width: 768px) {
  .navbtn {
    display: none;
  }
}
```

`@media (min-width: 768px)` 画面幅が768px以上の場合、レイアウトが有効になる

`display: flex` 要素を左右並びに配置

```css
@media (min-width: 768px) {
  .nav ul {
    display: flex;
    gap: 40px;
    color: #707070;
  }
}
```
