# AngularJS Injector & Mock
* 今まで見たサイトでは http://southdesign.de/blog/mock-angular-js-modules-for-test-di.html が理解しやすかった。

## 用語
### モック
本来の要素の代わりをするもの。ネットワークやデータベース等、複雑でテストの妨げとなるものをモックで置換することでテストの風通しを良くする。

## 解説じみたもの

コントローラが依存するサービス等をモックするには、テストコード中の`beforeEach`等の部分で`inject`を呼び出し、モックサービスと置換する。Angularは同じ名前を持つ要素をFILO方式(最後に宣言したものが使われる？)で上書きする。すなわち、何かサービスを宣言した後で同じ名前のサービスをロードすると、最後に宣言したものが最初のものと取り代わって_注入_される。

以下のように記述したとする。
```javascript
var someFunc;

beforeEach(function(){
  module('module');
  module('moduleMock');
  inject(function($injector) {
      someFunc = $injector.get('someFunc');
    });
});
```
ここで`beforeEach`では二度moduleが宣言されている。`moduleMock`には`module`と同名のモック用サービス`someFunc`が記述されている。これを本来のモジュールよりも後でロードすることで、Angularはモジュール内のサービス等を上書きする。そしてテスト対象で使用される、`module`で宣言されているサービス`someFunc`が`$injector`によってインスタンス化されモック用のものと取り代わる。左辺が実際にテストされるモジュールで使われるサービスであり、右辺は`$injector`がインスタンス化するモックサービスである。

`$injector`はモジュールのインスタンス化を行う。`inject`はインスタンス化されたサービス等をテスト対象に注入する。

```javascript
inject(function (_someFunc_) {
  someFunc = _someFunc_
});
```

injection部分は、上記のように記述することもできる。アンダースコアで名前を囲むと、名前の衝突を回避しつつ同様のインスタンス化の記述ができる。

## モックの記述
モックモジュールの記述も通常のモジュール通り行うが、新たにモジュールを作るので宣言方法がやや変化する。依存性の記述が増えているのが分かるだろうか？

```javascript
angular.module('hogehogeMock').service(...)     // NG: There's no module 'hogehogeMock'
angular.module('hogehogeMock', []).service(...) // OK: Creating new module
```
