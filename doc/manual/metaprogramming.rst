.. _man-metaprogramming:

.. currentmodule:: Base

.. 
 *****************
  Metaprogramming
 *****************

*****************
 メタプログラミング
*****************

.. 
 The strongest legacy of Lisp in the Julia language is its metaprogramming
 support. Like Lisp, Julia represents its own code as a data structure of
 the language itself.
 Since code is represented by objects that can be created and manipulated
 from within the language, it is possible for a program to transform and
 generate its own code. This allows sophisticated code generation
 without extra build steps, and also allows true Lisp-style macros operating at
 the level of `abstract syntax trees <https://en.wikipedia.org/wiki/Abstract_syntax_tree>`_.
 In contrast, preprocessor "macro" systems, like that of C and C++, perform
 textual manipulation and substitution before any actual parsing or
 interpretation occurs. Because all data types and code in Julia
 are represented by Julia data structures, powerful
 `reflection <https://en.wikipedia.org/wiki/Reflection_%28computer_programming%29>`_
 capabilities are available to explore the internals of a program and its types
 just like any other data.

Juliaの言語における最も強力なLispの遺産は、メタプログラミングのサポートです。Lisp同様、Juliaは自身のコードを
言語のデータストラクチャとして表します。コードは言語内で生成されたり変更されたりするオブジェクトにより表されるため、
プログラムはコードを変えたり生成したりすることが可能になります。これにより、洗練されたコードを追加の
ビルド工程無しに生成することが可能になり、また `抽象構文ツリー <https://en.wikipedia.org/wiki/Abstract_syntax_tree>`_ の
レベルにおけるLispスタイルのマクロ操作が可能になります。
一方で、CやC++のようなプレプロセッサマクロシステムは、実際の解析や解釈が始まる前に、テキストの処理や交換を行います。
Juliaの全てのデータ型とコードはJuliaデータストラクチャにより表されているため、
強力な `反映機能 <https://en.wikipedia.org/wiki/Reflection_%28computer_programming%29>`_ は他のデータ同様に
プログラムや型の内側を検索することができます。

.. 
 Program representation
 ----------------------

プログラムの表現
----------------------

.. 
 Every Julia program starts life as a string:

全てのJuliaのプログラムは文字列として生まれます。

.. doctest::

    julia> prog = "1 + 1"
    "1 + 1"

.. 
 **What happens next?**

**次にどうなるのか？**

.. 
 The next step is to `parse <https://en.wikipedia.org/wiki/Parsing#Computer_languages>`_
 each string into an object called an expression, represented by the Julia type
 :obj:`Expr`:

次に各文字列を式と呼ばれるオブジェクトに `解析 <https://en.wikipedia.org/wiki/Parsing#Computer_languages>`_ していきます。
式はJuliaの型では :obj:`Expr` と表現されます。:

.. doctest::

    julia> ex1 = parse(prog)
    :(1 + 1)

    julia> typeof(ex1)
    Expr

.. 
 :obj:`Expr` objects contain three parts:

:obj:`Expr` オブジェクトは3つのパーツを持ちます。:

.. 
 - a ``Symbol`` identifying the kind of expression. A symbol is an
   `interned string <https://en.wikipedia.org/wiki/String_interning>`_
   identifier (more discussion below).

- 式の種類を特定する ``Symbol`` シンボルは `intern文字列 <https://en.wikipedia.org/wiki/String_interning>`_ 識別子です（詳細は下記）。

.. doctest::

    julia> ex1.head
    :call
.. 
 - the expression arguments, which may be symbols, other expressions, or literal values:

- 式の引数。引数は記号、他の式またはリテラル値:

.. doctest::

    julia> ex1.args
    3-element Array{Any,1}:
      :+
     1
     1

.. 
- finally, the expression result type, which may be annotated by the user or inferred
  by the compiler (and may be ignored completely for the purposes of this chapter):

- 式の結果の型。型はユーザにより注釈をつけるか、コンパイラにより推測されます（これはこのチャプタでは扱いません）:

.. doctest::

    julia> ex1.typ
    Any

.. 
 Expressions may also be constructed directly in
 `prefix notation <https://en.wikipedia.org/wiki/Polish_notation>`_:

式は `プレフィックス表記法 <https://en.wikipedia.org/wiki/Polish_notation>`_ により、
直接構築することもできます。:

.. doctest::

    julia> ex2 = Expr(:call, :+, 1, 1)
    :(1 + 1)

.. 
 The two expressions constructed above -- by parsing and by direct
 construction -- are equivalent:

上記の2つの式（解析によるものと直接的に構築されたもの）は同等です。:

.. doctest::

    julia> ex1 == ex2
    true

.. 
**The key point here is that Julia code is internally represented
as a data structure that is accessible from the language itself.**

**ここでの重要なポイントは、Juliaコードは言語そのものからアクセスすることができるデータストラクチャとして、
内部的に表現される点です。**

.. 
 The :func:`dump` function provides indented and annotated display of :obj:`Expr`
 objects:

:func:`dump` 関数は、インデントされ注釈付けされた :obj:`Expr` オブジェクトの表示を行います。:

.. doctest::

    julia> dump(ex2)
    Expr
      head: Symbol call
      args: Array{Any}((3,))
        1: Symbol +
        2: Int64 1
        3: Int64 1
      typ: Any

.. 
 :obj:`Expr` objects may also be nested:

:obj:`Expr` オブジェクトはネスト化することもできます。:

.. doctest::

    julia> ex3 = parse("(4 + 4) / 2")
    :((4 + 4) / 2)

.. 
 Another way to view expressions is with Meta.show_sexpr, which displays the
 `S-expression <https://en.wikipedia.org/wiki/S-expression>`_ form of a given
 :obj:`Expr`, which may look very familiar to users of Lisp. Here's an example
 illustrating the display on a nested :obj:`Expr`::

式を見る他の方法には、与えられた :obj:`Expr` の `S-expression <https://en.wikipedia.org/wiki/S-expression>`_ フォームを表示す
Meta.show_sexprがあり、これはLispユーザには見覚えがあるかもしれません。以下はネスト化された :obj:`Expr` の表示の例です。::

    julia> Meta.show_sexpr(ex3)
    (:call, :/, (:call, :+, 4, 4), 2)

.. 
 Symbols
 ~~~~~~~

記号
~~~~~~~

.. 
 The ``:`` character has two syntactic purposes in Julia. The first form creates a
 :obj:`Symbol`, an `interned string <https://en.wikipedia.org/wiki/String_interning>`_
 used as one building-block of expressions:

Juliaにおける「:」は2つの構文上の目的を持ちます。1つ目の目的は、
式の1つの構築ブロックとして使われる `intern文字列 <https://en.wikipedia.org/wiki/String_interning>`_ である
:obj:`Symbol` を生成することです。:

.. doctest::

    julia> :foo
    :foo

    julia> typeof(ans)
    Symbol

.. 
 The :obj:`Symbol` constructor takes any number of arguments and creates a
 new symbol by concatenating their string representations together:

:obj:`Symbol` コンストラクタは任意の数の引数を取り、文字列表現を連結することで新しい記号を生成します。:

.. doctest::

    julia> :foo == Symbol("foo")
    true

    julia> Symbol("func",10)
    :func10

    julia> Symbol(:var,'_',"sym")
    :var_sym

.. 
 In the context of an expression, symbols are used to indicate access to
 variables; when an expression is evaluated, a symbol is replaced with
 the value bound to that symbol in the appropriate :ref:`scope
 <man-variables-and-scoping>`.

式の文脈の中で、記号は変数へのアクセスを示すために使われます。式が評価される際に、
記号は適切な :ref:`スコープ <man-変数とスコープ>` 内の記号に紐づく値に置き換えられます。

.. 
 Sometimes extra parentheses around the argument to ``:`` are needed to avoid
 ambiguity in parsing.:

解析時のあいまいさを回避するため、 ``:`` の引数の周りに追加の括弧が必要な場合があります。:

.. doctest::

    julia> :(:)
    :(:)

    julia> :(::)
    :(::)

.. 
 Expressions and evaluation
 --------------------------

式と評価
--------------------------

.. 
 Quoting
 ~~~~~~~

引用
~~~~~~~

.. 
 The second syntactic purpose of the ``:`` character is to create expression
 objects without using the explicit :obj:`Expr` constructor. This is referred
 to as *quoting*. The ``:`` character, followed by paired parentheses around
 a single statement of Julia code, produces an :obj:`Expr` object based on the
 enclosed code. Here is example of the short form used to quote an arithmetic
 expression:

``:`` の2つ目の目的は、明示的に :obj:`Expr` コンストラクタを使用せずに式オブジェクトを生成することです。
これは引用と呼ばれます。Juliaコードのステートメントの周りに1対の括弧が続く ``:`` は、
囲われたコードに基づく :obj:`Expr` オブジェクトを生成します。以下は引用を使用した短い算術式の例です。:

.. doctest::

    julia> ex = :(a+b*c+1)
    :(a + b * c + 1)

    julia> typeof(ex)
    Expr

.. 
 (to view the structure of this expression, try ``ex.head`` and ``ex.args``,
 or use :func:`dump` as above)

（この式の構造を見るには、 ``ex.head`` 、``ex.args`` または :func:`dump` ）を使用してください。）

.. 
 Note that equivalent expressions may be constructed using :func:`parse` or
 the direct :obj:`Expr` form:

上記と同等の式は、 :func:`parse` または直接 :obj:`Expr` フォームを使うことで構築できる点に注意してください。:

.. doctest::

   julia>      :(a + b*c + 1)  ==
          parse("a + b*c + 1") ==
          Expr(:call, :+, :a, Expr(:call, :*, :b, :c), 1)
   true

.. 
 Expressions provided by the parser generally only have symbols, other
 expressions, and literal values as their args, whereas expressions
 constructed by Julia code can have arbitrary run-time values
 without literal forms as args. In this specific example, ``+`` and ``a``
 are symbols, ``*(b,c)`` is a subexpression, and ``1`` is a literal
 64-bit signed integer.

構文解析によって発生した式は、通常記号、他の式およびリテラル値を引数として持ちますが、
一方でJuliaコードにより構築された式は、リテラル形式を引数として持つことなく、
任意の実行値を持つことができます。上記の例では、 ``+`` および ``a`` は記号、 ``*(b,c)`` は部分式、
そして ``1`` は64ビットリテラル整数として記述されています。

.. 
 There is a second syntactic form of quoting for multiple expressions:
 blocks of code enclosed in ``quote ... end``. Note that this form
 introduces :obj:`QuoteNode` elements to the expression tree, which
 must be considered when directly manipulating an expression tree
 generated from ``quote`` blocks. For other purposes, ``:( ... )``
 and ``quote .. end`` blocks are treated identically.

複数の式を引用する2つ目の構文形式として、 ``quote ... end`` に囲われたコードブロックがあります。
この形式は式ツリーに :obj:`QuoteNode` 要素を渡しますが、 ``quote`` ブロックにより生成された式ツリーを
直接的に操作する場合は考慮しなければならない点に注意してください。他の理由により、 ``:( ... )`` および
``quote .. end`` ブロックは同様に処理されます。

.. doctest::

    julia> ex = quote
               x = 1
               y = 2
               x + y
           end
    quote  # none, line 2:
        x = 1 # none, line 3:
        y = 2 # none, line 4:
        x + y
    end

    julia> typeof(ex)
    Expr

.. 
 Interpolation
 ~~~~~~~~~~~~~

加筆
~~~~~~~~~~~~~

.. 
 Direct construction of :obj:`Expr` objects with value arguments is
 powerful, but :obj:`Expr` constructors can be tedious compared to "normal"
 Julia syntax. As an alternative, Julia allows "splicing" or interpolation
 of literals or expressions into quoted expressions. Interpolation is
 indicated by the ``$`` prefix.

値引数とともに :obj:`Expr` オブジェクトを直接構築することは強力ですが、
通常のJulia構文と比較すると :obj:`Expr` コンストラクタは少し退屈です。
代替として、リテラルや式を引用式に継ぎ合せたり加筆することができます。
加筆は ``$`` プレフィックスで示されます。

.. 
 In this example, the literal value of ``a`` is interpolated:

この例では、リテラル値 ``a`` は加筆されています。:

.. doctest::

    julia> a = 1;

    julia> ex = :($a + b)
    :(1 + b)

.. 
 Interpolating into an unquoted expression is not supported and will
 cause a compile-time error:

引用されていない式への加筆はサポートされておらず、コンパイル時にエラーとなります。:

.. doctest::

    julia> $a + b
    ERROR: unsupported or misplaced expression $
     ...

.. 
 In this example, the tuple ``(1,2,3)`` is interpolated as an
 expression into a conditional test:

この例では、タプル ``(1,2,3)`` は条件に式として加筆されています。:

.. doctest::

    julia> ex = :(a in $:((1,2,3)) )
    :(a in (1,2,3))

.. 
 Interpolating symbols into a nested expression requires enclosing each
 symbol in an enclosing quote block::

記号のネスト化された式への加筆は、囲われた引用ブロック内に各記号を囲む必要があります。::

    julia> :( :a in $( :(:a + :b) ) )
                       ^^^^^^^^^^
                       quoted inner expression

.. 
 The use of ``$`` for expression interpolation is intentionally reminiscent
 of :ref:`string interpolation <man-string-interpolation>` and :ref:`command
 interpolation <man-command-interpolation>`. Expression interpolation allows
 convenient, readable programmatic construction of complex Julia expressions.

式の加筆時の ``$`` の使用は、意図的に :ref:`文字列の加筆 <man-文字列の加筆>` および
:ref:`コマンドの加筆 <man-コマンドの加筆>` に関連付けられています。
式の加筆は、複雑なJuliaの式の利便性と可読性の高いプログラム構築を可能にします。

.. 
  :func:`eval` and effects
  ~~~~~~~~~~~~~~~~~~~~~~~~

:func:`eval` と効果
~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Given an expression object, one can cause Julia to evaluate (execute) it
  at global scope using :func:`eval`:

与えられた式について、 :func:`eval`: を使ってグローバルスコープでJuliaに評価（実行）させることができます。:

.. doctest::

    julia> :(1 + 2)
    :(1 + 2)

    julia> eval(ans)
    3

    julia> ex = :(a + b)
    :(a + b)

    julia> eval(ex)
    ERROR: UndefVarError: b not defined
     ...

    julia> a = 1; b = 2;

    julia> eval(ex)
    3

.. 
  Every :ref:`module <man-modules>` has its own :func:`eval` function that
  evaluates expressions in its global scope.
  Expressions passed to :func:`eval` are not limited to returning values
  — they can also have side-effects that alter the state of the enclosing
  module's environment:

全ての :ref:`モジュール <man-モジュール>` は、それぞれグローバルスコープ内の式を評価する :func:`eval` 関数を持ちます。
:func:`eval` に渡された式は値を返すだけに限られず、囲われているモジュールの環境の状態を変えてしまう副作用がある場合があります。:

.. doctest::

    julia> ex = :(x = 1)
    :(x = 1)

    julia> x
    ERROR: UndefVarError: x not defined
     ...

    julia> eval(ex)
    1

    julia> x
    1

.. 
  Here, the evaluation of an expression object causes a value to be
  assigned to the global variable ``x``.

これは、式オブジェクトの評価が値をグローバル変数 ``x`` に割り当てられるようにする例です。

.. 
  Since expressions are just :obj:`Expr` objects which can be constructed
  programmatically and then evaluated, it is possible to dynamically generate
  arbitrary code which can then be run using :func:`eval`. Here is a simple example:

式は、プログラム的に構築されから評価される :obj:`Expr` オブジェクトであるため、 :func:`eval` を使用して
実行することができる任意のコードを動的に生成することが可能です。以下は簡単な例です。:

.. doctest::

    julia> a = 1;

    julia> ex = Expr(:call, :+, a, :b)
    :(1 + b)

    julia> a = 0; b = 2;

    julia> eval(ex)
    3

.. 
  The value of ``a`` is used to construct the expression ``ex`` which
  applies the ``+`` function to the value 1 and the variable ``b``. Note
  the important distinction between the way ``a`` and ``b`` are used:

``a`` の値は、「+」関数を値「1」および変数 ``b`` に適用する式 ``ex`` を構築するために使用されています。
``a`` と ``b`` の使われ方の重要な以下の違いに注意してください。:

.. 
  -  The value of the *variable* ``a`` at expression construction time is
     used as an immediate value in the expression. Thus, the value of
     ``a`` when the expression is evaluated no longer matters: the value
     in the expression is already ``1``, independent of whatever the value
     of ``a`` might be.
  -  On the other hand, the *symbol* ``:b`` is used in the expression
     construction, so the value of the variable ``b`` at that time is
     irrelevant — ``:b`` is just a symbol and the variable ``b`` need not
     even be defined. At expression evaluation time, however, the value of
     the symbol ``:b`` is resolved by looking up the value of the variable
     ``b``.
   
-  式構築時における変数 ``a`` の値は、その式の即値として使用されています。したがって、
   式が評価されている際の ``a`` の値は問題とはなりません。式の値は、 ``a`` の値が何であろうと、既に ``1`` です。
-  一方で、記号 ``:b`` は式構築に使われているため、そのタイミングにおける変数 ``b`` の値は問題にはなりません。
   ``:b`` はただの記号であり、変数 ``b`` は定義される必要がありません。しかし、式が評価される際には、
   記号 ``:b`` の値は変数 ``b`` の値を検索することで解決されます。

.. 
  Functions on :obj:`Expr`\ essions
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:obj:`Expr`\ 上の関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  As hinted above, one extremely useful feature of Julia is the capability to
  generate and manipulate Julia code within Julia itself. We have already
  seen one example of a function returning :obj:`Expr` objects: the :func:`parse`
  function, which takes a string of Julia code and returns the corresponding
  :obj:`Expr`. A function can also take one or more :obj:`Expr` objects as
  arguments, and return another :obj:`Expr`. Here is a simple, motivating example::

上記にあるとおり、Juliaの最も便利な機能は、Julia内でJuliaコードを生成したり操作できる点です。
既にこれまでに文字列のJuliaコードを取り、対応する :obj:`Expr` オブジェクトを返す :func:`parse` 関数を見てきました。
関数は1つまたは複数の :obj:`Expr` オブジェクトを引数として取り、他の :obj:`Expr` オブジェクトを返すことができます。以下は簡単な例です。::

   julia> function math_expr(op, op1, op2)
            expr = Expr(:call, op, op1, op2)
            return expr
          end

    julia>  ex = math_expr(:+, 1, Expr(:call, :*, 4, 5))
    :(1 + 4*5)

    julia> eval(ex)
    21

.. 
  As another example, here is a function that doubles any numeric argument,
  but leaves expressions alone::

他の例と同様に、ここでは関数は2つの数値引数を取り、式を1つにする例です。::

    julia> function make_expr2(op, opr1, opr2)
             opr1f, opr2f = map(x -> isa(x, Number) ? 2*x : x, (opr1, opr2))
             retexpr = Expr(:call, op, opr1f, opr2f)

             return retexpr
       end
    make_expr2 (generic function with 1 method)

    julia> make_expr2(:+, 1, 2)
    :(2 + 4)

    julia> ex = make_expr2(:+, 1, Expr(:call, :*, 5, 8))
    :(2 + 5 * 8)

    julia> eval(ex)
    42

.. _man-macros:

.. 
  Macros
  ------

マクロ
------

.. 
  Macros provide a method to include generated code in the final body
  of a program. A macro maps a tuple of arguments to a returned
  *expression*, and the resulting expression is compiled directly rather
  than requiring a runtime :func:`eval` call. Macro arguments may include
  expressions, literal values, and symbols.

マクロはプログラムのボディ部の最後に生成されたコードを含めるメソッドを提供します。
返された式の引数のマクロマップタプルと結果の式は、実行時に :func:`eval` を呼び出すことなく、
直接コンパイルされます。マクロの引数は、式、リテラル、値、そして記号を含めることができます。

.. 
  Basics
  ~~~~~~

基本
~~~~~~

.. 
  Here is an extraordinarily simple macro:

以下は非常にシンプルなマクロの例です。:

.. testcode::

    macro sayhello()
        return :( println("Hello, world!") )
    end

.. testoutput::
    :hide:

    @sayhello (macro with 1 method)

.. 
  Macros have a dedicated character in Julia's syntax: the ``@`` (at-sign),
  followed by the unique name declared in a ``macro NAME ... end`` block.
  In this example, the compiler will replace all instances of ``@sayhello``
  with::

マクロは専用の文字列、 ``macro NAME ... end`` ブロック内に宣言されたユニークな名前が後に続く ``@`` をJuliaの構文に持ちます。
この例では、コンパイラは ``@sayhello`` の全てのインスタンスを以下と置き換えます。::

    :( println("Hello, world!") )

.. 
  When ``@sayhello`` is given at the REPL, the expression executes
  immediately, thus we only see the evaluation result::

REPLに ``@sayhello`` が与えられると、式は即座に実行され、これにより評価結果のみ出力されます。::

    julia> @sayhello()
    "Hello, world!"

.. 
  Now, consider a slightly more complex macro::

それでは少し複雑なマクロを見てみましょう。:

    julia> macro sayhello(name)
               return :( println("Hello, ", $name) )
           end

.. 
  This macro takes one argument: ``name``. When ``@sayhello`` is
  encountered, the quoted expression is *expanded* to interpolate
  the value of the argument into the final expression::

このマクロは1つの引数 ``name`` を取ります。 ``@sayhello`` に当たると、引用式は引数の値を最終的な式に加筆するために拡張されます。::

    julia> @sayhello("human")
    Hello, human

.. 
  We can view the quoted return expression using the function :func:`macroexpand`
  (**important note:** this is an extremely useful tool for debugging macros)::

:func:`macroexpand` を使用することで、引用された返される式を確認することができます（**重要:**これはマクロのデバッグの際に非常に有効です）。::

    julia> ex = macroexpand( :(@sayhello("human")) )
    :(println("Hello, ","human"))
                        ^^^^^^^
                        interpolated: now a literal string

    julia> typeof(ex)
    Expr

.. 
  Hold up: why macros?
  ~~~~~~~~~~~~~~~~~~~~

なぜマクロ？
~~~~~~~~~~~~~~~~~~~~

.. 
  We have already seen a function ``f(::Expr...) -> Expr`` in a previous section.
  In fact, :func:`macroexpand` is also such a function. So, why do macros
  exist?

既に関数 ``f(::Expr...) -> Expr`` は前のセクションで説明しました。
加えて、 :func:`macroexpand` も似た様な関数です。ではなぜマクロが存在するのでしょうか。

.. 
  Macros are necessary because they execute when code is parsed, therefore,
  macros allow the programmer to generate and include fragments of customized
  code *before* the full program is run. To illustrate the difference,
  consider the following example::

コードが解析された際に実行されるため、マクロは不可欠です。つまり、マクロにより、
プログラマは全体のプログラムが実行されるよりも前に、カスタマイズされたコードの一部を生成したり含めたりすることができます。
この違いを明確にするために、以下の例を参照してください。::

    julia> macro twostep(arg)
               println("I execute at parse time. The argument is: ", arg)

               return :(println("I execute at runtime. The argument is: ", $arg))
           end

    julia> ex = macroexpand( :(@twostep :(1, 2, 3)) );
    I execute at parse time. The argument is: :((1,2,3))

.. 
  The first call to :func:`println` is executed when :func:`macroexpand`
  is called. The resulting expression contains *only* the second ``println``::

最初の :func:`println` の呼び出しは、 :func:`macroexpand` が呼び出された際に実行されます。結果の式は2つ目の ``println`` のみを含みます。::

    julia> typeof(ex)
    Expr

    julia> ex
    :(println("I execute at runtime. The argument is: ",$(Expr(:copyast, :(:((1,2,3)))))))

    julia> eval(ex)
    I execute at runtime. The argument is: (1,2,3)

.. 
  Macro invocation
  ~~~~~~~~~~~~~~~~

マクロの導入
~~~~~~~~~~~~~~~~

.. 
  Macros are invoked with the following general syntax::

マクロは以下の基本的な構文で呼び出されます。::

    @name expr1 expr2 ...
    @name(expr1, expr2, ...)

.. 
  Note the distinguishing ``@`` before the macro name and the lack of
  commas between the argument expressions in the first form, and the
  lack of whitespace after ``@name`` in the second form. The two styles
  should not be mixed. For example, the following syntax is different
  from the examples above; it passes the tuple ``(expr1, expr2, ...)`` as
  one argument to the macro::

最初の形式のマクロ名の ``@`` と引数式の間にカンマが無い点と、2番目の形式の ``@name`` の後にスペースが無い違いについて注意してください。
この2つの形式を混同しないようにしてください。例えば、以下の構文は上記の例とは異なります。
これはタプル ``(expr1, expr2, ...)`` を1つの引数としてマクロに渡します。::

    @name (expr1, expr2, ...)

.. 
  It is important to emphasize that macros receive their arguments as
  expressions, literals, or symbols. One way to explore macro arguments
  is to call the :func:`show` function within the macro body::

マクロが引数を式、リテラルまたは記号として受け取ることを強調することは重要です。
マクロの引数を確認するには、マクロのボディ内に :func:`show` 関数を呼び出す方法があります。::

    julia> macro showarg(x)
       show(x)
       # ... remainder of macro, returning an expression
    end


    julia> @showarg(a)
    (:a,)

    julia> @showarg(1+1)
    :(1 + 1)

    julia> @showarg(println("Yo!"))
    :(println("Yo!"))


.. 
  Building an advanced macro
  ~~~~~~~~~~~~~~~~~~~~~~~~~~

高度なマクロの構築
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Here is a simplified definition of Julia's :obj:`@assert` macro::

以下はJuliaの :obj:`@assert` マクロの簡略化された定義です。:

    macro assert(ex)
        return :( $ex ? nothing : throw(AssertionError($(string(ex)))) )
    end

.. 
  This macro can be used like this:

このマクロは以下のように使用することができます。:

.. doctest::

    julia> @assert 1==1.0

    julia> @assert 1==0
    ERROR: AssertionError: 1 == 0
     ...

.. 
  In place of the written syntax, the macro call is expanded at parse time to
  its returned result. This is equivalent to writing::

記述された構文の代わりに、マクロの呼び出しは解析時に返される結果まで拡張されます。
これは以下のように記述することと同様です。::

    1==1.0 ? nothing : throw(AssertionError("1==1.0"))
    1==0 ? nothing : throw(AssertionError("1==0"))

.. 
  That is, in the first call, the expression ``:(1==1.0)`` is spliced into
  the test condition slot, while the value of ``string(:(1==1.0))`` is
  spliced into the assertion message slot. The entire expression, thus
  constructed, is placed into the syntax tree where the :obj:`@assert` macro
  call occurs. Then at execution time, if the test expression evaluates to
  true, then ``nothing`` is returned, whereas if the test is false, an error
  is raised indicating the asserted expression that was false. Notice that
  it would not be possible to write this as a function, since only the
  *value* of the condition is available and it would be impossible to
  display the expression that computed it in the error message.

最初の呼び出しでは、式 ``:(1==1.0)`` は条件判断スロットに継ぎ合わされ、一方で ``string(:(1==1.0))`` の値は
アサーションメッセージスロットに継ぎ合わされます。したがって、構築された式全体は、
:obj:`@assert` マクロ呼び出しが発生する構文ツリーに挿入されます。その後、実行時において、
条件判断の結果がtrueの場合は ``nothing`` が返され、一方で条件判断の結果がfalseの場合は
アサーション式が誤りであることを示すエラーが出力されます。条件式の値のみが使用可能であり、
エラーメッセージ内で処理された式を表示することは不可能であるため、
これを関数として記述することはできない点に注意してください。

.. 
  The actual definition of :obj:`@assert` in the standard library is more
  complicated. It allows the user to optionally specify their own error
  message, instead of just printing the failed expression. Just like in
  functions with a variable number of arguments, this is specified with an
  ellipses following the last argument::

標準ライブラリの :obj:`@assert` の実際の定義はより複雑です。ユーザは、失敗した式を単に出力するのではなく、
オプションでエラーメッセージを指定することができます。多くの引数を持つ変数を使う関数の様に、
これは最後の引数の後に省略記号をつけて指定することができます。::

    macro assert(ex, msgs...)
        msg_body = isempty(msgs) ? ex : msgs[1]
        msg = string(msg_body)
        return :($ex ? nothing : throw(AssertionError($msg)))
    end

.. 
  Now :obj:`@assert` has two modes of operation, depending upon the number of
  arguments it receives! If there's only one argument, the tuple of expressions
  captured by ``msgs`` will be empty and it will behave the same as the simpler
  definition above. But now if the user specifies a second argument, it is
  printed in the message body instead of the failing expression. You can inspect
  the result of a macro expansion with the aptly named :func:`macroexpand`
  function:

:obj:`@assert` は、受け取る引数の数に依存した2つの操作モードがあります。引数が1つの場合は、
``msgs`` により取得された式のタプルは空となり、上記のような簡易の定義のものと同様の動作をします。
しかし、ユーザが2つ目の引数を定義した場合は、処理に失敗した式の代わりに、メッセージの本文に出力されます。
:func:`macroexpand` を使用することで、マクロの拡張結果を調べることができます。:

.. doctest::

    julia> macroexpand(:(@assert a==b))
    :(if a == b
            nothing
        else
            (Base.throw)(Base.Main.Base.AssertionError("a == b"))
        end)

    julia> macroexpand(:(@assert a==b "a should equal b!"))
    :(if a == b
            nothing
        else
            (Base.throw)(Base.Main.Base.AssertionError("a should equal b!"))
        end)

.. 
  There is yet another case that the actual :obj:`@assert` macro handles: what
  if, in addition to printing "a should equal b," we wanted to print their
  values? One might naively try to use string interpolation in the custom
  message, e.g., ``@assert a==b "a ($a) should equal b ($b)!"``, but this
  won't work as expected with the above macro. Can you see why? Recall
  from :ref:`string interpolation <man-string-interpolation>` that an
  interpolated string is rewritten to a call to :func:`string`.
  Compare:

しかし :obj:`@assert` が処理する別のケースが存在します。「a should equal b」と出力することに加えて、
この値を出力したいとなった場合、どうすればよいでしょうか。単純に ``@assert a==b "a ($a) should equal b ($b)!"`` のように
カスタムされたメッセージに文字列を継ぎ合せすることでできると考えるかもしれませんが、上記のマクロでは期待通りにはいきません。
なぜだかわかるでしょうか。加筆された文字列は :func:`string` を呼び出すために
再度記述されるという :ref:`文字列の加筆 <man-文字列の加筆>` の内容を思い出してください。
以下を比べてみてください。:

.. doctest::

    julia> typeof(:("a should equal b"))
    String

    julia> typeof(:("a ($a) should equal b ($b)!"))
    Expr

    julia> dump(:("a ($a) should equal b ($b)!"))
    Expr
      head: Symbol string
      args: Array{Any}((5,))
        1: String "a ("
        2: Symbol a
        3: String ") should equal b ("
        4: Symbol b
        5: String ")!"
      typ: Any

.. 
  So now instead of getting a plain string in ``msg_body``, the macro is
  receiving a full expression that will need to be evaluated in order to
  display as expected. This can be spliced directly into the returned expression
  as an argument to the :func:`string` call; see `error.jl
  <https://github.com/JuliaLang/julia/blob/master/base/error.jl>`_ for
  the complete implementation.

``msg_body`` 内にプレーンなテキストを得る代わりに、マクロは、期待通りに出力するために
評価される必要がある式全体を受け取っています。 :func:`string` の呼び出しの引数のように、
これは返された式に直接継ぎ合せることができます。
詳細な実装は `error.jl <https://github.com/JuliaLang/julia/blob/master/base/error.jl>`_ を
参照してください。

.. 
  The :obj:`@assert` macro makes great use of splicing into quoted expressions
  to simplify the manipulation of expressions inside the macro body.

:obj:`@assert` マクロは、マクロ本体の式の操作を単純化するために、引用式への継ぎ合せを非常にうまく利用します。

.. 
  Hygiene
  ~~~~~~~

衛生規約
~~~~~~~

.. 
  An issue that arises in more complex macros is that of
  `hygiene <https://en.wikipedia.org/wiki/Hygienic_macro>`_. In short, macros must
  ensure that the variables they introduce in their returned expressions do not
  accidentally clash with existing variables in the surrounding code they expand
  into. Conversely, the expressions that are passed into a macro as arguments are
  often *expected* to evaluate in the context of the surrounding code,
  interacting with and modifying the existing variables. Another concern arises
  from the fact that a macro may be called in a different module from where it
  was defined. In this case we need to ensure that all global variables are
  resolved to the correct module. Julia already has a major advantage over
  languages with textual macro expansion (like C) in that it only needs to
  consider the returned expression. All the other variables (such as ``msg`` in
  :obj:`@assert` above) follow the :ref:`normal scoping block behavior
  <man-variables-and-scoping>`.

より複雑なマクロにおいて発生する問題は、衛生規約（ `hygiene <https://en.wikipedia.org/wiki/Hygienic_macro>`_ ）です。
要約すると、マクロは、返された式の使用する変数が拡張しようとしている周りのコードの変数を誤って競合しないように、
保証しなければなりません。反対に、引数としてマクロに渡される式は、
既存の変数と相互作用および既存の変数を変更しながら、周囲のコードの文脈の中で評価されることが期待されています。
その他の懸念として、マクロは定義されたモジュールとはことなるモジュールで呼び出される点があります。
この場合、全てのグローバル変数は正しいモジュールに対して解決されていることを保証する必要があります。
Juliaは、返された式のみを考慮すればよいという点において、他のテキストマクロ拡張の言語（Cなど）に対して
大きな優位性を持ちます。他の全ての変数（上記 :obj:`@assert` の ``msg`` など）は :ref:`通常スコープの動作<man-変数とスコープ` に
従います。

.. 
  To demonstrate these issues,
  let us consider writing a ``@time`` macro that takes an expression as
  its argument, records the time, evaluates the expression, records the
  time again, prints the difference between the before and after times,
  and then has the value of the expression as its final value.
  The macro might look like this::

これらの問題を説明するため、式を引数として取り、時間を記録し、式を評価し、時間を再度記録し、
前後の時間の差分を出力し、その式の値を最終値として持つ ``@time`` マクロについて考えてみましょう。このマクロはこのようになります。::

    macro time(ex)
      return quote
        local t0 = time()
        local val = $ex
        local t1 = time()
        println("elapsed time: ", t1-t0, " seconds")
        val
      end
    end

.. 
  Here, we want ``t0``, ``t1``, and ``val`` to be private temporary variables,
  and we want ``time`` to refer to the :func:`time` function in the standard library,
  not to any ``time`` variable the user might have (the same applies to
  ``println``). Imagine the problems that could occur if the user expression
  ``ex`` also contained assignments to a variable called ``t0``, or defined
  its own ``time`` variable. We might get errors, or mysteriously incorrect
  behavior.

ここでは、 ``t0`` 、 ``t1`` および ``val`` は一時的なプライベート変数とする必要があり、
また ``time`` は、ユーザが定義した ``time`` 変数ではなく、標準ライブラリの　``time``　関数を指したいとします
（これは　 ``println`` も同様です）。ユーザ定義の式 ``ex`` は ``t0`` という変数の割り当ても含んでいる、
または ``time`` という変数を定義している場合の問題を想像してください。処理の結果エラーが出力されるかもしれませんし、
よくわからない間違った処理をするかもしれません。

.. 
  Julia's macro expander solves these problems in the following way. First,
  variables within a macro result are classified as either local or global.
  A variable is considered local if it is assigned to (and not declared
  global), declared local, or used as a function argument name. Otherwise,
  it is considered global. Local variables are then renamed to be unique
  (using the :func:`gensym` function, which generates new symbols), and global
  variables are resolved within the macro definition environment. Therefore
  both of the above concerns are handled; the macro's locals will not conflict
  with any user variables, and ``time`` and ``println`` will refer to the
  standard library definitions.

Juliaのマクロの拡張は、これらの問題を以下のように解決します。まず最初に、マクロの結果内の変数は
コーカルまたはグローバルのどちらかとして機密扱いになります。変数は、もしそれがローカルに割り当てられている
（およびグローバルとして宣言されていない）、ローカルとして宣言されている、または関数の引数名として使用されている場合は、
ローカルとしてみなされます。そうでなければ、変数はグローバルとみなされます。
ローカル変数はユニークになるように（新しい記号を生成する :func:`gensym` 関数を使用して）再度名前が付けられ、
グローバル変数はマクロ定義の環境内で解決されます。したがって、上記の2つの懸念は解消されます。
マクロのローカルはユーザ定義と競合することはなく、 ``time`` と ``println`` は標準ライブラリの定義を参照します。

.. 
  One problem remains however. Consider the following use of this macro::

しかし、1つの問題が残ります。以下のマクロの使い方を考えてみてください。::

    module MyModule
    import Base.@time

    time() = ... # compute something

    @time time()
    end

.. 
  Here the user expression ``ex`` is a call to ``time``, but not the same
  ``time`` function that the macro uses. It clearly refers to ``MyModule.time``.
  Therefore we must arrange for the code in ``ex`` to be resolved in the
  macro call environment. This is done by "escaping" the expression with
  :func:`esc`::

ここではユーザ定義式 ``ex`` は ``time`` の呼び出しですが、マクロが使用している ``time`` 関数とは異なるものです。
これは明らかに ``MyModule.time`` を参照しています。したがって、 ``ex`` がマクロの呼び出し環境で解決されるように、
手を加えなければなりません。これは、 :func:`esc` を使用して式をエスケープすることでできます。::

    macro time(ex)
        ...
        local val = $(esc(ex))
        ...
    end

.. 
  An expression wrapped in this manner is left alone by the macro expander
  and simply pasted into the output verbatim. Therefore it will be
  resolved in the macro call environment.

この方法でラッピングされた式はマクロ拡張からは干渉されず、単に言葉通りに出力されます。
したがって、これはマクロの呼び出し環境で解決されます。

.. 
  This escaping mechanism can be used to "violate" hygiene when necessary,
  in order to introduce or manipulate user variables. For example, the
  following macro sets ``x`` to zero in the call environment::

このエスケープの仕組みは、ユーザ定義変数を渡したり操作するために、必要な場合は衛生規約に
違反するために使うことができます。例えば、以下のマクロは呼び出し環境では ``x`` をゼロと設定しています。::

    macro zerox()
      return esc(:(x = 0))
    end

    function foo()
      x = 1
      @zerox
      x  # is zero
    end

.. 
  This kind of manipulation of variables should be used judiciously, but
  is occasionally quite handy.

この種の変数の操作は賢明に使用する必要がありますが、場合によっては非常に便利です。

.. 
  Code Generation
  ---------------

コード生成
---------------

.. 
  When a significant amount of repetitive boilerplate code is required, it
  is common to generate it programmatically to avoid redundancy. In most
  languages, this requires an extra build step, and a separate program to
  generate the repetitive code. In Julia, expression interpolation and
  :func:`eval` allow such code generation to take place in the normal course of
  program execution. For example, the following code defines a series of
  operators on three arguments in terms of their 2-argument forms::

多数の繰り返しの定型的なコードが必要な場合は、冗長性を避けるためにプログラムで生成することができます。
多くの言語では、これには追加のビルド工程が必要となり、また独立したプログラムで繰り返しのコードを生成する必要があります。
Juliaでは、式の加筆および :func:`eval` により、そのようなコードの生成を通常のプログラム実行時に行うことができます。
例として、以下のコードは2引数形式の点から、3つの引数の処理を定義したものです。::

    for op = (:+, :*, :&, :|, :$)
      eval(quote
        ($op)(a,b,c) = ($op)(($op)(a,b),c)
      end)
    end

.. 
  In this manner, Julia acts as its own `preprocessor
  <https://en.wikipedia.org/wiki/Preprocessor>`_, and allows code
  generation from inside the language. The above code could be written
  slightly more tersely using the ``:`` prefix quoting form::

このやり方では、Juliaは自身の `プレプロセッサ <https://en.wikipedia.org/wiki/Preprocessor>`_ として動作し、
言語内でのコード生成を可能にします。上記のコードは ``:`` プレフィックス引用形式を使用することで、
もう少し簡潔に記述することができます。::

    for op = (:+, :*, :&, :|, :$)
      eval(:(($op)(a,b,c) = ($op)(($op)(a,b),c)))
    end

.. 
  This sort of in-language code generation, however, using the
  ``eval(quote(...))`` pattern, is common enough that Julia comes with a
  macro to abbreviate this pattern::

しかし、この種の ``eval(quote(...))`` を使用した言語内コード生成は、
Juliaのマクロを使うことで省略することが可能です。::

    for op = (:+, :*, :&, :|, :$)
      @eval ($op)(a,b,c) = ($op)(($op)(a,b),c)
    end

.. 
  The :obj:`@eval` macro rewrites this call to be precisely equivalent to the
  above longer versions. For longer blocks of generated code, the
  expression argument given to :obj:`@eval` can be a block::

:obj:`@eval` は、上記の長いコードを正確に短く書き直しています。より長いコード生成のブロックでは、
:obj:`@eval` への式引数をブロックとすることができます。::

    @eval begin
      # multiple lines
    end

.. _man-non-standard-string-literals2:

.. 
  Non-Standard String Literals
  ----------------------------

非標準文字列リテラル
----------------------------

.. 
  Recall from :ref:`Strings <man-non-standard-string-literals>` that
  string literals prefixed by an identifier are called non-standard string
  literals, and can have different semantics than un-prefixed string
  literals. For example:

識別子の接頭辞が付いた文字列リテラルは非標準文字列リテラルと呼ばれ、
これは接頭辞が無い文字列リテラルとは異なる意味を持つという :ref:`文字列 <man-非標準文字列リテラル>` の内容を思い出してください。
例えば、:

.. 
  -  ``r"^\s*(?:#|$)"`` produces a regular expression object rather than a
     string
  -  ``b"DATA\xff\u2200"`` is a byte array literal for
     ``[68,65,84,65,255,226,136,128]``.

-  ``r"^\s*(?:#|$)"`` は文字列ではなく、通常の式オブジェクトを生成します。
-  ``[68,65,84,65,255,226,136,128]`` は ``b"DATA\xff\u2200"`` のバイト配列リテラルです。

.. 
  Perhaps surprisingly, these behaviors are not hard-coded into the Julia
  parser or compiler. Instead, they are custom behaviors provided by a
  general mechanism that anyone can use: prefixed string literals are
  parsed as calls to specially-named macros. For example, the regular
  expression macro is just the following::

驚くかもしれませんが、これらの処理はJuliaの構文解析やコンパイラにハードコーディングされているわけではありません。
代わりに、識別子の接頭辞が付いた文字列リテラルは、特別なマクロの呼び出しとして解析されるという、
誰もが使うことができる一般的な仕組みにより実現される、カスタムされた処理です。例えば、通常の式マクロは以下の通りです。::

    macro r_str(p)
      Regex(p)
    end

.. 
  That's all. This macro says that the literal contents of the string
  literal ``r"^\s*(?:#|$)"`` should be passed to the ``@r_str`` macro and
  the result of that expansion should be placed in the syntax tree where
  the string literal occurs. In other words, the expression
  ``r"^\s*(?:#|$)"`` is equivalent to placing the following object
  directly into the syntax tree::

これだけです。このマクロは、文字列リテラル ``r"^\s*(?:#|$)"`` のリテラル内容は ``@r_str`` マクロに渡され、
その拡張の結果は文字列リテラルが発生した構文ツリーに格納されると記述されています。言い換えれば、式 ``r"^\s*(?:#|$)"`` は
構文ツリーに以下のオブジェクトを直接格納することと同様です。::

    Regex("^\\s*(?:#|\$)")

.. 
  Not only is the string literal form shorter and far more convenient, but
  it is also more efficient: since the regular expression is compiled and
  the :obj:`Regex` object is actually created *when the code is compiled*,
  the compilation occurs only once, rather than every time the code is
  executed. Consider if the regular expression occurs in a loop::

この文字列リテラル形式は短くより利便性が高いだけでなく、より効率的です。通常式はコンパイルされ、
:obj:`Regex` オブジェクトはコードがコンパイルされた際に生成されるため、コンパイルは、
コードが実行されるごとに行われるのではなく、一度のみ行われます。通常式がループ内で起こるケースを考えてみてください。::

    for line = lines
      m = match(r"^\s*(?:#|$)", line)
      if m === nothing
        # non-comment
      else
        # comment
      end
    end

.. 
  Since the regular expression ``r"^\s*(?:#|$)"`` is compiled and inserted
  into the syntax tree when this code is parsed, the expression is only
  compiled once instead of each time the loop is executed. In order to
  accomplish this without macros, one would have to write this loop like
  this::

通常式 ``r"^\s*(?:#|$)"`` は、コードが解析された際にコンパイルされ構文ツリーに挿入されるため、
ループが実行される度にコンパイルされるのではなく、一度だけコンパイルされます。これをマクロ無しで実現するには、
ループを以下のように記述しなければなりません。::

    re = Regex("^\\s*(?:#|\$)")
    for line = lines
      m = match(re, line)
      if m === nothing
        # non-comment
      else
        # comment
      end
    end

.. 
  Moreover, if the compiler could not determine that the regex object was
  constant over all loops, certain optimizations might not be possible,
  making this version still less efficient than the more convenient
  literal form above. Of course, there are still situations where the
  non-literal form is more convenient: if one needs to interpolate a
  variable into the regular expression, one must take this more verbose
  approach; in cases where the regular expression pattern itself is
  dynamic, potentially changing upon each loop iteration, a new regular
  expression object must be constructed on each iteration. In the vast
  majority of use cases, however, regular expressions are not constructed
  based on run-time data. In this majority of cases, the ability to write
  regular expressions as compile-time values is invaluable.

さらに、もしコンパイラがregexオブジェクトは全てのループにおいて不変であるか判断がつかない場合、
特定の最適化処理は不可能であり、この記述方法は上記の便利なリテラル形式よりも非効率的となります。
もちろん、非リテラル形式がより便利である場合もあります。例えばもし変数を通常式に加筆したい場合、
この冗長的な記述をする必要があります。また通常式自体が動的の場合は、
新しい通常式がループの中の各繰り返しごとに構築される必要があります。
しかし、ほとんどのケースでは、通常式は実行時データを基に構築されることはありません。
多くの場合では、コンパイル時の値として通常式を書くことができる点は極めて価値があります。

.. 
  The mechanism for user-defined string literals is deeply, profoundly
  powerful. Not only are Julia's non-standard literals implemented using
  it, but also the command literal syntax (```echo "Hello, $person"```)
  is implemented with the following innocuous-looking macro::

このユーザ定義の文字列リテラルの仕組みは、非常に強力です。Juliaの非標準リテラルがこれを使用して実装されているだけでなく、
コマンドリテラル構文（ ```echo "Hello, $person"``` ）も以下の何の変哲も無いマクロにより実装されています。::

    macro cmd(str)
      :(cmd_gen($shell_parse(str)))
    end

.. 
  Of course, a large amount of complexity is hidden in the functions used
  in this macro definition, but they are just functions, written
  entirely in Julia. You can read their source and see precisely what they
  do — and all they do is construct expression objects to be inserted into
  your program's syntax tree.

もちろん多くの複雑なことがこのマクロ定義の中で使われている関数に隠されていますが、
それらは単に関数であり、全てJulia内に記述されています。ソースを読むことでそれらが何をしているかを知ることができますが、
それらは、構築された式オブジェクトをあなたのプログラムの構文ツリーに挿入することが全てです。

.. 
  Generated functions
  -------------------

生成関数
-------------------

.. 
  A very special macro is ``@generated``, which allows you to define so-called
  *generated functions*. These have the capability to generate specialized
  code depending on the types of their arguments with more flexibility and/or
  less code than what can be achieved with multiple dispatch. While macros
  work with expressions at parsing-time and cannot access the types of their
  inputs, a generated function gets expanded at a time when the types of
  the arguments are known, but the function is not yet compiled.

``@generated`` は特殊なマクロであり、生成関数と呼ばれるものを定義することができます。
この関数は、それらの引数の型に応じて、よりフレキシブルで、および[または]多重ディスパッチでできるものよりも少ないコードで、
特別なコードを生成することができます。マクロは解析時の拡張で動作し、インプットの型にアクセスすることはできませんが、
一方で生成関数は引数の型が判明し、関数がまだコンパイルされていないタイミングで拡張されます。

.. 
  Instead of performing some calculation or action, a generated function
  declaration returns a quoted expression which then forms the body for the
  method corresponding to the types of the arguments. When called, the body
  expression is first evaluated and compiled,
  then the returned expression is compiled and run.
  To make this efficient, the result is often cached.
  And to make this inferable, only a limited subset of the language is usable.
  Thus, generated functions provide a flexible framework to move work from
  run-time to compile-time, at the expense of greater restrictions on the allowable constructs.

計算や動作を行う代わりに、生成関数の宣言は、引数の型に対応するメソッドのボディ部を生成する引用式を返します。
呼び出されると、ボディ式が初めに評価およびコンパイルされ、返された式がコンパイルされ実行されます。
この処理を効率化するため、処理結果はキャッシュしばしばキャッシュされます。その後これを推測可能にし、
言語の限られたサブセットのみがアクセスできるようにします。したがって、許容される構築に対する大きな制約を犠牲にして、
生成関数は処理を実行時からコンパイル時に移動させるフレキシブルなフレームワークを提供します。

.. 
  When defining generated functions, there are four main differences to
  ordinary functions:
  
生成関数を定義する際、大きく4つの通常の関数との違いがあります。:  

.. 
  1. You annotate the function declaration with the ``@generated`` macro.
     This adds some information to the AST that lets the compiler know that
     this is a generated function.

1. ``@generated`` マクロで関数宣言に注釈を付けてください。
   これは、コンパイラにこの関数が生成関数であることを知らせるASTに情報を付け加えます。

.. 
  2. In the body of the generated function you only have access to the
     *types* of the arguments -- not their values -- and any function that
     was defined *before* the definition of the generated function.

2. 生成関数のボディ部では、引数の値ではなく、引数の型へのアクセスと、
   生成関数の定義よりも前に定義された関数へのアクセスのみを持ちます。

.. 
  3. Instead of calculating something or performing some action, you return
     a *quoted expression* which, when evaluated, does what you want.

3. 計算やその他の処理を行うのではなく、引用式が返されます。

.. 
  4. Generated functions must not have any side-effects or examine any non-constant
     global state (including, for example, IO, locks, or non-local dictionaries).
     In other words, they must be completely pure.
     Due to an implementation limitation,
     this also means that they currently cannot define a closure or untyped generator.

4. 生成関数はいかなる副作用や一定ではないグローバル状態（例えばIO、ロック、非ローカルディクショナリを含む）を持つことはできません。
   言い換えれば、保持できるのは純粋なものでなければなりません。実装の制約により、
   これは現在クロージャや非型生成プログラムを定義できないことを意味します。

.. 
  It's easiest to illustrate this with an example. We can declare a generated
  function ``foo`` as

例を見ることで簡単に理解できると思います。生成関数 ``foo`` を以下のように宣言することができます。


.. doctest::

    julia> @generated function foo(x)
               Core.println(x)
               return :(x * x)
           end
    foo (generic function with 1 method)

.. 
  Note that the body returns a quoted expression, namely ``:(x * x)``, rather
  than just the value of ``x * x``.

ボディ部は、値である ``x * x`` ではなく、引用式 ``:(x * x)`` を返す点に注意してください。

.. 
  From the caller's perspective, they are very similar to regular functions;
  in fact, you don't have to know if you're calling a regular or generated
  function - the syntax and result of the call is just the same.
  Let's see how ``foo`` behaves:

呼び出し側から見れば、これらは非常に通常関数と似ています。実際、ユーザは自分が通常関数を呼び出しているのか、
生成関数を呼び出しているのかを知る必要はありません。この2種類の呼び出しの構文と結果は同じです。
``foo`` がどのように動くのか見てみましょう。:

.. doctest::

    # note: output is from println() statement in the body
    julia> x = foo(2);
    Int64

    julia> x           # now we print x
    4

    julia> y = foo("bar");
    String

    julia> y
    "barbar"

.. 
  So, we see that in the body of the generated function, ``x`` is the
  *type* of the passed argument, and the value returned by the generated
  function, is the result of evaluating the quoted expression we returned
  from the definition, now with the *value* of ``x``.

生成関数のボディ部では、 ``x`` は渡された引数の型であり、
生成関数により返された値は定義から返された引用式の評価結果であり、この場合は ``x`` の値です。

.. 
  What happens if we evaluate ``foo`` again with a type that we have already
  used?

もし ``foo`` を既に使用した型で処理するとどうなるのでしょうか。

.. doctest::

    julia> foo(4)
    16

.. 
  Note that there is no printout of :obj:`Int64`. We can see that the body
  of the generated function was only executed once here,
  for the specific set of argument types, and the result was cached.
  After that, for this example, the expression returned from the generated
  function on the first invocation was re-used as the method body.
  However, the actual caching behavior is an implementation-defined performance optimization,
  so it is invalid to depend too closely on this behavior.

:obj:`Int64` の出力が無い点に注意してください。生成関数のボディ部は特定の引数の型のセットに対して1度だけ処理され、
処理結果はキャッシュされることがわかります。この例における後続の処理は、
最初の呼び出しの生成関数より返された式はメソッドのボディ部として再度使用されています。
しかし、実際のキャッシュ処理は実装定義されたパフォーマンスの最適化のためであり、
この処理に依存しすぎることは誤りです。

.. 
  The number of times a generated function is generated *might* be only once,
  but it *might* also be more often, or appear to not happen at all.
  As a consequence, you should *never* write a generated function with side effects - when,
  and how often, the side effects occur is undefined.
  (This is true for macros too - and just like for macros,
  the use of :func:`eval` in a generated function is a sign that
  you're doing something the wrong way.)
  However, unlike macros, the runtime system cannot correctly handle a call to
  :func:`eval`, so it is disallowed.

生成関数が生成される回数は1度だけですが、これは複数になったり、1度も起きない場合もあります。
結果として、生成関数は副作用とともに記述するべきではありません。
いつ、どのくらいの頻度で副作用が発生するかはわかりません（マクロでもあったように、
生成関数内で :func:`eval` を使用することは、何かが間違っているサインです）。
しかし、マクロとは違い、実行システムは :func:`eval` の呼び出しを正しく処理できないため、
これは許容されていません。

.. 
  The example generated function ``foo`` above did not do anything a normal
  function ``foo(x) = x * x`` could not do (except printing the type on the
  first invocation, and incurring higher overhead).
  However, the power of a generated function lies in its ability to compute
  different quoted expressions depending on the types passed to it:

上記の生成関数 ``foo`` の例は、通常の関数 ``foo(x) = x * x`` ができないこと
（最初の呼び出しの型を出力することと高いオーバーヘッドを被ることを除く）はしていません。
しかし、生成関数の利点は、渡された型に依存して異なる引用式を処理できる能力にあります。:

.. doctest::

   julia> @generated function bar(x)
              if x <: Integer
                  return :(x ^ 2)
              else
                  return :(x)
              end
          end
   bar (generic function with 1 method)

   julia> bar(4)
   16

   julia> bar("baz")
   "baz"

.. 
  (although of course this contrived example would be more easily implemented
  using multiple dispatch...)

（この不自然な例は多重ディスパッチを使用することでより簡単に実装できることは事実です。）

.. 
  Abusing this will corrupt the runtime system and cause undefined behavior:

これを不正に使用することは、実行システムに影響を与えたり定義していない動作をすることになります。:

.. doctest::

   julia> @generated function baz(x)
              if rand() < .9
                  return :(x^2)
              else
                  return :("boo!")
              end
          end
   baz (generic function with 1 method)

.. 
  Since the body of the generated function is non-deterministic, its behavior,
  *and the behavior of all subsequent code* is undefined.

生成関数のボディ部は確定的ではないため、その動作と後続のコードは定義されていません。

.. 
  *Don't copy these examples!*

*これらの例はコピーしないでください！*

.. 
  These examples are hopefully helpful to illustrate how generated functions
  work, both in the definition end and at the call site; however, *don't
  copy them*, for the following reasons:

これらの例は定義の最後と呼び出し側で生成関数がどのように動くか理解するのに役立つかと思います。
しかし、以下の理由により、コピーはしないようにしてください。:

.. 
  * the ``foo`` function has side-effects (the call to ``Core.println``), and it is undefined exactly when,
    how often or how many times these side-effects will occur
  * the ``bar`` function solves a problem that is better solved with multiple
    dispatch - defining ``bar(x) = x`` and ``bar(x::Integer) = x ^ 2`` will do
    the same thing, but it is both simpler and faster.
  * the ``baz`` function is pathologically insane

* ``foo`` 関数には副作用（ ``Core.println`` の呼び出し）があり、これはいつ、どれくらいの頻度で起こるのかは定義されていません。
* ``bar`` 関数は多重ディスパッチよりも効率的に動作します。 ``bar(x) = x`` と ``bar(x::Integer) = x ^ 2`` は
  同じ処理を行いますが、前者よりシンプルで早いです。
* ``baz`` 関数は理不尽なほど複雑です。

.. 
  Note that the set of operations that should not be attempted in a generated function
  is unbounded, and the runtime system can currently only detect a subset of
  the invalid operations. There are many other operations that will simply
  corrupt the runtime system without notification, usually in subtle
  ways not obviously connected to the bad definition.
  Because the function generator is run during inference,
  it must respect all of the limitations of that code.

生成関数内で実行すべきではない処理は数多くあり、実行システムは現在誤った処理のサブセットのみを検知できる点に注意してください。
その他にも、目立たず不正な定義に関連しない、通知なく実行システムに影響を与える処理は数多くあります。
推測時に関数の生成が実行されるため、コードの制約を把握する必要があります。

.. 
  Some operations that should not be attempted include:

生成関数の中で実行すべきではない処理の例は、:

.. 
    1. Caching of native pointers.

    2. Interacting with the contents or methods of Core.Inference in any way.

    3. Observing any mutable state.

       - Inference on the generated function may be run at *any* time,
         including while your code is attempting to observe or mutate this state.

    4. Taking any locks: C code you call out to may use locks internally,
       (for example, it is not problematic to call ``malloc``,
       even though most implementations require locks internally)
       but don't attempt to hold or acquire any while executing Julia code.

    5. Calling any function that is defined after the body of the generated function.
       This condition is relaxed for incrementally-loaded precompiled modules to
       allow calling any function in the module.
     
1. 固有のポインターをキャッシュする

2. Core.Inferenceの内容やメソッドに関連づける

3. 可変の状態を監視する

   - 生成関数の推測は、コードが状態を監視したり変更する際も含めて、いつでも実行されます。

4. ロックを取る:Cコードを呼び出すと内部的にロックを使いますが（例えば、
   ほとんどの実装ではロックを内部的に必要としますが、 ``malloc`` を呼び出すことは問題ではありません）、
   Juliaコードを実行中にはロックを保持したり取得しようとしないでください。

5. 生成関数のボディ部以降に定義された関数を呼び出す:この条件は、
   増分ロードのプリコンパイルがモジュール内の関数を呼び出すことができるように、緩められています。   

.. 
  Alright, now that we have a better understanding of how generated functions
  work, let's use them to build some more advanced (and valid) functionality...

生成関数がどのように動くのかについて知ることができたので、生成関数を使って高度な機能を構築してみましょう。

.. 
  An advanced example
  ~~~~~~~~~~~~~~~~~~~

高度な例
~~~~~~~~~~~~~~~~~~~

.. 
  Julia's base library has a :func:`sub2ind` function to calculate a
  linear index into an n-dimensional array, based on a set of n multilinear
  indices - in other words, to calculate the index ``i`` that can be used to
  index into an array ``A`` using ``A[i]``, instead of ``A[x,y,z,...]``. One
  possible implementation is the following::

Juliaの基本ライブラリは、n重線形インデックスのセットを基に、線形インデックスをn次元の配列に入れて計算する :func:`sub2ind` 関数を持ちます。
言い換えれば、この関数は ``A[x,y,z,...]`` の代わりに、 ``A[i]`` を使って配列 ``A`` に入れて
使用することができるインデックス ``i`` を計算します。実装の一例は以下の通りです。::

    function sub2ind_loop{N}(dims::NTuple{N}, I::Integer...)
        ind = I[N] - 1
        for i = N-1:-1:1
            ind = I[i]-1 + dims[i]*ind
        end
        return ind + 1
    end

.. 
  The same thing can be done using recursion::

再帰を使用して同じ処理をすることができます。::

    sub2ind_rec(dims::Tuple{}) = 1
    sub2ind_rec(dims::Tuple{}, i1::Integer, I::Integer...) =
        i1 == 1 ? sub2ind_rec(dims, I...) : throw(BoundsError())
    sub2ind_rec(dims::Tuple{Integer, Vararg{Integer}}, i1::Integer) = i1
    sub2ind_rec(dims::Tuple{Integer, Vararg{Integer}}, i1::Integer, I::Integer...) =
        i1 + dims[1] * (sub2ind_rec(tail(dims), I...) - 1)

.. 
  Both these implementations, although different, do essentially the same
  thing: a runtime loop over the dimensions of the array, collecting the
  offset in each dimension into the final index.

この2つの異なる実装は、配列の次元に対する実行時ループと、各次元のオフセットを最終のインデックスに集めるという同じ処理を行います。

.. 
  However, all the information we need for the loop is embedded in the type
  information of the arguments. Thus, we can utilize generated functions to
  move the iteration to compile-time; in compiler parlance, we use generated
  functions to manually unroll the loop. The body becomes almost identical,
  but instead of calculating the linear index, we build up an *expression*
  that calculates the index:

しかし、ループに必要な情報は、全て引数の型情報に埋め込まれています。したがって、
繰り返し処理をコンパイル時に移動させるために生成関数を使うことができ、
コンパイルの言葉で言うと、ループをマニュアルで展開するために生成関数を使うことができます。
ボディ部はほぼ同じものになりますが、線形インデックスを計算する代わりに、インデックスを計算する式を構築します。:

.. code-block:: julia

    @generated function sub2ind_gen{N}(dims::NTuple{N}, I::Integer...)
        ex = :(I[$N] - 1)
        for i = (N - 1):-1:1
            ex = :(I[$i] - 1 + dims[$i] * $ex)
        end
        return :($ex + 1)
    end

.. 
  **What code will this generate?**

**どのようなコードを生成するのか？**

.. 
  An easy way to find out, is to extract the body into another (regular)
  function:

確認する簡単な方法として、ボディ部を他の（通常）関数へ抽出することができます。:

.. doctest::

   julia> @generated function sub2ind_gen{N}(dims::NTuple{N}, I::Integer...)
              return sub2ind_gen_impl(dims, I...)
          end
   sub2ind_gen (generic function with 1 method)

   julia> function sub2ind_gen_impl{N}(dims::Type{NTuple{N}}, I...)
              length(I) == N || return :(error("partial indexing is unsupported"))
              ex = :(I[$N] - 1)
              for i = (N - 1):-1:1
                  ex = :(I[$i] - 1 + dims[$i] * $ex)
              end
              return :($ex + 1)
          end
   sub2ind_gen_impl (generic function with 1 method)

.. 
  We can now execute ``sub2ind_gen_impl`` and examine the expression it
  returns:

``sub2ind_gen_impl`` を実行して、これが返す式を確認することができます。:

.. doctest::

   julia> sub2ind_gen_impl(Tuple{Int,Int}, Int, Int)
   :(((I[1] - 1) + dims[1] * (I[2] - 1)) + 1)

.. 
  So, the method body that will be used here doesn't include a loop at all
  - just indexing into the two tuples, multiplication and addition/subtraction.
  All the looping is performed compile-time, and we avoid looping during
  execution entirely. Thus, we only loop *once per type*, in this case once
  per ``N`` (except in edge cases where the function is generated more than
  once - see disclaimer above).
  
つまり、ここで使用されるメソッドのボディ部はループを全く導入せず、単に2つのタプル
（乗算および加算/減算）にインデックス化するだけです。全てのループはコンパイル時に実行され、
実行時のループを回避します。したがって、型に対して1度だけループされ、
このケースでは ``N`` （1つ以上の関数が生成される特殊なケースは除きます。上記の否認声明を参照してください。）に対して1度ループされます。
 
