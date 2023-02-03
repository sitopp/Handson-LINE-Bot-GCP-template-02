# 1.概要

LIFFを使ったLINE会員証をつくり、Firebaseにホスティングします。


### 謝辞

このハンズオンは、LINE API EXPERTである捧さんのハンズオン勉強会「LIFF×AWSで会員証アプリを作ろう!」の内容を真似し、AWSをGoogleに置き換えたものです。<br>
https://zenn.dev/arahabica/books/d4373bd4401d6c/viewer/83e531

＞捧さん <br>
流用をご快諾いただき、ありがとうございました。


### 当日の持ち物

- LINEがインストール済みのスマホ
- ChromeがインストールされたPC（Chromeのバージョンは最新にしていただくとトラブルを回避しやすいです）

### 事前準備
- [GitHub アカウント](https://github.com/)
- LINEアカウント作成 (普段つかっているLINEアプリのアカウント)
- [LINE公式アカウント](https://developers.line.me/console/)
- [Firebase アカウント](https://firebase.google.com/?hl=ja) 
- [Google Cloud アカウント](https://cloud.google.com/free) 
- (推奨) [Google Chrome](https://www.google.com/intl/ja/chrome/gsem/download/) の最新版のインストール

### 使用するサービス・ツール
- GitHub
- LINE Messaging API / LIFF アプリ / LINE ログイン
- Google Cloud 
  - Firestore
- Firebase
  - Firebase hosting

### 使用する言語
- Nuxt.js

### 構成図


![kouseizu](https://user-images.githubusercontent.com/1670181/215822528-2a0ccb67-6afb-4eb3-8d45-148b3505852c.png)


### 注意事項
若干、Google Cloudの課金が発生する可能性があります。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定

## GCP側の設定

### GCS バケット作成

### Cloud Shell起動

### GCS バケット作成

### Node.jsのインストール

### Firebaseのプロジェクト作成

#### FirebaseのCLIツールをインストール

npm install -g firebase-tools
firebase --version
nodebrew use  v16.0.0

