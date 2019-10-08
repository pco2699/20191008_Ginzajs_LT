---
logoImg: ''
theme : "night"
transition: "slide"
highlightTheme: "monokai"
slideNumber: true
title: "Completely understand tsconfig.json"
---
<style>
  .reveal .slides {
    text-align: left;
  }
  .reveal .slides section>* {
        margin-left: 0;
        margin-right: 0;
  }
</style>

<div class="intro">
  <div style="font-size: 58px;">Completely understand tsconfig.json</div>
  @pco2699<br>
  Ginza.js#5 @ Plaid
</div>

---

<style>
.container{
    display: flex;
}
.col{
    flex: 1;
    text-align: left;
}
</style>

### Self Introduction

<div class="container">
<div class="col">
  <img src="./images/profile.jpg">
</div>

<div class="col">
  <ul>
    <li>Kazuyuki Takayama</li>
    <li>Twitter: @pco2699</li>
    <li>Like: <b>TypeScript</b></li>
  </ul>
</div>
</div>

---

### Content
- tsconfig.jsonの中身を解説する  

---

### Goal
- 雰囲気で理解している人が<br>tsconfig.jsonを完全に理解する

---

### TypeScript Version
- TypeScript 3.6準拠
- コンパイラオプションは追加/変化するので注意
- 困ったら公式を参照！

---

#### What is tsconfig.json
- TypeScriptのプロジェクトに置かれる設定ファイル
- 主にTypeScriptのコンパイラオプションを設定する

---

#### example - tsconfig.json

```json
{
    "compilerOptions": {
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "outFile": "../../built/local/tsc.js",
    },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```

---

##### コンパイル対象指定
- files
- exclude
- include
<br><br>

##### プロジェクト分割
- references/extends
<br><br>

##### IDE
- typeAcquisition
- compileOnSave
<br><br>

##### コンパイラオプション
- compilerOptions
<br><br>

---

##### コンパイル対象指定
- files
- exclude
- include
<br><br>

- tscでコンパイルする対象を指定する  


--

##### files
```json
{
  "files": [
      "core.ts",
      "sys.ts",
      "types.ts"
  ]
}
```
filesは個別にコンパイル対象を指定

--

##### include/exclude
```json
{
  "include": [
      "src/**/*"
  ],
  "exclude": [
      "node_modules",
      "**/*.spec.ts"
  ]
}
```
include/excludeはglobで対象・対象外を指定

---

##### プロジェクト分割
- references/extends

--

##### references/extends
- tsconfig.jsonを小さいプロジェクトに分割して
　管理できるようにする

--

##### project structure
```
./home
├── tsconfig-base.json
├── src
│   └── tsconfig.json
└── test # srcの型やソースコードに依存している
    └── tsconfig.json
```

--

##### home/tsconfig.json
```
{
  "compilerOptions": {
    ...
  }
}
```
ベースとなる設定を記載 (基底クラスのような働き)

--

##### src/tsconfig.json
```
{
    "extends": "../tsconfig-base.json",
    "compilerOptions": {
        // compiler options
        "composite": true
    }
}
```
参照されるtsconfig.jsonには`composite`をつける

--

##### test/tsconfig.json
```
{
    "extends": "../tsconfig-base.json",
    "references": [
        { "path": "../src" }
    ]
}
```
`references`で参照する。

--

```cmd
$ tsc --build src
```
testを動かす際は、`--build`で`src`をビルドする

---

##### IDE
- typeAcquision
- compileOnSave

--

##### typeAcquision
```
import React from 'react'; // reactの型ファイルを自動取得
```
import時に、型ファイルをIDEが自動取得するか

--

##### compileOnSave
```
{
  "compileOnSave": true
}
```
tsconfig.jsonを保存時にtsファイルをコンパイルし直すか

---

##### コンパイラオプション
- compilerOptions
<br><br>
コンパイラオプションは非常に多い(85個)<br>
デフォルトのtsconfig.jsonでtrueのものだけ説明<br>

---

```
$ tsc --init
```

```json
{
  "compilerOptions": {
    "target": "es5",  // コンパイル後のJSのバージョン
    "module": "commonjs", // コンパイル後のimport/export方式
    "strict": true,
    "esModuleInterop": true 
  }
}
```

---

##### strictオプション
次のオプションをすべてONにする
- noImplicitAny
- noImplicitThis
- alwaysStrict
- strictBindCallApply
- strictNullChecks
- strictFunctionTypes
- strictPropertyInitialization

--

##### strictで怒られる簡単なコード
```ts
const hoge = (input) => { // noImplicitAny error
    console.log(input);
}
hoge(1);

const piyo: number = null; // strictNullChecks error
```

--

##### その他
- alwaysStrict: JSの `use strict`モードを有効化
- strictBindCallApply: `call`,`apply`,`bind`も型チェック
- noImplicitThis: 暗黙のthis引き渡しを禁止
- strictFunctionTypes: 関数引数の双変性を無効
- strictPropertyInitialization: プロパティ初期化必須

---

- esModuleInterop

--

Interoperability of CommonJS <=> ES6 Module  

Interoperability: 相互運用性

--

#### CommonJs import/export
```ts
// export
const hoge = () => {
  // ...some func
}
exports = hoge

// import
const moment = require("hoge");
moment();
```

--

#### ES6 Module import/export
```ts
// export
export const hoge = () => {
  // ...some func
};

// import
import { hoge } from 'hoge';
hoge();
```

--

#### export: CommonJs import: ES6 Module

##### esmoduleInterOp: false
```ts
import * as hoge from './hoge'
hoge(); // *を呼び出すのはES6 Moduleの規約NG
```

##### esmoduleInterOp: true
```ts
// CommonJSをdefault importでimportできる
import hoge from './hoge'
hoge(); // ES6 Module準拠
```

--

その他は<a href="https://www.typescriptlang.org/docs/handbook/compiler-options.html">公式資料</a>を参照しましょう。

---

