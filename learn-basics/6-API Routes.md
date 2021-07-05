API Routes
=

https://nextjs.org/learn/basics/api-routes

Next.jsのAPI Routesにより、Node.jsのサーバーレス関数なAPIエンドポイントを簡単に作成することができる。

Creating API Routes
==

`pages/api` ディレクトリにAPIエンドポイントに対応するページを作成する

```jsx
// pages/api/hello.js

export default function handler(req, res) {
    res.status(200).json({ text: 'Hello' })
}
```

http://localhost:3000/api/hello にアクセスすると、上記関数が呼び出される

API Route Details
==

### Do Not Fetch an API Route from getStaticProps or getStaticPaths

getStaticPropsやgetStaticPathsからAPI RoutesのAPIをfetchしてはいけない（する必要がない）

getStaticPropsやgetStaticPathsは常にNext.jsのサーバーサイドで実行され、クライアントサイドで実行されることはないため。
これらの関数では、APIのfetchの代わりにサーバーサイドのコードを直接実装する。

### Dynamic API Routes

API Routesはpagesと同様に動的なURLを取り扱うことができる。詳細はドキュメント参照

### Others

API Routesは以下のような場合に有効

* Saving incoming data to your database
* Securely communicating with a third-party API
* Previewing draft content from your CMS
  Preview Mode documentation を参照
