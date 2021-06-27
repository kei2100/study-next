Pre-rendering and Data Fetching
=

https://nextjs.org/learn/basics/data-fetching

Pre-rendering
==

Pre-renderingはNext.jsの最も重要なコンセプトの一つ

デフォルトでは、Next.jsはすべてのページをプリレンダーする。
これは、Next.jsが、クライアントサイドのJavaScriptですべてを処理するのではなく、
あらかじめ各ページのHTMLを生成しておくことを意味する。
プリレンダリングを行うことで、パフォーマンスやSEOの向上が期待できる。

生成された各HTMLには、そのページに必要な最小限のJavaScriptコードが関連付けられる。
ページがブラウザに読み込まれると、そのJavaScriptコードが実行され、
ページが完全にインタラクティブになる。
(このプロセスは `hydration` と呼ばれる。）

### Check That Pre-rendering Is Happening

JavaScriptをオフにしてNext.jsアプリケーションにアクセスすることで、
JavaScriptなしでもページがレンダリングされていることを確認できる

プレーンなReact.jsアプリケーションではプリレンダリングは行われないので、
JavaScriptを無効化すると普通は描画が満足に行われることはない。

※ 注意：localhostのNext.jsアプリケーションではCSSが読み込まれない

![image](https://user-images.githubusercontent.com/1415655/123511704-e3909b00-d6bd-11eb-8ebd-3a103531940f.png)
![image](https://user-images.githubusercontent.com/1415655/123511710-e9867c00-d6bd-11eb-8867-d98ff605ddc8.png)

### Two Forms of Pre-rendering

プリレンダリングには2つの方法がある

* Static Generation: ビルド時に静的なHTMLを生成する。すべてのリクエストでこのプリレンダリングされたHTMLが使用される
* Server-side Rendering: リクエストごとにサーバーサイドでHTMLを生成する

NOTE: 開発モード(npm run dev)では、Static Generationを使用するページであってもリクエストごとのHTML生成となる

### Per-page Basis

ページごとにプリレンダリングの方法を設定できる。あるページはSSG、あるページはSSRのようにハイブリッドな構成が可能

### When to Use Static Generation v.s. Server-side Rendering

ユーザーのリクエストが行われる前にページをレンダリング可能と考えられる場合、
データのある無しに関わらずStatic Generationを採用するのがおすすめ。

例えば以下のようなページ

* Marketing pages
* Blog posts
* E-commerce product listings
* Help and documentation

一方で、ユーザーのリクエストが行われたタイミングでないとレンダリングに必要なデータが揃わない場合は、
ページ提供速度は遅くなるがServer-side Renderingを採用する。あるはプリレンダリングは行わないで、
Client-side JavaScriptによりデータ反映を行うこともできる。 特にユーザー固有のページをサーブする場合は
Client-side JavaScriptが最適。（SSRでCDNを挟んでユーザー固有のレンダリングをしてしまうとキャッシュが他人に見えてしまう障害を起こすリスクがある）

### Static Generation with and without Data

外部データを取得する必要がないページの場合、そのようなページは当然にStatic Generationが可能である。

しかし外部データが必要な場合であっても、Next.jsはそのようなページのStatic Generationをサポートする。
仕組みとしては、

* ページで、`getStaticProps` というasync関数をexportする
* その関数内でページに必要なデータをフェッチする処理を実装する
* するとNext.jsは、ビルド時にその関数を呼び出し、ページに必要なデータを取得したうえでページのStatic Generationを実行する

```jsx
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

NOTE: 開発モードではリクエストごとにgetStaticPropsの呼び出しが実行される

### getStaticProps Details

getStaticPropsは必ずサーバーサイドで実行されるため、外部APIを使用したデータフェッチはもとより、
Next.jsアプリケーションサーバーからDBアクセスして取得したデータなども利用することができる。

```jsx
export async function getSortedPostsData() {
  // Instead of the file system,
  // fetch post data from an external API endpoint
  const res = await fetch('..')
  return res.json()
}
```

※ fetch APIはNext.jsによりポリフィルされ、クライアントサイドでもサーバーサイドでも同様に呼び出すことができる。

```jsx
import someDatabaseSDK from 'someDatabaseSDK'

const databaseClient = someDatabaseSDK.createClient(...)

export async function getSortedPostsData() {
  // Instead of the file system,
  // fetch post data from a database
  return databaseClient.query('SELECT posts...')
}
```

### Development vs. Production

* development modeでは、getStaticPropsの呼び出しはリクエストごとに実行される（Server-side Renderingの挙動）
* production modeでは、ビルド時に実行される。しかしこの挙動は `getStaticPaths` 関数を実装し、そこから `fallback key` を
返却することで拡張することができる
  
どちらのモードでも最終的にproductionではビルド時に実行することを想定しているため、
リクエストヘッダーやクエリパラメータなどリクエスト時にのみ利用可能なデータを使うことはできない。

### Only Allowed in a Page

getStaticPropsは `page` ファイルからのみエクスポートすることができる。
理由の1つは、ページがレンダリングされる前にReactが必要なすべてのデータを持っている必要があるため。

### What If I Need to Fetch Data at Request Time?

ユーザーのリクエストが届かないうちは必要なデータが揃わず、プリレンダリングできないといった場合、
Static Generationは良い選択ではない。

このような場合はServer-side Renderingを選択するか、プリレンダリング自体を行わない選択をする。

### Using `getServerSideProps`

Server-side Renderingを行うには、getStaticPropsの代わりに `getServerSideProps` を `page` に実装する。

```jsx
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    }
  }
}
```

getServerSidePropsはリクエストごとに呼び出され、 contextパラメータにリクエスト由来のパラメータが設定される。

getServerSidePropsは、リクエスト時にデータを取得しなければならないページを事前にレンダリングする必要がある場合にのみ
使用する。TTFB（Time to First Byte）はgetStaticPropsよりも遅くなる、なぜなら 、
サーバーはリクエストごとに結果を計算しなければならず、追加設定なしにCDNで結果をキャッシュすることはできないため。

### Client-side Rendering

Client-side Renderingは以下のような流れで実行される

* 外部データを必要としないページの部分をプリレンダリングしておく
* ページが読み込まれたらJavaScriptを使用して必要なデータをフェッチし、残りの部分の処理を行う。

Client-side Renderingは、ユーザーのダッシュボードページなどプライベートでユーザー固有なデータを表示する場合に選択する

### SWR

Next.js開発チームは、外部データフェッチ用のReactHookライブラリ `SWR` を提供している。

クライアントサイドでデータを取得している場合は、SWRが強く勧められている。
SWRは、キャッシング、再検証、フォーカストラッキング、インターバルでの再取得などの機能を備える。

https://swr.vercel.app/ja

> “SWR” という名前は、 HTTP RFC 5861 で提唱された HTTP キャッシュ無効化戦略である stale-while-revalidate に由来しています。 SWR は、まずキャッシュからデータを返し（stale）、次にフェッチリクエストを送り（revalidate）、最後に最新のデータを持ってくるという戦略です。

> SWR では、 コンポーネントはデータの更新を継続的かつ自動的に受け取ることができます。
そして、 UI は常に高速でリアクティブなモノになります。

```jsx
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```
