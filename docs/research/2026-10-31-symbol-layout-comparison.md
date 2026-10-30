# 記号レイヤー比較調査（2026-10-31）

## 調査範囲と結論

roBa v2、QWERTY、Windows の US 配列と Microsoft IME、TS/JS・Markdown・shell の利用を前提に、Miryoku と QMK Corne 標準配列を一次資料で比較した。Seniply は括弧配置の設計判断を比較するために参照した。配列の採用率や入力速度を測る調査ではないため、「最多」「最速」とは判断しない。調査後に選択された配置は末尾に記録する。

現在の SYM は、右手に括弧と演算子をまとめる独自の整理として説明できる。確認した Miryoku / Corne の標準配置そのものではない。一方、親指で記号レイヤーを呼び出す考え方、括弧をまとめる考え方には一次資料で確認できる先例がある。配置の一致と、設計の共通点を区別する。

## 比較時点の配列（変更前）

各手は上段・中段・下段の順で、紙面の左から右に読む。これは依頼時に共有された SYM の記録。

```text
左手                     右手
`  ~  $  \  |            (  )  [  ]  =
!  &  #  %  ^            {  }  <  >  +
@  _  ;  :  ?            -  *  /  "  '
```

左内側親指の layer-tap で呼び出し、左 Space と右 Enter は専用キーとして維持する。NUM の横並びもこの調査では変更しない。

## Miryoku：テンキーと Shift 対応を軸に分ける

確認した公式リポジトリの master は `3420b1015cac2103d7c236744a098fdec179fb40`。公式 reference は 3×5＋親指3キーを片手の単位とし、入力する手と反対の親指でレイヤーを呼び出す構成を説明している。QWERTY の代替 alpha も用意されているため、記号配置の設計だけを比較するために Colemak へ移行する必要はない。[公式 reference](https://github.com/manna-harbour/miryoku/blob/3420b1015cac2103d7c236744a098fdec179fb40/docs/reference/readme.org)

以下は公式レイヤー資料と Configurator export で照合した左手部分。Num と Sym は別レイヤーであり、この2つを合わせたものが「Miryoku の記号アクセス」に相当する。Num の Shift 相当を Sym に直接置き、同じ位置関係を保っている。[Num 資料](https://github.com/manna-harbour/miryoku/blob/3420b1015cac2103d7c236744a098fdec179fb40/data/layers/miryoku-kle-num.json)、[Sym 資料](https://github.com/manna-harbour/miryoku/blob/3420b1015cac2103d7c236744a098fdec179fb40/data/layers/miryoku-kle-sym.json)、[Configurator export](https://github.com/manna-harbour/miryoku/blob/3420b1015cac2103d7c236744a098fdec179fb40/data/configurator/miryoku-configurator-crkbd.json)

```text
Num 左手                 Sym 左手
[  7  8  9  ]            {  &  *  (  }
;  4  5  6  =            :  $  %  ^  +
`  1  2  3  \            ~  !  @  #  |
親指: . 0 -              親指: ( ) _
```

Sym の `(` は上段にも親指にも存在する。記号入力は左手、呼び出しと主な修飾キーは右手という役割分担になる。さらに基本層の `, . / '` と Shift による `< > ? "` も利用する。したがって、現在の30記号をすべて1枚の SYM に直置きする構成とは、必要なレイヤー数・親指利用・記号の分散が異なる。[公式 reference](https://github.com/manna-harbour/miryoku/blob/3420b1015cac2103d7c236744a098fdec179fb40/docs/reference/readme.org)

現在の横並び NUM を残す条件では、Miryoku の Sym だけをコピーするとテンキーとの対応という主な記憶の手掛かりが失われる。全面移植より、「よく使う記号を呼び出しと反対の手に置く」という原則を比較する方が条件に合う。これは資料の配置からの推論であり、速度向上の実測結果ではない。

## QMK Corne default：US 数字列の記憶を残す

QMK master `f5db962110615fd263c3ebf9f0402e7c2b08fbe4` の `keyboards/crkbd/keymaps/default/keymap.c` を確認した。これは Corne の QMK 標準キーマップであり、Corne 利用者全体の代表サンプルではない。[固定したソース](https://github.com/qmk/qmk_firmware/blob/f5db962110615fd263c3ebf9f0402e7c2b08fbe4/keyboards/crkbd/keymaps/default/keymap.c)

記号の layer 2 は 3×6 の構成。左右とも外側キーを含む6列で、`—` はその行の記号以外の制御キーまたは無効キーを省略したもの。

```text
左手                        右手
—  !  @  #  $  %            ^  &  *  (  )  —
—  —  —  —  —  —            -  =  [  ]  \  `
—  —  —  —  —  —            _  +  {  }  |  ~
```

数字レイヤーの上段 `1 2 3 4 5 / 6 7 8 9 0` と、記号レイヤー上段 `! @ # $ % / ^ & * ( )` が US 配列の対応を保つ。右側の中段と下段も `-/_`, `=/+`, `[/ {`, `]/}`, `\ / |`, `` ` / ~ `` の上下対応になっている。左 Space と右 Enter は単機能で、右親指の `MO(2)` が記号レイヤーを呼び出す。`MO` は layer-tap ではない。[固定したソース](https://github.com/qmk/qmk_firmware/blob/f5db962110615fd263c3ebf9f0402e7c2b08fbe4/keyboards/crkbd/keymaps/default/keymap.c)

現在の「左 Space、右 Enter 専用」とは共通する。しかし3×6と3×5で列数が違い、呼び出す手も現在の左親指と逆なので、そのままコピーできる比較対象ではない。

## Seniply：括弧の同じ指への割り当ても設計選択になる

公式説明は、右手に括弧・コロン・セミコロン等を置き、括弧の種類ごとに同じ指を割り当て、開きをホーム段・閉じを下段にする。IDE が閉じ括弧を補完する場合など、開閉を連続して打つとは限らないことを理由にしている。これは作者の設計理由であり、あらゆるユーザーの入力で同じ結果になるという実証ではない。[公式 Symbols Layer 説明](https://stevep99.github.io/seniply/)

固定したソースの右手5列でも、上段 `=`, backtick, `: ; +`、中段 `* ( { [ -`、下段の対応位置に `) } ]` が確認できる。ただし KMonad 配布ファイルには UK 系の追加記号も含まれるため、このファイル全体を US 向け完成配列として転記してはいけない。[KMonad 配布ソース、commit 50b95b5ded119c1866a9ad014c0141f09961b8a3](https://github.com/stevep99/seniply/blob/50b95b5ded119c1866a9ad014c0141f09961b8a3/downloads/kmonad/colemak-dh-seniply-split.kbd)

この先例から、現在の `()` や `{}` が同じ人差し指の2列である点だけを根拠に「誤り」とは言えない。ただし現在は横隣接、Seniply は開きをホーム段・閉じを下段とするため、同じ配置でもない。

## この利用条件で評価すべき点

以下は配置と利用条件からの仮説であり、一次資料で証明された最適配置ではない。

- 現在の右上段 `( ) [ ] =`、中段 `{ } < > +` は、括弧の開閉が横に並び、種類のまとまりを覚えやすい構造。一方 `()` と `{}` は内側2列を同じ人差し指で担当し、`=` と `+` は小指側になる。
- JS/TS の `=>`, `===`, `+=` などが負担なら、`=` と `+` の位置は試す価値がある。ただし実際の頻度はコード・補完・formatter・snippet の使い方で変わる。
- Markdown の backtick、shell の `$`, backslash, pipe 等は左手にあるため、左親指を保持した左手入力の感触も確認する。右側だけの比較では評価が足りない。
- 配置の評価と layer-tap の hold 判定の評価を分ける。呼び出しの待ちや誤判定が主因なら、記号の位置を入れ替えただけでは解決を確認できない。
- 実機では IME OFF の英数入力と IME ON の双方で確認する。ここではホスト側での出力や Microsoft IME の実動作を検証していない。

候補を試すときは、同じ短い TS/JS・Markdown・shell のサンプルで誤入力、止まった箇所、修正回数、左親指保持の負担を記録する。初回の不慣れと、学習後にも残る負担を区別して判断する。

## 任意の試用案：右手の上2段だけを回す

確立した配列の名称を付けられる案ではなく、現在の30記号と左側を保つ局所的な試用案。NUM は変更しない。

```text
左手（変更なし）         右手（試用案）
`  ~  $  \  |            =  (  )  [  ]
!  &  #  %  ^            +  {  }  <  >
@  _  ;  :  ?            -  *  /  "  '
```

この案では `=` と `+` が小指から内側人差し指へ移り、`()` と `{}` は人差し指2列から人差し指・中指へ移る。その代わり `[]` と `<>` は中指・薬指から薬指・小指へ移る。`=>` の小指担当は現在の `=` から試用案の `>` へ移るため、この2文字の小指の打鍵回数は減らない。閉じ括弧を IDE 補完に任せることが多いなら、開閉を別の指にする利点自体が小さくなる可能性もある。現在の配列、上記試用案、Corne 型の US 数字列の記憶を使う案は、別々の優先順位を選ぶ比較対象とする。

## 配置より先に確認するレイヤー呼び出し

現在の `config/roBa.keymap` は `&lt` 全体に `flavor = "tap-preferred"`、`tapping-term-ms = <200>` を設定している。SYMだけでなくNUMとNAVも同じ設定を使う。

ビルド済みZMKの `acfd8e5ea76cf23ad1c9b6b99848f97a95224257` の `decide_tap_preferred()` は、別キーの押下ではholdを決定せず、タイマー満了でhold、親指キーの解放でtapを決定する。したがって短いSYMコードを200 ms未満で離すと、意図した記号ではなくIME ONと基本層の入力として解決し得る。親指を200 ms以上保持する場合は、先に押した他キーが保留後に処理されるので、「必ず200 ms待ってから他キーを押す必要がある」という意味ではない。[使用したバージョンの実装](https://github.com/zmkfirmware/zmk/blob/acfd8e5ea76cf23ad1c9b6b99848f97a95224257/app/src/behaviors/behavior_hold_tap.c)、[公式hold-tap仕様](https://zmk.dev/docs/keymaps/behaviors/hold-tap)

改善の比較候補は次の通り。

- 専用の `&mo SYM` は押下時にレイヤーを有効化する。hold/tapの判定待ちを除けるが、専用キーを1つ確保し、移した機能の置き場所を決める必要がある。[公式Momentary Layer仕様](https://zmk.dev/docs/keymaps/behaviors/layers#momentary-layer)
- IME ONと兼用する場合は、SYM専用のhold-tapを作り `hold-preferred` や `balanced` と比較する。前者は他キー押下、後者は他キーを押して離すことで早くholdを確定できる。一方、IME ONの短押しと次の文字が重なったときに記号と誤判定する可能性がある。誤判定率を確認せずに全面採用しない。[公式Interrupt Flavors仕様](https://zmk.dev/docs/keymaps/behaviors/hold-tap#interrupt-flavors)
- `&lt` 全体の変更はNUM・NAVにも影響する。SYMの試用だけなら専用behaviorで分離し、Space・Enter専用と横一列NUMという既決の条件を維持する。

この調査での推奨順序は、呼び出しの待ち・誤判定をまず切り分け、その後に現配列と局所試用案を比較すること。覚え直しの少なさを優先するならCorne型のUS数字列対応を別候補とする。IDEの括弧補完と使用頻度を測っていないため、現配列・局所試用案・既存配列のいずれかが本人に最速とは結論しない。

## 採用した試用配置（2026-10-31）

ユーザーの「Corne型にしてみて」に従い、US数字列との対応と右手の上下対応を採用した。Corne標準の6列をroBaの5列へ収めるため、右端のバッククォートとチルダは左手へ移した。左手の残りの空きも使い、変更前と同じ30種類の記号を直接入力できるようにした。

```text
左手                     右手
!  @  #  $  %            ^  &  *  (  )
`  ;  :  '  "            -  =  [  ]  \
~  <  >  /  ?            _  +  {  }  |
```

左親指によるSYM呼び出し、IME ONとの兼用、tap-preferredの200 ms判定は維持する。これはCorne標準の右親指MOとは異なる。今回の試用では記号配置だけを比較できるようにし、呼び出し操作の変更は別の判断とする。
