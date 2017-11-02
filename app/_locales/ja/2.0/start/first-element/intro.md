---
title: "Step 1: セットアップ"
subtitle: "Polymer Elementを作ろう"
---

<!-- toc -->

このチュートリアルでは、Polymer 2.0を使用してエレメントを作成する方法を学習します。シンプルなPolymer Elementであるトグルボタンを作成します。完成したボタンは次のようになります:

![Sample star-shaped toggle buttons, showing pressed and unpressed
state](/images/2.0/first-element/sample-toggles.png)

次のような簡単なマークアップでトグルボタンを使用することができます:

```html
<icon-toggle></icon-toggle>
```

Polymerを扱う際のキーコンセプトの多くをこの課題で紹介します。

すべてを理解していなくても心配しないでください。ここに示す各コンセプトについては、Polymerのドキュメントで詳しく説明しています。

## Step 1: セットアップを始める

このチュートリアルに行うには、以下のものが必要です。

- [git](https://git-scm.com/downloads)。
- [GitHub上で手に入る](https://github.com/PolymerLabs/polymer-2-first-element.git)スタートコード。
- デモを実行するための[Polymer CLI](/2.0/docs/tools/polymer-cli)。

### スタートコードをダウンロードする

1.  このコマンドを実行してスタートコードをダウンロードします:

    ```bash
    git clone https://github.com/PolymerLabs/polymer-2-first-element.git
    ```

2.  プロジェクトのフォルダを開きます:  

    ```bash
    cd polymer-2-first-element
    ```

    プロジェクトのフォルダは次のようになっているはずです:

    <pre>
    README.md
    bower.json
    demo/
    icon-toggle-finished/
    icon-toggle.html
    index.html
    </pre>

    メインのファイルは`icon-toggle.html`で、Custom Elementを定義しています。

### Polymer CLIをインストールする

デモをローカルで実行するためにPolymer CLIをインストールします。

Polymer CLIはNode.js、npm、git、Bowerを必要とします。詳細なインストール手順については、[Polymer CLIのドキュメント](/{{{polymer_version_dir}}}/docs/tools/polymer-cli)を参照してください。

下記コマンドでPolymer CLIをインストールします:

   ```bash
   npm install -g polymer-cli
   ```

### 依存しているパッケージをインストールしてデモを実行する

To install the element's dependencies and run the demo:

1.  プロジェクトディレクトリで `bower install` を実行します:

        bower install

    これにより、Polymerライブラリやその他のWeb Componentsの使用に必要な、コンポーネントと依存しているパッケージがインストールされます。

    プロジェクトディレクトリに `bower_components` という名前のフォルダが追加されているのが確認できます:

    <pre>
    README.md
    bower.json
    bower_components
    demo/
    icon-toggle-finished/
    icon-toggle.html
    index.html
    </pre>

2.  Polymerの開発用サーバーをプロジェクトディレクトリから実行します:

        polymer serve --open

    icon-toggleが表示されるはずのところにテキストが表示されるのが確認できます。それはあまり面白く見えませんが、すべてが機能していることを示しています。

    (このURLには実際のディレクトリ名ではなく、このエレメントの `bower.json` にリストされているコンポーネント名である `icon-toggle` が含まれていることに注意しましょう。なぜ `polymer serve` がこれを行うのか疑問に思うなら、[HTMLのインポートと依存関係の管理](/2.0/docs/tools/polymer-cli#element-project-layout)を参照してください。)

<img src="/images/2.0/first-element/starting-state.png" alt="Initial state of the demo. The demo
shows three icon-toggle elements, two labeled 'statically-configured icon toggles' and one labeled
'data-bound icon toggle'. Since the icon toggles are not implemented yet, they appear as
placeholder text reading 'Not much here yet'." title="Initial demo">

もしすべてうまく行っていたら、[step 2](step-2)へ進みます.

<a class="blue-button" href="step-2">Step 2: Add local DOM</a>
