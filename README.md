# たびルート　無料で公開する手順

このフォルダの中身をそのままアップロードすると、たびルートをインターネットに公開できます。
**費用は0円です。** クレジットカードの登録も不要です。

| 使うもの | 役割 | 料金 |
|---|---|---|
| GitHub Pages | サイトを公開する場所 | 無料 |
| Firebase（Sparkプラン） | ログイン・友達との共有 | 無料（カード登録なし） |
| 地図・場所・写真・路線のデータ | 国土地理院、OpenStreetMap、Wikipedia など | 無料・登録不要 |

## フォルダの中身

| ファイル | 内容 |
|---|---|
| `index.html` | アプリ本体 |
| `privacy.html` | プライバシーポリシー（運営者名・連絡先を書き換える） |
| `og.png` | LINEなどで送ったときに表示されるプレビュー画像 |
| `manifest.webmanifest`・`icons/` | スマホの「ホーム画面に追加」用のアイコン |
| `firestore.rules` | Firebaseのデータを守るルール |
| `firebase.json` | Firebase Hostingで公開する場合の設定（使わなければ無視してOK） |
| `.nojekyll` | GitHub Pages用の設定ファイル（そのままアップロード） |
| `sitemap.xml` | 検索エンジンに登録するためのページ一覧 |

---

## 手順1：まずは共有なしで公開する（約10分）

### 1-1. GitHubのアカウントを作る
https://github.com/signup から無料で作成します。ユーザー名はサイトのURLに入ります（例：`tabi-taro`）。

### 1-2. 公開用の置き場所（リポジトリ）を作る
1. GitHubにログインし、右上の「＋」→「New repository」。
2. Repository name に `tabiroute` と入力。
3. **Public** を選び、「Create repository」。

### 1-3. ファイルをアップロードする
1. 作成した画面の「uploading an existing file」をクリック。
2. このフォルダの**中身すべて**（`index.html`、`icons` フォルダなど）をドラッグ＆ドロップ。
   - `.nojekyll` は見えないファイルです。Macは Finder で `Command + Shift + .` を押すと表示されます。
   - アップロードできなくても動きます。
3. 下の「Commit changes」を押す。

### 1-4. 公開をオンにする
1. リポジトリの「Settings」→ 左の「Pages」。
2. 「Branch」を `main`、フォルダを `/ (root)` にして「Save」。
3. 1〜2分待つと、上に公開URLが出ます。
   `https://ユーザー名.github.io/tabiroute/`

これで、誰でも開いて使える状態になります（旅行はそれぞれの端末に保存）。

### 1-5. 公開URL（設定済み）
`index.html` と `sitemap.xml` には、公開URL `https://naru0116.github.io/tabiroute/` を書き込み済みです。LINEのプレビューや検索エンジンが、このURLを使います。
リポジトリ名などを変えてURLが変わったときは、両方のファイルの中にあるこのURLをすべて書き換えてください。

### 1-6. プライバシーポリシーを書き換える
`privacy.html` の【運営者名】【連絡先】と制定日を書き換えます。ログイン機能を使う場合は必須です。

---

## 手順2：友達との共有・ログインを使えるようにする（約15分）

### 2-1. Firebaseのプロジェクトを作る
1. https://console.firebase.google.com/ にGoogleアカウントでログイン。
2. 「プロジェクトを作成」→ 名前は `tabiroute` など →
   Googleアナリティクスは**オフ**で作成します。
3. 料金プランは最初から**Spark（無料）**です。アップグレードしないでください。

### 2-2. ログイン方法を有効にする
「構築」→「Authentication」→「始める」→「ログイン方法」で、次の3つを有効にします。
- **Google**（サポートメールを選んで保存）
- **メール / パスワード**
- **匿名**

続けて「設定」タブ →「承認済みドメイン」→「ドメインを追加」で、`ユーザー名.github.io` を追加します。

### 2-3. データベースを作る
1. 「構築」→「Firestore Database」→「データベースを作成」。
2. ロケーションは `asia-northeast1（東京）`、「本番環境モード」で作成。
3. 「ルール」タブを開き、中身をすべて消して、`firestore.rules` の内容を貼り付けて「公開」。

### 2-4. 設定値をアプリに書き込む
1. 歯車アイコン →「プロジェクトの設定」→ 下の「マイアプリ」で `</>`（ウェブ）を押す。
2. アプリ名を入れて登録すると `const firebaseConfig = { ... }` が表示されるので、`{` から `}` までをコピー。
3. GitHubで `index.html` を編集し、`const FIREBASE_CONFIG = null;` を探します（ブラウザの検索で `FIREBASE_CONFIG`）。
4. `null` の部分を、コピーした `{ ... }` に置き換えて保存します。

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza....",
  authDomain: "tabiroute-xxxx.firebaseapp.com",
  projectId: "tabiroute-xxxx",
  storageBucket: "tabiroute-xxxx.firebasestorage.app",
  messagingSenderId: "....",
  appId: "1:....:web:...."
};
```

この値は公開しても問題ありません。データは手順2-3のルールで守られます。

1〜2分後に公開URLを開き直すと、「ログイン」ボタンと「共有」ボタンが使えるようになっています。

---

## 無料のまま使うために

- **Firebaseは「Spark（無料）」のまま使う**：カードを登録していなければ、上限を超えても請求されません（その日の分が止まるだけです）。
  無料で使える量は、1日あたり読み取り5万回・書き込み2万回、保存容量1GBです。友達同士で使うには十分な量です。
- **地図・場所検索・路線・写真のデータ**：国土地理院、OpenStreetMap（Nominatim・Overpass）、OSRM、Wikipedia の無料サービスです。いずれも善意で提供されているため、個人や友達同士の利用を想定しています。
  - 大勢に宣伝して使ってもらう規模になったら、各サービスの利用条件を確認してください。
  - 画面下の出典表示は利用条件なので、消さないでください。
- **GitHub Pages**：公開リポジトリなら無料です。月100GBまで転送でき、個人の利用では超えません。

## 別の公開方法（どれも無料）

- **Cloudflare Pages**：https://pages.cloudflare.com/ →「Create a project」→「Direct Upload」で、このフォルダをドラッグ＆ドロップ。
  URLは `https://好きな名前.pages.dev/` になります。Firebaseの承認済みドメインに `好きな名前.pages.dev` を追加してください。
- **Firebase Hosting**：Firebaseに付いている公開機能です。パソコンでコマンド（Firebase CLI）を使います。
  このフォルダで `firebase init hosting`（既存の `firebase.json` を使う）→ `firebase deploy` を実行します。
  URLは `https://プロジェクトID.web.app/` です。

## 困ったとき

- **Googleでログインできない**：
  - 承認済みドメインに公開URLのドメインが入っているか確認してください。
  - LINEの中で開いている場合は、画面に出る「外部のブラウザで開く」を押してください。
- **共有ボタンが「設定が必要」と出る**：`FIREBASE_CONFIG` の書き換えが保存されているか確認してください。公開に1〜2分かかります。
- **写真が出ない**：写真の下の「再読み込み」を押してください。Wikipediaに写真がない場所は表示されません。
- **LINEのプレビューが出ない**：手順1-5の書き換えを確認してください。LINEはプレビューを一時保存するため、反映まで時間がかかることがあります。

---

## 検索で見つけてもらうために（SEO）

サイトには、検索エンジン向けの設定をあらかじめ入れてあります。

- 検索結果に出るタイトルと説明文（「旅行 予定表 自動作成」「合流場所」など、探す人が使う言葉を含めています）
- 検索エンジンが内容を理解するためのデータ（アプリの種類・無料であること・よくある質問）
- トップページの紹介文・使い方・よくある質問・都道府県別の人気スポット一覧（検索エンジンが読めるように、アプリとは別に文章で書いてあります）
- サイトマップ（`sitemap.xml`）

### Googleに登録する（無料・約10分）

公開しただけでは、Googleに見つけてもらえるまで何週間もかかることがあります。Google Search Console に登録すると早くなります。

1. https://search.google.com/search-console を開き、Googleアカウントでログイン。
2. 「プロパティを追加」→ 右側の **URLプレフィックス** に `https://naru0116.github.io/tabiroute/` を入力。
3. 所有権の確認で「**HTMLタグ**」を選び、表示された `<meta name="google-site-verification" content="……">` をコピー。
4. GitHubで `index.html` を編集し、`<link rel="canonical"` の行のすぐ上に貼り付けて「Commit changes」。
5. 1〜2分待ってから Search Console に戻り、「確認」を押す。
6. 左のメニュー「サイトマップ」で `sitemap.xml` と入力して「送信」。
7. 上の検索窓に `https://naru0116.github.io/tabiroute/` を入れ、「インデックス登録をリクエスト」を押す。

Bing にも出したい場合は、https://www.bing.com/webmasters で「Google Search Console からインポート」を選ぶだけで登録できます。

### 上位に出るために続けること

- **知ってもらう**：SNS（X・Instagram・LINE VOOM）やブログ、noteで紹介し、リンクを貼ってもらう。ほかのサイトからのリンクは、検索順位にいちばん効きます。
- **使い方の記事を増やす**：「京都 2日間 モデルコース」「東京と大阪の友達と合流するなら」のような記事ページが増えるほど、検索で見つかる入口が増えます。
- **Search Console を月に1回見る**：どんな言葉で表示されているかがわかるので、その言葉を紹介文に足していきます。

検索順位は、Googleが時間をかけて決めるものです。確実に上位に出る方法はありません。登録から効果が出るまでに、数週間〜数か月かかるのがふつうです。
