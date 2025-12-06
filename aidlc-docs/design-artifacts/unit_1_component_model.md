# ユニット1：アカウント管理コンポーネントモデル

このドキュメントでは、ユニット1のユーザーストーリーを実装するためのコンポーネントモデルを設計します。

## 計画

- [x] **ステップ1：コアコンポーネントの定義**：ユーザーアカウント管理に必要な主要コンポーネント（登録、ログイン、外部アカウント連携）を特定します。
- [x] **ステップ2：コンポーネントの属性と振る舞いの詳細化**：各コンポーネントのプロパティ（属性）と実行可能なアクション（振る舞い）を定義します。
- [x] **ステップ3：コンポーネントの相互作用のマッピング**：各ユーザーストーリーを実現するためにコンポーネントがどのように相互作用するかを図示します。
- [x] **ステップ4：APIエンドポイントの設計**：フロントエンドとバックエンドの通信に必要なAPIエンドポイントの概要を説明します。
- [x] **ステップ5：データモデルの作成**：ユーザーアカウントと連携アカウントのデータ構造を定義します。

## ステップ1：コアコンポーネントの定義

- **UserRegistration**: 新規ユーザー登録を処理します。
- **UserLogin**: ユーザー認証を管理します。
- **AccountLinker**: AmazonやNetflixなどの外部アカウントの連携を処理します。
- **AccountSettings**: ユーザーがアカウントを管理できるようにし、外部アカウントの連携解除も含まれます。
- **EmailService**: 登録時に確認メールを送信します。
- **UserService**: ユーザーデータと状態を管理します。
- **OAuthRedirectHandler**: 外部サービスからのOAuthリダイレクトを処理します。

## ステップ2：コンポーネントの属性と振る舞いの詳細化

### UserRegistration
- **属性**:
  - `email` (string)
  - `password` (string)
- **振る舞い**:
  - `register()`: ユーザーを登録し、`UserService`を呼び出してユーザーを作成し、`EmailService`をトリガーします。
  - `validateInput()`: メールとパスワードの形式が有効か検証します。

### UserLogin
- **属性**:
  - `email` (string)
  - `password` (string)
- **振る舞い**:
  - `login()`: ユーザーを認証します。
  - `logout()`: ユーザーセッションを終了します。

### AccountLinker
- **属性**:
  - `serviceName` (string): "Amazon" または "Netflix"
- **振る舞い**:
  - `linkAccount()`: 外部サービスの認証ページにリダイレクトします。
  - `unlinkAccount()`: `UserService`を呼び出してアカウントの連携を解除します。

### AccountSettings
- **属性**:
  - `userId` (string)
- **振る舞い**:
  - `getLinkedAccounts()`: 連携済みのアカウントのリストを取得します。
  - `unlinkAccount()`: `AccountLinker`を呼び出してアカウントの連携を解除します。

### EmailService
- **属性**:
  - `recipientEmail` (string)
  - `emailSubject` (string)
  - `emailBody` (string)
- **振る舞い**:
  - `sendConfirmationEmail()`: 登録確認メールを送信します。

### UserService
- **属性**:
  - `userId` (string)
  - `email` (string)
  - `linkedAccounts` (array)
- **振る舞い**:
  - `createUser()`: 新しいユーザーを作成します。
  - `getUser()`: ユーザー情報を取得します。
  - `updateUser()`: ユーザー情報を更新します。

### OAuthRedirectHandler
- **属性**:
  - `serviceName` (string)
  - `authorizationCode` (string)
- **振る舞い**:
  - `handleRedirect()`: 外部サービスからのリダイレクトを処理し、認証コードをアクセストークンと交換し、`UserService`を更新します。

## ステップ3：コンポーネントの相互作用のマッピング

### ユーザーストーリー1.1: 新規登録
1. ユーザーが`UserRegistration`コンポーネントを操作します。
2. `UserRegistration`が入力値を検証します。
3. `UserRegistration`が`UserService.createUser()`を呼び出します。
4. `UserService`がユーザーを作成します。
5. `UserRegistration`が`EmailService.sendConfirmationEmail()`を呼び出します。
6. `EmailService`がメールを送信します。
7. ユーザーはAmazonアカウント連携ページ（`AccountLinker`）にリダイレクトされます。

### ユーザーストーリー1.2: ログイン
1. ユーザーが`UserLogin`コンポーネントを操作します。
2. `UserLogin`がユーザーを認証します。
3. ユーザーはメインの推薦画面にリダイレクトされます。

### ユーザーストーリー1.3: Amazonアカウント連携
1. ユーザーがAmazon用の`AccountLinker`を操作します。
2. `AccountLinker`がAmazonのOAuthページにリダイレクトします。
3. ユーザーがAmazonで認証します。
4. Amazonが認証コードとともにアプリケーションにリダイレクトします。
5. `OAuthRedirectHandler`がコードを受け取ります。
6. `OAuthRedirectHandler`がコードをアクセストークンと交換します。
7. `OAuthRedirectHandler`が`UserService.updateUser()`を呼び出して連携アカウント情報を保存します。
8. ユーザーはNetflixアカウント連携ページ（`AccountLinker`）にリダイレクトされます。

### ユーザーストーリー1.4: Netflixアカウント連携
1. ユーザーがNetflix用の`AccountLinker`を操作します。
2. `AccountLinker`がNetflixのOAuthページにリダイレクトします。
3. ユーザーがNetflixで認証します。
4. Netflixが認証コードとともにアプリケーションにリダイレクトします。
5. `OAuthRedirectHandler`がコードを受け取ります。
6. `OAuthRedirectHandler`がコードをアクセストークンと交換します。
7. `OAuthRedirectHandler`が`UserService.updateUser()`を呼び出して連携アカウント情報を保存します。
8. ユーザーは「推薦準備完了」画面にリダイレクトされます。

### ユーザーストーリー1.5: アカウント連携の解除
1. ユーザーが`AccountSettings`を操作します。
2. ユーザーがアカウントの連携を解除することを選択します。
3. `AccountSettings`が`AccountLinker.unlinkAccount()`を呼び出します。
4. `AccountLinker`が`UserService.updateUser()`を呼び出して連携アカウントを削除します。
5. 確認メッセージが表示されます。

## ステップ4：APIエンドポイントの設計

- **`POST /api/register`**: ユーザー登録を処理します。
  - リクエストボディ: `{ "email": "...", "password": "..." }`
  - レスポンス: `{ "success": true }`
- **`POST /api/login`**: ユーザーログインを処理します。
  - リクエストボディ: `{ "email": "...", "password": "..." }`
  - レスポンス: `{ "token": "..." }`
- **`GET /api/account/linked`**: 連携済みのアカウントのリストを取得します。
  - レスポンス: `[{ "serviceName": "Amazon", "linked": true }, ...]`
- **`GET /api/link/{serviceName}`**: アカウント連携プロセスを開始します。
  - 外部サービスのOAuthページにリダイレクトします。
- **`GET /api/oauth/callback/{serviceName}`**: 外部サービスからのコールバックを処理します。
  - クエリパラメータ: `code=...`
  - 連携プロセスの次のステップまたはメインアプリケーションにリダイレクトします。
- **`POST /api/unlink/{serviceName}`**: 外部アカウントの連携を解除します。
  - レスポンス: `{ "success": true }`

## ステップ5：データモデルの作成

### User
- `userId` (string, primary key)
- `email` (string, unique)
- `passwordHash` (string)
- `createdAt` (datetime)
- `updatedAt` (datetime)

### LinkedAccount
- `linkedAccountId` (string, primary key)
- `userId` (string, foreign key to `User`)
- `serviceName` (string, e.g., "Amazon", "Netflix")
- `accessToken` (string, encrypted)
- `refreshToken` (string, encrypted)
- `createdAt` (datetime)
- `updatedAt` (datetime)

