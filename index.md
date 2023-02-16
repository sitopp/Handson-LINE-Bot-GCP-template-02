# 1.概要

LIFFを使ったLINE会員証をつくります。
フロントはFirebaseにホスティングし、バックエンドはコンテナ化してCloudRunにデプロイします。
DBは俺たちのFirestoreを使います。


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
- 若干、Google Cloudの課金が発生する可能性があります。
- 終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定

[LINE Developers](https://developers.line.me/ja/services/messaging-api/) にアクセスし「今すぐはじめよう」のボタンをクリック

![image](https://user-images.githubusercontent.com/1670181/219110261-5538a061-af74-46f3-8db9-7b1efa550f63.png)

![image](https://user-images.githubusercontent.com/1670181/219110053-d1af5ce9-e85e-4b5c-9765-2625a3a6bccc.png)

![image](https://user-images.githubusercontent.com/1670181/219228272-6620d1bc-9624-4b07-a0aa-2e063fe81220.png)

※河本さんのWebページの画像のスクショなのでクリックしても そんなに拡大しません。すいません(^^;)


![image](https://user-images.githubusercontent.com/1670181/219110341-8f3eca35-114f-4a8d-8893-6ac7e5f5b764.png)



- 「チャネル名」、「チャネル説明」で下記を入力します。
    - チャネル名 : デジタル会員証
    - チャネル説明 : デジタル会員証をGPS X LINEで実現

- 「大業種、小業種」の選択画面では、今回は「個人」を選択します。(リスト下の方にあります。)



![image](https://user-images.githubusercontent.com/1670181/219110416-75462d1d-4345-4360-9276-9656f0b9dc8e.png)

![image](https://user-images.githubusercontent.com/1670181/219110443-21b0e7ea-3332-453d-a823-c44ff83aed93.png)

※以下はスマホのLINEアプリで友達追加したところ

<img src="https://user-images.githubusercontent.com/1670181/219229370-979c9d5c-61e2-42d5-a11e-9139034ca86f.png" width=300>

![image](https://user-images.githubusercontent.com/1670181/219110479-c435a891-6560-4126-a9f9-2d95f46c734b.png)

### LINE ログインチャネルの作成

- 次に、デジタル会員証を表示できるよう、LINE ログインチャネルと LIFF を作成していきましょう。
- LINE Front-end Framework (LIFF) は、LINE が提供するウェブアプリのプラットフォームです。LINE ミニアプリにも関連する技術でもあります。詳細は下記の URL 先をご覧ください。
    - https://developers.line.biz/ja/docs/liff/overview/
    - https://www.youtube.com/watch?v=QD_M52ATbb8


- LINE Developerの「DEV」プロバイダーのチャネル一覧で、「新規チャネル作成」をクリックします。

<img src="https://user-images.githubusercontent.com/1670181/219230853-49098fd5-518f-4ef3-9cd8-c7bb584a9dce.png" width=500>


![image](https://user-images.githubusercontent.com/1670181/219230549-092c6a85-64b5-41d0-8787-fee7f81e8d47.png)

![image](https://user-images.githubusercontent.com/1670181/219111018-cbc3db27-d1d6-4daf-8c84-975b086eecfa.png)

- 「チャネル名」、「チャネル説明」で下記を入力します。
    - チャネル名 : デジタル会員証
    - チャネル説明 : デジタル会員証をGPS X LINEで実現
- 「アプリタイプ」は「ウェブアプリ」にチェックを入れます。


![image](https://user-images.githubusercontent.com/1670181/219111115-65fc2f5b-84fc-48d5-84e2-bd6a84bdc706.png)

![image](https://user-images.githubusercontent.com/1670181/219111151-63629bbe-2cb0-4975-a99b-204f592a2b58.png)

![image](https://user-images.githubusercontent.com/1670181/219111183-e6d0fe18-532c-4b9d-8b7f-b028e1c5c8f9.png)


- エンドポイントURLに入力するものがまだ無いので、「https://example.com」と入力しておきましょう

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
$ cd (任意のディレクトリに移動)
$ mkdir handson
$ cd handson
$ git clone https://github.com/sitopp/LINE-Digital-MembersCard-on-GCP.git
```

- あとでコマンド実行するのでそのまま開いておく

### GCP プロジェクト作成

- https://console.cloud.google.com/ 
- ヘッダー部分のプロジェクト選択肢 > 新しいプロジェクト 
- プロジェクト名： Handson-LINE-Bot-GCP-02 を作成
- 左上のケバブ じゃなくて ナビゲーションメニュー > Cloud の概要 > ダッシュボード > プロジェクトを選択 > プロジェクト ID を参照し、後で使うのでメモっておく。


### Firebase プロジェクト作成

- https://console.firebase.google.com/?hl=ja

<img src="https://user-images.githubusercontent.com/1670181/219256747-7f6dd3ae-d364-4273-892e-3c6c4d5f8a0b.png" width=500>

- プロジェクトを追加 > プロジェクト名入力欄をクリックして 「Handson-LINE-Bot-GCP-02」 を選択
- 「自身の取引、ビジネス、仕事、または職業のみを目的として Firebase を利用することを正式に認めます。」にチェック > 続行
- 「Firebase の料金プランの確認 Blaze 従量制」 > プランを確認

### Firebase アプリの登録

- Firebaseのグランドメニューから 「Handson-LINE-Bot-GCP-02」 を選択
- 「 アプリを追加 」をクリック
- ウェブアプリのアイコンをクリック

![image](https://user-images.githubusercontent.com/1670181/219115227-9b10f12a-e0ec-4187-8507-25cac5236bce.png)

#### ① アプリの登録

- アプリのニックネーム：liff
- 「このアプリの Firebase Hosting も設定します」 にチェック
- 「liff-dev-(自分のあだ名など).web.app」 記入
    - 世界で一意になる必要があります。重複してるとエラーが出て先へすすみません。
- アプリを登録

#### ② Firebase SDK の追加

- 「<script> タグを使用する」を選択
    - ドバッと<script> タグが表示される

- 普段使っているエディターで、以下のファイルを開く
    
    ```
    先ほどgit cloneした、
    handson/LINE-Digital-MembersCard-on-GCP/front/public/front/index.html
    ```
    - <script> タグを、index.htmlの<body>タグの最後に貼り付ける。 つまりサンプルコードの L.82〜L.100に上書きコピペする。

   ![image](https://user-images.githubusercontent.com/1670181/219258968-8997cf13-7ee0-42c9-a4f0-8862fec9768d.png)
    
    ※ もし<script>タグをコピーし忘れた場合は、後で SDKの設定 > CDN でも閲覧できる。
    

#### ③ Firebase CLI のインストール

- ターミナルに戻り、以下のコマンドを実行する

    ```
    $ pwd ←現在位置を確認。~/handsonディレクトリにいればOK。居なければ cd ~/handson などで移動
    $ cd LINE-Digital-MembersCard-on-GCP/line-api-use-case-MembersCard/front
    $ npm install
    $ npm install firebase
    $ npm install -g firebase-tools
    $ npm install firebase-admin
    ```
    
#### ④ Firebase Hosting へのデプロイ

- ターミナルから以下を実行（ガイダンスより、少しコマンドを変えています）

    ```
    $ firebase login:ci --no-localhost
    ```
    
    ![image](https://user-images.githubusercontent.com/1670181/219260071-0e2af9c3-7777-4ae7-abfa-1eefb538c35a.png)
    URLが表示されるので、コピーしてブラウザで開く。
    - Googleアカウントで認証する
    - 質問が表示されるので答えていく。
        - 「Yes, I just ran this command」クリック
        - 表示されたセッションIDと、ターミナルに表示されたものが同じことを確認してから、「Yes, This is my session ID」クリック
        - authorization codeが表示されるので「copy」をクリック
    - ターミナルに戻り、authorization codeを貼り付けてエンター
    
        
    ```
    $ firebase init
    ```
    ※プロジェクトが不明というエラーが出たらプロジェクト名を明示的に指定すると通る
    
    - 上下カーソルで以下の行にあわせてアクティブにする
        - 「Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys」　
    - スペースを押下することにより「○」→「◉」になる。（下図参照）
    
    <img width="889" alt="image" src="https://user-images.githubusercontent.com/1670181/219260733-9fafa3e7-b585-479d-a9b9-770022864f41.png">
    
    - エンターを押すと次へ進む
    - Please select an option: 上下カーソルで「Use an existing project」を選んでエンター
    - Select a default Firebase project for this directory ：今作ったFirebaseプロジェクトを指定してエンター    
    - What do you want to use as your public directory? public から始まる質問は全てデフォルト値を使うので、空エンター

    - 「firebase.json でサイトを指定する」の 「site」行の右横のコピーアイコンをクリックする
    ![image](https://user-images.githubusercontent.com/1670181/219261682-0e859620-bee4-48c4-bec4-256b035c0b44.png)

    - エディタを開く
    
    ```
    /front/firebase.json
    ```
    
    を編集し、「Site」行をペーストする
    ![image](https://user-images.githubusercontent.com/1670181/219261993-9f01f04c-4d9f-40dc-a953-a4e2261d1ebf.png)

    - 「準備ができたらウェブアプリをデプロイ」の下に表示されたデプロイコマンド「firebase deploy --only hosting:xxxxx」をコピー
    ![image](https://user-images.githubusercontent.com/1670181/219262086-49545f0e-bd10-4f02-9b51-e91fe4b22423.png)
    
    コンソールを開く
    
    ```
    $ firebase deploy --only hosting:xxxxx
    ```
    
        - さいあく、firebase deploy でもOK
        - もしエラーが出たら、npm install をやり直す

    
    - デプロイが成功すると、 Hosting URLが表示される
    ![image](https://user-images.githubusercontent.com/1670181/219263064-7b2102fe-8eb0-4a2d-9737-1257806625f7.png)
    - ブラウザに貼り付けて実行すると、以下のメッセージが表示される。
    ![image](https://user-images.githubusercontent.com/1670181/219082212-0a64ec6c-40c2-42d7-9f0f-2871767795d2.png)

    - URLの末尾に/front/をつけて実行する。
        - 例）https://hogehoge.web.app/front/
    - LINE 400 Bad Requestが表示されるが、今の所はこれでOK！

    - Firebaseのダッシュボードのガイダンスを、「コンソールに進む」をクリックして閉じる。
    
### LINE Developers で LIFF のエンドポイントを編集

- LINE ログインチャネルの設定画面を開く
- LIFF のタブを開く
- 「エンドポイント URL」に、FirebaseへのHosting URL+/front/のURLを入力して更新。
![image](https://user-images.githubusercontent.com/1670181/219120341-26d55fe3-564e-451a-aa97-84f7906224f9.png)

### LINE Developers で LIFF の公開

![image](https://user-images.githubusercontent.com/1670181/219120395-c4fc27d8-8025-4ba5-aebb-ee29005bdd4e.png)

### Firestoreの作成

- Firebaseコンソール https://console.firebase.google.com/ を開く。  
- Handson-LINE-Bot-GCP-02 を選択
- 左ペインの「構築」>「Firestore Database」を選択
- 「+コレクションを開始」
- コレクションID：「MembersCardUserInfo」
- ドキュメントID：自動ID
- フィールドは入力せずに、保存
    
### Firebase Admin SDKのクレデンシャルを取得

    - Firebase コンソール画面左上の「プロジェクトの概要」の右横の歯車アイコンをクリックし、「プロジェクトの設定」を選択
    - 「サービスアカウント」を選択
    - Pythonを選び、「新しい秘密鍵を生成」のボタンをクリックし、jsonファイルをダウンロード    
    ![image](https://user-images.githubusercontent.com/1670181/219122453-b98b6124-59c1-4ec7-81cc-4f77d817be7c.png)
    
    
    参考） [こちら」(https://rayt-log.com/%E3%80%90firebase%E3%80%91python%E3%81%A7cloud-firestore%E3%81%AB%E5%80%A4%E3%82%92%E8%BF%BD%E5%8A%A0%E3%83%BB%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95%EF%BC%81/) の 「Firebase Admin SDKを取得する」


## バックエンドの構築

### CloudRun の有効化

- GCP のWebコンソールにアクセス https://console.cloud.google.com/  
- ナビゲーションバー > Cloud Runを開く
- 有効化する

### Python コードの書き換え

ローカルでエディタを使う。

先ほどGit Cloneしたファイルを開いて編集する。
    
```
/handson/LINE-Digital-MembersCard-on-GCP/line-api-use-case-MembersCard/backend/content/key.json 
    → Firestoreの設定で取得した「Firebase Admin SDKのクレデンシャル」のjsonファイルをエディタで開き、中身を全行コピペして、保存。
    ※ファイルの上書きだと、日付が古くてデプロイしても反映されない可能性あり
```


```
/handson/LINE-Digital-MembersCard-on-GCP/line-api-use-case-MembersCard/backend/main.py 
    
- 20〜21行目を、LINEの情報で上書きする。
    LIFF_CHANNEL_ID = 'xxxxxxxxxx' ← LIFF を発行した、LINEログインのチャネルIDで書き換え
    CHANNEL_ACCESS_TOKEN = 'xxxxxxxxxx' ← Messaging APIのチャネルアクセストークンで書き換え
```
    
### backendコードのデプロイ
   
ローカルのコンソールから実行する。

```
$ cd ~/handson/LINE-Digital-MembersCard-on-GCP/line-api-use-case-MembersCard/backend
$ gcloud run deploy
Source code location (/xxxxxx/xxx/x/x/backend): 空エンター
Service name (backend): 空エンター
Please enter numeric choice or text value (must exactly match list item):  3 （←[3] asia-northeast1）
Allow unauthenticated invocations to [backend] (y/N)? Y ←ここだけデフォと違うので注意！ なお聞かれない時もある
    
```
    
※ すいません。。 gcloud をインストールしてない方は「gcloud run deploy」の段階でエラーが出るので、[こちら](https://cloud.google.com/sdk/docs/install?hl=ja)を参照して、gcloudコマンドラインツールをインストールし、再度実行してください。    


    - 正常終了すると「Service URL」が発行されるので、ブラウザで実行
        - 例) https://backend-t6innaw72a-an.a.run.app
        - Service Unavailableと表示されるがOK。
        - このURLは後で使うのでメモっておく。

    - GCPのCloud Runのダッシュボードを開き、一覧の中に作成したアプリが表示されていることを確認
        - https://console.cloud.google.com/run?hl=ja
        - backend > セキュリティ > 「未認証の呼び出しを許可」を選択していることを確認。もし違ったら変更する。
    

    
## フロントエンドのコード書き換え、デプロイ

- エディターでファイルを開いて編集する。
    - ~/handson/LINE-Digital-MembersCard-on-GCP/line-api-use-case-MembersCard/front/public/front/members_card.js 
    
    ```    
    const FUNCTION_URL = "https://xxxxxxxxxxxxxxxx"; ← cloud runのアプリのService URL 
    const liffId = "xxxxxxxxx-xxxxxxxxx"; ← LINEログインのLIFF ID
    ```

- ターミナルで、フロントのコードをFirebaseにデプロイ

    ```
    $ cd ~/handson/LINE-Digital-MembersCard-on-GCP/front
    $ firebase deploy
    ```

## LINEアプリで実行
    
最初はこの状態

<img src="https://user-images.githubusercontent.com/1670181/219277107-aa584601-a5d4-462e-86d3-57e538de92f4.PNG" height=500>
    
 「デジタル会員証」を押下するとLINEログインが走り、ポイント画面が表示される。（バーコードとポイントも表示されるハズだがまだ出ず。。）

<img src="https://user-images.githubusercontent.com/1670181/219277121-54945897-5d9d-455a-8048-d374fe23e512.PNG" height=500>
↓
<img src="https://user-images.githubusercontent.com/1670181/219277175-55f4ee16-5fd1-4612-9d5b-4f31b5e39831.PNG" height=500>
↓
<img src="https://user-images.githubusercontent.com/1670181/219277196-3ce5b9ac-66f1-4bb4-aa05-d3036bc5de17.PNG" height=500>

10秒後にプッシュが来て、購入したフローが再生され、会員証を閉じるとレシートが表示される。
    
<img src="https://user-images.githubusercontent.com/1670181/219277244-7766e2de-182a-4db5-b3a0-c066d64b121c.PNG" height=500>

    

    トラブルシュート：
    - 「Example Domain」というエラーが出た場合、エンドポイントURL が 「https://example.com」のままなんでLINEのLIFFの編集画面で書き換える。
    - 400 Bad Requertの場合、LINEログインが「開発中」のままの可能性あり。公開すべし
    - 10秒以上経ってもプッシュが来ない場合、　key.jsonを書き換えていないかも。ダウンロードしたクレデンシャルで上書きすべし
    あるいはmain.pyの書き換えができてないかも？




