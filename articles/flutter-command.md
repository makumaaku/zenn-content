---
title: "Flutter開発におけるコマンドロードマップ" # 記事のタイトル
emoji: "🐣" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter","Commnad"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## はじめに
Flutterでアプリ開発する際によく使うコマンド。  
いったい何者なのか〜よく使うものの紹介まで。
(随時更新予定)

## まずPATHを通すとは何か？
特定のプログラムを「プログラム名だけで実行できるようにする」こと。

### 内部で何が起こっているか
1. コマンドを入力する
2. シェルがコマンド検索パス一覧からコマンドを探す
3. コマンドが発見できれば、実行。なければ、`command not found: xxx`

もうちょっと具体的に説明すると、PATHを通すとは、`コマンド検索パス一覧`にパスを追加すること。

さあ、次は`シェル`と`コマンド検索パス一覧`が出てきたぞ..

### シェルってなに？
`OSとアプリケーションの間に立ってユーザからの要求をシステムに伝えるためのユーザインターフェースで、要はコマンドを入力することで動くプログラムのことをシェルといいます。`
(参考記事より抜粋)

よしわかった。以下の2文は同じ意味なんだね。  
- シェルがコマンド検索パス一覧から入力コマンドを探す
- 内部で`コマンド検索パス一覧`を探すプログラムが動き、入力コマンドがあるか探す

### シェルについての参考記事
https://mykii.blog/what-is-shell-bash-and-zsh/#:~:text=OS%E3%81%A8%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E9%96%93,%E3%82%92%E3%82%B7%E3%82%A7%E3%83%AB%E3%81%A8%E3%81%84%E3%81%84%E3%81%BE%E3%81%99%E3%80%82

### ここで言うコマンド検索パス一覧の確認方法
`コマンド検索パス一覧`って何者だ？と。
以下のコマンドで確認できます。  
```
echo $PATH
```

僕の場合は以下のようになりました。
`<user名>`の部分にあなたのユーザー名が入ります。
```
/Users/<user名>/.rbenv/shims:/Users/<user名>/.rbenv/bin:/Users/<user名>/opt/anaconda3/bin:/Users/<user名>/opt/anaconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/share/dotnet:~/.dotnet/tools:/Library/Apple/usr/bin:/Library/Frameworks/Mono.framework/Versions/Current/Commands:/usr/local/Cellar/libpq/13.1/bin:/Users/<user名>/Desktop/flutter/bin:/Users/<user名>/.pub-cache/bin:/Users/<user名>/Desktop/flutter/.pub-cache/bin:/Users/<user名>/Library/Android/sdk/platform-tools
```

つまり、以下のPATHリストが僕が通した(もともと通っていた)PATH達です。

- /Users/<user名>/.rbenv/shims
- /Users/<user名>/.rbenv/bin
- /Users/<user名>/opt/anaconda3/bin
- /Users/<user名>/opt/anaconda3/condabin
- /usr/local/bin
- /usr/bin:/bin
- /usr/sbin:/sbin
- /usr/local/share/dotnet:~/.dotnet/tools
- /Library/Apple/usr/bin:/Library/Frameworks/Mono.framework/Versions/Current/Commands:/usr/local/Cellar/libpq/13.1/bin
- /Users/<user名>/Desktop/flutter/bin
- /Users/<user名>/.pub-cache/bin
- /Users/<user名>/Desktop/flutter/.pub-cache/bin
- /Users/<user名>/Library/Android/sdk/platform-tools

僕がflutterコマンド(`flutter clean`など)を実行できるのは、
`/Users/<user名>/Desktop/flutter/bin`
上記のパスにflutterの実行ファイルがあるからです。  

ちなみにandroidのデバッグ時によく使用する`adb`コマンドも、
`/Users/<user名>/Library/Android/sdk/platform-tools`
このパスがリストに入っていないと使用できません。(`command not found: adb`)

こちらの記事を参考にしています。
https://qiita.com/soarflat/items/09be6ab9cd91d366bf71
https://qiita.com/sta/items/63e1048025d1830d12fd

### PATHを通すとは何か？の結論
ここまでの説明を読めばなんとなくイメージができるようになったと思います。
シェルが検索すべきPATHの一覧に、 新たなPATHを加えることですね。
シェルが探し当てれるように。

参考記事では、このことを以下のようにスッキリ一言にまとめてくれています。
- 特定のプログラムを「プログラム名だけで実行できるようにする」こと。
- シェルがコマンド実行ファイルを探しに行くパスのこと。

PATHを通すとは何か？がわかったところでPATHの通し方をマスターしましょう！

## PATHの通し方
さあ、以下の記事を参考にすれば余裕でしょ？
https://qiita.com/nbkn/items/01a11392921119fa0153

ぬ？設定ファイル`.bash_profile`とはなんぞ。。
もうちょっと前提を勉強してからですね。

### 使用しているシェルの確認
まずは自分のMACで使用しているシェルを確認しましょう。
MACにはさまざまな種類のシェルが存在しています。
よく見かけるのは、以下の2種類ですね!
- bash
- zsh

ところで自分が使用しているシェルは？
以下のコマンドで確認できます。
```
echo $SHELL
```
すると、
`/bin/zsh`
僕は`zsh`を使用しているとわかりました。

### PATHを保持する場所
使っているシェルに応じて、PATHを保存する場所も変化します。
- bash => `.bash_profile`
- zsh => `.zshrc`

なので、僕の場合は`.zshrc`にPATHを通せばよいというわけです。

### .zshrcファイルの編集
.zshrcファイルの編集は、以下のコマンドで可能です。
```
vi ~/.zshrc
```

flutterコマンドのパスを通したい場合は、以下をファイル内に追加すればOKです。
```
export PATH="$PATH:$HOME/Desktop/flutter/bin"
```

viってなに？という方のために軽く以下で説明します。

### viコマンドとは
viコマンドとは、ファイル編集コマンドです。
最初はちょっと扱いづらいですが、使うコマンドは限られてくると思います。

- `i (文字入力)`
- `:wq (ファイル保存)`
- `escボタン (文字入力モードの解除)`
`i`でインサートモードにして、文字を編集したら、`esc`ボタンで文字入力モードを終了し、`:wq`+ `Enter`キーで編集完了！という流れが多い気がします。


参考
https://eng-entrance.com/linux-vi-is#vim
http://yamabito.main.jp/linux/vi_commands.htm

### PATHを通した後は
- ターミナルを開きなおす
- `source ~/.zshrc`
のどちらかで変更が反映されます。

よし、PATHが通せるようになった。
もう1ステップ進もう！

## よく使うコマンドのショートカットを作成する
flutterを触っていて、
`flutter clean`,`flutter pub get`くらいなら楽勝で覚えられる。
しかし、
`build_runner`などを使用して、コードの自動生成を行う時に用いるコマンド

https://pub.dev/packages/build_runner

```
flutter pub run build_runner build --delete-conflicting-outputs
```
これを覚えるのは大変です。
これは毎回どこかにメモっておいたり、ググったり..
こんな時に、コマンドのショートカットの作成が便利です。

### コマンドのショートカットの作成方法
手順は以下の通りです。

#### `.zshrc`編集開始
```
vi ~/.zshrc
```
`i`を押して、インサートモードに切り替えてください。

#### ショートかっとを作成したいコマンドを追加
以下のようにショートカットを作成できます。
```
alias コマンド名='コマンド'
```
コマンド名は自由に決められます。

僕は今回、
- コマンド名 `fbr`(flutter build runner の頭文字を取ってみました ^^;)
- コマンド `flutter pub run build_runner build --delete-conflicting-outputs`
でショートカットを作成します。

```
alias fbr='flutter pub run build_runner build --delete-conflicting-outputs'
```
作成後は,`esc`ボタン => `:wq` => `Enter`ボタンで編集完了！
変更を反映させるために、
```
source ~/.zshrc
```
で完了です。
Flutterプロジェクト内で、自分の作ったコマンドを叩いてみてください。  
ああ、気持ち良い。

参考
https://www.choge-blog.com/%E3%83%A9%E3%82%A4%E3%83%95%E3%83%8F%E3%83%83%E3%82%AF/macterminalshortcutcommandcreate/
https://qiita.com/bonny_d/items/c5f0b0c2d2fdac8734f6

## 最後
いかがでしたか。
- PATHを通すとは何か
- PATHの通し方
- ショートカットコマンドの作り方
この３つを完全に理解できましたか？
この3つはプログラミングをやっていくなら、半永久的に役に立つ基本知識です。  
早めにマスターしましょう！

(よく使用するコマンドなども追加予定です)