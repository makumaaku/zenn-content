---
title: "プリウスとアクアから学ぶFlutterでのシングルトン" # 記事のタイトル
emoji: "🔼" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Singleton"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
今までシングルトンを使ったことがなかったので、一度詳しくまとめてみようと思います。
シングルトンの使い方についてざっくりと理解したい方は、`プリウスとアクアを使ってシングルトンを体感してみる`から読んでいただくのが良いかなと思います。  

## シングルトンとは
- シングルトンパターンで実装されたクラスは、実行時に１つしかインスタンスが生成されない
これだけではわかりにくいので、以下に具体例を示します。

### 普通のクラス
```dart
class MyClass{
    //...中身
}

final instance1 = MyClass();
final instance2 = MyClass();
```
`instance1`と`instance2`は同一の`MyClass`クラスからそれぞれのインスタンスを生成しています。  
インスタンスが複数なので、形は同じだが内部の状態(state)は異なるオブジェクトとして扱われます。

### シングルトンで実装されたクラス
```dart
class Singleton{
    //...中身
}

final instance1 = Singleton();
final instance2 = Singleton();
```
`instance1`と`instance2`は同一の`Singleton`クラスで同一のインスタンスを参照しています。
同じインスタンスを参照しているので、内部の状態(state)が共通の`Singleton`オブジェクトです。

プログラミングを始めたばかりだと、  
「同じクラスを呼んでいるんだから、内部のstateも共通になってるのでは?」  
と思ったりもしますが、普通は違います。  
１つのインスタンス毎に1つのステートを持ちます。  

シングルトンでクラスを実装すれば、どこから`Singleton()`を呼んでも同じ内部状態(ステート)になっているわけです。  

## シングルトンの作り方
Flutter (Dart)でのシングルトンの作り方について説明します。
- コンストラクタ`Singleton._internal()`をプライベート(アンダーバーをつけた状態)にし、外部から呼び出せないように
- クラス(staticな)変数にインスタンス(`Singleton._internal()`)を格納
- 外部から呼ばれる時には、(プライベートなインスタンスを格納した)クラス変数を返すように

```dart
class Singleton{
  static final Singleton _instance = Singleton._internal();

  factory Singleton(){
    return _instance;
  }

  Singleton._internal();
}
```

## プリウスとアクアを使ってシングルトンを体感してみる
具体例として、プリウス(Prius)君とアクア(Aqua)君に登場してもらいましょう。
普通のクラス`Prius`とシングルトンクラスの`Aqua`にそれぞれオプションをつけるメソッドを用意してやりましょう。

### 普通のクラスPrius
以下がプリウス君です。
```dart
class Prius {
  Prius();

  List<String> options = [];

  List<String> addOption(String option) {
    return options..add(option);
  }
}
```

このプリウス君にオプションをつけてやりましょう。
```dart
void main() {
  final prius1 = Prius();
  print(prius1.addOption('カーナビ')); //[カーナビ]
  print(prius1.addOption('ドラレコ')); //[カーナビ, ドラレコ]
  final prius2 = Prius();
  print(prius2.addOption('ヒーター')); //[ヒーター]
}
```
こちらの例では、同じプリウスでも`prius1`と`prius2`は違う車になっています。
オプションとして`カーナビ`と`ドラレコ`をつけた後に、`ヒーター`をつけましたが、違う車にそれぞれ取り付けてしまっています。  

ここで、シングルトンで作られたアクア君にオプションをつけてやりましょう。

### シングルトンクラスAqua
以下がアクア君です。
```dart
class Aqua {
  static final Aqua _instance = Aqua._internal();
  List<String> options = [];

  factory Aqua() {
    return _instance;
  }

  Aqua._internal();

  List<String> addOption(String option) {
    return options..add(option);
  }
}
```

このアクア君にオプションをつけてやりましょう。
```dart
void main() {
  final aqua1 = Aqua();
  print(aqua1.addOption('カーナビ')); //[カーナビ]
  print(aqua1.addOption('ドラレコ')); //[カーナビ, ドラレコ]
  final aqua2 = Aqua();
  print(aqua2.addOption('ヒーター')); //[カーナビ, ドラレコ, ヒーター]
}
```
`Aqua`はシングルトンクラスなので、`aqua1`と`aqua2`は同じ車を表しています。
したがって、`aqua1`と`aqua2`にそれぞれ取り付けたオプションは、全て反映されていますね。

## 最後に
今回の記事でFlutterでのシングルトンの作り方・使い方は理解できましたか？
シングルトンを使ったことがない方の参考になれば幸いです。

TwitterではFlutterの技術関連の発信をしています！
https://twitter.com/marksaito4

