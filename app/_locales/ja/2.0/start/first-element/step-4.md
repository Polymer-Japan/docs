---
title: "Step 4: インプットに反応させる"
subtitle: "Polymer Elementを作ろう"
---

もちろん、クリックできないボタンは、ボタンではありません。

ボタンを切り替えるには、イベントリスナーを追加します。Polymerでは、単純な<code>on-<var>event</var></code>アノテーションを持つイベントリスナーをエレメントのテンプレートに追加できます。
Polymerの`on-click`アノテーションを使用してボタンの`click`イベントをリッスンするようにコードを変更しましょう：

icon-toggle.html { .caption }

```html
<iron-icon icon="[[toggleIcon]]" on-click="toggle"></iron-icon>
```

**`on-click`は`onclick`とは異なります。** これは[標準の <code><var>element</var>.onclick</code> プロパティ](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onclick)と異なります。`on-click`のダッシュはPolymerのアノテーション付きイベントリスナーであることを示しています。
{.alert .alert-info}

上のコードは、ボタンが押されたときに`toggle`というメソッドを呼び出します。

次に、ボタンが押されたときに`pressed`プロパティを切り替える`toggle`メソッドを作成します。コンストラクタの後の`IconToggle`のクラス定義内に`toggle`メソッドを配置します。

icon-toggle.html { .caption }

```js
toggle() {
  this.pressed = !this.pressed;
}
```

これでコードは以下のようになるはずです:

icon-toggle.html { .caption }

```html
<script>
  class IconToggle extends Polymer.Element {
    static get is() {
      return 'icon-toggle';
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
        }
      }
    }
    constructor() {
      super();
    }
    toggle() {
      this.pressed = !this.pressed;
    }
  }

  customElements.define(IconToggle.is, IconToggle);
</script>
```

`icon-toggle.html`ファイルを保存し、デモをもう一度見てください。ボタンを押すと、押した状態と押していない状態を切り替えることができるはずです。

<img src="/images/2.0/first-element/databound-toggles.png" alt="Demo showing icon toggles with star and heart icons.">

**詳細: データバインディング** デモがどのように動作するかを確認するには`demo-element.html`を開いてみてください
（コードをダウンロードした場合、`demo`フォルダにこのファイルがあります）。もちろん、このエレメントのデモ _も_ エレメントです。
エレメントは、<a href="/2.0/docs/devguide/data-binding#two-way-bindings">双方向データバインディング</a>と
<a href="/2.0/docs/devguide/data-binding#annotated-computed">算出バインディング</a>を使用して、ボタンが切り替わったときに表示されている文字列を変更します。
{ .alert .alert-info }

<a class="blue-button" href="step-3">
  Previous step: データバインディングとプロパティを使う
</a>

<a class="blue-button" href="step-5">
  Next step: カスタムCSSプロパティによるテーマ設定
</a>
