.. _man-mathematical-operations:

.. currentmodule:: Base

..
 **************************************************
  Mathematical Operations and Elementary Functions
 **************************************************

**************************************************
 算術処理と基本的な関数
**************************************************

..
 Julia provides a complete collection of basic arithmetic and bitwise
 operators across all of its numeric primitive types, as well as
 providing portable, efficient implementations of a comprehensive
 collection of standard mathematical functions.

Juliaは、基本的な数値プリミティブ型の全ての算術演算子とビット演算子だけでなく、
可搬的かつ標準的な数学関数の効率的な実装を実現しています。

..
 Arithmetic Operators
 --------------------

算術演算子
--------------------

..
 The following `arithmetic operators
 <https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations>`_
 are supported on all primitive numeric types:

以下の `算術演算子 <https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations>`_ は
全ての数値プリミティブ型でサポートされています。

..
 ==========  ============== ======================================
 Expression  Name           Description
 ==========  ============== ======================================
 ``+x``      unary plus     the identity operation
 ``-x``      unary minus    maps values to their additive inverses
 ``x + y``   binary plus    performs addition
 ``x - y``   binary minus   performs subtraction
 ``x * y``   times          performs multiplication
 ``x / y``   divide         performs division
 ``x \ y``   inverse divide equivalent to ``y / x``
 ``x ^ y``   power          raises ``x`` to the ``y``\ th power
 ``x % y``   remainder      equivalent to ``rem(x,y)``
 ==========  ============== ======================================

==========  ============== ======================================
式          名称            概要
==========  ============== ======================================
``+x``      単一項加法      恒等作用素
``-x``      単一項減法      値を逆数と関連付ける
``x + y``   二項加法        加法を実施
``x - y``   二項減法        減法を実施
``x * y``   乗法           乗法を実施
``x / y``   除法           除法を実施
``x \ y``   逆数除法        ``y / x`` と同等
``x ^ y``   累乗           ``x`` を ``y``\ 分だけ掛ける
``x % y``   余り           ``rem(x,y)`` と同等
==========  ============== ======================================

..
 as well as the negation on ``Bool`` types:

``Bool`` 型の否定形についても同様。

..
 ==========  ============== ============================================
 Expression  Name           Description
 ==========  ============== ============================================
 ``!x``      negation       changes ``true`` to ``false`` and vice versa
 ==========  ============== ============================================

==========  ============== ============================================
式          名称            概要
==========  ============== ============================================
``!x``      否定形          ``true`` を ``false`` に、または ``false`` を ``true`` に変更する
==========  ============== ============================================

..
 Julia's promotion system makes arithmetic operations on mixtures of argument
 types "just work" naturally and automatically. See :ref:`man-conversion-and-promotion`
 for details of the promotion system.

Juliaの推進体制は、複数の引数の型を含む算術演算が自然にかつ自動的に
動作するようにしています。プロモーションの詳細については :ref:`man-変換とプロモーション`を参照ください。

..
 Here are some simple examples using arithmetic operators:

以下は算術演算の例です。

.. doctest::

    julia> 1 + 2 + 3
    6

    julia> 1 - 2
    -1

    julia> 3*2/12
    0.5

..
 (By convention, we tend to space operators more tightly if they get applied before
 other nearby operators. For instance, we would generally write ``-x + 2`` to reflect
 that first ``x`` gets negated, and then ``2`` is added to that result.)

（慣例的に、他の演算子が適用される場合は、スペースを開けずに記載する傾向があります。
例えば、通常 ``-x + 2`` と記載しますが、これは最初に ``x`` を負の数にし、その後 ``2`` をその結果に足します。）

..
 Bitwise Operators
 -----------------

ビット単位の演算子
-----------------

..
 The following `bitwise
 operators <https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators>`_
 are supported on all primitive integer types:

以下の `ビット単位の演算子 <https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators>`_ は
全ての数値プリミティブ型でサポートされています。

..
 ===========  =========================================================================
 Expression   Name
 ===========  =========================================================================
 ``~x``       bitwise not
 ``x & y``    bitwise and
 ``x | y``    bitwise or
 ``x $ y``    bitwise xor (exclusive or)
 ``x >>> y``  `logical shift <https://en.wikipedia.org/wiki/Logical_shift>`_ right
 ``x >> y``   `arithmetic shift <https://en.wikipedia.org/wiki/Arithmetic_shift>`_ right
 ``x << y``   logical/arithmetic shift left
 ===========  =========================================================================

===========  =========================================================================
式           名称
===========  =========================================================================
``~x``       ビット単位の否定
``x & y``    ビット単位の論理積
``x | y``    ビット単位の論理和
``x $ y``    ビット単位の排他的論理和
``x >>> y``  `論理右桁送り <https://en.wikipedia.org/wiki/Logical_shift>`_
``x >> y``   `算術右桁送り <https://en.wikipedia.org/wiki/Arithmetic_shift>`_
``x << y``   論理/算術左桁送り
===========  =========================================================================

..
 Here are some examples with bitwise operators:

以下はビット単位演算子の例です。

.. doctest::

    julia> ~123
    -124

    julia> 123 & 234
    106

    julia> 123 | 234
    251

    julia> 123 $ 234
    145

    julia> ~UInt32(123)
    0xffffff84

    julia> ~UInt8(123)
    0x84

..
 Updating operators
 ------------------

演算子の更新
------------------
..
 Every binary arithmetic and bitwise operator also has an updating
 version that assigns the result of the operation back into its left
 operand. The updating version of the binary operator is formed by placing a
 ``=`` immediately after the operator. For example, writing ``x += 3`` is
 equivalent to writing ``x = x + 3``::

      julia> x = 1
      1

      julia> x += 3
      4

      julia> x
      4
全ての二項演算子とビット単位の演算子には、被演算子の処理の結果を代入できる更新機能があります。
二校演算子の更新機能は、演算子の後ろに ``=`` を記載することで実行できます。
例えば、 ``x += 3`` と記載することで ``x = x + 3`` と同じ結果となります。::

      julia> x = 1
      1

      julia> x += 3
      4

      julia> x
      4

..
 The updating versions of all the binary arithmetic and bitwise operators
 are::

    +=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  $=  >>>=  >>=  <<=

更新機能を持つ二項演算子とビット単位の演算子は以下の通りです。::

    +=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  $=  >>>=  >>=  <<=

..
 .. note::
    An updating operator rebinds the variable on the left-hand side.
    As a result, the type of the variable may change.

.. note::
   更新機能の結果は、左辺にある変数の型に紐付きます。これにより、変数の型が変更されることがあります。

   .. doctest::

      julia> x = 0x01; typeof(x)
      UInt8

      julia> x *= 2 #Same as x = x * 2
      2

      julia> isa(x, Int)
      true

.. _man-numeric-comparisons:

..
 Numeric Comparisons
 -------------------

数値の比較
-------------------

..
 Standard comparison operations are defined for all the primitive numeric
 types:

標準的な比較演算子は、全ての数値プリミティブ型に定義されています。

..
 =================== ========================
 Operator            Name
 =================== ========================
 :obj:`==`           equality
 :obj:`\!=` :obj:`≠` inequality
 :obj:`<`            less than
 :obj:`<=` :obj:`≤`  less than or equal to
 :obj:`>`            greater than
 :obj:`>=` :obj:`≥`  greater than or equal to
 =================== ========================

=================== ========================
演算子               名称
=================== ========================
:obj:`==`           等しい
:obj:`\!=` :obj:`≠` 等しくない
:obj:`<`            より小さい
:obj:`<=` :obj:`≤`  以下
:obj:`>`            より大きい
:obj:`>=` :obj:`≥`  以上
=================== ========================

..
 Here are some simple examples:

以下は使用例です。

.. doctest::

    julia> 1 == 1
    true

    julia> 1 == 2
    false

    julia> 1 != 2
    true

    julia> 1 == 1.0
    true

    julia> 1 < 2
    true

    julia> 1.0 > 3
    false

    julia> 1 >= 1.0
    true

    julia> -1 <= 1
    true

    julia> -1 <= -1
    true

    julia> -1 <= -2
    false

    julia> 3 < -0.5
    false

..
 Integers are compared in the standard manner — by comparison of bits.
 Floating-point numbers are compared according to the `IEEE 754
 standard <https://en.wikipedia.org/wiki/IEEE_754-2008>`_:

整数は標準的な方法（ビットの比較）により比較されます。
浮動小数点数は `IEEE 754規格 <https://en.wikipedia.org/wiki/IEEE_754-2008>`_ に準拠して比較されます。

..
 -  Finite numbers are ordered in the usual manner.
 -  Positive zero is equal but not greater than negative zero.
 -  ``Inf`` is equal to itself and greater than everything else except ``NaN``.
 -  ``-Inf`` is equal to itself and less then everything else except ``NaN``.
 -  ``NaN`` is not equal to, not less than, and not greater than anything,
    including itself.

-  有限数は標準的な方法で順序付けされます。
-  正のゼロは負のゼロに等しく、大きくはありません。
-  ``Inf`` はそれ自体と等しく、 ``NaN`` を除く全てよりも大きいです。
-  ``-Inf`` はそれ自体と等しく、 ``NaN`` を除く全てよりも小さいです。
-  ``NaN`` はそれ自体を含め、全てに対し等しくなく、何よりも小さくなく、何よりも大きくありません。

..
 The last point is potentially surprising and thus worth noting:

最後の記述は驚くべきものかもしれませんが、特段意味があるものではありません。

.. doctest::

    julia> NaN == NaN
    false

    julia> NaN != NaN
    true

    julia> NaN < NaN
    false

    julia> NaN > NaN
    false

..
 and can cause especial headaches with :ref:`Arrays <man-arrays>`:

これは、 :ref:`配列 <man-arrays>` と共に理解に苦しむものかもしれません。

.. doctest::

    julia> [1 NaN] == [1 NaN]
    false

..
 Julia provides additional functions to test numbers for special values,
 which can be useful in situations like hash key comparisons:

Juliaは、ハッシュ値の比較など、特別な値を比較するための機能を提供します。

..
 =============================== ==================================
 Function                        Tests if
 =============================== ==================================
 :func:`isequal(x, y) <isequal>` ``x`` and ``y`` are identical
 :func:`isfinite(x) <isfinite>`  ``x`` is a finite number
 :func:`isinf(x) <isinf>`        ``x`` is infinite
 :func:`isnan(x) <isnan>`        ``x`` is not a number
 =============================== ==================================

=============================== ==================================
関数                            検証内容
=============================== ==================================
:func:`isequal(x, y) <isequal>` ``x`` および ``y`` は一致するか
:func:`isfinite(x) <isfinite>`  ``x`` は有限数であるか
:func:`isinf(x) <isinf>`        ``x`` は無限であるか
:func:`isnan(x) <isnan>`        ``x`` は数字以外であるか
=============================== ==================================

..
:func:`isequal` considers ``NaN``\ s equal to each other:

:func:`isequal` は、 ``NaN`` 同士は等しいと解釈します。

.. doctest::

    julia> isequal(NaN,NaN)
    true

    julia> isequal([1 NaN], [1 NaN])
    true

    julia> isequal(NaN,NaN32)
    true

..
 :func:`isequal` can also be used to distinguish signed zeros:

:func:`isequal` は符号付き0を区別するためにも使用することができます。

.. doctest::

    julia> -0.0 == 0.0
    true

    julia> isequal(-0.0, 0.0)
    false

..
 Mixed-type comparisons between signed integers, unsigned integers, and
 floats can be tricky. A great deal of care has been taken to ensure
 that Julia does them correctly.

符号付き整数、符号がない整数、および浮動小数点数間の複数の型の比較は注意が必要です。
Juliaでは、このような処理を正確に実施できるよう、細心の注意が払われています。

..
 For other types, :func:`isequal` defaults to calling :func:`==`, so if you want to
 define equality for your own types then you only need to add a :func:`==`
 method.  If you define your own equality function, you should probably
 define a corresponding :func:`hash` method to ensure that ``isequal(x,y)``
 implies ``hash(x) == hash(y)``.

その他の型については、 :func:`isequal` はデフォルトで :func:`==` を呼び出すため、
使用している型の等式を定義したい場合は、 :func:`==` メソッドを追加することで定義することができます。
もし使用している等式の関数を定義したい場合は、 ``isequal(x,y)`` が ``hash(x) == hash(y)`` を
表すことを保証する :func:`hash` メソッドを定義することをお勧めします。

..
 Chaining comparisons
 ~~~~~~~~~~~~~~~~~~~~

連続した比較
~~~~~~~~~~~~~~~~~~~~

..
 Unlike most languages, with the `notable exception of
 Python <https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators>`_,
 comparisons can be arbitrarily chained:

`Pythonのような例外 <https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators>`_ を
除いて、ほとんどの言語とは異なり、比較は任意に連続して使用することができます。

.. doctest::

    julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
    true

..
 Chaining comparisons is often quite convenient in numerical code.
 Chained comparisons use the :obj:`&&` operator for scalar comparisons,
 and the :obj:`&` operator for elementwise comparisons, which allows them to
 work on arrays. For example, ``0 .< A .< 1`` gives a boolean array whose
 entries are true where the corresponding elements of ``A`` are between 0
 and 1.

連続した比較は、数値コードで便利です。連続した比較は、スカラー比較に :obj:`&&` 演算子を使用し、
要素単位（elementwiseの比較では配列処理を行う :obj:`&` 演算子を使用します。例えば、 ``0 .< A .< 1`` は、 ``A`` に
対応する要素が0と1の間に存在し、エントリが真であるブール値配列を返します。

..
 The operator :obj:`.\< <Base..\<>` is intended for array objects; the operation
 ``A .< B`` is valid only if ``A`` and ``B`` have the same dimensions.  The
 operator returns an array with boolean entries and with the same dimensions
 as ``A`` and ``B``.  Such operators are called *elementwise*; Julia offers a
 suite of elementwise operators: :obj:`.* <Base..*>`, :obj:`.+ <Base..+>`, etc.  Some of the elementwise
 operators can take a scalar operand such as the example ``0 .< A .< 1`` in
 the preceding paragraph.
 This notation means that the scalar operand should be replicated for each entry of
 the array.

:obj:`.\< <Base..\<>` 演算子は配列オブジェクトを対象としています。演算 ``A .< B`` は ``A`` と ``B`` が同じ次元の場合のみ有効です。
演算子は、ブール値のエントリを持ち、かつ ``A`` と ``B`` と同じ次元の配列を返します。このような演算子は要素単位（elementwise）と呼ばれ、
Juliaは :obj:`.* <Base..*>` 、 :obj:`.+ <Base..+>` といった要素単位演算子の一式を提供します。いくつかの要素単位演算子は、
前述の ``0 .< A .< 1`` のようなスカラー被演算子を使用することができます。これは、配列の各エントリに対して
スカラー被演算子を複製する必要があることを意味します。

..
 Note the evaluation behavior of chained comparisons:

連続した比較の値の処理に注意してください。

.. doctest::

    julia> v(x) = (println(x); x)
    v (generic function with 1 method)

    julia> v(1) < v(2) <= v(3)
    2
    1
    3
    true

    julia> v(1) > v(2) <= v(3)
    2
    1
    false

..
 The middle expression is only evaluated once, rather than twice as it
 would be if the expression were written as
 ``v(1) < v(2) && v(2) <= v(3)``. However, the order of evaluations in a
 chained comparison is undefined. It is strongly recommended not to use
 expressions with side effects (such as printing) in chained comparisons.
 If side effects are required, the short-circuit :obj:`&&` operator should
 be used explicitly (see :ref:`man-short-circuit-evaluation`).

``v(1) < v(2) && v(2) <= v(3)`` と記載されている場合は2度処理されるかのように
思われるかもしれませんが、中間の式は1度だけ処理されます。しかし、連続した比較の処理順は
定義されていません。連続した比較で副次効果のある式（印刷など）を使用しないことを推奨します。
副次効果が必要な場合は、短絡 :obj:`&&` 演算子を明示的に使用する必要があります。（ :ref:`man-短絡評価` 参照」）

..
 Operator Precedence
 ~~~~~~~~~~~~~~~~~~~

演算子の優先順位
~~~~~~~~~~~~~~~~~~~

..
 Julia applies the following order of operations, from highest precedence
 to lowest:

Juliaは、優先順位の高いものから低いものまで、以下の処理順序を適用しています。

..
 ================= =============================================================================================
 Category          Operators
 ================= =============================================================================================
 Syntax            ``.`` followed by ``::``
 Exponentiation    ``^`` and its elementwise equivalent ``.^``
 Fractions         ``//`` and ``.//``
 Multiplication    ``* / % & \`` and  ``.* ./ .% .\``
 Bitshifts         ``<< >> >>>`` and ``.<< .>> .>>>``
 Addition          ``+ - | $`` and ``.+ .-``
 Syntax            ``: ..`` followed by ``|>``
 Comparisons       ``> < >= <= == === != !== <:`` and ``.> .< .>= .<= .== .!=``
 Control flow      ``&&`` followed by ``||`` followed by ``?``
 Assignments       ``= += -= *= /= //= \= ^= ÷= %= |= &= $= <<= >>= >>>=`` and ``.+= .-= .*= ./= .//= .\= .^= .÷= .%=``
 ================= =============================================================================================

================= =============================================================================================
カテゴリ           演算子
================= =============================================================================================
構文              ``::`` を伴う ``.``
累乗              ``^`` とその要素単位と等しい ``.^``
分数              ``//`` および ``.//``
乗法              ``* / % & \`` および ``.* ./ .% .\``
ビットシフト        ``<< >> >>>`` および ``.<< .>> .>>>``
加法              ``+ - | $`` および ``.+ .-``
構文              ``|>`` を伴う ``: ..``
比較              ``> < >= <= == === != !== <:`` および ``.> .< .>= .<= .== .!=``
制御フロー         ``||`` および ``?`` を伴う ``&&``
割り当て           ``= += -= *= /= //= \= ^= ÷= %= |= &= $= <<= >>= >>>=`` および ``.+= .-= .*= ./= .//= .\= .^= .÷= .%=``
================= =============================================================================================

.. _man-elementary-functions:

..
 Elementary Functions
 ~~~~~~~~~~~~~~~~~~~~

基本的な関数
~~~~~~~~~~~~~~~~~~~~

..
 Julia provides a comprehensive collection of mathematical functions and
 operators. These mathematical operations are defined over as broad a
 class of numerical values as permit sensible definitions, including
 integers, floating-point numbers, rationals, and complexes, wherever
 such definitions make sense.

Juliaは、包括的な数学関数および演算子を提供します。これらの数学的演算子は、
整数、浮動小数点数、有理数、およびそれらの複合などを許容可能な定義として、
幅広い数値クラスとして定義されています。

..
 Moreover, these functions (like any Julia function) can be applied
 in "vectorized" fashion to arrays and other collections with the
 syntax ``f.(A)``, e.g. ``sin.(A)`` will compute the elementwise
 sine of each element of an array ``A``.  See :ref:`man-dot-vectorizing`.

さらに、これらの関数（ほかのJuliaの関数のような）は、配列や構文 ``f.(A)`` を伴うその他の
要素に「ベクトル化」して適用することができます。 ``sin.(A)`` は、配列 ``A`` の各要素の要素単位の
正弦を計算します。詳細は :ref:`man-ベクトル化関数のドット構文` を参照してください。

.. _man-numerical-conversions:

..
 Numerical Conversions
 ---------------------

数値変換
---------------------

..
 Julia supports three forms of numerical conversion, which differ in their
 handling of inexact conversions.

Juliaは、不正確な変換の処理が異なる3つの形式の数値変換をサポートしています。

..
 - The notation ``T(x)`` or ``convert(T,x)`` converts ``x`` to a value of type ``T``.

   -  If ``T`` is a floating-point type, the result is the nearest representable
      value, which could be positive or negative infinity.

   -  If ``T`` is an integer type, an ``InexactError`` is raised if ``x``
      is not representable by ``T``.


 - ``x % T`` converts an integer ``x`` to a value of integer type ``T``
   congruent to ``x`` modulo ``2^n``, where ``n`` is the number of bits in ``T``.
   In other words, the binary representation is truncated to fit.

 - The :ref:`man-rounding-functions` take a type ``T`` as an optional argument.
   For example, ``round(Int,x)`` is a shorthand for ``Int(round(x))``.

- 表記法 ``T(x)`` または ``convert(T,x)`` は、 ``x`` を ``T`` 型の値に変換します。

  -  ``T`` が浮動小数点型の場合、変換の結果は最も近い表現可能な、正または負の無限大にもなり得る値になります。

  -  ``T`` が整数型の場合、 ``x`` が ``T`` で表現できない際は ``InexactError`` が発生します。


- ``x % T`` は、整数 ``x`` を、 ``x`` を法とする ``2^n`` （ ``n`` は ``T`` のビット数）に一致する整数型 ``T`` の値に変換します。言い換えれば、バイナリ表現は値に収まるように切り捨てられます。

- :ref:`man-端数処理関数` は、 ``T`` 型をオプション引数として解釈します。例えば、 ``round(Int,x)`` は ``Int(round(x))`` の短縮系です。

..
 The following examples show the different forms.

以下の例は異なる形式を表しています。

.. doctest::

    julia> Int8(127)
    127

    julia> Int8(128)
    ERROR: InexactError()
     in Int8(::Int64) at ./sysimg.jl:53
     ...

    julia> Int8(127.0)
    127

    julia> Int8(3.14)
    ERROR: InexactError()
     in Int8(::Float64) at ./sysimg.jl:53
     ...

    julia> Int8(128.0)
    ERROR: InexactError()
     in Int8(::Float64) at ./sysimg.jl:53
     ...

    julia> 127 % Int8
    127

    julia> 128 % Int8
    -128

    julia> round(Int8,127.4)
    127

    julia> round(Int8,127.6)
    ERROR: InexactError()
     in trunc(::Type{Int8}, ::Float64) at ./float.jl:464
     in round(::Type{Int8}, ::Float64) at ./float.jl:211
     ...

..
 See :ref:`man-conversion-and-promotion` for how to define your own
 conversions and promotions.

独自の変換とプロモーションの定義方法については、 :ref:`man-変換とプロモーション` を参照してください。

.. _man-rounding-functions:

..
 Rounding functions
 ~~~~~~~~~~~~~~~~~~

端数処理関数
~~~~~~~~~~~~~~~~~~

..
 =========================== ================================== =============
 Function                    Description                        Return type
 =========================== ================================== =============
 :func:`round(x) <round>`    round ``x`` to the nearest integer ``typeof(x)``
 :func:`round(T, x) <round>` round ``x`` to the nearest integer ``T``
 :func:`floor(x) <floor>`    round ``x`` towards ``-Inf``       ``typeof(x)``
 :func:`floor(T, x) <floor>` round ``x`` towards ``-Inf``       ``T``
 :func:`ceil(x) <ceil>`      round ``x`` towards ``+Inf``       ``typeof(x)``
 :func:`ceil(T, x) <ceil>`   round ``x`` towards ``+Inf``       ``T``
 :func:`trunc(x) <trunc>`    round ``x`` towards zero           ``typeof(x)``
 :func:`trunc(T, x) <trunc>` round ``x`` towards zero           ``T``
 =========================== ================================== =============

=========================== =============================================== =============
関数                        概要                                             戻り値の型
=========================== =============================================== =============
:func:`round(x) <round>`    ``x`` を最も近い整数になるよう丸めを行う              ``typeof(x)``
:func:`round(T, x) <round>` ``x`` を最も近い整数になるよう丸めを行う              ``T``
:func:`floor(x) <floor>`    ``x`` を ``-Inf`` に向かって丸めを行う              ``typeof(x)``
:func:`floor(T, x) <floor>` ``x`` を ``-Inf`` に向かって丸めを行う              ``T``
:func:`ceil(x) <ceil>`      ``x`` を ``+Inf`` に向かって丸めを行う              ``typeof(x)``
:func:`ceil(T, x) <ceil>`   ``x`` を ``+Inf`` に向かって丸めを行う              ``T``
:func:`trunc(x) <trunc>`    ``x`` を0に向かって丸めを行う                       ``typeof(x)``
:func:`trunc(T, x) <trunc>` ``x`` を0に向かって丸めを行う                       ``T``
=========================== =============================================== =============

..
 Division functions
 ~~~~~~~~~~~~~~~~~~

除算関数
~~~~~~~~~~~~~~~~~~

..
 ============================ =======================================================================
 Function                     Description
 ============================ =======================================================================
 :func:`div(x,y) <div>`       truncated division; quotient rounded towards zero
 :func:`fld(x,y) <fld>`       floored division; quotient rounded towards ``-Inf``
 :func:`cld(x,y) <cld>`       ceiling division; quotient rounded towards ``+Inf``
 :func:`rem(x,y) <rem>`       remainder; satisfies ``x == div(x,y)*y + rem(x,y)``; sign matches ``x``
 :func:`mod(x,y) <mod>`       modulus; satisfies ``x == fld(x,y)*y + mod(x,y)``; sign matches ``y``
 :func:`mod1(x,y) <mod1>`     ``mod()`` with offset 1; returns ``r∈(0,y]`` for ``y>0`` or ``r∈[y,0)`` for ``y<0``, where ``mod(r, y) == mod(x, y)``
 :func:`mod2pi(x) <mod2pi>`   modulus with respect to 2pi;  ``0 <= mod2pi(x)  < 2pi``
 :func:`divrem(x,y) <divrem>` returns ``(div(x,y),rem(x,y))``
 :func:`fldmod(x,y) <fldmod>` returns ``(fld(x,y),mod(x,y))``
 :func:`gcd(x,y...) <gcd>`    greatest positive common divisor of ``x``, ``y``,...
 :func:`lcm(x,y...) <lcm>`    least positive common multiple of ``x``, ``y``,...
 ============================ =======================================================================

============================ =======================================================================
関数                         概要
============================ =======================================================================
:func:`div(x,y) <div>`       0に向かって丸めを行う除算
:func:`fld(x,y) <fld>`       床関数のように ``-Inf`` に向かって丸めを行う除算
:func:`cld(x,y) <cld>`       天井関数のように ``+Inf`` に向かって丸めを行う除算
:func:`rem(x,y) <rem>`       余り； ``x == div(x,y)*y + rem(x,y)`` を満たす；記号は ``x`` と一致
:func:`mod(x,y) <mod>`       剰余演算； ``x == fld(x,y)*y + mod(x,y)`` を満たす；記号は ``y`` と一致
:func:`mod1(x,y) <mod1>`     オフセットが1の ``mod()`` ； ``mod(r, y) == mod(x, y)`` の場合、  ``y>0`` に ``r∈(0,y]`` を、　 ``y<0`` に ``r∈[y,0)`` を返す
:func:`mod2pi(x) <mod2pi>`   2piを法とする； ``0 <= mod2pi(x)  < 2pi``
:func:`divrem(x,y) <divrem>` ``(div(x,y),rem(x,y))`` を返す
:func:`fldmod(x,y) <fldmod>` ``(fld(x,y),mod(x,y))`` を返す
:func:`gcd(x,y...) <gcd>`    ``x`` 、 ``y`` 、...の最大の正の因数
:func:`lcm(x,y...) <lcm>`    ``x`` 、 ``y`` 、...の最小の正の公倍数
============================ =======================================================================

..
 Sign and absolute value functions
 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

符号と絶対値関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================================ ===========================================================
関数                             概要
================================ ===========================================================
:func:`abs(x) <abs>`             絶対値 ``x`` の正の値
:func:`abs2(x) <abs2>`           絶対値 ``x`` の2乗
:func:`sign(x) <sign>`           ``x`` の符号を示し、 -1、 0、 または +1を返す
:func:`signbit(x) <signbit>`     符号ビットがオン（true）またはオフ（false）であるかを示す
:func:`copysign(x,y) <copysign>` 絶対値 ``x`` と符号 ``y`` を持つ値
:func:`flipsign(x,y) <flipsign>` 絶対値 ``x`` と符号 ``x*y`` を持つ値
================================ ===========================================================

..
 Powers, logs and roots
 ~~~~~~~~~~~~~~~~~~~~~~

累乗、対数、根
~~~~~~~~~~~~~~~~~~~~~~

..
 ==================================== ==============================================================================
 Function                             Description
 ==================================== ==============================================================================
 :func:`sqrt(x) <sqrt>` ``√x``        square root of ``x``
 :func:`cbrt(x) <cbrt>` ``∛x``        cube root of ``x``
 :func:`hypot(x,y) <hypot>`           hypotenuse of right-angled triangle with other sides of length ``x`` and ``y``
 :func:`exp(x) <exp>`                 natural exponential function at ``x``
 :func:`expm1(x) <expm1>`             accurate ``exp(x)-1`` for ``x`` near zero
 :func:`ldexp(x,n) <ldexp>`           ``x*2^n`` computed efficiently for integer values of ``n``
 :func:`log(x) <log>`                 natural logarithm of ``x``
 :func:`log(b,x) <log>`               base ``b`` logarithm of ``x``
 :func:`log2(x) <log2>`               base 2 logarithm of ``x``
 :func:`log10(x) <log10>`             base 10 logarithm of ``x``
 :func:`log1p(x) <log1p>`             accurate ``log(1+x)`` for ``x`` near zero
 :func:`exponent(x) <exponent>`       binary exponent of ``x``
 :func:`significand(x) <significand>` binary significand (a.k.a. mantissa) of a floating-point number ``x``
 ==================================== ==============================================================================

==================================== ==============================================================================
関数                                 概要
==================================== ==============================================================================
:func:`sqrt(x) <sqrt>` ``√x``        ``x`` の平方根
:func:`cbrt(x) <cbrt>` ``∛x``        ``x`` の立方根
:func:`hypot(x,y) <hypot>`           長さ ``x`` および ``y`` をその他の辺に持つ直角三角形の斜辺
:func:`exp(x) <exp>`                 ``x`` における自然指数関数
:func:`expm1(x) <expm1>`             0に近い ``x`` の正確な ``exp(x)-1`` の結果
:func:`ldexp(x,n) <ldexp>`           ``n`` の整数値に対して効率的に計算された ``x*2^n``
:func:`log(x) <log>`                 ``x`` の自然対数
:func:`log(b,x) <log>`               ``b`` を底とした ``x`` の対数
:func:`log2(x) <log2>`               2を底とした ``x`` の対数
:func:`log10(x) <log10>`             10を底とした ``x`` の対数
:func:`log1p(x) <log1p>`             0に近い ``x`` の正確な ``log(1+x)`` の結果
:func:`exponent(x) <exponent>`       ``x`` の2進指数
:func:`significand(x) <significand>` 浮動小数点数 ``x`` の2進仮数
==================================== ==============================================================================

..
 For an overview of why functions like :func:`hypot`, :func:`expm1`, and :func:`log1p`
 are necessary and useful, see John D. Cook's excellent pair
 of blog posts on the subject: `expm1, log1p,
 erfc <http://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/>`_,
 and
 `hypot <http://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/>`_.

:func:`hypot`、 :func:`expm1`、および :func:`log1p` などの関数が必要かつ有用な理由については、
`expm1、 log1p、
erfc <http://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/>`_
および
`hypot <http://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/>`_
に関するJohn D. Cookのブログ記事を参照してください。

..
 Trigonometric and hyperbolic functions
 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

三角関数と双曲線関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..
 All the standard trigonometric and hyperbolic functions are also defined::

     sin    cos    tan    cot    sec    csc
     sinh   cosh   tanh   coth   sech   csch
     asin   acos   atan   acot   asec   acsc
     asinh  acosh  atanh  acoth  asech  acsch
     sinc   cosc   atan2

標準的な全ての三角関数と双曲線関数も定義されています。::

    sin    cos    tan    cot    sec    csc
    sinh   cosh   tanh   coth   sech   csch
    asin   acos   atan   acot   asec   acsc
    asinh  acosh  atanh  acoth  asech  acsch
    sinc   cosc   atan2

..
 These are all single-argument functions, with the exception of
 `atan2 <https://en.wikipedia.org/wiki/Atan2>`_, which gives the angle
 in `radians <https://en.wikipedia.org/wiki/Radian>`_ between the *x*-axis
 and the point specified by its arguments, interpreted as *x* and *y*
 coordinates.

これらはすべて引数が1つの関数ですが、 `atan2 <https://en.wikipedia.org/wiki/Atan2>`_ は例外で、角度をx軸と引数で
指定された点の `ラジアン <https://en.wikipedia.org/wiki/Radian>`_ で表し、x座標とy座標として解釈します。

..
 Additionally, :func:`sinpi(x) <sinpi>` and :func:`cospi(x) <cospi>` are provided for more accurate computations
 of :func:`sin(pi*x) <sin>` and :func:`cos(pi*x) <cos>` respectively.

さらに、 :func:`sinpi(x) <sinpi>` および:func:`cospi(x) <cospi>` は、より正確な :func:`sin(pi*x) <sin>`
および :func:`cos(pi*x) <cos>` の計算のために提供されています。

..
 In order to compute trigonometric functions with degrees
 instead of radians, suffix the function with ``d``. For example, :func:`sind(x) <sind>`
 computes the sine of ``x`` where ``x`` is specified in degrees.
 The complete list of trigonometric functions with degree variants is::

     sind   cosd   tand   cotd   secd   cscd
     asind  acosd  atand  acotd  asecd  acscd

ラジアンではなく角度で三角関数を計算するためには、 ``d`` を関数の末尾に付与してください。
例えば、 :func:`sind(x) <sind>` は、角度で指定された ``x`` の正弦を計算します。
角度で計算できる三角関数のリストは以下の通りです。::

    sind   cosd   tand   cotd   secd   cscd
    asind  acosd  atand  acotd  asecd  acscd

..
 Special functions
 ~~~~~~~~~~~~~~~~~

特殊な関数
~~~~~~~~~~~~~~~~~

..
 =================================================== ==============================================================================
 Function                                            Description
 =================================================== ==============================================================================
 :func:`erf(x) <erf>`                                `error function <https://en.wikipedia.org/wiki/Error_function>`_ at ``x``
 :func:`erfc(x) <erfc>`                              complementary error function, i.e. the accurate version of ``1-erf(x)`` for large ``x``
 :func:`erfinv(x) <erfinv>`                          inverse function to :func:`erf`
 :func:`erfcinv(x) <erfinvc>`                        inverse function to :func:`erfc`
 :func:`erfi(x) <erfi>`                              imaginary error function defined as ``-im * erf(x * im)``, where :const:`im` is the imaginary unit
 :func:`erfcx(x) <erfcx>`                            scaled complementary error function, i.e. accurate ``exp(x^2) * erfc(x)`` for large ``x``
 :func:`dawson(x) <dawson>`                          scaled imaginary error function, a.k.a. Dawson function, i.e. accurate ``exp(-x^2) * erfi(x) * sqrt(pi) / 2`` for large ``x``
 :func:`gamma(x) <gamma>`                            `gamma function <https://en.wikipedia.org/wiki/Gamma_function>`_ at ``x``
 :func:`lgamma(x) <lgamma>`                          accurate ``log(gamma(x))`` for large ``x``
 :func:`lfact(x) <lfact>`                            accurate ``log(factorial(x))`` for large ``x``; same as ``lgamma(x+1)`` for ``x > 1``, zero otherwise
 :func:`digamma(x) <digamma>`                        `digamma function <https://en.wikipedia.org/wiki/Digamma_function>`_ (i.e. the derivative of :func:`lgamma`) at ``x``
 :func:`beta(x,y) <beta>`                            `beta function <https://en.wikipedia.org/wiki/Beta_function>`_ at ``x,y``
 :func:`lbeta(x,y) <lbeta>`                          accurate ``log(beta(x,y))`` for large ``x`` or ``y``
 :func:`eta(x) <eta>`                                `Dirichlet eta function <https://en.wikipedia.org/wiki/Dirichlet_eta_function>`_ at ``x``
 :func:`zeta(x) <zeta>`                              `Riemann zeta function <https://en.wikipedia.org/wiki/Riemann_zeta_function>`_ at ``x``
 |airylist|                                          `Airy Ai function <https://en.wikipedia.org/wiki/Airy_function>`_ at ``z``
 |airyprimelist|                                     derivative of the Airy Ai function at ``z``
 :func:`airybi(z) <airybi>`, ``airy(2,z)``           `Airy Bi function <https://en.wikipedia.org/wiki/Airy_function>`_ at ``z``
 :func:`airybiprime(z) <airybiprime>`, ``airy(3,z)`` derivative of the Airy Bi function at ``z``
 :func:`airyx(z) <airyx>`, ``airyx(k,z)``            scaled Airy AI function and ``k`` th derivatives at ``z``
 :func:`besselj(nu,z) <besselj>`                     `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the first kind of order ``nu`` at ``z``
 :func:`besselj0(z) <besselj0>`                      ``besselj(0,z)``
 :func:`besselj1(z) <besselj1>`                      ``besselj(1,z)``
 :func:`besseljx(nu,z) <besseljx>`                   scaled Bessel function of the first kind of order ``nu`` at ``z``
 :func:`bessely(nu,z) <bessely>`                     `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the second kind of order ``nu`` at ``z``
 :func:`bessely0(z) <bessely0>`                      ``bessely(0,z)``
 :func:`bessely1(z) <bessely0>`                      ``bessely(1,z)``
 :func:`besselyx(nu,z) <besselyx>`                   scaled Bessel function of the second kind of order ``nu`` at ``z``
 :func:`besselh(nu,k,z) <besselh>`                   `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the third kind (a.k.a. Hankel function) of order ``nu`` at ``z``; ``k`` must be either ``1`` or ``2``
 :func:`hankelh1(nu,z) <hankelh1>`                   ``besselh(nu, 1, z)``
 :func:`hankelh1x(nu,z) <hankelh1x>`                 scaled ``besselh(nu, 1, z)``
 :func:`hankelh2(nu,z) <hankelh2>`                   ``besselh(nu, 2, z)``
 :func:`hankelh2x(nu,z) <hankelh2x>`                 scaled ``besselh(nu, 2, z)``
 :func:`besseli(nu,z) <besseli>`                     modified `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the first kind of order ``nu`` at ``z``
 :func:`besselix(nu,z) <besselix>`                   scaled modified Bessel function of the first kind of order ``nu`` at ``z``
 :func:`besselk(nu,z) <besselk>`                     modified `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the second kind of order ``nu`` at ``z``
 :func:`besselkx(nu,z) <besselkx>`                   scaled modified Bessel function of the second kind of order ``nu`` at ``z``
 =================================================== ==============================================================================

=================================================== ==============================================================================
関数                                                概要
=================================================== ==============================================================================
:func:`erf(x) <erf>`                                ``x`` における `誤差関数 <https://en.wikipedia.org/wiki/Error_function>`_
:func:`erfc(x) <erfc>`                              大きな値 ``x`` における ``1-erf(x)``のより正確な処理が可能な相補誤差関数
:func:`erfinv(x) <erfinv>`                          :func:`erf` に対する逆関数
:func:`erfcinv(x) <erfinvc>`                        :func:`erfc` に対する逆関数
:func:`erfi(x) <erfi>`                              :const:`im` が虚数単位の ``-im * erf(x * im)`` として定義される虚数誤差関数
:func:`erfcx(x) <erfcx>`                            スケーリングされた相補誤差関数；大きな値 ``x`` に対する正確な ``exp(x^2) * erfc(x)``
:func:`dawson(x) <dawson>`                          スケーリングされた虚数誤差関数（ドーソン関数）；大きな値 ``x`` に対する正確な  ``exp(-x^2) * erfi(x) * sqrt(pi) / 2``
:func:`gamma(x) <gamma>`                            ``x`` における `ガンマ関数 <https://en.wikipedia.org/wiki/Gamma_function>`_
:func:`lgamma(x) <lgamma>`                          大きな値 ``x`` における ``log(gamma(x))``
:func:`lfact(x) <lfact>`                            大きな値 ``x`` における ``log(factorial(x))`` ； ``x > 1`` における ``lgamma(x+1)`` と同様（それ以外の場合は0）
:func:`digamma(x) <digamma>`                        `ディガンマ関数 <https://en.wikipedia.org/wiki/Digamma_function>`_ （または  ``x`` における :func:`lgamma`) の微分）
:func:`beta(x,y) <beta>`                            ``x,y`` における `ベータ関数 <https://en.wikipedia.org/wiki/Beta_function>`_
:func:`lbeta(x,y) <lbeta>`                          大きな値  ``x`` または ``y`` における正確な ``log(beta(x,y))``
:func:`eta(x) <eta>`                                ``x`` における `ディレクレイータ関数 <https://en.wikipedia.org/wiki/Dirichlet_eta_function>`_
:func:`zeta(x) <zeta>`                              ``x`` における `リーマンゼータ関数 <https://en.wikipedia.org/wiki/Riemann_zeta_function>`_
|airylist|                                          ``z`` における `エアリー関数（Ai） <https://en.wikipedia.org/wiki/Airy_function>`_
|airyprimelist|                                     ``z`` におけるエアリー関数（Ai）の微分
:func:`airybi(z) <airybi>`, ``airy(2,z)``           ``z`` における `エアリー関数（Bi） <https://en.wikipedia.org/wiki/Airy_function>`_
:func:`airybiprime(z) <airybiprime>`, ``airy(3,z)`` ``z`` におけるエアリー関数（Bi）の微分
:func:`airyx(z) <airyx>`, ``airyx(k,z)``            ``z`` におけるスケーリングされたエアリー関数（Ai）および ``k`` 次微分
:func:`besselj(nu,z) <besselj>`                     ``z`` における第1種の次数 ``nu`` の  `ベッセル関数 <https://en.wikipedia.org/wiki/Bessel_function>`_
:func:`besselj0(z) <besselj0>`                      ``besselj(0,z)``
:func:`besselj1(z) <besselj1>`                      ``besselj(1,z)``
:func:`besseljx(nu,z) <besseljx>`                   スケーリングされた ``z`` における第1種の次数  ``nu`` のベッセル関数
:func:`bessely(nu,z) <bessely>`                     ``z`` における第2種の次数 ``nu`` の `ベッセル関数 <https://en.wikipedia.org/wiki/Bessel_function>`_
:func:`bessely0(z) <bessely0>`                      ``bessely(0,z)``
:func:`bessely1(z) <bessely0>`                      ``bessely(1,z)``
:func:`besselyx(nu,z) <besselyx>`                   スケーリングされた ``z`` における第2種の次数 ``nu`` のベッセル関数
:func:`besselh(nu,k,z) <besselh>`                   ``z`` における第３種の次数 ``nu`` の `ベッセル関数（ハンケル関数） <https://en.wikipedia.org/wiki/Bessel_function>`_ ； ``k`` は ``1`` または ``2`` のいずれかでなければならない
:func:`hankelh1(nu,z) <hankelh1>`                   ``besselh(nu, 1, z)``
:func:`hankelh1x(nu,z) <hankelh1x>`                 スケーリングされた ``besselh(nu, 1, z)``
:func:`hankelh2(nu,z) <hankelh2>`                   ``besselh(nu, 2, z)``
:func:`hankelh2x(nu,z) <hankelh2x>`                 スケーリングされた ``besselh(nu, 2, z)``
:func:`besseli(nu,z) <besseli>`                     ``z`` における第1種の次数 ``nu`` の修正された `ベッセル関数 <https://en.wikipedia.org/wiki/Bessel_function>`_
:func:`besselix(nu,z) <besselix>`                   スケーリングされた ``z`` における第1種の次数 ``nu`` の修正されたベッセル関数
:func:`besselk(nu,z) <besselk>`                     ``z`` における第2種の次数 ``nu`` の修正された `ベッセル関数 <https://en.wikipedia.org/wiki/Bessel_function>`_
:func:`besselkx(nu,z) <besselkx>`                   スケーリングされた ``z`` における第2種の次数 ``nu`` の修正されたベッセル関数
=================================================== ==============================================================================

.. |airylist| replace:: :func:`airy(z) <airy>`, :func:`airyai(z) <airyai>`, ``airy(0,z)``
.. |airyprimelist| replace:: :func:`airyprime(z) <airyprime>`, :func:`airyaiprime(z) <airyaiprime>`, ``airy(1,z)``
