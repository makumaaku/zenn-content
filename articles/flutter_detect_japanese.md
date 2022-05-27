---
title: "Flutterで日本語を含むかチェックする方法" # 記事のタイトル
emoji: "🇯🇵" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
今回はテキストに日本語が含まれているかどうかをチェックする方法をご紹介します。  
正規表現でサクッと実装できます。  

## 日本語を含むかどうかのチェック方法
以下でチェックができます。  
```dart
bool containJapanese(String text) {
  return RegExp(r'[\u3040-\u309F]|\u3000|[\u30A1-\u30FC]|[\u4E00-\u9FFF]')
  .hasMatch(text);
}
```

### 全ひらがな
```
\u3040-\u309F
```

### 全角カタカナ(長音符込み)
```
\u30A1-\u30FC
```

### 漢字
```
\u4E00-\u9FFF
```

## 最後に
サクッと書いてみましたが、漏れや抜けがあるかもしれないです。  
もし詳しい方いらっしゃいましたら、ご指摘いただけたらと思います。　　

## 参考
https://so-zou.jp/software/tech/programming/tech/regular-expression/meta-character/variable-width-encoding.htm
https://tama-san.com/kanji-regex/



