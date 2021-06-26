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

### Blog Data

TODO https://nextjs.org/learn/basics/data-fetching/blog-data
