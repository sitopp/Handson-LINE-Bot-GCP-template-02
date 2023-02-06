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

### GCPプロジェクト作成

ヘッダーのプロジェクト選択肢 > 新しいプロジェクト > 

Handson-LINE-Bot-GCP-02

ナビゲーションメニュー > Cloud の概要 > ダッシュボード > プロジェクト ID をコピー

### Cloud Shell起動

gcloud config set project プロジェクトID


#### FirebaseのCLIツールをインストール

git clone ~

npm i && npm audit fix --force

<!-- npm install @google-cloud/firestore
npm install @google-cloud/logging -->


### リージョンの設定
gcloud config set compute/region us-central1

### AppEngine の有効化
gcloud app create --region=us-central
y/N => y

gcloud app create --region=us-central

### Firestore データベースをセットアップ

Firebaseデータベースの作成
gcloud firestore databases create --region=us-central
y/N => y
gcloud firestore databases create --region=us-central


### Firebase プロジェクト作成

https://console.firebase.google.com/?hl=ja

プロジェクトを作成 > プロジェクト名入力欄をクリックして Handson-LINE-Bot-GCP-02 を選択
「自身の取引、ビジネス、仕事、または職業のみを目的として Firebase を利用することを正式に認めます。」にチェック > 続行

「Firebase の料金プランの確認 Blaze 従量制」 > プランを確認

### Firebase に対して認証を行いデプロイする

firebase login --no-localhost

空エンター > URLをブラウザのURL欄に貼り付けて実行 > Googleアカウントを選び

### GCS バケット作成
### GCS バケット作成

### Node.jsのインストール





