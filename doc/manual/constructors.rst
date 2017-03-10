.. _man-constructors:

.. currentmodule:: Base

.. 
 **************
  Constructors
 **************

**************
 コンストラクタ
**************

.. 
 Constructors [#]_ are functions that create new objects — specifically,
 instances of :ref:`man-composite-types`. In Julia,
 type objects also serve as constructor functions: they create new
 instances of themselves when applied to an argument tuple as a function.
 This much was already mentioned briefly when composite types were
 introduced. For example::

コンストラクタは [#]_ は、新しいオブジェクト、具体的には :ref:`man-コンポジット型` のインスタンスを作成する関数です。
Juliaでは、型オブジェクトはコンストラクタ関数としても機能します。引数タプルを関数として適用した場合、
型オブジェクトは新しいインスタンスを作成します。これは、コンポジット型の説明の際にすでに簡単に触れられています。例えば::

    type Foo
      bar
      baz
    end

    julia> foo = Foo(1,2)
    Foo(1,2)

    julia> foo.bar
    1

    julia> foo.baz
    2

.. 
 For many types, forming new objects by binding their field values
 together is all that is ever needed to create instances. There are,
 however, cases where more functionality is required when creating
 composite objects. Sometimes invariants must be enforced, either by
 checking arguments or by transforming them. `Recursive data
 structures <https://en.wikipedia.org/wiki/Recursion_%28computer_science%29#Recursive_data_structures_.28structural_recursion.29>`_,
 especially those that may be self-referential, often cannot be
 constructed cleanly without first being created in an incomplete state
 and then altered programmatically to be made whole, as a separate step
 from object creation. Sometimes, it's just convenient to be able to
 construct objects with fewer or different types of parameters than they
 have fields. Julia's system for object construction addresses all of
 these cases and more.

多くの型では、インスタンスを作成するには、フィールド値をバインドして新しいオブジェクトを作成する必要があります。
しかし、コンポジットオブジェクトを作成する際により多くの機能が必要になる場合があります。
場合によっては、引数をチェックしたり変換したりすることによって、不変性を強制する必要があります。
`再帰的データ構造 <https://en.wikipedia.org/wiki/Recursion_%28computer_science%29#Recursive_data_structures_.28structural_recursion.29>`_ 、
特に自己参照可能な構造は、不完全な状態として作成されてその後オブジェクト作成とは
別のステップとしてプログラムで変更されずに、きれいに構築することはできないことがあります。
場合によっては、フィールドに持つものよりも少ないまたは異なる種類のパラメータでオブジェクトを
構築できることが便利なこともあります。Juliaのオブジェクト構築システムは、これらすべての事例に対応しています。

.. 
 .. [#] Nomenclature: while the term “constructor” generally refers to
   the entire function which constructs objects of a type, it is common to
   abuse terminology slightly and refer to specific constructor methods as
   “constructors”. In such situations, it is generally clear from context
   that the term is used to mean “constructor method” rather than
   “constructor function”, especially as it is often used in the sense of
   singling out a particular method of the constructor from all of the
   others.

.. [#] 「コンストラクタ」という用語は、一般的にはある型のオブジェクトを構成する関数全体を指しますが、
       特定のコンストラクタメソッドを「コンストラクタ」と呼ぶことがあります。このような場合には、一般的に、
       その用語が「コンストラクタ関数」ではなく「コンストラクタメソッド」を意味するのに使用されることは文脈から判断することができます。
       「コンストラクタ」という用語は、特にコンストラクタの全体から特定のコンストラクタメソッドを選ぶという意味でよく使われています。

.. 
  Outer Constructor Methods
  -------------------------

その他のコンストラクタメソッド
-------------------------

.. 
  A constructor is just like any other function in Julia in that its
  overall behavior is defined by the combined behavior of its methods.
  Accordingly, you can add functionality to a constructor by simply
  defining new methods. For example, let's say you want to add a
  constructor method for ``Foo`` objects that takes only one argument and
  uses the given value for both the ``bar`` and ``baz`` fields. This is
  simple::

コンストラクタは、Juliaの他の関数と同様に、その処理はメソッドの動作を組み合わせることで定義されています。
したがって、新しいメソッドを定義することで、コンストラクタに機能を追加することができます。
例えば、引数を1つしか取らない ``Foo`` オブジェクトにコンストラクタメソッドを追加し、
``bar`` および ``baz`` フィールドに与えられた値を使用したいとします。これは単純に以下とすることでできます。::

    Foo(x) = Foo(x,x)

    julia> Foo(1)
    Foo(1,1)

.. 
  You could also add a zero-argument ``Foo`` constructor method that
  supplies default values for both of the ``bar`` and ``baz`` fields::

``bar`` フィールドと ``baz`` フィールドの両方にデフォルト値を与える、引数のない ``Foo`` コンストラクタメソッドを追加することもできます。::

    Foo() = Foo(0)

    julia> Foo()
    Foo(0,0)

.. 
  Here the zero-argument constructor method calls the single-argument
  constructor method, which in turn calls the automatically provided
  two-argument constructor method. For reasons that will become clear very
  shortly, additional constructor methods declared as normal methods like
  this are called *outer* constructor methods. Outer constructor methods
  can only ever create a new instance by calling another constructor
  method, such as the automatically provided default ones.

ここでは、引数のないコンストラクタメソッドは、単一引数のコンストラクタメソッドを呼び出し、
次に自動的に提供される2つの引数のコンストラクターメソッドを呼び出します。理由は後述しますが、
このケースのように通常のメソッドとして宣言される追加のコンストラクタメソッドはアウターコンストラクタメソッドと呼ばれます。
アウターコンストラクタメソッドは、自動的に与えられるデフォルトのものなどの別のコンストラクタメソッドを呼び出すことで、
新しいインスタンスを作成することができます。

.. 
  Inner Constructor Methods
  -------------------------

インナーコンストラクタメソッド
-------------------------

.. 
  While outer constructor methods succeed in addressing the problem of
  providing additional convenience methods for constructing objects, they
  fail to address the other two use cases mentioned in the introduction of
  this chapter: enforcing invariants, and allowing construction of
  self-referential objects. For these problems, one needs *inner*
  constructor methods. An inner constructor method is much like an outer
  constructor method, with two differences:

アウターコンストラクタメソッドは、オブジェクトを構築するための便利なメソッドを追加するという問題に
対処する点では利点がある一方で、この章の導入で述べた不変性の強制と自己参照可能な構造の許容という
2つのケースには対処できません。これらの問題に対応するためには、インナーコンストラクタメソッドが必要です。
インナーコンストラクタメソッドはアウターコンストラクタメソッドとよく似ていますが、以下2つの違いがあります。:

.. 
  1. It is declared inside the block of a type declaration, rather than
     outside of it like normal methods.
  2. It has access to a special locally existent function called ``new``
     that creates objects of the block's type.

1. 通常のメソッドのように外側ではなく、型宣言のブロック内で宣言される。
2.　ブロック型のオブジェクトを作成する、 ``new`` というローカルに存在する特殊な関数へのアクセスができる。

.. 
  For example, suppose one wants to declare a type that holds a pair of
  real numbers, subject to the constraint that the first number is
  not greater than the second one. One could declare it like this:

例えば、第1の数が第2の数よりも大きくないという制約に従う、実数のペアを持つする型を宣言したいとします。
これを次のように宣言することができます。:

.. testcode::

    type OrderedPair
      x::Real
      y::Real

      OrderedPair(x,y) = x > y ? error("out of order") : new(x,y)
    end

.. 
  Now ``OrderedPair`` objects can only be constructed such that
  ``x <= y``:

``OrderedPair`` オブジェクトは ``x <= y`` となるようにのみ構築することができます。:

.. doctest::

    julia> OrderedPair(1,2)
    OrderedPair(1,2)

    julia> OrderedPair(2,1)
    ERROR: out of order
     in OrderedPair(::Int64, ::Int64) at ./none:5
     ...

.. 
  You can still reach in and directly change the field values to violate
  this invariant, but messing around with an object's internals uninvited is
  considered poor form. You (or someone else) can also provide additional
  outer constructor methods at any later point, but once a type is
  declared, there is no way to add more inner constructor methods. Since
  outer constructor methods can only create objects by calling other
  constructor methods, ultimately, some inner constructor must be called
  to create an object. This guarantees that all objects of the declared
  type must come into existence by a call to one of the inner constructor
  methods provided with the type, thereby giving some degree of
  enforcement of a type's invariants.

この不変性に違反するようにフィールド値を直接変更することはできますが、呼び出されていないオブジェクトの内部を
操作することは不適切な方法であるとみなされています。また、後続に追加の
アウターコンストラクタメソッドを与えることができますが、一度型が宣言されると、
インナーコンストラクタメソッドを追加することはできません。アウターコンストラクタメソッドは
他のコンストラクタメソッドを呼び出すことによってのみオブジェクトを作成できるため、最終的には、
インナーコンストラクタを呼び出してオブジェクトを作成する必要があります。これにより、
宣言された型のすべてのオブジェクトが、その型で提供されるインナーコンストラクタメソッドを呼び出すことによって
存在しなければならないことが保証され、それによって型の不変性がある程度強制されます。

.. 
  Of course, if the type is declared as ``immutable``, then its
  constructor-provided invariants are fully enforced. This is an important
  consideration when deciding whether a type should be immutable.

もちろん型が ``immutable`` （不変）として宣言されている場合、
その与えられているコンストラクタの不変性は完全に強制されています。
これは、型が不変でなければならないかどうかを決定する際の重要な考慮事項です。

.. 
  If any inner constructor method is defined, no default constructor
  method is provided: it is presumed that you have supplied yourself with
  all the inner constructors you need. The default constructor is
  equivalent to writing your own inner constructor method that takes all
  of the object's fields as parameters (constrained to be of the correct
  type, if the corresponding field has a type), and passes them to
  ``new``, returning the resulting object::

インナーコンストラクタメソッドが定義されている場合、デフォルトコンストラクタメソッドは与えられません。
これは、必要となる全てのインナーコンストラクタがすでに与えられているとみなされるためです。
デフォルトコンストラクタは、オブジェクトのフィールドをパラメータ（対応するフィールドに型がある場合は
正しい型に制約される）として取るインナーコンストラクタメソッドを記述することと同様であり、
それらを ``new`` に渡して結果のオブジェクトを返します。::

    type Foo
      bar
      baz

      Foo(bar,baz) = new(bar,baz)
    end

.. 
  This declaration has the same effect as the earlier definition of the
  ``Foo`` type without an explicit inner constructor method. The following
  two types are equivalent — one with a default constructor, the other
  with an explicit constructor::

この宣言は、前述の明示的なインナーコンストラクターメソッドを持たない ``Foo`` 型の定義と同じ効果を持ちます。
次の2つの型は同様です。1つはデフォルトコンストラクタを使用し、もう1つは明示的なコンストラクタを使用します。::

    type T1
      x::Int64
    end

    type T2
      x::Int64
      T2(x) = new(x)
    end

    julia> T1(1)
    T1(1)

    julia> T2(1)
    T2(1)

    julia> T1(1.0)
    T1(1)

    julia> T2(1.0)
    T2(1)

.. 
  It is considered good form to provide as few inner constructor methods
  as possible: only those taking all arguments explicitly and enforcing
  essential error checking and transformation. Additional convenience
  constructor methods, supplying default values or auxiliary
  transformations, should be provided as outer constructors that call the
  inner constructors to do the heavy lifting. This separation is typically
  quite natural.

できるだけ少ないインナーコンストラクタメソッドを与えることは良いコードであるとみなされます。
これらは全ての引数を取り、本質的なエラーチェックと変換を強制します。
重い処理をするためにインナーコンストラクタを呼び出すアウターコンストラクタとして、
デフォルト値または補助的な変換を行う、追加の便利なコンストラクタメソッドを用意する必要があります。
この住み分けは非常に自然なものです。

.. 
  Incomplete Initialization
  -------------------------

不完全な初期化
-------------------------

.. 
  The final problem which has still not been addressed is construction of
  self-referential objects, or more generally, recursive data structures.
  Since the fundamental difficulty may not be immediately obvious, let us
  briefly explain it. Consider the following recursive type declaration::

まだ説明されていない最後の問題は自己参照オブジェクト、またはより一般的には、再帰的データ構造です。
この問題の根本的な難しさは明確ではないかもしれないため、簡単に説明します。次の再帰型宣言を考えてみましょう。::

    type SelfReferential
      obj::SelfReferential
    end

.. 
  This type may appear innocuous enough, until one considers how to
  construct an instance of it. If ``a`` is an instance of
  ``SelfReferential``, then a second instance can be created by the call::

この型は、そのインスタンス構築方法を検討するまでは、無害に見えるかもしれません。
``a`` が ``SelfReferential`` のインスタンスである場合、呼び出しにより2番目のインスタンスを作成できます。::

    b = SelfReferential(a)

.. 
  But how does one construct the first instance when no instance exists to
  provide as a valid value for its ``obj`` field? The only solution is to
  allow creating an incompletely initialized instance of
  ``SelfReferential`` with an unassigned ``obj`` field, and using that
  incomplete instance as a valid value for the ``obj`` field of another
  instance, such as, for example, itself.

しかし、``obj`` フィールドの有効な値としてインスタンスが存在しない場合、
どのように最初のインスタンスを作成するのでしょうか。唯一の解決策は、
割り当てられていない ``SelfReferential`` の不完全に初期化されたインスタンスを作成し、
その不完全なインスタンスを他のインスタンスの``obj`` フィールドの有効な値として使用することです。

.. 
  To allow for the creation of incompletely initialized objects, Julia
  allows the ``new`` function to be called with fewer than the number of
  fields that the type has, returning an object with the unspecified
  fields uninitialized. The inner constructor method can then use the
  incomplete object, finishing its initialization before returning it.
  Here, for example, we take another crack at defining the
  ``SelfReferential`` type, with a zero-argument inner constructor
  returning instances having ``obj`` fields pointing to themselves:

不完全に初期化されたオブジェクトの作成を可能にするために、Juliaは、型が持つよりも少ない
フィールド数で関数を呼び出し、未指定のフィールドが初期化されていないオブジェクトを返します。
インナーコンストラクタメソッドは、不完全なオブジェクトを使用し、値を返す前にその初期化を完了します。
ここで例として、自身を指す ``obj`` フィールドを持ち、インスタンスを返す引数が無いインナーコンストラクタを
使用する ``SelfReferential`` 型の定義をしてみます。:

.. testcode::

    type SelfReferential
      obj::SelfReferential

      SelfReferential() = (x = new(); x.obj = x)
    end

.. 
  We can verify that this constructor works and constructs objects that
  are, in fact, self-referential:

このコンストラクタが動作し、実際には自己参照型のオブジェクトを構築することを検証することができます。:

.. doctest::

    julia> x = SelfReferential();

    julia> is(x, x)
    true

    julia> is(x, x.obj)
    true

    julia> is(x, x.obj.obj)
    true

.. 
  Although it is generally a good idea to return a fully initialized
  object from an inner constructor, incompletely initialized objects can
  be returned:

インナーコンストラクタから完全に初期化されたオブジェクトを返すことは一般的には理想ですが、
不完全に初期化されたオブジェクトを返すことができます。:

.. doctest::

    julia> type Incomplete
             xx
             Incomplete() = new()
           end

    julia> z = Incomplete();

.. 
  While you are allowed to create objects with uninitialized fields, any
  access to an uninitialized reference is an immediate error:

初期化されていないフィールドを持つオブジェクトを作成することは許容されていますが、
初期化されていない参照へのアクセスは即座にエラーになります。:

.. doctest::

    julia> z.xx
    ERROR: UndefRefError: access to undefined reference
     ...

.. 
  This avoids the need to continually check for ``null`` values.
  However, not all object fields are references. Julia considers some
  types to be "plain data", meaning all of their data is self-contained
  and does not reference other objects. The plain data types consist of bits
  types (e.g. ``Int``) and immutable structs of other plain data types.
  The initial contents of a plain data type is undefined::

これにより、 ``null`` 値を継続的にチェックする必要がなくなります。
ただし、全てのオブジェクトフィールドが参照であるとは限りません。Juliaは、
いくつかの型を「プレーンデータ」、つまり、全てのデータが自己完結型であり
他のオブジェクトを参照しないものとみなします。プレーンデータ型は、
ビット型（例えば ``Int`` ）と他のプレーンデータ型の不変構造体から構成されています。
プレーンデータ型の初期の内容は未定義です。::

    julia> type HasPlain
             n::Int
             HasPlain() = new()
           end

    julia> HasPlain()
    HasPlain(438103441441)

.. 
  Arrays of plain data types exhibit the same behavior.

プレーンデータ型の配列は同じ動作を示します。

.. 
  You can pass incomplete objects to other functions from inner constructors to
  delegate their completion::

不完全なオブジェクトをインナーコンストラクタから他の関数に渡して、その完了を任せることができます。::

    type Lazy
      xx

      Lazy(v) = complete_me(new(), v)
    end

.. 
  As with incomplete objects returned from constructors, if
  ``complete_me`` or any of its callees try to access the ``xx`` field of
  the ``Lazy`` object before it has been initialized, an error will be
  thrown immediately.

``complete_me`` または呼び出されるもののいずれかが初期化される前に ``Lazy`` オブジェクトの
``xx`` フィールドにアクセスしようとした場合、コンストラクタから返された不完全なオブジェクトと同様に、
すぐにエラーがスローされます。

.. 
  Parametric Constructors
  -----------------------

パラメトリックコンストラクタ
-----------------------

.. 
  Parametric types add a few wrinkles to the constructor story. Recall
  from :ref:`man-parametric-types` that, by default,
  instances of parametric composite types can be constructed either with
  explicitly given type parameters or with type parameters implied by the
  types of the arguments given to the constructor. Here are some examples:

パラメータ型は、コンストラクタにいくつかの便利さを足します。 :ref:`man-パラメータ型` の内容を思い出してください。
デフォルトでは、パラメータコンポジット型のインスタンスは、明示的に与えられた型パラメータか、
またはコンストラクタに与えられた引数の型によって暗示的に与えられた型パラメータにより構築できます。以下はその例です。:

.. doctest::

    julia> type Point{T<:Real}
             x::T
             y::T
           end

    ## implicit T ##

    julia> Point(1,2)
    Point{Int64}(1,2)

    julia> Point(1.0,2.5)
    Point{Float64}(1.0,2.5)

    julia> Point(1,2.5)
    ERROR: MethodError: no method matching Point{T<:Real}(::Int64, ::Float64)
    Closest candidates are:
      Point{T<:Real}{T<:Real}(::T<:Real, !Matched::T<:Real) at none:2
      Point{T<:Real}{T}(::Any) at sysimg.jl:53
     ...

    ## explicit T ##

    julia> Point{Int64}(1,2)
    Point{Int64}(1,2)

    julia> Point{Int64}(1.0,2.5)
    ERROR: InexactError()
     in Point{Int64}(::Float64, ::Float64) at ./none:2
     ...

    julia> Point{Float64}(1.0,2.5)
    Point{Float64}(1.0,2.5)

    julia> Point{Float64}(1,2)
    Point{Float64}(1.0,2.0)

.. 
  As you can see, for constructor calls with explicit type parameters, the
  arguments are converted to the implied field types: ``Point{Int64}(1,2)``
  works, but ``Point{Int64}(1.0,2.5)`` raises an
  ``InexactError`` when converting ``2.5`` to ``Int64``.
  When the type is implied by the
  arguments to the constructor call, as in ``Point(1,2)``, then the types
  of the arguments must agree — otherwise the ``T`` cannot be determined —
  but any pair of real arguments with matching type may be given to the
  generic ``Point`` constructor.

見てわかる通り、明示的な型パラメータを持つコンストラクタ呼び出しでは、
引数は暗示的なフィールド型に変換されます。 ``Point{Int64}(1,2)`` は動作しますが、
``Point{Int64}(1.0,2.5)`` は ``2.5`` を ``Int64`` に変換する際に ``InexactError`` を出力します。
型が ``Point(1,2)`` のようにコンストラクタ呼び出しの引数によって暗示的に指定されている場合、
引数の型は一致する必要があり、一致していない場合は ``T`` を決定することができません。
しかし、型と一致する実引数のペアは汎用 ``Point`` コンストラクタに渡されます。

.. 
  What's really going on here is that ``Point``, ``Point{Float64}`` and
  ``Point{Int64}`` are all different constructor functions. In fact,
  ``Point{T}`` is a distinct constructor function for each type ``T``.
  Without any explicitly provided inner constructors, the declaration of
  the composite type ``Point{T<:Real}`` automatically provides an inner
  constructor, ``Point{T}``, for each possible type ``T<:Real``, that
  behaves just like non-parametric default inner constructors do. It also
  provides a single general outer ``Point`` constructor that takes pairs
  of real arguments, which must be of the same type. This automatic
  provision of constructors is equivalent to the following explicit
  declaration::

ここでは、 ``Point`` 、 ``Point{Float64}`` 、 ``Point{Int64}`` がすべて異なるコンストラクタ関数となります。
``Point{T}`` は、それぞれの型 ``T`` の異なるのコンストラクタ関数です。明示的に与えられたインナーコンストラクタがなければ、
コンポジット型 ``Point{T<:Real}`` の宣言は、自動的にインナーコンストラクタ ``Point{T}`` を可能な型 ``T<:Real`` の
それぞれに与え、 ``T<:Real`` 非パラメータのデフォルトインナーコンストラクタのような動作をします。
また、同じ型でなければならない実際の引数のペアを取る、単一の一般的なアウター ``Point`` コンストラクタも提供します。
このコンストラクタの自動提供は、次の明示的宣言と同じです。::

    type Point{T<:Real}
      x::T
      y::T

      Point(x,y) = new(x,y)
    end

    Point{T<:Real}(x::T, y::T) = Point{T}(x,y)

.. 
  Some features of parametric constructor definitions at work here deserve
  comment. First, inner constructor declarations always define methods of
  ``Point{T}`` rather than methods of the general ``Point`` constructor
  function. Since ``Point`` is not a concrete type, it makes no sense for
  it to even have inner constructor methods at all. Thus, the inner method
  declaration ``Point(x,y) = new(x,y)`` provides an inner
  constructor method for each value of ``T``. It is this method
  declaration that defines the behavior of constructor calls with explicit
  type parameters like ``Point{Int64}(1,2)`` and
  ``Point{Float64}(1.0,2.0)``. The outer constructor declaration, on the
  other hand, defines a method for the general ``Point`` constructor which
  only applies to pairs of values of the same real type. This declaration
  makes constructor calls without explicit type parameters, like
  ``Point(1,2)`` and ``Point(1.0,2.5)``, work. Since the method
  declaration restricts the arguments to being of the same type, calls
  like ``Point(1,2.5)``, with arguments of different types, result in "no
  method" errors.

ここで示しているパラメトリックコンストラクタ定義のいくつかの機能について説明を追加します。
まず、インナーコンストラクタ宣言は、一般的な ``Point`` コンストラクタ関数のメソッドではなく、
``Point{T}`` のメソッドを常に定義します。 ``Point`` は具体型ではないため、
インナーコンストラクタメソッドを持つことは意味がありません。したがって、
インナーメソッド宣言 ``Point(x,y) = new(x,y)`` は、 ``T`` の各値に対して
インナーコンストラクタメソッドを与えます。このメソッド宣言は、 ``Point{Int64}(1,2)`` や
``Point{Float64}(1.0,2.0)`` のような明示的な型パラメータをコンストラクタ呼び出しの動作を定義します。
一方、アウターコンストラクタ宣言は、同じ実数型の値のペアにのみ適用される一般的な ``Point`` コンストラクタのメソッドを定義します。
この宣言は、 ``Point(1,2)`` や ``Point(1.0,2.5)`` のような明示的な型パラメータを持たないコンストラクタ呼び出しを
動作するようにします。メソッドの宣言では、引数が同じ型に制限されるため、 ``Point(1,2.5)`` のように
異なる型の引数を持つ呼び出しを行うと、「no method」エラーが発生します。

.. 
  Suppose we wanted to make the constructor call ``Point(1,2.5)`` work by
  "promoting" the integer value ``1`` to the floating-point value ``1.0``.
  The simplest way to achieve this is to define the following additional
  outer constructor method:

整数値 ``1`` を浮動小数点値 ``1.0`` に「昇格」させることによって、コンストラクタ呼び出し ``Point(1,2.5)`` を
動作させたいとします。これを実現する最も簡単な方法は、以下の追加のアウターコンストラクターメソッドを定義することです。:

.. doctest::

    julia> Point(x::Int64, y::Float64) = Point(convert(Float64,x),y);

.. 
  This method uses the :func:`convert` function to explicitly convert ``x`` to
  :class:`Float64` and then delegates construction to the general constructor
  for the case where both arguments are :class:`Float64`. With this method
  definition what was previously a :exc:`MethodError` now successfully
  creates a point of type ``Point{Float64}``:

このメソッドは、 :func:`convert` 関数を使用して明示的に ``x`` を :class:`Float64` に変換し、
その後両方の引数が :class:`Float64` の場合の一般的なコンストラクタに渡します。
このメソッド定義により、以前は :exc:`MethodError` となっていたものが、正常に型 ``Point{Float64}`` のポイントを作成します。:

.. doctest::

    julia> Point(1,2.5)
    Point{Float64}(1.0,2.5)

    julia> typeof(ans)
    Point{Float64}

.. 
  However, other similar calls still don't work:

しかし、他の同様の呼び出しはまだ動作しません。:

.. doctest::

    julia> Point(1.5,2)
    ERROR: MethodError: no method matching Point{T<:Real}(::Float64, ::Int64)
    Closest candidates are:
      Point{T<:Real}{T<:Real}(::T<:Real, !Matched::T<:Real) at none:2
      Point{T<:Real}{T}(::Any) at sysimg.jl:53
     ...

.. 
  For a much more general way of making all such calls work sensibly, see
  :ref:`man-conversion-and-promotion`. At the risk
  of spoiling the suspense, we can reveal here that all it takes is
  the following outer method definition to make all calls to the general
  ``Point`` constructor work as one would expect:

そのような呼び出しを分かりやすくするためのより一般的な方法については、 :ref:`man-変換とプロモーション` を
参照してください。一般的な ``Point`` コンストラクタへの全ての呼び出しを期待どおりに動作させるためには、
以下のアウターメソッド定義が必要です。:

.. doctest::

    julia> Point(x::Real, y::Real) = Point(promote(x,y)...);

.. 
  The ``promote`` function converts all its arguments to a common type
  — in this case :class:`Float64`. With this method definition, the ``Point``
  constructor promotes its arguments the same way that numeric operators
  like :obj:`+` do, and works for all kinds of real numbers:

``promote`` 関数は全ての引数を共通の型（この場合は :class:`Float64` ）に変換します。
このメソッド定義では、「Point」コンストラクタは数値演算子 :obj:`+` のように引数を昇格させ、
あらゆる種類の実数に対して機能します。:

.. doctest::

    julia> Point(1.5,2)
    Point{Float64}(1.5,2.0)

    julia> Point(1,1//2)
    Point{Rational{Int64}}(1//1,1//2)

    julia> Point(1.0,1//2)
    Point{Float64}(1.0,0.5)

.. 
  Thus, while the implicit type parameter constructors provided by default
  in Julia are fairly strict, it is possible to make them behave in a more
  relaxed but sensible manner quite easily. Moreover, since constructors
  can leverage all of the power of the type system, methods, and multiple
  dispatch, defining sophisticated behavior is typically quite simple.

したがって、Juliaでデフォルトで提供されている暗示的な型パラメータコンストラクタはかなり厳密ですが、
それらをより緩めて分かりやすい方法で簡単に動作させることも可能です。さらに、コンストラクタは型システム、
メソッド、および多重ディスパッチの全ての機能を活用できるため、洗練された動作を定義することは非常に簡単です。

.. 
  Case Study: Rational
  --------------------

ケーススタディ：Rational
--------------------

.. 
  Perhaps the best way to tie all these pieces together is to present a
  real world example of a parametric composite type and its constructor
  methods. To that end, here is beginning of
  `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_,
  which implements Julia's :ref:`man-rational-numbers`::

おそらく、これらの全ての要素を結びつける最も良い方法は、パラメトリックコンポジット型とそのコンストラクタメソッドの
例を提示することです。以下はJuliaの :ref:`man-有理数` を実装する
`rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_ の例です。::

    immutable Rational{T<:Integer} <: Real
        num::T
        den::T

        function Rational(num::T, den::T)
            if num == 0 && den == 0
                error("invalid rational: 0//0")
            end
            g = gcd(den, num)
            num = div(num, g)
            den = div(den, g)
            new(num, den)
        end
    end
    Rational{T<:Integer}(n::T, d::T) = Rational{T}(n,d)
    Rational(n::Integer, d::Integer) = Rational(promote(n,d)...)
    Rational(n::Integer) = Rational(n,one(n))

    //(n::Integer, d::Integer) = Rational(n,d)
    //(x::Rational, y::Integer) = x.num // (x.den*y)
    //(x::Integer, y::Rational) = (x*y.den) // y.num
    //(x::Complex, y::Real) = complex(real(x)//y, imag(x)//y)
    //(x::Real, y::Complex) = x*y'//real(y*y')

    function //(x::Complex, y::Complex)
        xy = x*y'
        yy = real(y*y')
        complex(real(xy)//yy, imag(xy)//yy)
    end

.. 
  The first line — ``immutable Rational{T<:Int} <: Real`` — declares that
  :class:`Rational` takes one type parameter of an integer type, and is itself
  a real type. The field declarations ``num::T`` and ``den::T`` indicate
  that the data held in a ``Rational{T}`` object are a pair of integers of
  type ``T``, one representing the rational value's numerator and the
  other representing its denominator.

最初の行の ``immutable Rational{T<:Int} <: Real`` は :class:`Rational` が1つの整数型の型パラメータを取ること、
およびそれが実型であることを宣言しています。フィールド宣言の ``num::T`` および ``den::T`` は、
``Rational{T}`` オブジェクトに格納されているデータは型 ``T`` の整数のペアであることを示し、
前者は有理値の分子を表し、後者はその分母を表します。

.. 
  Now things get interesting. :class:`Rational` has a single inner constructor
  method which checks that both of ``num`` and ``den`` aren't zero and
  ensures that every rational is constructed in "lowest terms" with a
  non-negative denominator. This is accomplished by dividing the given
  numerator and denominator values by their greatest common divisor,
  computed using the ``gcd`` function. Since ``gcd`` returns the greatest
  common divisor of its arguments with sign matching the first argument
  (``den`` here), after this division the new value of ``den`` is
  guaranteed to be non-negative. Because this is the only inner
  constructor for :class:`Rational`, we can be certain that :class:`Rational`
  objects are always constructed in this normalized form.

これは興味深いものです。 :class:`Rational` は、 ``num`` および ``den`` が0以外であることをチェックし、
また全ての有理数が非マイナスの分母を持つ「最小の項」で構成されていることを保証する、
単一のインナーコンストラクタメソッドを持ちます。これは、これは、与えられた分子と分母の値を、
``gcd`` 関数を使用して算出された最大公約数で除算することで実現されます。
``gcd`` は最初の引数（ここでは ``den`` ）の記号に一致する、引数の最大公約数を返すため、
この除算の後の ``den`` の新しい値は、マイナスではないことが担保されます。
これは :class:`Rational` のインナーコンストラクタに対してのみであるため、
:class:`Rational` オブジェクトは常に標準化された状態であると言えます。

.. 
  :class:`Rational` also provides several outer constructor methods for
  convenience. The first is the "standard" general constructor that infers
  the type parameter ``T`` from the type of the numerator and denominator
  when they have the same type. The second applies when the given
  numerator and denominator values have different types: it promotes them
  to a common type and then delegates construction to the outer
  constructor for arguments of matching type. The third outer constructor
  turns integer values into rationals by supplying a value of ``1`` as the
  denominator.

:class:`Rational` は利便性のためにいくつかのアウターコンストラクタメソッドも提供します。
最初のアウターコンストラクタメソッドは、分母と分子の型が同じ場合に、
それらの型から型パラメータ ``T`` を推測する、スタンダードなコンストラクタです。
2つ目は与えられた分母と分子の値が異なる型を持つ場合に使用し、
これは分母と分子の値の型を共通する型に昇格させ、構文を一致する型の引数のアウターコンストラクタに渡します。
3つ目は、 ``1`` を分母として与えることで、整数値を有理数に変換します。

.. 
  Following the outer constructor definitions, we have a number of methods
  for the :obj:`//` operator, which provides a syntax for writing rationals.
  Before these definitions, :obj:`//` is a completely undefined operator with
  only syntax and no meaning. Afterwards, it behaves just as described in
  :ref:`man-rational-numbers`
  — its entire behavior is defined in these few lines. The first and most
  basic definition just makes ``a//b`` construct a :class:`Rational` by
  applying the :class:`Rational` constructor to ``a`` and ``b`` when they are
  integers. When one of the operands of :obj:`//` is already a rational
  number, we construct a new rational for the resulting ratio slightly
  differently; this behavior is actually identical to division of a
  rational with an integer. Finally, applying :obj:`//` to complex integral
  values creates an instance of ``Complex{Rational}`` — a complex number
  whose real and imaginary parts are rationals:

以下のアウターコンストラクタ定義では、有理数を記述する構文を提供する :obj:`//` 演算子のいくつかのメソッドがあります。
これらの定義より前では、 :obj:`//` は構文のみで意味を持たない未定義の演算子です。定義以降では、
:obj:`//` 演算子は「有理数」に説明がある通りの動作をし、その動作の全てはこれら数行の中に定義されています。
最も基本的な定義は、 ``a`` と ``b`` が整数の場合にそれらに :class:`Rational` コンストラクタを適用することで、
``a//b`` が :class:`Rational` を構成するようにすることです。これらの :obj:`//` の被演算子の一方がすでに有理数の場合は、
少し異なる処理結果の比率の新しい有理数を構成します。この処理は、整数で有理数を除算することと同一です。
最後に、 :obj:`//` を複雑な積分の値(complex integral values)に適用すると、
``Complex{Rational}`` を作成します。これは、実体と仮想のパーツが有理数である複素数です。:

.. doctest::

    julia> (1 + 2im)//(1 - 2im)
    -3//5 + 4//5*im

    julia> typeof(ans)
    Complex{Rational{Int64}}

    julia> ans <: Complex{Rational}
    false

.. 
  Thus, although the :obj:`//` operator usually returns an instance of
  :class:`Rational`, if either of its arguments are complex integers, it will
  return an instance of ``Complex{Rational}`` instead. The interested
  reader should consider perusing the rest of
  `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_:
  it is short, self-contained, and implements an entire basic Julia type.

したがって、通常 :obj:`//` 演算子は :class:`Rational` のインスタンスを返しますが、
もし一方の引数がcomplex integerの場合、代わりに ``Complex{Rational}`` のインスタンスを返します。
興味がある方は `rational.jl <https://github.com/JuliaLang/julia/blob/master/base/rational.jl>`_ の
残りを読むことをお勧めします。これは短く、包括的で、Juliaの型の基礎的な実装です。

.. _constructors-and-conversion:

.. 
  Constructors and Conversion
  ---------------------------

コンストラクタと変換
---------------------------

.. 
  Constructors ``T(args...)`` in Julia are implemented like other
  callable objects: methods are added to their types.
  The type of a type is ``Type``, so all constructor methods are
  stored in the method table for the ``Type`` type.
  This means that you can declare more flexible constructors, e.g.
  constructors for abstract types, by explicitly defining methods
  for the appropriate types.

Juliaのコンストラクタ ``T(args...)`` は、他の呼び出し可能オブジェクトのように実装されています。
メソッドは、その型に追加されます。ある型の型は ``Type`` であり、全てのコンストラクタメソッドは、
``Type`` 型のメソッドテーブルに格納されます。これは、適切な型に対して明示的にメソッドを定義することで、
抽象型のコンストラクタのようなフレキシブルなコンストラクタを宣言できることを意味します。

.. 
  However, in some cases you could consider adding methods to
  ``Base.convert`` *instead* of defining a constructor, because Julia
  falls back to calling :func:`convert` if no matching constructor
  is found. For example, if no constructor ``T(args...) = ...`` exists
  ``Base.convert(::Type{T}, args...) = ...`` is called.

しかし、Juliaは一致するコンストラクタが見つからなければ :func:`convert` を呼び出すため、
コンストラクタを定義する代わりに、 ``Base.convert`` にメソッドを追加することもできます。
例えば、コンストラクタ ``T(args...) = ...`` が存在しない場合、
``Base.convert(::Type{T}, args...) = ...`` が呼び出されます。

.. 
  ``convert`` is used extensively throughout Julia whenever one type
  needs to be converted to another (e.g. in assignment, ``ccall``,
  etcetera), and should generally only be defined (or successful) if the
  conversion is lossless.  For example, ``convert(Int, 3.0)`` produces
  ``3``, but ``convert(Int, 3.2)`` throws an ``InexactError``.  If you
  want to define a constructor for a lossless conversion from one type
  to another, you should probably define a ``convert`` method instead.

``convert`` は、ある型を別の型に変換する必要があるとき（例えば割り当てや ``ccall`` など）にJuliaでは広く使用され、
変換が可逆の場合は定義する必要があります。例えば、 ``convert(Int, 3.0)`` は ``3`` となりますが、
``convert(Int, 3.2)`` では ``InexactError`` となります。ある型から別の型への可逆変換のコンストラクタを定義したい場合は、
代わりに ``convert`` メソッドを定義する必要があります。

.. 
  On the other hand, if your constructor does not represent a lossless
  conversion, or doesn't represent "conversion" at all, it is better
  to leave it as a constructor rather than a ``convert`` method.  For
  example, the ``Array{Int}()`` constructor creates a zero-dimensional
  ``Array`` of the type ``Int``, but is not really a "conversion" from
  ``Int`` to an ``Array``.

一方、コンストラクタが可逆変換を表していない、または変換を全く表現していない場合は、
``convert`` メソッドではなくコンストラクタのままとした方がよいでしょう。
例えば、 ``Array{Int}()`` コンストラクタは、 ``Int`` 型のゼロ次元配列を作成しますが、
これは ``Int`` から ``Array`` への「変換」ではありません。

.. 
  Outer-only constructors
  -----------------------

アウターオンリーコンストラクタ
-----------------------

.. 
  As we have seen, a typical parametric type has inner constructors
  that are called when type parameters are known; e.g. they apply
  to ``Point{Int}`` but not to ``Point``.
  Optionally, outer constructors that determine type parameters
  automatically can be added, for example constructing a
  ``Point{Int}`` from the call ``Point(1,2)``.
  Outer constructors call inner constructors to do the core
  work of making an instance.
  However, in some cases one would rather not provide inner constructors,
  so that specific type parameters cannot be requested manually.

これまで見てきたように、典型的なパラメトリック型には、型パラメータが判明しているときに
呼び出されるインナーコンストラクタがあります。例えばインナーコンストラクタは「Point{Int}」には
適用されますが、 ``Point`` には適用されません。オプションで、型パラメータを自動的に決定する
アウターコンストラクタを追加することができます。例えば、 ``Point(1,2)`` の呼び出しから
``Point{Int}`` を構築することができます。アウターコンストラクタはインナーコンストラクタを呼び出して、
インスタンスを作成する中核的な処理を行います。しかし、場合によっては、特定の型パラメータがマニュアルで
呼び出されないようにするために、インナーコンストラクタを与えない方がいいケースがあります。

.. 
  For example, say we define a type that stores a vector along with
  an accurate representation of its sum::

例えば、ベクトルの和を正確に表現したベクトルを格納する型を定義したいとします。::

    type SummedArray{T<:Number,S<:Number}
        data::Vector{T}
        sum::S
    end

.. 
  The problem is that we want ``S`` to be a larger type than ``T``, so
  that we can sum many elements with less information loss.
  For example, when ``T`` is ``Int32``, we would like ``S`` to be ``Int64``.
  Therefore we want to avoid an interface that allows the user to construct
  instances of the type ``SummedArray{Int32,Int32}``.
  One way to do this is to provide only an outer constructor for ``SummedArray``.
  This can be done using method definition by type::

問題は、より多くの要素をより少ない情報の欠落で合計するようにするため、 ``S`` を ``T`` よりも大きな型にしたい点です。
例えば、 ``T`` が ``Int32`` の場合、 ``S`` を ``Int64`` にする必要があります。
つまり、ユーザが型 ``SummedArray{Int32,Int32}`` のインスタンスを構築できるインタフェースを防ぎたいと考えています。
これを実現する1つの方法は、 ``SummedArray`` のアウターコンストラクタのみを与えることです。
これは、型によるメソッド定義を使用することでできます。::

    type SummedArray{T<:Number,S<:Number}
        data::Vector{T}
        sum::S

        function (::Type{SummedArray}){T}(a::Vector{T})
            S = widen(T)
            new{T,S}(a, sum(S, a))
        end
    end

.. 
  This constructor will be invoked by the syntax ``SummedArray(a)``.
  The syntax ``new{T,S}`` allows specifying parameters for the type to be
  constructed, i.e. this call will return a ``SummedArray{T,S}``.
  
このコンストラクタは、構文 ``SummedArray(a)`` によって呼び出されます。構文 ``new{T,S}`` は、
構築される型のパラメータを指定することを可能にします。この場合、この呼び出しは ``SummedArray{T,S}`` を返します。
