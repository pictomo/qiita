---
title: 'p5.jsでWebサイト(React, Next)にお手軽メディアアート'
tags:
  - React
  - p5.js
  - Next.js
private: false
updated_at: '2026-01-01T09:16:59+09:00'
id: 2e232ea08ef6cfe3f144
organization_url_name: null
slide: false
ignorePublish: false
---

<!-- なぜp5.jsか? -->
<!-- P5-wrapperを使わない場合 -->
<!-- related works -->
<!-- 依存関係が合わない場合 -->

2025/12/13 21:47 当記事を発行
2025/12/14 11:53 Next.js版に加えReact版の追加、他軽微な修正
2025/12/15 16:39 スタイルに関する修正、メモリーリークの情報追加
2025/12/17 15:42 軽微な修正
2026/1/1 9:16 alt.ai上場廃止に伴うリンク変更

# はじめに
世の中にはカッコいいwebサイトが沢山ありますね。
その中でも私が特に気に入っているのが[このサイト](https://dala.craftedbygc.com/)。
この記事は最後まで読まなくても、[このサイト](https://dala.craftedbygc.com/)には絶対にアクセスしてみることをお勧めします。
感動しますよ、ただのWebサイトにこんなアートがつけられるのかと。

皆さんの中には、自己紹介サイトやポートフォリオサイトを持っている方も多いと思います。
あるいは会社のWebサイトや何かしらのWebアプリの開発経験がある方もいるかもしれません。
そういったWebサイトに、上で紹介したようなメディアアートを付加できたらカッコいいと思いませんか?
今回は[p5.js](https://p5js.org/)を使った、Webサイトに手軽にメディアアートを導入する方法のご紹介です。

# サンプルプロジェクト

今回サンプルとして利用しているのは、私自身の[自己紹介サイト](https://pictomo.github.io/)([リポジトリはこちら](https://github.com/pictomo/pictomo.github.io))です。
実装前がこんな感じで、
![p5-wrapper_before.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2708336/21a493ef-469a-48b9-b2c3-14aab02a8b0c.png)

実装後がこんな感じ。
![p5-wrapper_interaction.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2708336/176489e0-d81c-4a10-915e-48c5bde36b85.gif)

Webサイトのフレームワークは[Next.js](https://nextjs.org/)を使用しています。
依存関係のうち、関係のありそうなものは以下のとおり。
```json-doc:package.json
{
  "dependencies": {
    "@p5-wrapper/next": "^3.0.1", // 今回導入
    "@p5-wrapper/react": "^5.0.2", // 今回導入
    "next": "^16.0.10",
    "p5": "^2.1.1", // 今回導入
    "react": "^19.2.3",
    "react-dom": "^19.2.3",
  },
  "devDependencies": {
    "@types/node": "^24.10.3",
    "@types/p5": "^1.7.7", // 今回導入
    "@types/react": "^19.2.7",
    "@types/react-dom": "^19.2.3",
    "sass": "^1.96.0",
    "typescript": "^5.9.3"
  },
}
```
# 実装

## 1. p5.jsと関連パッケージのイントール
[p5.js](https://p5js.org/)の導入にはラッパーライブラリ([@P5-wrapper/react](https://github.com/P5-wrapper/react)と[@P5-wrapper/next](https://github.com/P5-wrapper/next))を使用します。

まずは[p5.js](https://p5js.org/)と[@P5-wrapper/react](https://github.com/P5-wrapper/react)をインストール
```sh:shell
npm install p5 @p5-wrapper/react
```

Next.jsの場合は[@P5-wrapper/next](https://github.com/P5-wrapper/next)もインストール
```sh:shell
npm install @p5-wrapper/next
```

TypeScriptの場合は型定義ファイルも導入。
```sh:shell
npm install --save-dev @types/p5
```

### 注意点
[@P5-wrapper/react](https://github.com/P5-wrapper/react)は2025/12/13現在、バージョン5系までがリリースされています。

peerDepsは次の通り。

5系
```json
"peerDependencies": {
  "p5": ">= 2.0.0",
  "react": ">= 19.0.0",
  "react-dom": ">= 19.0.0"
}
```

4系
```json
"peerDependencies": {
  "p5": ">= 1.4.1",
  "react": ">= 18.2.0",
  "react-dom": ">= 18.2.0"
}
```

特に4系は、p5.js 2系以降やReact 19系以降と共存するか怪しいです。

peerDepsに記載のメジャーバージョンに合わせることをお勧めします。

## 2. コンポーネントの作成

環境が揃ったら、早速試しにp5のキャンバスを表示するコンポーネントを作成してみましょう。

[@p5-wrapper/react](https://github.com/P5-wrapper/react)のREADMEにあるサンプルに少しだけ手を加えたものです。
```tsx:p5-wrapper.tsx
import * as React from "react";
import { P5Canvas, type Sketch } from "@p5-wrapper/react";

const sketch: Sketch = (p5) => {
  p5.setup = () => p5.createCanvas(600, 400, p5.WEBGL);

  p5.draw = () => {
    p5.background(250);
    p5.normalMaterial();
    p5.push();
    p5.rotateZ(p5.frameCount * 0.01);
    p5.rotateX(p5.frameCount * 0.01);
    p5.rotateY(p5.frameCount * 0.01);
    p5.plane(100);
    p5.pop();
  };
};

export function App() {
  return <P5Canvas sketch={sketch} />;
}
```

Next.jsの場合はこちら。
[@p5-wrapper/next](https://github.com/P5-wrapper/next)のREADMEにあるサンプルに少しだけ手を加えたものです。
```tsx:p5-wrapper.tsx
"use client";

import { type Sketch } from "@p5-wrapper/react";
import { NextReactP5Wrapper } from "@p5-wrapper/next";

const sketch: Sketch = (p5) => {
  p5.setup = () => p5.createCanvas(600, 400, p5.WEBGL);

  p5.draw = () => {
    p5.background(250);
    p5.normalMaterial();
    p5.push();
    p5.rotateZ(p5.frameCount * 0.01);
    p5.rotateX(p5.frameCount * 0.01);
    p5.rotateY(p5.frameCount * 0.01);
    p5.plane(100);
    p5.pop();
  };
};

export default function Page() {
  return <NextReactP5Wrapper sketch={sketch} />;
}
```

適当なページに配置して、その場所にキャンバスが現れたら成功です。
```tsx:page.tsx
import P5 from "../components/p5-wrapper";

export default function Page() {
  return (
    ...
    <P5 />
    ...
  );
};
```

こんな感じ。
![p5-wrapper_put.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2708336/0a2538ff-b0d5-4bd3-9461-422407308279.gif)

キャンパスが2つ現れた場合、[ReactのStrictMode](https://ja.react.dev/reference/react/StrictMode)の仕様によるものです。
開発環境だけにしか現れないし、offにすることもできます。

背景にする必要がない場合はこれで完成。
sketch関数内部を書き換えれば、通常のp5.jsと同様にアートやゲームを作成できます。

## 3. 背景への配置

それでは背景への配置を行いっていきましょう。

コンポーネントが背景に配置されるように、スタイルを当てます。
今回はSCSSモジュールと`z-index`、`position: fixed;`を使用していますが、プロジェクトに合わせた方法でスタイルを当ててください。
注意点として、CSSはP5Canvas(NextReactP5Wrapper)本体ではなく、その親要素に当てます。

背景透明化とサイズ変更はお好みで。
```diff_tsx:p5-wrapper.tsx
...

+ import styles from "./p5-wrapper.module.scss"; // スタイルのインポート

const sketch: Sketch = (p5) => {
  p5.setup = () =>
+     p5.createCanvas(window.innerWidth, window.innerHeight, p5.WEBGL); // 画面サイズに合わせる
-     p5.createCanvas(600, 400, p5.WEBGL);

  p5.draw = () => {
+     p5.clear(); // 背景の透明化
-     p5.background(250);
    p5.normalMaterial();
    p5.push();
    p5.rotateZ(p5.frameCount * 0.01);
    p5.rotateX(p5.frameCount * 0.01);
    p5.rotateY(p5.frameCount * 0.01);
+     p5.plane(500); // サイズ変更
-     p5.plane(100);
    p5.pop();
  };
};

export default function Page() {
+   // スタイルの適用
+   return (
+     <div className={styles.background}>
+       <P5Canvas sketch={sketch} /> {/* Next.jsの場合は NextReactP5Wrapper */}
+     </div>
+   );
-   return <P5Canvas sketch={sketch} />;
}
```

```css:p5-wrapper.module.scss
.background {
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}
```

こんな感じ。
![p5-wrapper_background.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2708336/4a4d83a4-adfa-46af-9c0c-1cce2f909724.gif)

## 4. インタラクション

mouseXなどのインタラクション専用のプロパティも、素のp5.js同様に実装できます。

```diff_tsx:p5-wrapper.tsx
...

  p5.draw = () => {
    p5.clear();
    p5.normalMaterial();
    p5.push();
    p5.rotateZ(p5.frameCount * 0.01);
+     p5.rotateX(p5.mouseY * 0.01);
+     p5.rotateY(p5.mouseX * 0.01);
-     p5.rotateX(p5.frameCount * 0.01);
-     p5.rotateY(p5.frameCount * 0.01);
    p5.plane(500);
    p5.pop();
  };

...
```

こんな感じ。
![p5-wrapper_interaction.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2708336/176489e0-d81c-4a10-915e-48c5bde36b85.gif)


# トラブルシューティング - メモリーリーク
個人的に色々と試している中、オブジェクトを大量に生成しては消していくような処理において、メモリーリークっぽい事象が発生することを確認しました。
これはp5.jsのバージョンを2系から1系に落とすことで解決しましたが、原因の究明には至っていません。
同様の事象に悩まされた方は、p5.jsのバージョンを落としてみてください。

p5.jsの2系はまだリリースから比較的日が浅いので、本体にも関連ライブラリにもこういった不具合は残されているかもしれません。
今後のアップデートに期待しましょう。

# 余談
当記事は[Life is Tech ! Mentors Advent Calendar 2025](https://qiita.com/advent-calendar/2025/mentors)13日目の記事として執筆しました。
つまり執筆日も決まっていたのですが、当記事で用いていた[@P5-wrapper/react](https://github.com/P5-wrapper/react)は、なんと本日2025/12/13の朝9:52にバージョン5系が正式リリース、特に依存パッケージの周りに大幅な変更がありました。
ラッキーっちゃラッキーなのですが、予定がものすごく狂った。
大慌てで変更内容を確認しつつ、当初の想定から大きく変更して記事を書き上げました。
勘弁してくれ。

# まとめ
今回はp5.jsとラッパーライブラリを使って、Next.jsのWebサイトに手軽にメディアアートを導入する方法をご紹介しました。
さあ、あとは皆さんの創造力を存分に発揮するだけ。

私自身の自己紹介サイトも色々と試行錯誤して、現在は[こんな感じ](https://pictomo.github.io/)になってます。
私はメディアアート初心者ですが、p5.jsはとっても簡単で思い通りになんでも作れる。
AI開発とも相性がよく、考えを日本語に書き下すだけで想像通りのアートが簡単にできてしまいます。

自身のWebサイトを持っている方、それがいまいちパッとしないなと感じている方は、是非当記事を参考に、p5.jsを使ってメディアアートを導入してみてください！
