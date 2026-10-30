# 産廃帳 公式サイト（GitHub Pages用）

iPhoneアプリ「産廃帳」のApp Store公開に使う、公式サイト一式です。
GitHub Pagesにアップロードするだけで公開できます（ビルド作業・サーバー・データベース・APIキーは不要です）。

## フォルダの中身

```
Sanpaicho-GitHubPages/
├── index.html                    トップページ
├── privacy.html                  プライバシーポリシー
├── support.html                  サポート・お問い合わせ
├── assets/
│   ├── style.css                 デザイン（全ページ共通）
│   └── app-icon.png              アプリアイコン（ご提供のアイコンを縮小しただけで、デザインは変更していません）
├── README.md                     この手順書（公開しても問題ありませんが、サイトには表示されません）
└── AppStore-Privacy-Checklist.md App Store Connectのプライバシー申告用チェックリスト
```

- ページ同士のリンク、CSS、画像はすべて**相対パス**で書いてあります。`https://ユーザー名.github.io/sanpaicho/` のようなサブフォルダのURLでもそのまま動きます。
- 外部のフォント・スクリプト・アクセス解析は読み込んでいません（JavaScriptは使っていません）。
- お問い合わせ先は全ページで `kanri.connect18@gmail.com` に統一しています。

---

## 公開する前に知っておくこと

### 既存のサイトは消えません

この手順では、**産廃帳専用の新しいリポジトリ `sanpaicho`** を作ります。
以前に作った別のアプリのサイト（別のリポジトリ）には一切触れないので、削除・上書きされることはありません。

> ⚠️ 注意：既存のリポジトリ（特に `ユーザー名.github.io` という名前のリポジトリ）にはアップロードしないでください。必ず新しく作った `sanpaicho` リポジトリにアップロードします。

### 公開されるURL

GitHubのユーザー名が `yourname` の場合、次のURLになります（`yourname` の部分はあなたのユーザー名に置き換わります）。

| ページ | URL |
|---|---|
| トップ | `https://yourname.github.io/sanpaicho/` |
| プライバシーポリシー | `https://yourname.github.io/sanpaicho/privacy.html` |
| サポート | `https://yourname.github.io/sanpaicho/support.html` |

※ 実際のURLは、公開後にGitHubの画面で必ず確認してください（手順11）。上の表は例です。

### 無料プランでの注意

GitHubの無料プランでGitHub Pagesを使うには、リポジトリを **Public（公開）** にする必要があります。
このサイトのファイルには、公開して困る情報（パスワードなど）は含まれていません。

---

## 公開手順（12ステップ）

### 1. GitHubにログイン

1. ブラウザで https://github.com を開きます。
2. 右上の「Sign in」から、いつものアカウントでログインします。

### 2. 新しいPublicリポジトリを作成

1. 画面右上の「＋」ボタン → 「New repository」をクリックします。
   （または https://github.com/new を開きます）

### 3. リポジトリ名を `sanpaicho` に設定

1. 「Repository name」に `sanpaicho` と入力します（すべて半角小文字）。
2. 「Description」は空欄のままで構いません（入れる場合は「産廃帳 公式サイト」など）。
3. 公開範囲は **「Public」** を選びます。
4. 「Add a README file」などのチェックは**入れなくて大丈夫**です。
5. 一番下の緑色の「Create repository」をクリックします。

> 「sanpaicho という名前はすでに存在します」と出た場合は、以前に同じ名前で作っています。そのリポジトリの中身を確認してから使うか、別の名前（例：`sanpaicho-app`）にしてください。名前を変えた場合、URLの `/sanpaicho/` の部分もその名前になります。

### 4. ZIPを展開

1. ダウンロードした `Sanpaicho-GitHubPages-Website.zip` をダブルクリックして展開します。
   - Mac：ダブルクリックで展開されます。
   - Windows：右クリック → 「すべて展開」。
2. `Sanpaicho-GitHubPages` フォルダができ、中に `index.html` などがあることを確認します。

### 5. HTML・CSS・画像ファイルをアップロード

1. 作成したリポジトリの画面で、「uploading an existing file」というリンクをクリックします。
   （リンクが見当たらない場合は「Add file」→「Upload files」）
2. `Sanpaicho-GitHubPages` フォルダを開き、**中身をすべて選択**して、ブラウザの「Drag files here」の枠にドラッグ＆ドロップします。
   - ドラッグするのは**フォルダの中身**です（`index.html`、`privacy.html`、`support.html`、`assets` フォルダ、`README.md`、`AppStore-Privacy-Checklist.md`）。
   - `Sanpaicho-GitHubPages` フォルダごとドラッグすると、URLが `/sanpaicho/Sanpaicho-GitHubPages/` になってしまうので注意してください。
   - `assets` フォルダもフォルダのままドラッグすれば、中の `style.css` と `app-icon.png` も一緒にアップロードされます。
3. アップロード一覧に `assets/style.css` と `assets/app-icon.png` が表示されていることを確認します。
4. 下の「Commit changes」ボタンをクリックします。
5. リポジトリのトップに `index.html` と `assets` フォルダが並んでいればOKです。

> `README.md` と `AppStore-Privacy-Checklist.md` はアップロードしてもしなくても構いません。チェックリストは手元の確認用です。

### 6. GitHubのSettingsを開く

リポジトリの画面上部のタブから「⚙ Settings」をクリックします。
（画面が狭いと「…」の中に隠れていることがあります）

### 7. Pagesを選択

左側のメニューの「Code and automation」の中にある「Pages」をクリックします。

### 8. 「Deploy from a branch」を選択

「Build and deployment」の「Source」で、「**Deploy from a branch**」を選びます。

### 9. mainブランチ・/(root)を指定

「Branch」の欄で、
- 左のボタン（None と表示されている場合）→ **`main`** を選択
- 右のボタン → **`/ (root)`** を選択

### 10. Saveを押す

「Save」ボタンをクリックします。

### 11. 公開完了後にURLを確認

1. 1〜5分ほど待ってから、同じ「Settings」→「Pages」の画面を再読み込みします。
2. 上部に「Your site is live at https://（ユーザー名）.github.io/sanpaicho/」と表示されたら公開完了です。
3. 「Visit site」をクリックし、次を確認します。
   - トップページにアイコンと「産廃帳」が表示される
   - 色付きのデザインで表示される（白黒の文字だけの場合は、CSSがアップロードされていません。手順5を確認してください）
   - 「プライバシー」「サポート」のリンクが開く
   - 「メールでお問い合わせ」をタップするとメールアプリが開き、宛先が `kanri.connect18@gmail.com` になっている
4. iPhoneのSafariでも開いて、表示が崩れていないか確認します。

> 「404 There isn't a GitHub Pages site here.」と出る場合は、まだ公開処理中です。数分待ってから再読み込みしてください。10分以上たっても同じ場合は、手順9で `main` と `/ (root)` を選んだか、`index.html` がリポジトリの一番上（フォルダの中ではない場所）にあるかを確認してください。

### 12. App Store ConnectにURLを登録

確認したURLを、App Store Connect（https://appstoreconnect.apple.com ）に登録します。

| 登録する場所 | 入力するURL |
|---|---|
| アプリ →「App情報」→「プライバシーポリシーURL」 | `https://（ユーザー名）.github.io/sanpaicho/privacy.html` |
| アプリ → 該当バージョン →「サポートURL」 | `https://（ユーザー名）.github.io/sanpaicho/support.html` |
| アプリ → 該当バージョン →「マーケティングURL」（任意） | `https://（ユーザー名）.github.io/sanpaicho/` |

- URLは、ブラウザのアドレスバーからコピーして貼り付けると、打ち間違いを防げます。
- 「App のプライバシー」の回答は、同梱の `AppStore-Privacy-Checklist.md` を参考にしてください。
- アプリ内（設定画面）からもプライバシーポリシーとサポートページを開けるようにする必要があります。必要な修正は `AppStore-Privacy-Checklist.md` の「アプリ側で必要な修正」にまとめています。

---

## 公開後の更新方法

### 文章を少し直したいとき（ブラウザだけでできます）

1. GitHubで `sanpaicho` リポジトリを開きます。
2. 直したいファイル（例：`support.html`）をクリックします。
3. 右上の鉛筆アイコン（Edit this file）をクリックします。
4. 文章を直します。`<` `>` で囲まれた部分（タグ）は消さないように注意してください。
5. 右上の「Commit changes...」→ もう一度「Commit changes」をクリックします。
6. 1〜5分ほどで公開中のサイトに反映されます。反映されない場合は、ブラウザを再読み込みしてください。

### ファイルを差し替えたいとき

1. リポジトリの画面で「Add file」→「Upload files」をクリックします。
2. 新しいファイルをドラッグ＆ドロップします（同じ名前のファイルは上書きされます）。
3. 「Commit changes」をクリックします。

### プライバシーポリシーを更新したとき

- `privacy.html` の一番下にある「最終更新日」を、更新した日付に書き換えてください（「施行日」は最初の公開日のままにします）。
- アプリの機能（保存する情報、通信先、権限など）を変えたときは、プライバシーポリシーと、App Store Connectの「Appのプライバシー」の回答も見直してください。
- アプリ内のプライバシーポリシー画面の文章も、同じ内容にそろえてください。

### PROの価格を変えたとき

`index.html` と `support.html` の「500円」を書き換えてください。

### URLは変えないでください

App Store Connectに登録したURLが開けなくなると、審査やユーザーの問い合わせに支障が出ます。リポジトリ名の変更、リポジトリの削除、Privateへの変更、ファイル名の変更はしないでください。

---

## よくあるつまずき

| 症状 | 原因と対処 |
|---|---|
| デザインが反映されず、文字だけ表示される | `assets` フォルダがアップロードされていません。手順5をやり直してください。 |
| アイコンが表示されない | `assets/app-icon.png` がありません。ファイル名の大文字・小文字も確認してください。 |
| URLが `/sanpaicho/Sanpaicho-GitHubPages/` になる | フォルダごとアップロードしています。フォルダの中身を直接アップロードし直してください。 |
| 404と表示される | 公開処理中か、Pagesの設定（手順8〜10）が保存されていません。 |
| Pagesの設定画面に「Upgrade」と出る | リポジトリがPrivateです。Settings → General の一番下「Danger Zone」→「Change visibility」でPublicにしてください。 |
