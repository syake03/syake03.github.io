# hub — ミヤケのハブサイト

Xのプロフィール欄に貼る1ページ。公開URL: https://syake03.github.io/

X投稿 → プロフィール → **このページ** → 各プロダクト、という導線の中継地点。
流入はほぼスマホ、滞在は数十秒。**鑑賞させる場所ではなく、転換させる場所**として設計している。

## デザインの約束(崩さないこと)

「紙と墨」。なかみの本棚(紙 `#F7F4ED` / 墨 / 藍)、Sumi(水墨画)、
ニュースタンダードラジオ(クリーム地に黒)が **実際に共有している色** をそのまま土台にした。
好みで選んだ配色ではないので、プロダクト側のトーンが変わらない限りここも変えない。

- **カードを使わない。** カードUIは「等価な選択肢の一覧」を意味してしまい、個性が消える。境界は1pxの罫線だけ
- **影を使わない**(なかみの本棚 `tokens.ts` と同じ規律)
- **全作品を同じ情報量で並べる**(2026-08-26に方針変更)。以前は「画像は1枚だけ・1つを非対称に」
  としていたが、依頼者の判断でポートフォリオとして全作品に画・説明・行き先を揃えた。
  カット(`assets/shot-*.jpg`)は `tools/frame.html` の共通枠で作り、地色と端末の形を統一している
- **色は地・文字・アクセントの3つだけ。** アクセントは藍1色
- **JSは0行**
- **古びる数字を書かない。** 配信回数や本数は、公開のたびに嘘になる。
  実績を出したいときは**数えた数ではなく、始めた年**で言う(「75回配信」ではなく「2023年から」)。
  静的サイトに自動更新の仕組みを足すのは、このページの規模に見合わない。

- ジャンプ率は極端に(名前 : メタ情報 で約8倍)。中間サイズを作らない

## 実装メモ

- 和文はシステムフォント(Hiragino Sans)。**見出しだけ** Zen Old Mincho をGoogle Fontsから。
  日本語は244個のサブセットに分割配信されるので、このページで実際に落ちるのは数十KB
- `img` に `width`/`height` 属性を書くときは CSS 側で **`height: auto` を必ず入れる**。
  入れないと height 属性がプレゼンテーションヒントとして効き、`aspect-ratio` が無視される(実際に踏んだ)
- スクロール連動アニメーションは `@supports (animation-timeline: view())` でガード。
  非対応ブラウザで要素が消えたままにならないよう、既定は必ず「見えている」状態
- ファーストビュー(名乗り・実数)にはアニメーションをかけない(LCPを損なう)

## OGP画像の作り直し方

版下は `tools/ogp.html`。本体と同じ書体・同じ余白で作ること(ここが別デザインだと導線の連続性が切れる)。

```bash
python3 -m http.server 4321 -d . &
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu \
  --hide-scrollbars --force-device-scale-factor=2 --window-size=1200,630 \
  --virtual-time-budget=6000 --screenshot=/tmp/ogp_raw.png "http://localhost:4321/tools/ogp.html"
sips -z 630 1200 -s format jpeg -s formatOptions 82 /tmp/ogp_raw.png --out assets/ogp.jpg
```

## 素材の出所

`assets/sumi-wide.jpg` は Sumi 本体が App Store で配信している実シーン
(`sumi/public/assets/scenes/falling-water/master-day.png`「深山瀑布」)から切り出したもの。

> ⚠️ `sumi/_reference/*-DO-NOT-SHIP/` の画像は **Google画像検索由来で権利未確認**。
> 見た目が似ていても、あちらは絶対に使わない。

## 残っている作業

- [ ] 顔写真アイコン(index.html にはまだ入れていない。入れるなら名乗りの上に小さく)
- [ ] 「本業は映像配信サービスの事業企画」の一文を戻すかどうか → Jのミカタの匿名運営と衝突するため保留中

## スクリーンショットを撮るときの注意(実際に踏んだ)

macOS の Chrome は **ウィンドウ幅を約460pxより狭くできない**。
`--window-size=430,H` を指定すると、**約460pxで組んだpage を430pxぶんだけ切り取った画像**が出てきて、
右端の文字が欠ける(「うまくいかなかった」が「くいかなかった」になる等)。
`--force-device-scale-factor` や `--headless=new` を変えても直らない。

→ **必ず幅500px以上で撮る。** 撮ったあとは右余白が左と対称かを必ず確認すること。
サイト自体は正常でも、この撮り方だと壊れた画像ができあがる。

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu \
  --hide-scrollbars --force-device-scale-factor=2 --window-size=500,2400 \
  --virtual-time-budget=7000 --screenshot=/tmp/s.png "http://localhost:4321/"
```

## Xのヘッダー画像

版下は `tools/header.html`(1500x500)。書き出し済みは `assets/x-header.jpg`。
**左下(アイコンが重なる)と下端には要素を置かない。** 素材は `tools/marks/` の4点。

## 書かないことにした表現

- **「ぜんぶひとりで」等の、ひとりでやっている強調** — 苦労自慢に寄るうえ、
  ニュースタンダードラジオはヨシダさんとの2人番組なので**事実としても正しくない**。
  作り手の手柄ではなく、相手が受け取るもの(読む・眺める・聴く・観に行く)を書く。

## リンクのラベルは、実際の行き先と一致させる

作品の行は `<li class="work"><a>` が行全体を覆う1リンク。**タップ領域が大きく、スマホで押しやすい**のが理由。
ただし「YOUTUBE / SPOTIFY」のように**行き先を複数書くと嘘になる**(実際にはどこを押しても1か所へ飛ぶ)。

行き先が複数ある作品は `.work--multi` を使い、meta を**本物のリンク**にする。
`<a>` の入れ子はHTMLとして不正なので、行の `<a>` の**兄弟**として置き、連番カラムぶんインデントを合わせている。

- CSSは必ず `.work > a`(直接の子)で書く。`.work a` にすると
  リンク行の中の `<a>` にも行のスタイルと連番カウンタが当たって崩れる(実際に踏んだ)
- リンク行の `<a>` には `padding: 9px 2px; margin: -9px 0` を入れ、
  **見た目の行間を変えずにタップ高を44px以上**にする

## 作品カットの作り直し方

素材は `tools/shots/`(Webは実サイトのスクショ、アプリは実画面)。共通枠に流し込んで書き出す。

```bash
python3 -m http.server 4321 -d . &
CH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
# Webサイトを撮る(幅は500以上。460未満は右が欠ける)
"$CH" --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=2 \
  --window-size=500,1000 --virtual-time-budget=9000 \
  --screenshot=tools/shots/xxx.png "https://example.com"
# 共通枠に入れて書き出す(mode=phone / square)
"$CH" --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=2 \
  --window-size=1000,660 --virtual-time-budget=6000 --screenshot=/tmp/f.png \
  "http://localhost:4321/tools/frame.html?img=shots/xxx.png&mode=phone"
sips -z 660 1000 -s format jpeg -s formatOptions 78 /tmp/f.png --out assets/shot-xxx.jpg
```

## CSSの詳細度で踏んだ罠

`.work p { font-size: var(--t-body) }`(詳細度0,1,1)は `.work-links`(0,1,0)に**勝つ**。
リンク行の文字だけ本文サイズ(14px)に膨らんでいた。`.work .work-links` のように
**クラス2つ以上で書く**こと。`.work > a` に限定した件と同じ種類の事故。

## ドメインの取り違えに注意(実際にやらかした)

`japantriptools.com` は **第三者(2026-06-18にお名前.com/GMOで取得・転売目的とみられる)のもの**で、
本人のサイトではない。本人のドメインは **`triptoolsjapan.com`**(Cloudflareで取得、2026-07-20公開)。

一度この誤りで、他人のサイトへリンクし、そのスクリーンショットをポートフォリオに載せてしまった。
**URLが200を返すことは「本人のサイトである」証明にはならない。**
リンクを足すときは、必ず各プロジェクトの STATUS.md / SPEC で実際のドメインを確認すること。
