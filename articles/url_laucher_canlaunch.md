---
title: "Flutterのurl_launcherでURLが開けない判定される場合の対処" # 記事のタイトル
emoji: "🔗" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Dart"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
flutterのパッケージurl_launcherを使っていて、URLが開けるはずなのに、
`canLaunch(url)`が`false`になってしまう..
という問題があったので、メモしておこうかなと。

https://pub.dev/packages/url_launcher

## URLが開けなかった原因
そのURLにはカタカナが含まれていました。日本語がURLに含まれているのがおそらく原因だったと思います。
例えばこんな感じに(このURLは存在しません)
`https://zenn.dev/articles/url_laucher_canlaunch/テスト１`

`canLaunch`が`false`になってしまったら、以下のように、URLをエンコードしてから再度チェックしてみてください。

```dart:url_launcher.dart
Future<void> launchUrl(String url) async {
final _canLaunch = await canLaunch(url);
 if (_canLaunch) {
    final success = await launch(url);
    print('success :$success $url');
    return;
  }
  final encodedUrl = Uri.encodeFull(url);
  final _canLaunchAgain = await canLaunch(encodedUrl);
  if (!_canLaunchAgain) {
    print('link launch error $encodedUrl');
    return;
  }
  await launch(encodedUrl);
}
```
## 最後に
プロジェクト内に、このようなメソッドを作成して、url_launcherを使用する場合は、この処理をくぐらせるようにしています。
もしプロジェクト内で、`canLaunch(url)`と`launch(url)`をバラバラで使用している場合は、上記のようなコードをメソッド化しておいて、使用すると良いかもしれません。
