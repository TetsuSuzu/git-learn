

---



---

## 📌 プルリクエストとコミットの関係

```mermaid
flowchart TD
    A[貢献者がフィーチャーブランチで作業] --> B[複数のコミットを積み重ねる<br>各コミット = 特定の変更を記録]
    B --> C[プルリクエストを作成・提出]
    C --> D[レビュアーがコミットごとに変更を確認]
    D --> E{承認されたか？}
    E -- ✅ 承認 --> F[デフォルトブランチにマージ]
    E -- ❌ 修正要求 --> G[追加コミットで修正]
    G --> D
    F --> H[変更がベースコードに統合される 🎉]
```

---

## 🔍 プルリクエスト内のコミットの役割

| 要素 | 説明 |
|---|---|
| **個々のコミット** | 貢献者が行った**特定の変更**を1つ1つ記録 |
| **コミットの集合** | PR全体として「提案された修正の全体像」を形成 |
| **可視性** | PR作成者だけでなく**全ての関係者**が閲覧可能 |
| **マージ後** | デフォルトブランチのコミット履歴に統合される |

---

## 📝 コミットとPRの具体的なイメージ

```mermaid
gitGraph
    commit id: "既存のコード"
    branch feature-branch
    checkout feature-branch
    commit id: "コミット①: バグ修正"
    commit id: "コミット②: テスト追加"
    commit id: "コミット③: ドキュメント更新"
    checkout main
    merge feature-branch id: "PR承認→マージ"
```


---

## 📝 まとめ

> プルリクエスト内の**個々のコミット**は、貢献者が行った**特定の変更を明確に示す**役割を持ちます。  
> これらが積み重なることでPR全体の修正内容が形成され、  
> **承認後にデフォルトブランチへマージ**されることで、変更がベースコードに正式に統合されます。

🔗 公式ドキュメント：[About pull requests - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
