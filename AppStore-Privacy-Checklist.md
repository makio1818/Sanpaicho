# 産廃帳 App Store Connect プライバシー申告チェックリスト

最終確認日：2026年10月29日
対象：産廃帳 Ver.2.0（`Sanpaicho-MapCluster-Fix` 版のソースコード）

このチェックリストは、App Store Connect の「Appのプライバシー」の回答と、審査前の確認のためのものです。
**ソースコードを読んで確認した内容**にもとづいていますが、実機でのビルドや通信の計測はしていません。最終的な回答は開発者ご本人の判断で行ってください。

> ⚠️ お手元のXcodeプロジェクトが、この解析に使ったバージョンと違う場合（あとから機能やライブラリを追加した場合など）は、結果が変わります。

---

## 1. Appleの「収集」の定義（ここが判断の基準です）

Appleの説明（[App privacy details on the App Store](https://developer.apple.com/app-store/app-privacy-details/)）では、

- **「収集（Collect）」** ＝ データを端末の外に送信し、**開発者や開発者の提携先**が、リアルタイムでリクエストを処理するのに必要な時間より長くアクセスできる状態にすること。
- **Appleが収集するデータ**（MapKitなどAppleのフレームワーク・サービス経由）について、**開発者が申告する責任はない**、とされています。

つまり、**端末内に保存するだけのデータ**や、**端末内でアクセスするだけの権限（カメラ・連絡先・位置情報）**は、それだけでは「収集」になりません。

---

## 2. ソースコードの解析結果

| # | 確認項目 | 結果 | 根拠（ファイル） |
|---|---|---|---|
| 1 | アプリ名・Bundle ID | 表示名「産廃帳」。Bundle ID は **仮の値 `com.example.sanpaicho`**（共有拡張 `com.example.sanpaicho.share`） | `project.pbxproj`、`Info.plist` |
| 2 | SwiftDataに保存される情報 | 施設（名称・読み・種別・住所・緯度経度・メモ・お気に入り・最終利用実績）、品目・単価、電話番号、追加住所、名刺（氏名・会社・部署・役職・電話・FAX・メール・郵便番号・住所・Webサイト・メモ）、連絡先ひも付け（連絡先の識別子） | `Shared/Models/*.swift` |
| 3 | 名刺画像の保存先 | App Groupコンテナ内 `Library/Application Support/CardImages/`（JPEG、最大1600pxと480pxの縮小画像、ファイル保護あり）。名刺削除時に削除 | `Shared/Data/CardImageStore.swift` |
| 4 | OCR処理方式 | Vision `VNRecognizeTextRequest`（ja-JP / en-US）による**端末内処理** | `Sanpaicho/Scan/CardTextRecognizer.swift` |
| 5 | カメラ・写真 | カメラ：VisionKit の書類スキャナ（`NSCameraUsageDescription` あり）。写真：`PhotosPicker`（ライブラリ全体の許可は不要） | `CardScanFlowView.swift`、`CardEditorView.swift` |
| 6 | 連絡先の読み込み・書き込み | 読み込み：`CNContactPickerViewController`（許可不要）と共有拡張（vCard）。書き込み：`CNContactViewController(forNewContact:)`。許可要求は登録済み連絡先を開くときのみ。重複確認は許可済みの場合のみ端末内で照合 | `Sanpaicho/Contacts/`、`SanpaichoShare/` |
| 7 | ジオコーディング | `CLGeocoder` → 見つからなければ `MKLocalSearch`。**住所の文字がAppleに送信される**。無料版でも住所保存時に実行 | `Shared/Logic/AddressGeocoder.swift` |
| 8 | MapKit | PRO版のマップ（`MKMapView`）、位置の手動検索（`MKLocalSearch`） | `Sanpaicho/Views/Map/`、`LocationPickerView.swift` |
| 9 | 位置情報 | `requestWhenInUseAuthorization` をPRO版マップでのみ要求。現在地は保存・送信しない | `MapTabView.swift` |
| 10 | 外部サーバーとの通信 | **なし**（`URLSession` 等の独自通信なし）。Apple（地図・位置検索・App Store）以外への通信なし | 全ソースのgrep |
| 11 | Firebase・解析・広告SDK | **なし**（Swift Package 0件、外部SDKなし） | `project.pbxproj` |
| 12 | StoreKit 2 | 非消耗型 `com.example.sanpaicho.pro`（**仮の値**）。`Transaction.currentEntitlements`で判定、`AppStore.sync`で復元、`Transaction.updates`を監視。ファミリー共有：オフ | `Sanpaicho/Store/StoreManager.swift`、`Sanpaicho.storekit` |
| 13 | データ削除方法 | 施設・名刺を個別に削除可能。一括削除なし（アプリ削除で全削除）。**名刺の累計保存件数（数字のみ）はキーチェーンに残る** | `FacilityDetailView.swift`、`CardDetailView.swift`、`CardQuota.swift` |
| 14 | iCloud同期・バックアップ | iCloud同期なし（`cloudKitDatabase: .none`）。バックアップ除外の設定はないため、**端末のバックアップには含まれる可能性あり** | `Shared/Data/SharedStore.swift` |
| 15 | アプリ内のプライバシーポリシー | 設定画面にアプリ内のポリシー画面あり。WebのURLは未設定（`privacyPolicyURL = nil`） | `PrivacyPolicyView.swift`、`AppConfig.swift` |
| — | トラッキング | なし。`PrivacyInfo.xcprivacy`：`NSPrivacyTracking = false`、収集データなし、UserDefaults理由 `CA92.1` | `PrivacyInfo.xcprivacy` |

---

## 3. 「Appのプライバシー」の回答案

### 最初の質問：「このAppからデータを収集しますか？」

**回答案：「いいえ、このAppからデータを収集しません」（データの収集なし）**

理由：開発者や提携先が受け取る経路が、ソースコード上に存在しないため。

### データの種類ごとの判断

| データの種類 | 端末内での扱い | 端末外への送信 | 「収集」に当たるか | 判断 |
|---|---|---|---|---|
| 連絡先情報（名前・電話・メール・住所） | 施設・名刺として端末内に保存 | なし | 当たらない | 申告不要 |
| 連絡先（ユーザーの連絡先リスト） | 選んだ1件のみ取り込み／重複確認は端末内 | なし | 当たらない | 申告不要 |
| 写真またはビデオ（名刺画像） | 端末内に保存 | なし | 当たらない | 申告不要 |
| その他のユーザーコンテンツ（品目・単価・メモ） | 端末内に保存 | なし | 当たらない | 申告不要 |
| 正確な位置情報・おおよその位置情報（現在地） | 地図表示にのみ使用、保存しない | 開発者へはなし（地図表示はiOS・Apple） | 当たらない | 申告不要 |
| 住所の文字（ジオコーディング・位置検索） | 座標を端末内に保存 | **Appleへ送信** | Appleが受け取るデータであり、開発者は申告不要とされている | **要確認**（下記A） |
| 購入履歴 | 購入状態を端末内で確認 | Apple（App Store）とのやりとり | 開発者がサーバーで保持しない | **要確認**（下記B） |
| ユーザーID・デバイスID | 使用していない | なし | 当たらない | 申告不要 |
| 使用状況データ・診断（クラッシュ等） | 独自の収集なし | なし | 当たらない | **要確認**（下記C） |
| お問い合わせメールの内容・メールアドレス | — | ユーザーが自分のメールアプリから送信 | — | **要確認**（下記D） |

### 要確認の項目

**A. MapKit・ジオコーディング（住所の文字がAppleに送られる）**
Appleは「Appleが収集するデータについて開発者は申告する責任はない」とし、例としてMapKitを挙げています。そのため、この送信を理由に「収集あり」にする必要は**ないと考えられます**。ただし、プライバシーポリシー（Webとアプリ内）では、Appleに送られることを説明しています。
→ **要確認**：最新のApp Store Connectの画面の説明と照らし合わせてください。

**B. 購入履歴（PRO版の購入）**
購入の処理はAppleが行い、本アプリはApp Storeの購入記録を端末内で確認するだけで、開発者のサーバーへ送っていません。そのため「購入履歴」の収集には**当たらないと考えられます**。
→ **要確認**：App Store Connectの販売レポート（開発者が見られる売上データ）をどう扱うか、Appleの最新の説明で確認してください。

**C. クラッシュレポート**
アプリ内に独自の収集はありません。ただし、ユーザーが「Appデベロッパと共有」をオンにしている場合、AppleからApp Store Connect／Xcodeにクラッシュ情報が届きます。これはAppleの仕組みによるものです。
→ **要確認**：独自のクラッシュ収集ツールを追加しない限り、通常は申告不要と考えられますが、最新の説明を確認してください。

**D. お問い合わせメール**
お問い合わせは、ユーザーが自分のメールアプリで送るメールです（アプリ内に送信フォームはありません）。Appleは「任意のフィードバックやカスタマーサービスの問い合わせで、すべての条件を満たすもの」は申告が任意になる場合があると説明しています。ただし、その条件の中に「送信フォームにユーザー名やアカウント名が目立つように表示される」などの項目があり、メールアプリ経由の場合に当てはまるかは明確ではありません。
→ **要確認**：
- 「データの収集なし」のままにするか、
- 慎重に「連絡先情報 → メールアドレス」「ユーザーコンテンツ → カスタマーサポート」を、目的「Appの機能（サポート対応）」・ユーザーに**リンクされる**・トラッキング**なし**として申告するか、
を開発者ご本人が判断してください。迷う場合は、後者（申告する）の方が審査上は安全です。

### トラッキング

**回答案：トラッキングなし**（広告・データブローカーとの共有なし、IDFA未使用、`App Tracking Transparency` 未使用）

---

## 4. 審査ガイドラインとの対応

| ガイドライン | 要件 | 状況 |
|---|---|---|
| 5.1.1(i) | App Store Connect とアプリ内の**両方**にプライバシーポリシーへのリンク | Web：`privacy.html` ✅／アプリ内：ポリシー画面はあるが、**WebのURLが未設定** → 下記「アプリ側で必要な修正」 |
| 5.1.1(i) | 収集するデータ・方法・用途 | `privacy.html` 2〜9 ✅ |
| 5.1.1(i) | 第三者が同等の保護を行うこと | 第三者SDKなし。Appleの利用を8・12で説明 ✅ |
| 5.1.1(i) | 保存期間・削除方針、同意の撤回・削除の方法 | `privacy.html` 5〜7（権限の変更）、10・11 ✅ |
| 5.1.1(v) | アカウント作成がある場合のアカウント削除機能 | アカウント機能なし → 対象外 |
| 1.5 | サポートURLから簡単に連絡できること | `support.html` にメールボタン ✅ |
| 3.1.1 | デジタル機能の解放はアプリ内課金で／購入の復元 | StoreKit 2 ／「購入を復元」ボタンあり ✅ |

---

## 5. アプリ側で必要な修正（今回はXcodeプロジェクトを変更していません）

### 必須

1. **お問い合わせ先メールアドレス**
   `Shared/Config/AppConfig.swift`
   ```swift
   static let supportEmail = "support@example.com"
   ```
   → `"kanri.connect18@gmail.com"` に変更。

2. **プライバシーポリシーのURL**
   `Shared/Config/AppConfig.swift`
   ```swift
   static let privacyPolicyURL: URL? = nil
   ```
   → 公開後に確認した実際のURLに変更（例：`URL(string: "https://（ユーザー名）.github.io/sanpaicho/privacy.html")`）。
   現在の実装では、URLを設定すると「設定」→「プライバシーポリシー」画面の下にWeb版へのリンクが表示されます。
   ※ 審査で「アプリ内からすぐにアクセスできる」ことを確実にするため、設定画面の「サポート」欄に「プライバシーポリシー（Web）」の行を直接追加することもおすすめします。

3. **サポートページへのリンク（未実装）**
   現在、設定画面には「お問い合わせ」（メール作成）はありますが、Webのサポートページへのリンクはありません。
   - `AppConfig.swift` に `static let supportURL = URL(string: "https://（ユーザー名）.github.io/sanpaicho/support.html")` を追加
   - `SettingsView.swift` の「サポート」セクションに「サポート・よくある質問」の行を追加し、`openURL(AppConfig.supportURL)` で開く

4. **アプリ内のプライバシーポリシーの文章を、Web版と一致させる**
   `Sanpaicho/Views/Settings/PrivacyPolicyView.swift` の現在の文章は、次の点でWeb版より情報が少なく、一部の表現が実装と食い違います。
   - 「すべてお使いのiPhoneの中にのみ保存」「アプリを削除すると、これらのデータも削除」
     → **名刺の累計保存件数（数字のみ）がキーチェーンに残る**こと、**端末のバックアップに含まれる場合がある**ことを追記
   - 連絡先に登録したときに**連絡先の識別子を保存**することを追記
   - **住所の保存時に自動でAppleへ住所が送られる**（無料版でも）ことを明記（現在は「住所からの位置検索」とだけ記載）
   - データの**削除方法**（施設・名刺の削除、アプリの削除）を追記
   - 施行日・最終更新日を、Web版と同じ日付にそろえる
   - お問い合わせ先にメールアドレスを明記
   いちばん簡単なのは、アプリ内の文章を短い要約にして、末尾にWeb版（`privacy.html`）へのリンクを置く方法です。

5. **仮のIDを本番用に変更**
   - Bundle ID：`com.example.sanpaicho` → 自分のID（共有拡張は `＜本体のID＞.share`）
   - App Group：`group.com.example.sanpaicho` → `group.＜本体のID＞`（`AppConfig.appGroupID`、2つの `.entitlements`、Signing & Capabilities）
   - 商品ID：`com.example.sanpaicho.pro` → App Store Connectで作成した非消耗型の商品ID（`AppConfig.proProductID` と `Sanpaicho.storekit`）
   ※ App Groupを変えると、テスト中に端末へ保存したデータは新しい保存先から見えなくなります（公開前なら問題ありません）。

6. **App Store Connect への登録**
   - プライバシーポリシーURL：`…/sanpaicho/privacy.html`
   - サポートURL：`…/sanpaicho/support.html`
   - 価格：PRO 500円（日本）で非消耗型の商品を作成し、審査用のスクリーンショット・説明を登録

### 任意（おすすめ）

- 名刺の文字読み取りや位置の自動取得が失敗したときの案内文に、サポートページへのリンクを追加
- `PrivacyInfo.xcprivacy`：現状（トラッキングなし・収集データなし・UserDefaults `CA92.1`）で解析結果と一致しています。上記Dで「メールアドレス」等を申告する場合は、`NSPrivacyCollectedDataTypes` もそろえるかどうか **要確認**（メールアプリ経由のためアプリ自体は収集していない、という整理もあり得ます）。
- キーチェーンの利用（`kSecClassGenericPassword`）は、プライバシーマニフェストの「必須理由API」の対象ではないと考えられますが **要確認**。

---

## 6. 未確認・要確認のまとめ

| 項目 | 状態 |
|---|---|
| Xcodeでのビルド・実機での通信内容 | 未確認（ソースコードの読み取りのみ） |
| お手元のプロジェクトとこの解析対象が同じか | 要確認 |
| MapKit・ジオコーディングを申告不要とする判断 | 要確認（Appleの説明上は申告不要） |
| 購入履歴を申告不要とする判断 | 要確認 |
| お問い合わせメールを申告するか | 要確認（開発者の判断） |
| クラッシュレポートの扱い | 要確認 |
| 公開URL（GitHubのユーザー名） | 公開後に確認 |
| PROの実際の価格表示 | App Store Connectで500円（日本）を設定後に確認 |

## 参考（Apple公式）

- App privacy details on the App Store：https://developer.apple.com/app-store/app-privacy-details/
- App Review Guidelines（5.1.1 データの収集と保存、1.5 デベロッパ情報）：https://developer.apple.com/app-store/review/guidelines/
- Privacy manifest files：https://developer.apple.com/documentation/bundleresources/privacy-manifest-files
