このサイトはGithub Pagesで公開している。  
日々のメンテナンスはVS Codeを使い、Markdown形式で記事を書いてGitにプッシュしてアップデートする。  
MarkdownをHTMLに変換する仕組みにはDocsifyを使用している。（Github Pages標準のJekyllは少し面倒なので使っていない）  
以下はGeminiにまとめてもらった備忘録。

---
<!-- toc -->

# Docsify ＋ GitHub Pages 導入ガイド

VS CodeとGit環境をベースに、複雑なビルド（SSG）なしでMarkdownをブラウザで綺麗に閲覧・公開するための手順です。

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
    ├── agri/
    |   └── xxxxx.md
    └── other/
        └── xxxxx.md
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
  <meta charset="utf-8">
  <title>兵庫県で農家になる</title>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <!-- <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0"> -->
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">

  <!-- <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css"> -->
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@5/dist/themes/core.min.css">

  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap" rel="stylesheet">
  <link href="https://fonts.googleapis.com/css2?family=Sawarabi+Mincho&display=swap" rel="stylesheet">
  <link href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@400;700&display=swap" rel="stylesheet">
  <link href="https://fonts.googleapis.com/css2?family=BIZ+UDPGothic:wght@400;700&display=swap" rel="stylesheet">

  <style>
    /* カバーページの背景画像を不透明度（透過）付きで表示 */
    .cover.show {
      background: linear-gradient(to bottom, rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.6)),
        url('media/cover.png') center center / cover no-repeat !important;
    }

    /* サイト全体のデフォルトフォントを指定 */
    :root {
      /* --base-font-family: 'Noto Sans JP', sans-serif, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto; */
      /* --base-font-family: 'M PLUS Rounded 1c', sans-serif, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto;       */
      --base-font-family: 'BIZ UDPGothic', sans-serif !important;
    }

    html, body, article, main, section, div, p, span, li, a, h1, h2, h3, h4, h5, h6, .markdown-section {
      font-family: var(--base-font-family) !important;
      text-decoration: none !important;
      border-bottom: none !important;
      box-shadow: none !important;
      background-image: none !important;
    }

    /* カバーページ */
    section.cover h1,
    section.cover h2,
    section.cover h3,
    section.cover p,
    section.cover a {
      /* font-family: var(--base-font-family) !important; */
      color: #ffffff !important;
    }

    section.cover h1 {
      font-weight: 700;
    }
  </style>
</head>

<body>
  <div id="app"></div>

  <script>
    window.$docsify = {
      // name: 'My Notes',
      // repo: 'https://github.com/__USER__/__REPOSITORY__', // 右上にGitHubリボンを表示
      loadNavbar: true,    // 右上のナビゲーションバー (_navbar.md)
      loadSidebar: true,   // 左側のサイドバー (_sidebar.md)
      subMaxLevel: 0,      // サイドバーに自動表示する記事内見出しの深さ (H2〜H3)
      coverpage: true,     // カバーページ / 表紙バナー (_coverpage.md)
      onlyCover: false,    // カバーページから本文にスクロールしない
      auto2top: true,      // ページ遷移時に自動で最上部にスクロール

      copyCode: {
        buttonText: 'Copy',
        errorText: 'Error',
        successText: 'Copied!'
      }
    }
  </script>

  <script src="//cdn.jsdelivr.net/npm/docsify@5/dist/docsify.min.js"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify-copy-code/dist/docsify-copy-code.min.js"></script>

</body>
</html>

```

### ② `_sidebar.md` （左側メニュー）

リポジトリのルートに作成し、表示したいファイルやフォルダのリンクを記述します。

```markdown
* [ホーム](README.md)

* 農業記録
  * [10月](content/agri/2026-10.md)

```

### ③ `_navbar.md` （右上メニュー）

リポジトリのルートに作成します。

```markdown
* リンク
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