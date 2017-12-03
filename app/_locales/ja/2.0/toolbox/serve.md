---
title: アプリケーションの配信
---

<!-- toc -->

ユーザーは、任意のサーバーテクノロジーを使用してApp Toolboxアプリケーションを配信できます。 [Polymer CLI](/%7B%7B%7Bpolymer_version_dir%7D%7D%7D/docs/tools/polymer-cli)ビルドプロセスは、最新のWebテクノロジーを活用できる高速ロードアプリケーションをサポートします。

`polymer build`コマンドのデフォルト出力は、HTTP / 2およびHTTP / 2サーバープッシュをサポートしているサーバー/ブラウザーの組み合わせ用に設計されたunbundled buildで、キャッシュの最適化をしつつ高速な初期描画に必要なリソースを提供します。

詳細は、[PRPLパターンのドキュメント](prpl)を参照してください。

サーバープッシュをサポートしないサーバーとブラウザーの組み合わせの場合、アプリケーションを実行するために必要なラウンドトリップの回数を最小限に抑えるように設計されたbundled buildを生成できます。

`--bundled` フラグを使ってbundled buildを作成します：

```
polymer build --bundle
```

## Dynamic serving

Dynamic serving（ブラウザごとに異なるコンテンツを配信すること）を実行する必要がある場合、multiple buildsが選択されることになります。multiple buildsを生成するには、それらを[polymer.json](/%7B%7B%7Bpolymer_version_dir%7D%7D%7D/docs/tools/polymer-json)ファイルで設定します。

multiple buildsを使用する場合、サーバーロジックは、通常、ブラウザから送信されたユーザーエージェント文字列を調べることによって、各ブラウザに対して適切なビルドを提供する必要があります。

次の場合、Dynamic servingを実行する必要があります：

- あるユーザーにはbundled buildを配信し、他のユーザーにはunbundled buildを配信したい場合。
- あるユーザーにはES6を配信し、他のユーザーにはES5を配信したい場合。

詳細については、[プロダクション用のアプリケーションビルド](build-for-production)に関するドキュメントを参照してください。
