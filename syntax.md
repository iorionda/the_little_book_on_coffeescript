# CoffeeScriptの文法

## コメント

``` coffee
# これはコメント
```

``` coffee
###
    複数行のコメント
###
```

## 空白の扱い

* CoffeeScriptでは空白が重要
  * 中括弧`{}`をタブで置き換えるため。
  * これによりスクリプトが同じ形式でコンパイルされることを保障する。

## 変数とスコープ

### ローカル変数
* CoffeeScriptは単純にグローバル変数をなくした。
* 裏側ではCoffeeScriptはスクリプトを無名関数でラップすることでローカルコンテキストを保持している。
* 自動的にすべての変数宣言に `var` をつけている。

``` coffee
myVariable = "test"
```

というコードをコンパイルすると以下のような`javascript`コードになる。

``` javascript
var myVariable;
myVariable = "test";
```

### グローバル変数
グローバル変数をつくるにはグローバルオブジェクトのプロパティとして直接指定するか、次のパターンを用いる。

``` coffee
exports = this
exports.MyVariabla = "foo-bar"
```

root コンテキストでは `this` はグローバルオブジェクトに等しくなる。

## 関数

* CoffeeScriptは `function` 文を削除し、`->` に置き換えた。
* 関数は1行でもいいし、インデントを行なって記述することも可能。
* 関数の最後の式が暗黙的に返り値になるため `return` 文を使う必要はない。
* 関数の途中で値を返すときには `return` 文を使う。

``` coffee
func = -> "bar"
```

``` coffee
func = ->
  # 余計な行
  "bar"
```

## 関数引数

* CoffeeScript では矢印の前で、括弧の中に引数を指定する。

``` coffee
time = (a, b) -> a * b
```

* CoffeeScript ではデフォルト引数もサポートしている

``` coffee
times = (a = 1, b = 2) -> a * b
```

* 可変長引数を利用するのにスプラットを用いることも可能

``` coffee
sum = (nums...) ->
  result = 0
  nums.forEach(n) -> result += n
  result
```

``` coffee
trigger = (event...) ->
  events.splice(1, 0, this)
  this.constructor.trigger.apply(events)
```

## 関数の実行
関数の実行はJavaScriptと全く同じように実行することが可能。
しかし、RubyのようにCoffeeScriptは関数が最低1つの引数と実行されれば、自動的に関数を呼び出す。

``` coffee
a = "Howdy!"

alert a
# alert(a) と同じ

alert inspect a
# alert(inspect(a)) と同じ
```

括弧は必須ではないが、何がどんな引数と実行されるかがすぐにわかる場合は使用するべき。

``` coffee
alert inspect(a)
```

もしも実行時に1つも引数を渡さない場合、CoffeeScriptは関数を実行しているのか、変数として取り扱っているのかを判断できない。
なので、関数を引数なしで実行する場合には十分に気をつけて括弧をつける必要がある。

## 関数コンテキスト
コンテキストの変化を管理するために、CoffeeScript ではいくつかのヘルパが用意されている。
そのようなヘルパのバリエーションのひとつとして太った矢印(ファットアロー) `=>` の関数がある。
細い矢印の代わりにファットアローを用いることで、関数コンテキストがローカルのものに紐付けることを確実になる。

``` coffee
this.clickHandler = -> alert "clicked"
element.addEventListener "click", (e) => this.clickHandler(e)
```
これを行いたい理由は`addEventListener()`からのコールバックは通常 `element` のコンテキストで実行されるからだ。
すなわち、`this`は`element`と等しくなる。
もしも `self = this` のような面倒な操作をせずに`this`をローカルコンテキストに留めたい場合はファットアローを用いる。


## オブジェクトの構文と配列の定義
オブジェクト構文はJavaScriptとまったく同じように指定することが可能。
中括弧のペアやキー/バリューの文を使う。
しかし、関数実行と同じようにCoffeeScriptは中括弧を省略することが可能になっている。
また、インデントを利用することもできるし改行をカンマの代わりに使うこともできる。

``` coffee
object1 = {one: 1, two: 2}

# 中括弧なしで
object2 = one:1, two: 2

# 改行をカンマの代わりにする
object3 =
  one: 1
  two: 2

User.create(name: "Jhon Smith")
```

同様に配列はインデントをカンマの代わりに区切りに使うことができる。
ただし、角括弧`[]`は必須となる。

``` coffee
array1 = [1, 2, 3]
array2 = [
  1
  2
  3
]

array3 = [1,2,3,]
```

上の`array3`の例でもわかるように、CoffeeScriptは配列末尾のカンマ(trailing comma)を取り除く。


## フローコントロール
CoffeeScript の括弧が必須ではないという条件は`if`と`else`キーワードでも同様だ。

``` coffee
if true == true
  "We're ok"

if true != true then "Panic"

# 以下と同じ意味になる
# if (1 > 0) ? "OK" : "Y2K!"
if 1 > 0 then "OK" else "Y2K!"
```

上の例でもわかるが、`if`文が1行であるなら`then`キーワードを用いる必要がある。
そうすることで CoffeeScript にブロックの始まりを伝えることができる。
また条件演算子(?:)はサポートされていない。

CoffeeScript はまた、Rubyで採用されている後置if を利用することが可能となっている。

``` coffee
alert "It's cold!" if heat < 5
```

否定にびっくりマークを用いる代わりに`not`キーワードを用いることができる。
それを用いることでコードをより読みやすくなる。

``` coffee
if not true then "Panic!"
```

上の例では `unless` 文を `if` の反対として使用することができる。

``` coffee
unless true
  "Panic!"
```

not と似た感覚で CoffeeScript は `is` 文も持っている。
`is` 文は `==` に変換される。

``` coffee
if true is 1
  "Type coercion fail!"
```

`is`,  `not` の代わりに、 `isnt` を使用できる

``` coffee
if true isnt true
  alert "Ooopsite day!"
```

## 文字列への挿入

CoffeeScript は Ruby式の文字列への挿入を JavaScript に対して追加している。
ダブルクォートで括った文字列に対しては `#{}` タグを含むことができる。
その中に文字列に対して挿入される式を記述する。

``` coffee
favarite_color = "Blue. No, yel..."
question = "BridgeKeeper: What.. is your favarite color?
            Galahad: #{favarite_color}
            BridgeKeeper: Wrong!!
            "
```

上の例のように複数行のわたった文字列を書くこともできる。

## ループと内包表記

``` coffee
for name in ["Roger", "Roderick", "Brian"]
  alert "Relase #{name}"
```

もしも繰り返しのインデックスが必要であれば、もうひとつの引数を渡す。

``` coffee
for name, i in ["Roger", "Roderick", "Brian"]
  alert "#{i} - Relase #{name}"
```

後置式を用いることで1行で繰り返すこともできる。

``` coffee
alert "Release #{prisoner}" for prisoner in ["Roger", "Roderick", "Brian"]
```

また、Python のリスト内包表記のようにフィルタリングを行うこともできる。

``` coffee
prisoners = ["Roger", "Roderick", "Brian"]
alert "Relase #{prisoner}" for prisoner in prisoners when prisoner[0] is "R"
```

内包表記を用いてオブジェクトのプロパティについて繰り返しを行うこともできる。

``` coffee
names = sam: "seaborn", donna: "moss"
alert("#{first} #{last}") for first, last of names
```

CoffeeScript が持つ低レベルなループは `while` のみ。
JavaScript の `while` と同じような動作をするが、利点が追加されており結果として配列を返す。

``` coffee
num = 6
minstrel = while num -= 1
  num + " Brave Sir Robin ran away"
```

## 配列

CoffeeScriptは配列の範囲(region)を用いたスライスについてのヒントをRubyから得ている。
範囲は2つの数値から作られる。
最初と最後の位置を示す2つの数値は`..`か`...`で区切られる。
もし、範囲の前に何もない場合にはCoffeeScriptはそれを配列に変換する。

``` coffee
range = [1..5
```

しかし、もしも範囲が変数の直後に置かれたならば、CoffeeScriptはそれを`slice()`メソッドの呼び出しに変換する。

``` coffee
firstTwo = ["one", "two", "three"][0..1]
```

上の例では範囲は新しい配列を返す。
それは、元の配列の最初の要素のみを持つ配列だ。
同じ文法の配列の一部を他の配列で置換することにも使える。

``` coffee
numbers = [0..9]
numbers = [3..5] = [-3, -4, -5]
```

JavaScript では`slice()`を文字列に対して呼ぶことができる。
そのため、範囲を文字列に対して使用することで部分文字列を返すことが可能となっている。

``` coffee
my = "my string"[0..2]
```

JavaScriptでは配列のなかにある値が存在するか確認することはとても面倒だった。
`indexOf()`がすべてのブラウザ間においてサポートされていなかったからである。
CoffeeScriptはこれを`in`演算子を用いて解決している。

``` coffee
words = ["rattled", "roudy", "rebbles", "ranks"]
alert "Stop warning me" if "ranks" in words
```

## エイリアスと存在確認演算子
