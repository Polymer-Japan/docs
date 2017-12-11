---
subtitle: 機能の概要
title: Polymerライブラリ
---

PolymerライブラリはCustom Elementsを作成するための機能一式を提供します。これらの機能は、標準のDOMエレメントのように動作するCustom Elementsを迅速かつ容易に作成できるように設計されています。標準的なDOMエレメントと同様、Polymer Elementでは以下のようなことが可能です。：

- コンストラクタまたは`document.createElement`を使用して命令的にインスタンス化する。
- 属性またはプロパティを利用してエレメントの設定を行う。
- 各インスタンス内部にDOMを追加する。
- プロパティや属性の変化に応じて処理を行う（オブザーバー）。
- 内部のデフォルトや外部からスタイルを設定する。
- 内部の状態を操作するメソッドに応答して処理する。

Polymer Elementの基本的な定義は以下のようになります。：

```
    <dom-module id="x-custom">
      <!-- 任意のshadow DOMテンプレート -->
      <template>
        <style>
          /* エレメントへ適用するCSSルール */
        </style>

        <!-- エレメントのshadow DOM -->

        <div>{{greeting}}</div> <!-- ローカルDOM内におけるデータバインディング -->
      </template>

      <script>
        // ES2015のクラス構文を使ってエレメントのAPIを定義する
        class XCustom extends Polymer.Element {

          static get is() { return 'x-custom'; }

          // エレメントのパブリックAPIとしてプロパティを宣言する
          static get properties() {
            return {
              greeting: {
                type: String,
                value: "Hello!"
              }
            }
          }

          // エレメントのパブリックAPIとしてメソッドを追加する
          greetMe() {
            console.log(this.greeting);
          }

        }

        // ブラウザにx-customエレメントを登録する
        customElements.define(XCustom.is, XCustom);
      </script>

    </dom-module>
```

このドキュメントでは、機能を次のグループに分けて解説しています。：

-   [Custom Elements](custom-elements)：エレメントを登録することで、classがCustom Element名に関連付けられます。エレメントには、そのライフサイクルを管理するコールバックが備わっています。また、Polymerでは、プロパティを宣言することで、エレメントのプロパティAPIをPolymerのデータシステムに統合することもできます。

-   [Shadow DOM](shadow-dom)：Shadow DOMは、エレメント内にカプセル化されたローカルのDOMツリーを提供します。PolymerはDOMテンプレートからShadow Treeを自動的に生成しエレメントに挿入することができます。

-   [イベント](events)：Polymerは、Shadow DOMの子にイベントリスナーをアタッチするための宣言的構文を提供します。また、ジェスチャー関連イベントを扱うためのオプションのライブラリも提供されます。

- [データシステム](data-system)：Polymerのデータシステムは、プロパティオブザーバー(Property Observer)や算出プロパティ(Computed Properties)といった機能によって、プロパティや属性へのデータバインディングを提供しています。

Polymer 1.xベースの既存のエレメントを新しいAPIにアップグレードする場合は、[Upgrade guide](/2.0/docs/upgrade)を参照してください 。

リリースの最新の状況を確認したい場合は、[Release notes](/2.0/docs/release-notes)を参照してください。
