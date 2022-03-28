---
title: "Flutterで数字のカンマ区切りを実装する方法" # 記事のタイトル
emoji: "🔢" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
Flutterで数字をカンマ区切りで表示したいな。と思ったのですが、コードがさっと思い付かず。
例えば、`1000円`でなく、`1,000円`と表示したい場合などです。    
コピペ用にメモしておけたらと思います。

## Flutterでカンマ区切りをする
かなり簡単で、2ステップで完了です。
- `intl`をインストールしてください。
- 実装する

https://pub.dev/packages/intl

## 数字にを3桁ごとに区切る場合のコピペ用コード
```dart
import 'package:intl/intl.dart';

String addCommaToNum({required int num}){
  final formatter = NumberFormat("#,###");
  String numWithComma = formatter.format(num);
  return numWithComma;
}
```

もっと複雑なケースは参考記事を参照してください。

## 参考
以下の記事がとても参考になりました。
https://qiita.com/mcz9mm/items/e1407ad24384eb92737d