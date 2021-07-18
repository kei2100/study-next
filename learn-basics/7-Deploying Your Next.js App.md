Deploying Your Next.js App
=

https://nextjs.org/learn/basics/deploying-nextjs-app

Next.js and Vercel
==

Next.js アプリを Vercel にデプロイすると以下のような機能を利用することができる。

* Static-Generation を使用するページと静的アセットは、自動的に Vercel CDN から提供され、非常に高速に配信される。
* Server-Side Rendering の関数と API Route は、自動的に分離されたサーバーレス関数となり、無限にスケールする
  
また Vercel は以下のような機能ももつ

* Custom Domains
* Next.js アプリで有効な Environment Variables
* Automatic HTTPS

Other Hosting Options
==

Next.js アプリは Node.js をサポートする任意のプロバイダにデプロイ可能。

これまで構築した nextjs-blog アプリの package.json には以下のタスクが含まれており、

```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

`npm run build` すると、production application が `.next` ディレクトリに作成される。

作成後、`npm run start` すると、静的に生成されたページと Server-side レンダリングするページ、API Routes を
サポートするハイブリッドな Node.js サーバーが起動する
