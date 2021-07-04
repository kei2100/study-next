Dynamic Routes
=
https://nextjs.org/docs/routing/dynamic-routes

## Page Path Depends on External Data

Next.jsで動的なURLを取り扱う方法について

* `pages/posts/[id].js` のように、`[` ではじまり `]` で終わるファイルを `page` に配置することで動的なルーティングができる
* `[id].js` の中で `getStaticPaths` async関数を実装し、許可するidのリストを返却する
* さらに `getStaticProps({params})` を実装する。paramsから `id` プロパティを取得することができ、ここからURLで指定された
  idを取得することができる。関数からidに対応するデータを返却する
