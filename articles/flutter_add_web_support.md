---
title: "既存のFlutterプロジェクトでwebをサポートする" # 記事のタイトル
emoji: "🖥" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter,FlutterWeb"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに

既存の Flutter プロジェクトで、WEB をサポートしたい！という機会がありました。  
せっかくなので、方法をメモとして残しておきたいと思います。

## web をサポートするためのコマンド

プロジェクトのルートディレクトリで、以下のコマンドを叩けば OK です。

```
flutter create .
```

## web 版の build

`flutter create .`を入力後に以下のコマンドで、chrome が起動するか確認してください。

```
flutter run -d chrome
```

## 参考

https://docs.flutter.dev/get-started/web#add-web-support-to-an-existing-app
