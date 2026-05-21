スノーボードの板をSnowboard Finderサイトに追加するためのスキルです。

## あなたの役割

ユーザーが板を追加したいと言っています。以下の手順で進めてください。

---

## Step 1: ブランドとモデルを確認する

`$ARGUMENTS` が空の場合、まずユーザーに質問してください：

> どのブランドとモデルを追加しますか？（例: Nitro Team、Burton Custom、K2 Manifest）

ブランドとモデル名が確定したら Step 2 に進みます。

---

## Step 2: インターネットで仕様を調べる

WebSearch と WebFetch を使って、以下の情報を日本語・英語で調べてください。
公式サイト・スノーボード専門店・レビューサイトなどを参照してください。

調べる項目：
- **フレックス**: メーカー表記（Soft/Medium/Stiff など）を 1〜10 に変換。Soft≒3、Medium≒5、Medium-Stiff≒7、Stiff≒8、Very Stiff≒9 を目安に
- **ライディングスタイル**: 以下から複数選択
  - `all-mountain`（オールマウンテン）
  - `freestyle`（フリースタイル）
  - `powder`（パウダー）
  - `carving`（カービング）
  - `park`（パーク・ジブ）
  - `gratri`（グラトリ）
- **推奨レベル**: 以下から複数選択
  - `beginner`（初心者）
  - `intermediate`（中級者）
  - `advanced`（上級者）
  - `expert`（エキスパート）
- **シェイプ**: ツイン / ディレクショナルツイン / ディレクショナル など（日本語で）
- **プロファイル**: キャンバー / ロッカー / フラット / ハイブリッドキャンバー / ハイブリッドロッカー など（日本語で）
- **価格帯**: 日本円（例: `¥85,000〜100,000`）。日本の販売価格を優先、なければUSドルから換算（1ドル≒150円）
- **対応サイズ**: 長さのリスト（cm単位の整数、例: `[148,151,154,157,160]`）
- **ハイライト3点**: 板の特徴を日本語で端的に（例: `["パーク特化モデル","キレのあるキャンバー","スタイル系ライダーに人気"]`）
- **ブランド公式URL**: 日本語ページがあれば優先
- **日本ブランドか**: true / false
- **女性向けか**: true / false

---

## Step 3: 調べた情報を表示する

以下の形式でまとめて表示してください：

```
📋 [ブランド] [モデル] の仕様

フレックス    : X / 10
スタイル      : ○○・○○
推奨レベル    : ○○・○○
シェイプ      : ○○
プロファイル  : ○○
価格帯        : ¥○○,000〜○○,000
サイズ        : 148, 151, 154...
ハイライト    :
  ① ○○
  ② ○○
  ③ ○○
公式URL       : https://...
```

「情報が見つからなかった項目」があれば正直に伝えてください。

---

## Step 4: ブラウザコンソール用のコードを生成する

以下のフォーマットで JavaScript コードを生成し、コードブロックで表示してください。
`...` の部分は調べた情報で埋めてください。

```js
// ① https://snowboardfinderjapan.netlify.app を開く
// ② F12 → Console タブ → 以下を貼り付けて Enter

(function(){
  const b = {
    brand: "ブランド名",
    model: "モデル名",
    flex: フレックス数値,
    styles: ["スタイル1","スタイル2"],
    levels: ["レベル1","レベル2"],
    isJP: false,
    isRak: false,
    isWomens: false,
    seasons: ["25-26","26-27"],
    shape: "シェイプ",
    profile: "プロファイル",
    price: "¥○○,000〜○○,000",
    lengths: [148,151,154],
    highlights: ["ハイライト1","ハイライト2","ハイライト3"],
    url: "https://...",
    rakutenUrl: "",
    _custom: true,
    _id: Date.now(),
  };
  const arr = JSON.parse(localStorage.getItem('customBoards') || '[]');
  if(arr.find(x=>x.brand===b.brand&&x.model===b.model)){
    console.warn('すでに登録済みです:', b.brand, b.model); return;
  }
  arr.push(b);
  localStorage.setItem('customBoards', JSON.stringify(arr));
  console.log('✅ 追加完了:', b.brand, b.model, '— ページをリロードすると検索結果に表示されます');
})();
```

---

## Step 5: 補足を伝える

最後に以下を伝えてください：

- 楽天アフィリエイトURLは後から `admin.html` の管理画面で追加できます（既存の板を一度削除して再登録）
- コードを実行後、ページをリロードすると検索結果に表示されます
- 情報が正確でない場合は「フレックスは○に修正して」と言えば修正します
