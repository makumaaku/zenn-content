---
title: "extends・implements・withの違いってなんだ" # 記事のタイトル
emoji: "❓" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに
Flutterでクラスを作る時によく現れる
- extends
- implements
- with
この３つ。
Widgetを作るときはに、しれっと`StatelessWidget`を`extends`しています。
```dart
import 'package:flutter/material.dart';

class MyScreen extends StatelessWidget {
  const MyScreen({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```
しれっとしすぎて気になっていませんでしたが、今回は気にしてみようと思います。

https://stackoverflow.com/questions/55295782/extends-versus-implements-versus-with
参考





## 最後に


TwitterではFlutterの技術関連の発信をしています！
https://twitter.com/marksaito4

