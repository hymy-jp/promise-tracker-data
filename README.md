# promise-tracker-data

公約トラッカーの週報と最新動向を配る公開データです。アプリ本体とは別のリポジトリです。公約の原文、検証軸、評価はここには置きません。

GitHub Pages の公開源は `main` のルートです。このブランチを `main` にマージすると、次のURLで配信されます。マージは所有者の承認が必要です。

- `https://data.hymy.co.jp/weekly_delivery_v1.json`
- `https://data.hymy.co.jp/promise_developments_v1.json`

## 初期ファイルの意味

どちらも連番 `1` です。進捗や評価は書いていません。

- 週報は `action: publish` かつ `issue_state: not_registered` です。記事はありません。これは配信停止ではありません。配信停止は、記事を含まない `action: pause` で別途出します。
- 最新動向は `entries` が空です。公約の項目が無いことは「更新情報が未登録」であり、「調査したが進展がなかった」ではありません。進展がなかったと言うときは、その公約を省かず、`record_state: checked_no_confirmed_change` と確認日、出典を入れます。`not_registered` は動向の `record_state` に書きません。

`publication_approval.reviewer` は、構造を満たすための仮の値です。承認者の名前と承認時刻へ差し替える前に `main` へマージしないでください。まだ誰もこの連番を取得していないあいだは、差し替え後も連番 `1` のままにします。配信後に本文だけを変えると、取得済みのアプリは同じ連番を拒否します。

## キャッシュ

GitHub Pages ではファイルごとの `Cache-Control` を指定できません。2026-10-25 に `http://data.hymy.co.jp/` を見ると `cache-control: max-age=600` でした。訂正、撤回、配信停止は、公開URLの本文に新しい `sequence` が出てから知らせます。古いファイルへ戻す操作はしません。

## 更新、訂正、撤回、ロールバック

新しいファイルは、前回より大きい `sequence` と、前回以降の `issued_at` を持ちます。アプリは、同じ連番の別本文、より小さい連番、より古い `issued_at` を拒否し、前回成功した内容を残します。

- 週報の新規記事は `action: publish` と `feed` を付け、`issue_state` は付けません。
- 週報の文言訂正は `action: correct` と `target_issue_id` を付け、同じ記事IDの `feed` を入れます。
- 週報の撤回は `action: withdraw` とし、`feed` は入れません。対象は `withdrawals` に残します。撤回した記事IDを、その後の `feed` に戻しません。
- 週報の配信停止は `action: pause` とし、`feed` も `issue_state` も入れません。再開は `action: resume` と、見せる記事の `feed` です。
- 最新動向の訂正は `action: correct` と `target_promise_id` を付け、対象を `entries` に残します。省いた公約は `withdrawals` に入れます。省いただけの削除は拒否されます。
- 最新動向の撤回は `action: withdraw` とし、対象を `withdrawals` に残します。撤回した公約を `entries` に戻しません。
- 最新動向の配信停止は `action: pause` かつ `service_state: paused` で、`entries` は付けません。再開は `action: resume`、`service_state: active` で、見せる `entries` をすべて入れ直します。

誤って公開したあとに、古いJSONファイルへ戻さないでください。新しい連番を見た端末だけが拒否し、見ていない端末が誤った版を受け取ります。誤った動向は次の連番の `withdraw` で取り下げます。
