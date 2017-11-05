---
title: Polymer2.0について
---

<!-- toc -->

Polymer 2.0は、大くの主要ブラウザベンダーによって実装済みの新しいCustom Element v1とshadow DOM v1の仕様をサポートするように設計されています。また、Polymer 1.xユーザーに対してもスムーズな移行手段を提供しています。

Polymer 2.0はいくつかの面において改善がなされています。：

*   **相互運用性の向上**：DOMの操作するのにPolymer.domがいらなくなったおかげで、Polymer製のコンポーネントを他のライブラリやフレームワークと併用することが簡単にできるようにしました。さらに、[Shady DOM](https://github.com/webcomponents/shadydom)のコードは、Polymerに統合せず再利用可能なポリフィルに分離されています。

*   **データシステムの改善**：Polymer 2.0では、データシステムに対する改善も行いました。この変更により、エレメント内あるいはエレメント間におけるデータの伝播の流れが推測しやすくなりデバッグが容易になりました。

*   **より標準に準拠**：Polymer 2.0では、エレメントを定義するのにPolymerファクトリメソッドに替えて、標準ES6のクラスと標準のCustom Element v1のメソッド群を使用するようになっています。また、機能をミックスするには、`Polymer`のbehaviorではなく、標準のJavaScript(クラス式のmixin)を利用します。 (`Polymer`ファクトリメソッドは互換レイヤを使用することで引き続きサポートされます)。

現時点ではいくつかのテストはChrome以外のブラウザでは失敗します。これらの問題はすぐに対処される予定ですが、当面は[Chrome Canary](https://www.google.co.jp/chrome/browser/canary.html)を利用するのが最善の策です。

Polymer 2.0では、1.xとの互換性を破る変更がいくつも行われていますが、これらの多くは新しいCustom Elements v1とshadow DOM v1の仕様に準拠した結果です。仕様の新しいバージョンのリリースが近づけば、さらなる変更が見込まれます。

以下のセクションで、Polymer 2.0における主な変更点について解説します。Polymer 2.0へのエレメントのアップグレードに関する詳細は、[Upgrade guide](https://www.polymer-project.org/2.0/docs/upgrade)を参照してください。

## Custom elements v1

Polymer 2.0のエレメントは、Custome Elements v1 APIに準拠することを目標としています。これによって、仕様v0に準拠するPolymer 1.xのいくつかの機能が変更されました。

主な変更点は：

*   Custome Elements v1仕様では、エレメントを定義にプロトタイプではなくES6のクラス構文を使用します。

  Polymer 2.0では、エレメントを拡張するのにES6ベースの基底クラス(`Polymer.Element`)を提供することで、ネイティブのES6の様式を利用できるようにします。なお、`Polymer`ファクトリメソッドを利用したレガシーなエレメントについても、Polymer 1.x互換レイヤーで引き続きサポートされています。

*   新しい仕様では、ライフサイクルコールバックのいくつかに変更が加えられています。特に目立った変更点は、`created`コールバックに代えてclassのconstructorを呼び出すようになった点です。また仕様では、constructor(Polymer 1.xのcreatedコールバックに相当)内において行われる処理に関して新たに制限も課されています。

*   なお、仕様でサポートされているエレメントのタイプ拡張(`is =`)は、Polymer 2.0では現時点においてはサポートしていません。

*   複雑であることを理由に新しい仕様で定義された`disable-upgrade`に関して、Polymer 2.xはサポートしていません。今後、ミックスインやアドオンとして提供される可能でしはあります。

以降のセクションでは、これらの変更点について詳しく解説します。

Custom Elements v1仕様の一般的な情報に関しては、Web Fundamentalsの[Custom elements v1: reusable web components](https://developers.google.com/web/fundamentals/primers/customelements/?hl=ja)を参照してください。

### ライフサイクルの変更 {#lifecycle-changes}

クラスベースのエレメントを作成した場合には、新しいネイティブのライフサイクルメソッド(仕様ではCustom Elemnetsの「リアクション(reaction)」と呼ばれます)が利用されます。ファクトリメソッド`Polymer`を利用してレガシーなエレメントを作成する場合には、従来通りのPolymerのコールバック名が利用されます。


<table>
  <tr>
    <td><strong>リアクション/コールバック名</strong></td>
    <td><strong>説明</strong></td>
  </tr>
  <tr>
    <td>
      <code>constructor (</code>ネイティブ<code>)</code>
      <p>
        <code>created</code> (レガシー)
    </td>
    <td>
      Custom Elements v1仕様では、<code>constructor</code>内のDOM API(従来のAPIでは<code>created</code>コールバック)から属性、子、または親の情報を読み取ることを禁止しています。同様に、<code>constructor</code>内においては、属性や子は追加されないおそれがあります。そのような作業はすべて遅らせるようにしましょう(例えば、<code>connectedCallback</code>まで)。
      レガシーな<code>created</code>コールバックは、<code>properties</code>のデフォルト値が設定される前に呼び出されることはなくなりました。そのため、<code>created</code>の中で設定されたプロパティを、エレメントのデフォルト値を定義する<code>value</code>の関数から参照することがないようにしてください。
      <p>
        その一方で、<code>properties</code>内の<code>value</code>で関数を定義する代わりに、<code>created</code>コールバックの中で<strong>あらゆる</strong>デフォルトのプロパティを設定できるようになりました。(Polymer 1.0では監視対象のプロパティ(observed properties)に対してこのような方法で設定を行うことが禁止されていました。)
    </td>
  </tr>
  <tr>
    <td><code>connectedCallback (</code>ネイティブ<code>)</code>
      <p>
        <code>attached</code> (レガシー)
    </td>
    <td>Polymer 1.xでは、初回のレンダリングまで<code>attached</code>コールバックの実行を遅延していたので、エレメントは自身やその子にアクセスすることができました。
    </td>
  </tr>
  <tr>
    <td><code>disconnectedCallback (</code>ネイティブ<code>)</code>
    <p>
      <code>detached</code> (レガシー)
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <code>attributeChangedCallback (</code>ネイティブ<code>)</code>
      <p>
        <code>attributeChanged</code> (レガシー)
    </td>
    <td>
      属性を監視するには<em>明示的に</em>登録する必要があります。
      <p>
        Polymer Elementの場合、<code>properties</code>オブジェクト内で明示的に宣言されたプロパティだけが属性の変更を追跡(tracking)できます。(つまり、属性の値を変更したとき、attribute changedコールバックが呼び出され、Polymerは属性からプロパティの値をセットします)。
      <p>
        Custom Elemnets v0では、<strong>いかなる</strong>属性の変更に対しても<code>attributeChangedCallback</code>を呼び出していました。
      <p>
        Polymer 1.xでは、明示的に宣言されたプロパティと<em>暗黙的に宣言されたプロパティ</em>の両方に対して属性のデシリアライズが行われていました。例えば、<code>properties</code>には宣言されてはいないが、バインディングで利用された場合やオブザーバーの依存部に使用された場合には、プロパティが暗黙的に宣言されたものと見なされます。
    </td>
  </tr>
  <tr>
    <td><code>ready</code> (Polymer独自の仕様)</td>
    <td>
      Polymerは、もはや<code>ready</code>が呼び出される前に、最初のLight DOMの割り当て(distribution)が完了していることを保証しません。
   </td>
  </tr>
</table>


コールバックの変更に加えて、`lazyRegister`オプションが削除された点や、すべてのメタプログラミング(テンプレートの解析、プロトタイプ上のアクセサの作成など)は、エレメントの最初のインスタンスが生成されるまで遅延されるようになった点に注意してください。

### タイプ拡張エレメント(Type-extension elements) {#type-extension}

Polymer 2.0は、タイプ拡張エレメントをサポートしていません（例：`<input is="iron-input">`）。Custom Elements v1の仕様では、タイプ拡張は、（「カスタマイズされたビルトインエレメント(customized build-in elements)」として）含引き続きサポートされておりまれ、Chromeでは実装が計画されています。しかし、Apple社は、`is`をサポートしないことを表明しており、不確定な仕様にCustom Elementsのポリフィルが依存するのを避けるため、我々は今後`is`を利用したタイプの拡張を推奨しません。代替的な手段として、ネイティブエレメントをラッパーのCustom Elementで囲むことでタイプの拡張ができるようになっています。

例えば：

`<a is="my-anchor">...</a>`

上記は次のように扱うようになります：

```
<my-anchor>
  <a>...</a>
</my-anchor>
```

ユーザーは、必要に応じて既存のタイプ拡張エレメントを置き換える必要があるかもしれません。

Polymerによって提供されるテンプレートのタイプ拡張(例えば`dom-bind`、`dom-if`や`dom-repeat`)はすべて、標準のCustom Elementsと同様に、Light DOM内に`<template>`を含めるようになりました。例えば：

1.xの場合、コードは次のようになります。：

```
<template is="dom-bind">...</template>
```

2.xでは、次のようになります。：

```
<dom-bind>
  <template>...</template>
</dom-bind>
```

Polymer Elementのテンプレート内部(つまり、`dom-module`の内部)でこれらが使用された場合、Polymerはテンプレートのタイプ拡張(例：`dom-if`や`dom-repeat`など)をテンプレートを処理する際に自動的にラップします。つまり、Polymer Elementの内部にネストされたテンプレートや他のPolymerのテンプレート(例：`dom-bind`)においては、`<template is="">`を使い続けることができ、またそうすべきことを意味します。

`index.html`**のようなメインドキュメントテンプレートを利用する場合には、手動でラップする必要があります。**

`custom-style`エレメントも標準仕様のCustom Elementのように、`<style>`エレメントをラップするように変更されました。

例えば：

```
<style is="custom-style">...</style>
```

上記は、以下のようになります：

```
<custom-style>
  <style>...</style>
</custom-style>
```


参照：

*   WHATWGのHTML仕様文書の[Creating a customized built-in element](https://html.spec.whatwg.org/#custom-elements-customized-builtin-example)
*   [Apple's position on customized built-in elements](https://github.com/w3c/webcomponents/issues/509#issuecomment-233419167)

## Shadow DOM v1

Polymer 2.0はShadow DOM v1をサポートしています。Polymerユーザーにとって、Polymer 1.0と2.0の主要な違いは、`<content>`エレメントが、v1仕様の`<slot>`エレメントに置き換えられたことです。

Polymer 1.xに含まれていたShady DOMとそれに関連するCSSカスタムプロパティのshimは、Polymerライブラリ本体からは取り除かれ、ポリフィル`webcomponents-lite.js`のバンドルに追加されました。新バージョンのShady DOMでは、代替のAPI(`Polymer.dom`)を公開せず、ネイティブDOM APIにパッチを当てることで、Polymer 2.0ユーザーはネイティブのDOM APIを直接利用できるようになりました。

ハイブリッドエレメントの場合、Polymer 2.0にネイティブのAPIに直接振り向ける`Polymer.dom`APIの新たなバージョンが含まれています。2.0だけに依存するエレメントに対しては、ネイティブのDOM APIを選択することで`Polymer.dom`を取り除くこともできます。

**Web Fundamentalsで詳細を確認**。Shadow DOMの概要については、[Shadow DOM v1：self-contained web components](https://developers.google.com/web/fundamentals/primers/shadowdom/?hl=ja)を参照してください。
{.alert .alert-info}

v1への仕様の変更に関する、簡潔で包括的な事例は、Hayato Itoの[What's New in Shadow DOM v1 (by examples)](http://hayato.io/2016/shadowdomv1/)を参照してください。

## データシステムの改善 {#data-system}

Polymer 2.0は、データシステムにいくつかの改善を導入しています。：

*   配列操作がよりシンプルになりました。抽象レイヤ`Polymer.Collection`や配列アイテムへのキーベース(key-based)のパスがなくなりました。(訳注：Collectionオブジェクトについては、[Polymer 1.0ドキュメントのObservers and computed properties](https://www.polymer-project.org/1.0/docs/devguide/observers)のObserve array mutationsを参照してください。)

*   データの変更をバッチで処理することで、パフォーマンスと正確性が向上しました。

*   オブザーバー、算出バインディング、および算出プロパティにおいて未定義(undefined)の依存部のチェックがなくなりました。これらはすべて初期化の際に一度だけチェックが行われます。

*   エレメントにオプションとしてミックスインを加えることで、オブジェクトや配列の*ダーティチェック*(dirty check)を抑制することができます。これによって、オブジェクトや配列として宣言されたプロパティに監視可能((obsevable)な変更を加えた際、Polymerはそのプロパティ以下すべて(サブプロパティ、配列のアイテム)を再評価します。これは、Polymerの提供する`set`メソッドや配列変更メソッドが使用できないアプリケーションや、不変データ(immutable data)パターンを使用しない場合に役立ちます。(訳注：ダーティーチェックとはエレメントがオブジェクトや配列の変更をチェックして、無駄なプロパティエフェクトが生じないようにする仕組みです。詳細は、データシステムのコンセプトのセクション内の可変データのミックスインに関する説明を参照してください。)

*   プロパティエフェクト(property effect)の順序が変更されました。

*   `properties`で明示的に列挙されたプロパティに限り、属性から設定を行うことができるようになりました。

*   エレメントの初期化(テンプレートのスタンプ処理やデータシステムの初期化を含む)は、エレメントがメインドキュメントにコネクトされるまで遅延されます。(これはCustom Elements v1の仕様変更を受けた結果です)。

*   その他小さな変更がいくつかの行われています。

以降のセクションでは、これらの変更点について詳しく解説していきます。

### オブジェクトや配列のダーティチェック

<!-- TODO: move me to data system concepts doc, summarize briefly here. -->

Polymer 1.xでは、*ダーティチェックメカニズム*を使用して、データシステムが余分な作業をするのを抑制していました。

Polymer 2.xにおいても、デフォルトでこのメカニズムの利用し続けてますが、エレメント自身がオブジェクトや配列のダーティチェックを行うかどうかオプトアウト(ユーザー自身がデフォルトの機能を利用しないことを選択)できるようになっています。

デフォルトのダーティチェックメカニズムを使用した場合には、次のコードにおいて*プロパティエフェクト*が生じることはありません。：

```
this.property.subproperty = 'new value!';
this.notifyPath('property');
```

`property`が参照するのは同一のオブジェクトでありダーティチェックは失敗します。そのためサブプロパティに対する変更がプロパティエフェクトによって伝播するありません。そこで代わりに、Polymerの提供する`set`メソッドや配列変更メソッドを使用するか、変更された正確なパスに対して`notifyPath`を呼び出す必要があります。：

```
this.set('property.subproperty', 'new value!');
// または
this.property.subproperty = 'new value!';
this.notifyPath('property.subproperty');
```

一般的に、ダーティチェックメカニズムの利用はパフォーマンス上好ましい手段です。そのため以下の要件が一つでも当てはまるアプリケーションにおいてはうまく機能します。：

*   不変データを使用する。
*   ちょっとした変更に対しても常にPolymerの提供するデータ変更メソッドを利用する。

一方で、不変データを使用しなかったり、Polymerの提供するデータ変更メソッドを利用できないシュチュエーションに対して、Polymer 2.0はオプションとして`MutableData`ミックスインを提供しています。`MutableData`ミックスインはダーティチェックを回避し、上記のコードは意図した通り動作します。また、*プロパティエフェクト*の発生前にいくつかの変更をまとめてバッチ処理することも可能です。：

`this.property.arrayProperty.push({ name: 'Alice' });`

```js
this.property.stringProperty = 'new value!';
this.property.counter++;
this.notifyPath('property');
```

`set`メソッドを用いることもできますし、単にトップレベル(top-level)のプロパティを設定することでプロパティエフェクトを発生させることもできます：

```
this.set('property', this.property);
// または
this.property = this.property;
```

特定のサブプロパティを変更するのに`set`メソッドを使用するのが多くの場合最も効率的な方法です。しかし、`MutableData`を利用するエレメントは、このAPIを利用する必要はありません。そうすることで、データバインディングや状態管理を行うライブラリとの互換性が向上するからです。

トップレベルでプロパティを設定し直すと、プロパティやそのサブプロパティ、あるいは配列アイテムなどに対するすべての*プロパティエフェクト*が再度実行さるので注意が必要です。ワイルドカードパスを指定したオブザーバー(例：`prop.*`)には、トップレベルの変更だけが通知されます。

```js
// 'property.*'に設定されたオブザーバーは、パス`prperty`に変更があった場合に実行されます。
this.property.deep.path = 'another new value';
this.notifyPath('property'); // トップレベルのプロパティへ変更を通知する必要があります。
```

`set`メソッドを使って特定のパスを設定すると、細かな通知も生成されます。：


```js
// 'property.*'に設定されたオブザーバーは、パス`prperty.deep.path`に変更があった場合にも実行されます。
this.set('property.deep.path', 'new value');
```


### よりシンプルな配列操作

`Polymer.Collection`APIとそれに関連した、配列でのキー(key)ベースのパス指定やspliceによる通知は削除されました。

この仕様変更には他にもいくつか利点があります。：

*   プリミティブな値による配列がサポートされます。
*   配列アイテムはユニーク(一意)である必要はありません。


キー(key)によるパスが削除されたので、配列のsplice通知に`keySplices`は利用できず、`indexSplices`プロパティだけが使えます。

### データ変更のバッチ処理

バインディングシステムにおいてデータの伝播をバッチで処理するようになりました。それによって、コンプレックスオブザーバーや算出(computing)関数は、複数のまとまった(coherent)変更に対して実行されます。変更をまとめるには、2つの方法があります。：

*   エレメントがプロパティを初期化する際は、まとめられた変更のセットが自動的に生成されます。

*   新たに導入された`setProperties`メソッドを使用することで、まとまりのある変更のセットをプログラムで生成することもできます。

```
this.setProperties({ item: 'Orange', count: 12 });
```

単一プロパティのアクセサ(accessors)については、なおもデータの伝搬は同期的に行われます。例えば、`a`と`b`の二つのプロパティを監視するオブザーバーがあるとします。：

```
// オブザーバーは二度実行されます
this.a = 10;
this.b = 20;

// オブザーバーは一度だけ実行されます
this.setProperties({a: 10, b: 20});
```

### プロパティエフェクトの順序

2.0では、オブザーバーは*プロパティ変更通知*の前に実行されます。 2.0におけるプロパティエフェクトの順序は次の通りです。：

*   算出プロパティを再計算する。
*   データバインディングに値を伝播します。
*   プロパティを属性に反映します。
*   オブザーバーを実行する。
*   プロパティ変更通知を発行する。

1.xでは、オブザーバーはプロパティ変更通知に続けて最後に実行されていました。

### オブザーバーの変更

2.0では、オブザーバーが未定義(undefined)の依存部をもったまま実行されるのを防ぐためのチェック機構が削除されました。

詳細に説明すると：

*   **一つでも**依存部が定義されている場合、マルチプロパティオブザーバー、算出プロパティ、算出バインディングが、初期化時に一度実行されます。

*   オブザーバーまたは算出関数は、引数として`undefined`を受け取る可能性が生じてくるので、これを正しく扱う処理が必要です。

2.xでは、オブザーバーや算出プロパティをインスタンスごとに動的に定義する機能も追加されています。
詳細は以下のセクションを参照してください。

[Add observers and computed properties dynamically](devguide/observers#dynamic-observers).


### その他のデータシステムの変更点

*   算出バインディングで使用される関数を設定または変更すると、バインディングシステムは新しい関数と最新のプロパティ値を用いて再度計算を実行します。例えば、以下のようにバインディングを指定したとします。：

    ```js
    some-property="{{_computeValue(a, b)}}"
    ```

    以下のように`_computeValue`関数を変更すると、`a`や`b`が変化しなくてもバインディングは再評価されます。：

    ```js
      this._computeValue = function(a, b) { ... }
    ```

*   ホストからのバインディングによって値が変化する場合には、プロパティ変更通知(<code><em>property</em>-changed</code> events)が発行されることはありません。


*   プロパティから属性へデシリアライズする際は、その属性を<code>properties</code>内のメタデータオブジェクトとして宣言する必要があります。Polymer 1.xでは、<em>暗黙的に(implicitly)</em>宣言されたプロパティ（例えば、バインディングの対象やオブザーバーの依存部として記述されたプロパティ）であってもデシリアライズが行われていました。


## Polymer 1.0の互換レイヤ

Polymer 2.0は、現在のPolymer 1.0ユーザーが引き続きインポートできるように`polymer/ polymer.html`を残したままにしています。このインポートには、エレメントを定義するためのレガシーな`Polymer`関数が含まれ、Polymer 1.0のAPIで書かれたコードとの互換性の破綻を招く変更を最小限に抑えるよう配慮がなされています。

ほとんどの場合、既存のユーザーがPolymer 2.0にアップグレード際に求められるのは、`content`の割り当てやスタイリングに関連したShadow DOM v1 APIに準拠させることと、Custom Elements v1 APIの仕様改訂に伴うわずかな変更を既存のコードに加えることだけです。

## 削除されたメソッドとプロパティ

不必要なコードを減らすという目的に従い、ES6ベースの新たなエレメント`Polymer.Element`においては、様々なメソッドやプロパティが取り除かれています。削除されたAPIはいくつかのカテゴリに分類できます。：

*   ネイティブDOM APIに付加されていたシンプルな機能(例：`fire`や`trasform`)
*   まれにしか使用されない属性やプロパティ(例：`attributeFollows`や`classFollows`など)
*   インスタンスに関連しないメソッドやプロパティ(例：1.xでは、`importHref`はインスタンスメソッドでしたが、コールバックのバインディング以外にインスタンス固有の処理を行うことはありませんでした。)

削除または移管されたAPIの包括的なリストは、`Polymer.Element`APIの最終的な仕様が確定した後に公開する予定です。

## ブラウザのサポートとポリフィル

リリース時点においてPolymer 2.0は、Polymer 1.xと同様に次のブラウザをサポートします。Polymer 1.xーIE 11、Edge、Safari(9+)、Chrome、Opera、Firefox

Polymer 2.0はCustom ElementsとShadow DOMの新しい仕様(v1)に互換のあるポリフィルと一緒に開発されテストされてきました。Polymer 2.0をテストするには、`1.0.0-rc.7`より上位バージョンの`webcomponentsjs`を使用します。webcomponentsjsは、bowerのdependenciesにおいてPoymer 2.xを指定することでバンドルされてきます。

ポリフィルを読み込む方法はいくつか存在します。：

*   `webcomponents-lite.js`には、上記サポート対象の全ブラウザで動作するのに必要な全てのポリフィルが含まれています。
*   `webcomponents-loader.js`は、実行時に機能検出(feature-detection)を行い、必要な場合にはポリフィルが読み込まれます。

上記以外の方法を選択した場合とそのトレードオフについて解説を読む：
*   [webcomponentsjs on GitHub](https://github.com/webcomponents/webcomponentsjs/blob/master/README.md)

## EcmaScript 2015(別名：ES6)

Polymer 2.xや2.xのクラススタイルのエレメントは、次世代の標準JavaScriptであるEcmaScript 2015(一般的にはES6として知られています)を使って記述されています。これは、Custom Elementの新たな仕様が要求するものです。ES6に馴染みがない場合、Polymerで使用されているES6の基本の理解が参考になります。特に、以下の機能はコード例で広く使用されています。：


* [ES6 classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
* [Shorthand property and method names](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_2015)
* [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [Template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

ウェブ上には、以下のような様々な学習用の情報が用意されています。：

* [You Don't Know JS: ES6 and Beyond](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/README.md#you-dont-know-js-es6--beyond)

最新のChrome、Safari 10、Safari Technology Preview、Firefox、およびEdgeでは、ES6をコンパイルすることなく実行できます。IE11とSafari 9でPolymer 2.xを実行するにはコンパイルが必要です。

Polymer CLIや`polymer-build`ライブラリは、ビルド時にES6からES5へのトランスパイルをサポートしています。さらに、ブラウザが必要とする場合には、`polymer serve`や`polymer test`といったコマンドも実行時にトランスパイルを行います。

より詳細な情報は、[Browser compatibility](browsers#es6)を参照してください。

## Polymer 2.0のインストール {#installing}

bowerを使って、最新のPolymer 2.xリリースをインストールできます。

```
bower install --save Polymer/polymer#^2.0.0
```

bowerを使って、現時点で利用可能なハイブリッドエレメントをインストールすることもできます。：

```
bower install --save PolymerElements/paper-button#^2.0.0
```

### 既存のプロジェクトをアップグレード {#upgrading}

既存のコードをPolymer 2.0上で動作させる方法については、[Upgrade guide](upgrade)を参照してください。

## Polymer Elementの可用性(availability) {#elements}

開発チームは、Polymer 1.7+と2.xの双方に互換性のある、新しい「ハイブリッド」フォーマットを利用できるようにPolymer Elementをアップデート中です。

以下のエレメントはすでにPolymer 2.0をサポートするようにアップデートが完了しており、アップデート処理は不要です。：

<ul>
<li><a href="https://github.com/PolymerElements/app-layout">app-layout</a></li>
<li><a href="https://github.com/PolymerElements/app-localize-behavior">app-localize-behavior</a></li>
<li><a href="https://github.com/PolymerElements/app-pouchdb">app-pouchdb</a></li>
<li><a href="https://github.com/PolymerElements/app-route">app-route</a></li>
<li><a href="https://github.com/PolymerElements/app-storage">app-storage</a></li>
<li><a href="https://github.com/PolymerElements/gold-zip-input">gold-zip-input</a></li>
<li><a href="https://github.com/PolymerElements/iron-a11y-announcer">iron-a11y-announcer</a></li>
<li><a href="https://github.com/PolymerElements/iron-a11y-keys">iron-a11y-keys</a></li>
<li><a href="https://github.com/PolymerElements/iron-a11y-keys-behavior">iron-a11y-keys-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-ajax">iron-ajax</a></li>
<li><a href="https://github.com/PolymerElements/iron-autogrow-textarea">iron-autogrow-textarea</a></li>
<li><a href="https://github.com/PolymerElements/iron-behaviors">iron-behaviors</a></li>
<li><a href="https://github.com/PolymerElements/iron-checked-element-behavior">iron-checked-element-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-collapse">iron-collapse</a></li>
<li><a href="https://github.com/PolymerElements/iron-component-page">iron-component-page</a></li>
<li><a href="https://github.com/PolymerElements/iron-demo-helpers">iron-demo-helpers</a></li>
<li><a href="https://github.com/PolymerElements/iron-doc-viewer">iron-doc-viewer</a></li>
<li><a href="https://github.com/PolymerElements/iron-dropdown">iron-dropdown</a></li>
<li><a href="https://github.com/PolymerElements/iron-fit-behavior">iron-fit-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-flex-layout">iron-flex-layout</a></li>
<li><a href="https://github.com/PolymerElements/iron-form-element-behavior">iron-form-element-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-icon">iron-icon</a></li>
<li><a href="https://github.com/PolymerElements/iron-icons">iron-icons</a></li>
<li><a href="https://github.com/PolymerElements/iron-iconset">iron-iconset</a></li>
<li><a href="https://github.com/PolymerElements/iron-iconset-svg">iron-iconset-svg</a></li>
<li><a href="https://github.com/PolymerElements/iron-image">iron-image</a></li>
<li><a href="https://github.com/PolymerElements/iron-input">iron-input</a></li>
<li><a href="https://github.com/PolymerElements/iron-jsonp-library">iron-jsonp-library</a></li>
<li><a href="https://github.com/PolymerElements/iron-label">iron-label</a></li>
<li><a href="https://github.com/PolymerElements/iron-list">iron-list</a></li>
<li><a href="https://github.com/PolymerElements/iron-localstorage">iron-localstorage</a></li>
<li><a href="https://github.com/PolymerElements/iron-location">iron-location</a></li>
<li><a href="https://github.com/PolymerElements/iron-media-query">iron-media-query</a></li>
<li><a href="https://github.com/PolymerElements/iron-menu-behavior">iron-menu-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-meta">iron-meta</a></li>
<li><a href="https://github.com/PolymerElements/iron-overlay-behavior">iron-overlay-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-pages">iron-pages</a></li>
<li><a href="https://github.com/PolymerElements/iron-range-behavior">iron-range-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-resizable-behavior">iron-resizable-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-scroll-target-behavior">iron-scroll-target-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-scroll-threshold">iron-scroll-threshold</a></li>
<li><a href="https://github.com/PolymerElements/iron-selector">iron-selector</a></li>
<li><a href="https://github.com/PolymerElements/iron-test-helpers">iron-test-helpers</a></li>
<li><a href="https://github.com/PolymerElements/iron-validatable-behavior">iron-validatable-behavior</a></li>
<li><a href="https://github.com/PolymerElements/iron-validator-behavior">iron-validator-behavior</a></li>
<li><a href="https://github.com/PolymerElements/marked-element">marked-element</a></li>
<li><a href="https://github.com/PolymerElements/neon-animation">neon-animation</a></li>
<li><a href="https://github.com/PolymerElements/paper-badge">paper-badge</a></li>
<li><a href="https://github.com/PolymerElements/paper-behaviors">paper-behaviors</a></li>
<li><a href="https://github.com/PolymerElements/paper-button">paper-button</a></li>
<li><a href="https://github.com/PolymerElements/paper-card">paper-card</a></li>
<li><a href="https://github.com/PolymerElements/paper-checkbox">paper-checkbox</a></li>
<li><a href="https://github.com/PolymerElements/paper-dialog">paper-dialog</a></li>
<li><a href="https://github.com/PolymerElements/paper-dialog-behavior">paper-dialog-behavior</a></li>
<li><a href="https://github.com/PolymerElements/paper-dialog-scrollable">paper-dialog-scrollable</a></li>
<li><a href="https://github.com/PolymerElements/paper-drawer-panel">paper-drawer-panel</a></li>
<li><a href="https://github.com/PolymerElements/paper-dropdown-menu">paper-dropdown-menu</a></li>
<li><a href="https://github.com/PolymerElements/paper-fab">paper-fab</a></li>
<li><a href="https://github.com/PolymerElements/paper-header-panel">paper-header-panel</a></li>
<li><a href="https://github.com/PolymerElements/paper-icon-button">paper-icon-button</a></li>
<li><a href="https://github.com/PolymerElements/paper-input">paper-input</a></li>
<li><a href="https://github.com/PolymerElements/paper-item">paper-item</a></li>
<li><a href="https://github.com/PolymerElements/paper-listbox">paper-listbox</a></li>
<li><a href="https://github.com/PolymerElements/paper-material">paper-material</a></li>
<li><a href="https://github.com/PolymerElements/paper-menu-button">paper-menu-button</a></li>
<li><a href="https://github.com/PolymerElements/paper-progress">paper-progress</a></li>
<li><a href="https://github.com/PolymerElements/paper-radio-button">paper-radio-button</a></li>
<li><a href="https://github.com/PolymerElements/paper-radio-group">paper-radio-group</a></li>
<li><a href="https://github.com/PolymerElements/paper-ripple">paper-ripple</a></li>
<li><a href="https://github.com/PolymerElements/paper-scroll-header-panel">paper-scroll-header-panel</a></li>
<li><a href="https://github.com/PolymerElements/paper-slider">paper-slider</a></li>
<li><a href="https://github.com/PolymerElements/paper-spinner">paper-spinner</a></li>
<li><a href="https://github.com/PolymerElements/paper-styles">paper-styles</a></li>
<li><a href="https://github.com/PolymerElements/paper-swatch-picker">paper-swatch-picker</a></li>
<li><a href="https://github.com/PolymerElements/paper-tabs">paper-tabs</a></li>
<li><a href="https://github.com/PolymerElements/paper-toast">paper-toast</a></li>
<li><a href="https://github.com/PolymerElements/paper-toggle-button">paper-toggle-button</a></li>
<li><a href="https://github.com/PolymerElements/paper-toolbar">paper-toolbar</a></li>
<li><a href="https://github.com/PolymerElements/paper-tooltip">paper-tooltip</a></li>
<li><a href="https://github.com/PolymerElements/platinum-sw">platinum-sw</a></li>
<li><a href="https://github.com/firebase/polymerfire">polymerfire</a></li>
<li><a href="https://github.com/PolymerLabs/note-app-elements">polymerlabs/note-app-elements</a></li>
<li><a href="https://github.com/PolymerElements/prism-element">prism-element</a></li>
</ul>
