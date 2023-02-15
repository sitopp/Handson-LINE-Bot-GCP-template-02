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
- 使い慣れたエディタ (VSCodeなど)
- ターミナルクライアント (Mac OSのターミナルなど)

### 使用する言語
- Node.js
- Python

### 構成図

<img width="799" alt="image" src="https://user-images.githubusercontent.com/1670181/219108116-1a42c37e-56da-49d5-8a0c-27007140d4bd.png">


### 注意事項
若干、Google Cloudの課金が発生する可能性があります。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定

[LINE Developers](https://developers.line.me/ja/services/messaging-api/) にアクセスし「今すぐはじめよう」のボタンをクリック

![image](https://user-images.githubusercontent.com/1670181/219110053-d1af5ce9-e85e-4b5c-9765-2625a3a6bccc.png)

![image](https://user-images.githubusercontent.com/1670181/219110127-2f847adc-8528-4e48-b0f2-27a247aa5593.png)

![image](https://user-images.githubusercontent.com/1670181/219110261-5538a061-af74-46f3-8db9-7b1efa550f63.png)

![image](https://user-images.githubusercontent.com/1670181/219110341-8f3eca35-114f-4a8d-8893-6ac7e5f5b764.png)

![image](https://user-images.githubusercontent.com/1670181/219110380-02344710-e761-4c5b-bed2-30d3e8e9aa3c.png)



![image](https://user-images.githubusercontent.com/1670181/219110416-75462d1d-4345-4360-9276-9656f0b9dc8e.png)

![image](https://user-images.githubusercontent.com/1670181/219110443-21b0e7ea-3332-453d-a823-c44ff83aed93.png)



![image](https://user-images.githubusercontent.com/1670181/219110479-c435a891-6560-4126-a9f9-2d95f46c734b.png)

### LINE ログインチャネルの作成

![image](https://user-images.githubusercontent.com/1670181/219110764-8d3680a1-20b7-41a0-bb0e-b85d68f95d5b.png)

![image](https://user-images.githubusercontent.com/1670181/219111018-cbc3db27-d1d6-4daf-8c84-975b086eecfa.png)

![image](https://user-images.githubusercontent.com/1670181/219111080-cd44d57b-f651-47dc-aab5-d3405ea93b08.png)

![image](https://user-images.githubusercontent.com/1670181/219111115-65fc2f5b-84fc-48d5-84e2-bd6a84bdc706.png)

![image](https://user-images.githubusercontent.com/1670181/219111151-63629bbe-2cb0-4975-a99b-204f592a2b58.png)

![image](https://user-images.githubusercontent.com/1670181/219111183-e6d0fe18-532c-4b9d-8b7f-b028e1c5c8f9.png)

![image](https://user-images.githubusercontent.com/1670181/219111227-08a959a3-3125-4bb8-9572-c19a94e10fba.png)

LIFF が作成できました。

後ほど使用するので、「LIFF ID」と「LIFF URL」をメモしましょう。

![image](https://user-images.githubusercontent.com/1670181/219111359-ddf585ae-43c6-4ced-9385-c222bc29764a.png)


### リッチメニューの作成

![image](https://user-images.githubusercontent.com/1670181/219112021-a837cb2a-83ac-4bd3-a4bf-6d49dbfd5ea8.png)

![image](https://user-images.githubusercontent.com/1670181/219112067-b4b94407-8716-48e5-9a21-37807641fba7.png)

![image](https://user-images.githubusercontent.com/1670181/219112113-59345e81-8cec-4424-8732-a52509f7ef69.png)

![image](https://user-images.githubusercontent.com/1670181/219112345-e16c80ee-76a3-45d0-933b-e73b0b887006.png)


## フロントエンドの構築

### ローカルで編集

ターミナルを開き、以下を実行

```
git clone git@github.com:sitopp/LINE-Digital-MembersCard-on-GCP.git 
cd LINE-Digital-MembersCard-on-GCP
```

- あとでコマンド実行するのでそのまま開いておく

### GCP プロジェクト作成

- https://console.cloud.google.com/ 

- ヘッダー部分のプロジェクト選択肢 > 新しいプロジェクト 

- Handson-LINE-Bot-GCP-02 を作成

- 左上のケバブ> ナビゲーションメニュー > Cloud の概要 > ダッシュボード > プロジェクト ID を参照。後で使うのでメモっておく。


### Firebase プロジェクト作成

- https://console.firebase.google.com/?hl=ja

![image](https://user-images.githubusercontent.com/1670181/219113905-7b105ee5-4105-4beb-a3bd-0a4d3cace97b.png)

- プロジェクトを追加 > プロジェクト名入力欄をクリックして Handson-LINE-Bot-GCP-02 を選択

- 「自身の取引、ビジネス、仕事、または職業のみを目的として Firebase を利用することを正式に認めます。」にチェック > 続行

- 「Firebase の料金プランの確認 Blaze 従量制」 > プランを確認

### Firebase アプリの登録

- ウェブアプリのアイコンをクリック
![image](https://user-images.githubusercontent.com/1670181/219115227-9b10f12a-e0ec-4187-8507-25cac5236bce.png)

- ①アプリの登録

- アプリのニックネーム：liff
- FirebaseHostingにチェック
- 好きな名前.web.app

②Firebase SDK の追加


- 普段使っているエディターで、以下のファイルを開く
```
LINE-Digital-MembersCard-on-GCP > front > public > front > index.html
```

- 「<script> タグを使用する」を選択すると<script>タグが表示される
- index.htmlの<body>タグの最後に貼り付ける。 サンプルコードの場合、L.82〜L.100に上書きコピペ。

![image](https://user-images.githubusercontent.com/1670181/219083123-682b8bba-19bf-48f3-acc7-fbe55eabda7b.png)

※もしコピーし忘れた場合は、後で、SDKの設定 >. CDN でも閲覧できる。
    
<1---firebase.json　を編集し、Siteの行を追加する。--->


### Firebase ビルド セットアップ

cd LINE-Digital-MembersCard-on-GCP/front

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





