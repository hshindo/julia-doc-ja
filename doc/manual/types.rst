.. _man-types:

.. currentmodule:: Base

.. 
 *********
  Types
 *********

*********
 型
*********

.. 
 Type systems have traditionally fallen into two quite different camps:
 static type systems, where every program expression must have a type
 computable before the execution of the program, and dynamic type
 systems, where nothing is known about types until run time, when the
 actual values manipulated by the program are available. Object
 orientation allows some flexibility in statically typed languages by
 letting code be written without the precise types of values being known
 at compile time. The ability to write code that can operate on different
 types is called polymorphism. All code in classic dynamically typed
 languages is polymorphic: only by explicitly checking types, or when
 objects fail to support operations at run-time, are the types of any
 values ever restricted.

型システムは伝統的に2つの全く異なる系統に分類されます。静的型付きは、
プログラムの実行前に計算可能な型を持つ必要があります。また、動的型付きは、
操作される実際の値が有効になる実行時まで型は定義されません。オブジェクト指向は、
コンパイル時に正確な値の型が定義されていなくてもコードを記述できるようにすることで、
静的型付けされた言語の柔軟性を可能にします。異なる型で動作できるコードの性質をポリモーフィズムと呼ばれます。
動的に型定義された古典的な言語のコードは全て多相性です。型が明示的にチェックされるか、
または実行時にオブジェクトが処理を継続できない場合にのみ、値の型が制限されます。

.. 
 Julia's type system is dynamic, but gains some of the advantages of
 static type systems by making it possible to indicate that certain
 values are of specific types. This can be of great assistance in
 generating efficient code, but even more significantly, it allows method
 dispatch on the types of function arguments to be deeply integrated with
 the language. Method dispatch is explored in detail in
 :ref:`man-methods`, but is rooted in the type system presented
 here.

Juliaの型システムは動的ですが、ある特定の値はある特定の型であることを示すことができるようにすることで、
静的型付けの利点のいくつかを得ています。これは、効率的なコードを生成する上で大きな助けになる可能性がありますが、
さらに重要なことに、関数の引数の型に対するメソッドのディスパッチを言語と深く統合することができます。
メソッドのディスパッチは :ref:`man-メソッド` で詳細に説明されていますが、ここに示す型システムに関連しています。

.. 
 The default behavior in Julia when types are omitted is to allow values
 to be of any type. Thus, one can write many useful Julia programs
 without ever explicitly using types. When additional expressiveness is
 needed, however, it is easy to gradually introduce explicit type
 annotations into previously "untyped" code. Doing so will typically
 increase both the performance and robustness of these systems, and
 perhaps somewhat counterintuitively, often significantly simplify them.

型が省略されたときのJuliaのデフォルトの動作は、値がどの型であっても許容します。
従って、型を明示的に使用することなく、有用なJuliaプログラムを書くことができます。
しかし、さらなる表現が必要な場合、以前の「型なし」コードに明示的な型の注釈を徐々に提示することができます。
こうすることにより、システムのパフォーマンスと堅牢性の両方が向上し、反直感的にかつ大幅に単純化されます。

.. 
 Describing Julia in the lingo of `type
 systems <https://en.wikipedia.org/wiki/Type_system>`_, it is: dynamic,
 nominative and parametric. Generic types can be parameterized,
 and the hierarchical relationships
 between types are explicitly declared, rather than implied by compatible
 structure. One particularly distinctive feature of Julia's type system
 is that concrete types may not subtype each other: all concrete types
 are final and may only have abstract types as their supertypes. While
 this might at first seem unduly restrictive, it has many beneficial
 consequences with surprisingly few drawbacks. It turns out that being
 able to inherit behavior is much more important than being able to
 inherit structure, and inheriting both causes significant difficulties
 in traditional object-oriented languages. Other high-level aspects of
 Julia's type system that should be mentioned up front are:

`型システム <https://en.wikipedia.org/wiki/Type_system>`_ の用語でJuliaを説明すると、
動的、主格、パラメトリックです。総括的な型をパラメータ化することができ、
型間の階層関係は、暗黙的な互換性のある構造ではなく、明示的に宣言されます。Juliaの型システムの特に特徴的な点は、
具体的な型は互いにサブタイプを持たないかもしれないということです。全ての具体的な型は最終的なものであり、
抽象型のみをその上位タイプとして持つことができます。これは最初は過度に制限されているように見えるかもしれませんが、
驚くほどわずかな欠点を伴う有益な結果をもたらします。動作を継承できることは構造を継承することよりも重要であり、
両方を継承することは従来のオブジェクト指向言語では大きな困難を引き起こすことがわかっています。
Juliaの型システムの他のハイレベルな側面は、以下の通りです。

.. 
   -  There is no division between object and non-object values: all values
      in Julia are true objects having a type that belongs to a single,
      fully connected type graph, all nodes of which are equally
      first-class as types.
   -  There is no meaningful concept of a "compile-time type": the only
      type a value has is its actual type when the program is running. This
      is called a "run-time type" in object-oriented languages where the
      combination of static compilation with polymorphism makes this
      distinction significant.
   -  Only values, not variables, have types — variables are simply names
      bound to values.
   -  Both abstract and concrete types can be parameterized by other types.
      They can also be parameterized by symbols, by values of any type for
      which :func:`isbits` returns true (essentially, things like numbers and bools
      that are stored like C types or structs with no pointers to other objects),
      and also by tuples thereof. Type parameters may be omitted when they
      do not need to be referenced or restricted.

-  オブジェクトの値と非オブジェクトの値には違いがありません。Juliaの全ての値は、
   接続された1つの型グラフに属する型を持つオブジェクトであり、全てのノードが同様に第1級の型です。
-  「コンパイル時の型」という概念はありません。値が持つ唯一の型は、プログラムが実行されている際の実際の型となります。
   これは、オブジェクト指向言語では実行時型と呼ばれ、静的コンパイルとポリモーフィズムの組み合わせによってこの区別が重要になります。
-  変数ではなく値だけが型を持ちます。変数は単に値に結び付けられた名前です。
-  抽象型および具象型は、他の型でパラメータ化できます。記号や、 :func:`isbits` がtrueを返す型の値（基本的には、
   C型のように格納された数値やブールや、他のオブジェクトへのポインタを持たない構造体）や、
   それらのタプルによってパラメータ化することもできます。型パラメータは、参照または制限する必要がない場合に省略することができます。

.. 
 Julia's type system is designed to be powerful and expressive, yet
 clear, intuitive and unobtrusive. Many Julia programmers may never feel
 the need to write code that explicitly uses types. Some kinds of
 programming, however, become clearer, simpler, faster and more robust
 with declared types.

Juliaの型システムは、強力で表現力豊かでありますが、それでいて明確でわかりやすく、直観的で作業の妨げにならないように
設計されています。Juliaプログラマは、明示的に型を使用するコードを書く必要性を感じることはないかもしれません。
しかし、いくつかのプログラミングは、型の宣言によってより明確で、より簡単に、より早く、より堅牢になります。

.. 
 Type Declarations
 -----------------

型宣言
-----------------

.. 
 The ``::`` operator can be used to attach type annotations to
 expressions and variables in programs. There are two primary reasons to
 do this:

 1. As an assertion to help confirm that your program works the way you
    expect,
 2. To provide extra type information to the compiler, which can then
    improve performance in some cases
   
``::`` 演算子を使用することで、プログラム内の式や変数に型の注釈を付けることができます。これには主に2つの理由があります。

1. プログラムが期待通りに動作することを確認するための表明として使用するため
2. コンパイラに追加の型情報を提供することで、場合によってはパフォーマンスを向上させることができるため

.. 
 When appended to an expression computing a value, the ``::``
 operator is read as "is an instance of". It can be used
 anywhere to assert that the value of the expression on the left is an
 instance of the type on the right. When the type on the right is
 concrete, the value on the left must have that type as its
 implementation — recall that all concrete types are final, so no
 implementation is a subtype of any other. When the type is abstract, it
 suffices for the value to be implemented by a concrete type that is a
 subtype of the abstract type. If the type assertion is not true, an
 exception is thrown, otherwise, the left-hand value is returned:

値を計算する式に追加した場合、 ``::`` 演算子は「is an instance of（〜のインスタンスである）」と読み込まれます。
左辺の式の値が右辺の型のインスタンスであることを宣言するために、どこでも使用することができます。
右辺の型が具体型である場合、左側の値はその実装としてその型を持たなければなりません。
前述の通り全ての具体型は最終的であり、実装は他の型のサブタイプとはならないことに注意してください。
型が抽象型である場合、値は抽象型のサブタイプである具体型によって実装さるには十分です。
型宣言が真でない場合は例外が出力され、それ以外の場合は左辺の値が返されます。

.. doctest::

    julia> (1+2)::AbstractFloat
    ERROR: TypeError: typeassert: expected AbstractFloat, got Int64
     ...

    julia> (1+2)::Int
    3

.. 
 This allows a type assertion to be attached to any expression
 in-place.

これにより、型宣言を任意の式に適切に付与することができます。

.. 
 When appended to a variable on the left-hand side of an assignment,
 or as part of a ``local`` declaration, the ``::`` operator means something
 a bit different: it declares the variable to always have the specified type,
 like a type declaration in a statically-typed language such as C. Every
 value assigned to the variable will be converted to the declared type
 using :func:`convert`:

割り当ての左側の変数に追加する、またはローカル宣言の一部として追加した場合、 ``::`` 演算子意味は少し変わります。
この場合変数は、C言語などの静的型言語における型宣言のように、常に指定された型を持つように宣言します。
変数に割り当てられた全ての値は、 :func:`convert` を使用することで宣言された型に変換されます。

.. doctest:: foo-func

    julia> function foo()
               x::Int8 = 100
               x
           end
    foo (generic function with 1 method)

    julia> foo()
    100

    julia> typeof(ans)
    Int8

.. 
 This feature is useful for avoiding performance "gotchas" that could
 occur if one of the assignments to a variable changed its type
 unexpectedly.

この機能は、変数への割り当ての1つが予期せずその型を変更した場合に発生する可能性のある
パフォーマンスの「落とし穴」を回避するのに便利です。

.. 
 This "declaration" behavior only occurs in specific contexts::

この「宣言」動作は、特定の文脈でのみ発生します。::

    local x::Int8  # in a local declaration
    x::Int8 = 10   # as the left-hand side of an assignment

.. 
 and applies to the whole current scope, even before the declaration.
 Currently, type declarations cannot be used in global scope, e.g. in
 the REPL, since Julia does not yet have constant-type globals.

宣言の前であっても、現在のスコープ全体に適用されます。Juliaはまだ定型のグローバルがないため、
REPLでは、現在型宣言はグローバルスコープでは使用できません。

.. 
 Declarations can also be attached to function definitions::

宣言は関数定義に付加することもできます。::

    function sinc(x)::Float64
        if x == 0
            return 1
        end
        return sin(pi*x)/(pi*x)
    end

.. 
 Returning from this function behaves just like an assignment to
 a variable with a declared type: the value is always converted to
 ``Float64``.

この関数からの戻り値は、宣言された型を持つ変数への割り当てと同様に動作します。
値は常に ``Float64`` に変換されます。


.. _man-abstract-types:

.. 
 Abstract Types
 --------------

抽象型
--------------

.. 
 Abstract types cannot be instantiated, and serve only as nodes in the
 type graph, thereby describing sets of related concrete types: those
 concrete types which are their descendants. We begin with abstract types
 even though they have no instantiation because they are the backbone of
 the type system: they form the conceptual hierarchy which makes Julia's
 type system more than just a collection of object implementations.

抽象型はインスタンス化することはできず、型グラフ内のノードとしてのみ機能し、それによって
関連するそれらの子孫である具体型の集合を記述します。インスタンス化できない関わらず
抽象型から説明を始める理由は、抽象型は型システムの根幹であるためです。抽象型はJuliaの
型システムを単なるオブジェクト実装の集合体以上にする概念的階層を形成します。

.. 
 Recall that in :ref:`man-integers-and-floating-point-numbers`, we
 introduced a variety of concrete types of numeric values: :class:`Int8`,
 :class:`UInt8`, :class:`Int16`, :class:`UInt16`, :class:`Int32`, :class:`UInt32`, :class:`Int64`,
 :class:`UInt64`, :class:`Int128`, :class:`UInt128`, :class:`Float16`, :class:`Float32`, and
 :class:`Float64`.  Although they have different representation sizes, :class:`Int8`,
 :class:`Int16`, :class:`Int32`, :class:`Int64`  and :class:`Int128` all have in common that
 they are signed integer types. Likewise :class:`UInt8`, :class:`UInt16`, :class:`UInt32`,
 :class:`UInt64` and :class:`UInt128` are all unsigned integer types, while
 :class:`Float16`, :class:`Float32` and :class:`Float64` are distinct in being
 floating-point types rather than integers. It is common for a piece of code
 to make sense, for example, only if its arguments are some kind of integer,
 but not really depend on what particular *kind* of integer.  For example,
 the greatest common denominator algorithm works for all kinds of integers,
 but will not work for floating-point numbers.  Abstract types allow the
 construction of a hierarchy of types, providing a context into which
 concrete types can fit.  This allows you, for example, to easily program to
 any type that is an integer, without restricting an algorithm to a specific
 type of integer.

「整数と浮動小数点数」にて、様々な数値の具体型を説明したことを思い出してください（:class:`Int8`、
 :class:`UInt8`、 :class:`Int16`、 :class:`UInt16`、 :class:`Int32`、 :class:`UInt32`、 :class:`Int64`、
 :class:`UInt64`、 :class:`Int128`、 :class:`UInt128`、 :class:`Float16`、 :class:`Float32`、
および :class:`Float64`）。それらの表現サイズは異なりますが、 :class:`Int8`:class:、 `Int16`、
 :class:`Int32`、 :class:`Int64`  および :class:`Int128` はすべて符号付き整数型であるという
共通点があります。同様に、 :class:`UInt8`、 :class:`UInt16`、 :class:`UInt32`、
 :class:`UInt64` および :class:`UInt128` はすべて符号なし整数型ですが、 :class:`Float16`、 :class:`Float32`
および :class:`Float64` は整数ではなく、浮動小数点型として区別されます。例えば、ある引数が何らかの種類の整数であっても、
特定の種類の整数に依存していない場合に限って、コードが意味をなすということは一般的です。
例えば、最大公約数アルゴリズムはあらゆる種類の整数で機能しますが、浮動小数点数では機能しません。
抽象型は、型の階層の構築を可能にし、具体型が適合できる文脈を提供します。これにより、
アルゴリズムを特定の整数型に制限することなく、整数型の任意の型に簡単にプログラムすることが可能になります。

.. 
 Abstract types are declared using the ``abstract`` keyword. The general
 syntaxes for declaring an abstract type are::

抽象型は、 ``abstract`` キーワードを使用して宣言されます。抽象型を宣言するための一般的な構文は次の通りです。::

    abstract «name»
    abstract «name» <: «supertype»

.. 
 The ``abstract`` keyword introduces a new abstract type, whose name is
 given by ``«name»``. This name can be optionally followed by ``<:`` and
 an already-existing type, indicating that the newly declared abstract
 type is a subtype of this "parent" type.

``abstract`` キーワード新しい抽象型を宣言し、その名前は ``«name»`` により与えられます。
この名前の後にオプションで ``<:`` と既存の型が続き、新しく宣言された抽象型がこの「親」の型のサブタイプであることを示します。

.. 
 When no supertype is given, the default supertype is :obj:`Any` — a
 predefined abstract type that all objects are instances of and all types
 are subtypes of. In type theory, :obj:`Any` is commonly called "top"
 because it is at the apex of the type graph. Julia also has a predefined
 abstract "bottom" type, at the nadir of the type graph, which is written as
 ``Union{}``. It is the exact opposite of :obj:`Any`: no object is an instance
 of ``Union{}`` and all types are supertypes of ``Union{}``.

上位タイプが指定されていない場合、デフォルトの上位タイプは :obj:`Any` となります。これは、全てのオブジェクトがインスタンスであり、
また全ての型がサブタイプである、事前に定義された抽象型です。型の理論では、型グラフの頂点にあるため、 :obj:`Any` は一般に
「トップ」と呼ばれます。またJuliaには、 ``Union{}`` と記述できる、あらかじめ定義された抽象的な「ボトム」型があります。
これは :obj:`Any` とは正反対です。いかなるオブジェクトは ``Union{}`` のインスタンスではなく、
全ての型は ``Union{}`` の上位タイプとなります。

.. 
  Let's consider some of the abstract types that make up Julia's numerical
  hierarchy::

Juliaの数値階層を構成するいくつかの抽象型を考えてみましょう。::

    abstract Number
    abstract Real     <: Number
    abstract AbstractFloat <: Real
    abstract Integer  <: Real
    abstract Signed   <: Integer
    abstract Unsigned <: Integer

.. 
  The :obj:`Number` type is a direct child type of :obj:`Any`, and :obj:`Real` is
  its child. In turn, :obj:`Real` has two children (it has more, but only two
  are shown here; we'll get to the others later): :class:`Integer` and
  :class:`AbstractFloat`, separating the world into representations of integers and
  representations of real numbers. Representations of real numbers
  include, of course, floating-point types, but also include other types,
  such as rationals. Hence, :class:`AbstractFloat` is a proper subtype of
  :obj:`Real`, including only floating-point representations of real numbers.
  Integers are further subdivided into :obj:`Signed` and :obj:`Unsigned`
  varieties.

:obj:`Number` 型は :obj:`Any` の直接的な子の型であり、 :obj:`Real` はその子です。次に、 :obj:`Real` は2つの子をとります
（実際にはもっとありますが、その他の子については別の場所で説明します）。 :class:`Integer` 、 :class:`AbstractFloat` が
それに当たります。これらは世界を整数の表現と実数の表現に分けます。実数の表現には、もちろん浮動小数点型が含まれますが、
有理数のような他の型も含まれます。したがって、 :class:`AbstractFloat` は、実数の浮動小数点表現だけを含む、
:obj:`Real` の適切なサブタイプです。整数はさらに :obj:`Signed` および :obj:`Unsigned` に細分化されます。

.. 
  The ``<:`` operator in general means "is a subtype of", and, used in
  declarations like this, declares the right-hand type to be an immediate
  supertype of the newly declared type. It can also be used in expressions
  as a subtype operator which returns ``true`` when its left operand is a
  subtype of its right operand:

``<:`` 演算子は一般的には「〜は〜のサブタイプ」を意味し、右側の型は新しく宣言された型の直接の
上位タイプであることを宣言する場合などに使用されます。また、左辺の被演算子が右辺の被演算子の
サブタイプである場合に ``true`` を返すサブタイプ演算子としての式で使用することもできます。

.. doctest::

    julia> Integer <: Number
    true

    julia> Integer <: AbstractFloat
    false

.. 
  An important use of abstract types is to provide default implementations for
  concrete types. To give a simple example, consider::

抽象型の重要な用途は、具体型のデフォルトの実装を提供することです。簡単な例を挙げてみましょう。::

    function myplus(x,y)
        x+y
    end

.. 
  The first thing to note is that the above argument declarations are equivalent
  to ``x::Any`` and ``y::Any``. When this function is invoked, say as
  ``myplus(2,5)``, the dispatcher chooses the most specific method named
  ``myplus`` that matches the given arguments. (See :ref:`man-methods` for more
  information on multiple dispatch.)

最初の注意点は、上記の引数の宣言は ``x::Any`` および ``y::Any`` と同等であるということです。
例えばこの関数が ``myplus(2,5)`` として呼び出されると、ディスパッチャは指定された引数に一致する
特定のメソッド ``myplus`` を選択します。多重ディスパッチに関する詳細は :ref:`man-メソッド` を参照してください。

.. 
  Assuming no method more specific than the above is found, Julia next internally
  defines and compiles a method called ``myplus`` specifically for two :class:`Int`
  arguments based on the generic function given above, i.e., it implicitly
  defines and compiles::

上記よりも明確なメソッドが見つからないケースを仮定すると、Juliaは内部的に上記の汎用関数に基づいて、
2つの :class:`Int` 引数に対して ``myplus`` というメソッドを内部的に定義してコンパイルします。
言い換えれば、暗黙的に定義しコンパイルします。::

    function myplus(x::Int,y::Int)
        x+y
    end

.. 
  and finally, it invokes this specific method.

最終的に、この特定のメソッドを呼び出します。

.. 
  Thus, abstract types allow programmers to write generic functions that can
  later be used as the default method by many combinations of concrete types.
  Thanks to multiple dispatch, the programmer has full control over whether the
  default or more specific method is used.

したがって、抽象型を使用することで、プログラマは後にデフォルトのメソッドとして使用できる汎用関数を
具体型の組み合わせにより記述できます。多重ディスパッチのおかげで、プログラマは、デフォルトのメソッドまたは
特定のメソッドを使用するかどうかを完全に制御することができます。

.. 
  An important point to note is that there is no loss in performance if the
  programmer relies on a function whose arguments are abstract types, because it
  is recompiled for each tuple of argument concrete types with which it is
  invoked. (There may be a performance issue, however, in the case of function
  arguments that are containers of abstract types; see :ref:`man-performance-tips`.)

注意すべき重要な点は、どれが呼び出されるかという引数の具体型の各タプルに対して再コンパイルされるため、
プログラマが引数が抽象型である関数に依存した場合、パフォーマンスに損失がないことです。
しかし、抽象型のコンテナである関数の引数の場合は、パフォーマンスの問題がある可能性があります。
詳細は :ref:`man-パフォーマスの助言` を参照してください。

.. 
  Bits Types
  ----------

ビット型
----------

.. 
  A bits type is a concrete type whose data consists of plain old bits.
  Classic examples of bits types are integers and floating-point values.
  Unlike most languages, Julia lets you declare your own bits types,
  rather than providing only a fixed set of built-in bits types. In fact,
  the standard bits types are all defined in the language itself::

ビット型は、データが平易な古いビットで構成される具象型です。ビット型の古典的な例は、整数と浮動小数点値です。
多くの言語とは異なり、Juliaでは、固定的なビルトインのビット型のみを提供するのではなく、
オリジナルのビット型を宣言することができます。実際、標準のビット型はすべて言語自体に定義されています。::

    bitstype 16 Float16 <: AbstractFloat
    bitstype 32 Float32 <: AbstractFloat
    bitstype 64 Float64 <: AbstractFloat

    bitstype 8  Bool <: Integer
    bitstype 32 Char

    bitstype 8  Int8     <: Signed
    bitstype 8  UInt8    <: Unsigned
    bitstype 16 Int16    <: Signed
    bitstype 16 UInt16   <: Unsigned
    bitstype 32 Int32    <: Signed
    bitstype 32 UInt32   <: Unsigned
    bitstype 64 Int64    <: Signed
    bitstype 64 UInt64   <: Unsigned
    bitstype 128 Int128  <: Signed
    bitstype 128 UInt128 <: Unsigned

.. 
  The general syntaxes for declaration of a ``bitstype`` are::

``bitstype`` の宣言の一般的な構文は次の通りです。::

    bitstype «bits» «name»
    bitstype «bits» «name» <: «supertype»

.. 
  The number of bits indicates how much storage the type requires and the
  name gives the new type a name. A bits type can optionally be declared
  to be a subtype of some supertype. If a supertype is omitted, then the
  type defaults to having :obj:`Any` as its immediate supertype. The
  declaration of :obj:`Bool` above therefore means that a boolean value takes
  eight bits to store, and has :class:`Integer` as its immediate supertype.
  Currently, only sizes that are multiples of 8 bits are supported.
  Therefore, boolean values, although they really need just a single bit,
  cannot be declared to be any smaller than eight bits.

ビット数は、型が必要とするストレージの量を示し、nameは新しい型に名前を与えます。
ビット型は、オプションで上位タイプのサブタイプとして宣言することができます。
上位タイプが省略されている場合、その型のデフォルトは、直接の上位タイプとして :obj:`Any` を持ちます。
したがって、上記の :obj:`Bool` の宣言は、ブール値は格納するのに8ビットを要し、
:class:`Integer` を直接の上位タイプとして持つことを意味します。現在、8ビットの倍数であるサイズのみ
サポートされています。したがって、ブール値は実際には1ビットのみ必要ですが、
8ビットより小さく宣言することはできません。

.. 
  The types :obj:`Bool`, :class:`Int8` and :class:`UInt8` all have identical
  representations: they are eight-bit chunks of memory. Since Julia's type
  system is nominative, however, they are not interchangeable despite
  having identical structure. Another fundamental difference between them
  is that they have different supertypes: :obj:`Bool`'s direct supertype is
  :class:`Integer`, :class:`Int8`'s is :obj:`Signed`, and :class:`UInt8`'s is :obj:`Unsigned`.
  All other differences between :obj:`Bool`, :class:`Int8`, and :class:`UInt8` are
  matters of behavior — the way functions are defined to act when given
  objects of these types as arguments. This is why a nominative type
  system is necessary: if structure determined type, which in turn
  dictates behavior, then it would be impossible to make :obj:`Bool` behave any
  differently than :class:`Int8` or :class:`UInt8`.

:obj:`Bool` 、 :class:`Int8` および :class:`UInt8` は全て同じ表現を持ち、8ビットのメモリの固まりです。
しかし、Juliaの型システムは名目上のものであるため、同じ構造を持っていても互換性はありません。
もう一つの根本的な違いは、それぞれが異なる上位タイプを持つ点です。 :obj:`Bool` の直接的な上位タイプは :class:`Integer` 、
:class:`Int8` では :obj:`Signed` 、 :class:`UInt8` の直接的な上位タイプは :obj:`Unsigned` となります。
:obj:`Bool` 、 :class:`Int8` および :class:`UInt8` の間のその他の違いは全て、動作（引数としての型のオブジェクトが
与えられた際にどのように動作するか定義される方法）の問題です。これは、名目上の型システムが必要な理由です。
もし構造体が型を決定し、それが振る舞いを定義する場合、 :obj:`Bool` を :class:`Int8` や :class:`UInt8` とは
異なるように動作させることは不可能です。

.. _man-composite-types:

.. 
  Composite Types
  ---------------

コンポジット型
---------------

.. 
  `Composite types <https://en.wikipedia.org/wiki/Composite_data_type>`_
  are called records, structures (``struct``\ s in C), or objects in various
  languages. A composite type is a collection of named fields, an instance
  of which can be treated as a single value. In many languages, composite
  types are the only kind of user-definable type, and they are by far the
  most commonly used user-defined type in Julia as well.

`コンポジット型 <https://en.wikipedia.org/wiki/Composite_data_type>`_ は、様々な言語ではレコード、
構造体（C言語では ``struct`` ）、またはオブジェクトと呼ばれています。
コンポジット型は、名前付きフィールドのコレクションであり、インスタンスは1つの値として扱うことができます。
多くの言語では、コンポジット型はユーザ定義可能な唯一の型であり、Juliaでも最も一般的に使用されるユーザ定義可能な型です。

.. 
  In mainstream
  object oriented languages, such as C++, Java, Python and Ruby, composite
  types also have named functions associated with them, and the
  combination is called an "object". In purer object-oriented languages,
  such as Ruby or Smalltalk, all values are objects whether they are
  composites or not. In less pure object oriented languages, including C++
  and Java, some values, such as integers and floating-point values, are
  not objects, while instances of user-defined composite types are true
  objects with associated methods. In Julia, all values are objects,
  but functions are not bundled with the objects they
  operate on. This is necessary since Julia chooses which method of a
  function to use by multiple dispatch, meaning that the types of *all* of
  a function's arguments are considered when selecting a method, rather
  than just the first one (see :ref:`man-methods` for more
  information on methods and dispatch). Thus, it would be inappropriate
  for functions to "belong" to only their first argument. Organizing
  methods into function objects rather than having
  named bags of methods "inside" each object ends up being a highly
  beneficial aspect of the language design.

C++、Java、Python、Rubyなどの主流なオブジェクト指向言語では、コンポジット型にもそれらに関連付けられた
名前付き関数があり、その組み合わせを「オブジェクト」と呼びます。RubyやSmalltalkのようなより純粋な
オブジェクト指向言語では、全ての値はコンポジットであろうとなかろうとオブジェクトです。
C++やJavaなどの純粋ではないオブジェクト指向言語では、整数や浮動小数点値などの一部の値はオブジェクトではなく、
一方でユーザー定義のコンポジット型のインスタンスは、関連するメソッドを持つ真のオブジェクトです。
Juliaでは、全ての値がオブジェクトですが、関数は操作対象のオブジェクトに拘束されません。
これは、Juliaが多重ディスパッチによりどの関数のメソッドを使用するのか選択するため必要となります。
これは、最初のものだけでなく、メソッドを選択するときに関数の引数の全ての型が考慮されることを意味します
（メソッドおよびディスパッチの詳細については :ref:`man-メソッド` を参照してください）。したがって、
関数が最初の引数だけに属すことは不適切です。各オブジェクトの「内部」に名前のついたバッグを持たせるのではなく、
メソッドを関数オブジェクトに構成することは、言語設計において非常に有益な側面になります。

.. 
  Since composite types are the most common form of user-defined concrete
  type, they are simply introduced with the ``type`` keyword followed by a
  block of field names, optionally annotated with types using the ``::``
  operator:

コンポジット型はユーザー定義の具体型の最も一般的な形式であるため、フィールド名のブロックが続く
``type`` キーワードにより宣言され、オプションで ``::`` 演算子を使用する型の注釈を付けることができます。

.. doctest::

    julia> type Foo
               bar
               baz::Int
               qux::Float64
           end

.. 
  Fields with no type annotation default to :obj:`Any`, and can accordingly
  hold any type of value.

型注釈のないフィールドは、デフォルトで :obj:`Any` となり、任意の型の値を保持することができます。

.. 
  New objects of composite type ``Foo`` are created by applying the
  ``Foo`` type object like a function to values for its fields:

コンポジット型 ``Foo`` の新しいオブジェクトは、フィールドの値に対する関数のような ``Foo`` 型オブジェクトを
適用することで作成されます。

.. doctest::

    julia> foo = Foo("Hello, world.", 23, 1.5)
    Foo("Hello, world.",23,1.5)

    julia> typeof(foo)
    Foo

.. 
  When a type is applied like a function it is called a *constructor*.
  Two constructors are generated automatically (these are called *default
  constructors*). One accepts any arguments and calls :func:`convert` to convert
  them to the types of the fields, and the other accepts arguments that
  match the field types exactly. The reason both of these are generated is
  that this makes it easier to add new definitions without inadvertently
  replacing a default constructor.

型が関数のように適用される場合、型はコンストラクタと呼ばれます。2つのコンストラクタは自動的に生成されます
（これらはデフォルトコンストラクタと呼ばれます）。1つはどのような引数も取り、
:func:`convert` を呼び出してフィールドの型に変換します。もう1つはフィールドの型に完全に一致する引数を取ります。
これらの両方が生成される理由は、これにより、不注意にデフォルトのコンストラクタを置き換えることなく、
新しい定義を追加することが容易になるためです。

.. 
  Since the ``bar`` field is unconstrained in type, any value will do.
  However, the value for ``baz`` must be convertible to :class:`Int`:

``bar`` フィールドは型に制限されていないため、どのような値でも対応できます。
しかし、 ``baz`` の値は :class:`Int` に変換可能でなければなりません。

.. doctest::

    julia> Foo((), 23.5, 1)
    ERROR: InexactError()
     in Foo(::Tuple{}, ::Float64, ::Int64) at ./none:2
     ...

.. 
  You may find a list of field names using the ``fieldnames`` function.

``fieldnames`` 関数を使用することで、フィールド名のリストを取得することができます。

.. doctest::

    julia> fieldnames(foo)
    3-element Array{Symbol,1}:
     :bar
     :baz
     :qux

.. 
  You can access the field values of a composite object using the
  traditional ``foo.bar`` notation:

伝統的な ``foo.bar`` 記号を使用することで、コンポジットオブジェクトのフィールド値にアクセスすることができます。

.. doctest::

    julia> foo.bar
    "Hello, world."

    julia> foo.baz
    23

    julia> foo.qux
    1.5

.. 
  You can also change the values as one would expect:
  
期待通りに値を変更することもできます。

.. doctest::

    julia> foo.qux = 2
    2

    julia> foo.bar = 1//2
    1//2

.. 
  Composite types with no fields are singletons; there can be only one
  instance of such types::

フィールドのないコンポジット型は単体であり、そのような型のインスタンスは1つだけです。::

    type NoFields
    end

    julia> is(NoFields(), NoFields())
    true

.. 
  The ``is`` function confirms that the "two" constructed instances of
  ``NoFields`` are actually one and the same. Singleton types are
  described in further detail `below <#man-singleton-types>`_.

``is`` 関数を使用することで、 ``NoFields`` の2つの構築されたインスタンスは実際には1つの同じものであるかということを
確認することができます。単体型については、`以下 <#man-singleton-types>`_ でさらに詳しく説明します。

.. 
  There is much more to say about how instances of composite types are
  created, but that discussion depends on both `Parametric
  Types <#man-parametric-types>`_ and on :ref:`man-methods`, and is
  sufficiently important to be addressed in its own section:
  :ref:`man-constructors`.

コンポジット型のインスタンスがどのように作成されるかについては多くのことが言えますが、
その議論は `パラメータ型 <#man-parametric-types>`_ と :ref:`man-メソッド` の両方に依存するため、
関連する :ref:`man-コンストラクタ` のセクションの中で説明されます。

.. _man-immutable-composite-types:

.. 
  Immutable Composite Types
  -------------------------

不変なコンポジット型
-------------------------

.. 
  It is also possible to define *immutable* composite types by using
  the keyword ``immutable`` instead of ``type``::

``type`` の代わりに ``immutable`` というキーワードを使用することで、
不変なコンポジット型を定義することもできます。::

    immutable Complex
        real::Float64
        imag::Float64
    end

.. 
  Such types behave much like other composite types, except that instances
  of them cannot be modified. Immutable types have several advantages:

このような型は、他のコンポジット型と同じように動作しますが、そのインスタンスは変更できません。
不変型にはいくつかの利点があります。

.. 
  - They are more efficient in some cases. Types like the ``Complex``
    example above can be packed efficiently into arrays, and in some
    cases the compiler is able to avoid allocating immutable objects
    entirely.
  - It is not possible to violate the invariants provided by the
    type's constructors.
  - Code using immutable objects can be easier to reason about.

- これらは場合によってはより効率的です。上記の例のような ``Complex`` 型は、
  効率的に配列にパックすることができ、場合によっては、コンパイラが不変のオブジェクトを
  割り当てることを避けることができます。
- 型のコンストラクタによって提供される不変性を違反することはできません。
- 不変オブジェクトを使用するコードは、推論するのが簡単になります。

.. 
  An immutable object might contain mutable objects, such as arrays, as
  fields. Those contained objects will remain mutable; only the fields of the
  immutable object itself cannot be changed to point to different objects.

不変オブジェクトには、配列などの変更可能なオブジェクトがフィールドとして含まれている場合があります。
これらのフィールドとして含まれているオブジェクトは変更可能なままです。不変オブジェクトそのもののフィールドだけを変更し、
異なるオブジェクトを示すことはできません。

.. 
  A useful way to think about immutable composites is that each instance is
  associated with specific field values --- the field values alone tell
  you everything about the object. In contrast, a mutable object is like a
  little container that might hold different values over time, and so is
  not identified with specific field values. In deciding whether to make a
  type immutable, ask whether two instances with the same field values
  would be considered identical, or if they might need to change independently
  over time. If they would be considered identical, the type should probably
  be immutable.

不変なコンポジットは、それぞれのインスタンスが特定のフィールド値に関連付けられている点が有益です。
フィールド値だけでオブジェクトに関する全てのことがわかります。対照的に、変更可能なオブジェクトは、
時間の経過とともに異なる値を保持する小さなコンテナのようなものであり、フィールド値では特定はできません。
型を不変にするかどうかを決めるには、同じフィールド値を持つ2つのインスタンスが同一であるとみなされるか、
または時間の経過とともに変更する必要があるかどうかを考慮します。それらが同一であると考えられる場合、
その型は不変であるべきです。

.. 
  To recap, two essential properties define immutability
  in Julia:

  * An object with an immutable type is passed around (both in assignment
    statements and in function calls) by copying, whereas a mutable type is
    passed around by reference.

  * It is not permitted to modify the fields of a composite immutable
    type.

要約すると、Juliaにおける不変性は以下2つの重要な特性により定義されています。

* 不変型のオブジェクトは、コピーによって代入文と関数呼び出しの両方で渡されますが、
  変更可能な型は参照により渡しされます。

* 不変コンポジット型のフィールドを変更することはできません。

.. 
  It is instructive, particularly for readers whose background is C/C++, to consider
  why these two properties go hand in hand.  If they were separated,
  i.e., if the fields of objects passed around by copying could be modified,
  then it would become more difficult to reason about certain instances of generic code.  For example,
  suppose ``x`` is a function argument of an abstract type, and suppose that the function
  changes a field: ``x.isprocessed = true``.  Depending on whether ``x`` is passed by copying
  or by reference, this statement may or may not alter the actual argument in the
  calling routine.  Julia
  sidesteps the possibility of creating functions with unknown effects in this
  scenario by forbidding modification of fields
  of objects passed around by copying.

CまたはC ++のバックグラウンドを持つ読者にとっては、なぜこれらの2つのプロパティが
連携しているのかを考えるのは有益なことです。それら2つのプロパティが分かれている場合、
例えばコピーによって渡されたオブジェクトのフィールドが変更可能な場合、ジェネリックコードの
特定のインスタンスを推測することはより困難になります。例えば、 ``x`` が抽象型の関数引数であり、
関数がフィールドを変更し、 ``x.isprocessed = true`` とします。コピーまたは参照の
どちらによって ``x`` が渡されるかに応じて、このステートメントは呼び出し側ルーチンの実際の
引数を変更する場合と変更しない場合があります。Juliaは、コピーによって渡されたオブジェクトの
フィールドの変更を禁止することで、このシナリオにおいて不明な効果を持つ関数を作成する可能性を回避します。

.. 
  Declared Types
  --------------


宣言型
--------------

.. 
  The three kinds of types discussed in the previous three sections
  are actually all closely related. They share the same key properties:

  - They are explicitly declared.
  - They have names.
  - They have explicitly declared supertypes.
  - They may have parameters.

前述のセクションで説明した3種類の型は、実際は全て密接に関連しています。
それらは同じ重要な特性を共有しています。

- それらは明示的に宣言されています。
- それらは名前を持ちます。
- それらは明示的に上位タイプを宣言しています。
- それらはパラメータを持つことができます。

.. 
  Because of these shared properties, these types are internally
  represented as instances of the same concept, :obj:`DataType`, which
  is the type of any of these types:

これらの共有の特性のため、これらの型は内部的に同じコンセプトのインスタンスであり、
３種類の型のいずれかの型である :obj:`DataType` として表されます。

.. doctest::

    julia> typeof(Real)
    DataType

    julia> typeof(Int)
    DataType

.. 
  A :obj:`DataType` may be abstract or concrete. If it is concrete, it
  has a specified size, storage layout, and (optionally) field names.
  Thus a bits type is a :obj:`DataType` with nonzero size, but no field
  names. A composite type is a :obj:`DataType` that has field names or
  is empty (zero size).

:obj:`DataType` は、抽象または具体型のどちらに使用することができます。具体型の場合、指定されたサイズ、
記憶域レイアウト、オプションでフィールド名を持ちます。したがって、ビット型は、
サイズがゼロ以外のフィールド名を持たない :obj:`DataType` です。コンポジット型は、フィールド名を持つ、
または空（ゼロサイズ）である :obj:`DataType` です。

.. 
  Every concrete value in the system is an instance of some :obj:`DataType`.

システムのあらゆる具体値は、なんらかの :obj:`DataType` のインスタンスです。

.. 
  Type Unions
  -----------

共用体
-----------

.. 
  A type union is a special abstract type which includes as objects all
  instances of any of its argument types, constructed using the special
  ``Union`` function::

共用体は特殊な抽象型であり、特殊な ``Union`` 関数を使用して構築され、引数型の全てのインスタンスをオブジェクトとして含みます。::

    julia> IntOrString = Union{Int,AbstractString}
    Union{AbstractString,Int64}

    julia> 1 :: IntOrString
    1

    julia> "Hello!" :: IntOrString
    "Hello!"

    julia> 1.0 :: IntOrString
    ERROR: type: typeassert: expected Union{AbstractString,Int64}, got Float64

.. 
  The compilers for many languages have an internal union construct for
  reasoning about types; Julia simply exposes it to the programmer.

多くの言語のコンパイラには、型を推測のための内部構造がありますが、Juliaはそれをプログラマに任せています。

.. _man-parametric-types:

.. 
  Parametric Types
  ----------------

パラメータ型
----------------

.. 
  An important and powerful feature of Julia's type system is that it is
  parametric: types can take parameters, so that type declarations
  actually introduce a whole family of new types — one for each possible
  combination of parameter values. There are many languages that support
  some version of `generic
  programming <https://en.wikipedia.org/wiki/Generic_programming>`_, wherein
  data structures and algorithms to manipulate them may be specified
  without specifying the exact types involved. For example, some form of
  generic programming exists in ML, Haskell, Ada, Eiffel, C++, Java, C#,
  F#, and Scala, just to name a few. Some of these languages support true
  parametric polymorphism (e.g. ML, Haskell, Scala), while others support
  ad-hoc, template-based styles of generic programming (e.g. C++, Java).
  With so many different varieties of generic programming and parametric
  types in various languages, we won't even attempt to compare Julia's
  parametric types to other languages, but will instead focus on
  explaining Julia's system in its own right. We will note, however, that
  because Julia is a dynamically typed language and doesn't need to make
  all type decisions at compile time, many traditional difficulties
  encountered in static parametric type systems can be relatively easily
  handled.

Juliaの型システムの重要かつ強力な機能は、パラメータを取ることができる点です。型はパラメータを取ることができるため、
型宣言は新しい型の集合の導入を行います。 `汎用プログラミング <https://en.wikipedia.org/wiki/Generic_programming>`_ の
いくつかのバージョンをサポートする多くの言語があり、
これらの言語を操作するデータ構造とアルゴリズムは、正確な型を指定することなく指定することができます。
例えば、ML、Haskell、Ada、Eiffel、C ++、Java、C＃、F＃、およびScalaには、
いくつかの汎用プログラミング形式が存在します。これらの言語の中には真のパラメトリック多様型を
サポートするもの（ML、Haskell、Scala）もあれば、一時的なテンプレートベースの汎用プログラミングの
スタイルをサポートするもの（C++、Javaなど）もあります。様々な言語の汎用プログラミングと
パラメータ型が多くありますが、ここではJuliaのパラメータ型と他の言語との比較はせず、
代わりにJuliaのシステムを説明することに専念します。しかし、Juliaは動的型言語であり、
コンパイル時に全ての型決定を行う必要がないため、静的パラメータ型システムで発生する従来の多くの問題を、比較的簡単に処理できる点は記しておきます。

.. 
  All declared types (the :obj:`DataType` variety) can be parameterized, with
  the same syntax in each case. We will discuss them in the following
  order: first, parametric composite types, then parametric abstract
  types, and finally parametric bits types.

全ての宣言された型（ :obj:`DataType` の派生）は、それぞれの場合に同じ構文でパラメータ化することができます。
以下では、パラメータコンポジット型、パラメータ抽象型、パラメータビット型の順に説明します。

.. 
  Parametric Composite Types
  ~~~~~~~~~~~~~~~~~~~~~~~~~~

パラメータコンポジット型
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. testsetup::

    abstract Pointy{T}
    type Point{T} <: Pointy{T}
        x::T
        y::T
    end

.. 
  Type parameters are introduced immediately after the type name,
  surrounded by curly braces::

型パラメータは、中括弧で囲まれた型名の直後に挿入されます。::

    type Point{T}
        x::T
        y::T
    end

.. 
  This declaration defines a new parametric type, ``Point{T}``, holding
  two "coordinates" of type ``T``. What, one may ask, is ``T``? Well,
  that's precisely the point of parametric types: it can be any type at
  all (or a value of any bits type, actually, although here it's clearly
  used as a type). ``Point{Float64}`` is a concrete type equivalent to the
  type defined by replacing ``T`` in the definition of ``Point`` with
  :class:`Float64`. Thus, this single declaration actually declares an
  unlimited number of types: ``Point{Float64}``, ``Point{AbstractString}``,
  ``Point{Int64}``, etc. Each of these is now a usable concrete type:

この宣言は、 ``T`` 型の2つの「座標」を保持する新しいパラメータ型、 ``Point{T}`` を定義します。
ここで、「 ``T`` とは何か？」と疑問を持つかもしれませんが、それはパラメータ型のポイントであり、
これはどのような型、もしくはビット型の値になることができます（ここでは型として使用されています）。
``Point{Float64}`` は、 ``Point`` の定義の ``T`` を :class:`Float64` に置き換えて定義した型に相当する具体型です。
したがって、この1つの宣言は、実際には ``Point{Float64}`` 、 ``Point{AbstractString}`` および
``Point{Int64}`` など、無制限の数の型を宣言します。それぞれは現在、使用可能な具体型です。

.. doctest::

    julia> Point{Float64}
    Point{Float64}

    julia> Point{AbstractString}
    Point{AbstractString}

..
The type ``Point{Float64}`` is a point whose coordinates are 64-bit
floating-point values, while the type ``Point{AbstractString}`` is a "point"
whose "coordinates" are string objects (see :ref:`man-strings`).
However, ``Point`` itself is also a valid type object:

``Point{Float64}`` 型は座標が64ビットの浮動小数点値であり、一方で ``Point{AbstractString}`` 型は
「座標」が文字列オブジェクト（ :ref:`man-文字列` を参照）です。しかし、 ``Point`` 自体も有効な型オブジェクトです。

.. doctest::

    julia> Point
    Point{T}

.. 
  Here the ``T`` is the dummy type symbol used in the original declaration
  of ``Point``. What does ``Point`` by itself mean? It is a type
  that contains all the specific instances ``Point{Float64}``,
  ``Point{AbstractString}``, etc.:

ここでの ``T`` は ``Point`` のオリジナルの宣言で使用されたダミーの型シンボルです。 ``Point`` 自体は、
全ての特定のインスタンス ``Point{Float64}`` 、 ``Point{AbstractString}`` などを含む型です。

.. doctest::

    julia> Point{Float64} <: Point
    true

    julia> Point{AbstractString} <: Point
    true

.. 
  Other types, of course, are not subtypes of it:

もちろん、他の型は「Point」のサブタイプではありません。

.. doctest::

    julia> Float64 <: Point
    false

    julia> AbstractString <: Point
    false

.. 
  Concrete ``Point`` types with different values of ``T`` are never
  subtypes of each other:

``T`` の値が異なる具体型 ``Point`` は、決してお互いのサブタイプではありません。

.. doctest::

    julia> Point{Float64} <: Point{Int64}
    false

    julia> Point{Float64} <: Point{Real}
    false

.. 
  This last point is very important:

    - **Even though** ``Float64 <: Real`` **we DO NOT have**
      ``Point{Float64} <: Point{Real}``\ **.**

この最後の点は非常に重要です。

- **たとえ** ``Float64 <: Real`` **の場合でも、以下とはなりません。**
  ``Point{Float64} <: Point{Real}``\ **.**  

.. 
  In other words, in the parlance of type theory, Julia's type parameters
  are *invariant*, rather than being covariant (or even contravariant).
  This is for practical reasons: while any instance of ``Point{Float64}``
  may conceptually be like an instance of ``Point{Real}`` as well, the two
  types have different representations in memory:

  -  An instance of ``Point{Float64}`` can be represented compactly and
     efficiently as an immediate pair of 64-bit values;
  -  An instance of ``Point{Real}`` must be able to hold any pair of
     instances of :obj:`Real`. Since objects that are instances of :obj:`Real`
     can be of arbitrary size and structure, in practice an instance of
     ``Point{Real}`` must be represented as a pair of pointers to
     individually allocated :obj:`Real` objects.

言い換えれば、型理論的な言い回しにおいて、Juliaの型パラメータは、共変（または反変）ではなく、
むしろ不変です。これは実用的な理由によるものです。 ``Point{Float64}`` のインスタンスは概念的には
``Point{Real}`` のインスタンスのようなものかもしれませんが、一方で2つの型はメモリ内で異なる表現を持ちます。

-  ``Point{Float64}`` のインスタンスは、64ビット値の直接のペアとしてコンパクトかつ効率的に表現できます。
-  ``Point{Real}`` のインスタンスは、 :obj:`Real` のインスタンスのペアを保持できる必要があります。
   :obj:`Real` のインスタンスであるオブジェクトは任意のサイズと構造にできるため、 ``Point{Real}`` のインスタンスは
   個別に割り当てられた :obj:`Real` オブジェクトのポインタのペアとして表現されなければなりません。

.. 
  The efficiency gained by being able to store ``Point{Float64}`` objects
  with immediate values is magnified enormously in the case of arrays: an
  ``Array{Float64}`` can be stored as a contiguous memory block of 64-bit
  floating-point values, whereas an ``Array{Real}`` must be an array of
  pointers to individually allocated :obj:`Real` objects — which may well be
  `boxed <https://en.wikipedia.org/wiki/Object_type_%28object-oriented_programming%29#Boxing>`_
  64-bit floating-point values, but also might be arbitrarily large,
  complex objects, which are declared to be implementations of the
  :obj:`Real` abstract type.

``Point{Float64}`` オブジェクトを直接の値と格納できることによって得られる効率性は、配列の場合に最大化します。
``Array{Float64}`` は64ビット浮動小数点値の連続したメモリブロックとして格納できますが、 ``Array{Real}`` は
個別に割り当てられた :obj:`Real` オブジェクトへのポインタの配列でなければなりません。
個別に割り当てられた :obj:`Real` オブジェクトは、64ビット浮動小数点値の集まりであることもできますが、
:obj:`Real` 抽象型の実装であると宣言されている任意に大きな複雑なオブジェクトであることもできます。

.. 
  Since ``Point{Float64}`` is not a subtype of ``Point{Real}``, the following method can't be applied to arguments of type ``Point{Float64}``::

``Point{Float64}`` は ``Point{Real}`` のサブタイプではないため、 ``Point{Float64}`` 型の引数に次のメソッドを適用することはできません。::

    function norm(p::Point{Real})
       sqrt(p.x^2 + p.y^2)
    end

.. 
  The correct way to define a method that accepts all arguments of type ``Point{T}`` where ``T`` is a subtype of ``Real`` is::

``T`` が ``Real`` のサブタイプである ``Point{T}`` 型の全ての引数を受け入れるメソッドを定義する正しい方法は、次の通りです。::

    function norm{T<:Real}(p::Point{T})
       sqrt(p.x^2 + p.y^2)
    end

.. 
  More examples will be discussed later in :ref:`man-methods`.

その他の例については、後の :ref:`man-メソッド` セクションで説明します。


.. 
  How does one construct a ``Point`` object? It is possible to define
  custom constructors for composite types, which will be discussed in
  detail in :ref:`man-constructors`, but in the absence of any
  special constructor declarations, there are two default ways of creating
  new composite objects, one in which the type parameters are explicitly
  given and the other in which they are implied by the arguments to the
  object constructor.

``Point`` オブジェクトはどのように構築されるのでしょうか。コンポジット型のカスタムコンストラクタを
定義することは可能であり、その方法は :ref:`man-コンストラクタ` で詳しく説明します。特別なコンストラクタ宣言がない場合、
新しいコンポジットオブジェクトを作成するデフォルトの方法が2つあります。1つは型パラメータを明示的に与える方法と、
もう一つはオブジェクトコンストラクタへの引数によって暗示的に与える方法です。

.. 
  Since the type ``Point{Float64}`` is a concrete type equivalent to
  ``Point`` declared with :class:`Float64` in place of ``T``, it can be applied
  as a constructor accordingly:

``Point{Float64}`` 型は、 ``T`` の代わりに「Float64」で宣言された ``Point`` に相当する具体型であるため、
それに応じてコンストラクタとして適用することができます。

.. doctest::

    julia> Point{Float64}(1.0,2.0)
    Point{Float64}(1.0,2.0)

    julia> typeof(ans)
    Point{Float64}

.. 
  For the default constructor, exactly one argument must be supplied for
  each field:

デフォルトコンストラクタでは、各フィールドに1つの引数を指定する必要があります。

.. doctest::

    julia> Point{Float64}(1.0)
    ERROR: MethodError: Cannot `convert` an object of type Float64 to an object of type Point{Float64}
    This may have arisen from a call to the constructor Point{Float64}(...),
    since type constructors fall back to convert methods.
     in Point{Float64}(::Float64) at ./sysimg.jl:53
     ...

    julia> Point{Float64}(1.0,2.0,3.0)
    ERROR: MethodError: no method matching Point{Float64}(::Float64, ::Float64, ::Float64)
    Closest candidates are:
      Point{Float64}{T}(::Any, ::Any) at none:3
      Point{Float64}{T}(::Any) at sysimg.jl:53
     ...

.. 
  Only one default constructor is generated for parametric types, since
  overriding it is not possible. This constructor accepts any arguments
  and converts them to the field types.

オーバーライドできないため、パラメータ型に対しては、デフォルトのコンストラクタは1つだけ生成されます。
このコンストラクタはあらゆる引数を受け取り、それらをフィールド型に変換します。

.. 
  In many cases, it is redundant to provide the type of ``Point`` object
  one wants to construct, since the types of arguments to the constructor
  call already implicitly provide type information. For that reason, you
  can also apply ``Point`` itself as a constructor, provided that the
  implied value of the parameter type ``T`` is unambiguous:

多くの場合、コンストラクタ呼び出しの引数の型はすでに暗黙的に型情報を持っているため、
作成したい ``Point`` オブジェクトの型を指定することは冗長的です。
そのため、パラメータ型 ``T`` の非明示的な値が明確であれば、
``Point`` 自体をコンストラクタとして適用することもできます。

.. doctest::

    julia> Point(1.0,2.0)
    Point{Float64}(1.0,2.0)

    julia> typeof(ans)
    Point{Float64}

    julia> Point(1,2)
    Point{Int64}(1,2)

    julia> typeof(ans)
    Point{Int64}

.. 
  In the case of ``Point``, the type of ``T`` is unambiguously implied if
  and only if the two arguments to ``Point`` have the same type. When this
  isn't the case, the constructor will fail with a :exc:`MethodError`:

``Point`` の場合、 ``T`` の型は、 ``Point`` への2つの引数が同じ型を持つ場合に限り、明確に示唆されます。
そうでない場合、コンストラクタは :exc:`MethodError` で処理に失敗します。

.. doctest::

    julia> Point(1,2.5)
    ERROR: MethodError: no method matching Point{T}(::Int64, ::Float64)
    ...

.. 
  Constructor methods to appropriately handle such mixed cases can be
  defined, but that will not be discussed until later on in
  :ref:`man-constructors`.

このような混合したケースを適切に処理するためのコンストラクタメソッドは定義可能ですが、
後の :ref:`man-コンストラクタ` で説明されます。

.. 
  Parametric Abstract Types
  ~~~~~~~~~~~~~~~~~~~~~~~~~

パラメータ抽象型
~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  Parametric abstract type declarations declare a collection of abstract
  types, in much the same way::

パラメトリック抽象型宣言は、ほぼ同じ方法で抽象型のコレクションを宣言します。::

    abstract Pointy{T}

.. 
  With this declaration, ``Pointy{T}`` is a distinct abstract type for
  each type or integer value of ``T``. As with parametric composite types,
  each such instance is a subtype of ``Pointy``:

この宣言では、 ``Pointy{T}`` は、 ``T`` の各型または整数値の異なる抽象型です。パラメータコンポジット型と同様に、
そのようなインスタンスはそれぞれ ``Pointy`` のサブタイプです。

.. doctest::

    julia> Pointy{Int64} <: Pointy
    true

    julia> Pointy{1} <: Pointy
    true

.. 
  Parametric abstract types are invariant, much as parametric composite
  types are:

パラメータ抽象型は、パラメータコンポジット型と同じように不変です。

.. doctest::

    julia> Pointy{Float64} <: Pointy{Real}
    false

    julia> Pointy{Real} <: Pointy{Float64}
    false

.. 
  Much as plain old abstract types serve to create a useful hierarchy of
  types over concrete types, parametric abstract types serve the same
  purpose with respect to parametric composite types. We could, for
  example, have declared ``Point{T}`` to be a subtype of ``Pointy{T}`` as
  follows::

通常の抽象型は、具体型よりも型の有用な階層を作成するのに役立ちますが、パラメータ抽象型はパラメータコンポジット型に関して、
同じ目的を果たします。例えば、 ``Point{T}`` を ``Pointy{T}`` のサブタイプにするには、次のように宣言できます。::

    type Point{T} <: Pointy{T}
        x::T
        y::T
    end

.. 
  Given such a declaration, for each choice of ``T``, we have ``Point{T}``
  as a subtype of ``Pointy{T}``:

このような宣言の場合、 ``T`` の各選択に対して、 ``Point{T}`` を ``Pointy{T}`` のサブタイプとして持ちます。

.. doctest::

    julia> Point{Float64} <: Pointy{Float64}
    true

    julia> Point{Real} <: Pointy{Real}
    true

    julia> Point{AbstractString} <: Pointy{AbstractString}
    true

.. 
  This relationship is also invariant:

この関係性も不変です。

.. doctest::

    julia> Point{Float64} <: Pointy{Real}
    false

.. 
  What purpose do parametric abstract types like ``Pointy`` serve?
  Consider if we create a point-like implementation that only requires a
  single coordinate because the point is on the diagonal line *x = y*::

``Pointy`` のようなパラメータ抽象型はどのような役割があるのでしょうか。点が対角線 *x = y* にあるため、
単一の座標だけを必要とする点のような実装を作成した場合を考えてみましょう。::

    type DiagPoint{T} <: Pointy{T}
        x::T
    end

.. 
  Now both ``Point{Float64}`` and ``DiagPoint{Float64}`` are
  implementations of the ``Pointy{Float64}`` abstraction, and similarly
  for every other possible choice of type ``T``. This allows programming
  to a common interface shared by all ``Pointy`` objects, implemented for
  both ``Point`` and ``DiagPoint``. This cannot be fully demonstrated,
  however, until we have introduced methods and dispatch in the next
  section, :ref:`man-methods`.

``Point{Float64}`` と ``DiagPoint{Float64}`` は両方とも ``Pointy{Float64}`` 抽象化の実装であり、
``T`` 型の他の全ての可能な選択についても同様です。これにより、 ``Point`` および ``DiagPoint`` の両方に
実装されたすべての ``Pointy`` オブジェクトで共有される共通インタフェースへのプログラミングが可能になります。
しかし、次のセクション :ref:`man-メソッド` にてメソッドおよびディスパッチの説明をするまで、
これを完全に証明することはできません。

.. 
  There are situations where it may not make sense for type parameters to
  range freely over all possible types. In such situations, one can
  constrain the range of ``T`` like so::

型パラメータがすべての可能な型に自由に及ぶことが意味を成さない場合があります。
このような状況では、 ``T`` の範囲を次のように制限することができます。::

    abstract Pointy{T<:Real}

.. 
  With such a declaration, it is acceptable to use any type that is a
  subtype of :obj:`Real` in place of ``T``, but not types that are not
  subtypes of :obj:`Real`:

このような宣言では、 ``T`` の代わりに :obj:`Real` のサブタイプである型の使用はできますが、
:obj:`Real` のサブタイプではない型は使用できません。

.. testsetup:: real-pointy

    abstract Pointy{T<:Real}

.. doctest:: real-pointy

    julia> Pointy{Float64}
    Pointy{Float64}

    julia> Pointy{Real}
    Pointy{Real}

    julia> Pointy{AbstractString}
    ERROR: TypeError: Pointy: in T, expected T<:Real, got Type{AbstractString}
     ...

    julia> Pointy{1}
    ERROR: TypeError: Pointy: in T, expected T<:Real, got Int64
     ...

.. 
  Type parameters for parametric composite types can be restricted in the
  same manner::

パラメータコンポジット型の型パラメータは、同じ方法で制限できます。::

    type Point{T<:Real} <: Pointy{T}
        x::T
        y::T
    end

.. 
  To give a real-world example of how all this parametric type
  machinery can be useful, here is the actual definition of Julia's
  :obj:`Rational` immutable type (except that we omit the constructor here
  for simplicity), representing an exact ratio of integers::

このパラメータ型の機能がどれほど有用であるかの現実的な例を示すために、
整数の正確な比を表すJuliaの :obj:`Rational` 不変型の実際の定義を紹介します
（ここでは単純化のためにコンストラクタを省略しています）。::

    immutable Rational{T<:Integer} <: Real
        num::T
        den::T
    end

.. 
  It only makes sense to take ratios of integer values, so the parameter
  type ``T`` is restricted to being a subtype of :class:`Integer`, and a ratio
  of integers represents a value on the real number line, so any
  :obj:`Rational` is an instance of the :obj:`Real` abstraction.

整数型の比率を取るのは理にかなっているので、パラメータ型 ``T`` は :class:`Integer` のサブタイプに制限され、
整数の比は数直線上の値を表します。したがって、任意の :obj:`Rational` は :obj:`Real` 抽象化のインスタンスです。

.. 
  Tuple Types
  ~~~~~~~~~~~

タプル型
~~~~~~~~~~~

.. 
  Tuples are an abstraction of the arguments of a function — without the
  function itself. The salient aspects of a function's arguments are their
  order and their types. Therefore a tuple type is similar to a
  parameterized immutable type where each parameter is the type
  of one field. For example, a 2-element tuple type resembles the following
  immutable type::

タプルは関数の引数の抽象化ですが、関数自体はありません。関数の引数の顕著な側面は、その順序と型です。
したがって、タプル型は、各パラメータが1つのフィールドの型であるパラメータ化された不変型に似ています。
例えば、2要素タプル型は、次の不変型に似ています。::

    immutable Tuple2{A,B}
        a::A
        b::B
    end

.. 
  However, there are three key differences:

  - Tuple types may have any number of parameters.
  - Tuple types are *covariant* in their parameters: ``Tuple{Int}`` is a subtype
    of ``Tuple{Any}``. Therefore ``Tuple{Any}`` is considered an abstract type,
    and tuple types are only concrete if their parameters are.
  - Tuples do not have field names; fields are only accessed by index.

しかし、3つの重要な違いがあります。

- タプル型は、任意の数のパラメータを持つことができます。
- タプル型は、そのパラメータにおいて共変です。 ``Tuple{Int}`` は ``Tuple{Any}`` のサブタイプです。
  したがって、 ``Tuple{Any}`` は抽象型とみなされ、タプル型はパラメータがある場合にのみ具体型となります。
- タプルはフィールド名を持ちません。フィールドはインデックスによってのみアクセスされます。

.. 
  Tuple values are written with parentheses and commas. When a tuple is constructed,
  an appropriate tuple type is generated on demand:

タプル値は、括弧とカンマで書かれます。タプルが構築されると、必要に応じて適切なタプル型が生成されます。

.. doctest::

    julia> typeof((1,"foo",2.5))
    Tuple{Int64,String,Float64}

.. 
  Note the implications of covariance:

共変の意味に注意してください。

.. doctest::

    julia> Tuple{Int,AbstractString} <: Tuple{Real,Any}
    true

    julia> Tuple{Int,AbstractString} <: Tuple{Real,Real}
    false

    julia> Tuple{Int,AbstractString} <: Tuple{Real,}
    false

.. 
  Intuitively, this corresponds to the type of a function's arguments
  being a subtype of the function's signature (when the signature matches).

直感的には、これは（署名が一致する場合において）関数の署名のサブタイプである関数の引数に対応します。

.. 
  Vararg Tuple Types
  ~~~~~~~~~~~~~~~~~~

Varargタプル型
~~~~~~~~~~~~~~~~~~

.. 
  The last parameter of a tuple type can be the special type ``Vararg``,
  which denotes any number of trailing elements:

タプル型の最後のパラメータは特殊な型 ``Vararg`` とすることができ、これは任意の数の末尾の要素を表します。

.. doctest::

    julia> isa(("1",), Tuple{AbstractString,Vararg{Int}})
    true

    julia> isa(("1",1), Tuple{AbstractString,Vararg{Int}})
    true

    julia> isa(("1",1,2), Tuple{AbstractString,Vararg{Int}})
    true

    julia> isa(("1",1,2,3.0), Tuple{AbstractString,Vararg{Int}})
    false

.. 
  Notice that ``Vararg{T}`` corresponds to zero or more elements of type ``T``.
  Vararg tuple types are used to represent the arguments accepted by varargs
  methods (see :ref:`man-varargs-functions`).

``Vararg{T}`` が型 ``T`` の0個以上の要素に対応することに注意してください。Varargタプル型は、
varargsメソッドで受け入れられる引数を表すために使用されます（ :ref:`man-varargs関数` を参照）。

.. 
  The type ``Vararg{T,N}`` corresponds to exactly ``N`` elements of type ``T``.  ``NTuple{N,T}`` is
  a convenient alias for ``Tuple{Vararg{T,N}}``, i.e. a tuple type containing exactly
  ``N`` elements of type ``T``.

``Vararg{T,N}`` 型は、型 ``T`` の正確に ``N`` 個の要素に対応します。 ``NTuple{N,T}`` は ``Tuple{Vararg{T,N}}`` の
便利なエイリアスであり、これは型 ``T`` の ``N`` 個の要素を正確に含むタプル型です。


.. _man-singleton-types:

.. 
  Singleton Types
  ^^^^^^^^^^^^^^^

シングルトン型
^^^^^^^^^^^^^^^

.. 
  There is a special kind of abstract parametric type that must be
  mentioned here: singleton types. For each type, ``T``, the "singleton
  type" ``Type{T}`` is an abstract type whose only instance is the object
  ``T``. Since the definition is a little difficult to parse, let's look
  at some examples:

ここで言及しなければならない特別な種類の抽象パラメータ型があります。それはシングルトン型です。
それぞれの型 ``T`` について、「シングルトン型」 ``Type{T}`` は抽象型であり、インスタンスのみが
オブジェクト ``T`` となります。この定義は少し難しいため、いくつかの例を見てみましょう。

.. doctest::

    julia> isa(Float64, Type{Float64})
    true

    julia> isa(Real, Type{Float64})
    false

    julia> isa(Real, Type{Real})
    true

    julia> isa(Float64, Type{Real})
    false

.. 
  In other words, :func:`isa(A,Type{B}) <isa>` is true if and only if ``A`` and
  ``B`` are the same object and that object is a type. Without the
  parameter, :obj:`Type` is simply an abstract type which has all type
  objects as its instances, including, of course, singleton types:

言い換えれば、 :func:`isa(A,Type{B}) <isa>` は、 ``A`` と ``B`` が同じオブジェクトで、そのオブジェクトが型である場合にのみ真です。
パラメータがなければ、 :obj:`Type` は単なる抽象型であり、シングルトン型を含めた全ての型オブジェクトをインスタンスとして持ちます。

.. doctest::

    julia> isa(Type{Float64},Type)
    true

    julia> isa(Float64,Type)
    true

    julia> isa(Real,Type)
    true

.. 
  Any object that is not a type is not an instance of ``Type``:
  
型ではないオブジェクトは、 ``Type`` のインスタンスではありません。

.. doctest::

    julia> isa(1,Type)
    false

    julia> isa("foo",Type)
    false

.. 
  Until we discuss :ref:`man-parametric-methods`
  and :ref:`conversions <man-conversion>`, it is
  difficult to explain the utility of the singleton type construct, but in
  short, it allows one to specialize function behavior on specific type
  *values*. This is useful for writing
  methods (especially parametric ones) whose behavior depends on a type
  that is given as an explicit argument rather than implied by the type of
  one of its arguments.

:ref:`man-パラメータメソッド` と :ref:`変換 <man-変換>` について説明するまで、シングルトン型の
コンストラクタの有用性を説明することは難しいですが、端的に言うと、
特定の型の値に対して関数の動きを特化させることができます。これは、その引数の型によって暗示されるのではなく、
明示的な引数として与えられる型に依存する処理を持つメソッド（特にパラメータを取るもの）を書くのに便利です。

.. 
  A few popular languages have singleton types, including Haskell, Scala
  and Ruby. In general usage, the term "singleton type" refers to a type
  whose only instance is a single value. This meaning applies to Julia's
  singleton types, but with that caveat that only type objects have
  singleton types.

Haskell、Scala、Rubyなどのいくつかの一般的な言語には、シングルトン型があります。
一般的な使用では、「シングルトン型」はインスタンスが単一の値である型を指します。
この意味はJuliaのシングルトン型にも適用されますが、型オブジェクトだけがシングルトン型を持つという点に注意してください。

.. 
  Parametric Bits Types
  ~~~~~~~~~~~~~~~~~~~~~

パラメータビット型
~~~~~~~~~~~~~~~~~~~~~

.. 
  Bits types can also be declared parametrically. For example, pointers
  are represented as boxed bits types which would be declared in Julia
  like this::

ビット型はパラメトリックに宣言することもできます。例えば、ポインタは次のようにJuliaで宣言される
ボックス化されたビット型として表すことができます。::

    # 32-bit system:
    bitstype 32 Ptr{T}

    # 64-bit system:
    bitstype 64 Ptr{T}

.. 
  The slightly odd feature of these declarations as compared to typical
  parametric composite types, is that the type parameter ``T`` is not used
  in the definition of the type itself — it is just an abstract tag,
  essentially defining an entire family of types with identical structure,
  differentiated only by their type parameter. Thus, ``Ptr{Float64}`` and
  ``Ptr{Int64}`` are distinct types, even though they have identical
  representations. And of course, all specific pointer types are subtype
  of the umbrella ``Ptr`` type:

典型的なパラメータコンポジット型と比較して、これらの宣言の少し奇妙な特徴は、
型パラメータ ``T`` が型自体の定義に使用されていない点です。これは、同じ構造を持つ型の集合の全体を
定義する抽象タグであり、型パラメータによってのみ区別されます。したがって、 ``Ptr{Float64}`` と
``Ptr{Int64}`` は、同じ表現を持っていますが、異なる型です。もちろん、全ての特定のポインタ型は、
傘である ``Ptr`` 型のサブタイプです。

.. doctest::

    julia> Ptr{Float64} <: Ptr
    true

    julia> Ptr{Int64} <: Ptr
    true

.. 
  Type Aliases
  ------------

型エイリアス
------------

.. 
  Sometimes it is convenient to introduce a new name for an already
  expressible type. For such occasions, Julia provides the ``typealias``
  mechanism. For example, :class:`UInt` is type aliased to either :class:`UInt32` or
  :class:`UInt64` as is appropriate for the size of pointers on the system::

すでに表現可能である型の新しい名前を導入すると便利な場合があります。
このような場合、Juliaは ``typealias`` を提供します。例えば、 :class:`UInt` は :class:`UInt32` または
:class:`UInt64` のエイリアスであり、システム上のポインタのサイズに適した値を返します。::

    # 32-bit system:
    julia> UInt
    UInt32

    # 64-bit system:
    julia> UInt
    UInt64

.. 
  This is accomplished via the following code in ``base/boot.jl``::

これは、以下の ``base/boot.jl`` のコードにより実現されます。::

    if is(Int,Int64)
        typealias UInt UInt64
    else
        typealias UInt UInt32
    end

.. 
  Of course, this depends on what :class:`Int` is aliased to — but that is
  predefined to be the correct type — either :class:`Int32` or :class:`Int64`.

もちろん、これは :class:`Int` が何のエイリアスがあるかによって異なりますが、
:class:`Int32` または :class:`Int64` のいずれかの正しい型があらかじめ定義されています。

.. 
  For parametric types, ``typealias`` can be convenient for providing
  names for cases where some of the parameter choices are fixed.
  Julia's arrays have type ``Array{T,N}`` where ``T`` is the element type
  and ``N`` is the number of array dimensions. For convenience, writing
  ``Array{Float64}`` allows one to specify the element type without
  specifying the dimension:

パラメータ型では、 ``typealias`` は、いくつかのパラメーターの選択肢が固定されている場合に
名前を提供するのに便利です。Juliaの配列には型 ``Array{T,N}`` があり、 ``T`` は要素の型、
``N`` は配列の次元数を表します。便宜上、 ``Array{Float64}`` と書くことで、
次元を指定せずに要素の型を指定することができます。

.. doctest::

    julia> Array{Float64,1} <: Array{Float64} <: Array
    true

.. 
  However, there is no way to equally simply restrict just the dimension
  but not the element type. Yet, one often needs to ensure an object
  is a vector or a matrix (imposing restrictions on the number of dimensions).
  For that reason, the following type aliases are provided::

しかし、要素のように次元数だけを単純に制限する方法はありません。
ただし、オブジェクトがベクトルまたはマトリクスであることを保証する（次元の数に制限を課す）必要が
ある場合があります。そのため、以下の型のエイリアスが用意されています。::

    typealias Vector{T} Array{T,1}
    typealias Matrix{T} Array{T,2}

.. 
  Writing ``Vector{Float64}`` is equivalent to writing
  ``Array{Float64,1}``, and the umbrella type ``Vector`` has as instances
  all ``Array`` objects where the second parameter — the number of array
  dimensions — is 1, regardless of what the element type is. In languages
  where parametric types must always be specified in full, this is not
  especially helpful, but in Julia, this allows one to write just
  ``Matrix`` for the abstract type including all two-dimensional dense
  arrays of any element type.

``Vector{Float64}`` と書くことは ``Array{Float64,1}`` と書くことと同等であり、傘型 ``Vector`` は、
要素の種類に関係なく、配列の次元数を示す二番目のパラメータが1である全ての ``Array`` オブジェクトを
インスタンスとして持ちます。パラメータ型を常に完全に指定する必要がある言語では、
これは特に有用ではありませんが、Juliaでは、任意の要素型のすべての2次元の高密度な配列を含む抽象型の ``Matrix`` を書くことができます。

.. 
  This declaration of ``Vector`` creates a subtype relation
  ``Vector{Int} <: Vector``.  However, it is not always the case that a parametric
  ``typealias`` statement creates such a relation; for example, the statement::

この ``Vector`` の宣言は、サブタイプの関係 ``Vector{Int} <: Vector`` を作成します。
しかし、以下のステートメントのように、必ずしもパラメータ ``typealias`` ステートメントが
このような関係を作成するとは限りません。::

    typealias AA{T} Array{Array{T,1},1}

.. 
  does not create the relation ``AA{Int} <: AA``.  The reason is that ``Array{Array{T,1},1}`` is not
  an abstract type at all; in fact, it is a concrete type describing a
  1-dimensional array in which each entry
  is an object of type ``Array{T,1}`` for some value of ``T``.

上記の例は ``AA{Int} <: AA`` の関係を作成しません。その理由は、 ``Array{Array{T,1},1}`` は抽象型ではないからです。
これは1次元配列を記述する具体型であり、各エントリは ``T`` の値に対する ``Array{T,1}`` 型のオブジェクトです。

.. 
  Operations on Types
  -------------------

型の操作
-------------------

.. 
  Since types in Julia are themselves objects, ordinary functions can
  operate on them. Some functions that are particularly useful for working
  with or exploring types have already been introduced, such as the ``<:``
  operator, which indicates whether its left hand operand is a subtype of
  its right hand operand.

Juliaの型はそれ自体がオブジェクトであるため、通常の関数は型に対し処理することができます。
左辺の被演算子が右辺の被演算子のサブタイプであるかどうかを示す ``<:`` 演算子など、
型の処理や検索に役立つ関数は既に説明されています。

.. 
  The ``isa`` function tests if an object is of a given type and returns
  true or false:

``isa`` 関数は、オブジェクトが指定された型かどうかをチェックし、trueまたはfalseを返します。

.. doctest::

    julia> isa(1,Int)
    true

    julia> isa(1,AbstractFloat)
    false

.. 
  The :func:`typeof` function, already used throughout the manual in examples,
  returns the type of its argument. Since, as noted above, types are
  objects, they also have types, and we can ask what their types are:

このマニュアルの例の中で既に使用されている :func:`typeof` 関数は、引数の型を返します。上記のように、
型はオブジェクトであるため、型を持ち、型が何であるかを確認することができます。

.. doctest::

    julia> typeof(Rational)
    DataType

    julia> typeof(Union{Real,Float64,Rational})
    DataType

    julia> typeof(Union{Real,String})
    Union

.. 
  What if we repeat the process? What is the type of a type of a type?
  As it happens, types are all composite values and thus all have a type of
  :obj:`DataType`:

もしこの処理を繰り返したらどうなるのでしょうか。ある型の型の型は何になるのでしょうか。
この場合、型は全てコンポジット値であり、 :obj:`DataType` の型を持ちます。

.. doctest::

    julia> typeof(DataType)
    DataType

    julia> typeof(Union)
    DataType

.. 
  :obj:`DataType` is its own type.

:obj:`DataType` はそれ自体の型です。

.. 
  Another operation that applies to some types is :func:`supertype`, which
  reveals a type's supertype.
  Only declared types (:obj:`DataType`) have unambiguous supertypes:

型に適用される別の処理は、型の上位タイプを示す :func:`supertype` です。
宣言された型（ :obj:`DataType` ）のみが曖昧さのない上位タイプを持ちます。

.. doctest::

    julia> supertype(Float64)
    AbstractFloat

    julia> supertype(Number)
    Any

    julia> supertype(AbstractString)
    Any

    julia> supertype(Any)
    Any

.. 
  If you apply :func:`supertype` to other type objects (or non-type objects), a
  :exc:`MethodError` is raised::

:func:`supertype` を他の型オブジェクト（または非型オブジェクト）に適用した場合、 :exc:`MethodError` が発生します。::

    julia> supertype(Union{Float64,Int64})
    ERROR: `supertype` has no method matching supertype(::Type{Union{Float64,Int64}})

.. 
  Custom pretty-printing
  ----------------------

  Often, one wants to customize how instances of a type are displayed.  This
  is accomplished by overloading the :func:`show` function.  For example,
  suppose we define a type to represent complex numbers in polar form:

  .. testcode::

      type Polar{T<:Real} <: Number
          r::T
          Θ::T
      end
      Polar(r::Real,Θ::Real) = Polar(promote(r,Θ)...)

  .. testoutput::
     :hide:

     Polar{T<:Real}

  Here, we've added a custom constructor function so that it can take arguments of
  different ``Real`` types and promote them to a commmon type (see
  :ref:`man-constructors` and :ref:`man-conversion-and-promotion`). (Of course, we
  would have to define lots of other methods, too, to make it act like a
  ``Number``, e.g. ``+``, ``*``, ``one``, ``zero``, promotion rules and so on.) By
  default, instances of this type display rather simply, with information about
  the type name and the field values, as e.g. ``Polar{Float64}(3.0,4.0)``.

  If we want it to display instead as ``3.0 * exp(4.0im)``, we would
  define the following method to print the object to a given output
  object ``io`` (representing a file, terminal, buffer, etcetera; see
  :ref:`man-networking-and-streams`):

  .. testcode::

      Base.show(io::IO, z::Polar) = print(io, z.r, " * exp(", z.Θ, "im)")

  More fine-grained control over display of ``Polar`` objects is possible.
  In particular, sometimes one wants both a verbose multi-line printing
  format, used for displaying a single object in the REPL and other interactive
  environments, and also a more compact single-line format used for :func:`print`
  or for displaying the object as part of another object (e.g. in an array).
  Although by default the ``show(io, z)`` function is called in both cases,
  you can define a *different* multi-line format for displaying an object
  by overloading a three-argument form of ``show`` that takes the ``text/plain``
  MIME type as its second argument (see :ref:`man-multimedia-io`), for example:

  .. testcode::

      Base.show{T}(io::IO, ::MIME"text/plain", z::Polar{T}) =
          print(io, "Polar{$T} complex number:\n   ", z)

  (Note that ``print(..., z)`` here will call the 2-argument ``show(io, z)`` method.)
  This results in:

  .. doctest::

      julia> Polar(3, 4.0)
      Polar{Float64} complex number:
         3.0 * exp(4.0im)

      julia> [Polar(3, 4.0), Polar(4.0,5.3)]
      2-element Array{Polar{Float64},1}:
       3.0 * exp(4.0im)
       4.0 * exp(5.3im)

  where the single-line ``show(io, z)`` form is still used for an array of ``Polar``
  values.   Technically, the REPL calls ``display(z)`` to display the result of
  executing a line, which defaults to ``show(STDOUT, MIME("text/plain"), z)``, which
  in turn defaults to ``show(STDOUT, z)``, but you should *not* define new :func:`display`
  methods unless you are defining a new multimedia display handler
  (see :ref:`man-multimedia-io`).

  Moreover, you can also define ``show`` methods for other MIME types in order
  to enable richer display (HTML, images, etcetera) of objects in environments
  that support this (e.g. IJulia).   For example, we can define formatted
  HTML display of ``Polar`` objects, with superscripts and italics, via:

  .. testcode::

      Base.show{T}(io::IO, ::MIME"text/html", z::Polar{T}) =
          println(io, "<code>Polar{$T}</code> complex number: ",
                  z.r, " <i>e</i><sup>", z.Θ, " <i>i</i></sup>")

  A ``Polar`` object will then display automatically using HTML in an environment
  that supports HTML display, but you can call ``show`` manually to get HTML
  output if you want:

  .. doctest::

     julia> show(STDOUT, "text/html", Polar(3.0,4.0))
     <code>Polar{Float64}</code> complex number: 3.0 <i>e</i><sup>4.0 <i>i</i></sup>

  .. raw:: html

     <p>An HTML renderer would display this as: <code>Polar{Float64}</code> complex number: 3.0 <i>e</i><sup>4.0 <i>i</i></sup></p>

.. _man-val-trick:

.. 
  "Value types"
  -------------

「値型」
-------------

.. 
  In Julia, you can't dispatch on a *value* such as ``true`` or
  ``false``. However, you can dispatch on parametric types, and Julia
  allows you to include "plain bits" values (Types, Symbols,
  Integers, floating-point numbers, tuples, etc.) as type parameters.  A
  common example is the dimensionality parameter in ``Array{T,N}``,
  where ``T`` is a type (e.g., ``Float64``) but ``N`` is just an
  ``Int``.

Juliaでは、 ``true`` や ``false`` などの値をディスパッチすることはできません。
しかし、パラメータ型ではディスパッチすることができ、型、記号、整数、浮動小数点数タプルなど
「プレーンなビット」値を含めることができます。一般的な例は、 ``T`` が ``Float64`` のような型を表し、
``N`` が ``Int`` を表す ``Array{T,N}`` の次元数パラメータです。

.. 
  You can create your own custom types that take values as parameters,
  and use them to control dispatch of custom types. By way of
  illustration of this idea, let's introduce a parametric type,
  ``Val{T}``, which serves as a customary way to exploit this technique
  for cases where you don't need a more elaborate hierarchy.

値をパラメータとして使用する独自の型を作成し、それらを使用して自作の型のディスパッチを制御することができます。
このアイデアを説明するために、パラメータ型の ``Val{T}`` を見てみましょう。
これは、より洗練された階層を必要としない場合にこの手法を活用する一般的な方法として機能します。

.. 
  ``Val`` is defined as::

``Val`` を次のように定義します。::

    immutable Val{T}
    end

.. 
  There is no more to the implementation of ``Val`` than this.  Some
  functions in Julia's standard library accept ``Val`` types as
  arguments, and you can also use it to write your own functions.  For
  example:

これ以上の ``Val`` の実装はありません。Juliaの標準ライブラリのいくつかの関数は、
``Val`` 型を引数として受け付け、それを使って独自の関数を書くこともできます。例えば、

.. testsetup:: value-types

    firstlast(::Type{Val{true}}) = "First";
    firstlast(::Type{Val{false}}) = "Last";

.. doctest:: value-types

    firstlast(::Type{Val{true}}) = "First"
    firstlast(::Type{Val{false}}) = "Last"

    julia> firstlast(Val{true})
    "First"

    julia> firstlast(Val{false})
    "Last"

.. 
  For consistency across Julia, the call site should always pass a
  ``Val`` *type* rather than creating an *instance*, i.e., use
  ``foo(Val{:bar})`` rather than ``foo(Val{:bar}())``.

Juliaの一貫性を保つため、呼び出し側はインスタンスを作成するのではなく、常に ``Val`` 型を受け渡す必要があります。
例えば、 ``foo(Val{:bar}())`` ではなく ``foo(Val{:bar})`` を使用すべきです。

.. 
  It's worth noting that it's extremely easy to mis-use parametric
  "value" types, including ``Val``; in unfavorable cases, you can easily
  end up making the performance of your code much *worse*.  In
  particular, you would never want to write actual code as illustrated
  above.  For more information about the proper (and improper) uses of
  ``Val``, please read the more extensive discussion in :ref:`the
  performance tips <man-performance-val>`.

``Val`` を含むパラメータ「値」型を誤って使用してしまうことは簡単に発生しますが、取るに足りません。
好ましくないケースでは、コードのパフォーマンスを大幅に悪化させることになります。
特に、上記のようなコードを実際の記述することは決して好ましいことではありません。
``Val`` の適切な（そして不適切な）使い方の詳細については、 :ref:`パフォーマンスに関するヒント 
<man-performance-val>` の広範な議論を参照してください。


.. _man-nullable-types:

.. 
  Nullable Types: Representing Missing Values
  -------------------------------------------

null可能な型：欠損値の表現
-------------------------------------------

.. 
  In many settings, you need to interact with a value of type ``T`` that may or
  may not exist. To handle these settings, Julia provides a parametric type
  called ``Nullable{T}``, which can be thought of as a specialized container
  type that can contain either zero or one values. ``Nullable{T}`` provides a
  minimal interface designed to ensure that interactions with missing values
  are safe. At present, the interface consists of four possible interactions:

  - Construct a :obj:`Nullable` object.
  - Check if a :obj:`Nullable` object has a missing value.
  - Access the value of a :obj:`Nullable` object with a guarantee that a
    :exc:`NullException` will be thrown if the object's value is missing.
  - Access the value of a :obj:`Nullable` object with a guarantee that a default
    value of type ``T`` will be returned if the object's value is missing.

多くの設定では、存在が不明確な型 ``T`` の値と向き合う必要があります。これらの設定を処理するために、
Juliaは ``Nullable{T}`` というパラメータ型を提供しています。これは、
ゼロまたは1つの値を持つ特殊なコンテナ型と考えることができます。 ``Nullable{T}`` は、
欠損値とのやり取りが安全であることを保証するように設計された、最小限のインターフェースを提供します。
現在、このインタフェースは4つの対話から構成されています。

- :obj:`Nullable` オブジェクトの構築
- :obj:`Nullable` オブジェクトが欠損値を持っているかの確認
- オブジェクトの値が無い場合に :exc:`NullException` がスローされる保証を持って、
  :obj:`Nullable` オブジェクトの値にアクセス
- オブジェクトの値が無い場合に、型 ``T`` のデフォルト値が返される保証を持って、
  :obj:`Nullable` オブジェクトの値にアクセス

.. 
  Constructing :obj:`Nullable` objects
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:obj:`Nullable` オブジェクトの構築
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  To construct an object representing a missing value of type ``T``, use the
  ``Nullable{T}()`` function:

型 ``T`` の欠損値を表すオブジェクトを作成するには、 ``Nullable{T}()`` 関数を使用します。

.. doctest::

    julia> x1 = Nullable{Int64}()
    Nullable{Int64}()

    julia> x2 = Nullable{Float64}()
    Nullable{Float64}()

    julia> x3 = Nullable{Vector{Int64}}()
    Nullable{Array{Int64,1}}()

.. 
  To construct an object representing a non-missing value of type ``T``, use the
  ``Nullable(x::T)`` function:

型 ``T`` の非欠損値を表すオブジェクトを作成するには、 ``Nullable(x::T)`` 関数を使用します。

.. doctest::

    julia> x1 = Nullable(1)
    Nullable{Int64}(1)

    julia> x2 = Nullable(1.0)
    Nullable{Float64}(1.0)

    julia> x3 = Nullable([1, 2, 3])
    Nullable{Array{Int64,1}}([1,2,3])

.. 
  Note the core distinction between these two ways of constructing a :obj:`Nullable`
  object: in one style, you provide a type, ``T``, as a function parameter; in
  the other style, you provide a single value of type ``T`` as an argument.

:obj:`Nullable` オブジェクトを構築するこれらの2つの方法の重要な違いに注目してください。
一方では、関数のパラメータとして型 ``T`` を指定し、もう一方では、型 ``T`` の単一の値を引数として指定しています。

.. 
  Checking if a :obj:`Nullable` object has a value
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:obj:`Nullable` オブジェクトが値を持っているかの確認
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  You can check if a :obj:`Nullable` object has any value using :func:`isnull`:

:func:`isnull` を使用することで、 :obj:`Nullable` オブジェクトに値があるかどうかを確認することができます。

.. doctest::

    julia> isnull(Nullable{Float64}())
    true

    julia> isnull(Nullable(0.0))
    false

.. 
  Safely accessing the value of a :obj:`Nullable` object
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:obj:`Nullable` オブジェクトの値への安全なアクセス
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. 
  You can safely access the value of a :obj:`Nullable` object using :func:`get`:

:func:`get` を使用することで、 :obj:`Nullable` オブジェクトの値に安全にアクセスすることができます。

.. doctest::

    julia> get(Nullable{Float64}())
    ERROR: NullException()
     in get(::Nullable{Float64}) at ./nullable.jl:62
     ...

    julia> get(Nullable(1.0))
    1.0

.. 
  If the value is not present, as it would be for ``Nullable{Float64}``, a
  :exc:`NullException` error will be thrown. The error-throwing nature of the
  :func:`get` function ensures that any attempt to access a missing value immediately
  fails.

値が存在しない場合、 ``Nullable{Float64}`` の場合と同様に、 :exc:`NullException` エラーが返されます。
:func:`get` 関数のエラースローの性質は、欠損値にアクセスしようとする試みがすぐに失敗することを保証します。

.. 
  In cases for which a reasonable default value exists that could be used
  when a :obj:`Nullable` object's value turns out to be missing, you can provide this
  default value as a second argument to :func:`get`:

:obj:`Nullable` オブジェクトの値が不足していることが判明したときに使用できる合理的なデフォルト値が存在する場合は、
このデフォルト値を :func:`get` の第2引数として指定することができます。

.. doctest::

    julia> get(Nullable{Float64}(), 0.0)
    0.0

    julia> get(Nullable(1.0), 0.0)
    1.0

.. 
  Note that this default value will automatically be converted to the type of
  the :obj:`Nullable` object that you attempt to access using the :func:`get` function.
  For example, in the code shown above the value ``0`` would be automatically
  converted to a :class:`Float64` value before being returned. The presence of default
  replacement values makes it easy to use the :func:`get` function to write
  type-stable code that interacts with sources of potentially missing values.

このデフォルト値は、 :func:`get` 関数を使用してアクセスしようとする :obj:`Nullable` オブジェクトの型に自動的に
変換されることに注意してください。例えば、上記のコードでは、値 ``0`` は自動的に :class:`Float64` 値に変換されて
返されます。デフォルトの値の変換は、欠損値の可能性があるソースを扱う、型が安定したコードを書くために
使用する :func:`get` 関数の利用を容易にします。
