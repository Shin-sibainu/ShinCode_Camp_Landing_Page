---
title: "Astro入門してみた🐣"
meta_title: "Astro入門してみた🐣"
description: "Astro入門してみた🐣"
date: 2024-05-09
image: "/images/banner.png"
categories: ["Astro"]
author: "kepo"
tags: ["Astro"]
draft: false
---

## Astroとは？

Astroは静的なWebコンテンツを生成するための静的サイトジェネレーターです。
後に記述するAstro Islandsというアーキテクチャを導入していることで、必要最低限のJSだけを読み込むことで高いパフォーマンスを実現させます。

## Astro Islands

Astroを学ぶと嫌でも聞くであろうこの言葉はAstroのWebアーキテクチャパターンです。
アイランドとはHTMLの静的なページ上にあるインタラクティブなUIコンポーネントのことです。
このアイランドはそれぞれ独立していて、要求に応じてJSがロードされる仕組みになっています。
この仕組みによってAstroは高いパフォーマンスを発揮できるのでしたね。

## Astroの環境構築

```cmd
npm create astro@latest
```

上記コマンドを実行します。
実行するとセットアップのために以下の流れで処理を行います。
(バージョンによりセットアップの手順が異なることはあります。)

1. create-astroのためにyを入力
2. プロジェクト用の新しいディレクトリを作成 `例：./astro-tutorial-blog`
3. `How would you like to start your new project?`と聞かれるので、適当なものを選択
4. `Do you plan to write TypeScript?`と聞かれるのでTypeScriptを使用するならYesを選択
5. `Install dependencies?`と聞かれるのでYesを選択
6. `Initialize a new git repository?`と聞かれるのでgitリポジトリを初期化する場合はYesを選択

これで環境構築は終了です。
それでは、ターミナルから以下コマンドを実行しローカルサーバーを立ち上げましょう。

```cmd
npm run dev
```

エラーなくローカルサーバー(おそらくlocalhost:4321)が立ち上がれば成功です。

## Astroのルーティング

### 静的ルーティング
`src/pages`に.astroか.mdのファイルを作成することで自動的にルーティングを解決してくれます。これはNext.jsと同じ感じなのでNext.js経験者の方であれば取っ付きやすいかと思います。
(余談ですが.mdでページを作成できるのが面白いなと感じました。)

### 動的ルーティング
