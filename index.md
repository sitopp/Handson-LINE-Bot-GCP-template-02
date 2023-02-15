# 1.概要

LIFFを使ったLINE会員証をつくり、Firebaseにホスティングします。


### 謝辞

- このハンズオンは、河本さん作成の [LINE と AWS Lambda function URLs を活用してデジタル会員証を実現](https://aws.amazon.com/jp/builders-flash/202208/line-digital-membership-card/?awsf.filter-name=*all)の内容を、GCPのサービス群を使う内容に焼き直したものです。
- 捧さんのハンズオン勉強会[「LIFF×AWSで会員証アプリを作ろう!」](https://zenn.dev/arahabica/books/d4373bd4401d6c/viewer/83e531)の内容も参考にさせていただきました。
- AWSからGCPへのコードの焼き直しについては、ChatGPTにお世話になりました。

### 当日の持ち物

- スマホ
- LINE
- PC

### 事前準備
- [GitHub アカウント](https://github.com/)
- [LINE公式アカウント](https://developers.line.me/console/)
- [Firebase アカウント](https://firebase.google.com/?hl=ja) 
- [Google Cloud アカウント](https://cloud.google.com/free) 
- [Google Chrome](https://www.google.com/intl/ja/chrome/gsem/download/) の最新版のインストール (推奨) 

### 使用するサービス・ツール
- GitHub
- LINE ( Messaging API / LIFF )
- Google Cloud ( Cloud Run / Cloud Shell )
- Firebase ( Firebase Hosting  / Firestore )

### 使用する言語

- Node.js
- Python

### 構成図

![kouseizu](https://user-images.githubusercontent.com/1670181/215822528-2a0ccb67-6afb-4eb3-8d45-148b3505852c.png)


### 注意事項
若干、Google Cloudの課金が発生する可能性があります。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定

[LINE Developers](https://developers.line.me/ja/services/messaging-api/) にアクセス

「今すぐはじめよう」のボタンをクリック

LINE BUSINESS ID画面が開いたら、「LINE アカウントでログイン」を選択し、いつも使っているLINEアカウントでログイン。
パスワードを忘れた場合は、QRコードログインが便利。
![image](https://user-images.githubusercontent.com/1670181/218750217-cd6a4366-e679-4368-acbb-8fb070471790.png)

### プロバイダー選択

#### プロバイダーを初めて作る場合

新規プロバイダー作成」を選択しプロバイダー名を入力します。
注 : LINE という文字は含められません。

![image](https://user-images.githubusercontent.com/1670181/218750409-cd11f358-a34b-436d-8054-5737eb351cd6.png)

#### 既にプロバイダーを作ってある場合

利用するプロバイダーを選択します。

![image](https://user-images.githubusercontent.com/1670181/218750552-60a393bb-dbdf-4e32-ac2a-00726948f192.png)




### Messaging API チャネルの作成

以下をメモっておく
CHANNEL_ACCESS_TOKEN 


### LINE ログインチャネルの作成

### LIFFの作成

以下をメモっておく
LIFF_CHANNEL_ID
LIFF ID


### リッチメニューの作成


## フロントエンドの構築

### ローカルで編集

VSCodeなど普段


git clone git@github.com:sitopp/LINE-Digital-MembersCard-on-GCP.git ★Todo

cd LINE-Digital-MembersCard-on-GCP

LINE関連の書き換え ★todo

そのまま開いておく


### Firebase プロジェクト作成


https://console.firebase.google.com/?hl=ja
スクショ★

プロジェクトを作成 > プロジェクト名入力欄をクリックして Handson-LINE-Bot-GCP-02 を選択
「自身の取引、ビジネス、仕事、または職業のみを目的として Firebase を利用することを正式に認めます。」にチェック > 続行

「Firebase の料金プランの確認 Blaze 従量制」 > プランを確認

### Firebase アプリの登録

上に続き、

アプリ名：liff
チュートリアルの通りにやっていく
※SDKの構成はスルー

### コード書き換え

git clone git@github.com:sitopp/LINE-Digital-MembersCard-on-GCP.git

エディターでFirebase関連のコード書き換えする。

index.html
![image](https://user-images.githubusercontent.com/1670181/219083123-682b8bba-19bf-48f3-acc7-fbe55eabda7b.png)

SDKの設定 >. CDN > <scriipt>〜から始まる部分をコピーして、<body>タグの最後に貼り付ける

<1---firebase.json　を編集し、Siteの行を追加する。--->



### Firebase ビルド セットアップ

cd LINE-Digital-MembersCard-on-GCP
cd front

（以下、容量不足のエラーが出たら、ハンズオン1回目のファイルを削除するなど不要ファイルを削除するか、ローカルで実行）

```
npm install
npm install firebase
npm install -g firebase-tools
npm install firebase-admin
firebase login:ci --no-localhost
```

- Yes, I just ran this command
- Yes, This is my session ID
- 表示されたトークンをコピーしてCloud Shellに貼り付け

    
```
firebase init
```

- 上下カーソルで 「Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys」　をアクティブにし、スペースを押下すると選択状態になるので、エンター
- Please select an option: 上下カーソルで「Use an existing project」を選んでエンター
- Select a default Firebase project for this directory ：今作ったFirebaseプロジェクトを指定してエンター    
- What do you want to use as your public directory? public から始まる質問は全てデフォルト値を使うので、空エンター

```
firebase deploy
もしエラーが出たら、npm install
```

表示された Hosting URLをブラウザに貼り付けて実行する

例）https://handson-line-bot-gcp-02-718.web.app/
以下のメッセージが表示される。
![image](https://user-images.githubusercontent.com/1670181/219082212-0a64ec6c-40c2-42d7-9f0f-2871767795d2.png)

    
URLの末尾に/front/をつけて実行する。
https://handson-line-bot-gcp-02-718.web.app/front/
LINE 400 Bad Requestが表示されるが、今の所はこれでOK。



### LINE Developers で LIFF のエンドポイントを編集

FirebaseのURLを記入する

LINEログインを「公開中」にする


### Firestoreの作成

MembersCardUserInfo テーブル
https://rayt-log.com/%E3%80%90firebase%E3%80%91python%E3%81%A7cloud-firestore%E3%81%AB%E5%80%A4%E3%82%92%E8%BF%BD%E5%8A%A0%E3%83%BB%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95%EF%BC%81/ のやり方でJsonを払出して、Jsonファイルをダウンロードしておく


## バックエンドの構築

### GCPプロジェクト作成

ヘッダーのプロジェクト選択肢 > 新しいプロジェクト > 

Handson-LINE-Bot-GCP-02

ナビゲーションメニュー > Cloud の概要 > ダッシュボード > プロジェクト ID を参照、後で使う

### CloudRun の有効化

コンソールでやる


### Cloud Shell起動

gcloud config set project プロジェクトID


gcloud init
1 -> 自分のメールアドレス設定 -> handon-line-bot-gcp-02〜の番号を入力。多分1

Do you want to configure a default Compute Region and Zone? (Y/n)?  Y
「32 asia-northeast1-b」を使いたいので、32と入力


gcloud config set project PROJECT_ID
PROJECT_ID は、このクイックスタートで作成したプロジェクトの名前に置き換えます。


Cloud Shellに backend/ 以下のコード一式をアップロードする


backend/
    content/ フォルダを作り、FirestoreのJsonをアップロードして、名前を key.jsonに変更する


Backend/app.pyを書き換える

Cloud Shell コンソールから以下を実行

```
LIFF_CHANNEL_ID = '(LIFFを作ったLINEログインのチャネルID'
CHANNEL_ACCESS_TOKEN = '(Messaging APIのチャネルアクセストークン)'
```

gcloud run deploy
Source code location (/home/sito989/backend): 空エンター
Service name (backend): 空エンター
Please enter numeric choice or text value (must exactly match list item):  3
（[3] asia-northeast1）
Allow unauthenticated invocations to [backend] (y/N)? Y ここだけデフォと違う★

URLが発行されたらブラウザで実行してみる
https://backend-t6innaw72a-an.a.run.app
Service Unavailable
と表示される。

Cloud Runのダッシュボードを開き、一覧の中に作成したアプリが表示されていることを確認
https://console.cloud.google.com/run?hl=ja


## フロントエンドのコード書き換え、デプロイ

エディターで、
Firebase/public/front/members_card.js 
「FUNCTION_URL」と「liffId」に下記の値を入力

FUNCTION_URL: cloud runのアプリのHosting URL 
liffid: さっき作ったLINEログインのLIFF ID


ターミナルで、
cd ~/firebase
firebase deploy



## LINEアプリから実行

画面が表示されたらOK!





