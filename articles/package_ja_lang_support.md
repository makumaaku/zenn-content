---
title: "MacでFlutter×VScodeで開発する場合のTips" # 記事のタイトル
emoji: "💡" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに

## プルリク
https://github.com/FirebaseExtended/flutterfire/pull/8110

## 手順

packageをフォークしてくる
https://docs.github.com/ja/get-started/quickstart/fork-a-repo
flutterfire系のフォーク例
https://www.kamo-it.org/blog/flutter-analytics/

Contributor Guideを読む
https://github.com/FirebaseExtended/flutterfire/blob/master/CONTRIBUTING.md

###
`dart pub global activate flutter_plugin_tools`
https://pub.dev/packages/flutter_plugin_tools

### 
https://clang.llvm.org/docs/ClangFormat.html
https://macappstore.org/clang-format/

10分以上かかったからフリーズしたかと思ったw
```
Downloading Command Line Tools for Xcode
Downloaded Command Line Tools for Xcode
Installing Command Line Tools for Xcode
```

### CLAに同意する
https://cla.developers.google.com/clas

### melosv1.3.0が動かない..

```
(base) marksito@MacBook-Pro-4 flutterfire_mark % melos --help.
Building package executable... 
Failed to build melos:melos:
../../../.pub-cache/hosted/pub.dartlang.org/melos-1.3.0/lib/src/command_runner/version.dart:244:56: Error: The getter 'name' isn't defined for the class 'SemverReleaseType'.
 - 'SemverReleaseType' is from 'package:conventional_commit/conventional_commit.dart' ('../../../.pub-cache/hosted/pub.dartlang.org/conventional_commit-0.4.2/lib/conventional_commit.dart').
Try correcting the name to the name of an existing getter, or defining a getter or field named 'name'.
        .firstWhereOrNull((releaseType) => releaseType.name == argument);

```

### pub global activate でバージョン指定する
なんとかバージョン指定する方法を発見して、v1.2.2に
https://scrapbox.io/ebiken/pub_global_activate_%E3%81%A7%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E6%8C%87%E5%AE%9A%E3%81%99%E3%82%8B

## melos 

この警告のせいで`melos run analyze`が通らなかった..
https://dart-lang.github.io/linter/lints/eol_at_end_of_file.html
直したらちゃんと通りました！

```
[flutterfire_ui_example]: No issues found!
[flutterfire_ui]: No issues found!

```

## 全コードのフォーマット化の依頼がきた
`melos run format`を走らせるだけでOKとのこと。

`command not found: swiftformat`とのエラーが出た
=> `brew install swiftformat`
参考
https://command-not-found.com/swiftformat

