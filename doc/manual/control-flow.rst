.. _man-control-flow:

.. currentmodule:: Base

.. 
 **************
  Control Flow
 **************

**************
 制御フロー
**************

.. 
 Julia provides a variety of control flow constructs:

 -  :ref:`man-compound-expressions`: ``begin`` and ``(;)``.
 -  :ref:`man-conditional-evaluation`:
    ``if``-``elseif``-``else`` and ``?:`` (ternary operator).
 -  :ref:`man-short-circuit-evaluation`:
    ``&&``, ``||`` and chained comparisons.
 -  :ref:`man-loops`: ``while`` and ``for``.
 -  :ref:`man-exception-handling`:
    ``try``-``catch``, :func:`error` and :func:`throw`.
 -  :ref:`man-tasks`: :func:`yieldto`.

Juliaはさまざまな制御フロー構成を提供しています。

-  :ref:`man-複合式`: ``begin`` および ``(;)``
-  :ref:`man-if文`:
   ``if``-``elseif``-``else`` および ``?:`` (三項演算子)
-  :ref:`man-短絡評価`:
   ``&&``、 ``||`` および連結比較
-  :ref:`man-繰り返し評価`: ``while`` および ``for``
-  :ref:`man-例外処理`:
   ``try``-``catch``、 :func:`error` および :func:`throw`
-  :ref:`man-タスク`: :func:`yieldto`

.. 
 The first five control flow mechanisms are standard to high-level
 programming languages. :class:`Task`\ s are not so standard: they provide non-local
 control flow, making it possible to switch between temporarily-suspended
 computations. This is a powerful construct: both exception handling and
 cooperative multitasking are implemented in Julia using tasks. Everyday
 programming requires no direct usage of tasks, but certain problems can
 be solved much more easily by using tasks.

上記の最初の5つの制御フローは、ハイレベルなプログラミング言語の標準的機能です。 :class:`Task`\ は標準ではなく、
これは一時的に中断された計算を切り替えることができる非ローカル制御フローを提供します。
これは強力な機能であり、例外処理および協同マルチタスクの両方がタスクを使用してJuliaに実装されています。
日々のプログラミングでは、タスクを直接使用する必要はありませんが、特定の問題はタスクを使用することで簡単に解決することができます。


.. _man-compound-expressions:

.. 
 Compound Expressions
 --------------------

複合式
--------------------


.. 
 Sometimes it is convenient to have a single expression which evaluates
 several subexpressions in order, returning the value of the last
 subexpression as its value. There are two Julia constructs that
 accomplish this: ``begin`` blocks and ``(;)`` chains. The value of both
 compound expression constructs is that of the last subexpression. Here's
 an example of a ``begin`` block:

複数の部分式を順番に評価し、最後の部分式の値をその値として返す単一の式を利用することは便利です。
Juliaには、これを実現する ``begin`` ブロックおよび ``(;)`` チェーンの2つの構成があります。
両方の複合式構成の値は、最後の部分式の値となります。以下は ``begin`` ブロックの例です。

.. doctest::

    julia> z = begin
             x = 1
             y = 2
             x + y
           end
    3

.. 
 Since these are fairly small, simple expressions, they could easily be
 placed onto a single line, which is where the ``(;)`` chain syntax comes
 in handy:

これらは単純な式であるため、 ``(;)`` チェーン構文を使用することで簡単に単一の行に置き換えることができます。

.. doctest::

    julia> z = (x = 1; y = 2; x + y)
    3

.. 
 This syntax is particularly useful with the terse single-line function
 definition form introduced in :ref:`man-functions`. Although it
 is typical, there is no requirement that ``begin`` blocks be multiline
 or that ``(;)`` chains be single-line:

この構文は、 :ref:`man-関数` で説明された簡潔な単一行の関数定義の際に特に便利です。
``begin`` ブロックを複数行にする必要はなく、 ``(;)`` チェーンを単一行にする必要はありません。

.. doctest::

    julia> begin x = 1; y = 2; x + y end
    3

    julia> (x = 1;
            y = 2;
            x + y)
    3

.. _man-conditional-evaluation:

.. 
 Conditional Evaluation
 ----------------------

if文
----------------------

.. 
 Conditional evaluation allows portions of code to be evaluated or not
 evaluated depending on the value of a boolean expression. Here is the
 anatomy of the ``if``-``elseif``-``else`` conditional syntax::

if文は、ブール式の値に応じてコードの一部を評価したり評価しなかったりすることができます。
``if``-``elseif``-``else`` 条件構文の例は次のとおりです。::
 
    if x < y
      println("x is less than y")
    elseif x > y
      println("x is greater than y")
    else
      println("x is equal to y")
    end

.. 
 If the condition expression ``x < y`` is ``true``, then the corresponding block
 is evaluated; otherwise the condition expression ``x > y`` is evaluated, and if
 it is ``true``, the corresponding block is evaluated; if neither expression is
 true, the ``else`` block is evaluated. Here it is in action:

条件式 ``x < y`` が ``true`` の場合、対応するブロックが評価されます。それ以外の場合は、
条件式 ``x > y`` が評価され、 ``true`` の場合は対応するブロックが評価されます。
どちらの式も真でない場合、 ``else`` ブロックが評価されます。以下は処理の例です。

.. doctest::

    julia> function test(x, y)
             if x < y
               println("x is less than y")
             elseif x > y
               println("x is greater than y")
             else
               println("x is equal to y")
             end
           end
    test (generic function with 1 method)

    julia> test(1, 2)
    x is less than y

    julia> test(2, 1)
    x is greater than y

    julia> test(1, 1)
    x is equal to y

.. 
 The ``elseif`` and ``else`` blocks are optional, and as many ``elseif``
 blocks as desired can be used. The condition expressions in the
 ``if``-``elseif``-``else`` construct are evaluated until the first one
 evaluates to ``true``, after which the associated block is evaluated,
 and no further condition expressions or blocks are evaluated.

``elseif`` および ``else`` ブロックはオプションであり、必要に応じた数の ``elseif`` ブロックを使用できます。
``if``-``elseif``-``else`` 構文の条件式は、最初のものが ``true`` と評価されるまで評価され、
その後に関連するブロックが評価され、それ以外の条件式やブロックは評価されません。

.. 
 ``if`` blocks are "leaky", i.e. they do not introduce a local scope.
 This means that new variables defined inside the ``ìf`` clauses can
 be used after the ``if`` block, even if they weren't defined before.
 So, we could have defined the ``test`` function above as

``if`` ブロックはフレキシブルであり、ローカルスコープとはなりません。
これは、 ``ìf`` 節の中で定義された新しい変数は、たとえ前に定義されていないとしても、
``ìf`` ブロックの後に使用できることを意味します。これにより、上記の ``test`` 関数を次のように定義することができます。

.. doctest:: leaky

    julia> function test(x,y)
             if x < y
               relation = "less than"
             elseif x == y
               relation = "equal to"
             else
               relation = "greater than"
             end
             println("x is ", relation, " y.")
           end
    test (generic function with 1 method)

.. 
 The variable ``relation`` is declared inside the ``if`` block, but used
 outside. However, when depending on this behavior, make sure all possible
 code paths define a value for the variable. The following change to
 the above function results in a runtime error

``relation`` 関数は ``if`` ブロック内で宣言されていますが、ブロックの外側で使用されています。
しかし、この動作を利用する際には、すべてのコードパスが変数の値を定義していることを確認してください。
上記の関数を次のように変更すると、ランタイムエラーが発生します。

.. doctest:: bad-leaky

    julia> function test(x,y)
             if x < y
               relation = "less than"
             elseif x == y
               relation = "equal to"
             end
             println("x is ", relation, " y.")
           end
    test (generic function with 1 method)

    julia> test(1,2)
    x is less than y.

    julia> test(2,1)
    ERROR: UndefVarError: relation not defined
     in test(::Int64, ::Int64) at ./none:7
     ...

.. 
 ``if`` blocks also return a value, which may seem unintuitive to users
 coming from many other languages. This value is simply the return value
 of the last executed statement in the branch that was chosen, so

``if`` ブロックが値を返すというのは、他の多くの言語を経験したユーザーにとっては直感的ではないように
思えるかもしれません。この値は、単に選択された分岐で最後に実行されたステートメントの戻り値です。

.. doctest::

    julia> x = 3
    3

    julia> if x > 0
               "positive!"
           else
               "negative..."
           end
    "positive!"

.. 
 Note that very short conditional statements (one-liners) are frequently expressed using
 Short-Circuit Evaluation in Julia, as outlined in the next section.

非常に短い条件文（1行）は、次のセクションで説明するように、多くの場合Juliaの短絡評価を使用して表現されることに注意してください。

.. 
 Unlike C, MATLAB, Perl, Python, and Ruby — but like Java, and a few
 other stricter, typed languages — it is an error if the value of a
 conditional expression is anything but ``true`` or ``false``:

C、MATLAB、Perl、Python、Rubyと異なり、しかしJavaやその他の厳密に型付けされた言語のように、
条件式の値が ``true`` または ``false`` 以外の場合はエラーとなります。:

.. doctest::

    julia> if 1
             println("true")
           end
    ERROR: TypeError: non-boolean (Int64) used in boolean context
     ...

.. 
 This error indicates that the conditional was of the wrong type:
 :obj:`Int64` rather than the required :obj:`Bool`.

このエラーは、条件文が間違った型であることを示します。この場合、 :obj:`Bool` ではなく :obj:`Int64` です。

.. 
 The so-called "ternary operator", ``?:``, is closely related to the
 ``if``-``elseif``-``else`` syntax, but is used where a conditional
 choice between single expression values is required, as opposed to
 conditional execution of longer blocks of code. It gets its name from
 being the only operator in most languages taking three operands::

いわゆる「三項演算子」の ``?:`` は、 ``if``-``elseif``-``else`` 構文と密接に関連していますが、
長いコードブロックを条件付きで実行する場合とは反対に、単一の式の値の条件付き選択が必要な場合に使用されます。
これは、3つのオペランドを取るほとんどの言語で唯一の演算子であることから、その名前が付けられています。::

    a ? b : c

.. 
 The expression ``a``, before the ``?``, is a condition expression, and
 the ternary operation evaluates the expression ``b``, before the ``:``,
 if the condition ``a`` is ``true`` or the expression ``c``, after the
 ``:``, if it is ``false``.

``?`` の前の式 ``a`` は条件式であり、式 ``a`` が ``true`` の場合、
または ``:`` の後の式 ``c`` が ``false`` の場合に三項演算子は式 ``b`` を評価します。

.. 
 The easiest way to understand this behavior is to see an example. In the
 previous example, the ``println`` call is shared by all three branches:
 the only real choice is which literal string to print. This could be
 written more concisely using the ternary operator. For the sake of
 clarity, let's try a two-way version first:

例を見ることでこの動作を良く理解できます。前の例では、 ``println`` 呼び出しは3つの分岐すべてで共有されています。
実際の選択肢は、どのリテラル文字を出力するかです。これは、三項演算子を使うことでより簡潔に書くことができます。
わかりやすくするために、最初に双方向バージョンを見てみましょう。

.. doctest::

    julia> x = 1; y = 2;

    julia> println(x < y ? "less than" : "not less than")
    less than

    julia> x = 1; y = 0;

    julia> println(x < y ? "less than" : "not less than")
    not less than

.. 
 If the expression ``x < y`` is true, the entire ternary operator
 expression evaluates to the string ``"less than"`` and otherwise it
 evaluates to the string ``"not less than"``. The original three-way
 example requires chaining multiple uses of the ternary operator
 together:

式 ``x < y`` が真の場合、三項演算子式全体が文字列 ``"less than"`` と評価され、
そうでない場合は ``"not less than"`` と評価されます。最初の例では、
三項演算子の複数の使用を連結させる必要があります。

.. doctest:: ternary-operator

    julia> test(x, y) = println(x < y ? "x is less than y"    :
                                x > y ? "x is greater than y" : "x is equal to y")
    test (generic function with 1 method)

    julia> test(1, 2)
    x is less than y

    julia> test(2, 1)
    x is greater than y

    julia> test(1, 1)
    x is equal to y

.. 
 To facilitate chaining, the operator associates from right to left.

連結を容易にするために、演算子は右から左に関連付けると良いでしょう。

.. 
 It is significant that like ``if``-``elseif``-``else``, the expressions
 before and after the ``:`` are only evaluated if the condition
 expression evaluates to ``true`` or ``false``, respectively:

``if``-``elseif``-``else`` のように、 ``:`` の前後の式は、それぞれ条件式が ``true`` または ``false`` と
評価された場合にのみ評価されます。

.. doctest::

    julia> v(x) = (println(x); x)
    v (generic function with 1 method)


    julia> 1 < 2 ? v("yes") : v("no")
    yes
    "yes"

    julia> 1 > 2 ? v("yes") : v("no")
    no
    "no"

.. _man-short-circuit-evaluation:

.. 
 Short-Circuit Evaluation
 ------------------------

短絡評価
------------------------

.. 
 Short-circuit evaluation is quite similar to conditional evaluation. The
 behavior is found in most imperative programming languages having the
 ``&&`` and ``||`` boolean operators: in a series of boolean expressions
 connected by these operators, only the minimum number of expressions are
 evaluated as are necessary to determine the final boolean value of the
 entire chain. Explicitly, this means that:

 -  In the expression ``a && b``, the subexpression ``b`` is only
    evaluated if ``a`` evaluates to ``true``.
 -  In the expression ``a || b``, the subexpression ``b`` is only
    evaluated if ``a`` evaluates to ``false``.

短絡評価は条件付き評価と非常によく似ています。この動作は、 ``&&`` および ``||`` ブール演算子を持つ
ほとんどの命令型プログラミング言語で見られます。これらの演算子で連結された一連のブール式では、
連結全体の最終的なブール値を決定するのに必要な最低限の式だけが評価されます。明示的には、これは次のことを意味します。

-  ``a && b`` の式では、 ``a`` が ``true`` と評価された場合にのみ部分式 ``b`` が評価されます。
-  ``a || b`` の式では、 ``a`` が ``false`` と評価された場合にのみ部分式 ``b`` が評価されます。

.. 
 The reasoning is that ``a && b`` must be ``false`` if ``a`` is
 ``false``, regardless of the value of ``b``, and likewise, the value of
 ``a || b`` must be true if ``a`` is ``true``, regardless of the value of
 ``b``. Both ``&&`` and ``||`` associate to the right, but ``&&`` has
 higher precedence than ``||`` does. It's easy to experiment with
 this behavior:

論理的理由として、 ``a && b`` の式は、 ``b`` の値に関係なく、 ``a`` が ``false`` の場合は ``false`` でなければならず、
また同様に ``a || b`` の式は、 ``b`` の値に関係なく、 ``a`` が ``true`` の場合は ``true`` でなければならないためです。
``&&`` および ``||`` は共に右の式に関連付けますが、 ``&&`` は ``||`` よりも高い優先順位を持ちます。
この動作を試してみましょう。

.. doctest::

    julia> t(x) = (println(x); true)
    t (generic function with 1 method)

    julia> f(x) = (println(x); false)
    f (generic function with 1 method)

    julia> t(1) && t(2)
    1
    2
    true

    julia> t(1) && f(2)
    1
    2
    false

    julia> f(1) && t(2)
    1
    false

    julia> f(1) && f(2)
    1
    false

    julia> t(1) || t(2)
    1
    true

    julia> t(1) || f(2)
    1
    true

    julia> f(1) || t(2)
    1
    2
    true

    julia> f(1) || f(2)
    1
    2
    false

.. 
 You can easily experiment in the same way with the associativity and
 precedence of various combinations of ``&&`` and ``||`` operators.

``&&`` および ``||`` 演算子の様々な組み合わせの結合性と優先度について、簡単な実験で確認することができます。

.. 
 This behavior is frequently used in Julia to form an alternative to very short
 ``if`` statements. Instead of ``if <cond> <statement> end``, one can write
 ``<cond> && <statement>`` (which could be read as: <cond> *and then* <statement>).
 Similarly, instead of ``if ! <cond> <statement> end``, one can write
 ``<cond> || <statement>`` (which could be read as: <cond> *or else* <statement>).

この動作は、Juliaでは非常に短い ``if`` 文の代わりに頻繁に使用されます。 ``if <cond> <statement> end`` の代わりに、
``<cond> && <statement>`` （「<cond>とその次に<statement>」と読むことができる）と書くことができます。
同様に、 ``if ! <cond> <statement> end`` の代わりに、 ``<cond> || <statement>`` （これは
「<cond>または <statement>」として読み取ることができる）と書くことができます。

.. 
 For example, a recursive factorial routine could be defined like this:

例えば、再帰的階乗ルーチンは次のように定義することができます。

.. doctest::

    julia> function fact(n::Int)
               n >= 0 || error("n must be non-negative")
               n == 0 && return 1
               n * fact(n-1)
           end
    fact (generic function with 1 method)

    julia> fact(5)
    120

    julia> fact(0)
    1

    julia> fact(-1)
    ERROR: n must be non-negative
     in fact(::Int64) at ./none:2
     ...

.. 
  Boolean operations *without* short-circuit evaluation can be done with the
  bitwise boolean operators introduced in :ref:`man-mathematical-operations`:
  ``&`` and ``|``. These are normal functions, which happen to support
  infix operator syntax, but always evaluate their arguments:

短絡評価のないブール演算は、 :ref:`man-算術処理と基本的な関数`で紹介されたビット単位の論理演算子（ ``&`` および ``|`` ）で
行うことができます。これらは通常の関数であり、中置演算子の構文をサポートしますが、常に引数を評価します。

.. doctest::

    julia> f(1) & t(2)
    1
    2
    false

    julia> t(1) | t(2)
    1
    2
    true

.. 
  Just like condition expressions used in ``if``, ``elseif`` or the
  ternary operator, the operands of ``&&`` or ``||`` must be boolean
  values (``true`` or ``false``). Using a non-boolean value anywhere
  except for the last entry in a conditional chain is an error:

``if`` 、 ``elseif`` または三項演算子で使用される条件式と同様に、 ``&&`` または ``||`` の被演算子は
ブール値（ ``true`` または ``false`` ）である必要があります。条件付き連結の最後のエントリを除き、
非ブール値を使用した場合はエラーとなります。

.. doctest::

    julia> 1 && true
    ERROR: TypeError: non-boolean (Int64) used in boolean context
     ...

.. 
  On the other hand, any type of expression can be used at the end of a conditional chain.
  It will be evaluated and returned depending on the preceding conditionals:

一方で、条件付き連結の終わりには、任意の型の式を使用できます。これは、前の条件に応じて評価され、結果が返されます。

.. testsetup::

    srand(123);

.. doctest::

    julia> true && (x = rand(2,2))
    2×2 Array{Float64,2}:
     0.768448  0.673959
     0.940515  0.395453

    julia> false && (x = rand(2,2))
    false

.. _man-loops:

.. 
  Repeated Evaluation: Loops
  --------------------------

繰り返し評価：ループ
--------------------------

.. 
  There are two constructs for repeated evaluation of expressions: the
  ``while`` loop and the ``for`` loop. Here is an example of a ``while``
  loop:

式の繰り返し評価には、 ``while`` ループと ``for`` ループの2つの方法があります。以下は ``while`` ループの例となります。

.. doctest::

    julia> i = 1;

    julia> while i <= 5
             println(i)
             i += 1
           end
    1
    2
    3
    4
    5

.. 
  The ``while`` loop evaluates the condition expression (``i <= 5`` in this
  case), and as long it remains ``true``, keeps also evaluating the body
  of the ``while`` loop. If the condition expression is ``false`` when the
  ``while`` loop is first reached, the body is never evaluated.

``while`` ループは条件式を評価し（上記の場合は ``i <= 5`` ）、結果が ``true`` である限り ``while`` ループの
本文を評価します。 ``while`` ループの最初の評価の際に、条件式の結果が ``false`` の場合、本文は評価されません。

.. 
  The ``for`` loop makes common repeated evaluation idioms easier to
  write. Since counting up and down like the above ``while`` loop does is
  so common, it can be expressed more concisely with a ``for`` loop:

``for`` ループは、共通の繰り返し評価文を書きやすくします。上記の ``while`` ループのように、
カウントダウンおよびカウントダウンは頻繁に使用するため、 ``for`` ループを使用することで簡潔に表現できます。

.. doctest::

    julia> for i = 1:5
             println(i)
           end
    1
    2
    3
    4
    5

.. 
  Here the ``1:5`` is a ``Range`` object, representing the sequence of
  numbers 1, 2, 3, 4, 5. The ``for`` loop iterates through these values,
  assigning each one in turn to the variable ``i``. One rather important
  distinction between the previous ``while`` loop form and the ``for``
  loop form is the scope during which the variable is visible. If the
  variable ``i`` has not been introduced in an other scope, in the ``for``
  loop form, it is visible only inside of the ``for`` loop, and not
  afterwards. You'll either need a new interactive session instance or a
  different variable name to test this:

ここでは、 ``1:5`` は ``Range`` オブジェクトであり、一連の数字1、2、3、4、5を表します。
``for`` ループはこれらの値を繰り返し処理し、それぞれを変数 ``i`` に順番に割り当てます。
前述の ``while`` ループ形式と ``for`` ループ形式の重要な違いの1つは、
どの変数が表示されるかというスコープです。変数 ``i`` が他のスコープで定義されていない場合、
``for`` ループ形式では ``for`` ループの内部でのみ表示され、その後は表示されません。
これをテストするには、新しい対話型セッションインスタンスまたは別の変数名が必要となります。

.. doctest::

    julia> for j = 1:5
             println(j)
           end
    1
    2
    3
    4
    5

    julia> j
    ERROR: UndefVarError: j not defined
     ...

.. 
  See :ref:`man-variables-and-scoping` for a detailed
  explanation of variable scope and how it works in Julia.

変数のスコープの詳細な説明とJuliaでの動作の詳細については、:ref:`man-変数のスコープ` を参照してください。

.. 
  In general, the ``for`` loop construct can iterate over any container.
  In these cases, the alternative (but fully equivalent) keyword ``in``
  or ``∈`` is typically used instead of ``=``, since it makes the code read more
  clearly:

通常、 ``for`` ループ文はどのコンテナに対しても繰り返し処理が可能です。これらの場合、
代替（または同等）キーワード ``in`` または ``∈`` は、コードがより明確になるため、
通常は ``=`` の代わりに使用されます。

.. doctest::

    julia> for i in [1,4,0]
             println(i)
           end
    1
    4
    0

    julia> for s ∈ ["foo","bar","baz"]
             println(s)
           end
    foo
    bar
    baz

.. 
  Various types of iterable containers will be introduced and discussed in
  later sections of the manual (see, e.g., :ref:`man-arrays`).

様々なタイプの繰り返し処理可能なコンテナについては、マニュアルの後のセクションで紹介します
（:ref:`man-配列` を参照してください）。

.. 
  It is sometimes convenient to terminate the repetition of a ``while``
  before the test condition is falsified or stop iterating in a ``for``
  loop before the end of the iterable object is reached. This can be
  accomplished with the ``break`` keyword:

テスト条件が改ざんされる前に ``while`` の繰り返し処理を終了する、または繰り返し処理可能な
オブジェクトの終わりに達する前に ``for`` ループの繰り返し処理を停止することで、
便利な場合があります。これは ``break`` キーワードを使用することで実現できます。

.. doctest::

    julia> i = 1;

    julia> while true
             println(i)
             if i >= 5
               break
             end
             i += 1
           end
    1
    2
    3
    4
    5

    julia> for i = 1:1000
             println(i)
             if i >= 5
               break
             end
           end
    1
    2
    3
    4
    5

.. 
  The above ``while`` loop would never terminate on its own, and the
  ``for`` loop would iterate up to 1000. These loops are both exited early
  by using the ``break`` keyword.

上記の ``while`` ループは終了することはなく、また ``for`` ループは1000まで繰り返されます。
これらのループは、 ``break`` キーワードを使用することで、どちらも早期に終了することができます。

.. 
  In other circumstances, it is handy to be able to stop an iteration and
  move on to the next one immediately. The ``continue`` keyword
  accomplishes this:

他の状況では、繰り返しを停止してすぐに次の処理に進むことで便利になる場合があります。
``continue`` キーワードを使用することでこれを実現できます。

.. doctest::

    julia> for i = 1:10
             if i % 3 != 0
               continue
             end
             println(i)
           end
    3
    6
    9

.. 
  This is a somewhat contrived example since we could produce the same
  behavior more clearly by negating the condition and placing the
  ``println`` call inside the ``if`` block. In realistic usage there is
  more code to be evaluated after the ``continue``, and often there are
  multiple points from which one calls ``continue``.

これは、条件を無効にして ``println`` 呼び出しを ``if`` ブロック内に置くことで同じ動作をより明確に実施できるため、
説得力に欠ける例です。現実的な利用では、 ``continue`` の後に評価されるべきコードがさらにあり、
また多くの場合、 ``continue`` を呼び出す複数のポイントが存在します。

.. 
  Multiple nested ``for`` loops can be combined into a single outer loop,
  forming the cartesian product of its iterables:

複数のネストされた ``for`` ループは、1つのアウターループに結合され、繰り返し処理のデカルト積を形成します。

.. doctest::

    julia> for i = 1:2, j = 3:4
             println((i, j))
           end
    (1,3)
    (1,4)
    (2,3)
    (2,4)

.. 
  A ``break`` statement inside such a loop exits the entire nest of loops,
  not just the inner one.

このようなループ内の ``break`` 文は、内側のループだけでなく、ループのネスト全体を終了します。

.. _man-exception-handling:

.. 
  Exception Handling
  ------------------

例外処理
------------------

.. 
  When an unexpected condition occurs, a function may be unable to return
  a reasonable value to its caller. In such cases, it may be best for the
  exceptional condition to either terminate the program, printing a
  diagnostic error message, or if the programmer has provided code to
  handle such exceptional circumstances, allow that code to take the
  appropriate action.

予期しない状況が発生した場合、関数は呼び出し側に妥当な値を返すことができない場合があります。
そのような場合、例外条件では、プログラムを終了させる、診断エラーメッセージを出力する、
またはプログラマが例外的な状況を処理するコードを提供している場合、
そのコードが適切な処置をとるようにすることができます。

.. 
  Built-in :exc:`Exception`\ s
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ビルトイン :exc:`Exception`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  :exc:`Exception`\ s are thrown when an unexpected condition has occurred. The
  built-in :exc:`Exception`\ s listed below all interrupt the normal flow of control.

予期しない条件が発生した場合、 :exc:`Exception` が返されます。以下に記載されているビルトインの :exc:`Exception` は、
すべて制御の通常の流れを中断します。

+------------------------------+
| :exc:`Exception`             |
+==============================+
| :exc:`ArgumentError`         |
+------------------------------+
| :exc:`BoundsError`           |
+------------------------------+
| :exc:`CompositeException`    |
+------------------------------+
| :exc:`DivideError`           |
+------------------------------+
| :exc:`DomainError`           |
+------------------------------+
| :exc:`EOFError`              |
+------------------------------+
| :exc:`ErrorException`        |
+------------------------------+
| :exc:`InexactError`          |
+------------------------------+
| :exc:`InitError`             |
+------------------------------+
| :exc:`InterruptException`    |
+------------------------------+
| :exc:`InvalidStateException` |
+------------------------------+
| :exc:`KeyError`              |
+------------------------------+
| :exc:`LoadError`             |
+------------------------------+
| :exc:`OutOfMemoryError`      |
+------------------------------+
| :exc:`ReadOnlyMemoryError`   |
+------------------------------+
| :exc:`RemoteException`       |
+------------------------------+
| :exc:`MethodError`           |
+------------------------------+
| :exc:`OverflowError`         |
+------------------------------+
| :exc:`ParseError`            |
+------------------------------+
| :exc:`SystemError`           |
+------------------------------+
| :exc:`TypeError`             |
+------------------------------+
| :exc:`UndefRefError`         |
+------------------------------+
| :exc:`UndefVarError`         |
+------------------------------+
| :exc:`UnicodeError`          |
+------------------------------+


.. 
  For example, the :func:`sqrt` function throws a :exc:`DomainError` if applied to a
  negative real value:

例えば、 :func:`sqrt` 関数は負の実数値に適用された場合、 :exc:`DomainError` を返します。

.. doctest::

    julia> sqrt(-1)
    ERROR: DomainError:
    sqrt will only return a complex result if called with a complex argument. Try sqrt(complex(x)).
     in sqrt(::Int64) at ./math.jl:252
     ...

.. 
  You may define your own exceptions in the following way:
  
独自の例外処理は、次のように定義することができます。

.. doctest::

    julia> type MyCustomException <: Exception end

.. 
  The :func:`throw` function
  ~~~~~~~~~~~~~~~~~~~~~~~~~~

:func:`throw` 関数
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Exceptions can be created explicitly with :func:`throw`. For example, a function
  defined only for nonnegative numbers could be written to :func:`throw` a :exc:`DomainError`
  if the argument is negative:

例外処理は、 :func:`throw` 関数により明示的に作成することができます。例えば、負でない数値に対してのみ定義された関数は、
引数が負の場合に :exc:`DomainError` を返すように :func:`throw` を使用して記述することができます。

.. doctest:: domain-error

    julia> f(x) = x>=0 ? exp(-x) : throw(DomainError())
    f (generic function with 1 method)

    julia> f(1)
    0.36787944117144233

    julia> f(-1)
    ERROR: DomainError:
     in f(::Int64) at ./none:1
     ...

.. 
  Note that :exc:`DomainError` without parentheses is not an exception, but a type of
  exception. It needs to be called to obtain an :exc:`Exception` object:

括弧無しの :exc:`DomainError` は例外処理ではなく、型の例外であることに注意してください。
:exc:`Exception` オブジェクトを取得するには、これを呼び出す必要があります。

.. doctest:: throw-function

    julia> typeof(DomainError()) <: Exception
    true

    julia> typeof(DomainError) <: Exception
    false

.. 
  Additionally, some exception types take one or more arguments that are used for
  error reporting:

さらに、一部の例外処理では、エラー報告に使用される1つ以上の引数を使用します。

.. doctest::

    julia> throw(UndefVarError(:x))
    ERROR: UndefVarError: x not defined
     ...

.. 
  This mechanism can be implemented easily by custom exception types following
  the way :exc:`UndefVarError` is written:

この仕組みは、 :exc:`UndefVarError` が書き込まれた方法に従ったカスタムされた例外処理によって簡単に実装できます。

.. doctest::

    julia> type MyUndefVarError <: Exception
               var::Symbol
           end
    julia> Base.showerror(io::IO, e::MyUndefVarError) = print(io, e.var, " not defined");

.. 
  .. note::
      When writing an error message, it is preferred to make the first word
      lowercase. For example,
      ``size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))``


      is preferred over

      ``size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))``.

      However, sometimes it makes sense to keep the uppercase first letter,
      for instance if an argument to a function is a capital letter:
      ``size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))``.
    
.. note::
    エラーメッセージを書く際には、1文字目は小文字にするべきです。例えば、
    ``size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))``    

    は以下よりも望ましいです。
     
    ``size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))``.
     
    しかし、1文字目を大文字にするほうがよい場合もあります。例えば、関数の引数が大文字の場合です。
    ``size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))``
.. 
  Errors
  ~~~~~~

エラー
~~~~~~

.. 
  The :func:`error` function is used to produce an :exc:`ErrorException` that
  interrupts the normal flow of control.

:func:`error` 関数は、通常の制御フローを中断する :exc:`ErrorException` を生成するために使用されます。

.. 
  Suppose we want to stop execution immediately if the square root of a
  negative number is taken. To do this, we can define a fussy version of
  the :func:`sqrt` function that raises an error if its argument is negative:

負の数の平方根が取られた場合、すぐに実行を停止したいとします。これを行うために、
引数が負の場合にエラーを発生させる煩雑な :func:`sqrt` 関数を定義することができます。

.. doctest::

    julia> fussy_sqrt(x) = x >= 0 ? sqrt(x) : error("negative x not allowed")
    fussy_sqrt (generic function with 1 method)

    julia> fussy_sqrt(2)
    1.4142135623730951

    julia> fussy_sqrt(-1)
    ERROR: negative x not allowed
     in fussy_sqrt(::Int64) at ./none:1
     ...

.. 
  If ``fussy_sqrt`` is called with a negative value from another function,
  instead of trying to continue execution of the calling function, it
  returns immediately, displaying the error message in the interactive
  session:

``fussy_sqrt`` が別の関数から負の値で呼び出された場合、呼び出し元関数の処理を継続しようとするのではなく、
すぐに戻り、対話型セッションでエラーメッセージを表示します。

.. doctest::

    julia> function verbose_fussy_sqrt(x)
             println("before fussy_sqrt")
             r = fussy_sqrt(x)
             println("after fussy_sqrt")
             return r
           end
    verbose_fussy_sqrt (generic function with 1 method)

    julia> verbose_fussy_sqrt(2)
    before fussy_sqrt
    after fussy_sqrt
    1.4142135623730951

    julia> verbose_fussy_sqrt(-1)
    before fussy_sqrt
    ERROR: negative x not allowed
     in fussy_sqrt at ./none:1 [inlined]
     in verbose_fussy_sqrt(::Int64) at ./none:3
     ...

.. 
  Warnings and informational messages
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ワーニングおよびインフォメーションメッセージ
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Julia also provides other functions that write messages to the standard error
  I/O, but do not throw any :exc:`Exception`\ s and hence do not interrupt
  execution:

Juliaは標準エラーI/Oにメッセージを書き込む他の関数も提供しますが、 :exc:`Exception` を返さないため、
処理の実行を中断しません。

.. doctest::

    julia> info("Hi"); 1+1
    INFO: Hi
    2

    julia> warn("Hi"); 1+1
    WARNING: Hi
    2

    julia> error("Hi"); 1+1
    ERROR: Hi
     in error(::String) at ./error.jl:21
     ...


.. 
  The ``try/catch`` statement
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~

``try/catch`` 文
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  The ``try/catch`` statement allows for :exc:`Exception`\ s to be tested for. For
  example, a customized square root function can be written to automatically
  call either the real or complex square root method on demand using
  :exc:`Exception`\ s :

``try/catch`` 文は :exc:`Exception` のテストを可能にします。例えば、カスタマイズされた平方根関数は、
必要に応じて :exc:`Exception` を使って実数または複素平方根のいずれかのメソッドを自動的に呼び出すために使用できます。

.. doctest:: try-catch

    julia> f(x) = try
             sqrt(x)
           catch
             sqrt(complex(x, 0))
           end
    f (generic function with 1 method)

    julia> f(1)
    1.0

    julia> f(-1)
    0.0 + 1.0im

.. 
  It is important to note that in real code computing this function, one would
  compare ``x`` to zero instead of catching an exception. The exception is much
  slower than simply comparing and branching.

この関数を計算する実際のコードでは、例外処理をとるのではなく、
``x`` をゼロと比較することに注意することが重要です。例外処理は、
単純に比較および分岐処理するよりも時間がかかります。

.. 
  ``try/catch`` statements also allow the :exc:`Exception` to be saved in a
  variable. In this contrived example, the following example calculates the
  square root of the second element of ``x`` if ``x`` is indexable, otherwise
  assumes ``x`` is a real number and returns its square root:

``try/catch`` 文は、 :exc:`Exception` を変数に保存することも可能にします。次の例では、
``x`` がインデックス可能な場合は、 ``x`` の2番目の要素の平方根を計算し、
それ以外の場合は ``x`` を実数とみなし、その平方根を返します。

.. doctest::

    julia> sqrt_second(x) = try
             sqrt(x[2])
           catch y
             if isa(y, DomainError)
               sqrt(complex(x[2], 0))
             elseif isa(y, BoundsError)
               sqrt(x)
             end
           end
    sqrt_second (generic function with 1 method)

    julia> sqrt_second([1 4])
    2.0

    julia> sqrt_second([1 -4])
    0.0 + 2.0im

    julia> sqrt_second(9)
    3.0

    julia> sqrt_second(-9)
    ERROR: DomainError:
     in sqrt_second(::Int64) at ./none:7
     ...

.. 
  Note that the symbol following ``catch`` will always be interpreted as a
  name for the exception, so care is needed when writing ``try/catch`` expressions
  on a single line. The following code will *not* work to return the value of ``x``
  in case of an error::

``catch`` の後の文字列は常に例外処理の名前として解釈されるため、 ``try/catch`` 式を1行に書く際には
注意が必要です。次のコードは、エラーが発生した場合に ``x`` の値を返すようには機能しません。::

    try bad() catch x end

..
  Instead, use a semicolon or insert a line break after ``catch``::
  
代わりに、セミコロンを使用するか、 ``catch`` の後に改行を挿入します。::

    try bad() catch; x end

    try bad()
    catch
      x
    end

.. 
  The ``catch`` clause is not strictly necessary; when omitted, the default
  return value is ``nothing``.

``catch`` 文は必ずしも必要ではありません。省略した場合、デフォルトの戻り値は ``nothing`` です。

.. doctest::

    julia> try error() end #Returns nothing

.. 
  The power of the ``try/catch`` construct lies in the ability to unwind a deeply
  nested computation immediately to a much higher level in the stack of calling
  functions. There are situations where no error has occurred, but the ability to
  unwind the stack and pass a value to a higher level is desirable. Julia
  provides the :func:`rethrow`, :func:`backtrace` and :func:`catch_backtrace` functions for
  more advanced error handling.
  
``try/catch`` 構造体の強みは、深くネストされた計算を、呼び出し関数のスタック内のより高いレベルに
すぐに巻き戻す機能にあります。エラーが発生せず、スタックを巻き戻してより高いレベルに値を渡す機能が
望ましい場合があります。Juliaは、より高度なエラー処理のために :func:`rethrow` 、
:func:`backtrace` および :func:`catch_backtrace` 関数を提供しています。  

.. 
  finally Clauses
  ~~~~~~~~~~~~~~~

finally文
~~~~~~~~~~~~~~~

.. 
  In code that performs state changes or uses resources like files, there is
  typically clean-up work (such as closing files) that needs to be done when the
  code is finished. Exceptions potentially complicate this task, since they can
  cause a block of code to exit before reaching its normal end. The ``finally``
  keyword provides a way to run some code when a given block of code exits,
  regardless of how it exits.

状態の変更やファイルのようなリソースを使用するコードでは、コードの終了時に
クリーンアップ作業（ファイルのクローズなど）を行う必要があることが一般的です。
例外処理は、正常終了に達する前にコードのブロックを終了する可能性があるため、
このタスクを複雑にする場合があります。 ``finally`` キーワードでは、コードブロックが
どのように終了するかに関わらず、そのコードブロックが終了したときにコードを実行することができます。

.. 
  For example, here is how we can guarantee that an opened file is closed::
  
例えば、開いているファイルを閉じる方法は次の通りです。::

    f = open("file")
    try
        # operate on file f
    finally
        close(f)
    end

.. 
  When control leaves the ``try`` block (for example due to a ``return``, or
  just finishing normally), ``close(f)`` will be executed. If
  the ``try`` block exits due to an exception, the exception will continue
  propagating. A ``catch`` block may be combined with ``try`` and ``finally``
  as well. In this case the ``finally`` block will run after ``catch`` has
  handled the error.

制御が ``try`` ブロックを離れた場合（例えば ``return`` や正常終了など）、 ``close(f)`` が実行されます。
``try`` ブロックが例外処理のために終了した場合、例外処理は後続への伝達を続けます。
``catch`` ブロックは、 ``try`` や ``finally`` も同様に組み合わせることができます。
この場合、 ``finally`` ブロックは、 ``catch`` がエラーを処理した後に実行されます。

.. _man-tasks:

.. 
  Tasks (aka Coroutines)
  ----------------------

タスク（またはコルーチン）
----------------------

..　
  Tasks are a control flow feature that allows computations to be
  suspended and resumed in a flexible manner. This feature is sometimes
  called by other names, such as symmetric coroutines, lightweight
  threads, cooperative multitasking, or one-shot continuations.

タスクは、柔軟に計算を一時停止したり、再開できるようにする制御フロー機能です。
この機能は、対称コルーチン、軽量スレッド、協調的マルチタスキング、
one-shot continuationなど、他の名前で呼ばれることがあります。

.. 
  When a piece of computing work (in practice, executing a particular
  function) is designated as a :class:`Task`, it becomes possible to interrupt
  it by switching to another :class:`Task`. The original :class:`Task` can later be
  resumed, at which point it will pick up right where it left off. At
  first, this may seem similar to a function call. However there are two
  key differences. First, switching tasks does not use any space, so any
  number of task switches can occur without consuming the call stack.
  Second, switching among tasks can occur in any order, unlike function calls,
  where the called function must finish executing before control returns
  to the calling function.

処理（実際には特定の機能を実行する）が :class:`Task` として指定されると、別の :class:`Task` に切り替えることで
中断することが可能になります。元の :class:`Task` は後で再開することができ、
中断した箇所から再開されます。これは関数呼び出しと同じように見えるかもしれません。
しかし、2つの重要な違いがあります。1つは、タスクの切り替えはスペースを使用しないため、
コールスタックを消費せずに任意の数のタスクスイッチを実行できます。もう1つは、
関数呼び出しとは異なり、タスク間の切り替えは任意の順序で行うことができます。
関数呼び出しの場合、呼び出された関数は呼び出し元の関数に制御が戻る前に実行を終了する必要があります。

.. 
  This kind of control flow can make it much easier to solve certain
  problems. In some problems, the various pieces of required work are not
  naturally related by function calls; there is no obvious "caller" or
  "callee" among the jobs that need to be done. An example is the
  producer-consumer problem, where one complex procedure is generating
  values and another complex procedure is consuming them. The consumer
  cannot simply call a producer function to get a value, because the
  producer may have more values to generate and so might not yet be ready
  to return. With tasks, the producer and consumer can both run as long as
  they need to, passing values back and forth as necessary.

この種の制御フローにより、特定の問題を簡単に解決することができます。あるケースでは、
処理の様々な部分は、関数呼び出しによってお互いに関連するものではなく、
実行するジョブの中に明示的な「呼び出し元」または「呼び出し先」がありません。
一例は、ある複雑な処理が値を生成し、別の複雑な処理がそれらを消費している生産側-消費側の問題です。
生産側は生成する値が多く、返す準備ができていない可能性があるため、
消費側は単に生産側の関数を呼び出して値を得ることはできません。
タスクでは、生産側と消費側は、必要に応じてお互いに値を渡しながら、必要なだけ処理することができます。

.. 
  Julia provides the functions :func:`produce` and :func:`consume` for solving
  this problem. A producer is a function that calls :func:`produce` on each
  value it needs to produce:

Juliaは、この問題を解決するために :func:`produce` および :func:`consume` 関数を提供しています。
生産側は、生成する必要がある各値に対して :func:`produce` を呼び出す関数です。

.. doctest::

    julia> function producer()
             produce("start")
             for n=1:4
               produce(2n)
             end
             produce("stop")
           end;

.. 
  To consume values, first the producer is wrapped in a :class:`Task`,
  then :func:`consume` is called repeatedly on that object:

値を使用するには、まず生産側を :class:`Task` にラッッピングし、そのオブジェクトに対して :func:`consume` を繰り返し呼び出します。

.. doctest::

    julia> p = Task(producer);

    julia> consume(p)
    "start"

    julia> consume(p)
    2

    julia> consume(p)
    4

    julia> consume(p)
    6

    julia> consume(p)
    8

    julia> consume(p)
    "stop"

.. 
  One way to think of this behavior is that ``producer`` was able to
  return multiple times. Between calls to :func:`produce`, the producer's
  execution is suspended and the consumer has control.

この動作を考える方法の1つは、 ``producer`` は何度も処理を返すことができるということです。
:func:`produce` への呼び出しの間、生産側の実行は一時停止され、消費側が制御権を持ちます。

.. 
  A Task can be used as an iterable object in a ``for`` loop, in which
  case the loop variable takes on all the produced values:

タスクは ``for`` ループ内の繰り返し処理可能オブジェクトとして使用できます。
この場合、ループ変数は生成された全ての値を取ります。

.. doctest::

    julia> for x in Task(producer)
             println(x)
           end
    start
    2
    4
    6
    8
    stop

.. 
  Note that the :func:`Task` constructor expects a 0-argument function. A
  common pattern is for the producer to be parameterized, in which case a
  partial function application is needed to create a 0-argument :ref:`anonymous
  function <man-anonymous-functions>`. This can be done either
  directly or by use of a convenience macro::

:func:`Task` コンストラクタは、引数を取らない関数が必要であることに注意してください。
一般的なパターンは、生産側をパラメータ化することです。この場合、引数を取らない :ref:`無名関数 <man-anonymous-functions>` を
作成するには部分関数アプリケーションが必要です。これは、直接または便利なマクロを使用することで行うことができます。::

    function mytask(myarg)
        ...
    end

    taskHdl = Task(() -> mytask(7))
    # or, equivalently
    taskHdl = @task mytask(7)

.. 
  :func:`produce` and :func:`consume` do not launch threads that can run on separate CPUs.
  True kernel threads are discussed under the topic of :ref:`man-parallel-computing`.

:func:`produce` と :func:`consume` は、別々のCPUで実行するスレッドを起動しません。
カーネルスレッドについては、 :ref:`man-並列処理` のトピックで説明します。

.. 
  Core task operations
  ~~~~~~~~~~~~~~~~~~~~

コアタスク処理
~~~~~~~~~~~~~~~~~~~~

.. 
  While :func:`produce` and :func:`consume` illustrate the essential nature of tasks, they
  are actually implemented as library functions using a more primitive function,
  :func:`yieldto`. ``yieldto(task,value)`` suspends the current task, switches
  to the specified ``task``, and causes that task's last :func:`yieldto` call to return
  the specified ``value``. Notice that :func:`yieldto` is the only operation required
  to use task-style control flow; instead of calling and returning we are always
  just switching to a different task. This is why this feature is also called
  "symmetric coroutines"; each task is switched to and from using the same mechanism.

:func:`produce` と :func:`consume` はタスクの本質的な性質を示していますが、実際には、
より基本的な関数 :func:`yieldto` を使用して、ライブラリ関数として実装されています。
``yieldto(task,value)`` は、実行しているタスクの中断、指定されたタスクへの切り替え、
およびそのタスクの最後の :func:`yieldto` 呼び出しで指定された値を返します。 :func:`yieldto` は、
タスクスタイルの制御フローを使用する必要がある唯一の処理であることに注意してください。
呼び出したり戻るのではなく、常に別のタスクに切り替えるだけです。
このため、この機能は「対称コルーチン」とも呼ばれます。各タスクは同じメカニズムを使用して切り替えられます。

.. 
  :func:`yieldto` is powerful, but most uses of tasks do not invoke it directly.
  Consider why this might be. If you switch away from the current task, you will
  probably want to switch back to it at some point, but knowing when to switch
  back, and knowing which task has the responsibility of switching back, can
  require considerable coordination. For example, :func:`produce` needs to maintain
  some state to remember who the consumer is. Not needing to manually keep track
  of the consuming task is what makes :func:`produce` easier to use than :func:`yieldto`.

:func:`yieldto` は強力ですが、タスクの大半は直接呼び出しません。なぜか少し考えてみてください。
現在のタスクを切り替えた場合は、ある時点で元のタスクに戻りたいと思うかもしれませんが、
いつスイッチバックするのかを知り、どのタスクがスイッチバックの責任を持っているのかを知るにはかなりの調整が必要です。
例えば、 :func:`produce` は、消費側が誰であるかを記憶するために、ある状態を維持する必要があります。
消費側タスクを手動で追跡する必要がない分、 :func:`produce` は :func:`yieldto` よりも使いやすくなります。

.. 
  In addition to :func:`yieldto`, a few other basic functions are needed to use tasks
  effectively.

  - :func:`current_task` gets a reference to the currently-running task.
  - :func:`istaskdone` queries whether a task has exited.
  - :func:`istaskstarted` queries whether a task has run yet.
  - :func:`task_local_storage` manipulates a key-value store specific to the current task.

:func:`yieldto` 加えて、タスクを効果的に使用するには、いくつかの基本的な関数が必要です。

- :func:`current_task` は、現在実行中のタスクへの参照を取得します。
- :func:`istaskdone` は、タスクが終了したかどうかを照会します。
- :func:`istaskstarted` は、タスクがまだ実行されているかどうかを照会します。
- :func:`task_local_storage` は、現在のタスクの固有のキー値ストアを操作します。

.. 
  Tasks and events
  ~~~~~~~~~~~~~~~~

タスクとイベント
~~~~~~~~~~~~~~~~

.. 
  Most task switches occur as a result of waiting for events such as I/O
  requests, and are performed by a scheduler included in the standard library.
  The scheduler maintains a queue of runnable tasks, and executes an event loop
  that restarts tasks based on external events such as message arrival.

ほとんどのタスクの切り替えは、I/O要求などのイベントを待機した結果として発生し、
また標準ライブラリに含まれるスケジューラによって実行されます。スケジューラは、
実行可能なタスクのキューを維持し、メッセージの到着などの外部イベントに基づいてタスクを再開するイベントループを実行します。

.. 
  The basic function for waiting for an event is :func:`wait`. Several objects
  implement :func:`wait`; for example, given a :class:`Process` object, :func:`wait` will
  wait for it to exit. :func:`wait` is often implicit; for example, a :func:`wait`
  can happen inside a call to :func:`read` to wait for data to be available.

イベントを待つための基本的な関数は :func:`wait` です。いくつかのオブジェクトは :func:`wait` を実装しています。
例えば、 :class:`Process` オブジェクトでは :func:`wait` オブジェクトが終了するまで待機します。
:func:`wait` はしばしば潜在的です。例えば、 :func:`wait` は :func:`read` の呼び出しの中でデータが
利用可能になるのを待つことができます。

.. 
  In all of these cases, :func:`wait` ultimately operates on a :class:`Condition`
  object, which is in charge of queueing and restarting tasks. When a task
  calls :func:`wait` on a :class:`Condition`, the task is marked as non-runnable, added
  to the condition's queue, and switches to the scheduler. The scheduler will
  then pick another task to run, or block waiting for external events.
  If all goes well, eventually an event handler will call :func:`notify` on the
  condition, which causes tasks waiting for that condition to become runnable
  again.

これらの全ての場合、 :func:`wait` は最終的にタスクのキューイングと再開を管理する :class:`Condition` オブジェクトで動作します。
タスクが :class:`Condition` で :func:`wait` を呼び出すと、タスクは実行不可能とマークされ、条件のキューに追加され、
スケジューラに切り替わります。スケジューラは、実行する別のタスクを選択するか、外部イベントの待機をブロックします。
全てがうまくいった場合、最終的にイベントハンドラはその条件で :func:`notify` を呼び出し、その条件を待っているタスクを再度実行可能にします。

.. 
  A task created explicitly by calling :class:`Task` is initially not known to the
  scheduler. This allows you to manage tasks manually using :func:`yieldto` if
  you wish. However, when such a task waits for an event, it still gets restarted
  automatically when the event happens, as you would expect. It is also
  possible to make the scheduler run a task whenever it can, without necessarily
  waiting for any events. This is done by calling :func:`schedule`, or using
  the :obj:`@schedule` or :obj:`@async` macros (see :ref:`man-parallel-computing` for
  more details).

:class:`Task` を呼び出すことによって明示的に作成されたタスクは、最初はスケジューラには認識されません。
これにより、必要に応じて :func:`yieldto` を使用して手動でタスクを管理することができます。
しかし、このようなタスクがイベントを待っているときは、イベントが発生したときに自動的に再起動されます。
また、イベントを待つことなく、可能な場合にスケジューラでタスクを実行させることも可能です。
これは、 :func:`schedule` を呼び出すか :obj:`@schedule` マクロまたは :obj:`@async` マクロを
使用して行うことができます（詳細は :ref:`man-並列処理` を参照）。

.. 
  Task states
  ~~~~~~~~~~~

タスクの状態
~~~~~~~~~~~

.. 
  Tasks have a ``state`` field that describes their execution status. A task
  state is one of the following symbols:

タスクには、実行状態を示す「state」フィールドがあります。タスクの状態は、次のシンボルのいずれかです。

.. 
  =============  ==================================================
  Symbol         Meaning
  =============  ==================================================
  ``:runnable``  Currently running, or available to be switched to
  ``:waiting``   Blocked waiting for a specific event
  ``:queued``    In the scheduler's run queue about to be restarted
  ``:done``      Successfully finished executing
  ``:failed``    Finished with an uncaught exception
  =============  ==================================================

=============  ==================================================
シンボル        概要
=============  ==================================================
``:runnable``  実行中、または切り替え可能
``:waiting``   特定のイベントを待つためブロックされた
``:queued``    スケジューラの実行キュー内で再開されようとしている
``:done``      正常終了
``:failed``    非定義の例外で終了
=============  ==================================================
