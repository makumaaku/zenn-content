---
title: "Flutterのurl_launcherでURLが開けない判定される場合の対処" # 記事のタイトル
emoji: "🔗" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Dart"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
url_launcherを使っていて、URLが開けるはずなのに、
`canLaunch(url)`が`false`になってしまう..
という問題があったので、メモしておこうかなと。

https://pub.dev/packages/url_launcher

​
## 原因
そのURLにはカタカナが含まれていました。  
以下のように`canLaunch`が`false`になってしまったら、URLをエンコードしてからチェックしてみてください。

```dart
final _canLaunch = await canLaunch(url);
 if (_canLaunch) {
    final success = await launch(url);
    print('success : $url');
    return;
  }
  final encodedUrl = Uri.encodeFull(url);
  final _canLaunchAgain = await canLaunch(encodedUrl);
  if (!_canLaunchAgain) {
    print('link launch error $encodedUrl');
    return;
  }
  await launch(encodedUrl);
```
プロジェクト内に、このようなメソッドを作成して、url_launcherを使用する場合は、この処理をくぐらせるようにしています。
​

​