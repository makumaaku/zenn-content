---
title: "正規表現を用いたemailのvalidationについて考える(Flutter)" # 記事のタイトル
emoji: ""📩" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Dart","正規表現"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに
Flutterでアプリ開発をしていると、emailログインを実装する機会は必ずあるといっても過言ではないと思います。
その際のemailのバリデーションで、正しいEメールの形式かをチェックする必要があると思います。
そこで正規表現(Regular Expression)という暗号みたいなものにぶつかったので、メモしていこうかなと。
​
## サクッとemailのvalidationを実装する用のコード
以下をコピペすればサクッと実装できます。
僕ならここだけコピペしてサクッと実装しますね。
理解する必要が出てきた or 理解したくなったときに、この記事を最後まで読み進めると思いますw
```dart
var email = "makumaaku@makumaaku.com"
bool emailValid = RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+").hasMatch(email);
```
引用元
https://stackoverflow.com/questions/16800540/validate-email-address-in-dart
​
## emailのvalidationを使って正規表現を理解していく
​
参考
https://www.mlab.im.dendai.ac.jp/~yamada/java/regex/
​
まずは、上で示した以下のコードが何を示しているのか理解していきましょう！
```dart
RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+")
```
​