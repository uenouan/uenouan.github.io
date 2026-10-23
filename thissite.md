このサイトはGithub Pagesで公開している。
日々のメンテナンスはVS Codeを使い、Markdown形式で記事を書いてGitにプッシュしてアップデートする。
MarkdownをHTMLに変換する仕組みにはDocsifyを使用している。（Github Pages標準のJekyllは少し面倒なので使っていない）

以下はGeminiにまとめてもらった備忘録。

---

# docsify ＋ GitHub Pages 導入ガイド

VS Code（WSL）とGit環境をベースに、複雑なビルド（SSG）なしでMarkdownをブラウザで綺麗に閲覧・公開するための手順です。

## 1. 全体像とメリット

* **仕組み**: ブラウザ側でMarkdownを直接読み込んでレンダリングするため、静的サイトジェネレーターのようなビルド待ち時間が不要。
* **ワークフロー**: いつものようにVS CodeでMarkdownを書き、Gitでプッシュするだけで即座に反映される。

---

## 2. リポジトリのファイル構成

リポジトリのルートディレクトリに、以下のような構成でファイルを配置します。

```text
my-repo/
├── index.html          # docsifyの本体・設定ファイル
├── _sidebar.md         # 左側のサイドバー（目次）
├── _navbar.md          # 右上のナビゲーションバー
├── _coverpage.md       # トップのカバーページ（オプション）
├── .nojekyll           # GitHub PagesのJekyll処理を無効化する空ファイル
├── README.md           # トップページ（ホーム）
└── content/            # サブフォルダ（必要に応じてMarkdownを配置）
    └── log/
        └── 2026-10.md

```

> **⚠️ 重要（ハマりポイント）**
> GitHub Pagesはデフォルトで `_`（アンダースコア）から始まるファイルをビルド対象外（無視）にする仕様があります。そのため、**ルートに中身が空の `.nojekyll` ファイルを必ず置いてください**。

---

## 3. 各設定ファイルのコード

### ① `index.html` （すべて入り構成）

リポジトリのルートに作成します。検索機能や目次プラグイン、コードコピー機能を含んだコードです。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>My Notes</title>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify-plugin-toc@1.3.1/dist/light.css">
</head>
<body>
  <div id="app"></div>

  <script>
    window.$docsify = {
      name: '📝 My Notes',
      repo: 'https://github.com/ユーザー名/リポジトリ名',
      loadNavbar: true,    // 右上のナビゲーションバー (_navbar.md)
      loadSidebar: true,   // 左側のサイドバー (_sidebar.md)
      subMaxLevel: 3,      // サイドバーに自動表示する記事内見出しの深さ (H2〜H3)
      coverpage: true,     // カバーページ / 表紙バナー (_coverpage.md)
      onlyCover: false,    // カバーページを下スクロールでそのまま本文へ繋げる
      auto2top: true,      // ページ遷移時に自動で最上部にスクロール
      search: {
        maxAge: 86400000,
        paths: 'auto',
        placeholder: '🔍 全文検索...',
        noData: '該当するノートが見つかりません',
        depth: 6
      },
      toc: {
        scope: '.markdown-section',
        headings: 'h2, h3, h4',
        title: '目次',
      },
      copyCode: {
        buttonText: 'コピー',
        errorText: 'エラー',
        successText: 'コピー完了'
      }
    }
  </script>

  <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify-copy-code/dist/docsify-copy-code.min.js"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/zoom-image.min.js"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify-plugin-toc@1.3.1/dist/docsify-plugin-toc.min.js"></script>
</body>
</html>

```

### ② `_sidebar.md` （左側メニュー）

リポジトリのルートに作成し、表示したいファイルやフォルダのリンクを記述します。

```markdown
* 🏠 [ホーム](README.md)

* 📝 ライフログ
  * [10月の記録](content/log/2026-10.md)

```

### ③ `_navbar.md` （右上メニュー）

リポジトリのルートに作成します。

```markdown
* 🚀 リンク
  * [GitHub](https://github.com)

```

### ④ `.nojekyll`

* ルートに作成する**中身が空のファイル**（拡張子なし）。
* これにより `_sidebar.md` などのファイルが正しくGitHub Pagesに認識されるようになります。

---

## 4. デプロイと公開のフロー

1. VS Code（WSL）でファイルを編集・作成する。
2. Gitでステージング・コミット・プッシュを行う。
```bash
git add .
git commit -m "Update docsify notes"
git push origin main

```


3. GitHub Pagesの設定（Settings > Pages）で公開ブランチが指定されていれば、数秒〜数分でWebサイトに反映されます。

---