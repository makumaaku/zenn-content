---
title: "FlutterWeb+Firebase Hostingで環境分けした管理ツールを作成する" # 記事のタイトル
emoji: "👨‍🏭" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Flutter,FlutterWeb"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

## はじめに

今回は Flutter Web + Firebase Hosting を用いて、管理ツールを作成する機会があったので、その手順を記録しておこうと思います。  
環境は以下の 3 つを用意します。

- 開発(dev)
- ステージング(stg)
- 本番(prod)

## 既存の Firebase プロジェクトで web をサポートする

既存のプロジェクトが web をサポートしていない場合は、以下のコマンドを叩けば OK です。

```bash
flutter create .
```

https://docs.flutter.dev/get-started/web#add-web-support-to-an-existing-app

## 管理ツールの初回デプロイ

ざっくりとした流れは以下です。

- Firebase でプロジェクト作成
- Firebase のプロジェクト設定からウェブアプリの登録
- `firebase init hosting`
- `flutter build web`
- `firebase deploy --only hosting`

わかりやすい記事があるので以下を参考にしてみてください。　　
今回は、`開発(dev)`,`ステージング(stg)`,`本番(prod)`の 3 つの環境があるので、Firebase のプロジェクトも 3 つ用意しています。  
必要な環境数に応じて、作成してください。

参考
https://www.flutter-study.dev/host-web-app/hosting
https://zenn.dev/pressedkonbu/articles/deploy-flutter-web-app-with-firebase-hosting

## 管理ツールのフォルダ構成

`lib`ファイルの中に`web`ディレクトリを作成し、その中に管理ツールのソースコードを書いていくことにしました。

```
root
  ┣ lib
     ┣ web
  ┣ web
    ┣ index.html
```

選択肢としては、以下の２つがありました。

- `lib`の中に`web`ディレクトリを作成
- 別プロジェクトとして作成
  アプリ側との共通コンポーネントが使いまわすために、`lib`ファイルの中で管理することにしました。
  以下で説明していますが、
  - index.html の環境切り替え
  - dart-define を使用した環境切り替え
    の２つを使用してしまっているので、ちょっとややこしくなってしまったかなとも思います。。

### main.dart 内での分岐

`lib`ディレクトリ内で管理することになったので、

- アプリ側を build
- 管理ツールを build
  この２つをどこかで分岐させないといけない分けです。

以下のように`runApp`部分で、以下のようにアプリと管理ツールを分岐しました。

```dart:main.dart
runApp(kIsWeb ? AdminApp():MyApp());
```

## firebase の config の設定

firebase の`projectId`や`apiKey`を設定する必要があります。  
ios と android は、以下で Firebase の config 部分の設定しています。  
ios => `ios/Runner/GoogleService-info.plist`  
android => `android/app/google-services.json`

しかし、web の部分は設定されていないので、`flutter run -d chrome`で管理ツールを起動する際に、以下のようなエラーが発生しました。

```
FirebaseOptions cannot be null when creating the default app
```

以下の部分で web の分は設定する必要がありました。

```dart
 await Firebase.initializeApp(
    options: //ここで設定する
```

参考
https://zenn.dev/maropook/articles/f82c98a56b14ca

### コマンドによる firebase_options.dart の自動生成

「さて、以下のコマンドで自動生成するか！」  
と思ったのですが、この構成部分も`開発`,`ステージング`,`本番`の環境分けが必要でした。

```
flutterfire configure
```

Firebase のプロジェクトに登録されているプラットフォーム毎の、config 情報を自動生成してくれます。  
最高に便利ですが、環境分けには対応できないようです..

コマンドの参考
https://qiita.com/Umigishi-Aoi/items/32a46255bb935417ab3d#flutterfire-cli-%E3%81%AE%E5%88%A9%E7%94%A8

### コマンド以外での firebase の config 情報を設定

今回は、コマンドで自動生成では融通が効かなかったので、web の場合のみ、環境毎に config 情報をそのまま渡すことにしました。

```dart
 await Firebase.initializeApp(
    options: //ここで設定する
```

`options`に以下のような感じで作成した`FirebaseOptions`を渡すイメージです。

```dart
  static FirebaseOptions get webConfig {
    const flavorName = String.fromEnvironment("FLAVOR");
    switch (flavorName) {
      case 'dev':
        return devWebConfig;
      case 'stg':
        return stgWebConfig;
      case 'prod':
        return prodWebConfig;
      default:
        return devWebConfig;
    }
  }
```

### dart-define で FLAVOR を渡す

上記の環境分け部分では、dart-define で環境変数を渡すようにしています。
開発環境の場合は、以下で`FlAVOR=dev`が取得できます。

```
flutter run -d chrome --dart-define=FlAVOR=dev
```

参考
https://zenn.dev/riscait/articles/separating-environments-in-flutter

## web/index.html の環境切り替え

project/web/index.html ファイル内に、firebase の config 情報が入っています。
index.html 内の config 情報を切り替えなければなりません。  
コードでいうと以下のような感じです。

```html
<script type="module">
  // Import the functions you need from the SDKs you need
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.8.4/firebase-app.js";
  import { getAnalytics } from "https://www.gstatic.com/firebasejs/9.8.4/firebase-analytics.js";
  // TODO: Add SDKs for Firebase products that you want to use
  // https://firebase.google.com/docs/web/setup#available-libraries

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "...",
    authDomain: "...",
    projectId: "...",
    storageBucket: "...",
    messagingSenderId: "...",
    appId: "...",
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
</script>
```

ここ結構迷いました。。。

### index.html ファイル内で環境を識別する

[こちら](https://stackoverflow.com/a/65647968)を参考に index.html に dart-define で定義した環境変数を渡してみました。
「うまくいくぞ！！」  
しかし、ios や android をビルドするときに、以下のような WEB 用のパッケージを import しているとエラーになってしまうようです。

```dart
import 'dart:js' as js;
import 'dart:html' as html;
```

管理ツールの build 時だけ、コメントアウトを外すみたいな運用は少し面倒かなと。  
コマンドだけで色々できるのが理想だったので、この方法はなしです。

### シンボリックリンクによる環境切り替え

```
root
  ┣ web
     ┣ default
        ┣ index.html
     ┣ dev
        ┣ index.html
     ┣ stg
        ┣ index.html
     ┣ prod
        ┣ index.html
   ┣ index.html

```

シンボリックリンクを作成し、`root/web/index.html`の中身は、

- `default`
- `dev`
- `stg`
- `prod`
  の中の`index.html`を参照できるようにします。

シンボリックリンクは git でコミットできるのも便利だと思います。

#### シンボリックリンクの作成

`dev`環境の`index.html`を参照したい場合は、`root/web`ディレクトリにて、以下のコマンドで OK です。

```bash
ln -s dev/index.html index.html
```

すでにシンボリックリンクが作成されている場合は、
上書きオプションを使ってください。

```bash
ln -fs dev/index.html index.html
```

参考
https://qiita.com/takeoverjp/items/bb1576e90a8a495db4b3

#### シンボリックリンクの確認

以下のコマンドで確認できます。

```bash
ll
```

`index.html`の参照先が表示されます。

```
index.html -> dev/index.html
```

参考
https://qiita.com/ngyuki/items/621830bc53aaf2923f73
https://s-port.shinwart.com/tech-column/kawatama03/

## 環境毎に Firebase Hosting へのデプロイ

環境が分かれていない場合は、

```bash
flutter build web
firebase deploy --only hosting
```

で終了なのですが、今回は`dev`,`stg`,`prod`の３つあるので、環境を指定してデプロイしないといけません。

### デプロイ時の環境指定

プロジェクトのルートに`.firebaserc`というファイルが作成されています。  
このファイルを編集することで、デプロイ先の Firebase の projectId を指定できるようになります。

```json:.firebaserc
{
  "projects": {
    "dev": "project-id-dev",
    "stg": "project-id-stg",
    "prod": "roject-id-prod",
    "default": "project-id-dev"
  }
}
```

### デプロイ先を dev 環境のプロジェクトに向ける

dev へ向ける

```bash
firebase use dev
```

stg へ向ける

```bash
firebase use stg
```

prod へ向ける

```bash
firebase use prod
```

参考
https://stackoverflow.com/questions/66560305/flutter-firebase-setting-different-deployment-targets-for-ios-android-and-w/66560306#66560306

### index.html の参照先を確認

↑ で紹介したシンボリックリンクの切り替えで`web/index.html`の参照先もデプロイしたい環境に向ける必要があります。

### 最終的なコマンド例

dev 環境へ deploy したい場合の例を以下に示します。

```bash
firebase use dev
ln -s dev/index.html index.html
flutter build web
firebase deploy --only hosting
```

というような流れになります。

## デプロイ時の確認事項

`flutter build web`後に生成された`root/build/web/index.html`の中身を確認してください。  
Firebase の config 情報が含まれていると思います。　　
自分の想定したデプロイ先と`projectId`が一致しているかを確認してみてください。

こちらの確認後に、`firebase deploy --only hosting`でデプロイするのが良いかと思います。

## 最後に

いかがでしたか？  
環境分けをした管理ツールを１から作るのは初めてだったので、色々と方針に悩みました。  
Flutter プロジェクトを管理ツール用に別で作ったほうがよかったのかな..とも思いつつ。  
この記事の使えるは参考にしていただけたらなと思います。  
何かご意見やご指摘があれば、ぜひお願いします！

Twitter では Flutter に関する情報を発信しています！
https://twitter.com/marksaito4
