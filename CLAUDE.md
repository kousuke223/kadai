# 課題リマインダー

大学の課題と締切を管理する、1ファイル完結のWebアプリ。黒板＋キャンパスノート風のデザイン。

## 構成

- `index.html` — マークアップ・CSS・JavaScriptをすべて含む単一ファイル。ビルド不要で、ブラウザで開けば動く。
- フレームワークなし（素のHTML/CSS/JS）。外部読み込みはGoogle Fonts（Zen Kaku Gothic New / DM Mono）のみ。
- アイコンはLucide相当のSVGパスを `ICONS` 定数に直接持っている。

## 画面

1. 黒板ヘッダー：今日の日付、「今日締切」「残りの課題」の件数
2. 付箋タブ（未完了／すべて／完了済み）と道具フィルター（全種類／PC／ノート／参考書）
3. ノート紙面：課題の行。タイトルをタップか消しゴムボタンで完了、ゴミ箱は2回タップで削除
4. 右下のペンボタン → 課題を書き込むモーダル

## データ

課題1件の形：

```js
{
  id: "t_xxxx",            // newId() で生成
  title: "課題の内容",
  due: "2026-11-20",        // 締切日（ローカル日付の YYYY-MM-DD）
  type: "pc",               // "pc" | "notebook" | "book"
  highlighter: "none",      // "none" | "pink" | "yellow" | "blue"
  done: false,
  doneAt: null,             // 完了時刻（ISO文字列）
  createdAt: "…",           // 作成時刻（ISO文字列）
  example: false            // true だと「例」バッジが付く
}
```

読み込んだデータは必ず `norm()` を通して上の形にそろえる。

## 保存の仕組み

保存まわりは `put()` / `patch()` / `remove()` の3関数に集約してある。保存先を変えるときはここだけ直せばよい。

- **このファイル単体で開いたとき**：`localStorage`（キー `kadai-reminder-tasks`）に保存。その端末のブラウザだけに残る。
- **Claudeのアーティファクトとして開いたとき**：`window.claude.use("db")` が使えるので、`tasks` コレクションに保存する（`mode === "db"` の分岐）。Claude外で動かすだけなら、この分岐と `init()` 内の `claude.use` 部分は消してよい。

## 注意点

- 日付は必ずローカル時刻で扱う（`isoDate()`）。`toISOString()` はUTCなので、日本の深夜0〜9時に日付が1日ずれる。
- ユーザー入力は `textContent` 経由で表示している（`h()` ヘルパー）。`innerHTML` に入力値を入れないこと。
- 色・余白は `:root` のCSS変数にまとめてある。ライトテーマ固定のデザイン。
- スマホ幅（約400px）で横スクロールが出ないことを確認する。

## 公開

GitHub Pages で公開している：https://kousuke223.github.io/kadai/

リポジトリは `kousuke223/kadai`。`main` ブランチに push すると1〜2分で反映される。

## やりたいことの候補（未着手）

- 締切前の通知（PWA化してプッシュ通知、など）
- 課題の編集機能
- 端末間の同期（バックエンドを用意する場合は `put/patch/remove` を差し替える）
