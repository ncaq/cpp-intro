# std::array

`std::vector<T>`を覚えているだろうか。T型の値をいくつでも保持できるクラスだ。

~~~cpp
int main()
{
    // int型の値を10個保持するクラス
    std::vector<int> v(10) ;

    // 0番目の値を1に
    v.at(0) = 1 ;

    // イテレーターを取る
    auto i = std::begin(v) ;
}
~~~

この章では、`vector`と似ているクラス、`std::array<T, N>`を学ぶ。`array`はT型の値をN個保持するクラスだ。

その使い方は一見`vector`と似ている。

~~~cpp
int main()
{
    // int型の値を10個保持するクラス
    std::array<int, 10> a ;

    // 0番目の値を1に
    a.at(0) = 1 ;

    // イテレーターを取る
    auto i = std::begin(a) ;
}
~~~

`vector`と違う点は、コンパイル時に要素数が固定されるということだ。

`vector`は実行時に要素数を決めることができる。

~~~cpp
int main()
{
    std::size_t N{} ;
    std::cin >> N ;

    // 要素数N
    std::vector<int> v(N) ; 
}
~~~

一方、`array`はコンパイル時に要素数を決める。標準入力から得た値は実行時のものなので、使うことはできない。

~~~c++
int main()
{
    std::size_T N{} ;
    std::cin >> N ;

    // エラー
    std::array< int, N > a ;
}
~~~

`vector`は実行時に要素数を変更することができる。メンバー関数`push_back`は要素数を1増やす。メンバー関数`resize(sz)`は要素数を`sz`にする。

~~~cpp
int main()
{
    // 要素数5
    std::vector<int> v(5) ;
    // 要素数6
    v.push_back(1) ;
    // 要素数2
    v.resize(2) ;
}
~~~

`array`は`push_back`も`resize`も提供していない。

`vector`も`array`もメンバー関数`at(i)`でi番目の要素にアクセスできる。実は、i番目にアクセスする方法は他にもある。`[i]`を使う方法だ。

~~~cpp
int main()
{
    std::array<int, 10> a ;

    // どちらも0番目の要素に1を代入
    a.at(0) = 1 ;
    a[0] = 1 ;

    // どちらも0番目の要素を標準出力
    std::cout << a.at(0) ;
    std::cout << a[0] ;
}
~~~

`at(i)`と`[i]`の違いは、要素の範囲外にアクセスしたときの挙動だ。`at(i)`はエラー処理が行われる。`[i]`は何が起こるかわからない。

~~~cpp
int main()
{
    // 10個の要素を持つ
    // 0番目から9番目までが妥当な範囲
    std::array<int, 10> a ;

    // エラー処理が行われる
    // プログラムは終了する
    a.at(10) = 0 ;
    // 何が起こるかわからない
    a[10] = 0 ;
}
~~~

この理由は、`[i]`は要素数が妥当な範囲かどうかを確認する処理を行っていないためだ。その分余計な処理が発生しないが、間違えたときに何が起こるかわからないという危険性がある。通常は`at(i)`を使うべきだ。

実はこの`[i]`は`operator []`というれっきとした演算子だ。演算子のオーバーロードもできる。例えば以下は任意個の要素を持ち、常にゼロを返すarrayのように振る舞う意味のないクラスだ。

~~~cpp
// 常にゼロを返すクラス
// 何を書き込んでもゼロを返す
struct null_array
{
    int dummy ;
    // 引数は無視
    int & operator [] ( std::size_t )
    {
        dummy = 0 ;
        return dummy ;
    }
} ;

int main()
{
    null_array a ;

    // 0
    std::cout << a[0] ;
    // 0
    std::cout << a[999] ;

    a[100] = 0 ;
    // 0
    std::cout << a[100] ;
}
~~~

なぜ`vector`という実行時に要素数を設定でき実行時に要素数を変更できる便利なクラスがありながら、`array`のようなコンパイル時に要素数が決め打ちで要素数の変更もできないようなクラスもあるのだろうか。その理由は`array`と`vector`はパフォーマンスの特性が異なるからだ。`vector`はストレージ(メモリー)の動的確保をしている。ストレージの動的確保は実行時の要素数を変更できるのだが、そのために予測不可能な非決定的なパフォーマンス特性を持つ。`array`はストレージの動的確保を行わない。この結果実行時に要素数を変更することはできないが、予測可能で決定的なパフォーマンス特性を持つ。

その他の`array`の使い方は、`vector`とほぼ同じだ。

さて、これから'array'を実装していこう。実装を通じて読者はC++のクラスとその他の機能を学んでいくことになる。
