# ファームウェアのビルド

このリポジトリはroBa v2・トラックボールあり・ロータリーエンコーダーなしの設定です。

## GitHub Actionsでビルドする

ローカルに開発環境を入れずにビルドできます。

1. 変更した設定を自分のGitHubリポジトリへコミット・pushします。`config`だけでなく、`boards`などの変更も含めます。
2. GitHubリポジトリの「Actions」を開きます。Fork後に無効になっていればActionsを有効化します。
3. pushで起動した「Build Firmware」を開きます。手動実行する場合は、同ワークフローの「Run workflow」で対象ブランチを選びます。
4. 全ジョブが成功したら、その実行画面の「Artifacts」から`firmware`をダウンロードし、ZIPを展開します。

ビルド対象は[`build.yaml`](../build.yaml)、実行手順は[`.github/workflows/build.yml`](../.github/workflows/build.yml)で定義しています。右側はZMK StudioのUSB接続用スニペット付きです。

## WSL / LinuxとDockerでビルドする

Docker Engine、またはWSL連携を有効にしたDocker Desktopを起動しておきます。Dockerコマンドを実行できるユーザーで、リポジトリのルートから次を実行します。初回は公式イメージと依存モジュールのダウンロードに時間がかかります。

```bash
mkdir -p .build/zmk/config firmware
cp -R config/. .build/zmk/config/
docker pull zmkfirmware/zmk-build-arm:stable

docker run --rm \
  --user "$(id -u):$(id -g)" \
  --env XDG_CACHE_HOME=/work/.cache \
  --env ZEPHYR_BASE=/work/zephyr \
  --env Zephyr_DIR=/work/zephyr/share/zephyr-package/cmake \
  --volume "$PWD:/config:ro" \
  --volume "$PWD/.build/zmk:/work" \
  --volume "$PWD/firmware:/out" \
  --workdir /work \
  zmkfirmware/zmk-build-arm:stable sh -eu -c '
    if [ ! -d .west ]; then
      west init -l config
    fi
    west update --fetch-opt=--filter=tree:0

    west build -p always -s zmk/app -d build/right \
      -b seeeduino_xiao_ble -S studio-rpc-usb-uart -- \
      -DZMK_CONFIG=/work/config -DZMK_EXTRA_MODULES=/config -DSHIELD=roBa_R
    cp build/right/zephyr/zmk.uf2 /out/roBa_R-seeeduino_xiao_ble-zmk.uf2

    west build -p always -s zmk/app -d build/left \
      -b seeeduino_xiao_ble -- \
      -DZMK_CONFIG=/work/config -DZMK_EXTRA_MODULES=/config -DSHIELD=roBa_L
    cp build/left/zephyr/zmk.uf2 /out/roBa_L-seeeduino_xiao_ble-zmk.uf2

    west build -p always -s zmk/app -d build/reset \
      -b seeeduino_xiao_ble -- \
      -DZMK_CONFIG=/work/config -DZMK_EXTRA_MODULES=/config -DSHIELD=settings_reset
    cp build/reset/zephyr/zmk.uf2 /out/settings_reset-seeeduino_xiao_ble-zmk.uf2
    west manifest --freeze --active-only > /out/west-manifest.yml
  '
```

`firmware/`に3つのUF2と、依存コミットを固定した`west-manifest.yml`ができます。`.build/`は取得した依存関係とビルドのキャッシュです。両方ともGitの管理対象外です。設定を変えて再ビルドするときも、最初の`cp`から実行して最新の設定をコピーします。

キーマップは`/work/config`、シールドなどのハードウェア定義は読み取り専用でマウントした`/config`を参照します。これはGitHub Actionsと同じ`ZMK_EXTRA_MODULES`方式です。

`config/west.yml`のZMKとドライバーはブランチを参照するため、後日同じコマンドでビルドした際に依存関係が更新されることがあります。今回の依存コミットと公式Dockerイメージの識別子は、成果物に添付したビルド情報で確認できます。

## できあがるファイル

| ファイル | 用途 |
| --- | --- |
| `roBa_R-seeeduino_xiao_ble-zmk.uf2` | 右側・トラックボール側。キーマップとZMK Studio対応を含む |
| `roBa_L-seeeduino_xiao_ble-zmk.uf2` | 左側。エンコーダー無効の設定を含む |
| `settings_reset-seeeduino_xiao_ble-zmk.uf2` | 設定の初期化用。ペアリング情報なども消えるため、必要な場合だけ使用 |

今回のエンコーダー無効化を左右とも反映する場合は、右側と左側にそれぞれ対応するUF2を書き込みます。その後、通常のキーマップ変更だけなら右側を更新します。

書き込みは、対象側をUSB接続し、リセットを素早く2回押して表示されるブートローダーのドライブへ対応するUF2をコピーします。通常の更新に初期化用UF2は不要です。

Studioで変更した配列が保存されている場合は、右側を書き込んだ後にStudioの「Restore Stock Settings」でファイル側の配列に戻します。Studio側の配列編集は失われます。

参照：[ZMK公式のユーザー設定ビルドワークフロー](https://github.com/zmkfirmware/zmk/blob/v0.3-branch/.github/workflows/build-user-config.yml)。
