# AngularJS tips
* 各種の(e.g. controller).jsファイルは`index.html`で認識される。
* Yeomanを使っているときは、`grunt build`を実行すると`dist`ディレクトリが作成され、配布用のファイルが生成される
* GithubのレポジトリをBowerの依存性に直接指定できる。記法は`user/repo`と書くだけでよい。
* AngularJSのスクリプトの追加はyeomanで行うことができる。
    例として、`$ yo angular:service myService`でサービスのjsファイルとテストのjsファイルが作成される。
    cf. [http://qiita.com/tetsuya/items/a488b66a88369307a213]

## Test
* テスト用のファイルには別途モジュール依存性を記述しなければならない。
* サービスやコントローラ等は、既にモジュールを引数として受け取るDIになっているため、テストの際はテスト側からモックを送り込めばよい。
* (単体)テストはJasmineを利用して行う。
* `describe`で1つのメソッドを検査する？
* `describe`の第一引数には名前、第二引数にはテスト対象の無名関数を記述する
* `it(...)`で一つのテストを行う
* `it`の第一引数には名前、第二引数にはテストの内容である無名関数を記述する
* テストの判定は`expect(...).toEqual(...)`のように記述する
* 前後処理は`beforeEach()`, `afterEach()`を用いて記述する

### Assertion method
* `toEqual`は`==`に相当する
* `toBe`は`===`に相当する
* 正規表現にマッチするかのアサーションでは`toMatch(/regexp/)`を使う
* `toBeDefined`, `toBeUndefined`, `toBeNull`, `toBeTruthy`, `toBeFalsy`, `toContain(hoge)`, `toBe{Less | Greater}than(hoge)`などが利用できる
