# grammardash

英語文法クイズアプリ「NextStage」。Point 008〜014 の問題を収録。

---

## 問題の追加

### 既存ポイントに問題を足す場合
1. `questions/pointXXX.json` を開く。
2. 既存と同じ形式でオブジェクトを追加する（`id` は `XXX-番号` でユニークに）。

### 新規ポイントを追加する場合
1. `questions/` に `point015.json` のように新規ファイルを作成する。
2. 中身は次の形式の **配列** にする。

**選択式（choice）の例:**
```json
{
  "id": "015-1",
  "point": "Point 015",
  "number": 1,
  "type": "choice",
  "question": "問題文（( ) に当てはまるものを選ぶ形式）",
  "choices": ["選択肢A", "選択肢B", "選択肢C", "選択肢D"],
  "answer": 0,
  "explanation": "解説文",
  "jp": "和訳"
}
```

**並び替え（reorder）の例:**
```json
{
  "id": "015-2",
  "point": "Point 015",
  "number": 2,
  "type": "reorder",
  "question": "問題文の ( ) 部分を並び替える",
  "tokens": ["単語1", "単語2", "単語3"],
  "answer": ["正解", "の", "並び"],
  "explanation": "解説",
  "jp": "和訳"
}
```

3. **index.html** の `QUESTION_FILES` 配列に `"questions/point015.json"` を追加する。

---

## 本体のバージョンアップ手順

1. **現在の index.html をバージョンとして保存**
   - 例: `index.html` を `index_v1_8.html` にコピー（次のバージョン番号で）。

2. **index.html で変更を加える**
   - 問題追加なら `QUESTION_FILES` の編集。
   - 機能変更なら該当箇所を編集。

3. **Git にコミット**
   - 必要に応じて `git add` → `git commit` → `git push`。

**バージョン履歴:** index_v1_0.html 〜 index_v1_7.html（index.html が常に最新版）。

---

## GitHub に連携して保存する手順

Dドライブのフォルダは「GitHub からダウンロードした最新版」のコピーで、今回の変更を GitHub に戻して保存したい場合の手順です。

### 前提
- **Git** がインストールされていること。未導入なら [Git for Windows](https://git-scm.com/download/win) をインストールする。
- GitHub に **grammardash** のリポジトリが既にあること（例: `https://github.com/あなたのユーザー名/grammardash`）。

---

### 方法A：いまの D ドライブのフォルダをそのまま GitHub に反映する（おすすめ）

1. **Git が使えるか確認**
   - コマンドプロンプトまたは PowerShell で `git --version` と入力。バージョンが表示されれば OK。

2. **D ドライブのフォルダを Git の管理下にする**
   - エクスプローラーで `d:\toshi\grammardash-main` を開く。
   - フォルダ内で **Shift + 右クリック** → **「PowerShell ウィンドウをここに開く」**（または「ターミナルを開く」）。
   - 次のコマンドを順に実行する：
   ```powershell
   git init
   git remote add origin https://github.com/あなたのユーザー名/grammardash.git
   ```
   （`あなたのユーザー名/grammardash` は、実際の GitHub のリポジトリ URL に置き換える。）

3. **変更を追加してコミット**
   ```powershell
   git add .
   git status
   git commit -m "Point 012-014 追加、v1_7 保存、README 更新"
   ```

4. **GitHub にプッシュ**
   - **「空」とは**：GitHub でリポジトリを「New」で作った直後で、まだ一度も `git push` していない状態。画面上も「Quick setup」や「…or push an existing repository」と出ている段階。
   - **「既にコミットがある」とは**：過去に別の PC や別のフォルダから push したことがある、またはリポジトリ作成時に「Add a README file」などにチェックを入れて最初のコミットができている状態。
   - リポジトリが **空** の場合（まだ何も push していない）：
     ```powershell
     git branch -M main
     git push -u origin main
     ```
   - リポジトリに **既にコミットがある**場合（「unrelated histories」と出る場合）：
     ```powershell
   git branch -M main
   git pull origin main --allow-unrelated-histories
   ```
     競合が出たらメッセージに従って解消してから：
     ```powershell
   git push -u origin main
   ```

5. **GitHub のページ**（`https://github.com/あなたのユーザー名/grammardash`）を開き、ファイルが反映されているか確認する。

---

### 方法B：GitHub を clone したフォルダで作業する

1. **作業用フォルダを決める**（例: `d:\work`）。
2. そのフォルダで **GitHub のリポジトリを clone**：
   ```powershell
   cd d:\work
   git clone https://github.com/あなたのユーザー名/grammardash.git
   cd grammardash
   ```
3. **D ドライブの「今の最新」で上書き**
   - `d:\toshi\grammardash-main` の中身（すべてのファイル）を、`d:\work\grammardash` の中にコピーして上書きする。
4. **変更をコミットして push**：
   ```powershell
   git add .
   git status
   git commit -m "Point 012-014 追加、v1_7 保存、README 更新"
   git push origin main
   ```

---

### よくあるエラー

**「fatal: detected dubious ownership in repository」と出る場合**

- Dドライブなど、別ドライブや別ユーザー由来のフォルダで `git` を実行すると、Git が「所有権が不明」と判断してこのエラーを出すことがあります。
- **対処**：次のコマンドを **1回だけ** 実行して、このフォルダを「安全なディレクトリ」として登録する。
  ```powershell
  git config --global --add safe.directory D:/toshi/grammardash-main
  ```
  （パスは実際のフォルダパスに合わせる。`d:\toshi\grammardash-main` の場合は上記の通りで OK。）
- 実行後、もう一度 `git status` や `git add .` などを試す。

---

### 補足
- **ユーザー名・リポジトリ名**は、GitHub のリポジトリページの URL で確認できる（`https://github.com/ユーザー名/リポジトリ名`）。
- 初回の `git push` で **ログイン**を求められたら、GitHub のユーザー名と **Personal Access Token（PAT）** を入力する（パスワードは使えない場合があります）。Token は GitHub → 設定 → Developer settings → Personal access tokens で発行できる。
- 今後は「変更したら `git add .` → `git commit -m "説明"` → `git push`」の流れで GitHub に保存できる。