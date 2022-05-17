---
title: "FlutterFire UIにプルリク出してみた話(翻訳)" # 記事のタイトル
emoji: "🔥" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Firebase"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
僕がお話しするのはタイトルにある通り、firebaseの公式パッケージであるflutter_fire_uiにプルリクを出してみたという経験についてです。 
flutter_fire_uiはflutter3のアナウンスの中にも含まれているパッケージです！  

github  
https://github.com/firebase/flutterfire/tree/master/packages/flutterfire_ui

pub.dev  
https://pub.dev/packages/flutterfire_ui

## 動機
flutter_fire_uiを知ったのは`FirestoreListView`の存在を知った時です。  
ページングを簡単に実装できて最高じゃないかと思って、速攻導入しました。  
https://twitter.com/marksaito4/status/1469879685651832840

そして何より驚いたのが、ほんの数行で、`SignInScreen`や`ProfileScreen`の実装ができてしまうことです。  

```dart
import 'package:flutter/material.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:flutterfire_ui/auth.dart';

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    const providerConfigs = [EmailProviderConfiguration()];

    return MaterialApp(
      initialRoute: FirebaseAuth.instance.currentUser == null ? '/sign-in' : '/profile',
      routes: {
        '/sign-in': (context) {
          return SignInScreen(
            providerConfigs: providerConfigs,
            actions: [
              AuthStateChangeAction<SignedIn>((context, state) {
                Navigator.pushReplacementNamed(context, '/profile');
              }),
            ],
          );
        },
        '/profile': (context) {
          return ProfileScreen(
            providerConfigs: providerConfigs,
            actions: [
              SignedOutAction((context) {
                Navigator.pushReplacementNamed(context, '/sign-in');
              }),
            ],
          );
        },
      },
    );
  }
}
```
[flutter_fire_uiのREADME](https://pub.dev/packages/flutterfire_ui)より引用  

しかし、僕が最初に触った頃は実装後の画面の文字は全て英語でした。  
「日本人が本格的に使えるようになるのはもうちょい先か...」  
と思っていました。  

そんな時に、他の言語の翻訳のプルリクを発見しました。  
https://github.com/firebase/flutterfire/commit/c3a1a839a3963a75cc17e931a3eee6e091df40ac

https://github.com/firebase/flutterfire/commit/c47f60757ccbfcee1eaa5d7ed6ee01258f3b9d4f

オープンソースにコミットするという経験はなかったので、怖かったのですが、  
「よし、日本語も行こう。」  
と思い、プルリクを作ってみようと思いました。  

## プルリクを作るまでの大まかな流れ
プルリクを作るまでの手順は以下の通りです。  
1. [flutterfireのリポジトリ](https://github.com/firebase/flutterfire)を自分のリポジトリにフォークする
1. フォークしてきた自分のリポジトリで変更
1. 変更分のプルリクを作成([参考](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork))
1. [プルリク](https://github.com/firebase/flutterfire/pull/8110#issue-1141534309)時にデフォルトで用意されている`Description`の記載や`CheckList`のチェック

という流れでした。  
最後のCheckListを埋める作業が自分的には難しかったです。  

実際に作ったプルリクはこちらです。  
https://github.com/firebase/flutterfire/pull/8110

## プルリク完成までの具体的な説明
流れはざっくりと掴めたと思うので、もうちょっと詰まったところを具体的に説明していこうかなと思います。  
基本は用意されているチェックリストに沿って作業していくだけです。  
![](/images/commit-to-flutter-fire/flutterfireui_pull_request.png)   
プルリクの作り方も[Contributor Guide](https://github.com/firebase/flutterfire/blob/master/CONTRIBUTING.md)にきちんと載っています。  

### melosの導入
flutterfireのパッケージはモノレポで作られているので、`melos`を使って、テスト・解析・フォーマット化などを行うようです。  
Contributor Guide内の[Environment Setup](https://github.com/firebase/flutterfire/blob/master/CONTRIBUTING.md#3-environment-setup)を参考にmelosのセットアップをします。  

### melosでのチェック
セットアップしたmelosを使って、解析・フォーマット化を行います。  
> Once you have made your changes, ensure that it passes the internal analyzer & formatting checks. The following commands can be run locally to highlight any issues before committing your code:
```
# Run the analyze check
melos run analyze

# Format code
melos run format
```
僕はフォーマット化が漏れていたので、きっちり[指摘](https://github.com/firebase/flutterfire/pull/8110#issuecomment-1049722770)されています..  

### CLA(Contributor License Agreements)に同意
プルリクのチェックリストにあるように、Contributor License Agreementsに同意する必要があるようです。  
https://cla.developers.google.com/

![](/images/commit-to-flutter-fire/cla.png)  

### 翻訳の場合はダブルチェックが必要
翻訳の場合はダブルチェックが必要なようです。  
firebaseのチームに日本人が居れば話は別だったかもしれませんが^^;  
以下のように「日本語がわかる仲間にダブルチェックをお願いしてくれ」と言われました。  
https://github.com/firebase/flutterfire/pull/8110#issuecomment-1047662948

今回は、僕をプログラミングに誘ってくださった[osm-y](https://github.com/osm-y)さんに協力していただきました！  
岐阜で可茂IT塾というコミュニティを作っている方です。  
ちなみに僕もそこのメンバーです^^  

https://www.kamo-it.org/member/

## リリースされました
バージョン0.4.0で反映されていると思います。  
https://pub.dev/packages/flutterfire_ui/changelog#040

## 感想
さすがに有名どころであるfirebaseのパッケージなので、チェックも厳重ですね。  
しかも、オープンソースにコミットをしたこともなかったので、余計にハードルが高く感じました。  
プルリク自体は`2022/2/18`に作ったのですが、マージされたのは`2022/3/22`と1ヶ月以上かかりました。　　
正直なところ、
「永遠にマージされないかもしれない..」  
と思い、プルリクを取り下げようか迷っていました^^;  

最終的にはマージしていただけたので、勇気を持ってプルリクを作ってみてよかったなと感じています。  

## 注意点
もちろんオープンソースへのコミットは`「コミットしたコードへの責任が発生する」`ということです。  
以下のように追加の翻訳をお願いされることもあります。  
https://github.com/firebase/flutterfire/pull/8366#issuecomment-1084481916
一度コミットして終了！という訳にはいかないと思います。　　


## 最後に
一般的にオープンソースへのコミットはハードルが高いものと思われます。  
特に1度も経験がなければ、尚更です。  
この記事を通じて、  
「大体のイメージはついたかな。」  
という状態になっていただけたなら幸いです。 
日本も負けじとがんばりましょう！　　

Twitterでは、Flutterの技術関連の発信をしています！
https://twitter.com/marksaito4









