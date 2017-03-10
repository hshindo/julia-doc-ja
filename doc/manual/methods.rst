.. _man-methods:

.. 
  *********
   Methods
  *********

*********
 メソッド
*********

.. 
 Recall from :ref:`man-functions` that a function is an object
 that maps a tuple of arguments to a return value, or throws an exception
 if no appropriate value can be returned. It is common for the same
 conceptual function or operation to be implemented quite differently for
 different types of arguments: adding two integers is very different from
 adding two floating-point numbers, both of which are distinct from
 adding an integer to a floating-point number. Despite their
 implementation differences, these operations all fall under the general
 concept of "addition". Accordingly, in Julia, these behaviors all belong
 to a single object: the ``+`` function.

:ref:`man-関数` セクションの内容を思い出してください。関数は、引数のタプルを戻り値にマップするオブジェクトであり、
もし適切な値が返されない場合は、例外がスローされます。概念上同様な関数または処理が、
異なる引数の型に対して全く異なる実装がされることは一般的です。例えば、2つの整数を加算することは、
2つの浮動小数点数を加算することとは異なり、またどちらも浮動小数点数に整数を足すこととは異なります。
実装の違いはありますが、これらの処理は全て「追加」という概念に当てはまります。
したがって、Juliaではこれらの動作はすべて単一のオブジェクト``+``関数に属します。

.. 
 To facilitate using many different implementations of the same concept
 smoothly, functions need not be defined all at once, but can rather be
 defined piecewise by providing specific behaviors for certain
 combinations of argument types and counts. A definition of one possible
 behavior for a function is called a *method*. Thus far, we have
 presented only examples of functions defined with a single method,
 applicable to all types of arguments. However, the signatures of method
 definitions can be annotated to indicate the types of arguments in
 addition to their number, and more than a single method definition may
 be provided. When a function is applied to a particular tuple of
 arguments, the most specific method applicable to those arguments is
 applied. Thus, the overall behavior of a function is a patchwork of the
 behaviors of its various method definitions. If the patchwork is well
 designed, even though the implementations of the methods may be quite
 different, the outward behavior of the function will appear seamless and
 consistent.

同じ概念の様々な実装をスムーズに使用するために、一度に関数の全てを定義する必要はなく、
引数型とカウントの特定の組み合わせに対して特定の動作を指定することによって、
区分的に定義することができます。関数が可能な動作の定義は、メソッドと呼ばれます。
ここまででは、1つのメソッドで定義された、全ての型の引数に適応できる関数の例のみを見てきました。
しかし、メソッド定義の特徴は、メソッドには引数の種類とその数を示す注釈を付けることができ、
複数のメソッド定義を与えることができます。関数が特定の引数のタプルに適用される場合、
それらの引数に適用可能な最も具体的な方法が適用されます。したがって、関数の動作は、
様々なメソッド定義の動作のパッチワークのようなものです。このパッチワークが適切に設計されていれば、
たとえメソッドの実装が大きく異なっていたとしても、関数の外見上の動作はシームレスで一貫性があります。

.. 
 The choice of which method to execute when a function is applied is
 called *dispatch*. Julia allows the dispatch process to choose which of
 a function's methods to call based on the number of arguments given, and
 on the types of all of the function's arguments. This is different than
 traditional object-oriented languages, where dispatch occurs based only
 on the first argument, which often has a special argument syntax, and is
 sometimes implied rather than explicitly written as an
 argument. [#]_ Using all of a function's arguments to
 choose which method should be invoked, rather than just the first, is
 known as `multiple dispatch
 <https://en.wikipedia.org/wiki/Multiple_dispatch>`_. Multiple
 dispatch is particularly useful for mathematical code, where it makes
 little sense to artificially deem the operations to "belong" to one
 argument more than any of the others: does the addition operation in
 ``x + y`` belong to ``x`` any more than it does to ``y``? The
 implementation of a mathematical operator generally depends on the types
 of all of its arguments. Even beyond mathematical operations, however,
 multiple dispatch ends up being a powerful and convenient paradigm
 for structuring and organizing programs.

関数が適用されたときに実行するメソッドの選択は、ディスパッチと呼ばれます。
Juliaでは、与えられた引数の数および対象の関数の引数の型に基づいて、
ディスパッチプロセスがどの関数のメソッドを呼び出すかを選択することができます。
これは、通常特殊な引数構文を持ち、引数として明示的に記述されるのではなく暗示的に使用される、
最初の引数のみに基づいてディスパッチが行われる従来のオブジェクト指向言語とは異なります。
[#]_ 最初の引数だけでなく、関数の引数の全てを使用して呼び出すメソッドを選択することは、
`多重ディスパッチ <https://en.wikipedia.org/wiki/Multiple_dispatch>`_ と呼ばれます。
多重ディスパッチは、作為的に処理が複数ではなく1つの引数に「属する」と
見なすことでスムーズに動作する、数学的コードに対して特に便利です。例えば、 ``x + y`` 内の加算処理は、 ``y`` よりも ``x`` に
属するのかといった点です。算術演算子の実装は、一般的に、全ての引数の型に依存します。しかし、数学的な処理を以外でも、
多重ディスパッチはプログラムの構造化と整理のためのパワフルで便利なパラダイムになります。

.. 
 .. [#] In C++ or Java, for example, in a method call like
   ``obj.meth(arg1,arg2)``, the object obj "receives" the method call and is
   implicitly passed to the method via the ``this`` keyword, rather than as an
   explicit method argument. When the current ``this`` object is the receiver of a
   method call, it can be omitted altogether, writing just ``meth(arg1,arg2)``,
   with ``this`` implied as the receiving object.

.. [#] 例えば、C++やJavaの ``obj.meth(arg1,arg2)`` のようなメソッドの呼び出しでは、オブジェクトobjはメソッド呼び出しを
 「受け取り」、明示的なメソッド引数ではなく ``this`` キーワードを介して暗黙的にメソッドに渡されます。
 カレントの ``this`` オブジェクトがメソッドの呼び出しの受け手の場合、 ``this`` が受け取りオブジェクトとして暗示されていて
 ``meth(arg1,arg2)`` と記述することで、省略することができます。
 
.. 
 Defining Methods
 ----------------

メソッドの定義
----------------

.. 
 Until now, we have, in our examples, defined only functions with a
 single method having unconstrained argument types. Such functions behave
 just like they would in traditional dynamically typed languages.
 Nevertheless, we have used multiple dispatch and methods almost
 continually without being aware of it: all of Julia's standard functions
 and operators, like the aforementioned ``+`` function, have many methods
 defining their behavior over various possible combinations of argument
 type and count.

これまでの例では、制約のない型を持つ単一のメソッドの関数のみ定義してきました。このような関数は、
従来の動的に型指定された言語と同じように動作します。それにもかかわらず、我々はそれを意識することなく、
多重ディスパッチおよびメソッドをほぼ継続的に使用してきました。前述の ``+`` 関数のようなJuliaの標準関数と演算子は全て、
引数の型とカウントのさまざまな組み合わせに対して動作を定義する多くのメソッドを持っています。

.. 
 When defining a function, one can optionally constrain the types of
 parameters it is applicable to, using the ``::`` type-assertion
 operator, introduced in the section on :ref:`man-composite-types`:

関数を定義する際に、 :ref:`man-コンポジット型` のセクションで紹介された ``::`` 演算子を使うことで、
適用可能なパラメータの型を任意に制約することができます。:

.. doctest::

    julia> f(x::Float64, y::Float64) = 2x + y;

.. 
 This function definition applies only to calls where ``x`` and ``y`` are
 both values of type :obj:`Float64`:

この関数定義は、 ``x`` と ``y`` の両方が :obj:`Float64` 型の値である呼び出しにのみ適応されます。:

.. doctest::

    julia> f(2.0, 3.0)
    7.0

.. 
 Applying it to any other types of arguments will result in a :exc:`MethodError`:

他の型の引数に適用した場合、 :exc:`MethodError` となります。:

.. doctest::

    julia> f(2.0, 3)
    ERROR: MethodError: no method matching f(::Float64, ::Int64)
    Closest candidates are:
      f(::Float64, !Matched::Float64) at none:1
    ...

    julia> f(Float32(2.0), 3.0)
    ERROR: MethodError: no method matching f(::Float32, ::Float64)
    Closest candidates are:
      f(!Matched::Float64, ::Float64) at none:1
    ...

    julia> f(2.0, "3.0")
    ERROR: MethodError: no method matching f(::Float64, ::String)
    Closest candidates are:
      f(::Float64, !Matched::Float64) at none:1
    ...

    julia> f("2.0", "3.0")
    ERROR: MethodError: no method matching f(::String, ::String)
    ...

.. 
 As you can see, the arguments must be precisely of type :obj:`Float64`.
 Other numeric types, such as integers or 32-bit floating-point values,
 are not automatically converted to 64-bit floating-point, nor are
 strings parsed as numbers. Because :obj:`Float64` is a concrete type and
 concrete types cannot be subclassed in Julia, such a definition can only
 be applied to arguments that are exactly of type :obj:`Float64`. It may
 often be useful, however, to write more general methods where the
 declared parameter types are abstract:

ご覧のように、引数は :obj:`Float64` 型でなければなりません。整数や32ビット浮動小数点値などの他の数値型は、
自動的に64ビット浮動小数点には変換されず、また文字列は数字として解釈されません。
:obj:`Float64` 型は具体型であり、Juliaでは具体型はサブクラス化できないため、
このような定義は :obj:`Float64` 型である引数のみに対し適用が可能です。
しかし、宣言されたパラメータ型が抽象型である、より一般的なメソッドを記述することはしばしば便利です。:

.. doctest::

    julia> f(x::Number, y::Number) = 2x - y;

    julia> f(2.0, 3)
    1.0

.. 
 This method definition applies to any pair of arguments that are
 instances of :obj:`Number`. They need not be of the same type, so long as
 they are each numeric values. The problem of handling disparate numeric
 types is delegated to the arithmetic operations in the expression
 ``2x - y``.

このメソッド定義は、 :obj:`Number` のインスタンスである引数のペアに適用されます。それぞれのが数値である限り、
そのペアは同じ型である必要はありません。異なる種類の数値型を処理する問題は、 ``2x - y`` の算術演算に代表されます。

.. 
 To define a function with multiple methods, one simply defines the
 function multiple times, with different numbers and types of arguments.
 The first method definition for a function creates the function object,
 and subsequent method definitions add new methods to the existing
 function object. The most specific method definition matching the number
 and types of the arguments will be executed when the function is
 applied. Thus, the two method definitions above, taken together, define
 the behavior for ``f`` over all pairs of instances of the abstract type
 :obj:`Number` — but with a different behavior specific to pairs of
 :obj:`Float64` values. If one of the arguments is a 64-bit float but the
 other one is not, then the ``f(Float64,Float64)`` method cannot be
 called and the more general ``f(Number,Number)`` method must be used:

複数のメソッドで関数を定義するには、異なる数値と型の引数を使用して関数を複数回定義するだけです。
関数の最初のメソッド定義は関数オブジェクトを作成し、後続のメソッド定義は既存の関数オブジェクトに新しいメソッドを追加します。
引数の数と型に最も一致するメソッド定義は、関数が適用されたときに実行されます。したがって、上記の2つのメソッド定義は、
抽象型 :obj:`Number` のインスタンスの全てのペアに対する ``f`` の動作を定義しますが、 :obj:`Float64` の値のペアに対する動作は異なります。
引数の1つが64ビット浮動小数点数型でもう1つの引数が64ビット浮動小数点数型ではない場合、
``f(Float64,Float64)`` メソッドは使用することができず、より全般的な ``f(Number,Number)`` メソッドを使用する必要があります。:

.. doctest::

    julia> f(2.0, 3.0)
    7.0

    julia> f(2, 3.0)
    1.0

    julia> f(2.0, 3)
    1.0

    julia> f(2, 3)
    1

.. 
 The ``2x + y`` definition is only used in the first case, while the
 ``2x - y`` definition is used in the others. No automatic casting or
 conversion of function arguments is ever performed: all conversion in
 Julia is non-magical and completely explicit. :ref:`man-conversion-and-promotion`, however, shows how clever
 application of sufficiently advanced technology can be indistinguishable
 from magic. [Clarke61]_

``2x + y`` 定義は最初のケースにのみ使用され、 ``2x - y`` 定義は他のケースに使用されます。
関数の引数のキャストや変換は自動では実行されません。Juliaの変換は全て魔法のようではなく明示的に実行されます。
しかし、 :ref:`man-変換とプロモーション` では進んだ技術の応用は魔法のようであることを示しています。 [Clarke61]_

.. 
 For non-numeric values, and for fewer or more than two arguments, the
 function ``f`` remains undefined, and applying it will still result in a
 :obj:`MethodError`:

数値以外の値や2つ以上の引数に対しては、関数 ``f`` は未定義のままであり、それを適用すると :obj:`MethodError` となります。:

.. doctest::

    julia> f("foo", 3)
    ERROR: MethodError: no method matching f(::String, ::Int64)
    Closest candidates are:
      f(!Matched::Number, ::Number) at none:1
    ...

    julia> f()
    ERROR: MethodError: no method matching f()
    Closest candidates are:
      f(!Matched::Float64, !Matched::Float64) at none:1
      f(!Matched::Number, !Matched::Number) at none:1
    ...

.. 
 You can easily see which methods exist for a function by entering the
 function object itself in an interactive session:

対話式セッションに関数オブジェクトを入力すると、関数のどのメソッドが存在するかを簡単に確認できます。:

.. doctest::

    julia> f
    f (generic function with 2 methods)

.. 
 This output tells us that ``f`` is a function object with two
 methods. To find out what the signatures of those methods are, use the
 :func:`methods` function:

これは、 ``f`` が2つのメソッドを持つ関数オブジェクトであることを示しています。
メソッドの特徴を調べるには、 :func:`methods` 関数を使います。:

.. doctest::

    julia> methods(f)
    # 2 methods for generic function "f":
    f(x::Float64, y::Float64) at none:1
    f(x::Number, y::Number) at none:1

.. 
 which shows that ``f`` has two methods, one taking two :obj:`Float64`
 arguments and one taking arguments of type :obj:`Number`. It also
 indicates the file and line number where the methods were defined:
 because these methods were defined at the REPL, we get the apparent
 line number ``none:1``.

これは、 ``f`` は2つのメソッドを持つことを示しており、1つは2つの :obj:`Float64` 型の引き数を取り、
もう1つは :obj:`Number` 型の引き数を取ります。また、これは、メソッドが定義されているファイル名と行数を表示します。
例のメソッドはREPLで定義されているため、行数 ``none:1`` が表示されます。

.. 
 In the absence of a type declaration with ``::``, the type of a method
 parameter is :obj:`Any` by default, meaning that it is unconstrained since
 all values in Julia are instances of the abstract type :obj:`Any`. Thus, we
 can define a catch-all method for ``f`` like so:

``::`` を使用した型宣言がない文の場合、メソッドパラメータの型はデフォルトで :obj:`Any` となります。
Juliaの全ての値は抽象型の「Any」のインスタンスであるため制約されません。
したがって、 ``f`` の包括的なメソッドを次のように定義することができます。:

.. doctest::

    julia> f(x,y) = println("Whoa there, Nelly.");

    julia> f("foo", 1)
    Whoa there, Nelly.

.. 
 This catch-all is less specific than any other possible method
 definition for a pair of parameter values, so it is only be called on
 pairs of arguments to which no other method definition applies.

この包括的なメソッドは、他のメソッドと比較するとパラメータ値のペアの定義は明確ではないため、
他のメソッド定義が適用されない引数のペアに対してのみ呼び出されます。

.. 
 Although it seems a simple concept, multiple dispatch on the types of
 values is perhaps the single most powerful and central feature of the
 Julia language. Core operations typically have dozens of methods:

これはシンプルなコンセプトに見えますが、値の型に対する多重ディスパッチは、
おそらくJuliaの中で最も強力で中心的な機能です。コアオペレーションには、通常数十種類のメソッドがあります。:

.. doctest::
   :options: +SKIP

    julia> methods(+)
    # 166 methods for generic function "+":
    +(a::Float16, b::Float16) at float16.jl:136
    +(x::Float32, y::Float32) at float.jl:206
    +(x::Float64, y::Float64) at float.jl:207
    +(x::Bool, z::Complex{Bool}) at complex.jl:126
    +(x::Bool, y::Bool) at bool.jl:48
    +(x::Bool) at bool.jl:45
    +{T<:AbstractFloat}(x::Bool, y::T) at bool.jl:55
    +(x::Bool, z::Complex) at complex.jl:133
    +(x::Bool, A::AbstractArray{Bool,N<:Any}) at arraymath.jl:105
    +(x::Char, y::Integer) at char.jl:40
    +{T<:Union{Int128,Int16,Int32,Int64,Int8,UInt128,UInt16,UInt32,UInt64,UInt8}}(x::T, y::T) at int.jl:32
    +(z::Complex, w::Complex) at complex.jl:115
    +(z::Complex, x::Bool) at complex.jl:134
    +(x::Real, z::Complex{Bool}) at complex.jl:140
    +(x::Real, z::Complex) at complex.jl:152
    +(z::Complex, x::Real) at complex.jl:153
    +(x::Rational, y::Rational) at rational.jl:179
    ...
    +(a, b, c, xs...) at operators.jl:119

.. 
 Multiple dispatch together with the flexible parametric type system give
 Julia its ability to abstractly express high-level algorithms decoupled
 from implementation details, yet generate efficient, specialized code to
 handle each case at run time.

多重ディスパッチと柔軟なパラメータ型システムを行うことで、Juliaは実装の詳細から切り離された
ハイレベルのアルゴリズムを抽象的に表現することができ、それでいて実行時に各ケースを
処理する効率的で特別なコードを生成することができます。

.. 
 Method Ambiguities
 ------------------

メソッドのあいまいさ
------------------

.. 
 It is possible to define a set of function methods such that there is no
 unique most specific method applicable to some combinations of
 arguments:

引数の組み合わせに適用可能な一意のメソッドが存在しない関数メソッドのセットを定義することができてしまいます。:

.. doctest::

    julia> g(x::Float64, y) = 2x + y;

    julia> g(x, y::Float64) = x + 2y;

    julia> g(2.0, 3)
    7.0

    julia> g(2, 3.0)
    8.0

    julia> g(2.0, 3.0)
    ERROR: MethodError: g(::Float64, ::Float64) is ambiguous. Candidates:
      g(x, y::Float64) at none:1
      g(x::Float64, y) at none:1
     ...

.. 
 Here the call ``g(2.0, 3.0)`` could be handled by either the
 ``g(Float64, Any)`` or the ``g(Any, Float64)`` method, and neither is
 more specific than the other. In such cases, Julia raises a ``MethodError``
 rather than arbitrarily picking a method. You can avoid method ambiguities
 by specifying an appropriate method for the intersection case:

この場合、 ``g(2.0, 3.0)`` の呼び出しは ``g(Float64, Any)`` または ``g(Any, Float64)`` メソッドの
いずれかで処理することができ、どちらがよりふさわしいということはありません。
このような場合、Juliaはメソッドを任意に選択するのではなく、 ``MethodError`` を返します。
インターセクションケースに適切なメソッドを指定することによって、メソッドのあいまいさを回避することができます。:

.. doctest:: unambiguous

    julia> g(x::Float64, y::Float64) = 2x + 2y;

    julia> g(x::Float64, y) = 2x + y;

    julia> g(x, y::Float64) = x + 2y;

    julia> g(2.0, 3)
    7.0

    julia> g(2, 3.0)
    8.0

    julia> g(2.0, 3.0)
    10.0

.. 
 It is recommended that the disambiguating method be defined first,
 since otherwise the ambiguity exists, if transiently, until the more
 specific method is defined.

特定のメソッドが定義されるまであいまいさが存在するため、
最初にあいまいさを回避するメソッドを定義することを推奨します。

.. _man-parametric-methods:

.. 
 Parametric Methods
 ------------------

パラメータメソッド
------------------

.. 
 Method definitions can optionally have type parameters immediately after
 the method name and before the parameter tuple:

メソッド定義はオプションで、メソッド名の直後またはパラメータタプルの前に型パラメータを持つことができます。:

.. doctest::

    julia> same_type{T}(x::T, y::T) = true;

    julia> same_type(x,y) = false;

.. 
 The first method applies whenever both arguments are of the same
 concrete type, regardless of what type that is, while the second method
 acts as a catch-all, covering all other cases. Thus, overall, this
 defines a boolean function that checks whether its two arguments are of
 the same type:

最初のメソッドは、どの型であっても2つの引数が同じ具体型であれば適用されます。
2つ目のメソッドは包括的なメソッドとして機能し、他の全てのケースをカバーします。
したがって、これは2つの引数が同じ型であるかを確認するブール関数を定義します。:

.. doctest::

    julia> same_type(1, 2)
    true

    julia> same_type(1, 2.0)
    false

    julia> same_type(1.0, 2.0)
    true

    julia> same_type("foo", 2.0)
    false

    julia> same_type("foo", "bar")
    true

    julia> same_type(Int32(1), Int64(2))
    false

.. 
 This kind of definition of function behavior by dispatch is quite common
 — idiomatic, even — in Julia. Method type parameters are not restricted
 to being used as the types of parameters: they can be used anywhere a
 value would be in the signature of the function or body of the function.
 Here's an example where the method type parameter ``T`` is used as the
 type parameter to the parametric type ``Vector{T}`` in the method
 signature:

Juliaにおけるこの種の関数定義のディスパッチによる動きは一般的です。メソッドの型パラメータは、
パラメータの型としての使用に限定されず、関数または関数の本体のシグネチャ内に値がある場所なら
どこでも使用できます。以下はメソッドの型パラメータ ``T`` がメソッドシグネチャのパラメータ型 ``Vector{T}`` に対して
使用されている例です。:

.. doctest::

    julia> myappend{T}(v::Vector{T}, x::T) = [v..., x]
    myappend (generic function with 1 method)

    julia> myappend([1,2,3],4)
    4-element Array{Int64,1}:
     1
     2
     3
     4

    julia> myappend([1,2,3],2.5)
    ERROR: MethodError: no method matching myappend(::Array{Int64,1}, ::Float64)
    Closest candidates are:
      myappend{T}(::Array{T,1}, !Matched::T) at none:1
    ...

    julia> myappend([1.0,2.0,3.0],4.0)
    4-element Array{Float64,1}:
     1.0
     2.0
     3.0
     4.0

    julia> myappend([1.0,2.0,3.0],4)
    ERROR: MethodError: no method matching myappend(::Array{Float64,1}, ::Int64)
    Closest candidates are:
      myappend{T}(::Array{T,1}, !Matched::T) at none:1
    ...

.. 
 As you can see, the type of the appended element must match the element
 type of the vector it is appended to, or else a :exc:`MethodError` is raised.
 In the following example, the method type parameter ``T`` is used as the
 return value:

ご覧の通り、追加された要素の型は、追加されたベクトルの要素型と一致する必要があります。
一致しない場合は :exc:`MethodError` が出力されます。次の例では、メソッド型パラメータ ``T`` は戻り値として使用されています。:

.. doctest::

    julia> mytypeof{T}(x::T) = T
    mytypeof (generic function with 1 method)

    julia> mytypeof(1)
    Int64

    julia> mytypeof(1.0)
    Float64

.. 
 Just as you can put subtype constraints on type parameters in type
 declarations (see :ref:`man-parametric-types`), you
 can also constrain type parameters of methods::

型宣言の型パラメータにサブタイプの制約を入れることができるように（ :ref:`man-パラメータ型` 参照）、
メソッドの型パラメータを制約することができます。::

    same_type_numeric{T<:Number}(x::T, y::T) = true
    same_type_numeric(x::Number, y::Number) = false

    julia> same_type_numeric(1, 2)
    true

    julia> same_type_numeric(1, 2.0)
    false

    julia> same_type_numeric(1.0, 2.0)
    true

    julia> same_type_numeric("foo", 2.0)
    no method same_type_numeric(String,Float64)

    julia> same_type_numeric("foo", "bar")
    no method same_type_numeric(String,String)

    julia> same_type_numeric(Int32(1), Int64(2))
    false

.. 
 The ``same_type_numeric`` function behaves much like the ``same_type``
 function defined above, but is only defined for pairs of numbers.

``same_type_numeric`` 関数は上記の ``same_type`` 関数のように動作しますが、
``same_type_numeric`` 関数は数値のペアに対してのみ定義されています。

.. _man-vararg-fixedlen:

.. 
 Parametrically-constrained Varargs methods
 ------------------------------------------

パラメトリック制約付き可変引数メソッド
------------------------------------------

.. 
 Function parameters can also be used to constrain the number of arguments that may be supplied to a "varargs" function (:ref:`man-varargs-functions`).  The notation ``Vararg{T,N}`` is used to indicate such a constraint.  For example:

関数パラメータは、可変引数関数（ :ref:`man-可変引数関数` 参照）に渡される引数の数を制限するためにも使用できます。
``Vararg{T,N}`` という表記は、そのような制約を示すために使用されます。例えば:

.. doctest::

    julia> bar(a,b,x::Vararg{Any,2}) = (a,b,x);

    julia> bar(1,2,3)
    ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64)
    ...

    julia> bar(1,2,3,4)
    (1,2,(3,4))

    julia> bar(1,2,3,4,5)
    ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64, ::Int64, ::Int64)
    ...

.. 
 More usefully, it is possible to constrain varargs methods by a parameter.  For example::

もっと便利なことに、パラメータで可変引数メソッドを制約することが可能です。例えば::

    function getindex{T,N}(A::AbstractArray{T,N}, indexes::Vararg{Number,N})

.. 
 would be called only when the number of ``indexes`` matches the dimensionality of the array.

これは、インデックスの数が配列の次元数と一致する場合にのみ呼び出されます。

.. _man-note-on-optional-and-keyword-arguments:

.. 
 Note on Optional and keyword Arguments
 --------------------------------------

オプション引数とキーワード引数に関する注意
--------------------------------------

.. 
 As mentioned briefly in :ref:`man-functions`, optional arguments are
 implemented as syntax for multiple method definitions. For example,
 this definition::

:ref:`man-f関数` で簡単に述べたように、オプション引数は複数のメソッド定義の構文として実装されています。例えば::

    f(a=1,b=2) = a+2b

.. 
 translates to the following three methods::

この定義は、次の3つの方法に変換されます。::

    f(a,b) = a+2b
    f(a) = f(a,2)
    f() = f(1,2)

.. 
 This means that calling ``f()`` is equivalent to calling ``f(1,2)``. In
 this case the result is ``5``, because ``f(1,2)`` invokes the first
 method of ``f`` above. However, this need not always be the case. If you
 define a fourth method that is more specialized for integers::

これは、 ``f()`` の呼び出しは ``f(1,2)`` の呼び出しと同じであることを意味します。
この場合、 ``f(1,2)`` は ``f`` の最初のメソッドを呼び出すため、結果は ``5`` となります。
しかし、必ずしもそうである必要はありません。整数に特化した4番目のメソッドを定義した場合は、次のようになります。::

    f(a::Int,b::Int) = a-2b

.. 
 then the result of both ``f()`` and ``f(1,2)`` is ``-3``. In other words,
 optional arguments are tied to a function, not to any specific method of
 that function. It depends on the types of the optional arguments which
 method is invoked. When optional arguments are defined in terms of a global
 variable, the type of the optional argument may even change at run-time.

この場合、 ``f()`` と ``f(1,2)`` の結果は ``-3`` となります。言い換えれば、オプションの引数は、
関数の特定のメソッドではなく、関数に結び付けられます。どのメソッドが呼び出されるかは、
オプションの引数の型に依存します。オプションの引数がグローバル変数で定義されている場合、
オプションの引数の型は実行時に変更されることがあります。

.. 
 Keyword arguments behave quite differently from ordinary positional arguments.
 In particular, they do not participate in method dispatch. Methods are
 dispatched based only on positional arguments, with keyword arguments processed
 after the matching method is identified.

キーワード引数は、通常の引数とは全く異なる動作をします。特に、メソッドディスパッチ時には動作しません。
メソッドは通常の引数だけに基づいてディスパッチされ、一致するメソッドが識別された後にキーワード引数が処理されます。

.. 
 Function-like objects
 ---------------------

関数のようなオブジェクト
---------------------

.. 
 Methods are associated with types, so it is possible to make any arbitrary
 Julia object "callable" by adding methods to its type.
 (Such "callable" objects are sometimes called "functors.")

メソッドは型に関連付けられているため、型にメソッドを追加することで、
任意のJuliaオブジェクトを「呼び出し可能」にすることができます
（このような「呼び出し可能な」オブジェクトは、「functor」と呼ばれることもあります。）。

.. 
 For example, you can define a type that stores the coefficients of a
 polynomial, but behaves like a function evaluating the polynomial::

例えば、多項式の係数を格納する型を定義できますが、その型は多項式を評価する関数のように動作します。::

    immutable Polynomial{R}
        coeffs::Vector{R}
    end

    function (p::Polynomial)(x)
        v = p.coeffs[end]
        for i = (length(p.coeffs)-1):-1:1
            v = v*x + p.coeffs[i]
        end
        return v
    end

.. 
 Notice that the function is specified by type instead of by name.
 In the function body, ``p`` will refer to the object that was called.
 A ``Polynomial`` can be used as follows::

関数が名前ではなく型によって指定されていることに注意してください。関数本体では、
``p`` は呼び出されたオブジェクトを参照します。多項式 ``Polynomial`` は次のように使用できます。::

    julia> p = Polynomial([1,10,100])
    Polynomial{Int64}([1,10,100])

    julia> p(3)
    931

.. 
 This mechanism is also the key to how type constructors and closures
 (inner functions that refer to their surrounding environment) work
 in Julia, discussed :ref:`later in the manual <constructors-and-conversion>`.

このメカニズムは、型コンストラクタとクロージャ（周囲の環境を参照する内部関数）がJuliaで
どのように機能するかの鍵でもあります。 :ref:`これは後にマニュアル <コンストラクタと変換>` の中で説明されます。

.. 
 Empty generic functions
 -----------------------

空の汎用関数
-----------------------

.. 
 Occasionally it is useful to introduce a generic function without yet adding
 methods.
 This can be used to separate interface definitions from implementations.
 It might also be done for the purpose of documentation or code readability.
 The syntax for this is an empty ``function`` block without a tuple of
 arguments::

まだメソッドを追加していない空の汎用関数を使用すると便利な場合があります。
これは、インタフェース定義を実装から分離するために使用できます。
これはドキュメンテーションやコードの読みやすさのために使うこともできます。
このための構文は、引数のタプルがない空の関数ブロックです。::

    function emptyfunc
    end

.. [Clarke61] Arthur C. Clarke, *Profiles of the Future* (1961): Clarke's Third Law.
