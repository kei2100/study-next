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

Navigate Between Pages
=
https://nextjs.org/learn/basics/navigate-between-pages

Pages in Next.js
==

* Next.js ではページは pages ディレクトリに配置され、それぞれアプリケーションのルートパスに関連付けられる。
  * `pages/index.js` is associated with the `/` route.
  * `pages/posts/first-post.js` is associated with the `/posts/first-post` route.

### Link Component

HTML において、ページ間のリンク付は `<a>` タグを使う。Next.js では `<a>` タグをラップした
`Link` コンポーネントを使い、クライアントサイドナビゲーションによるページ遷移を行うことができる


```jsx
// pages/index.js
import Link from 'next/link'

export default function Home() {
  return (
      <div className="container">
        <Head>
          <title>Create Next App</title>
          <link rel="icon" href="/favicon.ico" />
        </Head>
        <main>
          <h1 className="title">
            Read{' '}
            <Link href="/posts/first-post">
              <a>this page!</a> {/* Link コンポーネントで <a> をラップする */}
            </Link>
          </h1>
        </main>
      </div>
  )
}
```

```jsx
// pages/posts/first-post.js
import Link from 'next/link'

export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a> {/* Link コンポーネントで <a> をラップする */}
        </Link>
      </h2>
    </>
  )
}
```

Link コンポーネントでは、クライアントサイドナビゲーション (client-side navigation) を行うことができる。

クライアントサイドナビゲーションでは、遷移先のパスに示される JavaScript コードを取得し、それを使ってページの遷移（ページの必要部分だけのレンダリング）を行うことで、
ブラウザデフォルトの全体のページ遷移よりも、高速にページ遷移を行うことができる。

### Code splitting and prefetching

Next.js は自動的にコード分割を行う。
各ページはそのページに必要なものだけを読み込むようになっている。
つまり、ホームページがレンダリングされるときに、他のページのコードが最初に提供されることはない。

これにより、何百ページものページがある場合でも、ホームページの読み込みが速くなる。

また、リクエストしたページのコードだけを読み込むということは、 ページが孤立することを意味する。
あるページでエラーが発生しても、アプリケーションの残りの部分は動作する。

さらに、本番環境の Next.js では、 ブラウザのビューポートにリンクコンポーネントが表示されると、
Next.js はバックグラウンドでリンク先のページのコードを自動的にプリフェッチする。
ユーザーがリンクをクリックしたときには、リンク先のページのコードはすでにバックグラウンドで読み込まれており、
ページの遷移はほぼ瞬時に行われる。

### Summary

Next.js の以下機能により、アプリケーションは良好なパフォーマンスを提供できるようになる。

* 自動的コード分割
* クライアントサイドナビゲーション
* ページプリフェッチ (本番環境)

これらは pages 配下にページを作成し Link コンポーネントを使うことで実現でき、
別途ルーティングライブラリを導入したりする必要がない。


Assets, Metadata, and CSS
=

https://nextjs.org/learn/basics/assets-metadata-css
