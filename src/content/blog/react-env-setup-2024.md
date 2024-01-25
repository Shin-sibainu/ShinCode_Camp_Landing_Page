---
title: "【2024年】React開発環境構築をViteを使って構築しよう"
meta_title: "Reactの開発環境構築を2024年バージョンで行います。"
description: "Reactの開発環境構築を2024年バージョンで行います。"
date: 2024-01-10
image: "/images/blogs/thumbnails/react-setup-2024.png"
categories: ["React"]
author: "ShinCode"
tags:
  [
    "vitest",
    "tailwindcss",
    "storybook",
    "shadcn/ui",
    "husky",
    "eslint",
    "prettier",
  ]
draft: false
---

## Reactに何を導入するのか

2024年現在で個人的に導入した方が良いなと思うライブラリは以下です。

- **Vite (フロントエンドツール)**
- **Vitest (テスト用)**
- **TailwindCSS (CSSフレームワーク)**
- **StoryBook (UIカタログ)**
- **Shadcn/ui (UIコンポーネントライブラリ)**
- **Eslint (構文チェック)**
- **Prettier (コードフォーマッタ)**
- **husky(コミット前自動コマンド発火)**

初期セットアップとしてこれらを導入すればプロジェクト開発は割とスムーズにいくかなと思います。

完成系のGithubソースコードはこちらです。
[完成版ソースコード](https://github.com/Shin-sibainu/react-setup-for-yt-2024)

それでは順番にはじめます。

#### Viteを使って環境構築開始

まず始めにviteが高速なのでviteを利用します。ターミナルで以下を実行します。

```cmd
npm create vite@latest react-env-setup -- --template react-swc-ts
```

swcはRustベースのWebコンパイラです。高速に動くので採用します。
作成したプロジェクトに移動します。

```cmd
cd react-env-setup
npm install
npm run dev
npm run build
npm run preview
```

これらを実行して動くか確認してください。previewはbuildした際のdistフォルダのindex.htmlを読み込みます。本番環境で動く動作の確認ができます。

#### importパスエイリアス設定

ファイルをimportする際に

```js
import Sample from "../../../components/Sample.tsx";
```

のような相対パスだと取り扱いにくいので

```js
import Sample from @/components/Sample.tsx
```

のような形に変更するパスエイリアス設定をします。

(./tsconfig.json)

```json
"compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"]
    }
  },
```

これでパスエイリアス設定は終了です。ただ、viteで環境構築している場合はvite.config.tsも設定を変更する必要があるので、それは面倒です。なので**vite-tsconfig-paths**モジュールをインストールします。

```cmd
npm i -D vite-tsconfig-paths
```

(./vite.config.ts)

```ts
import react from "@vitejs/plugin-react-swc";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [react(), tsconfigPaths()], //for path alias
});
```

これでvite.config.tsの修正は不要になりました。

#### vitestでテストの導入

今回はvitestとtesting-libraryを使ってテストができる環境を作ります。以下をインストールしましょう。

```cmd
npm install -D vitest happy-dom @vitest/coverage-v8 @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

執筆中・・・
