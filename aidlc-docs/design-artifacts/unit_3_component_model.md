# ユニット3：レコメンデーションインタラクションのコンポーネントモデル

このドキュメントは、フィードバックの提供、ウォッチリストの管理、レコメンデーション理由の表示など、レコメンデーションインタラクションに関連するユーザーストーリーを実装するために設計されたコンポーネントモデルを詳述します。

## 対象となるユーザーストーリー

*   **ユーザーストーリー3.1：レコメンデーションへのフィードバック（興味なし）**
    *   ユーザーとして、興味のない推薦映画を「興味なし」とマークしたい。
*   **ユーザーストーリー3.2：ウォッチリストへの追加**
    *   ユーザーとして、興味のある推薦映画をアプリ内のウォッチリストに追加したい。
*   **ユーザーストーリー3.3：レコメンデーション理由の表示**
    *   ユーザーとして、なぜその映画が推薦されたのか具体的な理由を知りたい。

## コンポーネントの定義

### 1. RecommendationListコンポーネント

*   **責任**: 推薦された映画のリストを表示し、各映画アイテムに対するユーザーインタラクションを調整する。
*   **属性**:
    *   `recommendations`: `RecommendationItem`オブジェクトの配列。
*   **振る舞い**:
    *   `displayRecommendations(recommendationsData)`: レコメンデーションデータに基づいてリストを描画する。
    *   `handleItemAction(itemId, actionType)`: 個々のアイテムに対するアクション（例：「興味なし」、「ウォッチリストに追加」）を処理し、適切なサービスにディスパッチする。
    *   `updateRecommendation(itemId, newStatus)`: 特定の推薦アイテムの状態（例：非表示）を更新する。

### 2. RecommendationItemコンポーネント

*   **責任**: 個々の推薦映画の詳細を表示し、ユーザーアクションボタンを提供する。
*   **属性**:
    *   `id`: 映画の一意の識別子。
    *   `title`: 映画のタイトル。
    *   `imageUrl`: 映画のポスター画像のURL。
    *   `description`: 映画の短い説明。
    *   `isNotInterested`: ユーザーが「興味なし」とマークしたかどうかを示すブール値。
*   **振る舞い**:
    *   `render()`: 映画情報とアクションボタン（「興味なし」、「ウォッチリストに追加」、「なぜこれを推薦？」）を表示する。
    *   `onNotInterestedClick()`: 「興味なし」ボタンがクリックされたときに`RecommendationList`に通知する。
    *   `onAddToWatchlistClick()`: 「ウォッチリストに追加」ボタンがクリックされたときに`RecommendationList`に通知する。
    *   `onReasonClick()`: 「なぜこれを推薦？」がクリックされたときに`RecommendationList`に通知する。

### 3. FeedbackServiceコンポーネント

*   **責任**: ユーザーのフィードバック（「興味なし」）を記録し、将来の推薦アルゴリズムに影響を与えるためのデータ処理を行い、一時的な「元に戻す」機能を提供する。
*   **属性**:
    *   `notInterestedItems`: ユーザーが「興味なし」とマークしたアイテムの履歴を保持する一時的なリスト。
*   **振る舞い**:
    *   `markAsNotInterested(itemId)`: アイテムを「興味なし」としてマークし、関連データを保存する。
    *   `undoNotInterested(itemId)`: 「興味なし」のアクションを元に戻す。
    *   `getFeedbackData()`: 推薦アルゴリズム改善のために収集されたフィードバックデータを返す。

### 4. WatchlistServiceコンポーネント

*   **責任**: ユーザーのウォッチリストの管理（追加、削除、表示）を行う。
*   **属性**:
    *   `watchlist`: ユーザーのウォッチリストにある映画アイテムの配列（永続化される）。
*   **振る舞い**:
    *   `addItem(item)`: 映画をウォッチリストに追加する。
    *   `removeItem(itemId)`: ウォッチリストから映画を削除する。
    *   `getWatchlist()`: 現在のウォッチリストを返す。

### 5. RecommendationReasoningコンポーネント

*   **責任**: 特定の推薦映画に対する詳細な推薦理由を表示する。
*   **属性**:
    *   `reasonText`: 表示する推薦理由のテキスト。
*   **振る舞い**:
    *   `displayReason(itemId)`: 特定の映画の推薦理由を検索し、表示する。
    *   `fetchReason(itemId)`: バックエンドまたは推薦エンジンから推薦理由を取得する。

### 6. RecommendationEngineClientコンポーネント

*   **責任**: 推薦エンジンとの通信を処理し、推薦リストや推薦理由を取得する。
*   **属性**:
    *   なし（主にメソッドを持つサービス）
*   **振る舞い**:
    *   `getRecommendations(userId)`: ユーザーIDに基づいて推薦リストを取得する。
    *   `getRecommendationReason(itemId)`: 特定の映画の推薦理由を取得する。
    *   `sendFeedback(userId, itemId, feedbackType)`: ユーザーフィードバックを推薦エンジンに送信する。

### 7. UserInterfaceManagerコンポーネント

*   **責任**: アプリケーション全体のUIフローとナビゲーションを管理する。
*   **属性**:
    *   `currentPage`: 現在表示されているページの識別子（例：「recommendations」、「watchlist」）。
*   **振る舞い**:
    *   `navigateTo(page)`: 指定されたページにナビゲートする。
    *   `showNotification(message)`: ユーザーへの通知（例：「ウォッチリストに追加しました」）を表示する。

## コンポーネントの相互作用（ユーザーストーリーごとのフロー）

### ユーザーストーリー3.1：レコメンデーションへのフィードバック（興味なし）

1.  **ユーザーアクション**: ユーザーが`RecommendationItem`の「興味なし」ボタンをクリック。
2.  **`RecommendationItem`**: `onNotInterestedClick()`を呼び出し、`RecommendationList`に通知。
3.  **`RecommendationList`**: `handleItemAction(itemId, 'notInterested')`を呼び出し、`FeedbackService`に`markAsNotInterested(itemId)`を要求。
4.  **`FeedbackService`**:
    *   `itemId`を「興味なし」リストに追加。
    *   `RecommendationEngineClient.sendFeedback(userId, itemId, 'notInterested')`を介してこの情報をバックエンドに送信。
    *   「元に戻す」オプションのためのタイマーを開始。
5.  **`RecommendationList`**: `updateRecommendation(itemId, { isNotInterested: true })`を呼び出し、UIからアイテムを非表示にする。
6.  **（オプション）ユーザーアクション**: ユーザーが「元に戻す」をクリック。
7.  **`RecommendationList`**: `handleItemAction(itemId, 'undoNotInterested')`を呼び出し、`FeedbackService`に`undoNotInterested(itemId)`を要求。
8.  **`FeedbackService`**: `itemId`を「興味なし」リストから削除し、`RecommendationEngineClient`を介してバックエンドにフィードバックのキャンセルを通知。
9.  **`RecommendationList`**: `updateRecommendation(itemId, { isNotInterested: false })`を呼び出し、UIにアイテムを再表示する。

### ユーザーストーリー3.2：ウォッチリストへの追加

1.  **ユーザーアクション**: ユーザーが`RecommendationItem`の「ウォッチリストに追加」ボタンをクリック。
2.  **`RecommendationItem`**: `onAddToWatchlistClick()`を呼び出し、`RecommendationList`に通知。
3.  **`RecommendationList`**: `handleItemAction(itemData, 'addToWatchlist')`を呼び出し、`WatchlistService`に`addItem(itemData)`を要求。
4.  **`WatchlistService`**: `itemData`をユーザーのウォッチリストに追加し、永続化する。
5.  **`UserInterfaceManager`**: `showNotification("ウォッチリストに追加しました")`を呼び出し、成功メッセージをユーザーに表示。
6.  **ユーザーアクション**: ユーザーがウォッチリストページにナビゲート。
7.  **`UserInterfaceManager`**: `navigateTo('watchlist')`を呼び出す。
8.  **`WatchlistService`**: `getWatchlist()`を呼び出し、ウォッチリスト内の映画を取得。
9.  **UI表示**: ウォッチリストの映画が専用ページに表示される。
10. **ユーザーアクション**: ユーザーがウォッチリストから映画を削除。
11. **UIコンポーネント（ウォッチリストページ内）**: `WatchlistService`に`removeItem(itemId)`を要求。
12. **`WatchlistService`**: `itemId`をウォッチリストから削除し、永続化を更新する。

### ユーザーストーリー3.3：レコメンデーション理由の表示

1.  **ユーザーアクション**: ユーザーが`RecommendationItem`の「なぜこれを推薦？」リンク/アイコンをクリック。
2.  **`RecommendationItem`**: `onReasonClick()`を呼び出し、`RecommendationList`に通知。
3.  **`RecommendationList`**: `handleItemAction(itemId, 'showReason')`を呼び出し、`RecommendationReasoning`コンポーネントに`displayReason(itemId)`を要求。
4.  **`RecommendationReasoning`**:
    *   `fetchReason(itemId)`を呼び出し、`RecommendationEngineClient.getRecommendationReason(itemId)`を介して推薦理由を取得。
    *   取得した理由をユーザーに表示する（ポップアップやモーダルなど）。
