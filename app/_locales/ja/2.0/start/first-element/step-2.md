---
title: "Step 2: ローカルDOMの追加"
subtitle: "Polymer Elementを作ろう"
---

次に、アイコンを表示するシンプルなエレメントを作成します。

このステップでは、以下のことについて学習します:

*   Polymerを使用してCustom Elementsを作成する。
*   Shadow DOMの扱い。

_Shadow DOM_ は、あなたが作成したエレメントによって管理されるDOMエレメントのセットです。このセクションで詳しく解説します。

Shadow DOMのコンセプトの詳細については、開発者向けドキュメントを参照してください。: [Shadow DOM コンセプト](https://www.polymer-project.org/2.0/docs/devguide/shadow-dom)

## icon-toggle.htmlを編集する

`icon-toggle.html `を開きましょう。このファイルにはCustom Elementのスケルトンが含まれています。

他のHTMLファイルとは異なり、<em>このファイルをブラウザで読み込んでも、何も表示されません</em>。新しいエレメントを<em>定義</em>するだけです。
このデモでは`icon-toggle.html`をインポートしているので、`<icon-toggle>`エレメントを使用できます。
次の手順でエレメントに機能を追加すると、その機能がデモに表示されます。

まず、既存のコードを見てみましょう:


スタートコード-HTMLのインポート { .caption }

```html
<link rel="import" href="../polymer/polymer-element.html">
<link rel="import" href="../iron-icon/iron-icon.html">
```

重要事項:

*   `link rel="import"`は<em>HTML import</em>であり、HTMLファイルにリソースを含める方法です。
*   これらの記述は、Polymerライブラリとこのステップの後半で使用する`iron-icon`という別のCustom Elementをインポートするためのものです。

**詳細: HTML Imports** HTML Importsの詳細な議論については、HTML5Rocks.comの [HTML Imports: #include for the web](http://www.html5rocks.com/en/tutorials/webcomponents/imports/)
を参照してください。
{ .alert .alert-info }

次にエレメント自体を定義します:

スタートコード—ローカルDOM template { .caption }

```html
<dom-module id="icon-toggle">
  <template>
    <style>
      /* shadow DOM styles go here */
      :host {
        display: inline-block;
      }
    </style>
    <!-- shadow DOM goes here -->
    <span>Not much here yet.</span>
  </template>
```

重要事項:

*   `<dom-module>` タグは、エレメントのローカルDOM定義をラップします。
    この場合の`id`属性は、このモジュールが`icon-toggle`というエレメントのローカルDOMを含むことを示しています。
*   `<template>`は、エレメントのローカルDOM構造とスタイリングを実際に定義している部分です。ここにCustom Elementのマークアップを追加します。
*   `<template>`の中の`<style>`は ローカルDOMに<em>スコープされた</em>スタイルを定義するため、他のドキュメントに影響を及ぼしません。
*   `:host`疑似クラスは、定義しているCustom Element（この場合は`<icon-toggle>`）と一致します。これは、ローカルDOMツリーを格納または<em>ホスト</em>する要素です。

**詳細: Shadow DOM.** Shadow DOMでは、エレメント内に<em>スコープ付</em>DOMツリーを追加することができます。このスコープ付DOMツリーのローカルスタイルとマークアップはWebページの他の部分と切り離されています。Shadow DOMはWHATWGのShadow DOM仕様に基づいており、使用可能な場合はネイティブShadow DOMを使って動作します。
詳細については、Polymerライブラリ Docsの<a href="/2.0/docs/devguide/shadow-dom">Shadow
DOM Concepts</a>を参照してください。
{ .alert .alert-info }

エレメント定義の最後は、エレメントを登録するためのJavaScriptです。
エレメントに`<dom-module>`がある場合、このスクリプトは通常、`<dom-module>`の<em>内部</em>に配置されます。


スタートコード—エレメントの登録 { .caption }

```html
<script>
  class IconToggle extends Polymer.Element {
    static get is() {
      return "icon-toggle";
    }
    constructor() {
      super();
    }
  }
  customElements.define(IconToggle.is, IconToggle);
</script>
```

重要事項:

  * PolymerはES6クラスの構文を使用します。このコードでは、ベースのPolymer.Elementクラスを拡張して独自のクラスを作成します:

    ```
    class IconToggle extends Polymer.Element {...}
    ```

  * 新しい要素に名前を付けると、タグを使用するときにブラウザがそれを認識できるようになります。この名前は、エレメントのテンプレート定義(`<dom-module id="icon-toggle">`)で指定された`id`と一致する必要があります。

    ```
    static get is() {
      return "icon-toggle";
    }
    ```

  * この要素にはコンストラクタがあります:

    ```
    constructor() {
      super();
    }
    ```

    現時点では、このコンストラクタは何もしていません。後で使用するので、プレースホルダーとしてここに含まれています。

  * スクリプトの最後の行でCustom Elements APIから "define"メソッドを呼び出してエレメントを登録します:

    ```
    customElements.define(IconToggle.is, IconToggle);
    ```

### ローカルDOM構造を作成する

ここまでで、エレメントの基本的なレイアウトについて学んできました。次にローカルDOMテンプレートに具体的な内容を追加していきます。

`shadow DOM goes here`というコメントの下にある`<span>`を探しましょう:

icon-toggle.html—before { .caption }

```html
    <!-- shadow DOM goes here -->
    <span>Not much here yet.</span>
  </template>
```

`<span>`とその内容を以下の`<iron-icon>`タグに置き換えます:

icon-toggle.html—after { .caption }

```html
    <!-- shadow DOM goes here -->
    <iron-icon icon="polymer">
    </iron-icon>
  </template>
```

重要事項:

  * `<iron-icon>`エレメントは、アイコンをレンダリングするCustom Elementです。ここでは、 "Polymer"という名前のアイコンを使用するようにハードコードされています。

### ローカルDOMをスタイリングする

Shadow DOMを扱うための新しいいくつかのCSSセレクターがあります。`icon-toggle.html`ファイルには、前述の`:host`セレクタがすでに含まれており、トップレベルの`<icon-toggle>`エレメントをスタイリングすることができます。

`<iron-icon>`エレメントをスタイリングするには、既存のコンテンツの後の`<style>`タグ内にCSSを追加します。

icon-toggle.html: Before { .caption }

```html
    <style>
      /* shadow DOM styles go here */
      :host {
        display: inline-block;
      }
    </style>
```

icon-toggle.html: After { .caption }

```html
    <style>
      /* shadow DOM styles go here */
      :host {
        display: inline-block;
      }
      iron-icon {
        fill: rgba(0,0,0,0);
        stroke: currentcolor;
      }
      :host([pressed]) iron-icon {
        fill: currentcolor;
      }
    </style>
```

重要事項:

*   `<iron-icon>` タグはSVGアイコンを使用します。`fill`と`stroke`のプロパティは、SVG固有のCSSプロパティです。それぞれアイコンの塗りつぶしの色と輪郭の色を設定します。

*   `:host()` 関数は、<em>カッコ内のセレクタがhost要素の属性と一致する場合、</em>host要素と一致します。この場合、`[pressed]`は標準のCSS属性セレクタなので、`icon-toggle`に`pressed`属性が設定されている場合にこのルールが適用されます。

Custom Elementの定義は次のようになるはずです:

icon-toggle.html { .caption }

```html
<link rel="import" href="../polymer/polymer-element.html">
<link rel="import" href="../iron-icon/iron-icon.html">
<dom-module id="icon-toggle">
  <template>
    <style>
      /* shadow DOM styles go here */
      :host {
        display: inline-block;
      }
      iron-icon {
        fill: rgba(0,0,0,0);
        stroke: currentcolor;
      }
      :host([pressed]) iron-icon {
        fill: currentcolor;
      }
    </style>
    <!-- shadow DOM goes here -->
    <iron-icon icon="polymer"></iron-icon>
  </template>
  <script>
    class IconToggle extends Polymer.Element {
      static get is() {
      return "icon-toggle";
      }
      constructor() {
        super();
      }
    }
    customElements.define(IconToggle.is, IconToggle);
  </script>
</dom-module>
```

`polymer serve`動作していることを確認し、デモページをリロードしてください。トグルボタンがハードコードされたアイコンで表示されるはずです。

<img src="/images/2.0/first-element/hardcoded-toggles.png" alt="Demo showing icon toggles displaying Polymer icon">

デモでは`pressed`属性が設定されているため、1つのトグルが押されたときのスタイルになっているのがわかると思います。
しかし、あなたがクリックしたいところをすべてクリックしても、ボタンはトグルされません。
なぜなら、`pressed`プロパティを変更するコードがないからです。

**もし新しいトグルが表示されない場合、** 上のコードとファイルを再度確認してください。空白のページが表示された場合は、デモフォルダまたはdemo/index.htmlを開いていることを確認してください。
{ .alert .alert-info }

<a class="blue-button" href="intro">Previous step: イントロ</a>
<a class="blue-button"
    href="step-3">Next step: データバインディングとプロパティを使う</a>
