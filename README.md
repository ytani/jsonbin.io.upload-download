# JSONBin Sync

[JSONBin.io](https://jsonbin.io/) のbinに、JSONファイルをアップロード・ダウンロードするためのシンプルなWebアプリです。
`index.html` 1ファイルだけで動作し、Pythonなどのインストールは不要です。

## 特長

- 複数のbin(接続先)をドロップダウンで切り替え
- 接続先の確認用に、Bin IDとMaster Keyの**末尾4文字だけ**を表示
- アップロード前に、接続先名・ファイル名・最終更新日時(経過時間付き)を確認
- ダウンロードのファイル名に日時を付与(`data_接続先名_YYYYMMDD_HHMMSS.json`)するため、上書きされない
- 通信は非同期で行うため、画面が固まらない
- ログの保存・クリア
- 設定(`config.json`)はブラウザ内に保存され、次回から自動で読み込まれる

## セキュリティについて(重要)

**`config.json` には Master Key が含まれます。GitHub などの公開場所には絶対にアップロードしないでください。**

- このリポジトリに置くのは `index.html`(と README.md)だけです。キーは一切含まれません。
- `config.json` は各自のPCに保管し、アプリ画面から読み込みます。
- 読み込んだ設定は、そのブラウザの `localStorage` に**平文で**保存されます。共有PCで使った後は、必ず「設定を消去」を押してください。
- リポジトリに `config.json` を誤ってコミットしないよう、`.gitignore` に次の1行を入れておくことをおすすめします。

```
config.json
```

## config.json の書き方

```json
{
  "selected": "メイン",
  "bins": [
    {
      "name": "メイン",
      "master_key": "$2a$10$xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "bin_id": "xxxxxxxxxxxxxxxxxxxxxxxx"
    },
    {
      "name": "テスト用",
      "master_key": "$2a$10$yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy",
      "bin_id": "yyyyyyyyyyyyyyyyyyyyyyyy"
    }
  ]
}
```

| 項目 | 説明 |
| --- | --- |
| `bins` | 接続先の一覧(1件以上必須) |
| `bins[].name` | ドロップダウンに表示される名前。ダウンロードのファイル名にも使われる |
| `bins[].master_key` | JSONBinのMaster Key |
| `bins[].bin_id` | 対象binのID |
| `selected` | 最後に選んだ接続先名(省略可。アプリが自動で更新) |

- 同じMaster Keyで別のbinを使う場合も、別のKeyを使う場合も、`bins` に1件ずつ書くだけです。
- `name` はbinごとに重複しないようにしてください。

## 使い方

1. 初回は「設定を読み込む」を押して `config.json` を選ぶか、画面に `config.json` をドラッグ&ドロップします。
2. 読み込みに成功すると設定がブラウザに保存され、次回からは開くだけで使えます。
3. ドロップダウンで接続先を選びます(選択は記憶されます)。
4. **アップロード**: JSONファイルを選ぶと確認ダイアログが表示されます。内容を確認して実行します。**上書きは取り消せません。**
5. **ダウンロード**: 選んだbinの最新の内容を、日時付きのファイル名で保存します。
6. `config.json` を編集した場合は、もう一度読み込んでください。

## GitHub Pages での公開手順

1. GitHubで新しいリポジトリを作成し、`index.html` をアップロードします(**`config.json` は入れない**)。
2. リポジトリの **Settings → Pages** を開きます。
3. **Source** を「Deploy from a branch」にし、**Branch** を `main`、フォルダを `/ (root)` にして保存します。
4. 数分後、`https://ユーザー名.github.io/リポジトリ名/` で利用できます。

`index.html` を更新してpushすれば、公開ページにも反映されます。

## 注意事項

- 設定の保存先はブラウザごとです。別のPCやブラウザでは、初回に `config.json` を読み込む必要があります。
- ダウンロードの保存先は、ブラウザのダウンロード設定に従います。
- JSONBinの無料枠にはリクエスト数の上限があります。アップロードとダウンロードの1回ごとに1リクエストを消費します(接続先の切り替えや確認では通信しません)。

## トラブルシューティング

| 症状 | 対処 |
| --- | --- |
| 「設定の読み込みエラー」と表示される | `config.json` のJSON形式と、`name` / `master_key` / `bin_id` の記載漏れを確認してください |
| `HTTPエラー: 401` | Master Keyが正しいか確認してください |
| `HTTPエラー: 404` | `bin_id` が正しいか確認してください |
| `HTTPエラー: 429` | リクエスト数の上限に達しています。時間をおいて再度お試しください |
| 「JSONとして読めません」と表示される | アップロードするファイルがJSON形式として正しいか確認してください |
| ボタンが押せない | 設定が未読み込みか、通信中です。「設定を読み込む」から読み込んでください |
