---
title: "【2024年】React開発環境構築をViteを使って構築しよう"
meta_title: "Reactの開発環境構築を2024年バージョンで行います。"
description: "Reactの開発環境構築を2024年バージョンで行います。"
date: 2024-01-25
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

これでtsconfig.jsonファイルの修正は不要になりました。

#### vitestでテストの導入

今回はvitestとtesting-libraryを使ってテストができる環境を作ります。以下をインストールしましょう。

```cmd
npm install -D vitest happy-dom @vitest/coverage-v8 @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

happy-domはjest-domよりも高速で動くテスト仮想環境を構築します。なので最近ではhappy-domが使われるのでこちらをインストールしましょう。

テストとカバレッジテストも行いたいので、package.jsonに以下を追加しましょう。

```json
//省略
 "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:watch": "vitest watch",
    "coverage": "vitest run --coverage",
  },
//省略
```

testしたいときは**npm run test**コマンド、カバレッジテストしたい時は**npm run --coverage**でテストができます。

また、happy-domとvitestをテストの際に利用したいので、vite.config.tsファイルを以下のように修正します。

```ts
/// <reference types="vitest" />
import react from "@vitejs/plugin-react-swc";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [react(), tsconfigPaths()],
  test: {
    environment: "happy-dom", //追加
    setupFiles: ["./vitest-setup.ts"], //追加
  },
});
```

そしてvitest-setup.tsファイルを作成します。

(./vitest-setup.ts)

```ts
import "@testing-library/jest-dom/vitest";
```

としてください。これでvitestで利用される**test**や**expect**関数がimportなしで利用できるようになります。

もしもtestやexpect関数が利用できない場合は以下をtsconfig.jsonファイルに追加してください。

```json
{
  "include": ["src", "vitest-setup.ts"]
}
```

それではテストの準備が完了したので、実際にテストできるかテストします。

#### 仮のテスト実装

テキストを入力できるインプットコンポーネントを仮に実装してみます。

(.src/components/TextInput.tsx)

```tsx
import { useState } from "react";

const TextInput = () => {
  const [text, setText] = useState("");

  return (
    <div>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="Enter some text"
        aria-label="Text Input"
      />
      <p>Entered Text: {text}</p>
    </div>
  );
};

export default TextInput;
```

ラベル文字のテストと入力発火のテストを実装してみます。

(./src/components/TextInput.test.tsx)

```tsx
// TextInput.test.js
import userEvent from "@testing-library/user-event";
import { render, screen } from "@testing-library/react";
import TextInput from "./TextInput";

test("TextInput Component Test", async () => {
  const user = userEvent.setup();
  render(<TextInput />);

  const inputElement = screen.getByLabelText("Text Input");
  expect(screen.getByText("Entered Text:")).toBeInTheDocument();

  await user.type(inputElement, "Hello World");
  expect(screen.getByText("Entered Text: Hello World")).toBeInTheDocument();
});
```

これでテストに成功すればvitestのセットアップは終了です。続いてはeslintとprettierのセットアップに移行します。

#### EslintとPrettierで構文チェックとコード整形

執筆中・・・
