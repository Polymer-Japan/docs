---
title: Polymer 2.xのインストール
---

<!-- toc -->

Polymerについて詳しく知っていて、新しくリリースされたバージョンを使い始めるなら、あなたは適切な場所にいます！もしPolymerプロジェクトとWebコンポーネントの概要については知りたいなら:

* [Polymerのクイックツアーを受ける](/{{{polymer_version_dir}}}/start/quick-tour)
* [初めてのPolymerアプリケーションを構築する方法を学ぶ](/{{{polymer_version_dir}}}/start/toolbox/set-up)
* [Polymerライブラリを使って初めてのエレメントを構築する方法を学ぶ](/{{{polymer_version_dir}}}/start/first-element/intro)

Polymerは[Bower](https://bower.io/)を使用して配信されます。

アプリケーションテンプレートを作成してPolymerを自動的にインストールするには、[Polymer CLIを使用します](#use-cli)。

プロジェクトを一から始めるには、[Bowerを使用してPolymerをインストールします](#use-bower)。

### Polymer CLIを使用してアプリケーションテンプレートを作成し、Polymerをインストールする {#use-cli}

Polymer CLIを使うには、Node.js、npm、Git、Bowerが必要です。詳細なインストール手順については、[Polymer CLIのドキュメント](../docs/tools/polymer-cli)を参照してください。

1. Polymer CLIをインストールする

    ```bash
    npm install -g polymer-cli
    ```

3. Polymer 2.0のテストフォルダを作成し、そのフォルダに移動する

    ```bash
    mkdir polymer-20-test
    cd polymer-20-test
    ```

4. プロジェクトの初期化

    ```bash
    polymer init
    ```

5. `polymer-2-application`を選択する

6. プロジェクトを起動する

    ```bash
    polymer serve
    ```

### Bowerを使用してPolymerをインストールする {#use-bower}

1. Bowerをインストールする

    ```bash
    npm install -g bower
    ```

2. Polymer CLIをインストールする

    Polymer CLIを使うには、Node.js、npm、Git、Bowerが必要です。詳細なインストール手順については、[Polymer CLIのドキュメント](../docs/tools/polymer-cli)を参照してください。

    ```bash
    npm install -g polymer-cli
    ```

3. 最新のPolymer 2.0 Bowerからインストールする

    ```bash
    bower install Polymer/polymer#^2.0.0
    ```

4. テスト用の`index.html`ファイルを作成し、下記コードを`<head>`タグ内に追加する:
  - `<script src="/bower_components/webcomponentsjs/webcomponents-loader.js"></script>` to
  load the polyfills
  - `<link rel="import" href="/bower_components/polymer/polymer.html">` to
  import Polymer

5. 必要なエレメントをインポートして使用する

6. プロジェクトを起動する

    ```bash
    polymer serve
    ```

本番用のプロジェクトの構築については、[本番用のPolymerアプリケーションの構築](../toolbox/build-for-production)に関するドキュメントを参照してください。
