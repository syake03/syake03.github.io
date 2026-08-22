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
- **画像はページ全体で1枚だけ。** 全リンクにサムネを付けると、罫線にしても結局カードに見える。
  1つだけ非対称に扱うことが、没個性を消す一番効く手
- **色は地・文字・アクセントの3つだけ。** アクセントは藍1色
- **JSは0行**
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
