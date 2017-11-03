---
title: "Step 5: カスタムCSSプロパティによるテーマ設定"
subtitle: "Polymer Elementを作ろう"
---

ここまでで基本的な機能を持ったボタンができました。しかし、それは押された状態と押されていない状態の両方に既存のテキストカラーを使用したまま止まっています。もうちょっと装飾されたものが欲しいと思ったらどうすれば良いのでしょうか？

Shadow DOMはユーザーが誤ってエレメントの内部をスタイリングしてしまうのを防ぐのに役立ちます。_カスタムCSSプロパティ_ を使用することでコンポーネントにスタイルを設定できるようになります。
Polymerは、[カスケーディング変数のCSSカスタムプロパティ](http://www.w3.org/TR/css-variables/)仕様からインスピレーションを得たカスタムCSSプロパティを実装しています。

`var`関数を使用してエレメント内にカスタムプロパティを適用します。

<pre><code>background-color: var(<em>--my-custom-property</em>, <em>defaultValue</em>);</pre></code>

<code>--<em>my-custom-property</em></code>はカスタムプロパティ名で、常に2つのダッシュ（ - ）で始まり、
<code><em>defaultValue</em></code>はカスタムプロパティが設定されていない場合に使用される（オプションの）CSSの値です。

## Add new custom property values

エレメントの`<style>`タグ内を編集し、`fill`と`stroke`の値をカスタムプロパティで置き換えます:

icon-toggle.html: Before  { .caption }

```
  <style>
    /* local styles go here */
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

icon-toggle.html: After  { .caption }

```
  <style>
    /* local styles go here */
    :host {
      display: inline-block;
    }
    iron-icon {
      fill: var(--icon-toggle-color, rgba(0,0,0,0));
      stroke: var(--icon-toggle-outline-color, currentcolor);
    }
    :host([pressed]) iron-icon {
      fill: var(--icon-toggle-pressed-color, currentcolor);
    }
  </style>
```

デフォルト値のおかげで、`color`を設定するだけで`<icon-toggle>`のスタイルを変更することができますが、他にもオプションがあります。

`demo`フォルダから`demo-element.html`を開き、新しいプロパティを設定します。

demo-element.html: Before { .caption }

```
    <style>
      :host {
        font-family: sans-serif;
      }
    </style>
```

demo-element.html: After { .caption }

```
    <style>
      :host {
        font-family: sans-serif;
        --icon-toggle-color: lightgrey;
        --icon-toggle-outline-color: black;
        --icon-toggle-pressed-color: red;
      }
    </style>
```

デモをもう一度実行してカラフルなエレメントを手に入れましょう。


<img src="/images/2.0/first-element/toggles-styled.png" alt="Demo showing
icon toggles with star and heart icons. Pressed icons are red.">

それでおしまいです - エレメントが完成しました。基本的なUI、API、カスタムスタイルプロパティを持つエレメントが作成されました。

もしエレメントの動作に問題がある場合は、[完成版](https://github.com/PolymerLabs/polymer-2-first-element/tree/master/icon-toggle-finished)をチェックしてください。

カスタムプロパティについてもう少し詳しく知りたければ、以下の説明を読んでください。

## Extra credit: ドキュメントレベルでカスタムプロパティを設定する

たとえば、ドキュメントレベルでカスタムプロパティの値を定義して、アプリケーション全体のテーマを設定したいことがよくあります。
カスタムプロパティはほとんどのブラウザに組み込まれていないため、Polymer Elementの外でカスタムプロパティを定義するには、特別な`custom-style`タグを使用する必要があります。
`index.html`ファイルの`<head>`タグの中に次のコードを追加してみてください：

```
<custom-style>
  <style>
    /* Define a document-wide default—will not override a :host rule in  */
    html {
      --icon-toggle-outline-color: red;
    }
    /* Override the value specified in demo-element */
    demo-element {
      --icon-toggle-pressed-color: blue;
    }
    /* This rule does not work! */
    body {
      --icon-toggle-color: purple;
    }
  </style>
</custom-style>
```

重要事項:

*   `demo-element`セレクタは`demo-element`エレメントと一致し、`demo-element`の`html`ルールよりも **高い特異性** を持つため、そこで値を上書きします。

*   カスタムプロパティは、`html`セレクタ **またはPolymer Custom Element** と一致するルールセットの中で **のみ** 定義できます。これは、Polymerに実装されているカスタムプロパティの制限です。

デモをもう一度実行すると、押されたボタンは青色になりますが、**メインカラーとアウトラインカラーは変更されていません**。

`--icon-toggle-color`プロパティは`body`タグに適用できないため、設定されません。このルールを`demo-element`ブロックに移動して、適用されていることを確認してください。

`html`ルールセットは、`--icon-toggle-outline-color`のドキュメント全体のデフォルト値を作成します。
しかし、この値は、`demo-element`エレメント内の対応するルールによってオーバーライドされます。
このデフォルト値での動作を確認するには、`demo-element.html`の対応するルールをコメントアウトしてください:

demo-element.html { .caption }

```
    <style>
      :host {
        font-family: sans-serif;
        --icon-toggle-color: lightgrey;
        /* --icon-toggle-outline-color: black; */
        --icon-toggle-pressed-color: red;
      }
    </style>
```

最後に、`custom-style`内のセレクタと一致させるには、エレメントが **ドキュメントスコープ内**
(たとえば、`index.html`など。別のエレメントのShadow DOMの中ではない。)になければならないことに注意してください。
たとえば、これらのルールは`custom-style`内では機能 **しません**:

```
    iron-icon {
      --iron-icon-width: 40px;
      --iron-icon-height: 40px;
    }
```

これは、この`iron-icon`エレメントが別の要素のShadow DOMの内側にあるためです。
ただし、カスタムプロパティはツリーを継承しているため、これらのプロパティをドキュメントレベルで設定して、ページ上のすべての`iron-icon`エレメントのサイズを設定することができます:

```
    html {
      --iron-icon-width: 40px;
      --iron-icon-height: 40px;
    }
```

詳細については、[カスタムCSSプロパティ](https://www.polymer-project.org/2.0/docs/devguide/custom-css-properties)に関するドキュメントを参照してください。

あなたオリジナルのエレメントを使い始める準備はできていますか？Polymer CLIを使用して[エレメントのプロジェクトを作成することができます](/2.0/docs/tools/polymer-cli#element)。

また、Polymer App Toolboxを使用してアプリケーション開発をスタートするには
[Build an app](/2.0/start/toolbox/set-up)のチュートリアルを見てみましょう。

または前のセクションを見直してください:

<a class="blue-button" href="step-4">
  Previous Step: React to input
</a>
