---
title: "Dart2.17で追加された拡張enumを試す" # 記事のタイトル
emoji: "🤔" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter", "enum"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに

Dart2.17 から enum の機能が拡張されたようなので、使い方を試してみようと思います。

## SDK バージョンの下限を 2.17.0 以上に

「さあ、はじめよう。」と思ったらいきなりエラー出ましたね。  
SDK バージョンの下限が`2.15.0`になってました。

![](/images/flutter_enum_extension/error_minmum_sdk.png)

```
This requires the 'enhanced-enums' language feature to be enabled.  Try updating your pubspec.yaml to set the minimum SDK constraint to 2.17.0 or higher, and running 'pub get'.
```

`pubspec.yaml`の`environment`部分を更新します。

```yaml:pubspec.yaml
environment:
  sdk: ">=2.17.0 <3.0.0"
```

## 中身を見てみる

参考の medium の記事で紹介されている例を使っていきます。

### 主な追加機能

- enum にコンストラクタを追加できるようになった(state を持てるようになった)
- toString()をオーバーライドできるようになった

enum のそれぞれの要素毎にコンストラクタを初期化できる。

```dart
enum Water {
  frozen(32),
  lukewarm(100),
  boiling(212);

  final int tempInFahrenheit;
  const Water(this.tempInFahrenheit);

  @override
  String toString() => "The $name water is $tempInFahrenheit F.";
}

```

ちなみに`$name`の`name`は Dart の 2.15 から追加されている機能ですね。

```dart
final w = Water.frozen;
print(w.name); //frozen
```

## toString()を使ってみる

この状態で print してみると、

```dart
 print(Water.frozen); //The frozen water is 32 F.
 print(Water.frozen.toString()); //The frozen water is 32 F.
```

なるほど。
`print`しようとすると、オーバーライドした`toString()`メソッドが勝手に反映されているのかな。

### toString()をコメントアウトしたら

```dart
 // @override
 // String toString() => "The $name water is $tempInFahrenheit F.";
```

```dart
 print(Water.frozen); //Water.frozen
```

うむ。当たり前ですが従来通り。

**参考**
https://medium.com/dartlang/dart-2-17-b216bfc80c5d

### enum にメソッドを生やす

enum の判定メソッドも実装可能なようですね。
今までは`extension`を使って書く必要がありました。

```dart
 bool get isFrozen => this == frozen;
```

enum をよく条件分岐の際に使うのですが、if 文の中で以下のような条件を書くのが面倒だと思っていました。(特に enum の名前が長いときは)

```dart
if(type == SampleType.typeA){
 ...
}
```

extension を使った書き方はこちらの記事にまとめてあります。  
https://www.kamo-it.org/blog/flutter-extension/

## まとめ

ざっくりと試してみましたが、enum に state を持たせられるので、

- enum で条件分岐
- 条件に応じてテキスト表示
  みたいな場合には、enum 側に寄せて簡潔に書けるという感じでしょうか。
  意識して使っていこうと思います。

## 参考

https://medium.com/dartlang/dart-2-17-b216bfc80c5d
https://qiita.com/hosikiti/items/9242a9e1d50c1dc5faa2
https://codewithandrea.com/tips/enums-with-members-dart-2.17
