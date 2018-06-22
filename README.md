## 日本語ドキュメント

このリポジトリは [Polymer/docs](https://github.com/Polymer/docs) を Fork しています。

## 翻訳について

翻訳の進め方についてはWikiに詳細をまとめています。  
https://github.com/Polymer-Japan/docs/wiki

### 翻訳ファイルの作成

翻訳作業は[GitLocalize](https://gitlocalize.com/repo/473)を使用して行います。  
直接Markdownファイルを編集するとGitLocalizeと衝突してしまうので、**必ずGitLocalize上で編集するようにしてください**。

GitLocalizeで翻訳ファイルを作成すると、`app` ディレクトリと同じファイルパスで `app/_locales/ja` の中にMarkdownが作成されます。

e.g.

| 対象 | ファイルパス |
|------------|---------------------------------|
| オリジナル | app/2.0/docs/es6.md |
| 日本語 | app/_locales/ja/2.0/docs/es6.md |

---

## Polymer documentation site

https://www.polymer-project.org/

### Install

The documentation site runs in Google App Engine, using the App Engine Python standard environment. Before you start
you'll need the following prerequisites:

-   Python 2.7
-   [Google Cloud SDK](https://cloud.google.com/sdk/)
-   App Engine Python standard environment. Ensure this is installed by running the following command:

        gcloud components install app-engine-python
        
    Or, if you installed via apt:
    
        sudo apt-get install google-cloud-sdk-app-engine-python

Set up your repo:

    git clone https://github.com/Polymer/docs 
    cd docs
    npm install


### Running the site

The first time you run the site, run `gulp` to build the site in its entirety:

    gulp

Then start the App Engine dev server on `dist/app.yaml`:

    dev_appserver.py dist/

The site will be served from http://localhost:8080.

#### Making changes / watching files / live reload

If you're making changes use the `watch` task. Optionally add `--reload` to live
reload the tab when changes are saved.

    gulp watch --reload

The site will be served on http://localhost:3000. Making changes will refresh
the browser tab.

Optional flags:

- `--reload`: refreshes the browser tab when changes are made
- `--open`: opens a new browser tab when `gulp watch` is started

**Tip** - run `gulp help` to see the list of available gulp tasks.

### Run tests

Install [WebTest framework](http://webtest.pythonpaste.org/en/latest/):

    pip install WebTest

Then run:

    npm test

If your Google Cloud SDK isn't installed in `~/google-cloud-sdk`, set the `CLOUD_SDK` environment
variable to the path to the Cloud SDK:

    export CLOUD_SDK=~/cloud/google-cloud-sdk


### Deployment

Build and deploy version `YYY-MM-DD` of the site:

    gulp
    npm run deploy YYYY-MM-DD

