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

Global Styles
==

CSS Modulesはコンポーネントレベルのスタイル適用に使えるが、全てのページでグローバルにロードされるCSSも定義することができ、
Next.jsでもサポートされている。

グローバルにロードされるCSSを作成するには `pages/_app.js` を作成する。

`_app.js` に定義する `App` コンポーネントは、全ての異なるページで共通に使うトップレベルコンポーネントになる。
`App` コンポーネントは、例えば、ページ間移動があっても保持される共通の状態を持ったりするのに使うことができる。

```jsx
// pages/_app.js

import '../styles/global.css'

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

```css
/* styles/global.css */
html,
body {
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen, Ubuntu,
  Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  line-height: 1.6;
  font-size: 18px;
}

* {
  box-sizing: border-box;
}

a {
  color: #0070f3;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

img {
  max-width: 100%;
  display: block;
}
```

グローバルにロードされるCSSのインポートは _app.js からのみ許可されており、
個別ページのコンポーネントからはロードすることは認められていない。
(CSSファイル自体のパスは任意)

Styling Tips
==

### Using `classnames` library to toggle classes

```css
/* components/alert.module.css */

.success {
  color: green;
}
.error {
  color: red;
}
```

```jsx
// components/alert.js

import styles from './alert.module.css'
import cn from 'classnames'

export default function Alert({ children, type }) {
  return (
    <div
            className={cn({
              [styles.success]: type === 'success',
              [styles.error]: type === 'error'
            })}
    >
      {children}
    </div>
  )
}
```

Next.jsは、何も設定しなくても、PostCSSを使ってCSSをコンパイルする。

PostCSSの設定をカスタマイズするには、postcss.config.jsというトップレベルのファイルを作成する。
これは、Tailwind CSSのようなライブラリを使用している場合に便利。

以下は、Tailwind CSSを追加する手順。
Next.jsのデフォルトの動作に合わせるために、postcss-preset-envとpostcss-flexbugs-fixesを使用するのが良い。

```bash
$ npm install tailwindcss postcss-preset-env postcss-flexbugs-fixes
```

```js
// postcss.config.js

module.exports = {
  plugins: [
    'tailwindcss',
    'postcss-flexbugs-fixes',
    [
      'postcss-preset-env',
      {
        autoprefixer: {
          flexbox: 'no-2009'
        },
        stage: 3,
        features: {
          'custom-properties': false
        }
      }
    ]
  ]
}
```

```js
// tailwind.config.js

module.exports = {
  purge: [
    // Use *.tsx if using TypeScript
    './pages/**/*.js',
    './components/**/*.js'
  ]
  // ...
}
```
