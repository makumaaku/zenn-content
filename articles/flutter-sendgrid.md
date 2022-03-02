---
title: "FlutterでSendGrid経由でメールを送信する" # 記事のタイトル
emoji: "📩" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","SendGrid"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに
FlutterからSendGridを利用してメールを送信するという機会があったので、その際の経験をまとめておきます。


## APIKEYを発行するだけだと何も設定しないと以下のエラーになる。
```
SendGridError: The from address does not match a verified Sender Identity. Mail cannot be sent until this error is resolved.
```

## 差出人の認証が必要
