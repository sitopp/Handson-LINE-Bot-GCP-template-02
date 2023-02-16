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

![image](https://user-images.githubusercontent.com/1670181/219129474-fcb80ca0-666f-4d4c-9ba5-9d26da2c1374.png)


### 注意事項
若干、Google Cloudの課金が発生する可能性があります。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定

[LINE Developers](https://developers.line.me/ja/services/messaging-api/) にアクセスし「今すぐはじめよう」のボタンをクリック

![image](https://user-images.githubusercontent.com/1670181/219110053-d1af5ce9-e85e-4b5c-9765-2625a3a6bccc.png)

![image](https://user-images.githubusercontent.com/1670181/219228272-6620d1bc-9624-4b07-a0aa-2e063fe81220.png)
※河本さんのWebページの画像のスクショなのでクリックしても拡大しません。すいません(^^;)

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
cd 
mkdir handson
cd handson
git clone git@github.com:sitopp/LINE-Digital-MembersCard-on-GCP.git 
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

- ③ Firebase CLI のインストール

- ターミナルに戻る。

```
cd LINE-Digital-MembersCard-on-GCP/front
npm install
npm install firebase
npm install -g firebase-tools
npm install firebase-admin
```
    
- ④Firebase Hosting へのデプロイ

```
firebase login:ci --no-localhost
```
URLが表示されるので、コピーしてブラウザで開く。質問が表示されるので答えていく。
- Yes, I just ran this command
- Yes, This is my session ID
- 表示されたトークンをコピー。
- ターミナルに戻り、トークンを貼り付けてエンター
    
```
firebase init
```

- 上下カーソルで 「Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys」　をアクティブにし、スペースを押下すると選択状態になるので、エンター
- Please select an option: 上下カーソルで「Use an existing project」を選んでエンター
- Select a default Firebase project for this directory ：今作ったFirebaseプロジェクトを指定してエンター    
- What do you want to use as your public directory? public から始まる質問は全てデフォルト値を使うので、空エンター

```
firebase.jsonに「Site」行をコピーする
![image](https://user-images.githubusercontent.com/1670181/219119562-8dc1ce0e-ac81-4d84-8ac3-a3bac9b24283.png)

- firebase deploy
- もしエラーが出たら、npm install
```

デプロイが成功すると、 Hosting URLが表示されるので、ブラウザに貼り付けて実行する

例）https://handson-line-bot-gcp-02-718.web.app/
以下のメッセージが表示される。
![image](https://user-images.githubusercontent.com/1670181/219082212-0a64ec6c-40c2-42d7-9f0f-2871767795d2.png)

URLの末尾に/front/をつけて実行する。
https://handson-line-bot-gcp-02-718.web.app/front/
LINE 400 Bad Requestが表示されるが、今の所はこれでOK。



### LINE Developers で LIFF のエンドポイントを編集

LINE 側セットアップで行った、LINE ログインチャネルの LIFF に戻り、LIFF の「エンドポイント URL」にFirebaseへのHosting URL+/front/のURLを入力し、更新します。
    
![image](https://user-images.githubusercontent.com/1670181/219120341-26d55fe3-564e-451a-aa97-84f7906224f9.png)

![image](https://user-images.githubusercontent.com/1670181/219120395-c4fc27d8-8025-4ba5-aebb-ee29005bdd4e.png)

### Firestoreの作成

Firebaseコンソール https://console.firebase.google.com/
    -  Handson-LINE-Bot-GCP-02 を選択
    - 左ペインの「構築」>「Firestore Database」を選択
    - 「+コレクションを開始」
    - コレクションID：「MembersCardUserInfo」
    - ドキュメントID：自動ID
    - フィールドは入力せずに、保存
    
### Firebase Admin SDKのクレデンシャルを取得

- Firebase コンソール画面左上の「プロジェクトの概要」の右横の歯車アイコンをクリックし、「プロジェクトの設定」を選択
    - 全般 / Cloud Messaging...の並びにある、「サービスアカウント」を選択
    - そして下の方にある新しい秘密鍵を生成のボタンをクリックし、jsonファイルをダウンロードします。
    
    ![image](https://user-images.githubusercontent.com/1670181/219122453-b98b6124-59c1-4ec7-81cc-4f77d817be7c.png)
    
    
    参考）https://rayt-log.com/%E3%80%90firebase%E3%80%91python%E3%81%A7cloud-firestore%E3%81%AB%E5%80%A4%E3%82%92%E8%BF%BD%E5%8A%A0%E3%83%BB%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95%EF%BC%81/ の 「Firebase Admin SDKを取得する」


## バックエンドの構築

### CloudRun の有効化

GCP のWebコンソールで開き、有効化

### Python コードの書き換え

ローカルでエディタを使う。
先ほどGit Cloneしたファイルを開いて編集する。
    
- backend/content/key.json 
    上で取得した「Firebase Admin SDKのクレデンシャル」をエディタで開き、Ctrl+Aで全文コピーして、backend/content/key.json に上書きペースト

- backend/main.py 
    20〜21行目を、LINEの情報で上書き

```
LIFF_CHANNEL_ID = 'xxxxxxxxxx' ← LIFF チャネルIDで書き換え
CHANNEL_ACCESS_TOKEN = 'xxxxxxxxxx' ← Messaging APIのチャネルアクセストークンで書き換え
```

    

    ローカルでコンソールを使う。


```
gcloud run deploy
Source code location (/home/sito989/backend): 空エンター
Service name (backend): 空エンター
Please enter numeric choice or text value (must exactly match list item):  3
（[3] asia-northeast1）
Allow unauthenticated invocations to [backend] (y/N)? Y ここだけデフォと違う★
```

    
    gcloud をインストールしてない方は「gcloud run deploy」の段階でエラーが出るので、[こちら](https://cloud.google.com/sdk/docs/install?hl=ja)を参照して、gcloudコマンドラインツールをインストールし、再度実行。    
    
    
- URLが発行されたらブラウザで実行してみる
- 例) https://backend-t6innaw72a-an.a.run.app
- Service Unavailableと表示されるがOK。
    - このURLは後で使うのでメモっておく。

- GCPのCloud Runのダッシュボードを開き、一覧の中に作成したアプリが表示されていることを確認
https://console.cloud.google.com/run?hl=ja


## フロントエンドのコード書き換え、デプロイ

- エディターでファイルを開いて編集する。

  -  Firebase/public/front/members_card.js 
    
    ```    
    const FUNCTION_URL = "https://xxxxxxxxxxxxxxxx"; ← cloud runのアプリのHosting URL 
    const liffId = "xxxxxxxxx-xxxxxxxxx"; ← LINEログインのLIFF ID
    ```

    - ターミナルで、フロントのNode.jsコードをFirebaseにデプロイしなおす。

    ```
    cd ~/handson/LINE-Digital-MembersCard-on-GCP/front
    firebase deploy
    ```

## LINEアプリで実行

画面が表示されたらOK!




