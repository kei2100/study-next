Dynamic Routes
=
https://nextjs.org/docs/routing/dynamic-routes

## Page Path Depends on External Data

Next.jsで動的なURLを取り扱う方法について

* `pages/posts/[id].js` のように、`[` ではじまり `]` で終わるファイルを `page` に配置することで動的なルーティングができる
* `[id].js` の中で `getStaticPaths` async関数を実装し、許可するidのリストを返却する
* さらに `getStaticProps({params})` を実装する。paramsから `id` プロパティを取得することができ、ここからURLで指定された
  idを取得することができる。関数からidに対応するデータを返却する
  
## Dynamic Routes Details

### Development v.s. Production

* In development (npm run dev or yarn dev), getStaticPaths runs on every request.
* In production, getStaticPaths runs at build time.

### Fallback

```jsx
export default function Post({ postData }) {
  return (
          <Layout>
              ...
          </Layout>
  )
}

export async function getStaticPaths() {
    const paths = getAllPostIds()
    return {
        paths,
        fallback: false
    }
}

export async function getStaticProps({ params }) {
  const postData = await getPostData(params.id)
  return {
    props: {
      postData
    }
  }
}
```

このfallbackの意味は以下になる

* `fallback: false` の場合、getStaticPathsによって返されるpathsにパスが一致しないと404になる
* `fallback: true` の場合、以下のような挙動になる
  * 404にはならず、上記コード例だとPost関数が呼び出される
  * このときpostDataの値はundefinedになる
  * こうして生成されたHTMLは、pathsのプリレンダリングされたページと同様に後続のリクエストに提供される
  * その後getStaticPropsも呼ばれる。ここでエラーになると500になるので注意
* `falback: 'blocking'` の場合、
  * 未知のパスはgetStaticPropsを使ってServer-side renderingされ、後続のリクエストに備えキャッシュされる
　
### Catch-all Routes

`pages/posts/[...id].js` のようなページを作成すると、以下のようなパスにマッチさせることができる
* /posts/a
* /posts/a/b
* /posts/a/b/c

このとき、getStaticPathsは以下のようにparams.idを配列で返却する必要がある
(`[id].js`のときは文字列だった)

```jsx
return [
  {
    params: {
      // Statically Generates /posts/a/b/c
      id: ['a', 'b', 'c']
    }
  }
  //...
]
```

また、getStaticPropsの引数のparams.idも配列になる

```jsx
export async function getStaticProps({ params }) {
  // params.id will be like ['a', 'b', 'c']
}
```

### Router

If you want to access the Next.js router, you can do so by importing the useRouter hook from next/router.

### 404 pages

`pages/404.js` を配置することで404ページをカスタムできる

```jsx
// pages/404.js
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>
}
```
