# 那須家ライフプランWebアプリ Ver5.4.1 運用手順

## Ver5.4.1の構成

Ver5.4.1では、静的WebアプリとGitHub更新APIを分離します。

```text
nasu-lifeplan
  静的Webアプリ表示用

nasu-lifeplan-api
  GitHub data.json更新API用
```

既存の `nasu-lifeplan` はStatic Assets専用のため、Secretsを登録できません。Secretsは必ず `nasu-lifeplan-api` 側に登録します。

## 毎月の更新方法

1. CSV追加
2. PDF追加
3. 更新開始
4. 妻スマホ確認

終了です。

## 初回設定

1. Cloudflare Workers & Pagesで新しいWorkerを作成
2. Worker名を `nasu-lifeplan-api` にする
3. `worker.js` を貼り付けてデプロイ
4. `nasu-lifeplan-api` 側にSecretsを設定する
5. アプリ画面の詳細設定にAPI URLを入力する
6. 更新APIキーを入力する
7. GitHub自動反映をONにする

## 入力するAPI URL例

```text
https://nasu-lifeplan-api.ef41108.workers.dev/api/update-data
```

## nasu-lifeplan-apiに設定するSecrets

- `GITHUB_TOKEN`
- `GITHUB_OWNER`
- `GITHUB_REPO`
- `GITHUB_BRANCH`
- `GITHUB_DATA_PATH`
- `UPDATE_API_KEY`

GitHubトークンはブラウザには保存しません。ブラウザで入力するのはAPI URLと更新APIキーだけです。APIキーは保存するかどうかを選べます。

## 必要なファイル

- SBI CSV：3ファイル以上
- マネーフォワード資産内訳PDF：1ファイル
- マネーフォワードバランスシートPDF：1ファイル

SBI CSV 4件目は任意です。米国株はCSVではなく、マネーフォワード資産内訳PDFで確認します。

## 更新開始で自動実行されること

- CSV解析
- PDF解析
- 更新前後比較
- history保存
- AIコメント更新
- 家計健康診断更新
- 更新完了レポート生成
- GitHub自動反映
- Cloudflare反映確認
- 更新完了画面表示

## Cloudflare反映確認

更新開始時に `meta.lastSyncId` を発行し、GitHubへ送る `data.json` にも保存します。

公開URLの `data.json` を取得し、`remote.meta.lastSyncId === local.meta.lastSyncId` の場合だけ「Cloudflare反映済み」と表示します。`version` や更新月だけでは成功扱いにしません。

## API URL未設定時

GitHub更新API URLが未設定の場合、GitHub自動更新は実行しません。

画面には「GitHub更新API URLが未設定です。nasu-lifeplan-api のURLを入力してください。」と表示します。

その場合でも「data.jsonを書き出す」「手動アップロードはこちら」は使えます。

## 困った時

GitHub更新に失敗した場合は「手動アップロードはこちら」から `data.json` を書き出し、GitHubに手動アップロードできます。

Cloudflareだけ反映待ちの場合は、GitHub更新済みです。少し待ってから共有URLを再確認してください。
