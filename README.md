# Mod作業台

スマホからJava版modのビルドを回すWebアプリ。ビルド本体はGitHub Actionsが動かすので、PCは起動してなくてOK。

## できること

- ブランチ・Javaバージョン・Gradleタスクを選んでビルド開始
- `gradle.properties` の `minecraft_version` からJavaバージョンを自動で選ぶ（1.20.1→17、1.20.5以降→21 など）
- ビルド中・成功・失敗をリアルタイムで表示（5秒ごとに更新）
- 失敗したら、どのファイルの何行目でコンパイルエラーが出たかをそのまま表示
- 成功したjarを「最新のjar」からワンタップでダウンロード
- 実行中の中止、失敗したビルドのやり直し

## セットアップ

### 1. アプリをGitHub Pagesに置く

1. GitHubで新しいリポジトリを作る（例: `mod-bench`）。公開でOK（トークンはアプリに入っていない）
2. このフォルダの `index.html` `manifest.webmanifest` `icon-180.png` `icon-192.png` `icon-512.png` をアップロード
3. リポジトリの Settings → Pages → Branch を `main` / `/(root)` にして保存
4. 少し待つと `https://<ユーザー名>.github.io/mod-bench/` で開ける

### 2. トークンを作る

GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token

- Repository access: **Only select repositories** でmodのリポジトリだけ選ぶ
- Repository permissions:
  - **Actions**: Read and write（ビルド開始・中止・やり直し）
  - **Contents**: Read and write（ワークフロー追加・gradle.properties読み取り）
  - **Workflows**: Read and write（ワークフロー追加。手動で置くなら不要）
- 有効期限は好きに。切れたらアプリが「トークンが無効」と出すので作り直す

### 3. アプリで接続

1. アプリを開くと設定画面が出るので、`owner/リポジトリ名` とトークンを入れる
2. 「ワークフローを追加」を押すと `.github/workflows/build.yml` がコミットされる
   - 自分で置きたい時は同梱の `build.yml` をそのまま `.github/workflows/` に入れる
3. 「クラフト開始」でビルド

### 4. ホーム画面に追加

- iPhone: Safariで開いて 共有 → ホーム画面に追加
- Android: Chromeのメニュー → ホーム画面に追加

## 仕組みメモ

- `build.yml` は `workflow_dispatch` で起動、`./gradlew <タスク>` を実行
- jarは2か所に置かれる
  - Actionsのartifact `mod-jars`（14日保存）
  - `dev-build` という名前のプレリリース（毎回上書き）。スマホから直接落とせるのはこっち
- コンパイルエラーは javac / Kotlin の出力から拾ってGitHubのアノテーションにしてる。GitHubの上限で1回に表示できるのは9件まで
- 非公開リポジトリのjarを落とす時は、ブラウザでGitHubにログインしておく必要がある
- トークンは「この端末に保存」をオンにするとブラウザ内に保存される。共有端末ではオフにする
- Gradle wrapper（`gradlew`）がリポジトリにコミットされている前提
