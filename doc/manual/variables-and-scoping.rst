.. _man-variables-and-scoping:

.. currentmodule:: Base

.. 
 ********************
  Scope of Variables
 ********************

********************
 変数のスコープ
********************

.. 
 The *scope* of a variable is the region of code within which a
 variable is visible. Variable scoping helps avoid variable naming
 conflicts. The concept is intuitive: two functions can both have
 arguments called ``x`` without the two ``x``'s referring to the same
 thing. Similarly there are many other cases where different blocks of
 code can use the same name without referring to the same thing. The
 rules for when the same variable name does or doesn't refer to the
 same thing are called scope rules; this section spells them out in
 detail.

変数のスコープは、変数が表示されるコードの範囲です。変数のスコープは、変数名の競合を避けるのに役立ちます。
この概念は直感的です。例えば、2つの関数は、対象の引数が同じものを参照する場合を除いて、
どちらも ``x`` という引数を持つことができます。同様に、異なるコードブロックが同じものを参照せずに
同じ名前を使用できるケースがあります。同じ変数名が同じものを参照する・しないの規則は、
スコープ規則と呼ばれます。これはこのセクションで詳しく説明します。

.. 
 Certain constructs in the language introduce *scope blocks*, which are
 regions of code that are eligible to be the scope of some set of
 variables. The scope of a variable cannot be an arbitrary set of
 source lines; instead, it will always line up with one of these
 blocks.  There are two main types of scopes in Julia, *global scope*
 and *local scope*, the latter can be nested.  The constructs
 introducing scope blocks are:
 
言語内の特定の構文はスコープブロックを提示し、スコープブロックは一連の変数のスコープの範囲を示すことができます。
変数のスコープは任意のソース行の集まりとすることはできず、これらのブロックのいずれかと常に並びます。
Juliaにはグローバルスコープとローカルスコープの2つの主要なスコープがあり、
後者はネストすることができます。スコープブロックを提示する構文は次のとおりです。

.. _man-scope-table:

.. 
 +--------------------------------+----------------------------------------------------------------------------------+
 | Scope name                     | block/construct introducing this kind of scope                                   |
 +================================+==================================================================================+
 | :ref:`global <man-global>`     | module, baremodule, at interactive prompt (REPL)                                 |
 +--------------------------------+------------------------------+---------------------------------------------------+
 | :ref:`local <man-local-scope>` | :ref:`soft <man-soft-scope>` | for, while, comprehensions,                       |
 |                                |                              | try-catch-finally, let                            |
 |                                +------------------------------+---------------------------------------------------+
 |                                | :ref:`hard <man-hard-scope>` | functions (either syntax, anonymous & do-blocks), |
 |                                |                              | type, immutable, macro                            |
 +--------------------------------+------------------------------+---------------------------------------------------+

+--------------------------------+----------------------------------------------------------------------------------+
| スコープ名                       | この種のスコープを提示する構文                                                        |
+================================+==================================================================================+
| :ref:`global <man-global>`     | モジュール、ベアモジュール、インタラクティブプロンプト（REPL）                             |
+--------------------------------+------------------------------+---------------------------------------------------+
| :ref:`local <man-local-scope>` | :ref:`soft <man-soft-scope>` | for、while、comprehensions、                       |
|                                |                              | try-catch-finally、let                            |
|                                +------------------------------+---------------------------------------------------+
|                                | :ref:`hard <man-hard-scope>` | 関数（構文、無名、またはdoブロック）、                  |
|                                |                              | type、immutable、マクロ                             |
+--------------------------------+------------------------------+---------------------------------------------------+

.. 
 Notably missing from this table are :ref:`begin blocks
 <man-compound-expressions>` and :ref:`if blocks
 <man-conditional-evaluation>`, which do *not* introduce new scope
 blocks.  All three types of scopes follow somewhat different rules
 which will be explained below as well as some extra rules for
 certain blocks.

注目すべきことは、新しいスコープブロックを導入しない :ref:`begin ブロック <man-compound-expressions>` と
:ref:`if ブロック <man-conditional-evaluation>` が上記に無い点です。
以下で説明するように、3つのスコープは全て、異なるいくつかのルールに従います。

.. 
 Julia uses `lexical scoping <https://en.wikipedia.org/wiki/Scope_%28computer_science%29#Lexical_scoping_vs._dynamic_scoping>`_,
 meaning that a function's scope does not inherit from its caller's
 scope, but from the scope in which the function was defined.
 For example, in the following code the ``x`` inside ``foo`` refers
 to the ``x`` in the global scope of its module ``Bar``::

Juliaは、関数のスコープは呼び出し元のスコープから継承されず、関数が定義されたスコープから継承される
`レキシカルスコープ <https://en.wikipedia.org/wiki/Scope_%28computer_science%29#Lexical_scoping_vs._dynamic_scoping>`_ を
使用しています。例えば、次のコードでは、 ``foo`` 内の ``x`` はモジュール ``Bar`` のグローバルスコープ内の ``x`` を参照します。::

    module Bar
    x = 1
    foo() = x
    end

.. 
 and not a ``x`` in the scope where ``foo`` is used::
 
``foo`` が使用されているスコープ内の ``x`` ではない場合、::

    julia> import Bar

    julia> x = -1;

    julia> Bar.foo()
    1

.. 
 Thus *lexical scope* means that the scope of variables can be inferred
 from the source code alone.

従って、レキシカルスコープは、変数のスコープがソースコードだけから推測できることを意味します。

.. _man-global:

.. 
 Global Scope
 ------------


グローバルスコープ
------------

.. 
 *Each module introduces a new global scope*, separate from the global
 scope of all other modules; there is no all-encompassing global scope.
 Modules can introduce variables of other modules into their scope
 through the :ref:`using or import <man-modules>` statements or through
 qualified access using the dot-notation, i.e. each module is a
 so-called *namespace*.  Note that variable bindings can only be
 changed within their global scope and not from an outside module. ::

各モジュールは、他の全てのモジュールのグローバルスコープとは別の新しいグローバルスコープを提示します。
全てを包括するグローバルスコープは存在しません。モジュールは、 :ref:`using または import <man-modules>` 
ステートメントを通じて、またはドット表記を使用した条件付きのアクセスを通じて、
他のモジュールの変数をそのスコープに提示できます。各モジュールはいわゆる名前用のスペースです。
変数のバインディングは、グローバルスコープ内でのみ変更でき、外部モジュールでは変更できないことに注意してください。::

    module A
    a = 1 # a global in A's scope
    end

    module B
    # b = a # would error as B's global scope is separate from A's
        module C
        c = 2
        end
    b = C.c # can access the namespace of a nested global scope
            # through a qualified access
    import A # makes module A available
    d = A.a
    # A.a = 2 # would error with: "ERROR: cannot assign variables in other modules"
    end

.. 
 Note that the interactive prompt (aka REPL) is in the global scope of
 the module ``Main``.
 
対話型プロンプト（別名REPL）はモジュール ``Main`` のグローバルスコープ内にあることに注意してください。

.. _man-local-scope:

.. 
 Local Scope
 -----------

ローカルスコープ
-----------

.. 
 A new local scope is introduced by most code-blocks, see above
 :ref:`table <man-scope-table>` for a complete list.  A local scope
 *usually* inherits all the variables from its parent scope, both for
 reading and writing.  There are two subtypes of local scopes, hard and
 soft, with slightly different rules concerning what variables are
 inherited.  Unlike global scopes, local scopes are not namespaces,
 thus variables in an inner scope cannot be retrieved from the parent
 scope through some sort of qualified access.

新しいローカルスコープは、ほとんどのコードブロックで提示されます。詳細は上記の :ref:`table <man-scope-table>` を参照してください。
ローカルスコープは、通常親スコープの全ての変数を読み書き用に継承します。
ローカルスコープには、ハードとソフトの2つのサブタイプがあり、どの変数が継承されているかについて
わずかに異なるルールがあります。グローバルスコープとは異なり、ローカルスコープは名前用のスペースではないため、
内部スコープ内の変数は条件付きアクセスによって親スコープから取り出すことはできません。

.. 
 The following rules and examples pertain to both hard and soft local
 scopes.  A newly introduced variable in a local scope does not
 back-propagate to its parent scope.  For example, here the ``z`` is not
 introduced into the top-level scope::

次のルールと例は、ハードローカルスコープとソフトローカルスコープの両方に当てはまります。
ローカルスコープ内に新しく提示された変数は、その親スコープに逆方向に伝達されません。
例えば、 ``z`` は最上位のスコープに提示されません。::

    for i=1:10
        z = i
    end

    julia> z
    ERROR: UndefVarError: z not defined

.. 
 (Note, in this and all following examples it is assumed that their
 top-level is a global scope with a clean workspace, for instance a
 newly started REPL.)

この例と次の全ての例では、最上位がクリーンな作業領域を持つグローバルスコープであること想定しています
（例えば、新しく起動されたREPL）。

.. 
 Inside a local scope a variable can be forced to be a local variable
 using the ``local`` keyword::

ローカルスコープの内部では、変数は ``local`` キーワードを使用して強制的にローカル変数にすることができます。::

    x = 0
    for i=1:10
        local x
        x = i + 1
    end

    julia> x
    0

.. 
 Inside a local scope a new global variable can be defined using the
 keyword ``global``::

ローカルスコープの内部では、 ``global`` グローバル変数を使用して新しいグローバル変数を定義できます。::

    for i=1:10
        global z
        z = i
    end

    julia> z
    10

..
   However, there is no keyword to introduce a new local variable into a
   parent local scope.

..
  The location of both the ``local`` and ``global`` keywords within the
  scope block is irrelevant.  The following is equivalent to the last
  example (although stylistically worse)::
  
スコープブロック内の ``local`` キーワードと ``global`` キーワードの両方の位置は重要ではありません。
見た目は一部異なりますが、以下は最後の例と同じです。::

    for i=1:10
        z = i
        global z
    end

    julia> z
    10

.. 
  Multiple global or local definitions can be on one line and can also
  be paired with assignments::

複数のグローバル定義またはローカル定義を1行に含めることもできますし、割り当てとペアにすることもできます。::

    for i=1:10
        global x=i, y, z
        local a=4, b , c=1
    end


.. _man-soft-scope:

.. 
  Soft Local Scope
  ^^^^^^^^^^^^^^^^

ソフトローカルスコープ
^^^^^^^^^^^^^^^^

.. 
  In a soft local scope, all variables are inherited from its parent
  scope unless a variable is specifically marked with the keyword
  ``local``.
  
ソフトローカルスコープでは、変数に特に ``local`` キーワードでマークされていない限り、全ての変数は親スコープから継承されます。

.. 
  Soft local scopes are introduced by for-loops, while-loops,
  comprehensions, try-catch-finally-blocks, and let-blocks.  There
  are some extra rules for :ref:`let-blocks <man-let-blocks>` and for
  :ref:`for-loops and comprehensions <man-for-loops-scope>`.

ソフトループスコープは、for-loops、while-loops、comprehensions、try-catch-finally-block、
およびlet-blocksによって提示されます。 :ref:`let-blocks <man-let-blocks>` と
:ref:`for-loops および comprehensions <man-for-loops-scope>` には
いくつかの特別なルールがあります。

.. 
  In the following example the ``x`` and ``y`` refer always to the same
  variables as the soft local scope inherits both read and write
  variables::

次の例では、 ``x`` と ``y`` は常にソフトローカルスコープと同じ変数を参照し、読み取りと書き込みの両方の変数を継承します。::

    x,y = 0, 1
    for i = 1:10
        x = i + y + 1
    end

    julia> x
    12

.. 
  Within soft scopes, the `global` keyword is never necessary, although
  allowed.  The only case when it would change the semantics is
  (currently) a syntax error::

ソフトスコープ内では、 `global` キーワードは決して必須ではありませんが、使用することができます。
セマンティックを変更する唯一のケースは、（現在時点では）構文エラーのみです。::

    let
        local x = 2
        let
            global x = 3
        end
    end

    # ERROR: syntax: `global x`: x is local variable in the enclosing scope

.. _man-hard-scope:

.. 
  Hard Local Scope
  ^^^^^^^^^^^^^^^^

ハードローカルスコープ
^^^^^^^^^^^^^^^^

.. 
  Hard local scopes are introduced by function definitions (in all their
  forms), type & immutable-blocks, and macro-definitions.

   In a hard local scope, all variables are inherited from its parent
   scope unless:

   - an assignment would result in a modified *global* variable, or
   - a variable is specifically marked with the keyword ``local``.
   
ハードローカルスコープは、関数定義（全ての形式）、型および不変ブロック、およびマクロ定義によって提示されます。

ハードローカルスコープでは、次の場合を除き、全ての変数は親スコープから継承されます。
 
- 割り当てによってグローバル変数が変更される
- 変数に ``local`` キーワードが付いている

.. 
  Thus global variables are only inherited for reading but not for
  writing::

したがって、グローバル変数は、読み込みのためだけに継承され、書き込みのためには継承されません。::

    x,y = 1,2
    function foo()
        x = 2 # assignment introduces a new local
        return x + y # y refers to the global
    end

    julia> foo()
    4

    julia> x
    1

.. 
  An explicit ``global`` is needed to assign to a global variable::
  
グローバル変数に割り当てるには、明示的な ``global`` が必要です。::

    x = 1
    function foo()
        global x = 2
    end
    foo()

    julia> x
    2

.. 
  Note that *nested functions* can behave differently to functions
  defined in the global scope as they can modify their parent scope's
  *local* variables::

ネストされた関数は、親スコープのローカル変数を変更できるため、
グローバルスコープで定義された関数とは動作が異なることに注意してください。::

    x,y = 1,2
    function foo()
        x = 2 # introduces a new local
        function bar()
            x = 10 # modifies the parent's x
            return x+y # y is global
        end
        return bar() + x # 12 + 10 (x is modified in call of bar())
    end

    julia> foo()
    22  # (x,y unchanged)

.. 
  The distinction between inheriting global and local variables for
  assignment can lead to some slight differences between functions
  defined in local vs. global scopes.  Consider the modification of the
  last example by moving ``bar`` to the global scope::

割り当てにおけるグローバル変数とローカル変数の継承の違いは、ローカルスコープと
グローバルスコープとで定義された関数の若干の違いを生むことがあります。
最後の例について、 ``bar`` をグローバルスコープに移動する変更を見て見ましょう。::

    x,y = 1,2
    function bar()
        x = 10 # local
        return x+y
    end
    function foo()
        x = 2 # local
        return bar() + x # 12 + 2 (x is not modified)
    end

    julia> foo()
    14 # as x is not modified anymore.
       # (x,y unchanged)

.. 
  Note that above subtlety does not pertain to type and macro
  definitions as they can only appear at the global scope.
  There are special scoping rules concerning the evaluation of default
  and keyword function arguments which are described in the
  :ref:`Function section <man-evaluation-scope-default-values>`.

型およびマクロの定義はグローバルスコープにのみ現れるため、上記の細部はそれらの定義に当てはまりません。
:ref:`関数セクション <man-evaluation-scope-default-values>` で説明されているように、
デフォルトおよびキーワード関数の引数の評価に関する特別なスコープのルールがあります。

.. 
  An assignment introducing a variable used inside a function, type or
  macro definition need not come before its inner usage:

関数、型、またはマクロ定義内で使用される変数を提示する代入は、その内部の使用の前に定義必要はありません。

.. doctest::

    julia> f = y -> x + y
    (::#1) (generic function with 1 method)

    julia> f(3)
    ERROR: UndefVarError: x not defined
     in (::##1#2)(::Int64) at ./none:1
     ...

    julia> x = 1
    1

    julia> f(3)
    4

.. 
  This behavior may seem slightly odd for a normal variable, but allows
  for named functions — which are just normal variables holding function
  objects — to be used before they are defined. This allows functions to
  be defined in whatever order is intuitive and convenient, rather than
  forcing bottom up ordering or requiring forward declarations, as long
  as they are defined by the time they are actually called.  As an
  example, here is an inefficient, mutually recursive way to test if
  positive integers are even or odd::

この動作は、通常の変数では少し奇妙に見えるかもしれませんが、関数オブジェクトを持つ通常の変数である、
名前付き関数をそれが定義される前に使用することができます。これにより、
実際に呼び出されたタイミングで定義されている限り、ボトムアップの順序付けや事前宣言が必要なく、
直感的で便利な順序で関数を定義できます。例として、正の整数が偶数か奇数かをテストする非効率的な、
相互再帰的な方法を次に示します。::

    even(n) = n == 0 ? true  :  odd(n-1)
    odd(n)  = n == 0 ? false : even(n-1)

    julia> even(3)
    false

    julia> odd(3)
    true

.. 
  Julia provides built-in, efficient functions to test for oddness and evenness
  called :func:`iseven` and :func:`isodd` so the above definitions should only be
  taken as examples.

Juliaは、 :func:`iseven` と :func:`isodd` という奇数か偶数かをテストするためのビルトインの
効率的な関数を提供しているため、上記は例として考えてください。

.. 
  Hard vs. Soft Local Scope
  ^^^^^^^^^^^^^^^^^^^^^^^^^

ハードローカルスコープvsソフトローカルスコープ
^^^^^^^^^^^^^^^^^^^^^^^^^

.. 
  Blocks which introduce a soft local scope, such as loops, are
  generally used to manipulate the variables in their parent scope.
  Thus their default is to fully access all variables in their parent
  scope.

ループなどのソフトローカルスコープを提示するブロックは、通常親スコープ内の変数を操作するために使用されます。
従って、デフォルトでは親スコープ内の全ての変数にアクセスすることができます。

.. 
  Conversely, the code inside blocks which introduce a hard local scope
  (function, type, and macro definitions) can be executed at any place in
  a program.  Remotely changing the state of global variables in other
  modules should be done with care and thus this is an opt-in feature
  requiring the ``global`` keyword.
  
一方で、ハードローカルスコープ（関数、型、およびマクロ定義）を提示するブロック内のコードは、
プログラム内の任意の場所で実行することができます。他のモジュールでグローバル変数の状態を
遠隔で変更する場合は注意が必要です。これは ``global`` キーワードを必要とするオプトイン機能です。  

.. 
  The reason to allow *modifying local* variables of parent scopes in
  nested functions is to allow constructing `closures
  <https://en.wikipedia.org/wiki/Closure_%28computer_programming%29>`_
  which have a private state, for instance the ``state`` variable in the
  following example::

ネストされた関数で親スコープのローカル変数を変更できるようにする理由は、
プライベート状態を持つ `クロージャ 
<https://en.wikipedia.org/wiki/Closure_%28computer_programming%29>`_ を作成できるようにするためです。
例えば、次の例の「state」変数です。::

    let
        state = 0
        global counter
        counter() = state += 1
    end

    julia> counter()
    1

    julia> counter()
    2

.. 
  See also the closures in the examples in the next two sections.
  
次の2つのセクションの例のクロージャも参照してください。  

.. _man-let-blocks:

.. 
  Let Blocks
  ^^^^^^^^^^

letブロック
^^^^^^^^^^

.. 
  Unlike assignments to local variables, ``let`` statements allocate new
  variable bindings each time they run. An assignment modifies an
  existing value location, and ``let`` creates new locations. This
  difference is usually not important, and is only detectable in the
  case of variables that outlive their scope via closures. The ``let``
  syntax accepts a comma-separated series of assignments and variable
  names::

ローカル変数への代入とは異なり、 ``let`` 文は実行するたびに新しい変数のバインディングを割り当てます。
割り当てによって既存の値の場所が変更され、 ``let`` によって新しい場所が作成されます。
この違いは通常は重要ではなく、クロージャによって想定以上に長く存在する変数がある場合にのみ発見することが可能です。
``let`` 構文は、カンマで区切られた一連の代入と変数名を受け入れます。::

    let var1 = value1, var2, var3 = value3
        code
    end

.. 
  The assignments are evaluated in order, with each right-hand side
  evaluated in the scope before the new variable on the left-hand side
  has been introduced. Therefore it makes sense to write something like
  ``let x = x`` since the two ``x`` variables are distinct and have separate
  storage. Here is an example where the behavior of ``let`` is needed::

代入は、左辺の新しい変数が提示される前に右辺がスコープ内で順番に評価されます。したがって、
2つの　``x`` 変数が区別され、別々の記憶域を持つため、　``let x = x`` のような構文を書くことは理にかなっています。
``let`` の動作が必要な場合の例を次に示します。::

    Fs = Array{Any}(2)
    i = 1
    while i <= 2
        Fs[i] = ()->i
        i += 1
    end

    julia> Fs[1]()
    3

    julia> Fs[2]()
    3

.. 
  Here we create and store two closures that return variable ``i``.
  However, it is always the same variable ``i``, so the two closures
  behave identically. We can use ``let`` to create a new binding for
  ``i``::

ここでは、変数「i」を返す2つのクロージャを作成して格納しています。しかし、これらは同じ変数 ``i`` であるため、
2つのクロージャは同じように動作します。そこで ``let`` を使って ``i`` の新しいバインディングを作成することができます。::

    Fs = Array{Any}(2)
    i = 1
    while i <= 2
        let i = i
            Fs[i] = ()->i
        end
        i += 1
    end

    julia> Fs[1]()
    1

    julia> Fs[2]()
    2

.. 
  Since the ``begin`` construct does not introduce a new scope, it can be
  useful to use a zero-argument ``let`` to just introduce a new scope
  block without creating any new bindings:

``begin`` 構造体は新しいスコープを提示しないため、新しいバインディングを作成せずに
新しいスコープブロックを提示する引数のない ``let`` を使用すると便利です。

.. doctest::

    julia> let
               local x = 1
               let
                   local x = 2
               end
               x
           end
    1

.. 
  Since ``let`` introduces a new scope block, the inner local ``x``
  is a different variable than the outer local ``x``.

``let`` は新しいスコープブロックを提示するため、内部ローカル ``x`` は外部ローカル ``x`` とは異なる変数となります。

.. _man-for-loops-scope:

.. 
  For Loops and Comprehensions
  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^

forループとコンプリへンション(comprehension)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. 
  ``for`` loops and :ref:`comprehensions <comprehensions>` have the
  following behavior: any new variables introduced in their body scopes
  are freshly allocated for each loop iteration.  This is in contrast to
  ``while`` loops which reuse the variables for all
  iterations. Therefore these constructs are similar to ``while`` loops
  with ``let`` blocks inside::

``for`` ループと「コンプリへンション(comprehension)」には、ボディスコープに提示された新しい変数は、
ループの繰り返し処理ごとに新たに割り当てる機能があります。これは、全ての繰り返し処理で変数を
再利用する ``while`` ループとは対照的です。従って、これらの構造体は ``let`` ブロックを内部に持つ ``while`` ループと似ています。::

    Fs = Array{Any}(2)
    for i = 1:2
        Fs[i] = ()->i
    end

    julia> Fs[1]()
    1

    julia> Fs[2]()
    2

.. 
  ``for`` loops will reuse existing variables for its iteration variable::

``for`` ループは、繰り返し処理用の変数に既存の変数を再利用します。::

    i = 0
    for i = 1:3
    end
    i  # here equal to 3

.. 
  However, comprehensions do not do this, and always freshly allocate their
  iteration variables::

しかし、コンプリへンション(comprehension)はこれを行わず、いつも新しく繰り返し処理用の変数を割り当てます。::

    x = 0
    [ x for x=1:3 ]
    x  # here still equal to 0

.. 
  Constants
  ---------

定数
---------

.. 
  A common use of variables is giving names to specific, unchanging
  values. Such variables are only assigned once. This intent can be
  conveyed to the compiler using the ``const`` keyword::

変数の一般的な使用は、特定の変更されない値に名前を付けることです。そのような変数は1度だけ割り当てられます。
この目的は、 ``const`` キーワードを使用してコンパイラに伝えることができます。::

    const e  = 2.71828182845904523536
    const pi = 3.14159265358979323846

.. 
  The ``const`` declaration is allowed on both global and local variables,
  but is especially useful for globals. It is difficult for the compiler
  to optimize code involving global variables, since their values (or even
  their types) might change at almost any time. If a global variable will
  not change, adding a ``const`` declaration solves this performance
  problem.

``const`` の宣言は、グローバル変数とローカル変数の両方で可能ですが、特にグローバルに便利です。
グローバル変数の値（または型）はほとんどいつでも変更される可能性があるため、
コンパイラはグローバル変数を含むコードを最適化することは難しいです。グローバル変数が変更されない場合、
``const`` 宣言を追加すると、このパフォーマンスの問題を解決することができます。

.. 
  Local constants are quite different. The compiler is able to determine
  automatically when a local variable is constant, so local constant
  declarations are not necessary for performance purposes.

ローカル定数は異なっています。コンパイラは、ローカル変数が定数の場合は自動的に認識することができるため、
パフォーマンス上の目的でローカル定数の宣言は必要ありません。

.. 
  Special top-level assignments, such as those performed by the
  ``function`` and ``type`` keywords, are constant by default.

``function`` と ``type`` キーワードによって実行されるような特別なトップレベルの割り当ては、
デフォルトで定数です。

.. 
  Note that ``const`` only affects the variable binding; the variable may
  be bound to a mutable object (such as an array), and that object may
  still be modified.
  
``const`` は変数のバインディングにのみ影響することに注意してください。変数は配列などの変更可能な
オブジェクトにバインドされており、そのオブジェクトは変更されている可能性があります。
