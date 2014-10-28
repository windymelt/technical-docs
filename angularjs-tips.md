# AngularJS tips
* 各種の(e.g. controller).jsファイルは`index.html`で認識される。
* Yeomanを使っているときは、`grunt build`を実行すると`dist`ディレクトリが作成され、配布用のファイルが生成される
* GithubのレポジトリをBowerの依存性に直接指定できる。記法は`user/repo`と書くだけでよい。
* AngularJSのスクリプトの追加はyeomanで行うことができる。
    例として、`$ yo angular:service myService`でサービスのjsファイルとテストのjsファイルが作成される。
    cf. [http://qiita.com/tetsuya/items/a488b66a88369307a213]
* テスト用のファイルには別途モジュール依存性を記述しなければならない。
