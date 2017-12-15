---
title: ジェスチャーイベント
---

<!-- toc -->

Polymerは、特別なユーザーインタラクションに向けて独自の「ジェスチャーイベント」をオプションでサポートしています。ジェスチャーイベントは、タッチ及びマウスの両環境でアップ/ダウン/トラックに関して一貫したイベントを発生させることができ、仕様の`mouse-`イベントや`touch-`イベントに代えて利用されることが推奨されます。これにより、タッチとマウスの両デバイス間で相互運用性が向上します。

**通常、モバイルブラウザでは`tap`の代わりに、標準の`click`を利用します。** 後方互換を担保するため`tap`イベントはジェスチャーイベントのミックスインに含まれていますが、最新のモバイルブラウザではもはや必要とされません。
{.alert .alert-info}

## ジェスチャーイベントの使用

ハイブリッドエレメントを使用する場合は、デフォルトでジェスチャーイベントがサポートされます。`Polymer.Element`をベースにクラススタイルで作成したエレメントでは、`Polymer.GestureEventListeners`ミックスインをインポートすることで、ジェスチャサポートを明示的に追加する必要があります。

```html
<link rel="import" href="polymer/lib/mixins/gesture-event-listeners.html">

<script>
    class TestEvent extends Polymer.GestureEventListeners(Polymer.Element) {
      ...
</script>
```

ジェスチャーイベントにはいくつか追加の設定が必要なため、単純に一般的な`addEventListener`メソッドを用いてリスナーを追加することはできません。ジェスチャーイベントをリッスンするには、：

- ジェスチャーイベントの一つに[アノテーション付イベントリスナー](events#annotated-listeners)を使用する。

    ```html
    <div id="dragme" on-track="handleTrack">Drag me!</div>
    ```

    アノテーション付イベントリスナーを使用した場合、Polymerは自動的にジェスチャーイベントを特別に記録するようになります。

- `Polymer.Gestures.addListener`/`Polymer.Gestures.removeListener`メソッドを使用する

    ```js
    Polymer.Gestures.addListener(this, 'track', e => this.trackHandler(e));
    ```

    この`Polymer.Gestures.addListener`メソッドを使用して、ホストエレメントにリスナーを追加できます。

### ジェスチャーイベントのタイプ

サポートされるジェスチャーイベントのタイプは次のとおりです。各タイプについて簡単な説明と`event.detail`から取得できる詳細なプロパティを列挙して紹介します。：

- **down**：指/ボタンが下げられた
    - `x`：イベントのclientX座標
    - `y`：イベントのclientY座標
    - `sourceEvent`：`down`アクションを最初に発生させたDOMイベント
- **up**：指/ボタンが上げられた
    - `x`：イベントのclientX座標
    - `y`：イベントのclientY座標
    - `sourceEvent`：`up`アクションを最初に発生させたDOMイベント
- **tap**：ダウン＆アップが発生した
    - `x`：イベントのclientX座標
    - `y`：イベントのclientY座標
    - `sourceEvent`：`tap`アクションを最初に発生させたDOMイベント
- **track**：指/ボタンが下げながら動いた
    - `state`：トラッキング状態を示す文字列：
        - `start`：トラッキングが最初に検出された時に発生(指/ボタンが押され、事前に設定された距離の閾値を超えて移動した時）
        - `track`：トラッキングの最中に発生
        - `end`：トラッキングが終了した時に発生
    - `x`：イベントのclientX座標
    - `y`：イベントのclientY座標
    - `dx`：トラックイベントの開始時から水平方向にピクセル単位で生じた変化
    - `dy`：トラックイベントの開始時から垂直方向にピクセル単位で生じた変化
    - `ddx`：トラックイベントの終了時からから水平方向にピクセル単位で生じた変化
    - `ddy`：トラックイベントの終了時からから垂直方向にピクセル単位で生じた変化
    - `hover()`：現在ホバーされているエレメントを判断するために呼び出される可能性のあるメソッド

### 例

宣言的イベントリスナーの例 { .caption }

```html
<link rel="import" href="polymer/polymer-element.html">
<link rel="import" href="polymer/lib/mixins/gesture-event-listeners.html">

<dom-module id="drag-me">
  <template>
    <style>
      #dragme {
        width: 500px;
        height: 500px;
        background: gray;
      }
    </style>

    <div id="dragme" on-track="handleTrack">[[message]]</div>
  </template>

  <script>
    class DragMe extends Polymer.GestureEventListeners(Polymer.Element) {

      static get is() {return 'drag-me'}

      handleTrack(e) {
        switch(e.detail.state) {
          case 'start':
            this.message = 'Tracking started!';
            break;
          case 'track':
            this.message = 'Tracking in progress... ' +
              e.detail.x + ', ' + e.detail.y;
            break;
          case 'end':
            this.message = 'Tracking ended!';
            break;
        }
      }

    }
    customElements.define(DragMe.is, DragMe);
  </script>
</dom-module>
```

命令的なイベントリスナーの例 { .caption }

以下の例では、ホストエレメントに対してリスナーを追加するのに`Polymer.Gestures.addListener`を使用しています。このようなケースでは、アノテーション付イベントリスナーで設定することができません。もし、リスナーがホストエレメントまたはShadow DOMの子に対して設定されている場合には、通常、一度追加したイベントリスナーに関してその削除について心配する必要はありません。

動的に追加された子にイベントリスナーを設定する場合には、子を削除する時点で、`Polymer.Gestures.addListener`によって設定されたイベントリスナーを削除する必要があるかもしれません。これは、子エレメントがガベージコレクトされるようにするためです。

```html
<link rel="import" href="../../bower_components/polymer/polymer-element.html">
<link rel="import" href="../../bower_components/polymer/lib/mixins/gesture-event-listeners.html">

<dom-module id="drag-me-app">
  <template>
    <style>
      :host {
        border: 1px solid blue;
        background: gray;
      }
    </style>
    [[message]]
  </template>

  <script>
    class DragMeApp extends Polymer.GestureEventListeners(Polymer.Element) {
      static get is() { return 'drag-me-app'; }
      static get properties() {
        return {
          message: {
            type: String,
            value: "Select my text. I will track you."
          }
        };
      }
      constructor() {
        super();
        Polymer.Gestures.addListener(this, 'track', e => this.handleTrack(e));
      }
      handleTrack(e) {
        switch(e.detail.state) {
          case 'start':
            this.message = 'Tracking started!';
            break;
          case 'track':
            this.message = 'Tracking in progress... ' +
              e.detail.x + ', ' + e.detail.y;
            break;
          case 'end':
            this.message = 'Tracking ended!';
            break;
        }
      }
    }
    customElements.define(DragMeApp.is, DragMeApp);
  </script>
</dom-module>

```

## ネイティブブラウザのジェスチャーイベントの処理 {#gestures-and-scroll-direction}

ブラウザは、特定のジェスチャーに対するネイティブな処理を実装しており、タッチによるスクロールやユーザーによるピンチジェスチャーによるコンテンツのズームなどをサポートしています。

ジェスチャーイベントをリッスンすると、デフォルトではブラウザネイティブなジェスチャー処理は無効になります。例えば、`track`イベントのリスナーを持つノードは、ブラウザがスクロールやピンチズームのジェスチャーを処理しないようにします。

Polymerのジェスチャーイベント<em>に加えて</em>ネイティブなジェスチャー処理を利用したい場合は、 `Polymer.Gestures.setTouchAction`関数を使用することで、ブラウザがどのイベントをネイティブに処理するか指定できます。例えば、ブラウザによる垂直方向のスクロールをサポートし、エレメントが左右スワイプを処理できるようにするには次のように記述します。：

```js
constructor() {
  super();
  Polymer.Gestures.addListener(this, 'track', this.handleTrack);
  // Let browser handle vertical scrolling and zoom
  Polymer.Gestures.setTouchAction(this, 'pan-y pinch-zoom');
}
```

`setTouchAction`の最初の引数は、リスナーがアタッチされているノードです。 2番目の引数は `touch-action` CSSプロパティの有効な値です。

`touch-action`キーワードの完全なリストについては、<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action" data-md-type="link">`touch-action` on MDN</a>を参照してください。

イベントリスナー**を追加した後は、いつでも `setTouchAction`を呼び出すことができます**。この変更は、`setTouchAction`が呼び出さル時、実行中のジェスチャーには影響しません。

ネイティブジェスチャー処理が有効になっているときは、ブラウザによっては、Polymerのジェスチャーイベントが発生する可能性があります。ジェスチャーイベントが発生すると、ネイティブブラウザの処理の前にリスナーが呼び出されます。イベントで `preventDefault`を呼び出すことで、ネイティブブラウザの処理を防ぐことができます。

```js
handleTrack(e) {
  // do something
  ...
  // suppress native scrolling
  e.preventDefault();
}
```

ジェスチャーイベントリスナーがスクロールパフォーマンスを**妨げないように**したい場合は、次のセクションで説明するように、すべてのジェスチャーイベントリスナーを強制的に<em>passive</em>にします。

### passiveなジェスチャーリスナーを使う

アプリケーションは `Polymer.setPassiveTouchGestures（true）`を呼び出すことで、ジェスチャー関連の全てのイベントリスナーを強制的に<em>passive</em>にすることができます。passiveイベントリスナーは、`preventDefault`を呼び出してデフォルトのブラウザ処理を妨げることはないので、ブラウザはイベントリスナーから戻るのを待たずにネイティブのジェスチャーを処理できます。

ジェスチャー関連のイベントリスナーを設定する前に`setPassiveTouchGestures`を呼び出す必要があります。例えば、アプリケーションのエントリーポイントや、メインのアプリケーションエレメントの`constructor`で設定するようにします（常に最初にロードされるエレメントで呼び出す必要があります）。

passiveタッチジェスチャーを使用すればスクロールのパフォーマンスは向上しますが、アプリケーション内のいずれかのエレメントのジェスチャーで`preventDefault`が呼び出される可能性があれば問題が発生するかもしれません。
