.. _man-functions:

.. currentmodule:: Base

..
 ***********
  Functions
 ***********

***********
 関数
***********

..
 In Julia, a function is an object that maps a tuple of argument values
 to a return value. Julia functions are not pure mathematical functions,
 in the sense that functions can alter and be affected by the global
 state of the program. The basic syntax for defining functions in Julia
 is:

Juliaでは、関数は、引数値のチューブルを戻り値にマップするオブジェクトです。Juliaの関数は純粋な数学関数ではなく、
関数がプログラムの全体的な状態によって変更され、影響を受けることがあります。Juliaで関数を定義するための基本的な構文は次のとおりです。

.. testcode::

    function f(x,y)
      x + y
    end

.. testoutput::
    :hide:

    f (generic function with 1 method)

..
 There is a second, more terse syntax for defining a function in Julia.
 The traditional function declaration syntax demonstrated above is
 equivalent to the following compact "assignment form"::

    f(x,y) = x + y

Juliaには関数を定義するためのより簡潔な構文があります上記の伝統的な関数宣言の構文は、
以下のコンパクトな「代入形式」と同一です。::

    f(x,y) = x + y

..
 In the assignment form, the body of the function must be a single
 expression, although it can be a compound expression (see
 :ref:`man-compound-expressions`). Short, simple
 function definitions are common in Julia. The short function syntax is
 accordingly quite idiomatic, considerably reducing both typing and
 visual noise.

代入形式では、関数の本体は単一の式でなければなりませんが、複合式も使用が可能です。（ :ref:`man-複合式` を参照）
Juliaでは、短く簡潔な関数定義が一般的です。短い関数構文は慣用的であり、タイピングと視覚的ノイズの両方を大幅に軽減します。

..
 A function is called using the traditional parenthesis syntax:

関数は従来の括弧構文を使用して呼び出されます。

.. doctest::

    julia> f(2,3)
    5

..
 Without parentheses, the expression ``f`` refers to the function object,
 and can be passed around like any value:

括弧なしでは、式 ``f`` は関数オブジェクトを参照し、他の値と同様に渡されることができます。

.. doctest::

    julia> g = f;

    julia> g(2,3)
    5

..
 As with variables, Unicode can also be used for function names:

変数と同様に、関数名にUnicodeを使用することもできます。

.. doctest::

    julia> ∑(x,y) = x + y
    ∑ (generic function with 1 method)

..
 Argument Passing Behavior
 -------------------------

引数渡し
-------------------------

..
 Julia function arguments follow a convention sometimes called "pass-by-sharing",
 which means that values are not copied when they are passed to functions.
 Function arguments themselves act as new variable *bindings* (new locations that
 can refer to values), but the values they refer to are identical to the passed
 values. Modifications to mutable values (such as Arrays) made within a function
 will be visible to the caller. This is the same behavior found in Scheme, most
 Lisps, Python, Ruby and Perl, among other dynamic languages.

Julia関数の引数は、「pass-by-sharing」と呼ばれることもあり、関数に渡されたときに値がコピーされないことを意味します。
関数の引数は、それ自体が新しい変数の紐付け（値を参照できる新しい場所）として機能しますが、
それらが参照する値は渡された値と同じです。関数内で行われる変更可能な値（配列など）の変更は、
呼び出し元に表示されます。これは、Scheme、ほとんどのLisps、Python、Ruby、Perlなど他の動的言語で見られるのと同じ挙動です。

.. _man-return-keyword:

..
 The ``return`` Keyword
 ----------------------

``return`` キーワード
----------------------

..
 The value returned by a function is the value of the last expression
 evaluated, which, by default, is the last expression in the body of the
 function definition. In the example function, ``f``, from the previous
 section this is the value of the expression ``x + y``. As in C and most
 other imperative or functional languages, the ``return`` keyword causes
 a function to return immediately, providing an expression whose value is
 returned::

    function g(x,y)
      return x * y
      x + y
    end

関数によって返される値は、処理された最後の式の値であり、デフォルトでは、関数定義の最後の式です。
前のセクションの関数 ``f`` の例では、返される値は式 ``x + y`` の値です。
C言語やその他の命令型言語や関数型言語の場合のように、 ``return`` キーワードを指定すると、
関数がすぐに返され、値が返された式を与えます。::

    function g(x,y)
      return x * y
      x + y
    end

..
 Since function definitions can be entered into interactive sessions, it
 is easy to compare these definitions::

    f(x,y) = x + y

    function g(x,y)
      return x * y
      x + y
    end

    julia> f(2,3)
    5

    julia> g(2,3)
    6

関数定義は対話型セッションに入力できるため、簡単に定義を比較することができます。::

    f(x,y) = x + y

    function g(x,y)
      return x * y
      x + y
    end

    julia> f(2,3)
    5

    julia> g(2,3)
    6

..
 Of course, in a purely linear function body like ``g``, the usage of
 ``return`` is pointless since the expression ``x + y`` is never
 evaluated and we could simply make ``x * y`` the last expression in the
 function and omit the ``return``. In conjunction with other control
 flow, however, ``return`` is of real use. Here, for example, is a
 function that computes the hypotenuse length of a right triangle with
 sides of length *x* and *y*, avoiding overflow::

    function hypot(x,y)
      x = abs(x)
      y = abs(y)
      if x > y
        r = y/x
        return x*sqrt(1+r*r)
      end
      if y == 0
        return zero(x)
      end
      r = x/y
      return y*sqrt(1+r*r)
    end

``g`` のような一次関数では、式 ``x + y`` は処理されず、 ``x * y`` を関数の最後の式にして戻り値を省略することができるため、
``return`` の使用は無意味です。しかし、他の制御フローと連動して、 ``return`` が必要となります。例えば、
長さ *x* および *y* の辺を持つ直角三角形の斜辺の長さを計算し、オーバーフローを回避する関数を以下に示します。::

    function hypot(x,y)
      x = abs(x)
      y = abs(y)
      if x > y
        r = y/x
        return x*sqrt(1+r*r)
      end
      if y == 0
        return zero(x)
      end
      r = x/y
      return y*sqrt(1+r*r)
    end

..
 There are three possible points of return from this function, returning
 the values of three different expressions, depending on the values of
 *x* and *y*. The ``return`` on the last line could be omitted since it
 is the last expression.

*x* および *y* の値によってこの関数から返されるポイントは3つあり、3つの異なる式の値が返されます。
最後の行の ``return`` は、最後の式であるため、省略することができます。

..
 Operators Are Functions
 -----------------------

演算子と関数
-----------------------

..
 In Julia, most operators are just functions with support for special
 syntax. (The exceptions are operators with special evaluation semantics
 like ``&&`` and ``||``. These operators cannot be functions since
 :ref:`short-circuit evaluation <man-short-circuit-evaluation>` requires that
 their operands are not evaluated before evaluation of the operator.)
 Accordingly, you can also apply them using parenthesized argument lists,
 just as you would any other function:

Juliaでは、ほとんどの演算子は特別な構文をサポートする関数であると言えます。（例外は、 ``&&`` や ``||`` のような
特別な処理をする演算子があります。これらの演算子は、 :ref:`短絡評価 <man-short-circuit-evaluation>` では演算子を
処理する前に被演算子が処理されないため、関数ではありません。）他の関数と同様に、
括弧で囲まれた引数リストを使用して、それらを適用することもできます。

.. doctest::

    julia> 1 + 2 + 3
    6

    julia> +(1,2,3)
    6

..
 The infix form is exactly equivalent to the function application form —
 in fact the former is parsed to produce the function call internally.
 This also means that you can assign and pass around operators such as
 :func:`+` and :func:`*` just like you would with other function values:

挿入形式は、関数のアプリケーション形式と全く同じです。実際は、前者は内部的に関数呼び出しを生成する処理をします。
他の関数値と同じように、 :func:`+` や :func:`*` などの演算子を指定し渡すこともできます。

.. doctest:: f-plus

    julia> f = +;

    julia> f(1,2,3)
    6

..
 Under the name ``f``, the function does not support infix notation,
 however.

しかし、 ``f`` 関数は、挿入法をサポートしていません。

..
 Operators With Special Names
 ----------------------------

特別な名前の演算子
----------------------------

..
 A few special expressions correspond to calls to functions with non-obvious
 names. These are:

以下のような特別な式は、明白でない名前の関数の呼び出しに対応しています。

..
 =================== ==================
 Expression          Calls
 =================== ==================
 ``[A B C ...]``     :func:`hcat`
 ``[A, B, C, ...]``  :func:`vcat`
 ``[A B; C D; ...]`` :func:`hvcat`
 ``A'``              :func:`ctranspose`
 ``A.'``             :func:`transpose`
 ``1:n``             :func:`colon`
 ``A[i]``            :func:`getindex`
 ``A[i]=x``          :func:`setindex!`
 =================== ==================

=================== ==================
式                  呼び出し
=================== ==================
``[A B C ...]``     :func:`hcat`
``[A, B, C, ...]``  :func:`vcat`
``[A B; C D; ...]`` :func:`hvcat`
``A'``              :func:`ctranspose`
``A.'``             :func:`transpose`
``1:n``             :func:`colon`
``A[i]``            :func:`getindex`
``A[i]=x``          :func:`setindex!`
=================== ==================

..
 These functions are included in the ``Base.Operators`` module even
 though they do not have operator-like names.

これらの関数は、演算子のような名前ではありませんが ``Base.Operators`` モジュールに含まれています。

.. _man-anonymous-functions:

..
 Anonymous Functions
 -------------------

無名関数
-------------------

..
 Functions in Julia are `first-class objects
 <https://en.wikipedia.org/wiki/First-class_citizen>`_: they can be assigned to
 variables, and called using the standard function call syntax from the
 variable they have been assigned to. They can be used as arguments, and
 they can be returned as values. They can also be created anonymously,
 without being given a name, using either of these syntaxes:

Juliaの関数は `第1級オブジェクト <https://en.wikipedia.org/wiki/First-class_citizen>`_ です。
関数は変数に代入することができ、代入された変数から標準の関数呼び出し構文を使用して呼び出すことができます。
関数は引数として使用でき、値として返すことができます。以下の構文のいずれかを使用して、
名前を付けずに匿名で関数を作成することもできます。

.. doctest::

    julia> x -> x^2 + 2x - 1
    (::#1) (generic function with 1 method)

    julia> function (x)
               x^2 + 2x - 1
           end
    (::#3) (generic function with 1 method)

..
 This creates a function taking one argument *x* and returning the
 value of the polynomial *x*\ ^2 + 2\ *x* - 1 at that value.
 Notice that the result is a generic function, but with a
 compiler-generated name based on consecutive numbering.

これは、引数 *x* をとり、その値で多項式 *x*\ ^2 + 2\ *x* - 1 の結果を返す関数を作成します。
この結果は汎用関数ですが、連続した番号付けに基づくコンパイラ生成の名前が付いています。

..
 The primary use for anonymous functions is passing them to functions which take
 other functions as arguments. A classic example is :func:`map`,
 which applies a function to each value of an array and returns a new
 array containing the resulting values:

無名関数の主な用途は、他の関数を引数とする関数に渡すことです。典型的な例は :func:`map` で、
配列の各値に関数を適用し、結果の値を持つ新しい配列を返します。

.. doctest::

    julia> map(round, [1.2,3.5,1.7])
    3-element Array{Float64,1}:
     1.0
     4.0
     2.0

..
 This is fine if a named function effecting the transform one wants
 already exists to pass as the first argument to :func:`map`. Often, however,
 a ready-to-use, named function does not exist. In these situations, the
 anonymous function construct allows easy creation of a single-use
 function object without needing a name:

これは、変換を有効にする名前付き関数がすでにある関数に :func:`map` への最初の引数として渡す場合には問題ありません。
しかし、すぐに使える名前付き関数は多くの場合存在しません。このような場合、無名関数を使用すると、
名前を付けることなく使いやすい関数オブジェクトを簡単に生成できます。

.. doctest::

    julia> map(x -> x^2 + 2x - 1, [1,3,-1])
    3-element Array{Int64,1}:
      2
     14
     -2

..
 An anonymous function accepting multiple arguments can be written using
 the syntax ``(x,y,z)->2x+y-z``. A zero-argument anonymous function is
 written as ``()->3``. The idea of a function with no arguments may seem
 strange, but is useful for "delaying" a computation. In this usage, a
 block of code is wrapped in a zero-argument function, which is later
 invoked by calling it as ``f()``.

複数の引数を取る無名関数は、構文 ``(x,y,z)->2x+y-z`` を使用して記述できます。引数のない無名関数は、
``()->3`` と書くことができます。引数を持たない関数の考え方は不思議に思えるかもしれませんが、
計算を「遅らせる」際に便利です。この使用法では、コードのブロックは引数が
無い関数にラップされ、後で ``f()`` として呼び出されます。

..
 Multiple Return Values
 ----------------------

複数の戻り値
----------------------

..
 In Julia, one returns a tuple of values to simulate returning multiple
 values. However, tuples can be created and destructured without needing
 parentheses, thereby providing an illusion that multiple values are
 being returned, rather than a single tuple value. For example, the
 following function returns a pair of values:

Juliaでは、複数の値を返すシミュレーションするために値のチュープルを返します。しかし、チュープルは、
括弧無しで作成および破棄することができ、それにより1つのチュープル値ではなく、複数の値が返されるという錯覚が発生します。
例えば、次の関数は1組の値を返します。

.. doctest::

    julia> function foo(a,b)
             a+b, a*b
           end;

..
 If you call it in an interactive session without assigning the return
 value anywhere, you will see the tuple returned:

戻り値をどこにも指定せずに対話セッションで関数を呼び出すと、返されたチュープルが表示されます。

.. doctest::

    julia> foo(2,3)
    (5,6)

..
 A typical usage of such a pair of return values, however, extracts each
 value into a variable. Julia supports simple tuple "destructuring" that
 facilitates this:

しかし、このような1組の戻り値の典型的な使用法は、それぞれの値を変数に抽出します。Juliaは、
これを容易にするチュープル「構造解除」をサポートしています。

.. doctest::

    julia> x, y = foo(2,3);

    julia> x
    5

    julia> y
    6

..
 You can also return multiple values via an explicit usage of the
 ``return`` keyword::

    function foo(a,b)
      return a+b, a*b
    end

また、 ``return`` キーワードを明示的に使用して複数の値を返すこともできます。::

    function foo(a,b)
      return a+b, a*b
    end

..
 This has the exact same effect as the previous definition of ``foo``.

これは前述の ``foo`` の定義とまったく同じ効果があります。

.. _man-varargs-functions:

..
 Varargs Functions
 -----------------

可変引数関数
-----------------

..
 It is often convenient to be able to write functions taking an arbitrary
 number of arguments. Such functions are traditionally known as "varargs"
 functions, which is short for "variable number of arguments". You can
 define a varargs function by following the last argument with an
 ellipsis:

任意の数の引数を取って関数を書くことができるとは便利です。そのような関数は「variable number of arguments」の
略語である 「varargs（可変引数）」関数として知られています。最後の引数の後に省略記号をつけることで、
varargs関数を定義することができます。

.. doctest::

    julia> bar(a,b,x...) = (a,b,x)
    bar (generic function with 1 method)

..
 The variables ``a`` and ``b`` are bound to the first two argument values
 as usual, and the variable ``x`` is bound to an iterable collection of
 the zero or more values passed to ``bar`` after its first two arguments:

変数 ``a`` および ``b`` は、通常のように最初の2つの引数値に紐付き、変数 ``x`` は、
最初の2つの引数の後に ``bar`` に渡される繰り返し処理可能な0またはそれ以上の値に紐付きます。

.. doctest::

    julia> bar(1,2)
    (1,2,())

    julia> bar(1,2,3)
    (1,2,(3,))

    julia> bar(1,2,3,4)
    (1,2,(3,4))

    julia> bar(1,2,3,4,5,6)
    (1,2,(3,4,5,6))

..
 In all these cases, ``x`` is bound to a tuple of the trailing values
 passed to ``bar``.

これらのケースの場合、 ``x`` は ``bar`` に渡された末尾の値のチュープルに紐付きます。

..
 It is possible to constrain the number of values passed as a variable argument; this will be discussed later in :ref:`man-vararg-fixedlen`.

渡される値の数を可変引数として制限することは可能です。これに関する詳細は後の :ref:`man-パラメトリック制約付き可変引数メソッド` で
説明されます。

..
 On the flip side, it is often handy to "splice" the values contained in
 an iterable collection into a function call as individual arguments. To
 do this, one also uses ``...`` but in the function call instead:

繰り返し処理コレクションに含まれる値を個々の引数として関数呼び出しに「継承」することはしばしば便利です。
これを行うには、関数呼び出しで ``...`` を使用します。

.. doctest::

    julia> x = (3,4)
    (3,4)

    julia> bar(1,2,x...)
    (1,2,(3,4))

..
 In this case a tuple of values is spliced into a varargs call precisely
 where the variable number of arguments go. This need not be the case,
 however:

この場合、値のチュープルは、可変引数がどこに渡されるか可変引数の呼び出しに継承されます。
しかし、そうである必要はありません。

.. doctest::

    julia> x = (2,3,4)
    (2,3,4)

    julia> bar(1,x...)
    (1,2,(3,4))

    julia> x = (1,2,3,4)
    (1,2,3,4)

    julia> bar(x...)
    (1,2,(3,4))

..
 Furthermore, the iterable object spliced into a function call need not
 be a tuple:

さらに、関数呼び出しに継承された繰り返し処理可能オブジェクトはチュープルである必要はありません。

.. doctest::

    julia> x = [3,4]
    2-element Array{Int64,1}:
     3
     4

    julia> bar(1,2,x...)
    (1,2,(3,4))

    julia> x = [1,2,3,4]
    4-element Array{Int64,1}:
     1
     2
     3
     4

    julia> bar(x...)
    (1,2,(3,4))

..
 Also, the function that arguments are spliced into need not be a varargs
 function (although it often is):

また、引数が継承された関数は、可変引数関数である必要はありません。

.. doctest::

    julia> baz(a,b) = a + b;

    julia> args = [1,2]
    2-element Array{Int64,1}:
     1
     2

    julia> baz(args...)
    3

    julia> args = [1,2,3]
    3-element Array{Int64,1}:
     1
     2
     3

    julia> baz(args...)
    ERROR: MethodError: no method matching baz(::Int64, ::Int64, ::Int64)
    Closest candidates are:
      baz(::Any, ::Any) at none:1
    ...

..
 As you can see, if the wrong number of elements are in the spliced
 container, then the function call will fail, just as it would if too
 many arguments were given explicitly.

例にある通り、継承された中に間違った数の要素がある場合、あまりにも多くの引数が明示的に与えられた場合と同様に、
関数呼び出しは失敗します。

..
 Optional Arguments
 ------------------

オプションの引数
------------------

..
 In many cases, function arguments have sensible default values and therefore
 might not need to be passed explicitly in every call. For example, the
 library function :func:`parse(type,num,base) <parse>` interprets a string as a number
 in some base. The ``base`` argument defaults to ``10``. This behavior can be
 expressed concisely as::

    function parse(type, num, base=10)
        ###
    end

多くの場合、関数の引数は明確なデフォルト値を持つため、引数は全ての呼び出しの際に明示的に与える必要はありません。
例えば、ライブラリ関数 :func:`parse(type,num,base) <parse>` は、文字列を何らかの数値として解釈します。
``base`` 引数のデフォルトは ``10`` です。この動作は、以下のように簡潔に表現できます。::

    function parse(type, num, base=10)
        ###
    end

..
 With this definition, the function can be called with either two or three
 arguments, and ``10`` is automatically passed when a third argument is not
 specified:

この定義では、関数は2つまたは3つの引数を使って呼び出すことができ、
3つ目の引数が指定されていない場合は自動的に ``10`` が渡されます。

.. doctest::

    julia> parse(Int,"12",10)
    12

    julia> parse(Int,"12",3)
    5

    julia> parse(Int,"12")
    12

..
 Optional arguments are actually just a convenient syntax for writing
 multiple method definitions with different numbers of arguments
 (see :ref:`man-note-on-optional-and-keyword-arguments`).

オプションの引数は、引数の数が異なる複数のメソッド定義を記述する際に便利な構文です。
（ :ref:`man-オプションとキーワードの引数に関する注意` を参照してください。）

..
 Keyword Arguments
 -----------------

キーワード引数
-----------------

..
 Some functions need a large number of arguments, or have a large number of
 behaviors. Remembering how to call such functions can be difficult. Keyword
 arguments can make these complex interfaces easier to use and extend by
 allowing arguments to be identified by name instead of only by position.

いくつかの関数は、多数の引数を必要とするか、または多数の挙動を持ちます。
関数の呼び出し方法を覚えておくのは難しいことです。キーワード引数を使用することで、
引数を位置だけでなく名前で識別できるため、これらの複雑なインタフェースを使いやすく拡張することができます。

..
 For example, consider a function ``plot`` that
 plots a line. This function might have many options, for controlling line
 style, width, color, and so on. If it accepts keyword arguments, a possible
 call might look like ``plot(x, y, width=2)``, where we have chosen to
 specify only line width. Notice that this serves two purposes. The call is
 easier to read, since we can label an argument with its meaning. It also
 becomes possible to pass any subset of a large number of arguments, in
 any order.

例えば、線を引く ``plot`` 関数を考えてみましょう。この関数には、線のスタイル、幅、
色などを設定するための多くのオプションがあります。キーワード引数が使える場合、
線の幅のみを指定した関数の呼び出しは ``plot(x, y, width=2)`` のようになるかもしれません。
これは2つの目的に役立ちます。引数の意味に関連するラベルを付けることができるので、
関数の呼び出し式は読みやすくなります。また、多数の引数のサブセットを任意の順序で渡すことも可能になります。

..
 Functions with keyword arguments are defined using a semicolon in the
 signature::

     function plot(x, y; style="solid", width=1, color="black")
         ###
     end

キーワード引数を持つ関数は、セミコロンを使用して定義されます。::

    function plot(x, y; style="solid", width=1, color="black")
        ###
    end

..
 When the function is called, the semicolon is optional: one can
 either call ``plot(x, y, width=2)`` or ``plot(x, y; width=2)``, but
 the former style is more common.  An explicit semicolon is required only
 for passing varargs or computed keywords as described below.

関数が呼び出されるとき、セミコロンはオプションです。 ``plot(x, y, width=2)`` または ``plot(x, y; width=2)`` で
呼び出すことができますが、前者の形式がより一般的です。明示的なセミコロンは、以下で説明するように、
可変引数または計算キーワードを渡すためにのみ必要です。

..
 Keyword argument default values are evaluated only when necessary
 (when a corresponding keyword argument is not passed), and in
 left-to-right order. Therefore default expressions may refer to
 prior keyword arguments.

キーワード引数のデフォルト値は、必要なときのみ（対応するキーワード引数が渡されない場合）および
左から右の順序でのみ処理されます。したがって、デフォルトの式はキーワードの前の引数を参照することがあります。

..
The types of keyword arguments can be made explicit as follows::

    function f(;x::Int64=1)
        ###
    end

キーワード引数の型は、以下のように明示的に指定できます。::

    function f(;x::Int64=1)
        ###
    end

..
 Extra keyword arguments can be collected using ``...``, as in varargs
 functions::

    function f(x; y=0, kwargs...)
        ###
    end

可変引数関数のように、 ``...`` を使用して追加のキーワード引数を指定することができます。::

    function f(x; y=0, kwargs...)
        ###
    end

..
 Inside ``f``, ``kwargs`` will be a collection of ``(key,value)`` tuples,
 where each ``key`` is a symbol. Such collections can be passed as keyword
 arguments using a semicolon in a call, e.g. ``f(x, z=1; kwargs...)``.
 Dictionaries can also be used for this purpose.

``f`` の内部では、 ``kwargs`` は各 ``key`` がシンボルである ``(key,value)`` のチュープルのコレクションになります。
そのようなコレクションは、呼び出しの際にセミコロンを使用してキーワード引数として渡すことができます。
（例 ``f(x, z=1; kwargs...)`` ）辞書もこの目的のために使用することができます。

..
 One can also pass ``(key,value)`` tuples, or any iterable
 expression (such as a ``=>`` pair) that can be assigned to such a
 tuple, explicitly after a semicolon.  For example, ``plot(x, y;
 (:width,2))`` and ``plot(x, y; :width => 2)`` are equivalent to
 ``plot(x, y, width=2)``.  This is useful in situations where the
 keyword name is computed at runtime.

セミコロンの後にこのようなチュープルに割り当てられる ``(key,value)`` チュープル、
または繰り返し処理可能な式（ ``=>`` ペアなど）を渡すこともできます。例えば、
``plot(x, y; (:width,2))`` および ``plot(x, y; :width => 2)`` は ``plot(x, y, width=2)`` と同義です。
これは、実行時にキーワード名が計算される場合に便利です。

..
 The nature of keyword arguments makes it possible to specify the same
 argument more than once. For example, in the call
 ``plot(x, y; options..., width=2)`` it is possible that the ``options``
 structure also contains a value for ``width``. In such a case the
 rightmost occurrence takes precedence; in this example, ``width``
 is certain to have the value ``2``.

キーワード引数の性質により、同じ引数を複数回指定することができます。例えば ``plot(x, y; options..., width=2)`` の呼び出しで
``options`` 構造体に ``width`` の値を与えることができます。このような場合、最も右端の値が優先されます。
この例では、 ``width`` の値は  ``2`` となります。

.. _man-evaluation-scope-default-values:

..
 Evaluation Scope of Default Values
 ----------------------------------

デフォルト値の処理範囲
----------------------------------

..
 Optional and keyword arguments differ slightly in how their default
 values are evaluated. When optional argument default expressions are
 evaluated, only *previous* arguments are in scope. In contrast, *all*
 the arguments are in scope when keyword arguments default expressions
 are evaluated. For example, given this definition::

    function f(x, a=b, b=1)
        ###
    end

オプションとキーワードの引数は、デフォルト値の処理方法が若干異なります。オプションの引数のデフォルト式が処理されるとき、
前の引数だけが処理範囲内になります。対照的に、キーワード引数のデフォルト式が処理されるときは、
すべての引数が処理範囲内になります。例えば、次の定義の場合、::

    function f(x, a=b, b=1)
        ###
    end

..
 the ``b`` in ``a=b`` refers to a ``b`` in an outer scope, not the
 subsequent argument ``b``. However, if ``a`` and ``b`` were keyword
 arguments instead, then both would be created in the same scope and
 the ``b`` in ``a=b`` would refer to the subsequent argument ``b``
 (shadowing any ``b`` in an outer scope), which would result in an
 undefined variable error (since the default expressions are evaluated
 left-to-right, and ``b`` has not been assigned yet).

``a=b`` の ``b`` は、後続の引数 ``b`` ではなく、この例の外側の ``b`` を参照します。しかし、 ``a`` と ``b`` がキーワード引数であれば、
どちらも同じ処理範囲内に作成され、 ``a=b`` の ``b`` は後続の引数 ``b`` を参照し、変数未定義エラーとなります。
（デフォルトの式は左から右に処理され、 ``b`` はまだ割り当てられていないため。）

..
 Do-Block Syntax for Function Arguments
 --------------------------------------

関数引数のためのDo-Block構文
--------------------------------------

..
 Passing functions as arguments to other functions is a powerful technique,
 but the syntax for it is not always convenient. Such calls are especially
 awkward to write when the function argument requires multiple lines. As
 an example, consider calling :func:`map` on a function with several cases::

    map(x->begin
               if x < 0 && iseven(x)
                   return 0
               elseif x == 0
                   return 1
               else
                   return x
               end
           end,
        [A, B, C])

他の関数への引数として関数を渡すことは強力な手法ですが、その構文は必ずしも便利ではありません。
このような関数の呼び出しは、関数の引数に複数の行が必要な際に書くのが特に厄介になります。
例として、 :func:`map` 関数を呼び出すケースを考えてみましょう。::

    map(x->begin
               if x < 0 && iseven(x)
                   return 0
               elseif x == 0
                   return 1
               else
                   return x
               end
           end,
        [A, B, C])

..
 Julia provides a reserved word ``do`` for rewriting this code more clearly::

    map([A, B, C]) do x
        if x < 0 && iseven(x)
            return 0
        elseif x == 0
            return 1
        else
            return x
        end
    end

Juliaは、このコードをより明確に書き換えるために予約語 ``do`` を提供しています。::

    map([A, B, C]) do x
        if x < 0 && iseven(x)
            return 0
        elseif x == 0
            return 1
        else
            return x
        end
    end

..
 The ``do x`` syntax creates an anonymous function with argument ``x``
 and passes it as the first argument to :func:`map`. Similarly, ``do a,b``
 would create a two-argument anonymous function, and a plain ``do``
 would declare that what follows is an anonymous function of the form
 ``() -> ...``.

``do x`` 構文は、引数 ``x`` を持つ無名関数を作成し、それを :func:`map` の最初の引数として渡します。
同様に、 ``do a,b`` は2つの引数を持つ無名関数を作成し、ただの ``do`` は続く次の記述が ``() -> ...`` 形式の
無名関数であるでと宣言します。

..
 How these arguments are initialized depends on the "outer" function;
 here, :func:`map` will sequentially set ``x`` to ``A``, ``B``, ``C``,
 calling the anonymous function on each, just as would happen in the
 syntax ``map(func, [A, B, C])``.

これらの引数がどのように初期化されるかは、「外側」の関数によって決まります。
:func:`map` は、構文 ``map(func, [A, B, C])`` の場合と同様に、 ``x`` を ``A`` 、 ``B`` 、 ``C`` に順番に
設定してそれぞれに無名関数を呼び出します。

..
 This syntax makes it easier to use functions to effectively extend the
 language, since calls look like normal code blocks. There are many
 possible uses quite different from :func:`map`, such as managing system
 state. For example, there is a version of :func:`open` that runs code
 ensuring that the opened file is eventually closed::

    open("outfile", "w") do io
        write(io, data)
    end

この構文は、呼び出しが通常のコードブロックのように見えるため、関数を使用して効果的かつ容易に言語を拡張することができます。
システム状態の管理など、 :func:`map` とはまったく異なるさまざまな用途があります。例えば、開かれたファイルが後に閉じられることを
保証するコードを実行する :func:`open` があります。::

    open("outfile", "w") do io
        write(io, data)
    end

..
 This is accomplished by the following definition::

    function open(f::Function, args...)
        io = open(args...)
        try
            f(io)
        finally
            close(io)
        end
    end

これは、次の定義によって実行できます。::

    function open(f::Function, args...)
        io = open(args...)
        try
            f(io)
        finally
            close(io)
        end
    end

..
 Here, :func:`open` first opens the file for writing and then passes
 the resulting output stream to the anonymous function you defined
 in the ``do ... end`` block. After your function exits, :func:`open`
 will make sure that the stream is properly closed, regardless of
 whether your function exited normally or threw an exception.
 (The ``try/finally`` construct will be described in
 :ref:`man-control-flow`.)

:func:`open` は最初にファイルを書き込み用に開き、一連の結果の出力を ``do ... end`` ブロックで定義した無名関数に渡します。
関数の処理が終了した後、関数が正常に終了したか異常終了したかにかかわらず、 :func:`open` はデータ取得が正しく終了したことを確認します。
（ ``try/finally`` 構造体については、 :ref:`man-コントロールフロー` に説明があります。）

..
 With the ``do`` block syntax, it helps to check the documentation or
 implementation to know how the arguments of the user function are
 initialized.

``do`` ブロック構文では、ユーザ定義関数の引数がどのように初期化されるかを知るため、記述や実装をチェックします。

.. _man-dot-vectorizing:

..
 Dot Syntax for Vectorizing Functions
 ------------------------------------

ベクトル化関数のドット構文
------------------------------------

..
 In technical-computing languages, it is common to have "vectorized" versions of
 functions, which simply apply a given function ``f(x)`` to each element of an
 array ``A`` to yield a new array via ``f(A)``.   This kind of syntax is
 convenient for data processing, but in other languages vectorization is also
 often required for performance: if loops are slow, the "vectorized" version of a
 function can call fast library code written in a low-level language.   In Julia,
 vectorized functions are *not* required for performance, and indeed it is often
 beneficial to write your own loops (see :ref:`man-performance-tips`), but they
 can still be convenient.  Therefore, *any* Julia function ``f`` can be applied
 elementwise to any array (or other collection) with the syntax ``f.(A)``.

プログラミング言語では、関数の「ベクトル化」バージョンを持つことが一般的です。
これは、関数 ``f(x)`` を配列 ``A`` の各要素に適用して ``f(A)`` を介して新しい配列を生成します。
この種の構文はデータ処理に便利ですが、他の言語では、ベクトル化はパフォーマンスのために必要です。
ループが遅い場合、関数の「ベクトル化」バージョンでは、低いレベルの言語で書かれた速いライブラリコードを
呼び出すことができます。Juliaでは、ベクトル化された関数はパフォーマンスのために必要ではなく、
独自のループを書くことで有益になります。（「パフォーマンスに関するヒント」を参照）Julia関数 ``f`` は、
``f.(A)``の構文を使用して配列（または他のコレクション）に要素ごとに適用することができます。

..
 Of course, you can omit the dot if you write a specialized "vector" method
 of ``f``, e.g. via ``f(A::AbstractArray) = map(f, A)``, and this is just
 as efficient as ``f.(A)``.   But that approach requires you to decide in advance
 which functions you want to vectorize.

``f`` の特殊な「ベクトル」メソッドを書くと、 ``f(A::AbstractArray) = map(f, A)`` のようにドットを省略することができ、
これは ``f.(A)`` と同様に効率的です。しかし、この方法では、どの関数をベクトル化するかを事前に決めておく必要があります。

..
 More generally, ``f.(args...)`` is actually equivalent to
 ``broadcast(f, args...)``, which allows you to operate on multiple
 arrays (even of different shapes), or a mix of arrays and scalars
 (see :ref:`man-broadcasting`).  For example, if you have ``f(x,y) = 3x + 4y``,
 then ``f.(pi,A)`` will return a new array consisting of ``f(pi,a)`` for each
 ``a`` in ``A``, and ``f.(vector1,vector2)`` will return a new vector
 consisting of ``f(vector1[i],vector2[i])`` for each index ``i``
 (throwing an exception if the vectors have different length).

一般的には、 ``f.(args...)`` は ``broadcast(f, args...)`` と同一で、複数の配列（異なる形状のものでも）や配列と
スカラーの組み合わせで操作できます。（詳細は :ref:`man-ブロードキャスティング` を参照してください。）例えば、
``f(x,y) = 3x + 4y`` の場合、 ``f(pi,a)`` は各 ``a`` に ``A`` を入れた新しい配列を返し、 ``f.(vector1,vector2)`` は、
各インデックス ``i`` に対して ``f(vector1[i],vector2[i])`` からなる新しいベクトルを返します。
（ベクトルの長さが異なる場合はエラーを出力します。）

..
 Moreover, *nested* ``f.(args...)`` calls are *fused* into a single ``broadcast``
 loop.  For example, ``sin.(cos.(X))`` is equivalent to ``broadcast(x -> sin(cos(x)), X)``,
 similar to ``[sin(cos(x)) for x in X]``: there is only a single loop over ``X``,
 and a single array is allocated for the result.   [In contrast, ``sin(cos(X))``
 in a typical "vectorized" language would first allocate one temporary array for ``tmp=cos(X)``,
 and then compute ``sin(tmp)`` in a separate loop, allocating a second array.]
 This loop fusion is not a compiler optimization that may or may not occur, it
 is a *syntactic guarantee* whenever nested ``f.(args...)`` calls are encountered.  Technically,
 the fusion stops as soon as a "non-dot" function is encountered; for example,
 in ``sin.(sort(cos.(X)))`` the ``sin`` and ``cos`` loops cannot be merged
 because of the intervening ``sort`` function.

さらに、ネストされた ``f.(args...)`` 呼び出しは、単一の ``broadcast`` ループフュージョンされます。
例えば ``sin.(cos.(X))`` は  ``[sin(cos(x)) for x in X]`` と同様に ``broadcast(x -> sin(cos(x)), X)`` と同一です。
``X`` 上に1つのループがあり、結果のために1つの配列が割り当てられます。一方、典型的な「ベクトル化」言語の ``sin(cos(X))`` は、
最初に ``tmp=cos(X)`` に対して1つの一時的な配列を割り当て、別のループで ``sin(tmp)`` を計算し、2番目の配列を割り当てます。
このループフュージョンはコンパイラの最適化ではなく、最適化が発生しない場合もあります。これはネストされた ``f.(args...)`` の
呼び出しが発生するたびに文法的に保証されます。技術的には、このフュージョンはドットが無い関数にあたるとすぐに止まります。
例えば、 ``sin.(sort(cos.(X)))`` では、介在する ``sort`` 関数のために、 ``sin`` と ``cos`` のループはマージされません。

..
 Finally, the maximum efficiency is typically achieved when the output
 array of a vectorized operation is *pre-allocated*, so that repeated
 calls do not allocate new arrays over and over again for the results
 (:ref:`man-preallocation`:).   A convenient syntax for this is
 ``X .= ...``, which is equivalent to ``broadcast!(identity, X, ...)``
 except that, as above, the ``broadcast!`` loop is fused with any nested
 "dot" calls.  For example, ``X .= sin.(Y)`` is equivalent to
 ``broadcast!(sin, X, Y)``, overwriting ``X`` with ``sin.(Y)`` in-place.
 If the left-hand side is an array-indexing expression, e.g.
 ``X[2:end] .= sin.(Y)``, then it translates to ``broadcast!`` on a ``view``,
 e.g. ``broadcast!(sin, view(X, 2:endof(X)), Y)``, so that the left-hand
 side is updated in-place.

最後に、最大の効率化は、ベクトル化された処理の出力配列を事前に割り当て、繰り返し呼び出された際に
毎回新しい配列を結果に割り当てないようにすることで通常達成されます。（ :ref:`man-出力の事前割り当て`: を参照）
便利な構文は ``X .= ...`` です。これは、上記の通り ``broadcast!`` ループはネストされたドット付きの呼び出しと
フュージョンすることを除き、 ``broadcast!(identity, X, ...)`` と同一です。例えば、 ``X .= sin.(Y)`` は
``broadcast!(sin, X, Y)`` と同一であり、 ``X`` を ``sin.(Y)`` で上書きします。左辺が ``X[2:end] .= sin.(Y)`` のような
配列インデックス式の場合、式は ``broadcast!(sin, view(X, 2:endof(X)), Y)`` のように ``view`` 上に
``broadcast!`` に変換され、これにより左辺がその場で更新されます。

..
 (In future versions of Julia, operators like ``.*`` will also be handled with
 the same mechanism: they will be equivalent to ``broadcast`` calls and
 will be fused with other nested "dot" calls.  ``X .+= Y`` is equivalent
 to ``X .= X .+ Y`` and will eventually result in a fused in-place assignment.
 Similarly for ``.*=`` etcetera.)

（Juliaの将来のバージョンでは、 ``.*`` のような演算子も同じメカニズムで処理されます。これは ``broadcast`` の呼び出しと同等で、
他のネストされたドット付きの呼び出しとフュージョンします。 ``X .+= Y`` は ``X .= X .+ Y`` と同等で、フージョンされた割り当てが行われます。
これは ``.*=`` などでも同様です。）

..
 Further Reading
 ---------------

参考資料
---------------

..
 We should mention here that this is far from a complete picture of
 defining functions. Julia has a sophisticated type system and allows
 multiple dispatch on argument types. None of the examples given here
 provide any type annotations on their arguments, meaning that they are
 applicable to all types of arguments. The type system is described in
 :ref:`man-types` and defining a function in terms of methods chosen
 by multiple dispatch on run-time argument types is described in
 :ref:`man-methods`.

これは関数の定義の完全な説明とは言えません。Juliaは洗練された型システムを持ち、引数型の複数の展開が可能です。
ここで示した例は、型の注釈を提供していません。注釈はすべての型の引数に適用できるます。
型システムについては :ref:`man-型` セクションで説明されており、実行時引数型の複数の展開によって選択されたメソッドに関する関数を
定義する方法については :ref:`man-メソッド` セクションで説明されています。
