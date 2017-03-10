.. _man-conversion-and-promotion:

.. 
 **************************
  Conversion and Promotion
 **************************

**************************
 変換とプロモーション
**************************

.. 
 Julia has a system for promoting arguments of mathematical operators to
 a common type, which has been mentioned in various other sections,
 including :ref:`man-integers-and-floating-point-numbers`, :ref:`man-mathematical-operations`, :ref:`man-types`, and
 :ref:`man-methods`. In this section, we explain how this promotion
 system works, as well as how to extend it to new types and apply it to
 functions besides built-in mathematical operators. Traditionally,
 programming languages fall into two camps with respect to promotion of
 arithmetic arguments:

Juliaには、 :ref:`man-整数と浮動小数点数` 、 :ref:`man-算術処理と基本的な関数` 、 :ref:`man-型` および :ref:`man-メソッド` などのさまざまな
セクションで述べられているように、算術演算子の引数を共通の型に昇格（プロモーション）させるためのシステムがあります。
このセクションでは、このプロモーションシステムがどのように機能するのか、
またどのように新しい型にまで広げるのかおよびビルトイン以外の算術演算子の関数に適用するのかを説明します。
伝統的に、プログラミング言語は算術引数のプロモーションシステムについて、2つに分類することができます。:

.. 
 -  **Automatic promotion for built-in arithmetic types and operators.**
    In most languages, built-in numeric types, when used as operands to
    arithmetic operators with infix syntax, such as ``+``, ``-``, ``*``,
    and ``/``, are automatically promoted to a common type to produce the
    expected results. C, Java, Perl, and Python, to name a few, all
    correctly compute the sum ``1 + 1.5`` as the floating-point value
    ``2.5``, even though one of the operands to ``+`` is an integer.
    These systems are convenient and designed carefully enough that they
    are generally all-but-invisible to the programmer: hardly anyone
    consciously thinks of this promotion taking place when writing such
    an expression, but compilers and interpreters must perform conversion
    before addition since integers and floating-point values cannot be
    added as-is. Complex rules for such automatic conversions are thus
    inevitably part of specifications and implementations for such
    languages.
 -  **No automatic promotion.** This camp includes Ada and ML — very
    "strict" statically typed languages. In these languages, every
    conversion must be explicitly specified by the programmer. Thus, the
    example expression ``1 + 1.5`` would be a compilation error in both
    Ada and ML. Instead one must write ``real(1) + 1.5``, explicitly
    converting the integer ``1`` to a floating-point value before
    performing addition. Explicit conversion everywhere is so
    inconvenient, however, that even Ada has some degree of automatic
    conversion: integer literals are promoted to the expected integer
    type automatically, and floating-point literals are similarly
    promoted to appropriate floating-point types.
   
-  **ビルトインの算術型および演算子の自動昇格**
   ほとんどの言語では、 ``+`` 、 ``-`` 、 ``*`` 、 ``/`` などの中置構文を持つ算術演算子の被演算子として使用されると、
   ビルトインの数値型は自動的に共通の型に昇格され、期待される結果が得られます。C、Java、Perl、およびPythonでは、
   たとえ ``+`` の被演算子の1つが整数であっても、 ``1 + 1.5`` は浮動小数点値 ``2.5`` として、正しく計算されます。
   これらのシステムは、プログラマにとっては完全に見えないほど便利で、慎重に設計されています。
   これらのコードを書いている際に、この昇格が起きていることに気にする人はほとんどいないかと思いますが、
   整数や浮動小数点の値をそのままでは追加できないため、コンパイラやインタプリタは追加する前に変換を実行する必要があります。
   したがって、このような自動変換の複雑なルールは、そのような言語の仕様および実装の一部と言えます。
-  **自動昇格なし**
   この分類には、AdaやMLなどの「厳格な」静的型言語が含まれます。これらの言語では、
   全ての変換はプログラマによって明示的に指定される必要があります。したがって、
   例の式 ``1 + 1.5`` は、AdaとMLの両方でコンパイルエラーになります。
   その代わりに、加算を実行する前に、整数「1」を浮動小数点値に明示的に変換するように、
   ``real(1) + 1.5`` と書く必要があります。しかし、常に明示的に変換するのは非常に面倒ですが、
   Adaでもある程度の自動変換が行われます。整数リテラルは予想される整数型に自動的に昇格され、
   浮動小数点リテラルも同様に適切な浮動小数点型に昇格されます。

.. 
 In a sense, Julia falls into the "no automatic promotion" category:
 mathematical operators are just functions with special syntax, and the
 arguments of functions are never automatically converted. However, one
 may observe that applying mathematical operations to a wide variety of
 mixed argument types is just an extreme case of polymorphic multiple
 dispatch — something which Julia's dispatch and type systems are
 particularly well-suited to handle. "Automatic" promotion of
 mathematical operands simply emerges as a special application: Julia
 comes with pre-defined catch-all dispatch rules for mathematical
 operators, invoked when no specific implementation exists for some
 combination of operand types. These catch-all rules first promote all
 operands to a common type using user-definable promotion rules, and then
 invoke a specialized implementation of the operator in question for the
 resulting values, now of the same type. User-defined types can easily
 participate in this promotion system by defining methods for conversion
 to and from other types, and providing a handful of promotion rules
 defining what types they should promote to when mixed with other types.

ある意味では、Juliaは「自動昇格なし」カテゴリに分類されます。算術演算子は特別な構文を持つ関数に過ぎず、
関数の引数は決して自動的に変換されません。しかし、多種多様な引数型に算術演算を適用することは、
多様な多重ディスパッチの極端なケースに過ぎません。Juliaのディスパッチおよび型システムは特に処理に適しています。
算術被演算子の「自動」昇格は、単に特別なアプリケーションとして出現します。Juliaには、
算術演算子のための初めから定義された汎用のディスパッチルールがあります。
これは、被演算子の組み合わせに対して特定の実装が存在しない場合に呼び出されます。
これらの汎用ルールは、まず初めにユーザー定義可能なプロモーションルールを使用してすべての被演算子を共通型に昇格させ、
結果の同じ型となった値について問題となる演算子に対して特殊な実装を呼び出します。
ユーザー定義型は、他の型への変換のためのメソッドを定義し、他の型と混合したときに
どの型に昇格するべきかを定義するプロモーションルールをいくつか提供することで、このプロモーションシステムに関与しています。

.. _man-conversion:

.. 
 Conversion
 ----------

変換
----------

.. 
 Conversion of values to various types is performed by the ``convert``
 function. The ``convert`` function generally takes two arguments: the
 first is a type object while the second is a value to convert to that
 type; the returned value is the value converted to an instance of given
 type. The simplest way to understand this function is to see it in
 action:

値のさまざまな型への変換は、 ``convert`` 関数によって実行されます。 ``convert`` 関数は通常2つの引数をとります。
1つ目は型オブジェクトであり、2つ目は指定した型に変換する値です。
戻り値は、指定された型のインスタンスに変換された値です。
この関数を理解する最も簡単な方法は、実際に動作を確認することです。:

.. doctest::

    julia> x = 12
    12

    julia> typeof(x)
    Int64

    julia> convert(UInt8, x)
    0x0c

    julia> typeof(ans)
    UInt8

    julia> convert(AbstractFloat, x)
    12.0

    julia> typeof(ans)
    Float64

    julia> a = Any[1 2 3; 4 5 6]
    2x3 Array{Any,2}:
     1  2  3
     4  5  6

    julia> convert(Array{Float64}, a)
    2x3 Array{Float64,2}:
     1.0  2.0  3.0
     4.0  5.0  6.0

.. 
 Conversion isn't always possible, in which case a no method error is
 thrown indicating that ``convert`` doesn't know how to perform the
 requested conversion:

変換が常に可能であるとは限りません。この場合、「no method error」がスローされ、
これは ``convert`` が要求された変換を実行できないことを示します。:

.. doctest::

    julia> convert(AbstractFloat, "foo")
    ERROR: MethodError: Cannot `convert` an object of type String to an object of type AbstractFloat
    This may have arisen from a call to the constructor AbstractFloat(...),
    since type constructors fall back to convert methods.
     ...

.. 
 Some languages consider parsing strings as numbers or formatting
 numbers as strings to be conversions (many dynamic languages will even
 perform conversion for you automatically), however Julia does not: even
 though some strings can be parsed as numbers, most strings are not valid
 representations of numbers, and only a very limited subset of them are.
 Therefore in Julia the dedicated :func:`parse` function must be used
 to perform this operation, making it more explicit.

いくつかの言語では、文字列を数値として、または数値を変換される文字列としてを解析しようとします
（多くの動的言語は自動的に変換を実行します）。しかし、Juliaは異なっています。
いくつかの文字列は数値として解析されますが、その数は非常に限られており、
ほとんどの文字列は数値として表現することはできません。したがって、Juliaでは、
この処理を実行するために専用の :func:`parse` 関数を明示的に使用しなければなりません。

.. 
  Defining New Conversions
  ~~~~~~~~~~~~~~~~~~~~~~~~

新しい変換の定義
~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  To define a new conversion, simply provide a new method for :func:`convert`.
  That's really all there is to it. For example, the method to convert a
  real number to a boolean is this::

新しい変換を定義するには、単に :func:`convert` の新しいメソッドを提供するだけです。他に必要なものはありません。
例えば、実数をブール値に変換する方法は次の通りです。::

    convert(::Type{Bool}, x::Real) = x==0 ? false : x==1 ? true : throw(InexactError())

.. 
  The type of the first argument of this method is a :ref:`singleton
  type <man-singleton-types>`, ``Type{Bool}``, the only instance of
  which is ``Bool``. Thus, this method is only invoked when the first
  argument is the type value ``Bool``. Notice the syntax used for the first
  argument: the argument name is omitted prior to the ``::`` symbol, and only
  the type is given.  This is the syntax in Julia for a function argument whose type is
  specified but whose value is never used in the function body.  In this example,
  since the type is a singleton, there would never be any reason to use its value
  within the body.
  When invoked, the method determines
  whether a numeric value is true or false as a boolean, by comparing it
  to one and zero:

このメソッドの最初の引数の型は、 :ref:`シングルトン型 <man-シングルトン型>` の ``Type{Bool}`` であり、
``Bool`` の唯一のインスタンスです。
したがって、このメソッドは、最初の引数が型値 ``Bool`` の場合にのみ呼び出されます。
最初の引数に使用される構文に注目してください。引数名は ``::`` シンボルの前に省略され、
型だけが与えられています。これは、型が指定されているものの、
その値が関数本体で使用されていない関数引数のJulia構文です。この例では、型がシングルトン型であるため、
その値を本体内で使用する必要はありません。呼び出されると、このメソッドは数値を1と0と比較することによって、
真偽値をブール値として判断します。:

.. doctest::

    julia> convert(Bool, 1)
    true

    julia> convert(Bool, 0)
    false

    julia> convert(Bool, 1im)
    ERROR: InexactError()
     in convert(::Type{Bool}, ::Complex{Int64}) at ./complex.jl:23
     ...

    julia> convert(Bool, 0im)
    false

.. 
  The method signatures for conversion methods are often quite a bit more
  involved than this example, especially for parametric types. The example
  above is meant to be pedagogical, and is not the actual Julia behaviour.
  This is the actual implementation in Julia::

変換メソッドのメソッドシグネチャ、特にパラメータ型のシグネチャは、この例よりもかなり複雑です。
上の例は説明用のものであり、実際のJuliaの動作ではありません。以下がJuliaの実際の実装です。::

    convert{T<:Real}(::Type{T}, z::Complex) = (imag(z)==0 ? convert(T,real(z)) :
                                               throw(InexactError()))

    julia> convert(Bool, 1im)
    ERROR: InexactError()
     in convert(::Type{Bool}, ::Complex{Int64}) at ./complex.jl:18
     ...

.. 
  Case Study: Rational Conversions
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ケーススタディ：Rationalの変換
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  To continue our case study of Julia's ``Rational`` type, here are the
  conversions declared in
  `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_,
  right after the declaration of the type and its constructors::

Juliaの ``Rational`` 型のケーススタディを続行するため、 `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_ 
で宣言された変換が、型とそのコンストラクタの宣言の直後にある例を見てみましょう。::

    convert{T<:Integer}(::Type{Rational{T}}, x::Rational) = Rational(convert(T,x.num),convert(T,x.den))
    convert{T<:Integer}(::Type{Rational{T}}, x::Integer) = Rational(convert(T,x), convert(T,1))

    function convert{T<:Integer}(::Type{Rational{T}}, x::AbstractFloat, tol::Real)
        if isnan(x); return zero(T)//zero(T); end
        if isinf(x); return sign(x)//zero(T); end
        y = x
        a = d = one(T)
        b = c = zero(T)
        while true
            f = convert(T,round(y)); y -= f
            a, b, c, d = f*a+c, f*b+d, a, b
            if y == 0 || abs(a/b-x) <= tol
                return a//b
            end
            y = 1/y
        end
    end
    convert{T<:Integer}(rt::Type{Rational{T}}, x::AbstractFloat) = convert(rt,x,eps(x))

    convert{T<:AbstractFloat}(::Type{T}, x::Rational) = convert(T,x.num)/convert(T,x.den)
    convert{T<:Integer}(::Type{T}, x::Rational) = div(convert(T,x.num),convert(T,x.den))

.. 
  The initial four convert methods provide conversions to rational types.
  The first method converts one type of rational to another type of
  rational by converting the numerator and denominator to the appropriate
  integer type. The second method does the same conversion for integers by
  taking the denominator to be 1. The third method implements a standard
  algorithm for approximating a floating-point number by a ratio of
  integers to within a given tolerance, and the fourth method applies it,
  using machine epsilon at the given value as the threshold. In general,
  one should have ``a//b == convert(Rational{Int64}, a/b)``.

初めの4つの変換メソッドは、合理的な型への変換を行います。最初のメソッドは、
分子と分母を適切な整数型に変換することによって、rational型の1つを別のrational型に変換します。
2つ目のメソッドは、分母を1とすることで同じように整数の変換をします。3つ目のメソッドは、
与えられた許容値内の整数比で浮動小数点数を近似するための標準アルゴリズムを実装し、
4つ目のメソッドは、与えられた値のマシンイプシロンを閾値として使用して、3つ目のメソッドを適用します。
一般に、 ``a//b == convert(Rational{Int64}, a/b)`` の形で使うことができます。

.. 
  The last two convert methods provide conversions from rational types to
  floating-point and integer types. To convert to floating point, one
  simply converts both numerator and denominator to that floating point
  type and then divides. To convert to integer, one can use the ``div``
  operator for truncated integer division (rounded towards zero).

最後の2つの変換メソッドは、rational型から浮動小数点型および整数型への変換を提供します。
浮動小数点に変換するには、分子と分母の両方をその浮動小数点型に変換してから除算するだけです。
整数に変換するには、切り捨てられた整数の除算（0に端数処理される）に ``div`` 演算子を使用できます。

.. _man-promotion:

.. 
  Promotion
  ---------

プロモーション（昇格）
---------

.. 
  Promotion refers to converting values of mixed types to a single common
  type. Although it is not strictly necessary, it is generally implied
  that the common type to which the values are converted can faithfully
  represent all of the original values. In this sense, the term
  "promotion" is appropriate since the values are converted to a "greater"
  type — i.e. one which can represent all of the input values in a single
  common type. It is important, however, not to confuse this with
  object-oriented (structural) super-typing, or Julia's notion of abstract
  super-types: promotion has nothing to do with the type hierarchy, and
  everything to do with converting between alternate representations. For
  instance, although every ``Int32`` value can also be represented as a
  ``Float64`` value, ``Int32`` is not a subtype of ``Float64``.

プロモーション（昇格）は、色々な型の値を1つの共通の型に変換することを指します。
厳密には必要というわけではありませんが、値が変換される共通の型は、
元の値のすべてを忠実に表すことができることを示しています。この意味では、
値が「より大きい」型、つまり、入力値の全てを共通の型で表すことができるものに変換されるため、
「プロモーション」という用語は適切です。しかし、これをオブジェクト指向の（構造的）上位タイプ、
またはジュリアの概念の抽象上位タイプと混同しないことが重要です。昇格は型の階層とは関係がなく、
代替表現間の変換が全てです。例えば、全ての ``Int32`` 値は ``Float64`` 値としても表現できますが、
``Int32`` は ``Float64`` のサブタイプではありません。

.. 
  Promotion to a common "greater" type is performed in Julia by the ``promote``
  function, which takes any number of arguments, and returns a tuple of
  the same number of values, converted to a common type, or throws an
  exception if promotion is not possible. The most common use case for
  promotion is to convert numeric arguments to a common type:

Juliaにおける「より大きい」型への昇格は、複数の引数をとり、共通の型に変換された同じ数の値のタプルを返す、
``promote`` 関数によって実行されます。昇格が不可能な場合は例外をスローします。
昇格の最も一般的な使用例は、数値引数を共通の型に変換することです。:

.. doctest::

    julia> promote(1, 2.5)
    (1.0,2.5)

    julia> promote(1, 2.5, 3)
    (1.0,2.5,3.0)

    julia> promote(2, 3//4)
    (2//1,3//4)

    julia> promote(1, 2.5, 3, 3//4)
    (1.0,2.5,3.0,0.75)

    julia> promote(1.5, im)
    (1.5 + 0.0im,0.0 + 1.0im)

    julia> promote(1 + 2im, 3//4)
    (1//1 + 2//1*im,3//4 + 0//1*im)

.. 
  Floating-point values are promoted to the largest of the floating-point
  argument types. Integer values are promoted to the larger of either the
  native machine word size or the largest integer argument type.
  Mixtures of integers and floating-point values are promoted to a
  floating-point type big enough to hold all the values. Integers mixed
  with rationals are promoted to rationals. Rationals mixed with floats
  are promoted to floats. Complex values mixed with real values are
  promoted to the appropriate kind of complex value.

浮動小数点値は浮動小数点引数型のなかで最大のものに昇格されます。整数値は、使用しているコンピュータのワードサイズ、
または最大の整数引数型の、いずれか大きい方に昇格されます。整数と浮動小数点値の混合の場合は、
全ての値を保持するのに十分な大きさの浮動小数点型に昇格されます。整数と有理数の混合の場合は、
有理数に昇格されます。有理数と浮動小数点数の混合の場合は、浮動小数点数に昇格されます。
複素数値と実数の混合の場合は、適切な種類の複素数値に昇格されます。

.. 
  That is really all there is to using promotions. The rest is just a
  matter of clever application, the most typical "clever" application
  being the definition of catch-all methods for numeric operations like
  the arithmetic operators ``+``, ``-``, ``*`` and ``/``. Here are some of
  the catch-all method definitions given in
  `promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_::

以上が昇格の使用方法です。以下は賢い使用方法であり、最も典型的な賢い機能は、
算術演算子 ``+`` 、 ``-`` 、 ``*`` および ``/`` のような数値演算の汎用メソッドの定義です。
以下に、 `promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_ で
指定された汎用メソッド定義の一部を示します。::

    +(x::Number, y::Number) = +(promote(x,y)...)
    -(x::Number, y::Number) = -(promote(x,y)...)
    *(x::Number, y::Number) = *(promote(x,y)...)
    /(x::Number, y::Number) = /(promote(x,y)...)

.. 
  These method definitions say that in the absence of more specific rules
  for adding, subtracting, multiplying and dividing pairs of numeric
  values, promote the values to a common type and then try again. That's
  all there is to it: nowhere else does one ever need to worry about
  promotion to a common numeric type for arithmetic operations — it just
  happens automatically. There are definitions of catch-all promotion
  methods for a number of other arithmetic and mathematical functions in
  `promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_,
  but beyond that, there are hardly any calls to ``promote`` required in
  the Julia standard library. The most common usages of ``promote`` occur
  in outer constructors methods, provided for convenience, to allow
  constructor calls with mixed types to delegate to an inner type with
  fields promoted to an appropriate common type. For example, recall that
  `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_
  provides the following outer constructor method::

これらのメソッド定義では、数値のペアを加算、減算、乗算、および除算するためのより具体的なルールがない場合、
値を共通の型に昇格してから処理をし直します。必要なことはこれだけです。
算術演算の一般的な数値型への昇格を心配する必要はありません。それは自動的に処理されます。
`promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_ には数多くの
算術関数の汎用プロモーションメソッドの定義がありますが、
Julia標準ライブラリで必要な ``promote`` を呼び出すことはほとんどありません。
``promote`` の最も一般的な使用はアウターコンストラクタメソッドにおいてであり、
これにより異なる型を持つコンストラクタの呼び出しを、適切な共通の型に昇格されたフィールドの内部型に渡すことができます。
例えば、 `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_ は
次のアウターコンストラクタメソッドを提供することを思い出してください。::

    Rational(n::Integer, d::Integer) = Rational(promote(n,d)...)

.. 
  This allows calls like the following to work:

これにより、次のような呼び出しが可能になります。:

.. doctest::

    julia> Rational(Int8(15),Int32(-5))
    -3//1

    julia> typeof(ans)
    Rational{Int32}

.. 
  For most user-defined types, it is better practice to require
  programmers to supply the expected types to constructor functions
  explicitly, but sometimes, especially for numeric problems, it can be
  convenient to do promotion automatically.

ほとんどのユーザー定義型では、プログラマは予想される型をコンストラクタ関数に明示的に指定することをお勧めしますが、
特に数値処理の場合は、自動的に昇格させるのが便利です。

.. _man-promotion-rules:

.. 
  Defining Promotion Rules
  ~~~~~~~~~~~~~~~~~~~~~~~~

プロモーションルールの定義
~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Although one could, in principle, define methods for the ``promote``
  function directly, this would require many redundant definitions for all
  possible permutations of argument types. Instead, the behavior of
  ``promote`` is defined in terms of an auxiliary function called
  ``promote_rule``, which one can provide methods for. The
  ``promote_rule`` function takes a pair of type objects and returns
  another type object, such that instances of the argument types will be
  promoted to the returned type. Thus, by defining the rule::

原則的には、 ``promote`` 関数のメソッドを直接定義することはできますが、
これには全ての可能な引数型の順列に対して多くの冗長的な定義が必要となります。
代わりに、 ``promote`` の動作は ``promote_rule`` と呼ばれる補助的な関数の観点から定義されています。
``promote_rule`` 関数は型オブジェクトのペアを取り、別の型オブジェクトを返します。
例えば、引数型のインスタンスは返される型に昇格されます。したがって、ルールを定義することによって、::

    promote_rule(::Type{Float64}, ::Type{Float32} ) = Float64

.. 
  one declares that when 64-bit and 32-bit floating-point values are
  promoted together, they should be promoted to 64-bit floating-point. The
  promotion type does not need to be one of the argument types, however;
  the following promotion rules both occur in Julia's standard library::

4ビットと32ビットの浮動小数点値を一緒に昇格させると、64ビットの浮動小数点に昇格する必要があると宣言しています。
昇格する型は引数の型のどちらかである必要はありませんが、Juliaの標準ライブラリでは、
次の両方のプロモーションルールが発生します。::


    promote_rule(::Type{UInt8}, ::Type{Int8}) = Int
    promote_rule(::Type{BigInt}, ::Type{Int8}) = BigInt

.. 
  In the latter case, the result type is ``BigInt`` since ``BigInt`` is
  the only type large enough to hold integers for arbitrary-precision
  integer arithmetic.  Also note that one does not need to define both
  ``promote_rule(::Type{A}, ::Type{B})`` and
  ``promote_rule(::Type{B}, ::Type{A})`` — the symmetry is implied by
  the way ``promote_rule`` is used in the promotion process.

後者の場合、 ``BigInt`` は任意精度の整数演算のための整数を保持するのに十分な大きさを持つの唯一の型であるため、
結果の型は ``BigInt`` となります。また、 ``promote_rule(::Type{A}, ::Type{B})`` および
``promote_rule(::Type{B}, ::Type{A})`` の両方を定義する必要はないことに注意してください。
この対称性は、 ``promote_rule`` がプロモーションのプロセスで使用される方法によって暗示されます。

.. 
  The ``promote_rule`` function is used as a building block to define a
  second function called ``promote_type``, which, given any number of type
  objects, returns the common type to which those values, as arguments to
  ``promote`` should be promoted. Thus, if one wants to know, in absence
  of actual values, what type a collection of values of certain types
  would promote to, one can use ``promote_type``:

``promote_rule`` 関数はビルドブロックとして使用され、 ``promote_type`` という2番目の関数を定義します。
これは、任意の数の型オブジェクトについて、 ``promote`` の昇格させる引数としてそれらの値に共通の型を返します。
したがって、実際の値がない場合、特定の型の値のコレクションがどの型に昇格するかを知りたい場合、
``promote_type`` を使用できます。:

.. doctest::

    julia> promote_type(Int8, UInt16)
    Int64

.. 
  Internally, ``promote_type`` is used inside of ``promote`` to determine
  what type argument values should be converted to for promotion. It can,
  however, be useful in its own right. The curious reader can read the
  code in
  `promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_,
  which defines the complete promotion mechanism in about 35 lines.

内部的には、 ``promote_type`` はプロモート処理の内部で使用され、昇格のためにどの型の引数値を変換するべきかを決定します。
しかし、それ自体で有用なことがあります。興味がある方は、約35行で完全なプロモーションの仕組みを定義している
`promotion.jl <https://github.com/JuliaLang/julia/blob/master/base/promotion.jl>`_ のコードを読んでみてください。

.. 
  Case Study: Rational Promotions
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ケーススタディ：有理数の昇格
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Finally, we finish off our ongoing case study of Julia's rational number
  type, which makes relatively sophisticated use of the promotion
  mechanism with the following promotion rules::


ついに、ここまで継続してきたJuliaの有理数型に関するケーススタディを終了します。
これは、次のプロモーションルールで昇格のメカニズムを比較的洗練された形で使用することができます。::

    promote_rule{T<:Integer,S<:Integer}(::Type{Rational{T}}, ::Type{S}) = Rational{promote_type(T,S)}
    promote_rule{T<:Integer,S<:Integer}(::Type{Rational{T}}, ::Type{Rational{S}}) = Rational{promote_type(T,S)}
    promote_rule{T<:Integer,S<:AbstractFloat}(::Type{Rational{T}}, ::Type{S}) = promote_type(T,S)

.. 
  The first rule says that promoting a rational number with any other integer
  type promotes to a rational type whose numerator/denominator type is the
  result of promotion of its numerator/denominator type with the other integer
  type. The second rule applies the same logic to two different types of rational
  numbers, resulting in a rational of the promotion of their respective
  numerator/denominator types. The third and final rule dictates that promoting
  a rational with a float results in the same type as promoting the
  numerator/denominator type with the float.

最初のルールは、他の整数型の有理数を昇格することは、他の整数型の分子/分母の型の昇格の結果である、
有理数型に昇格することをを示しています。2つ目のルールは、同じロジックを2つの異なる型の有理数に適用し、
その結果、それぞれの分子/分母型の昇格を合理的にします。3つ目と最後のルールは、浮動小数点を使用して有理数を昇格させると、
浮動小数点で分子/分母を昇格させた場合と同じ型になることを示しています。

.. 
  This small handful of promotion rules, together with the `conversion
  methods discussed above <#case-study-rational-conversions>`_, are
  sufficient to make rational numbers interoperate completely naturally
  with all of Julia's other numeric types — integers, floating-point
  numbers, and complex numbers. By providing appropriate conversion
  methods and promotion rules in the same manner, any user-defined numeric
  type can interoperate just as naturally with Julia's predefined
  numerics.
  
このプロモーションルールは、前述の「変換メソッド」とともに、Juliaの他の全ての数値型（整数、浮動小数点数、および複素数）と
有理数を完全かつ自然に相互運用させることができます。適切な変換メソッドとプロモーションルールを同じ方法で提供することで、
ユーザー定義の数値型は全て、Juliaの事前定義された数値と自然に相互運用できます。
