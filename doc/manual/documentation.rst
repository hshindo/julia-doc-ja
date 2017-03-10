.. _man-documentation:

.. 
 ***************
  Documentation
 ***************

******************
 ドキュメンテーション
******************

.. 
 Julia enables package developers and users to document functions, types and
 other objects easily via a built-in documentation system since Julia 0.4.

Juliaは、パッケージ開発者やユーザーがJulia 0.4以降のビルトインドキュメントシステムを使用して、
関数、型およびその他のオブジェクトを簡単にドキュメント化することができます。

.. 
  .. tip::

    This documentation system can also be used in Julia 0.3 via the
    `Docile.jl <https://github.com/MichaelHatherly/Docile.jl>`_ package; see
    the documentation for that package for more details.


.. tip::

このドキュメントシステムは、Julia 0.3で `Docile.jl <https://github.com/MichaelHatherly/Docile.jl>`_ 
パッケージで使用できます。詳細については、パッケージの説明を参照してください。

.. 
 The basic syntax is very simple: any string appearing at the top-level right
 before an object (function, macro, type or instance) will be interpreted as
 documenting it (these are called *docstrings*). Here is a very simple example:

基本的な構文はとても単純です。オブジェクト（関数、マクロ、型、またはインスタンス）の直前に最上位に記述される文字列は、
それらをドキュメント化すると解釈されます（これらはdocstringと呼ばれています）。以下は簡単な例です。:

.. doctest::

    "Tell whether there are too foo items in the array."
    foo(xs::Array) = ...

.. 
 Documentation is interpreted as `Markdown <https://en.wikipedia.org/wiki/Markdown>`_,
 so you can use indentation and code fences to delimit code examples from text.
 Technically, any object can be associated with any other as metadata;
 Markdown happens to be the default, but one can construct other string
 macros and pass them to the ``@doc`` macro just as well.

ドキュメントは `Markdown <https://en.wikipedia.org/wiki/Markdown>`_ として解釈されるため、
インデントとコードフェンスを使用してテキストのコード例を区切ることができます。
技術的には、どのオブジェクトも他のメタデータと関連付けることができます。
マークダウンはデフォルトとなりますが、他の文字列マクロを作成して ``@doc`` マクロに渡すこともできます。

.. 
 Here is a more complex example, still using Markdown:

以下はMarkdownを使用した複雑な例です。:

.. code-block:: julia

    """
        bar(x[, y])

    Compute the Bar index between `x` and `y`. If `y` is missing, compute
    the Bar index between all pairs of columns of `x`.

    # Examples
    ```julia
    julia> bar([1, 2], [1, 2])
    1
    ```
    """
    function bar(x, y) ...

.. 
 As in the example above, we recommend following some simple conventions when writing
 documentation:

上記の例のように、ドキュメントを書くときのいくつかの簡単な慣習に沿うことをお勧めします。:

.. 
 1. Always show the signature of a function at the top of the documentation,
    with a four-space indent so that it is printed as Julia code.

    This can be identical to the signature present in the Julia code
    (like ``mean(x::AbstractArray)``), or a simplified form.
    Optional arguments should be represented with their default values (i.e. ``f(x, y=1)``)
    when possible, following the actual Julia syntax. Optional arguments which
    do not have a default value should be put in brackets (i.e. ``f(x[, y])`` and
    ``f(x[, y[, z]])``). An alternative solution is to use several lines: one without
    optional arguments, the other(s) with them. This solution can also be used to document
    several related methods of a given function. When a function accepts many keyword
    arguments, only include a ``<keyword arguments>`` placeholder in the signature (i.e.
    ``f(x; <keyword arguments>)``), and give the complete list under an ``# Arguments``
    section (see point 4 below).

1. 文書の上部には、関数のシグネチャを常に表示し、4桁のインデントをつけてJuliaコードとして表示します。
   
   これは、Juliaコード（ ``mean(x::AbstractArray)`` など）、または簡略化されたフォームのシグネチャと同一であると言えます。
   可能な場合は、実際のJulia構文に従い、オプションの引数をデフォルト値（例えば ``f(x, y=1)`` ）で表す必要があります。
   デフォルト値を持たないオプションの引数は角括弧（ ``f(x[, y])`` や ``f(x[, y[, z]])`` など）で囲んでください。
   別の解決方法は、複数の行を使用することで、オプション引数を持たない行と、それ以外の行を記述する方法です。
   この方法は、関数の関連する複数のメソッドをドキュメント化する際にも使用できます。関数が多くのキーワード引数を許容する場合、
   シグネチャ内に ``<keyword arguments>`` プレースホルダ（ ``f(x; <keyword arguments>)`` など）を記述し、
   ``# Arguments`` セクション配下に完全なリストを与える必要があります（4点目を参照）。

.. 
 2. Include a single one-line sentence describing what the function does or what the
    object represents after the simplified signature block. If needed, provide more details
    in a second paragraph, after a blank line.

    The one-line sentence should use the imperative form ("Do this", "Return that") instead
    of the third person (do not write "Returns the length...") when documenting functions.
    It should end with a period. If the meaning of a function cannot be summarized easily,
    splitting it into separate composable parts could be beneficial (this should not be
    taken as an absolute requirement for every single case though).

2. 簡略化されたシグネチャブロックの後に、関数が何を表すか、またはオブジェクトが表すものを記述する1行の文を含めてください。
   必要に応じて、空白行の後の2段落目に詳細を入力してください。
   
   関数をドキュメント化する場合は、1行文は、三人称（Returns the length…のような文）ではなく、
   命令形（Do thisやReturn thatなどの形）で記述する必要があります。そして文はピリオドで終わる必要があります。
   関数の意味を簡単に要約することができない場合、別々の構成可能なパーツに分割することができます
   （ただし、個別のケースごとに絶対的な要件とはみなされません）。

.. 
 3. Do not repeat yourself.

    Since the function name is given by the signature, there is no need to
    start the documentation with "The function ``bar``...": go straight to the point.
    Similarly, if the signature specifies the types of the arguments, mentioning them
    in the description is redundant.

3. 繰り返しをしないようにしてください。
  
   関数名はシグネチャによって与えられるため、 "The function ``bar``..." のようにドキュメントを開始する必要はありません。
   必要なことだけを記述してください。同様に、シグネチャが引数の型を指定している場合、それらを概要に記述することは冗長的です。 

.. 
 4. Only provide an argument list when really necessary.

    For simple functions, it is often clearer to mention the role of the arguments directly
    in the description of the function's purpose. An argument list would only repeat
    information already provided elsewhere. However, providing an argument list can be a good
    idea for complex functions with many arguments (in particular keyword arguments).
    In that case, insert it after the general description of the function, under
    an ``# Arguments`` header, with one ``*`` bullet for each argument. The list should
    mention the types and default values (if any) of the arguments::

4. 本当に必要なときにのみ引数リストを与えてください。
    
   単純な関数の場合、関数の目的の記述の中で、引数の役割について直接言及することはしばしば便利です。
   引数リストは既に他の場所で提供されている情報を繰り返すだけです。しかし、引数リストを提供することは、
   多くの引数（特にキーワード引数）を持つ複雑な関数に対しては有効です。その場合は、関数の一般的な説明の後に
   ``# Arguments`` ヘッダの下に、各引数に1つの「*」付きで挿入します。リストには、引数の型とデフォルト値（存在する場合）が
   記載されている必要があります。::
   
       """
       ...
       # Arguments
       * `n::Integer`: the number of elements to compute.
       * `dim::Integer=1`: the dimensions along which to perform the computation.
       ...
       """

.. 
 5. Include any code examples in an ``# Examples`` section.

    Examples should, whenever possible, be written as *doctests*. A *doctest* is a fenced
    code block (see `Code blocks`_) starting with `````jldoctest`` and contains any number of
    ``julia>`` prompts together with inputs and expected outputs that mimic the Julia REPL.

    For example in the following docstring a variable ``a`` is defined and the expected
    result, as printed in a Julia REPL, appears afterwards::

5. ``# Examples`` セクションにコードの例を含めてください。

   可能であれば、コードの例はdoctestとして記述してください。doctestは、 `````jldoctest`` で始まり、
   インプットとJulia REPLを模した期待されたアウトプットを持つ任意の数の ``julia>`` プロンプトを含んだ、
   分離コードブロック（ `コードブロック`_ を参照）です。
   
   例えば、次のdocstringでは、変数 ``a`` が定義され、Julia REPLに出力されているように、
   期待される結果がその後に表示されます。::

       """
       Some nice documentation here.

       # Examples

       ```jldoctest
       julia> a = [1 2; 3 4]
       2×2 Array{Int64,2}:
        1  2
        3  4
       ```
       """

.. warning::
.. 
      Calling ``rand`` and other RNG-related functions should be avoided in doctests since
      they will not produce consistent outputs during different Julia sessions.

      Operating system word size (``Int32`` or ``Int64``) as well as path separator
      differences (``/`` or ``\``) will also effect the reproducibility of some doctests.

      Note that whitespace in your doctest is significant! The doctest will fail if you
      misalign the output of pretty-printing an array, for example.

``rand`` やその他のRNG関連関数は、異なるJuliaセッション中に一貫した出力を生成しないため、doctestでは避けるべきです。

OSのワードサイズ（ ``Int32`` または ``Int64`` ）やパスの区切り文字の違い（ ``/`` または ``\`` ）も、
doctestの再現性に影響する可能性があります。

doctestの空白が重要であることに注意してください。例えば、pretty-printing配列の出力の位置をずらすと、doctestが失敗します。

.. 
   You can then run ``make -C doc doctest`` to run all the doctests in the Julia Manual,
   which will ensure that your example works.

   Examples that are untestable should be written within fenced code blocks starting with
   `````julia`` so that they are highlighted correctly in the generated documentation.
   
``make -C doc doctest`` を実行することでJulia Manual内のdoctestを全て実行することができ、
これによりあなたの例が正常に動作するかを確認することができます。

テストできない例は、 `````julia`` から始まる分離コードブロック内に記述することで、生成されたドキュメント内で適切にハイライトされます。  

.. tip::

.. 
      Wherever possible examples should be **self-contained** and **runnable** so that
      readers are able to try them out without having to include any dependencies.

可能な限り、例は自己完結的で実行可能でなければなりません。これにより使用者は依存関係を一切含まずにそれらをテストすることができます。

.. 
  6. Use backticks to identify code and equations.

     Julia identifiers and code excerpts should always appear between backticks `````
     to enable highlighting. Equations in the LaTeX syntax can be inserted between
     double backticks ``````. Use Unicode characters rather than their LaTeX escape sequence,
     i.e. ````α = 1```` rather than :samp:`\`\`\\\\alpha = 1\`\``.

6. コードと式を識別するためにバッククォートを使用します。
   
   Juliaの識別子とコードの引用は、ハイライトを可能にするために常にバッククォート ````` の間に記述する必要があります。
   LaTeX構文の方程式は、ダブルバッククォート `````` の間に挿入することができます。LaTeXエスケープシーケンスではなく、
   Unicode文字を使用してください。例えば、 :samp:`\`\`\\\\alpha = 1\`\`` ではなく ````α = 1```` と記述してください。

.. 
  7. Place the starting and ending ``"""`` characters on lines by themselves.

7. 開始と終了の「”""」文字を1行に記述してください。

.. 
   That is, write::

これは、以下のように記述するべきであり、::

       """
       ...

       ...
       """
       f(x, y) = ...

.. 
   rather than::

以下のようにはしないでください。::

       """...

       ..."""
       f(x, y) = ...

.. 
   This makes it more clear where docstrings start and end.

これにより、どこからdocstringが開始し、どこで終了するのかが明確になります。

.. 
  8. Respect the line length limit used in the surrounding code.

     Docstrings are edited using the same tools as code. Therefore, the same conventions
     should apply. It it advised to add line breaks after 92 characters.

8. 周囲のコードで使用されている行の長さの制限を守ってください。

.. 
  Accessing Documentation
  -----------------------

ドキュメントへのアクセス
-----------------------

.. 
  Documentation can be accessed at the REPL or in IJulia by typing ``?``
  followed by the name of a function or macro, and pressing ``Enter``. For
  example,

ドキュメントは、REPLまたはIJuliaで、関数名やマクロ名の後に ``?`` を入力し、
``Enter`` キーを押すことでアクセスできます。例えば、

.. doctest::

    ?fft
    ?@time
    ?r""

.. 
  will bring up docs for the relevant function, macro or string macro
  respectively. In `Juno <http://junolab.org>`_ using ``Ctrl-J, Ctrl-D`` will
  bring up documentation for the object under the cursor.

それぞれ関連する関数、マクロ、または文字列マクロのドキュメントを表示します。 `Juno <http://junolab.org>`_ では
``Ctrl-J, Ctrl-D`` を使用することで、カーソルの下にオブジェクトのドキュメントが表示します。

.. 
  Functions & Methods
  -------------------

関数＆メソッド
-------------------

.. 
  Functions in Julia may have multiple implementations, known as methods.
  While it's good practice for generic functions to have a single purpose,
  Julia allows methods to be documented individually if necessary.
  In general, only the most generic method should be documented, or even the
  function itself (i.e. the object created without any methods by
  ``function bar end``). Specific methods should only be documented if their
  behaviour differs from the more generic ones. In any case, they should not
  repeat the information provided elsewhere. For example:

Juliaの関数には、メソッドと呼ばれる複数の実装があります。一般的な関数が一つのみの目的を持つことはよい習慣ですが、
Juliaでは必要に応じてメソッドを個別のドキュメントにすることができます。通常、最も一般的なメソッドまたは関数
（例えば ``function bar end`` メソッド以外で生成されたオブジェクト）がドキュメント化されるべきです。
固有のメソッドは、処理がより一般的なものと異なる場合にのみドキュメント化されるべきです。
いずれにしても、他の場所で提供されている情報は、繰り返すべきではありません。例えば、:

.. doctest::

    """
    Multiplication operator. `x*y*z*...` calls this function with multiple
    arguments, i.e. `*(x,y,z...)`.
    """
    function *(x, y)
      # ... [implementation sold separately] ...
    end

    "When applied to strings, concatenates them."
    function *(x::AbstractString, y::AbstractString)
      # ... [insert secret sauce here] ...
    end

    help?>*
    Multiplication operator. `x*y*z*...` calls this function with multiple
    arguments, i.e. `*(x,y,z...)`.

    When applied to strings, concatenates them.

.. 
  When retrieving documentation for a generic function, the metadata for
  each method is concatenated with the ``catdoc`` function, which can of
  course be overridden for custom types.

一般的な関数のドキュメントを取得する際には、個々のメソッドのメタデータは、
カスタムの型に対して上書きされることができる ``catdoc`` 関数と連結されます。

.. 
  Advanced Usage
  --------------

高度な使用法
--------------

.. 
  The ``@doc`` macro associates its first argument with its second in a
  per-module dictionary called ``META``. By default, documentation is
  expected to be written in Markdown, and the ``doc""`` string macro simply
  creates an object representing the Markdown content. In the future it is
  likely to do more advanced things such as allowing for relative image or
  link paths.

``@doc`` マクロは、 ``META`` というモジュールごとの辞書の中で、最初の引数を2番目の引数に関連付けます。
デフォルトでは、ドキュメントは Markdownで書かれていることが期待され、 ``doc""`` 文字列マクロは
マークダウンの内容を表すオブジェクトを作成します。将来的には、相対的な画像やリンクのパスを許容するなど、
より高度な処理を行う可能性があります。

.. 
  When used for retrieving documentation, the ``@doc`` macro (or equally,
  the ``doc`` function) will search all ``META`` dictionaries for metadata
  relevant to the given object and return it. The returned object (some
  Markdown content, for example) will by default display itself
  intelligently. This design also makes it easy to use the doc system in a
  programmatic way; for example, to re-use documentation between different
  versions of a function:

``@doc`` マクロ（あるいは同様の ``doc`` 関数）は、ドキュメントの検索に使用すると、
指定されたオブジェクトに関連するメタデータを全ての ``META`` ディクショナリから検索し、
結果を返します。返されたオブジェクト（例えば、マークダウンの内容など）は、
デフォルトでそれを表示します。また、この設計により、プログラミングの観点でドキュメントシステムが
使いやすくなります。例えば、関数の異なるバージョン間でドキュメントを再利用する場合、:

.. doctest::

    @doc "..." foo!
    @doc (@doc foo!) foo

.. 
  Or for use with Julia's metaprogramming functionality:

またはJuliaのメタプログラミング機能と併用する場合、:

.. doctest::

    for (f, op) in ((:add, :+), (:subtract, :-), (:multiply, :*), (:divide, :/))
        @eval begin
            $f(a,b) = $op(a,b)
        end
    end
    @doc "`add(a,b)` adds `a` and `b` together" add
    @doc "`subtract(a,b)` subtracts `b` from `a`" subtract

.. 
  Documentation written in non-toplevel blocks, such as ``if``, ``for``, and ``let``, are not
  automatically added to the documentation system. ``@doc`` must be used in these cases. For
  example:

``if`` 、 ``for`` 、 ``let`` などの最上位ではないブロックで書かれたドキュメントは、
自動的にはドキュメントシステムには追加されません。これらのケースでは ``@doc`` を使用しなければなりません。例えば、:

.. code-block:: julia

    if VERSION > v"0.4"
        "..."
        f(x) = x
    end

.. 
  will not add any documentation to ``f`` even when the condition is ``true`` and must instead
  be written as:

これは条件が ``true`` であっても ``f`` にドキュメントを追加することはなく、代わりに次のように記述する必要があります。:

.. code-block:: julia

    if VERSION > v"0.4"
        @doc "..." ->
        f(x) = x
    end

.. 
  Syntax Guide
  ------------

構文ガイド
------------

.. 
  A comprehensive overview of all documentable Julia syntax.

全てのドキュメント化可能なJulia構文の包括的な概要です。

.. 
  In the following examples ``"..."`` is used to illustrate an arbitrary docstring which may
  be one of the follow four variants and contain arbitrary text:

以下の例では、 ``"..."`` は、4つのつづり違いのうちの1つであり任意のテキストを含む、任意のdocstringを示すために使用されています。:

.. code-block:: julia

    "..."

    doc"..."

    """
    ...
    """

    doc"""
    ...
    """

.. 
  ``@doc_str`` should only be used when the docstring contains ``$`` or ``\`` characters that
  should not be parsed by Julia such as LaTeX syntax or Julia source code examples containing
  interpolation.

``@doc_str`` は、LaTeX構文や補間を含むJuliaソースコードの例など、Juliaによって解析されるべきではない ``$`` または
``\`` 文字がdocstringに含まれている場合にのみ使用してください。

.. 
  Functions and Methods
  ~~~~~~~~~~~~~~~~~~~~~

関数とメソッド
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: julia

    "..."
    function f end

    "..."
    f

.. 
  Adds docstring ``"..."`` to ``Function`` ``f``. The first version is the preferred syntax,
  however both are equivalent.

これはdocstring ``"..."`` を ``Function`` ``f`` に追加します。最初のバージョンが好ましい構文ですが、どちらも同等です。

.. code-block:: julia

    "..."
    f(x) = x

    "..."
    function f(x)
        x
    end

    "..."
    f(x)

.. 
  Adds docstring ``"..."`` to ``Method`` ``f(::Any)``.

これはdocsting ``"..."`` を ``Method`` ``f(::Any)`` に追加します。

.. code-block:: julia

    "..."
    f(x, y = 1) = x + y

.. 
  Adds docstring ``"..."`` to two ``Method``\ s, namely ``f(::Any)`` and ``f(::Any, ::Any)``.

これはdocsting ``"..."`` を2つの``Method``\  ``f(::Any)`` と ``f(::Any, ::Any)`` に追加します。

.. 
  Macros
  ~~~~~~

マクロ
~~~~~~

.. code-block:: julia

    "..."
    macro m(x) end

.. 
  Adds docstring ``"..."`` to the ``@m(::Any)`` macro definition.

これはdocsting ``"..."`` を ``@m(::Any)`` マクロ定義に追加します。

.. code-block:: julia

    "..."
    :(@m)

.. 
  Adds docstring ``"..."`` to the macro named ``@m``.

これはdocsting ``"..."`` を ``@m`` マクロに追加します。

.. 
  Types
  ~~~~~

型
~~~~~

.. code-block:: julia

    "..."
    abstract T1

    "..."
    type T2
        ...
    end

    "..."
    immutable T3
        ...
    end

.. 
  Adds the docstring ``"..."`` to types ``T1``, ``T2``, and ``T3``.

これはdocsting ``"..."`` を型 ``T1`` 、 ``T2`` 、 ``T3`` に追加します。

.. code-block:: julia

    "..."
    type T
        "x"
        x
        "y"
        y
    end

.. 
  Adds docstring ``"..."`` to type ``T``, ``"x"`` to field ``T.x`` and ``"y"`` to field
  ``T.y``. Also applicable to ``immutable`` types.

これはdocsting ``"..."`` を型 ``T`` に、 ``"x"`` をフィールド ``T.x`` に、 ``"y"`` をフィールド ``T.y`` に追加します。
これは ``immutable`` 型にも適用できます。

.. code-block:: julia

    "..."
    typealias A T

.. 
  Adds docstring ``"..."`` to the ``Binding`` ``A``.

これはdocsting ``"..."`` を ``Binding`` ``A`` に追加します。

.. 
  ``Binding``\ s are used to store a reference to a particular ``Symbol`` in a ``Module``
  without storing the referenced value itself.

``Binding``\ は、参照された値自体を格納することなく、 ``Module`` 内の特定の ``Symbol`` への参照を格納するために使用されます。

.. 
  Modules
  ~~~~~~~

モジュール
~~~~~~~

.. code-block:: julia

    "..."
    module M end

    module M

    "..."
    M

    end

.. 
  Adds docstring ``"..."`` to the ``Module`` ``M``. Adding the docstring above the ``Module``
  is the preferred syntax, however both are equivalent.

これはdocsting ``"..."`` を ``Module`` ``M`` に追加します。 ``Module`` 上にdocstringを追加するのが好ましい構文ですが、
どちらも同等です。

.. code-block:: julia

    "..."
    baremodule M
    # ...
    end

    baremodule M

    import Base: @doc

    "..."
    f(x) = x

    end

.. 
  Documenting a ``baremodule`` by placing a docstring above the expression automatically
  imports ``@doc`` into the module. These imports must be done manually when the
  module expression is not documented. Empty ``baremodule``\ s cannot be documented.

式の上にdocstringを記述して ``baremodule`` をドキュメント化すると、
自動的に ``@doc`` がモジュールにインポートされます。これらのインポートは、
モジュール式がドキュメント化されていない場合は、手動で行う必要があります。
空の ``baremodule``\ はドキュメント化することができません。

.. 
  Global Variables
  ~~~~~~~~~~~~~~~~

グローバル変数
~~~~~~~~~~~~~~~~

.. code-block:: julia

    "..."
    const a = 1

    "..."
    b = 2

    "..."
    global c = 3

.. 
  Adds docstring ``"..."`` to the ``Binding``\ s ``a``, ``b``, and ``c``.

これはdocsting ``"..."`` を ``Binding``\  ``a`` 、 ``b`` および ``c`` に追加します。

.. note::
.. 
    When a ``const`` definition is only used to define an alias of another definition, such
    as is the case with the function ``div`` and its alias ``÷`` in ``Base``, do not
    document the alias and instead document the actual function.

    If the alias is documented and not the real definition then the docsystem (``?`` mode)
    will not return the docstring attached to the alias when the real definition is
    searched for.

    For example you should write

``const`` が別の定義のエイリアスを定義するために使用された場合
（例えば関数 ``div`` とそのエイリアスである ``Base`` における ``÷`` ）、
エイリアスはドキュメント化せずに、その実際の関数をドキュメント化してください。

もし実際の定義ではなくエイリアスがドキュメント化された場合、docsystem（ ``?`` モード）は、
実際の定義が検索された際にエイリアスに付随するdocstringを返しません。

例えば、以下のように記述するべきであり、

    .. code-block:: julia

        "..."
        f(x) = x + 1
        const alias = f

.. 
    rather than

以下は望ましくありません。

    .. code-block:: julia

        f(x) = x + 1
        "..."
        const alias = f

.. code-block:: julia

    "..."
    sym

.. 
  Adds docstring ``"..."`` to the value associated with ``sym``. Users should prefer
  documenting ``sym`` at it's definition.

これはdocsting ``"..."`` を ``sym`` に関連する値に追加します。ユーザは、定義において、
``sym`` をドキュメント化することが望ましいです。

.. 
  Multiple Objects
  ~~~~~~~~~~~~~~~~

複数のオブジェクト
~~~~~~~~~~~~~~~~

.. code-block:: julia

    "..."
    a, b

.. 
  Adds docstring ``"..."`` to ``a`` and ``b`` each of which should be a documentable
  expression. This syntax is equivalent to

これはdocsting ``"..."`` をそれぞれがドキュメント化可能な表現である必要がある ``a`` および ``b`` に追加します。
この構文は以下と等しいです。

.. code-block:: julia

    "..."
    a

    "..."
    b

.. 
  Any number of expressions many be documented together in this way. This syntax can be useful
  when two functions are related, such as non-mutating and mutating versions ``f`` and ``f!``.

任意の数の式はこの方法で一緒にドキュメント化することができます。
この構文は、変形していないバージョンと変形したバージョンの ``f`` と ``f!`` のように、
2つの関数が関連する際に便利です。

.. 
  Macro-generated code
  ~~~~~~~~~~~~~~~~~~~~

マクロで生成されたコード
~~~~~~~~~~~~~~~~~~~~

.. code-block:: julia

    "..."
    @m expression

.. 
  Adds docstring ``"..."`` to expression generated by expanding ``@m expression``. This allows
  for expressions decorated with ``@inline``, ``@noinline``, ``@generated``, or any other
  macro to be documented in the same way as undecorated expressions.

これはdocsting ``"..."`` を ``@m expression`` を拡張することにより生成された式に追加します。
この方法により、 ``@inline`` 、 ``@noinline`` 、 ``@generated`` またはその他のマクロで修飾された式を、
修飾されていない式と同じように、ドキュメント化することができます。

.. 
  Macro authors should take note that only macros that generate a single expression will
  automatically support docstrings. If a macro returns a block containing multiple
  subexpressions then the subexpression that should be documented must be marked using the
  :func:`@__doc__` macro.

マクロの作成者は、1つの式を生成するマクロのみ自動的にdocstringをサポートすることに注意してください。
もしマクロが複数の部分式を含むブロックを返す場合は、ドキュメント化したい部分式は :func:`@__doc__` マクロを使って
マークしなければなりません。

.. 
  The ``@enum`` macro makes use of ``@__doc__`` to allow for documenting ``Enum``\ s.
  Examining it's definition should serve as an example of how to use ``@__doc__`` correctly.

``@enum`` マクロは、 ``@__doc__`` を使って ``Enum``\ をドキュメント化できるようにします。
その定義を確認することで、 ``@__doc__`` を正しく使用する方法の例を理解できるはずです。

.. function:: @__doc__(ex)

   .. Docstring generated from Julia source

.. 
   Low-level macro used to mark expressions returned by a macro that should be documented. If more than one expression is marked then the same docstring is applied to each expression.

   .. code-block:: julia

       macro example(f)
           quote
               $(f)() = 0
               @__doc__ $(f)(x) = 1
               $(f)(x, y) = 2
           end |> esc
       end

   ``@__doc__`` has no effect when a macro that uses it is not documented.

.. 
  Markdown syntax
  ---------------

Markdown構文
---------------

.. 
  The following markdown syntax is supported in Julia.

以下のMarkdown構文はJuliaでサポートされています。

.. 
  Inline elements
  ~~~~~~~~~~~~~~~

インライン要素
~~~~~~~~~~~~~~~

.. 
  Here "inline" refers to elements that can be found within blocks of text, i.e. paragraphs. These include the following elements.

ここにおける「インライン」は、パラグラフなどテキストのブロック内にある要素を指します。要素は以下を含みます。

.. 
  Bold
  ^^^^

太字
^^^^

.. 
  Surround words with two asterisks, ``**``, to display the enclosed text in boldface.

2つのアスタリスク ``**`` で単語を囲むことにより、囲まれた単語を太字で表示することができます。

.. code-block:: text

   A paragraph containing a **bold** word.

.. 
  Italics
  ^^^^^^^

イタリック体
^^^^^^^^^^^

.. 
  Surround words with one asterisk, ``*``, to display the enclosed text in italics.

1つのアスタリスク ``*`` で単語を囲むことにより、囲まれた単語をイタリック体で表示することができます。

.. code-block:: text

   A paragraph containing an *emphasised* word.

.. 
  Literals
  ^^^^^^^^

リテラル
^^^^^^^^

.. 
  Surround text that should be displayed exactly as written with single backticks, ````` .

1つのバッククオート ````` で単語を囲むことにより、囲まれた単語を記載した通り（リテラル）に表示することができます。

.. code-block:: text

   A paragraph containing a `literal` word.

.. 
  Literals should be used when writing text that refers to names of variables, functions, or other parts of a Julia program.

リテラルは変数名、関数名、またはその他のJuliaのプログラム名を指すテキストを記述する際に使用してください。

.. tip::
.. 
    To include a backtick character within literal text use three backticks rather than one to enclose the text.

バッククオートをリテラル内に含めたい場合は、3つのバッククオートでテキストを囲んでください。

    .. code-block:: text

       A paragraph containing a ``` `backtick` character ```.
.. 
    By extension any odd number of backticks may be used to enclose a lesser number of backticks.

さらに、奇数のバッククオートは、それよりも少ないバッククオートを囲むために使用します。

.. 
  :math:`\LaTeX`
  ^^^^^^^^^^^^^^

:math:`\LaTeX`
^^^^^^^^^^^^^^

.. 
  Surround text that should be displayed as mathematics using :math:`\LaTeX` syntax with double backticks, `````` .

算術演算として表示したい箇所は、ダブルバッククオート `````` と「 :math:`\LaTeX` を使って囲んでください。

.. code-block:: text

   A paragraph containing some ``\LaTeX`` markup.

.. tip::
.. 
    As with literals in the previous section, if literal backticks need to be written within double backticks use an even number greater than two. Note that if a single literal backtick needs to be included within :math:`\LaTeX` markup then two enclosing backticks is sufficient.

前のセクションのリテラルと同様に、リテラルバッククオートをダブルバッククオート内に記述したい場合は、
2つ以上の偶数のバッククオートを使用してください。1つのリテラルバッククオートを :math:`\LaTeX` マークアップ内に
記述したい場合は、2つのバッククオートで十分です。

.. 
  Links
  ^^^^^

リンク
^^^^^

.. 
  Links to either external or internal addresses can be written using the following syntax, where the text enclosed in square brackets, ``[ ]``, is the name of the link and the text enclosed in parentheses, ``( )``, is the URL.

外部または内部アドレスへのリンクは、リンクの名前を角括弧 ``[ ]`` 内に、URLを丸括弧 ``( )`` 内に記述する構文で表すことができます。

.. code-block:: text

   A paragraph containing a link to [Julia](http://www.julialang.org).

.. 
Footnote references
^^^^^^^^^^^^^^^^^^^

脚注の参照
^^^^^^^^^^^^^^^^^^^

.. 
  Named and numbered footnote references can be written using the following syntax. A footnote name must be a single alphanumeric word containing no punctuation.

名前付きおよび番号付きの脚注の参照は、脚注名を句読点を含まない英数字の1単語とする構文とすることで、記述できます。

.. code-block:: text

   A paragraph containing a numbered footnote [^1] and a named one [^named].

.. note::
.. 
      The text associated with a footnote can be written anywhere within the same page as the footnote reference. The syntax used to define the footnote text is discussed in the `Footnotes`_ section below.

ある1つの脚注に関連するテキストは、その脚注がある同じページ内であればどこにでも記述することができます。
脚注のテキストを定義する構文の詳細は、以下の `脚注`_ セクションを参照してください。

.. 
  Toplevel elements
  ~~~~~~~~~~~~~~~~~

最上位の要素
~~~~~~~~~~~~~~~~~

.. 
  The following elements can be written either at the "toplevel" of a document or within another "toplevel" element.

以下の要素は、ドキュメントの「最上位」または他の要素の「最上位」に記述することができます。

.. 
  Paragraphs
  ^^^^^^^^^^

パラグラフ
^^^^^^^^^^

.. 
  A paragraph is a block of plain text, possibly containing any number of inline elements defined in the `Inline elements`_ section above, with one or more blank lines above and below it.

パラグラフは、その前後に1行または2行の空白行を伴う、上記 `インライン要素`_ で定義されている任意の数のインライン要素を持つ、
プレーンテキストのブロックです。

.. code-block:: text

   This is a paragraph.

   And this is *another* one containing some emphasised text.
   A new line, but still part of the same paragraph.

.. 
  Headers
  ^^^^^^^

ヘッダ
^^^^^^^

.. 
 A document can be split up into different sections using headers. Headers use the following syntax:

ドキュメントはヘッダを使用することでいくつかのセクションに分割することができます。ヘッダは以下の構文を使用します。:

.. code-block:: text

   # Level One
   ## Level Two
   ### Level Three
   #### Level Four
   ##### Level Five
   ###### Level Six

.. 
  A header line can contain any inline syntax in the same way as a paragraph can.

ヘッダ行は、パラグラフと同様に、任意の数のインライン構文を含めることができます。

.. tip::
.. 
      Try to avoid using too many levels of header within a single document. A heavily nested document may be indicative of a need to restructure it or split it into several pages covering separate topics.

1つのドキュメント内に多くの階層のヘッダを使うことは避けるようにしてください。複雑にネスト化されたドキュメントは、
再構成し直すか、またはトピックごとの複数のページに分割する必要があります。

.. 
  Code blocks
  ^^^^^^^^^^^

コードブロック
^^^^^^^^^^^^

.. 
  Source code can be displayed as a literal block using an indent of four spaces as shown in the following example.

ソースコードは、下記の例の通り、4つのスペースをインデントとして使用することで、リテラルブロックとして表示させることができます。

.. code-block:: text

   This is a paragraph.

       function func(x)
           # ...
       end

   Another paragraph.

.. 
  Additionally, code blocks can be enclosed using triple backticks with an optional "language" to specify how a block of code should be highlighted.

加えて、コードブロックは、どのようにコードブロックがハイライトされるかを指定するオプションの「タイトル」を付けることができ、
3つのバッククオートで囲みます。

.. code-block:: text

   A code block without a "language":

   ```
   function func(x)
       # ...
   end
   ```

   and another one with the "language" specified as `julia`:

   ```julia
   function func(x)
       # ...
   end
   ```

.. note::
.. 
      "Fenced" code blocks, as shown in the last example, should be prefered over indented code blocks since there is no way to specify what language an indented code block is written in.

最後の例にあるように分離コードブロックは、インデントされたコードブロックがどの「タイトル」で記述されているかを特定する方法が無いため、
インデントされたコードブロック外に記述することが望ましいです。

.. 
  Block quotes
  ^^^^^^^^^^^^

ブロック引用
^^^^^^^^^^^^

.. 
  Text from external sources, such as quotations from books or websites, can be quoted using ``>`` characters prepended to each line of the quote as follows.

文献やウェブサイトなどの外部ソースからのテキストは、以下例のように各行単位の先頭に ``>`` を使うことで引用することができます。

.. code-block:: text

   Here's a quote:

   > Julia is a high-level, high-performance dynamic programming language for
   > technical computing, with syntax that is familiar to users of other
   > technical computing environments.

.. 
  Note that a single space must appear after the ``>`` character on each line. Quoted blocks may themselves contain other toplevel or inline elements.

各行単位の ``>`` の後にはスペースを1つ開けることに注意してください。
引用されたブロック自体が最上位またはインライン要素を含む場合があります。

.. 
  Images
  ^^^^^^

画像
^^^^^^

.. 
  The syntax for images is similar to the link syntax mentioned above. Prepending a ``!`` character to a link will display an image from the specified URL rather than a link to it.

画像の構文は上記のリンクの構文と似ています。リンクの先頭に ``!`` 文字を追加することで、
リンク先ではなく指定されたURLからの画像を表示します。

.. code-block:: text

   ![alternative text](link/to/image.png)

.. 
  Lists
  ^^^^^

リスト
^^^^^

.. 
Unordered lists can be written by prepending each item in a list with either ``*``, ``+``, or ``-``.

順不同のリストは、リスト化したいものの先頭に ``*`` 、 ``+`` または ``-`` を追加することで記述することができます。

.. code-block:: text

   A list of items:

     * item one
     * item two
     * item three

.. 
  Note the two spaces before each ``*`` and the single space after each one.

``*`` の前には2つのスペース、後ろには1つのスペースが必要である点に注意してください。

.. 
  Lists can contain other nested toplevel elements such as lists, code blocks, or quoteblocks. A blank line should be left between each list item when including any toplevel elements within a list.

リストは、他のリスト、コードブロック、または引用ブロックなど、他のネスト化された最上位要素を含めることができます。
リスト内に他の最上位要素を含める場合は、各リストの間に1行の空白行を入れてください。

.. code-block:: text

   Another list:

     * item one

     * item two

       ```
       f(x) = x
       ```

     * And a sublist:

         + sub-item one
         + sub-item two

.. note::

.. 
      The contents of each item in the list must line up with the first line of the item. In the above example the fenced code block must be indented by four spaces to align with the ``i`` in ``item two``.

各リストの内容は、最初の項目と同じように並べて記述する必要があります。上記の例では、 ``item two`` の ``i`` に揃えるために、
分離コードブロックは4つのスペース分インデントさせる必要があります。

.. 
  Ordered lists are written by replacing the "bullet" character, either ``*``, ``+``, or ``-``, with a positive integer followed by either ``.`` or ``)``.

順序を意識したリストは、 ``*`` 、 ``+`` または ``-`` などの圏点の代わりに、 ``.`` または ``)`` を伴う
正の整数を使用することにより表すことができます。

.. code-block:: text

   Two ordered lists:

    1. item one
    2. item two
    3. item three


    5) item five
    6) item six
    7) item seven

.. 
  An ordered list may start from a number other than one, as in the second list of the above example, where it is numbered from five. As with unordered lists, ordered lists can contain nested toplevel elements.

上記の例の2つ目のリストのように、1以外の数字からリストが始まることができます。
また、順不同のリストのように、ネスト化された最上位の要素を含めることができます。

.. 
  Display equations
  ^^^^^^^^^^^^^^^^^

ディスプレイ式
^^^^^^^^^^^^^^^^^

.. 
  Large :math:`\LaTeX` equations that do not fit inline within a paragraph may be written as display equations using a fenced code block with the "language" ``math`` as in the example below.

パラグラフの1行に収まらない長い :math:`\LaTeX` 式は、以下の例のように ``math`` 「タイトル」を持つ分離コードブロックを
使用したディスプレイ式として記述することができます。

.. code-block:: text

   ```math
   f(a) = \frac{1}{2\pi}\int_{0}^{2\pi} (\alpha+R\cos(\theta))d\theta
   ```

.. 
  Footnotes
  ^^^^^^^^^

脚注
^^^^^^^^^

.. 
  This syntax is paired with the inline syntax for `Footnote references`_. Make sure to read that section as well.

この構文は `脚注の参照`_ の構文と対をなします。 `脚注の参照`_ セクションについても一読ください。

.. 
  Footnote text is defined using the following syntax, which is similar to footnote reference syntax, aside from the ``:`` character that is appended to the footnote label.

脚注テキストの構文は、脚注参照構文と似ていますが、 ``:`` を使用して脚注ラベルを追加します

.. code-block:: text

   [^1]: Numbered footnote text.

   [^note]:

       Named footnote text containing several toplevel elements.

         * item one
         * item two
         * item three

       ```julia
       function func(x)
           # ...
       end
       ```

.. note::
.. 
    No checks are done during parsing to make sure that all footnote references have matching footnotes.

構文の解析中には、全ての脚注の参照が一致する脚注を持っているかのチェックは行われません。

.. 
  Horizontal rules
  ^^^^^^^^^^^^^^^^

水平の罫線
^^^^^^^^^^^^^^^^

.. 
  The equivalent of an ``<hr>`` HTML tag can be written using the following syntax:

HTMLの ``<hr>`` タグと同様のものは、以下の構文を使って記述することができます。

.. code-block:: text

   Text above the line.

   ---

   And text below the line.

.. 
  Tables
  ^^^^^^

テーブル
^^^^^^

.. 
  Basic tables can be written using the syntax described below. Note that markdown tables have limited features and cannot contain nested toplevel elements unlike other elements discussed above -- only inline elements are allowed. Tables must always contain a header row with column names. Cells cannot span multiple rows or columns of the table.

基本的なテーブルは、以下の例にある構文を使うことで表すことができます。マークダウンテーブルは限られた機能のみを持ち、
上記の他の要素のようにネスト化された最上位の要素を含めることはできず、インライン要素のみ使用することができます。
テーブルは列名を持つヘッダ行が含まれている必要があります。セルはテーブルの複数の行は列にまたがることができません。

.. code-block:: text

   | Column One | Column Two | Column Three |
   |:---------- | ---------- |:------------:|
   | Row `1`    | Column `2` |              |
   | *Row* 2    | **Row** 2  | Column ``3`` |

.. note::

.. 
      As illustrated in the above example each column of ``|`` characters must be aligned vertically.

      A ``:`` character on either end of a column's header separator (the row containing ``-`` characters) specifies whether the row is left-aligned, right-aligned, or (when ``:`` appears on both ends) center-aligned. Providing no ``:`` characters will default to right-aligning the column.

上記の例のように、各列の ``|`` は垂直に並べる必要があります。

列のヘッダセパレータ（ ``-`` がある行）の最後にある ``:`` は、その行が右揃え、左揃え、
または（ ``:`` が両端にある場合は）中央揃えであることを指定します。 ``:`` がない場合は、デフォルトで列は右揃えとなります。

.. 
  Admonitions
  ^^^^^^^^^^^

注意書き
^^^^^^^^^^^

.. 
  Specially formatted blocks with titles such as "Notes", "Warning", or "Tips" are known as admonitions and are used when some part of a document needs special attention. They can be defined using the following ``!!!`` syntax:

"Notes" 、 "Warning" または "Tips" などのタイトルがついた特別なフォーマットを持つブロックは注意書きとして、
ドキュメントの一部に特に注意が必要な場合に使用されます。注意書きは以下のように ``!!!`` 文を使用して定義することができます。:

.. code-block:: text

   !!! note

       This is the content of the note.

   !!! warning "Beware!"

       And this is another one.

       This warning admonition has a custom title: `"Beware!"`.

.. 
  Admonitions, like most other toplevel elements, can contain other toplevel elements. When no title text, specified after the admonition type in double quotes, is included then the title used will be the type of the block, i.e. ``"Note"`` in the case of the ``note`` admonition.

注意書きは、他の最上位の要素と同様に、他の最上位の要素を含めることができます。
注意書きのタイプの後ろのダブルクオート内にタイトルテキストが無い場合、
``note`` タイプの場合は ``"Note"`` のように、ブロックのタイプがタイトルとして使用されます。

.. 
  Markdown Syntax Extensions
  --------------------------

Markdown構文の拡張
--------------------------

.. 
  Julia's markdown supports interpolation in a very similar way to basic string
  literals, with the difference that it will store the object itself in
  the Markdown tree (as opposed to converting it to a string). When the
  Markdown content is rendered the usual ``show`` methods will be
  called, and these can be overridden as usual. This design allows the
  Markdown to be extended with arbitrarily complex features (such as
  references) without cluttering the basic syntax.

Juliaのマークダウンは、基本的な文字列リテラルと同様に加筆をサポートしていますが、
オブジェクトを文字列に変換するのではなく、オブジェクトをマークダウンツリーに格納する点は異なります。
マークダウンの内容が与えられた場合は、通常の ``show`` メソッドが呼び出され、
通常通り上書きされます。この設計により、基本的な構文を煩雑にすることなく、
マークダウンを任意の複雑な機能（参照など）で拡張することができます。

.. 
  In principle, the Markdown parser itself can also be arbitrarily
  extended by packages, or an entirely custom flavour of Markdown can be
  used, but this should generally be unnecessary.
  
原則的には、マークダウン解析自体は、パッケージにより拡張したり完全にカスタマイズされたマークダウンを使用することもできますが、
これは一般的には不用です。  

