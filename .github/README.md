# ブランチ自動削除・事前通知 GitHub Actions ワークフロー

## 概要

このリポジトリには、不要なブランチを自動的に削除する GitHub Actions ワークフローと、その削除予定を事前に Teams へ通知するワークフローが含まれています。  
運用ルールに沿って安全にブランチを整理できる仕組みです。

---

## ファイル構成

```
.github/
  ├── workflows/
  │     ├── delete-old-branches.yml       # 古いブランチ自動削除ワークフロー
  │     └── notify-branch-deletion.yml    # 削除予定ブランチ事前通知ワークフロー
  └── protected-branches.txt              # 手動で除外したいブランチ一覧
  └── README.md                           # この説明書
```

---

## 1. delete-old-branches.yml について

- **目的**  
  最終コミット日から3か月以上経過した不要ブランチを自動削除します。
- **除外対象**  
  - `master`
  - `prototype/apply/*`
  - `develop/apply/*`
  - `feature/apply/*`
  - `protected-branches.txt` に記載されたブランチ
- **実行タイミング**  
  毎月1日 0:00 (UTC) に自動実行されます。

---

## 2. notify-branch-deletion.yml について

- **目的**  
  削除予定のブランチ一覧を、削除の1週間前に Microsoft Teams へ通知します（Webhook URLが必要）。
- **実行タイミング**  
  毎月25日 0:00 (UTC) に自動実行されます。
- **通知内容**  
  3か月以上更新がなく、削除対象となるブランチ名一覧
- **Webhook 設定方法**  
  GitHubリポジトリの `Settings > Secrets and variables > Actions` で  
  `TEAMS_WEBHOOK` という名前で Webhook URL を登録してください。

---

## 3. protected-branches.txt の運用

- `.github/protected-branches.txt` に「削除したくないブランチ名」を1行ずつ記入します。
- 除外パターン（例: `feature/apply/example`）や個別ブランチ名を書いてください。
- 削除不可ブランチは定期実行前にコミット・プッシュしておいてください。

---

## 4. 前提・注意事項

- GitHub Actions の実行権限が必要です。
- ワークフローはリポジトリに push されている全てのブランチを対象に動作します。
- 削除処理は `git push origin --delete <branch>` を使います。
- Teams通知はWebhook URLが未設定の場合、通知されません（サンプルメッセージが出力されます）。

---

## 5. 運用イメージ

1. 不要なブランチを、除外リスト（protected-branches.txt）を使って運用管理。
2. 毎月25日に削除予定ブランチ一覧がTeamsへ通知（Webhook設定時）。
3. 毎月1日に自動削除が実施され、リポジトリが整理されます。

---

## 問い合わせ・改善

運用方法やワークフローに関する質問・改善要望は Issue や Pull Request でご連絡ください。
