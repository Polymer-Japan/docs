---
title: エレメントの定義
---

<!-- toc -->

## Custom Elementの定義{#register-element}

Custom Elementを定義するには、`Polymer.Element`の拡張クラスを作成し、そのクラスを`customElements.define`メソッドに渡します。

仕様では、Custom Element名は、**小文字のASCII文字で始まり、ダッシュ(-)を含まなければなりません。**

例: { .caption }

```
// define the element's class element
class MyElement extends Polymer.Element {

  // Element class can define custom element reactions
  connectedCallback() {
    super.connectedCallback();
    console.log('my-element created!');
  }

  ready() {
    super.ready();
    this.textContent = 'I\'m a custom element!';
  }
}

// Associate the new class with an element name
customElements.define('my-element', MyElement);

// create an instance with createElement:
var el1 = document.createElement('my-element');

// ... or with the constructor:
var el2 = new MyElement();
```

上記の通り、[Custom Elementのライフサイクル](custom-elements#element-lifecycle)で説明したようにエレメントのクラスにはCustom Elementのリアクションとしてコールバックを定義することができます。

## 既存のエレメントを拡張 {#extend-element}

ES6ではネイティブに提供されるサブクラス化の仕組みを活用することで、ES6の構文を使って定義済みの既存のエレメントを拡張したりカスタマイズしたりすることができます。：

```js
// Subclass existing element
class MyElementSubclass extends MyElement {
  static get is() { return 'my-element-subclass'; }
  static get properties() { ... }
  constructor() {
    super();
    ...
  }
  ...
}

// Register custom element definition using standard platform API
customElements.define(MyElementSubclass.is, MyElementSubclass);
```

エレメントの拡張についての詳細は、Custom Elementのコンセプトの解説のセクションの[他のエレメントの拡張](custom-elements#extending-elements)を参照してください。

サブクラスにテンプレートを指定しない場合、デフォルトでスーパークラスのテンプレートが継承されます。この動作をオーバーライドしたり、スーパークラスのテンプレートを変更したりするには、サブクラスの`template`のgetterメソッドをオーバーライドします。

## ミックスインの使用

*ミックスイン* によってコードをエレメント間で共有することができます。ミックスインを使い、トップの基底クラスに新たな機能を追加してみます。：

```js
class MyElementWithMixin extends MyMixin(Polymer.Element) {

}
```

この例に関しては、2つのステップに分けて考えると理解しやすいでしょう。：

```js
// Create a new base class that adds MyMixin's features to Polymer.Element
const BaseClassWithMixin = MyMixin(Polymer.Element);

// Extend the new base class
class MyElementWithMixin extends BaseClassWithMixin { ... }
```

ミックスインは単に継承チェーンにクラスを追加するだけなので、継承の一般的なルールがそのまま適用されます。

ミックスインの定義に関する詳細は、Custom Elementのコンセプトの[クラス式のミックスインでコードを共有](custom-elements#mixins)を参照してください。

## インポートとAPI

Polymer Elementを定義するのに三つの主要なHTML Importsがあります。：

インポート | 説明
--- | ---
`polymer-element.html` | `Polymer.Element`を基底クラスに定義します。
`legacy-element.html` | `Polymer.Element`を拡張し、Poymer 1.xと互換性のある`Polymer.LegacyElement`APIが付加された`Polymer.LegacyElement`を基底クラスにエレメントを定義します。また、レガシーなファクトリメソッド`Polymer()`を利用して、1.xと2.xのハイブリッドエレメントを定義することもできます(`polymer-element.html`を含んでいます）。
`polymer.html` | 1.xでpolymer.htmlのバンドルに含まれていたヘルパーエレメント（`custom-style`、`dom-bind`、`dom-if`、`dom-repeat`など)を加えてインクルードします（`legacy-element.html`を含んでいます）。

リソースを最小限にしたい場合には、`polymer-element.html`をインポートし、必要なヘルパーエレメントを個別にインポートして下さい。

1.xの後方互換APIが必要な場合には、2.xのクラススタイルのエレメントを作成する際の基底クラスとして`Polymer.LegacyElement`を使用できます。またヘルパーエレメントを利用する場合は個別にインポートする必要があります。

1.xと2.xの両方で動作可能なハイブリッドエレメントを定義するには、`polymer.html`をインポートして使用します。

## クラススタイルのエレメントでハイブリッドな動作(Behavior)を使用

`Polymer.mixinBehavior`関数を使用して、クラススタイルのエレメントにハイブリッドな動作(behavior)を付加することができます。：

```
class XClass extends Polymer.mixinBehaviors([MyBehavior, MyBehavior2], Polymer.Element) {

  ...
}
customElements.define('x-class', XClass);
```

この`mixinBehavior`関数は、1.xのレガシーAPIもミックスインするので、`Polymer.LegacyElement`を拡張したのとほとんど同じことです。これらのレガシーAPIが必要とされるのは、ハイブリッドな動作(behavior)がそれらに依存しているためです。

## メインのHTMLドキュメントでエレメントを定義 {#main-document-definitions}

試験的な実装においては、メインドキュメントからエレメントを定義するだけで済むかもしれません。本番環境では、常にエレメントは分割したファイルに定義した上で、メインドキュメントにインポートする必要があります。

メインのHTMLドキュメントでエレメントを定義するには、エレメントは`HTMLImports.whenReady(callback)`から定義します。`callback`はドキュメント内の全てのインポートの読み込み(loading)が完了した時点で呼び出されます。

```
<!DOCTYPE html>
<html>
  <head>
    <script src="bower_components/webcomponentsjs/webcomponents-lite.js">
    </script>
    <link rel="import" href="bower_components/polymer/polymer-element.html">
    <title>Defining a Polymer Element from the Main Document</title>
  </head>
  <body>
    <dom-module id="main-document-element">
      <template>
        <p>
          Hi! I'm a Polymer element that was defined in the
          main document!
        </p>
      </template>
      <script>
        HTMLImports.whenReady(function() {
          class MainDocumentElement extends Polymer.Element {

            static get is() { return 'main-document-element'; }

          }
          window.customElements.define(MainDocumentElement.is, MainDocumentElement);
        });
      </script>
    </dom-module>
    <main-document-element></main-document-element>
  </body>
</html>
```

## レガシーエレメントを定義する {#legacy-element}

レガシーなエレメントは、エレメントの登録に`Polymer`関数が使用できます。関数は新しいエレメントのプロトタイプを引数に取ります。プロトタイプには、Custom ElementのHTMLタグ名を指定する`is`プロパティが必要です。

仕様では、Custom Elementの名前は**ASCII文字で始まり、ダッシュ(-)を含む必要があります。**

例: { .caption }

```
    // register an element
    MyElement = Polymer({

      is: 'my-element',

      // See below for lifecycle callbacks
      created: function() {
        this.textContent = 'My element!';
      }

    });

    // create an instance with createElement:
    var el1 = document.createElement('my-element');

    // ... or with the constructor:
    var el2 = new MyElement();
```

`Polymer`関数は、エレメントをブラウザに登録し、コードからエレメントインスタンスを新規に生成するコンストラクタを返します。

`Polymer`関数はCustom Elementのプロトタイプチェーンを構築し、それをPolymerの`Base`プロトタイプ（Polymerの付加的な機能を提供）につなぎます。そのため開発者が独自のプロトタイプチェーンを構築することはできません。しかし、[behaviors](#prototype-mixins)プロパティを使用することでエレメント間でコードを共有することはできます。

## ライフサイクルコールバック {#lifecycle-callbacks}

`Polymer.Element`クラスは、Polymerのビルトイン機能で必須のタスクを実行するため、Web標準のCustom Elementのライフサイクルコールバックを実装しています。

また、Polymerは、エレメントのDOMの生成と初期化が完了した時点で呼び出されれる`ready`という特別なコールバックも用意しています。

<table>
  <tr>
    <th>レガシーコールバック</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><code>created</code></td>
    <td>
      エレメントの生成後、プロパティ値の設定やローカルDOMの初期化前に呼び出されます。
      <p>プロパティ値が設定される前のワンタイムの初期化に利用されます。
      </p>
      <p>ネイティブの<code>constructor</code>に相当します。
      </p>
    </td>
  </tr>
  <tr>
    <td><code>ready</code></td>
    <td>プロパティ値が設定され、ローカルDOMが初期化された後に呼び出されます。
      <p>
        ローカルDOMの初期化後に、コンポーネントのワンタイムな設定を行うのに利用されます。（プロパティ値を元に設定する場合は、<a href="observers">オブザーバー</a>を利用した方が良いかもしれません）。
      </p>
    </td>
  </tr>
  <tr>
    <td><code>attached</code></td>
    <td>
      エレメントがドキュメントに追加(attached)された後に呼び出されます。エレメントの存続期間中であれば複数回呼び出すことができます。初回の<code>attached</code>コールバックは<code>ready</code>コールバックが実行されるまで呼び出されないことが保証されています。
      <p>
        ドキュメントレベル(document-level)のイベントリスナーを追加するのにも利用されます。（エレメントに対してローカルなイベントリスナーを設定するには、<a href="events.html#annotated-listeners">アノテーション付イベントリスナー</a>や<a href="events#event-listeners">リスナーオブジェクト</a>のような宣言的なイベント処理を利用できます）。
      </p>
     <p>ネイティブの<code>connectedCallback</code>に相当します。</p>
    </td>
  </tr>
  <tr>
    <td><code>detached</code></td>
    <td>
      エレメントがドキュメントから切り離された(detached)された後に呼び出されます。エレメントの存続期間中であれば複数回呼び出すことができます。
      <p>
        <code>attached</code>コールバックで追加されたイベントリスナーの削除にも利用されます。
      </p>
      <p>ネイティブの<code>disconnectedCallback</code>に相当します。</p>
    </td>
  </tr>
  <tr>
    <td><code>attributeChanged</code></td>
    <td>エレメントの属性の一つが変更されたときに呼び出されます。
      <p>
        対応するプロパティが宣言<em>されていない</em>属性の変更を扱う場合に利用できます。（対応するプロパティが宣言されている場合は、<a href="properties#attribute-deserialization">属性のデシリアライゼーション</a>で説明しているように、Polymerが自動的に属性の変更を処理します）。
      </p>
      <p>ネイティブの<code>attributeChangedCallback</code>相当します。
      </p>
    </td>
  </tr>
</table>

### レガシーなbehaviors {#prototype-mixins}

レガシーなエレメントにおいては<em>behaviors</em>フォームを利用することでエレメント間でコードを共有することができます。behaviorにはプロパティ、ライフサイクルコールバック、イベントリスナーやその他の機能を定義することができます。

より詳細なガイドは、Polymer 1.xのドキュメントの[Behaviors](/1.0/docs/devguide/behaviors)のセクションを参照してください。
