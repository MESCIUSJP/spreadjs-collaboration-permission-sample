# SpreadJS 共同編集 権限制御サンプル

SpreadJS の共同編集機能で、ユーザーごとに **編集／閲覧** の権限を切り替えるサンプルです。
1 つの画面に 3 つの iframe を並べ、それぞれ異なる権限のユーザーとして同じワークブックに同時接続します。

| ユーザー | モード | できること |
|---|---|---|
| User1 | `edit` | セルの編集を含むすべての操作 |
| User2 | `view` | 閲覧のみ |
| User3 | `view_allowFilterAndSort` | 閲覧に加えてフィルターと並べ替え |

## 権限制御のしくみ

- **クライアント側**（[public/client.js](public/client.js)）
  `bindPresence` に渡すユーザー情報の `permission` で、`BrowsingMode`（`edit` / `view`）と、閲覧モードで許可する操作（`PermissionTypes.allowFilter | allowSort`）を指定します。
- **サーバー側**（[server.js](server.js)）
  - `connect` ミドルウェアで、接続時のトークンからユーザーを特定し、接続のタグに保存します。
  - `submit` ミドルウェアで、閲覧モード（`view*`）のユーザーから送られた Op を拒否します。クライアント側の制限をすり抜けた変更も、ここで防げます。

> ユーザー情報やトークンはサンプル用のモックです。実際のアプリケーションでは、認証基盤と連携してください。

## 必要な環境

- Node.js
- SpreadJS 共同編集機能（`@mescius/spread-sheets-collaboration` など）

## 実行方法

```bash
npm install
npm run build   # public/client.js と public/container.js をバンドル
npm start
```

ブラウザで <http://127.0.0.1:8080/iframe.html> を開きます。

## ライセンスキー

localhost で動かす場合、ライセンスキーは不要です（評価版として動作します）。
それ以外の環境で動かす場合は、次の 2 か所のコメントを外し、ライセンスキーを設定してから再ビルドしてください。

- クライアント：[public/client.js](public/client.js) の `GC.Spread.Sheets.LicenseKey`
- サーバー：[server.js](server.js) の `server.LicenseKey`

> **注意**：ライセンスキーを設定したままコミットしないでください。
> ビルド出力の `public/index.js` と `public/iframe.js` にはキーが埋め込まれるため、`.gitignore` で除外しています。

## ファイル構成

```
├── server.js            # Express と共同編集サーバー（認証・権限検証）
├── webpack.config.js
└── public/
    ├── iframe.html      # 3 つの iframe を並べたエントリーページ
    ├── container.js     # iframe のリロード処理（→ iframe.js にビルド）
    ├── index.html       # 各 iframe に表示する SpreadJS のページ
    └── client.js        # SpreadJS と共同編集クライアント（→ index.js にビルド）
```

## 参考

- [SpreadJS 共同編集 | 製品ヘルプ](https://demo.mescius.jp/spreadjs/docs/spreadjs-collaboration-server)
