# 公開手順

## 採用構成

- ソース管理：GitHub Organization `TOFU-BITS` の公開リポジトリ `tofubits-web`
- 公開：Cloudflare Pages
- 正規URL：`https://tofubits.com`
- `https://www.tofubits.com/*` は同じパスの `https://tofubits.com/*` へ301転送

ドメインとDNSがすでにCloudflareにあるため、GitHub Pagesを経由するより、Cloudflare Pagesへ直接つなぐ構成がシンプルです。GitHubへのpushごとに自動公開されます。

## 1. GitHubリポジトリを作る

1. GitHubの `TOFU-BITS` Organizationを開く
2. **New repository** を選ぶ
3. Repository nameを `tofubits-web` にする
4. Visibilityは **Public**
5. README等の初期ファイルは追加せず、**Create repository**
6. GitHubが表示する「push an existing repository from the command line」の手順で、このフォルダをpushする

## 2. Cloudflare Pagesに接続する

1. Cloudflare Dashboardで **Workers & Pages** を開く
2. **Create application → Pages → Connect to Git**
3. GitHubを接続し、`TOFU-BITS/tofubits-web` を選ぶ
4. Project nameは `tofubits-web`
5. Production branchは `main`
6. Framework presetは **None**
7. Build commandは空欄
8. Build output directoryは `/`（空欄を許す画面では空欄でも可）
9. **Save and Deploy**

`https://tofubits-web.pages.dev` でトップと `/privacy/` が表示されることを確認します。

## 3. 独自ドメインをつなぐ

1. Pagesの `tofubits-web` プロジェクトで **Custom domains** を開く
2. **Set up a domain** を選び、`tofubits.com` を入力
3. **Activate domain** まで進める
4. 同じ操作で `www.tofubits.com` も追加する

Cloudflare管理下のドメインなので、必要なDNSレコードとTLS証明書は通常自動で設定されます。手動で先にCNAMEを作らず、必ずPagesのCustom domains画面から追加します。

## 4. wwwをルートドメインへ寄せる

1. Cloudflare Dashboardの **Rules → Redirect Rules** を開く
2. **Create rule → Redirect Rule**
3. Rule nameを `Redirect www to apex` にする
4. Custom filter expressionで Hostname equals `www.tofubits.com`
5. Dynamic redirectを選び、次を指定する
   - Expression: `concat("https://tofubits.com", http.request.uri.path)`
   - Status code: `301`
   - Preserve query string: On
6. 保存して有効化する

## 5. 最終確認

- `https://tofubits.com` が表示され、鍵マークが出る
- `https://tofubits.com/privacy/` が表示される
- `https://www.tofubits.com/privacy/` が `https://tofubits.com/privacy/` に移動する
- GitHub、Instagram、Threads、メールの各リンクが開く
- iPhone幅で横にはみ出さず、文字が読みやすい

## 更新方法

`main` ブランチへpushするとCloudflare Pagesが自動で再公開します。アプリ公開時は `index.html` の該当カードをリンクに変更し、ステータス文言も更新します。
