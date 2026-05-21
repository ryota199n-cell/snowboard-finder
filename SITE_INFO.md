# Snowboard Finder Japan — サイト情報・運用ガイド

---

## 1. サイト概要

| 項目 | 内容 |
|------|------|
| サイト名 | Snowboard Finder Japan |
| URL | https://snowboardfinderjapan.netlify.app |
| 管理画面 | https://snowboardfinderjapan.netlify.app/admin.html（PW: snow2025） |
| GitHub | https://github.com/ryota199n-cell/snowboard-finder |
| ホスティング | Netlify（GitHubへのプッシュで自動デプロイ） |
| 構成 | 静的HTML + バニラJS（フレームワーク不使用） |

**目的**: スノーボードの板選びに迷う初心者〜中級者向けのレコメンドエンジン。レベル・スタイル・フレックス・身長・体重を入力すると最適な板を提案し、楽天アフィリエイトへ誘導する。

---

## 2. 技術構成

```
snowboard-finder/
├── index.html          # メインサイト（全コードが1ファイルに集約）
├── admin.html          # 管理者画面（板の追加・削除UI）
└── .claude/
    ├── commands/
    │   └── add-board.md        # Claude Code スラッシュコマンド
    └── project-instructions.md # Claude.aiプロジェクト指示文
```

### index.html の内部構造

| セクション | 内容 |
|-----------|------|
| `RAKUTEN {}` | 楽天アフィリエイトURLマップ `{ ブランド: { モデル: URL } }` |
| `BRAND_COLORS {}` | ブランドごとのSVGカラー定義 |
| `const DB = [...]` | 板データベース（46モデル） |
| localStorageマージ処理 | `customBoards` キーから追加板を読み込みDBに結合 |
| `scoreBoard()` | レコメンドエンジン本体 |
| `renderResults()` | 検索結果のHTMLレンダリング |

### スコアリング（scoreBoard関数）

| 要素 | 最大スコア |
|------|-----------|
| フレックス一致度 | 50pt（完全一致50、±1で38、±2で24…） |
| スタイル一致 | 1スタイルあたり最大33pt |
| レベル一致 | 20pt |
| 長さ適合 | 15pt |
| 体重適合 | 10pt |

---

## 3. 板データベース

現在 **46モデル** を収録。

| ブランド | モデル数 | 備考 |
|---------|---------|------|
| Burton | 6 | Process, Custom, Custom Flying V, Deep Thinker, Hometown Hero, Feelgood |
| Nitro | 3 | Team, Beast, Cheap Thrills |
| Lib Tech | 3 | Travis Rice Pro, Skate Banana, T.Rice Orca |
| K2 | 3 | Manifest, Afterblack, Dreamsicle（女性向け） |
| FNTC | 3 | TNT C, TNT R, CAT |
| Capita | 3 | DOA, Mercury（他1） |
| YONEX | 2 | ACHSE, NANOACE |
| Salomon | 2 | Assassin, Huck Knife |
| Rossignol | 2 | XV Sashimi LF, One LF |
| Rome SDS | 2 | Marshal, Ravine |
| OGASAKA | 2 | CT, FC |
| NOVEMBER | 2 | ARTISTE, ICECAT |
| Jones | 2 | Storm Chaser, Mountain Twin |
| Bataleon | 2 | Global Warmer, Party Wave |
| Arbor | 2 | Formula Rocker, Westmark Rocker |
| その他 | 各1 | GNU, Never Summer, GENTEMSTICK, WRX SB, RICE28, SCOOTER, 011artistic |

### 板オブジェクトの構造

```js
{
  brand: "Nitro",           // ブランド名（RAKUTENオブジェクトのキーと一致させること）
  model: "Team",            // モデル名
  isJP: false,              // 日本ブランドか
  isRak: false,             // 楽天URLありか（rakutenUrlがあれば自動でtrue）
  isWomens: false,          // 女性向けか
  seasons: ["25-26","26-27"],
  levels: ["intermediate","advanced"],  // beginner/intermediate/advanced/expert
  styles: ["freestyle","park","gratri"],  // all-mountain/freestyle/powder/carving/park/gratri
  flex: 6,                  // 1（超ソフト）〜10（超ハード）
  shape: "ツイン",
  profile: "キャンバー",
  lengths: [148,151,154,157,160],
  price: "¥84,000〜99,000",
  highlights: ["特徴1","特徴2","特徴3"],
  url: "https://...",       // ブランド公式サイト
  rakutenUrl: "",           // 楽天アフィリエイトURL（空文字可）
  _custom: true,            // ← カスタム追加板のみ付与
  _id: 1234567890,          // ← カスタム追加板のみ付与（Date.now()）
}
```

---

## 4. 楽天アフィリエイト連携

楽天URLは `RAKUTEN` オブジェクトで管理（index.html内 680行付近）：

```js
const RAKUTEN = {
  "Burton": {
    "Process": "https://hb.afl.rakuten.co.jp/...",
    "Custom":  "https://hb.afl.rakuten.co.jp/...",
  },
  // ...
};
```

- `isRak: true` の板にのみ「楽天で見る」ボタンが表示される
- カスタム追加板は `rakutenUrl` に値があれば自動で `isRak: true` になる
- 新しい楽天URLを追加する場合：楽天アフィリエイトの管理画面でURLを発行し、`RAKUTEN` オブジェクトに追記してGitHubにプッシュ

---

## 5. 板の追加方法（3通り）

### 方法A: Claude Code スラッシュコマンド（PC推奨）

snowboard-finderフォルダをClaude Codeで開いた状態で：

```
/add-board
```

→ ブランドとモデルを聞いてくる → Webで仕様を自動調査 → ブラウザコンソール用コードを生成

### 方法B: Claude.ai アプリ（スマホ・PC）

「Snowboard Finder」プロジェクトのチャットで：

```
ファインダーにNitro Teamを追加したい
```

と送るだけで方法Aと同じ動作をする。
（プロジェクト指示の設定が必要。次項参照）

### 方法C: 管理画面から手動入力

https://snowboardfinderjapan.netlify.app/admin.html にアクセス → PW: `snow2025` → フォームに入力

---

## 6. Claude.ai プロジェクトの設定

スマホのClaude.aiアプリで方法Bを使うための設定：

1. Claude.ai を開く
2. 左メニュー **「プロジェクト」→「新しいプロジェクト」**
3. プロジェクト名: `Snowboard Finder`
4. **「プロジェクト指示を追加」** をタップ
5. `.claude/project-instructions.md` の内容を貼り付けて保存

以後、そのプロジェクト内で「板を追加したい」と言うだけで動作する。

---

## 7. コードの修正・デプロイ方法

```bash
# 板データを直接編集する場合
# index.html の const DB = [...] 内に板オブジェクトを追記

# 楽天URLを追加する場合
# index.html の const RAKUTEN = {...} 内に追記

# 変更をデプロイ
git add index.html
git commit -m "板を追加: ブランド モデル"
git push origin main
# → Netlifyが自動でデプロイ（1〜2分で反映）
```

---

## 8. 注意事項

- **localStorageはブラウザ固有** — 管理画面・コンソールから追加した板は、追加したブラウザのみに保存される。全訪問者に見せたい場合は `index.html` の `DB` に直接追記してGitHubにプッシュすること
- **RAKUTENオブジェクトのキー** は `brand` フィールドと完全一致が必要（大文字小文字・スペースも含む）
- **`desc` フィールドは不使用** — 説明文は `highlights` 配列（最大3項目）で管理
