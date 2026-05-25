# git-learn

git の基本操作を学ぶための練習リポジトリです。

> **注意**: このリポジトリのデフォルトブランチは `master` です。  
> GitHub の新しいリポジトリではデフォルトが `main` になっていますが、`master` と `main` は**名前が違うだけで役割は同じ**です。どちらも「本流」となるブランチを指します。  
> コマンドの `master` の部分は、リポジトリによって `main` に読み替えてください。

---

## 目次

1. [リポジトリの初期化](#1-リポジトリの初期化)
2. [ファイルのステージングとコミット](#2-ファイルのステージングとコミット)
3. [ブランチ操作](#3-ブランチ操作)
4. [マージの種類](#4-マージの種類)
5. [クローン (clone)](#5-クローン-clone)
6. [リバート (revert)](#6-リバート-revert)
7. [フック (hooks)](#7-フック-hooks)
8. [その他よく使うコマンド](#8-その他よく使うコマンド)

---

## 1. リポジトリの初期化

```bash
git init
```

カレントディレクトリを git リポジトリとして初期化します。`.git` フォルダが作成され、バージョン管理が始まります。

---

## 2. ファイルのステージングとコミット

```bash
git add ファイル名      # 特定のファイルをステージング
git add .              # すべての変更をステージング
git commit -m "メッセージ"  # コミット（変更を記録）
```

### ステージングとは？

「コミットに含めるファイルを選ぶ準備」のことです。  
変更 → `git add`（ステージング） → `git commit`（記録）の流れで作業します。

```
作業ディレクトリ  →  ステージング  →  コミット履歴
 (編集した状態)    (git add した状態)  (git commit した状態)
```

---

## 3. ブランチ操作

```bash
git branch ブランチ名              # ブランチを作成
git checkout ブランチ名            # ブランチを切り替え
git checkout -b ブランチ名         # 作成と切り替えを同時に行う
git branch                        # ブランチ一覧を表示
```

### ブランチとは？

作業の「分岐点」です。本流（master）を壊さずに新機能の開発や修正を行えます。

```
master:   A --- B --- C
                 \
FeatureA:         D --- E
```

---

## 4. マージの種類

### Fast-forward マージ

master に変更がない状態でブランチをマージすると、ポインタを前に進めるだけです。

```bash
git merge FeatureA
```

```
マージ前:  master: A --- B
                        \
           FeatureA:     C --- D

マージ後:  master: A --- B --- C --- D  (FeatureA と同じ状態)
```

### 通常マージ（マージコミット）

master とブランチの両方に変更があると、2つの親を持つマージコミットが作られます。

```bash
git merge FeatureA
```

```
マージ前:  master:   A --- B --- C
                          \
           FeatureA:       D --- E

マージ後:  master:   A --- B --- C --- M  (M はマージコミット)
                          \         /
           FeatureA:       D --- E
```

### squash マージ

ブランチの複数コミットを1つにまとめて取り込みます。ブランチの履歴は master に残りません。

```bash
git merge --squash FeatureB
git commit -m "merge FeatureB"
```

---

## 5. クローン (clone)

```bash
git clone リポジトリのURL
git clone リポジトリのURL フォルダ名  # 保存先フォルダ名を指定
```

### クローンとは？

GitHub などのリモートリポジトリをローカルにコピーしてくる操作です。  
コピーと同時にリモートが `origin` として自動的に設定されます。

```bash
# 例
git clone https://github.com/TetsuSuzu/git-learn
cd git-learn
```

クローン後は以下のコマンドでリモートの最新状態を取得できます。

```bash
git pull origin master   # リモートの変更をローカルに取り込む
git push origin master   # ローカルの変更をリモートに送る
```

---

## 6. リバート (revert)

```bash
git revert コミットID
```

### リバートとは？

「指定したコミットの変更を打ち消す新しいコミット」を作る操作です。  
過去のコミットを**削除しない**ため、チームでの共同作業でも安全に使えます。

```bash
# コミット履歴を確認して ID をコピー
git log --oneline

# 例: abc1234 のコミットを打ち消す
git revert abc1234
```

```
リバート前:  A --- B --- C  (C の変更が問題)
リバート後:  A --- B --- C --- D  (D が C を打ち消すコミット)
```

### revert と reset の違い

| コマンド | 動作 | 安全性 |
|---|---|---|
| `git revert` | 打ち消すコミットを新たに追加 | 安全（履歴を消さない） |
| `git reset` | コミット履歴を巻き戻して削除 | 危険（プッシュ済みには使わない） |

---

## 7. フック (hooks)

### フックとは？

git の特定の操作（コミット・プッシュなど）の**前後に自動で実行されるスクリプト**です。  
`.git/hooks/` フォルダ内に配置します。

```
.git/
└── hooks/
    ├── pre-commit        ← コミット前に実行
    ├── commit-msg        ← コミットメッセージの検証
    ├── pre-push          ← プッシュ前に実行
    └── post-merge        ← マージ後に実行
```

### フックの作成例（コミット前にチェック）

`.git/hooks/pre-commit` ファイルを作成し、実行権限を付与します。

```bash
#!/bin/sh
# コミットメッセージに TODO が含まれていたら警告する例
echo "pre-commit フック実行中..."
```

```bash
# 実行権限を付与（Mac/Linux）
chmod +x .git/hooks/pre-commit
```

### 代表的なフックの種類

| フック名 | タイミング | 用途例 |
|---|---|---|
| `pre-commit` | `git commit` の直前 | コードの静的解析、テスト実行 |
| `commit-msg` | コミットメッセージ確定前 | メッセージの書式チェック |
| `pre-push` | `git push` の直前 | テストが通るか確認 |
| `post-merge` | `git merge` の完了後 | 依存パッケージの自動インストール |

---

## 8. その他よく使うコマンド

```bash
git status                    # 作業ディレクトリの状態を確認
git log --oneline             # コミット履歴を1行ずつ表示
git log --all --oneline --graph  # ブランチも含めてグラフ表示
git diff                      # 変更内容を確認
git stash                     # 作業中の変更を一時退避
git stash pop                 # 退避した変更を戻す
```

---

## このリポジトリで学んだこと

```
* 8618cfe merge FeatureB
| * 7db01c1 change2
| * 9d85f41 change1
|/
*   7c7d7d4 Merge branch 'FeatureA'
|\
| * 7361353 fix filec
* | fb5f696 change fileb
|/
* 2377e1e add filec
* 3de5cd5 unchange filea
* 13b4fe0 change filea
* 73229b8 This is first commit
```
