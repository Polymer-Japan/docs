---
title: "Step 3: データバインディングとプロパティを使う"
subtitle: "Polymer Elementを作ろう"
---

今の状態だと、エレメントにはあまり動きがありません。このステップでは、マークアップ、属性、JavaScript、プロパティなどからアイコンに設定できる基本的なAPIを紹介します。

まず、データバインディングについて少し説明します。次のように、HTMLマークアップで使用できる`toggleIcon`プロパティを作成します:

```html
<iron-icon icon="[[toggleIcon]]"></iron-icon>
```

これを正しく動かすには、toggleIconをプロパティとして宣言する必要があります。

次に、`toggleIcon`プロパティの宣言を追加します。

scriptタグを見つけ、エレメントのクラス定義に次のプロパティを追加します:

icon-toggle.html: Before { .caption }

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
icon-toggle.html: After { .caption }

```html
<script>
  class IconToggle extends Polymer.Element {
    static get is() {
    return "icon-toggle";
    }
    static get properties() {
      return {
        toggleIcon: {
          type: String
        },
      }
    }
    constructor() {
      super();
    }
  }
  customElements.define(IconToggle.is, IconToggle);
</script>
```

重要事項:

  * HTMLで使用するためにプロパティを宣言する必要があります。
  * この単純なプロパティ宣言には、型（この場合は`String`）の定義が含まれています。

**詳細: デシリアライズ属性。** 宣言されたプロパティの型定義は、Polymerが属性値（常に文字列値）をJavaScriptプロパティ値に変換または<em>デシリアライズ</em>する方法に影響します。
デフォルトは`String`なので、この`toggleIcon`の宣言は形だけのものです。詳細は、Polymerドキュメントの<a href="/2.0/docs/devguide/properties#attribute-deserialization">属性のデシリアライズ</a>を参照してください。
{ .alert .alert-info }

次に、`<iron-icon>`エレメントを見つけて、`icon`属性の値を "polymer"から"`[[toggleIcon]]`"に変更します。

icon-toggle.html { .caption }

```html
<!-- local DOM goes here -->
<iron-icon icon="[[toggleIcon]]">
</iron-icon>
```

重要事項:

  * `toggleIcon`は、トグルボタンエレメントで定義される<em>プロパティ</em>です。まだデフォルト値は設定されていません。

  * `icon="[[toggleIcon]]" `という代入は、<em>データバインディング</em>と呼ばれるものです。エレメントの`toggleIcon`<em>プロパティ</em>を`<iron-icon>`の`icon`プロパティにリンクしています。

これで、エレメントを使うだけでなく、マークアップやJavaScriptを使うことで`toggleIcon`プロパティを設定できるようになりました。
新しいアイコンがどこから来ているのか知りたければ、`demo`フォルダの`demo-element.html`を見てください。

以下のような記述を確認できるはずです:

demo-element.html—既存のデモコード { .caption }

```html
<icon-toggle toggle-icon="star" pressed></icon-toggle>
```

これらの行は _すでに記述されている_ ため、新しく追加する必要はありません。これらの行は、画面上に表示されるデモの一部です。
しかし、自分で試してみたい場合は、新しい`<icon-toggle>`エレメントを`demo-element.html`ファイルに追加してみてください。
`add`、`menu`、`settings`などの名前のアイコンを試しに追加することができます。

**詳細: 属性およびプロパティ名。** 上記のマークアップは`toggleIcon`ではなく`toggle-icon`を使用していることに注意しましょう。
Polymerでは、属性名はダッシュケースを使用しプロパティ名はキャメルケースで表します。
詳細については、Polymerライブラリのドキュメントの<a href="/2.0/docs/devguide/properties#property-name-mapping">プロパティ名と属性名のマッピング</a>を参照してください。
{ .alert .alert-info }

`properties`ブジェクトは、さらにいくつかの機能をサポートしています。`pressed`プロパティのサポートを追加するには、次の行を追加します:

icon-toggle.html: Before { .caption }
```html
<script>
  class IconToggle extends Polymer.Element {
    static get is() {
    return "icon-toggle";
    }
    static get properties() {
      return {
        toggleIcon: {
          type: String
        },
      }
    }
    constructor() {
      super();
    }
  }
  customElements.define(IconToggle.is, IconToggle);
</script>
```

icon-toggle.html: After { .caption }

```html
<script>
  class IconToggle extends Polymer.Element {
    static get is() {
    return "icon-toggle";
    }
    static get properties() {
      return {
        pressed: {
          type: Boolean,
          notify: true,
          reflectToAttribute: true,
          value: false
        },
        toggleIcon: {
          type: String
        },
      }
    }
    constructor() {
      super();
    }
  }
  customElements.define(IconToggle.is, IconToggle);
</script>
```

重要事項:

*   このより複雑なプロパティの場合、複数のフィールドを持つ構成のオブジェクトを指定します。
*   `value`は、プロパティの[default value](/2.0/docs/devguide/properties#configure-values)を指定しています。
*   `notify`プロパティは、プロパティの値が変更されたときに<em>プロパティ変更イベントをディスパッチする</em>ようPolymerに指示します。これにより、他のノードによって変更が監視されるようになります。
*   `reflectToAttribute`プロパティは、[プロパティが変更されたとき、対応する属性を更新するように](/2.0/docs/devguide/properties#attribute-reflection)Polymerに指示をします。これにより、`icon-toggle[pressed]`のような属性セレクタを使用してエレメントをスタイリングすることができます。

**詳細: `notify` 及び `reflectToAttribute`。** `notify`プロパティと`reflectToAttribute`プロパティは同じように _見える_ かもしれません: これらは両方とも、エレメントの状態を外の世界に見えるようにしています。
`reflectToAttribute`は、 **DOMツリーの中** で状態を可視化するため、CSSおよび`querySelector`メソッドに対して可視化されます。
`notify`は、JavaScriptのイベントハンドラまたはPolymerの<a href="/2.0/docs/devguide/data-binding#two-way-bindings">双方向データバインディング</a>のいずれかを使用して、エレメントの外側から状態の変化をオブザーバブルにします。
{ .alert .alert-info }

これで、エレメントが`押され`、`toggleIcon`プロパティが動作しています。

デモをリロードすると、前のステップでハードコードされたアイコンに代わりスターとハートのアイコンが表示されるはずです:

<img src="/images/2.0/first-element/static-toggles.png" alt="Demo showing icon toggles with star and heart icons">

<a class="blue-button" href="step-2">Previous step: ローカルDOMの追加</a>
<a class="blue-button" href="step-4">Next step: インプットに反応させる</a>
