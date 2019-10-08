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
  <div style="font-size: 62px;">Completely understand tsconfig.json</div>
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
    <li>高山 和幸</li>
    <li>@pco2699</li>
    <li><b>TypeScript</b>と<br><b>Algorithms</b></li>
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
- コンパイラオプションはよく追加/変化するので注意
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

##### その他
- references/extends
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

- tsc時にコンパイルする対象を指定する  
- webpackでコンパイルする対象が決まっていれば  
tsconfig.jsonに記載する必要はなし


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

##### その他
- references/extends
- typeAcquisition
- compileOnSave

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
ベースとなる設定を記載。(基底クラスのような働き)

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

##### その他
- typeAcquision
- compileOnSave

この２つはIDE周りの設定

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
tsconfig.jsonを保存した時にtsファイルをコンパイルし直すか

---

##### コンパイラオプション
- compilerOptions
<br><br>
コンパイラオプションは非常に多い(85個)<br>
時間が無いので代表的なものだけ説明<br>

---

型を厳しくチェックしたい

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

その他は<a href="https://www.typescriptlang.org/docs/handbook/compiler-options.html">公式資料</a>を参照しましょう。　　

---

