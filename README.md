# HUSKYLENS I2C修正版

元ソース: https://github.com/tangjie133/pxt-huskylens (MIT)

UART対応版ではなく、初代HUSKYLENS用I2C拡張の修正版です。

## 修正内容

- 16バイト受信バッファーを呼び出し間で保持し、未処理データを失わないようにしました。
- 応答が分割されても100msのタイムアウトまで受信を続け、待機中は1ms休止します。
- 通信失敗時に古い認識結果を無効化します。
- 結果テーブルの上限（10件）を超える応答は失敗として扱います。
- `isRequestSuccessful()` で直前の取得の成功を確認できます。
- `initI2c()` は最大5回、`initMode()` は最大3回で失敗を返します。初期化失敗時は呼び出し元で停止や再試行を行ってください。従来の「成功まで待つ」動作から変更しています。

## MakeCodeへの導入

このフォルダーの内容を専用のGitHubリポジトリーにアップロードし、MakeCodeの「拡張機能」でそのURLを指定してください。元のHUSKYLENS拡張は削除してから追加します。同名の名前空間・列挙型があるため併用できません。GitHubへの公開はまだ行っていません。

手元で試す場合は、元の拡張が入っていないMakeCodeプロジェクトのJavaScript画面に `huskylens.ts` の内容を貼り付けても確認できます。

```typescript
let connected = huskylens.initI2c()
if (connected) {
    connected = huskylens.initMode(protocolAlgorithm.ALGORITHM_COLOR_RECOGNITION)
}
basic.forever(function () {
    if (connected) {
        huskylens.request()
        if (huskylens.isRequestSuccessful()) {
            // 認識結果を使用する処理
        } else {
            // サーボカーなら、ここで停止する処理を呼ぶ
        }
    }
    basic.pause(100)
})
```

HUSKYLENS本体のProtocol TypeはI2Cに設定してください。通信処理は1か所から呼んでください。今回の変更は認識結果の取得を対象とし、学習・SD保存・画面表示などの既存の無制限リトライは変更していません。

## 確認範囲

`node huskylens-fixed/verify.cjs`（親フォルダーから実行）でTypeScript変換とI2C応答の模擬テストを行います。実機通信・MakeCodeのターゲットビルドは未確認です。電源不足やノイズ、物理的に固着したI2Cバスはこの修正では解決しません。
