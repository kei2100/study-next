Create a Next.js app
=
https://nextjs.org/learn/basics/create-nextjs-app

Next.js: The React Framework
==

Next.js は React アプリケーションをスクラッチで作成する場合にしばしば直面する以下のような煩雑な問題を解決する。

* コードは webpack などを使ってバンドルする必要があり、Babel などを使ってコンパイルする必要がある
* 最適化のためのコード分割
* パフォーマンスと SEO のためのコードの事前レンダリング方法
* React アプリをデータストアに接続するためのサーバーサイドコーディング

Next.js はこれらの問題に対し適切な抽象化と優れたディベロッパーエクスペリエンスを提供する。
以下は機能の一例

* 直感的な page-based のルーティングシステム (動的ルーティングをサポート)
* Static-Site-Generation (SSG) と Server-Side-Rendering (SSR) の2つの事前レンダリングをページごとにサポート
* 高速なページロードのための自動的なコード分割
* Build-in CSS と Sass、および CSS-in-JS ライブラリのサポート
* 開発環境での高速なリフレッシュ
* Serverless Functions で API エンドポイントを構築するための API routes
* 各機能の拡張可能性

Create a Next.js App
==

```bash
$ npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
$ cd nextjs-blog
$ npm run dev
```

### Editing the Piage
Let’s try editing the starter page.

Make sure the Next.js development server is still running.
Open pages/index.js with your text editor.
Find the text that says “Welcome to” under the `<h1>` tag and change it to “Learn”.
Save the file.

編集が保存されると [Fast Refresh](https://nextjs.org/docs/basic-features/fast-refresh) によりページが自動リロードされる
