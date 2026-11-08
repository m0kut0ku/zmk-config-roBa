# zmk-config-roBa

Windows・US配列・Microsoft IME向けのキーマップです。roBa v2、トラックボールあり、ロータリーエンコーダーなしの構成で使います。

[kumamuk-gitの元配列](https://github.com/kumamuk-git/zmk-config-roBa/blob/main/config/roBa.keymap)に合わせた、左手テンキー・右手記号・左手矢印の構成です。QWERTY、独立したIME OFF／ON、Lの右のマイナス、Ctrl／Win／Altと親指キーの基本位置、J／K／Lのマウスクリックを維持しています。

![キーマップ](keymap-drawer/roBa.svg)

## 通常入力と長押し

左下の3キーは左からCtrl・Win・Altです。親指キーは、キーボードを上から見て左から次の順序です。

| 位置 | 短押し | 長押し |
| --- | --- | --- |
| 左・外側 | IME OFF | SYS（接続設定） |
| 左・中央 | Space | NUM（数字・記号） |
| 左・内側 | IME ON | NAV（移動） |
| 右・内側 | Backspace | Backspace |
| 右・外側 | Enter | FN（Fキー） |

SpaceとEnterは元配列と同じ長押し兼用です。レイヤー長押しの判定時間は200 msで、長押しではSpace・Enter・IME切替を送りません。Backspaceは専用キーに戻しています。

Zは短押しでZ、長押しでShiftです。Iは短押しでI、長押しでスクロールです。右下端はDeleteです。

内側のキーは次の配置です。

| 位置 | 通常入力 |
| --- | --- |
| 左・内側上 | Win + Shift + S |
| 左・内側下 | `:` |
| 右・内側上 | `'`（Shiftで`"`） |
| 右・内側下 | `;` |

Lの右は引き続き`-`で、Shiftとの組み合わせで`_`です。元配列のL右の引用符と右内側上のマイナスを交換しています。

## コンボ

元配列の同時押しを通常層だけに設定しています。数字・移動などの手動レイヤーでは発動しません。

| 同時に押すキー | 出力 |
| --- | --- |
| S + D | Tab |
| D + F | Shift + Tab |
| A + S | IME OFF |
| L + `-` | `"` |
| C + V | `=` |

IME OFFコンボも親指と同じLANG2を送ります。元配列の無変換キーや、通常層へ強制移動するマクロは使いません。

## 数字と記号

Spaceを長押しするとNUMです。左手は元配列と同じテンキー型です。

| 段 | 左手：左から順 | 右手：左から順 |
| --- | --- | --- |
| 上段 | `-`　7　8　9　`+` | `^`　`&`　`~`　`(`　`)` |
| 中段 | `/`　4　5　6　`*` | `!`　`@`　`#`　`$`　`%` |
| 下段 | 0／Shift　1　2　3　`.` | `[`　`]`　`{`　`}`　`\\` |

NUMの左内側上はCtrl + Alt + テンキー0、左内側下は`=`、右内側上は`_`、右下端は`|`です。

数字は通常の数字キーではなく`KP_NUMBER_*`を送ります。Windowsで数字として使う際はNumLockをオンにしてください。数字が移動キーとして動く場合はWindows側でNumLockの状態を確認します。

以前の独立したSYMレイヤーは廃止しています。引用符・コロン・セミコロン・マイナス・カンマ・ピリオド・スラッシュは通常層、その他の記号は主にNUMで入力します。

## 移動

IME ONを長押しするとNAVです。

| 通常の位置 | NAVでの動作 |
| --- | --- |
| E | ↑ |
| S / D / F | ← / ↓ / → |
| A / G | Home / End |
| Q | Esc |
| W / R | Ctrl + Shift + Tab / Ctrl + Tab |
| Z | Shift |
| X / V | Win + Shift + ← / Win + Shift + → |

ZのShiftや専用Ctrlと組み合わせて範囲選択・単語移動もできます。以前のWASD型矢印、Page Up／Down専用割り当ては元配列に合わせて変更しています。

## Fキーと接続設定

Enterを長押しするとFNです。

- Y／U／I／O／PがF1〜F5、H／J／K／L／`-`がF6〜F10です。
- スラッシュ位置がF11、右下端のDelete位置がF12です。
- 右内側上がF13です。

IME OFFを長押しするとSYSです。

- Y／U／I／O／PでBluetoothプロファイル0〜4を選択します。
- X／C／Vは通常の数字1／2／3です。
- スラッシュ位置で選択中プロファイルのペアリング情報を消去します。
- 右下端のDelete位置で全プロファイルのペアリング情報を消去します。
- 右内側下で右側のブートローダーへ入ります。

## トラックボール

ボールを動かすとMOUSEが自動で有効になり、J／K／Lが左／中央／右クリックになります。停止後700 msで解除されます。文字入力後300 ms以内はMOUSEを新たに有効にしません。

自動切替は従来どおりZMK標準の[Temporary Layer Input Processor](https://v0-3-branch.zmk.dev/docs/keymaps/input-processors/temp-layer)を使います。ドライバー側の`automouse-layer`は0で無効にしています。

MOUSE中の通常キー押下でMOUSEを解除しますが、I／スクロール、J／K／L、Z／Shift、Ctrl／Win／Altは除外しています。除外キーを押しても700 msのタイムアウトは延長されません。Shiftの位置は旧配列の左内側下からZへ、スクロールの入口は右内側下からIへ移しています。

Iを長押ししながらボールを動かすとスクロールします。手動レイヤーはMOUSEより優先され、SCROLLは最優先です。マウスクリックの配置、自動切替の待ち時間・解除条件の基本動作は維持しています。

## WindowsとMicrosoft IME

Windows側のハードウェアキーボードレイアウトは英語（US、101/102キー）にし、日本語入力にはMicrosoft IMEを使います。記号はUS配列を前提にしています。

IME OFFは`LANG2`、IME ONは`LANG1`を送ります。元配列の変換／無変換ではなく、Microsoftの専用IME OFF／ONキーです。[MicrosoftのIME ON／OFFキー仕様](https://learn.microsoft.com/en-us/windows-hardware/design/component-guidelines/keyboard-japan-ime)

## 設定の反映と確認

設定の正本は[config/roBa.keymap](config/roBa.keymap)です。ビルド・書き込みの手順は[ファームウェアのビルド](docs/build.md)を参照してください。通常のキーマップ変更では右側のUF2を更新し、ペアリング情報を初期化する必要はありません。

ZMK Studioで編集した保存済みキーマップがある場合、書き込み後にStudioの「Restore Stock Settings」でファイル側の配列に戻してください。Studio側の編集内容は失われます。[ZMK Studio](https://zmk.dev/docs/features/studio)

書き込み後は次を確認してください。

- IME OFF／ON、Space／Enterの短押しと長押し、Backspaceの連続入力。
- Zの短押し／Shift、Iの短押し／スクロール。
- NumLockをオンにしたテンキー入力、記号、Tabなどのコンボ。
- NAVの矢印・範囲選択、F1〜F12。
- 自動MOUSEのJ／K／Lクリック、Shift＋クリック、文字入力への復帰。
