## 意味上のポインター

### リファレンスと同じ機能

ポインターはオブジェクトを参照するための機能だ。この点ではリファレンスと同じ機能を提供している。

リファレンスを覚えているだろうか。T型へのリファレンスはT型のオブジェクトそのものではなく、T型のオブジェクトへの参照だ。リファレンスへの操作は、参照したオブジェクトへの操作になる。

~~~cpp
int main()
{
    // int型のオブジェクト
    int object = 0 ;

    // オブジェクトを変更
    object = 123 ;

    // 123
    std::cout << object ;

    // T型へのリファレンス
    // objectを参照する
    int & reference = object ;

    // objectが変更される
    reference = 456 ;

    // 456
    std::cout << object ;

    // referenceはobjectを参照している
    object = 789 ;

    // 参照するobjectの値
    // 789
    std::cout << reference ;
}
~~~

リファレンスは宣言と同時に初期化する。リファレンスの参照先をあとから返ることはできない。

~~~cpp
int main()
{
    int x = 0 ;

    // rはxを参照する
    int r = x ;

    int y = 1 ;

    // xに1が代入される
    r = y ;
}
~~~

最後の`r = y ;`はリファレンスrの参照先をyに変えるという意味ではない。リファレンスrの参照先にyの値を代入するという意味だ。

ポインターはリファレンスに似ている。並べてみるとほとんど同じ意味だ。

+ T型へのリファレンスはT型のオブジェクトを参照する
+ T型へのポインターはT型のオブジェクトを参照する

T型へのリファレンス型が`T &`であるのに対し、T型へのポインター型は`T *`だ。

~~~cpp
// intへのリファレンス型
using ref_type = int & ;
// intへのポインター型
using ptr_type = int * ;
~~~

リファレンスの初期化は、単に参照したい変数名をそのまま書けばよかった。

~~~c++
int object { } ;
int & reference = object ;
~~~

ポインターの場合、参照したい変数名に、`&`をつける必要がある。

~~~c++
int object { } ;
int * pointer = &object ;
~~~

リファレンスを経由してリファレンスが参照するオブジェクトを操作するには、単にリファレンス名を使えばよかった。

~~~c++
// 書き込み
reference = 0
// 読み込み
int read = reference ;  
~~~

ポインターの場合、ポインター名に`*`をつける必要がある。

~~~c++
// 書き込み
*pointer = 0 ;
// 読み込み
int read = *pointer ;
~~~

ポインター名をそのまま使った場合、それは参照先のオブジェクトの値ではなく、ポインターという値になる。

~~~cpp
// オブジェクト
int object { } ;

// オブジェクトのポインター値で初期化
int * p1 = &object

// p1のポインター値で代入
// つまりobjectを参照する
int * p2 = p1 ;
~~~

このように比較すると、ポインターはリファレンスと同じ機能を提供していることがわかる。実際、リファレンスというのはポインターのシンタックスシュガーにすぎない。ポインターの機能を制限して、文法をわかりやすくしたものだ。

### リファレンスと違う機能

リファレンスがポインターの機能制限版だというのであれば、ポインターにあってリファレンスにはない機能はなんだろうか。代入と、何も参照しない状態だ。

### 代入

リファレンスは代入ができないが、ポインターは代入ができる。

~~~c++
int x { } ;
int y { } ;

int & reference = x ;
// xにyの値を代入
// リファレンスの参照先は変わらない
reference = y ;

int * pointer = &x ;
// pointerの参照先をyに変更
pointer = &y ;
~~~

### 何も参照しない状態

リファレンスは必ず初期化しなければならない。

~~~c++
// エラー、初期化されていない
int & reference ; 
~~~

そのため、リファレンスは常にオブジェクトを参照している。

ポインターは初期化しなくてもよい。

~~~c++
int * pointer ;
~~~

この場合、具体的に何かを参照していない状態になる。この場合にポインターの値はどうなるかはわからない。初期化のない整数の値がわからないのと同じだ。

~~~c++
// 値はわからない
int data ;
~~~

このわからない値が発生することを、専門用語では「未規定の挙動」という。

わからない値の整数を読むことは推奨できない。書くことはできる。

~~~cpp
int main()
{
    // 値はわからない
    int data ; 

    // 推奨できない
    std::cout << data ;

    // OK
    data = 0 ;
}
~~~

このプログラムは何らかの値を出力するはずだが、具体的にどういう値を出力するのかはわからない。

そしてここからがポインターの恐ろしいところだが、ポインターの場合にもこのわからない値は発生する。わからない値を持ったポインターの参照先への読み書きは、「未定義の挙動」を引き起こす。

~~~cpp
int main()
{
    int * pointer ;

    // 未定義の挙動
    std::cout << *pointer ;

    // 未定義の挙動
    *pointer = 123 ;
}
~~~

なぜ未定義の挙動になるかというと、わからない値のポインターは、たまたまどこかの妥当なオブジェクトを参照してしまっているかもしれないからだ。

未定義の挙動は恐ろしい。未定義の挙動が発生した場合、何が起こっても文句は言えない。なぜならばその挙動は本来存在するはずがないのだから。上のプログラムはコンパイル時にエラーになるかもしれないし、実行時にエラーになるかもしれない。いや、もっとひどいことにはエラーにならないかもしれない。そして人生、宇宙、すべてのものの答えと、あろうことか答えに対する質問まで出力するかもしれない。

### 明示的に何も参照しないポインター: nullptr

ポインターを未初期化にしていると、よくわからない値になってしまう。そのため、何も参照していないことを明示的に示すためのポインターの値、nullポインター値がある。`nullptr`だ。

~~~cpp
int * pointer = nullptr ;
~~~

nullptrはどんな型へのポインターに対しても、何も参照していない値となる。

~~~cpp
// doubleへのポインター
double * p1 = nullptr ;

// std::stringへのポインター
std::string p2 = nullptr ;
~~~

C言語とC++では歴史的な理由で、`nullptr`の他にも`NULL`もnullポインター値

~~~cpp
int * pointer = NULL ;
~~~

C++ではさらに歴史的な理由で、`0`もnullポインター値として扱う。

~~~cpp
int * pointer = 0 ;
~~~

ただし、nullポインター値が実際に0である保証はない。ポインターの値についてはあとで詳しく扱う。

### 無効な参照先の作り方

ポインターやリファレンスによって参照先が参照される時点では有向だったが、後に無効になる参照先を作ることができてしまう。

例えば以下のコードだ。

~~~cpp
int * f()
{
    // 寿命は関数
    int variable {} ;

    return &variable ;
}

int main()
{
    int * ptr = f() ;
    // エラー
    int read = *ptr ;
}
~~~

このコードの問題は、関数fの中の変数variableの寿命は関数fの中だけで、呼び出し元に戻ったときには寿命が尽きるというところにある。変数variableへのポインターは変数variableの寿命が尽きたあとも存在してしまうので、存在しないオブジェクトにポインター経由でアクセスしようとしてエラーになる。

同じ問題はリファレンスでも起きるが、ポインターのほうがこの問題を起こしやすい。


~~~cpp
int & f()
{
    int variable {} ;
    return variable ;
}
~~~
