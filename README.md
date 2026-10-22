# Mod作業台

スマホからJava版modをビルドするWebアプリ。modのフォルダをzipにしてアップロードすると、GitHub Actionsがビルドしてjarを返してくれる。PCは起動してなくてOK。

アプリ: https://eeeeeeruki.github.io/mc-build/

## 使い方

1. トークンを作る（https://github.com/settings/personal-access-tokens/new）
   - Repository access: **Only select repositories** → `mc-build-uploads`
   - Permissions: **Actions / Contents / Workflows** を「Read and write」
2. アプリの「設定」にトークンを貼って「保存して接続」
   - アップロード先は空のままなら `EeeeeeruKi/mc-build-uploads` を使う
3. modのフォルダ（build.gradle がある階層）をzipにして選ぶ
4. 「アップロードしてクラフト」→ 数分で「最新のjar」からダウンロード

## アプリが自動でやること

- zipの中の `build/` `.gradle/` `run/` `.idea/` などを外してから送る
- フォルダが入れ子でも、`build.gradle` がある階層を探す
- `gradle.properties` の `minecraft_version` からJavaを選ぶ（1.20.1→17、1.20.5以降→21 など）
- Forge / NeoForge / Fabric / Quilt を見分けて、表示名を `mod_name` と `mod_version` から付ける
- 失敗したら、どのファイルの何行目でコンパイルエラーが出たかを表示

## 仕組みメモ

- アップロードしたzipは非公開リポジトリ `mc-build-uploads` の `upload/…` ブランチに置かれる
- `upload-build.yml`（mc-build-uploads 側）がzipを展開してビルドし、jarをリリースに置く
- `gradlew` が無いzipでも、`gradle-wrapper.properties` のバージョンか最新のGradleでビルドする
- アップロードとjarは新しいもの10個まで残って、古いものはビルド時に自動で消える
- 非公開リポジトリのjarがアプリから落とせない時は、GitHubにログインした状態でリリースページから落とす
- トークンは「この端末に保存」をオンにするとブラウザ内に保存される。共有端末ではオフにする
