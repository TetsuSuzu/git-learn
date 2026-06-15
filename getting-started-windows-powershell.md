# はじめての Git（Windows PowerShell・超入門）

パソコン初心者でも進められるように、**Windows の PowerShell**を使って
「フォルダを作る → メモ帳でファイルを作る → Git で記録する」までを**1ステップずつ**説明します。

> この資料の対象：Git も PowerShell も初めての方。コマンドは**1行ずつコピペ**して `Enter` で実行してください。

---

## 0. 準備（最初の1回だけ）

### PowerShell を開く
- スタートメニューで **「PowerShell」** と入力して **Windows PowerShell** を起動します。
- 黒い（または青い）画面が出れば準備OK。ここに**コマンドを打ち込みます**。

### Git が入っているか確認
```powershell
git --version
```
- `git version 2.xx.x` のように表示されればOK。
- 「認識されていません」と出たら、[Git for Windows](https://git-scm.com/download/win) をインストールしてください。

### 名前とメールを登録（最初の1回だけ／コミットの署名に使われます）
```powershell
git config --global user.name "あなたの名前"
git config --global user.email "you@example.com"
```

---

## 1. 作業フォルダを作る

デスクトップに練習用フォルダ `git-practice` を作って、その中へ移動します。

```powershell
cd $HOME\Desktop              # デスクトップへ移動
mkdir git-practice            # フォルダを新規作成
cd git-practice               # 作ったフォルダの中へ入る
```

- `cd` = フォルダを移動する（Change Directory）
- `mkdir` = フォルダを作る（Make Directory）
- いま自分がどこにいるか確認したいときは `pwd` と打つと現在地が出ます。

---

## 2. リポジトリの初期化（git init）

このフォルダを「Gitで管理する場所」にします。

```powershell
git init
```

- `.git` という**隠しフォルダ**が作られ、ここからバージョン管理が始まります。
- 確認（隠しフォルダも表示）:
  ```powershell
  Get-ChildItem -Force
  ```
  一覧に `.git` があれば成功です。

---

## 3. メモ帳でファイルを作る

### 方法A：メモ帳（Notepad）で作る ← 初心者おすすめ
```powershell
notepad memo.txt
```
1. `memo.txt` は存在しないので、メモ帳が **「新しいファイルを作成しますか?」** と聞いてきます → **「はい」**
2. メモ帳が開くので、下の「ファイルの中身の例題」を入力
3. **`Ctrl + S`** で保存 → メモ帳を閉じる

### 方法B：コマンドだけで中身ごと作る（メモ帳を開かない）
日本語が文字化けしないよう **UTF-8** で保存します。
```powershell
Set-Content -Path memo.txt -Encoding utf8 -Value @"
私のはじめての Git メモ
- これは練習用のファイルです
- Git でバージョン管理を学んでいます
"@
```
- `@" ... "@` は**複数行のまとめ書き**（ヒアストリング）です。

### ファイルの中身の例題（memo.txt）
```text
私のはじめての Git メモ
- これは練習用のファイルです
- Git でバージョン管理を学んでいます
```

### 中身を確認
```powershell
Get-Content memo.txt
```
入力した内容が表示されればOK（`Get-Content` = ファイルの中身を表示）。

---

## 4. 状態を確認する（git status）

```powershell
git status
```
- `Untracked files:` の下に **memo.txt** が赤字で出ます。
- これは「**まだGitに記録されていない新しいファイルがあるよ**」という意味です。

---

## 5. ステージングする（git add）

```powershell
git add memo.txt        # この1ファイルを記録の準備に入れる
# git add .             # （全部の変更をまとめて入れたいとき）
```

### ステージングって何？（イメージ）
**「コミット（記録）に含めるものを、段ボール箱にいったん入れる作業」**です。

```
編集したファイル   →   git add（箱に入れる）   →   git commit（箱を封して保管）
  作業ディレクトリ        ステージング                コミット履歴
```

もう一度 `git status` を打つと、memo.txt が**緑字**に変わります（＝箱に入った状態）。

---

## 6. コミットする（git commit）

箱を封して「この時点の状態」を記録します。

```powershell
git commit -m "最初のメモを追加"
```
- `-m` のあとの `" "` が**コミットメッセージ**（何をしたかのメモ）。
- `1 file changed, 3 insertions(+)` のように出れば成功です。

---

## 7. 記録を確認する（git log）

```powershell
git log --oneline
```
- `xxxxxxx 最初のメモを追加` のように、いま作ったコミットが1行で表示されます。

---

## 8. 2回目（変更 → 記録）の流れ

ファイルを少し変えて、もう一度記録してみましょう。

```powershell
# 行を1行追加する（>> は「追記」。> だと上書きなので注意）
Add-Content -Path memo.txt -Encoding utf8 -Value "- 2回目の変更を試しています"

git status          # 変更ありと表示される（modified）
git add memo.txt    # 箱に入れる
git commit -m "メモに1行追加"
git log --oneline   # コミットが2つに増える
```

---

## まとめ（毎回の基本サイクル）

```
1. ファイルを編集（メモ帳など）
        ↓
2. git status   … 何が変わったか確認
        ↓
3. git add      … 記録するものを選ぶ（ステージング）
        ↓
4. git commit -m "メッセージ"   … 記録する
        ↓
5. git log      … 記録を確認
```

| コマンド | 役割 |
|---|---|
| `git init` | フォルダをGit管理下にする（最初の1回） |
| `notepad ファイル名` | メモ帳でファイルを作る/編集する |
| `git status` | いまの状態を確認 |
| `git add ファイル名` | 記録の準備（ステージング） |
| `git commit -m "メモ"` | 変更を記録 |
| `git log --oneline` | 記録の履歴を表示 |

> この先の「ブランチ」「マージ」「クローン」などは [README.md](README.md) を参照してください。
