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

### 1-5. 公開URLを書き込む（LINEのプレビュー用）
GitHubで `index.html` を開き、右上の鉛筆マークで編集します。
`https://YOUR-SITE-URL/` という文字が2か所あるので、公開URL（例：`https://tabi-taro.github.io/tabiroute/`）に書き換えて「Commit changes」を押します。

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
