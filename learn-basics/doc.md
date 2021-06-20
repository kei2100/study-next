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

### What You’ll Learn in This Lesson

* How to add static files (images, etc) to Next.js.
* How to customize what goes inside the <head> for each page.
* How to create a reusable React component which is styled using CSS Modules.
* How to add global CSS in `pages/_app.js`.
* Some useful tips for styling in Next.js.

Assets
==

Next.js では、静的ファイルをトップレベルの `public` ディレクトリに配置することで、asset を配信することができる。
`public` ディレクトリに配置するファイルは、静的ファイルの他、`robots.txt` や Google Site Verification などの静的アセット
を配置するのにも適している。

### Unoptimized Image

通常、HTML では以下のようにしてイメージファイルを表示することができる。

```html
<img src="/images/profile.jpg" alt="Your Name" />
```

しかし、この場合以下のような事柄は手動で対応する必要がある。

* イメージのレスポンシブ対応
* サードパーティライブラリによる画像最適化
* ビューポートに入ったときにだけイメージをロードする

Next.js では、`Image` コンポーネントを使用することでこれらのサポートを受けることができる。

### Image Component and Image Optimization

`next/image` は、HTML の `<img>` 拡張したもので、現代のウェブのために進化したもの。

Next.js はデフォルトで画像最適化をサポートする。
ブラウザがサポートしている場合、WebPなどの最新フォーマットで画像をリサイズ、最適化して提供することができる。
これにより、ビューポートが小さいデバイスに大きな画像を配信することがなくなる。
また、Next.jsは将来の画像フォーマットを自動的に採用し、それらのフォーマットをサポートするブラウザに提供することができる。

自動画像最適化はどんな画像ソースにも対応しており、画像がCMSなどの外部データソースでホストされていても最適化することができる。

### Using the Image Component

Next.js は、ビルド時に画像を最適化するのではなく、ユーザーリクエストに応じてオンデマンドで画像を最適化する。
そのため static site generator や静的ファイルのみのサイトと異なり、画像枚数が大量になるにつれてビルド時間が長くなることはない。

画像はデフォルトで遅延ロードされるので、ビューポートの外側にある画像に対して、
ページスピードが影響を受けることはない。画像は、ビューポートにスクロールされたタイミングでロードされる。

画像は、Google が検索ランキングで使用する Core Web Vitals の一つである Cumulative Layout Shift を回避するようにレンダリングされる。

以下は Image コンポーネントを使用した例。height と width はソース画像のアスペクト比で希望のレンダリングサイズを指定する必要がある。

```jsx
import Image from 'next/image'
const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
)
```

Metadata
==

ページの metadata、例えば `<title>` タグのようなものを変更したいとき、
Next.js では Head コンポーネントを使うことでそれを実現する。

```jsx
import Head from 'next/head'
export default function FirstPost() {
  return (
          <>
            <Head>
              <title>First Post</title>
            </Head>
            <h1>First Post</h1>
            <h2>
              <Link href="/">
                <a>Back to home</a>
              </Link>
            </h2>
          </>
  )
}
```

CSS Styling
==

Next.jsは `styled-jsx` というCSS-in-JSライブラリをビルトインでサポートしている

```jsx
<style jsx>{`
  .container {
    min-height: 100vh;
    padding: 0 0.5rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  main {
     padding: 5rem 0;
     flex: 1;
     display: flex;
     flex-direction: column;
     justify-content: center;
     align-items: center;
   }

  .... 
  
`}</style>
```

`styled-jsx` はReactコンポーネントの中で、そのコンポーネント内でのみ有効なCSSを記述できる。

`styled-components` や `emotion` のような他のCSS-in−JSライブラリも使うことができる。

### Writing and Importing CSS

Next.jsにはCSSとSassのサポートが組み込まれており、
.cssファイルと.scssファイルをインポートできる。
TailwindCSSなどの一般的なCSSライブラリの使用もサポートされている。

Layout Component
==

Next.jsにビルトインサポートされているCSS Modulesを使って `Layout` コンポーネントを定義していく

```tsx
// components/layout.js

import styles from './layout.module.css'

export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>
}
```

```css
/* components/layout.modules.css */
/* Important: To use CSS Modules, the CSS file name must end with .module.css. */

.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

```tsx
// pages/posts/first-post.js

import Head from 'next/head'
import Link from 'next/link'
import Layout from '../../components/layout'

export default function FirstPost() {
  return (
    <Layout> {/* Layoutコンポーネントを利用  */}
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </Layout>
  )
}
```

### Automatically Generates Unique Class Names

Devtoolなどで確認すると、上記 `Layout` コンポーネントのclass名には、
`layout_container__...:` のような自動生成されたclass名が設定されている。

これはCSS Modulesにより自動的に生成されたclass名であり、これによりクラス名の衝突を紀にする必要がなくなる。

さらにNext.jsは、CSS Modulesについても自動コード分割を行うことができ、
ページごとに最小限のCSSが読み込まれることによりバンドルサイズが小さくなる。

CSSモジュールはビルド時にJavaScriptバンドルから抽出され、
Next.jsによって自動的にロードされる.cssファイルを生成する。

