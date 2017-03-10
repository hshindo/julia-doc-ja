.. _man-interfaces:

.. 
 ************
  Interfaces
 ************

************
 インタフェース
************

.. 
 A lot of the power and extensibility in Julia comes from a collection of informal interfaces.  By extending a few specific methods to work for a custom type, objects of that type not only receive those functionalities, but they are also able to be used in other methods that are written to generically build upon those behaviors.

Juliaの利便性と拡張性の多くは、インフォーマルなインタフェースの集まりから来ています。
カスタムの型のためにいくつかの特定のメソッドを拡張することによって、
その型のオブジェクトはそれらの機能を受け取るだけでなく、
処理を一般的に構築するように記述された他のメソッドでも使用できます。

.. _man-interfaces-iteration:

.. 
 Iteration
 ---------

反復
---------

..
 ================================================== ======================== =====================================================================================
 Required methods                                                            Brief description
 ================================================== ======================== =====================================================================================
 :func:`start(iter) <start>`                                                 Returns the initial iteration state
 :func:`next(iter, state) <next>`                                            Returns the current item and the next state
 :func:`done(iter, state) <done>`                                            Tests if there are any items remaining
 **Important optional methods**                     **Default definition**   **Brief description**
 :func:`iteratorsize(IterType) <iteratorsize>`      ``HasLength()``          One of `HasLength()`, `HasShape()`, `IsInfinite()`, or `SizeUnknown()` as appropriate
 :func:`iteratoreltype(IterType) <iteratoreltype>`  ``HasEltype()``          Either `EltypeUnknown()` or `HasEltype()` as appropriate
 :func:`eltype(IterType) <eltype>`                  ``Any``                  The type the items returned by :func:`next`
 :func:`length(iter) <length>`                      (*undefined*)            The number of items, if known
 :func:`size(iter, [dim...]) <size>`                (*undefined*)            The number of items in each dimension, if known
 ================================================== ======================== =====================================================================================

================================================== ======================== =====================================================================================
必要なメソッド                                                                概要
================================================== ======================== =====================================================================================
:func:`start(iter) <start>`                                                 初期の反復状態を返す
:func:`next(iter, state) <next>`                                            現在のアイテムと次の状態を返す
:func:`done(iter, state) <done>`                                            アイテムが残っているかテストする
**重要なオプションメソッド**                           **デフォルト定義**        **概要**
:func:`iteratorsize(IterType) <iteratorsize>`      ``HasLength()``          `HasLength()`, `HasShape()`, `IsInfinite()`, または `SizeUnknown()` のうち、適切な1つ
:func:`iteratoreltype(IterType) <iteratoreltype>`  ``HasEltype()``          `EltypeUnknown()` または `HasEltype()` のうち、適切な1つ
:func:`eltype(IterType) <eltype>`                  ``Any``                  :func:`next` により返されるアイテムの型
:func:`length(iter) <length>`                      (*定義なし*)               判明している場合は、アイテム数
:func:`size(iter, [dim...]) <size>`                (*定義なし*)               判明している場合は、各次元のアイテム数
================================================== ======================== =====================================================================================

..
  ================================================================ ======================================================================
  Value returned by :func:`iteratorsize(IterType) <iteratorsize>`  Required Methods
  ================================================================ ======================================================================
  `HasLength()`                                                    :func:`length(iter) <length>`
  `HasShape()`                                                     :func:`length(iter) <length>`  and :func:`size(iter, [dim...]) <size>`
  `IsInfinite()`                                                   (*none*)
  `SizeUnknown()`                                                  (*none*)
  ================================================================ ======================================================================

================================================================ ======================================================================
:func:`iteratorsize(IterType) <iteratorsize>` により返される値     必要なメソッド
================================================================ ======================================================================
`HasLength()`                                                    :func:`length(iter) <length>`
`HasShape()`                                                     :func:`length(iter) <length>` および :func:`size(iter, [dim...]) <size>`
`IsInfinite()`                                                   (*なし*)
`SizeUnknown()`                                                  (*なし*)
================================================================ ======================================================================

.. 
  ==================================================================== ==================================
  Value returned by :func:`iteratoreltype(IterType) <iteratoreltype>`  Required Methods
  ==================================================================== ==================================
  `HasEltype()`                                                        :func:`eltype(IterType) <eltype>`
  `EltypeUnknown()`                                                    (*none*)
  ==================================================================== ==================================

==================================================================== ==================================
:func:`iteratoreltype(IterType) <iteratoreltype>` により返される値      必要なメソッド
==================================================================== ==================================
`HasEltype()`                                                        :func:`eltype(IterType) <eltype>`
`EltypeUnknown()`                                                    (*なし*)
==================================================================== ==================================

.. 
  Sequential iteration is implemented by the methods :func:`start`, :func:`done`, and :func:`next`. Instead of mutating objects as they are iterated over, Julia provides these three methods to keep track of the iteration state externally from the object. The :func:`start(iter) <start>` method returns the initial state for the iterable object ``iter``. That state gets passed along to :func:`done(iter, state) <done>`, which tests if there are any elements remaining, and :func:`next(iter, state) <next>`, which returns a tuple containing the current element and an updated ``state``. The ``state`` object can be anything, and is generally considered to be an implementation detail private to the iterable object.

逐次反復は :func:`start` 、 :func:`done` 、および :func:`next` のメソッドによって実装されています。
Juliaは、反復処理する際にオブジェクトを変更する代わりに、オブジェクトから外部的に反復状態を追跡するために
これらの3つのメソッドを提供します。 :func:`start(iter) <start>` メソッドは、反復可能なオブジェクト ``iter`` 初期状態を返します。
状態は、要素が残っているかをテストする :func:`done(iter, state) <done>` および現在の要素と
更新された ``state`` を含むタプルを返す :func:`next(iter, state) <next>` に渡されます。
``state`` オブジェクトは何にでも当てはまり、一般的に、反復可能オブジェクト専用の実装の詳細と見なされます。

.. 
  Any object defines these three methods is iterable and can be used in the :ref:`many functions that rely upon iteration <stdlib-collections-iteration>`. It can also be used directly in a ``for`` loop since the syntax::

これらの3つのメソッドを定義するオブジェクトは反復可能であり、 :ref:`反復に依存する多くの関数 <stdlibコレクションの反復>` 内で
使用することができます。
また、「for」ループで直接使用することもできます。なぜなら構文は、::

    for i in iter   # or  "for i = iter"
        # body
    end

.. 
  is translated into::

以下のように解釈できるからです。::

    state = start(iter)
    while !done(iter, state)
        (i, state) = next(iter, state)
        # body
    end

.. 
  A simple example is an iterable sequence of square numbers with a defined length:
  
簡単な例は、長さが定義された平方数の反復可能なシーケンスです。:  

.. doctest::

    julia> immutable Squares
               count::Int
           end
           Base.start(::Squares) = 1
           Base.next(S::Squares, state) = (state*state, state+1)
           Base.done(S::Squares, state) = state > S.count;
           Base.eltype(::Type{Squares}) = Int # Note that this is defined for the type
           Base.length(S::Squares) = S.count;

.. 
  With only ``start``, ``next``, and ``done`` definitions, the ``Squares`` type is already pretty powerful. We can iterate over all the elements:

``start`` 、 ``next`` および ``done`` だけでも、 ``Squares`` 型は強力です。全ての要素を繰り返し処理できます。:

.. doctest::

    julia> for i in Squares(7)
               println(i)
           end
    1
    4
    9
    16
    25
    36
    49

.. 
  We can use many of the builtin methods that work with iterables, like :func:`in`, :func:`mean` and :func:`std`:

:func:`in` 、 :func:`mean` および :func:`std` のように、反復可能で動作する多くのビルトインメソッドを使用できます。:

.. doctest::

    julia> 25 in Squares(10)
    true

    julia> mean(Squares(100)), std(Squares(100))
    (3383.5,3024.355854282583)

.. 
  There are a few more methods we can extend to give Julia more information about this iterable collection.  We know that the elements in a ``Squares`` sequence will always be ``Int``. By extending the :func:`eltype` method, we can give that information to Julia and help it make more specialized code in the more complicated methods. We also know the number of elements in our sequence, so we can extend :func:`length`, too.

Juliaにこの繰り返し可能なコレクションについての詳しい情報を与えるために拡張できるメソッドがいくつかあります。
``Squares`` シーケンスの要素は常に ``Int`` となります。 :func:`eltype` メソッドを拡張することにより、情報をJuliaに与え、
より複雑なメソッドでより特殊なコードを作成するのをサポートすることができます。シーケンス内の要素の数も知っているため、
:func:`length` も拡張できます。

.. 
  Now, when we ask Julia to :func:`collect` all the elements into an array it can preallocate a ``Vector{Int}`` of the right size instead of blindly ``push!``\ ing each element into a ``Vector{Any}``:

Juliaに全ての要素を配列に :func:`collect` するように命令した場合、各要素を盲目的に ``Vector{Any}`` に ``push!``\ するのではなく、
適切なサイズの ``Vector{Int}`` を事前に割り当てることができます。:

.. doctest::

    julia> collect(Squares(100))' # transposed to save space
    1×100 Array{Int64,2}:
     1  4  9  16  25  36  49  64  81  100  …  9025  9216  9409  9604  9801  10000

.. 
  While we can rely upon generic implementations, we can also extend specific methods where we know there is a simpler algorithm.  For example, there's a formula to compute the sum of squares, so we can override the generic iterative version with a more performant solution:

一般的な実装に頼ることもできますが、よりシンプルなアルゴリズムがあることがわかっている場合は、
特定のメソッドを拡張することもできます。例えば、平方数の和を計算する式があるため、
より一般的な反復バージョンを、より効果的なソリューションで上書きすることができます。:

.. doctest::

    julia> Base.sum(S::Squares) = (n = S.count; return n*(n+1)*(2n+1)÷6)
           sum(Squares(1803))
    1955361914

.. 
  This is a very common pattern throughout the Julia standard library: a small set of required methods define an informal interface that enable many fancier behaviors.  In some cases, types will want to additionally specialize those extra behaviors when they know a more efficient algorithm can be used in their specific case.

これは、Juliaの標準ライブラリ全体を通して非常に一般的なパターンです。少数の必須メソッドは、
多くの魅力的な動作を可能にするインフォーマルなインターフェイスを定義します。
より効率的なアルゴリズムを特定のケースで使用できることが分かっている場合、型はそれらの追加の動作をさらに特殊化しようとします。

.. _man-interfaces-indexing:

.. 
  Indexing
  --------

インデックス
--------

.. 
  ====================================== ==================================
  Methods to implement                   Brief description
  ====================================== ==================================
  :func:`getindex(X, i) <getindex>`      ``X[i]``, indexed element access
  :func:`setindex!(X, v, i) <setindex!>` ``X[i] = v``, indexed assignment
  :func:`endof(X) <endof>`               The last index, used in ``X[end]``
  ====================================== ==================================

====================================== ==================================
実装するメソッド                          概要
====================================== ==================================
:func:`getindex(X, i) <getindex>`      ``X[i]`` 、インデックスされた要素のアクセス
:func:`setindex!(X, v, i) <setindex!>` ``X[i] = v`` 、インデックスされた割り当て
:func:`endof(X) <endof>`               ``X[end]`` に使用された最後のインデックス
====================================== ==================================

.. 
  For the ``Squares`` iterable above, we can easily compute the ``i``\ th element of the sequence by squaring it.  We can expose this as an indexing expression ``S[i]``.  To opt into this behavior, ``Squares`` simply needs to define :func:`getindex`:

上記の反復可能な「Squares」については、シーケンスの ``i``\ 番目の要素はそれを2乗することで簡単に計算できます。
これをインデックスの式 ``S[i]`` として記述することができます。この動作に加わるためには、
``Squares`` は単に :func:`getindex` を定義する必要があります。:

.. doctest::

    julia> function Base.getindex(S::Squares, i::Int)
               1 <= i <= S.count || throw(BoundsError(S, i))
               return i*i
           end
           Squares(100)[23]
    529

.. 
  Additionally, to support the syntax ``S[end]``, we must define :func:`endof` to specify the last valid index:

さらに、構文 ``S[end]`` をサポートするには、最後の有効なインデックスを指定するために :func:`endof` を定義する必要があります。:

.. doctest::

    julia> Base.endof(S::Squares) = length(S)
           Squares(23)[end]
    529

.. 
  Note, though, that the above *only* defines :func:`getindex` with one integer index. Indexing with anything other than an ``Int`` will throw a ``MethodError`` saying that there was no matching method.  In order to support indexing with ranges or vectors of Ints, separate methods must be written:

ただし、上の例では :func:`getindex` と整数インデックスを1つだけ定義していることに注意してください。
``Int`` 以外のものでインデックスを作成すると、一致するメソッドがないことを示す ``MethodError`` がスローされます。
Intの範囲やベクトルでインデックスを作成するには、別のメソッドを記述する必要があります。:

.. doctest::

    julia> Base.getindex(S::Squares, i::Number) = S[convert(Int, i)]
           Base.getindex(S::Squares, I) = [S[i] for i in I]
           Squares(10)[[3,4.,5]]
    3-element Array{Int64,1}:
      9
     16
     25

.. 
  While this is starting to support more of the :ref:`indexing operations supported by some of the builtin types <man-array-indexing>`, there's still quite a number of behaviors missing. This ``Squares`` sequence is starting to look more and more like a vector as we've added behaviors to it. Instead of defining all these behaviors ourselves, we can officially define it as a subtype of an ``AbstractArray``.

これは「いくつかのビルトインの型でサポートされているインデックス処理」の多くをサポートし始めていますが、
依然として全ての動作ではありません。この ``Squares`` シーケンスは、私たちが動作を追加するごとに、
ベクトルのような動作をします。これらの動作を全て自分で定義するのではなく、
``AbstractArray`` のサブタイプとして正式に定義することができます。

.. _man-interfaces-abstractarray:

.. 
  Abstract Arrays
  ---------------

抽象配列
---------------

..
  ===================================================================== ============================================ =======================================================================================
  Methods to implement                                                                                               Brief description
  ===================================================================== ============================================ =======================================================================================
  :func:`size(A) <size>`                                                                                             Returns a tuple containing the dimensions of ``A``
  :func:`getindex(A, i::Int) <getindex>`                                                                             (if ``LinearFast``) Linear scalar indexing
  :func:`getindex(A, I::Vararg{Int, N}) <getindex>`                                                                  (if ``LinearSlow``, where ``N = ndims(A)``) N-dimensional scalar indexing
  :func:`setindex!(A, v, i::Int) <setindex!>`                                                                        (if ``LinearFast``) Scalar indexed assignment
  :func:`setindex!(A, v, I::Vararg{Int, N}) <setindex!>`                                                             (if ``LinearSlow``, where ``N = ndims(A)``) N-dimensional scalar indexed assignment
  **Optional methods**                                                  **Default definition**                       **Brief description**
  :func:`Base.linearindexing(::Type) <Base.linearindexing>`             ``Base.LinearSlow()``                        Returns either ``Base.LinearFast()`` or ``Base.LinearSlow()``. See the description below.
  :func:`getindex(A, I...) <getindex>`                                  defined in terms of scalar :func:`getindex`  :ref:`Multidimensional and nonscalar indexing <man-array-indexing>`
  :func:`setindex!(A, I...) <setindex!>`                                defined in terms of scalar :func:`setindex!` :ref:`Multidimensional and nonscalar indexed assignment <man-array-indexing>`
  :func:`start`/:func:`next`/:func:`done`                               defined in terms of scalar :func:`getindex`  Iteration
  :func:`length(A) <length>`                                            ``prod(size(A))``                            Number of elements
  :func:`similar(A) <similar>`                                          ``similar(A, eltype(A), size(A))``           Return a mutable array with the same shape and element type
  :func:`similar(A, ::Type{S}) <similar>`                               ``similar(A, S, size(A))``                   Return a mutable array with the same shape and the specified element type
  :func:`similar(A, dims::NTuple{Int}) <similar>`                       ``similar(A, eltype(A), dims)``              Return a mutable array with the same element type and size `dims`
  :func:`similar(A, ::Type{S}, dims::NTuple{Int}) <similar>`            ``Array{S}(dims)``                           Return a mutable array with the specified element type and size
  **Non-traditional indices**                                           **Default definition**                       **Brief description**
  :func:`indices(A) <indices>`                                          ``map(OneTo, size(A))``                      Return the ``AbstractUnitRange`` of valid indices
  :func:`Base.similar(A, ::Type{S}, inds::NTuple{Ind}) <similar>`       ``similar(A, S, Base.to_shape(inds))``       Return a mutable array with the specified indices ``inds`` (see below)
  :func:`Base.similar(T::Union{Type,Function}, inds) <similar>`         ``T(Base.to_shape(inds))``                   Return an array similar to ``T`` with the specified indices ``inds`` (see below)
  ===================================================================== ============================================ =======================================================================================

                                                                                                      概要
===================================================================== ============================================ =======================================================================================
実装するメソッド                                                                                                      概要
===================================================================== ============================================ =======================================================================================
:func:`size(A) <size>`                                                                                             ``A`` の次元を含むタプルを返す
:func:`getindex(A, i::Int) <getindex>`                                                                             （``LinearFast`` の場合は） 線形スカラーインデックス
:func:`getindex(A, I::Vararg{Int, N}) <getindex>`                                                                  （ ``N = ndims(A)`` である ``LinearSlow`` の場合は )N次元スカラーインデックス
:func:`setindex!(A, v, i::Int) <setindex!>`                                                                        （ ``LinearFast`` の場合は）スカラーインデックスの割り当て
:func:`setindex!(A, v, I::Vararg{Int, N}) <setindex!>`                                                             （ ``N = ndims(A)`` である ``LinearSlow`` の場合は） N次元のスカラーインデックスの割り当て
**オプションのメソッド**                                                  **デフォルト定義**                             **概要**
:func:`Base.linearindexing(::Type) <Base.linearindexing>`             ``Base.LinearSlow()``                        ``Base.LinearFast()`` もしくは ``Base.LinearSlow()`` を返す。下記説明を参照。
:func:`getindex(A, I...) <getindex>`                                  スカラー :func:`getindex` の観点から定義される    詳細は :ref:`多次元と非スカラーインデックス <man-配列のインデックス>`                                 
:func:`setindex!(A, I...) <setindex!>`                                スカラー :func:`setindex!` の観点から定義される   詳細は :ref:`多次元と非スカラーインデックス <man-配列のインデックス>`
:func:`start`/:func:`next`/:func:`done`                               スカラー :func:`getindex`  の観点から定義される   反復
:func:`length(A) <length>`                                            ``prod(size(A))``                            要素数
:func:`similar(A) <similar>`                                          ``similar(A, eltype(A), size(A))``           同じ形と要素の型を持つ可変配列を返す
:func:`similar(A, ::Type{S}) <similar>`                               ``similar(A, S, size(A))``                   同じ形と指定された要素の型の可変配列を返す
:func:`similar(A, dims::NTuple{Int}) <similar>`                       ``similar(A, eltype(A), dims)``              同じ要素の型とサイズの可変配列を返す
:func:`similar(A, ::Type{S}, dims::NTuple{Int}) <similar>`            ``Array{S}(dims)``                           指定された要素の型とサイズの可変配列を返す
**既存と異なるインデックス**                                              **デフォルト定義**                             **概要**
:func:`indices(A) <indices>`                                          ``map(OneTo, size(A))``                      有効なインデックスの ``AbstractUnitRange`` を返す
:func:`Base.similar(A, ::Type{S}, inds::NTuple{Ind}) <similar>`       ``similar(A, S, Base.to_shape(inds))``       指定されたインデックス ``inds`` の可変配列を返す（下記参照）
:func:`Base.similar(T::Union{Type,Function}, inds) <similar>`         ``T(Base.to_shape(inds))``                   ``T`` に似た配列を指定されたインデックス ``inds`` で返す（下記参照）
===================================================================== ============================================ =======================================================================================

.. 
  If a type is defined as a subtype of ``AbstractArray``, it inherits a very large set of rich behaviors including iteration and multidimensional indexing built on top of single-element access.  See the :ref:`arrays manual page <man-arrays>` and :ref:`standard library section <stdlib-arrays>` for more supported methods.

型が ``AbstractArray`` のサブタイプとして定義されている場合は、シングル要素アクセスの上に構築された
反復処理や多次元インデックスを含む非常に大きな一連の動作を継承します。その他の多くのサポートされている
メソッドについては、 :ref:`マニュアルの配列 <man-配列>` と :ref:`標準ライブラリ <stdlib配列>` を参照してください。

.. 
  A key part in defining an ``AbstractArray`` subtype is :func:`Base.linearindexing`. Since indexing is such an important part of an array and often occurs in hot loops, it's important to make both indexing and indexed assignment as efficient as possible.  Array data structures are typically defined in one of two ways: either it most efficiently accesses its elements using just one index (linear indexing) or it intrinsically accesses the elements with indices specified for every dimension.  These two modalities are identified by Julia as ``Base.LinearFast()`` and ``Base.LinearSlow()``.  Converting a linear index to multiple indexing subscripts is typically very expensive, so this provides a traits-based mechanism to enable efficient generic code for all array types.

``AbstractArray`` サブタイプを定義する上で重要な部分は、 :func:`Base.linearindexing` です。
インデックスは配列の重要な部分であり、また頻繁にホット・ループ内で行われるため、
インデックスとインデックスの割り当ての両方を効率的に行うことが重要です。配列データ構造は、
通常、1つのインデックス（線形インデックス）を使用して要素に最も効率的にアクセスするか、
全ての次元に指定されたインデックスを持つ要素に本質的にアクセスするかの2つの方法のいずれかで定義されます。
これら2つの方法は、Juliaにより ``Base.LinearFast()`` および ``Base.LinearSlow()`` として識別されます。
線形インデックスを複数のインデックス付きサブスクリプトに変換するのは通常非常にコストがかかるため、
全ての配列型に対して効率的な汎用コードを可能にする特性に基づくのメカニズムを提供します。

.. 
  This distinction determines which scalar indexing methods the type must define. ``LinearFast()`` arrays are simple: just define :func:`getindex(A::ArrayType, i::Int) <getindex>`.  When the array is subsequently indexed with a multidimensional set of indices, the fallback :func:`getindex(A::AbstractArray, I...)` efficiently converts the indices into one linear index and then calls the above method. ``LinearSlow()`` arrays, on the other hand, require methods to be defined for each supported dimensionality with ``ndims(A)`` ``Int`` indices.  For example, the builtin ``SparseMatrixCSC`` type only supports two dimensions, so it just defines :func:`getindex(A::SparseMatrixCSC, i::Int, j::Int)`.  The same holds for :func:`setindex!`.

この区別は、型が定義しなければならないスカラーインデックスメソッドを決定します。 ``LinearFast()`` 配列はシンプルであり、
:func:`getindex(A::ArrayType, i::Int) <getindex>` を定義します。その後、配列が多次元インデックスでインデックス付けされると、
フォールバック :func:`getindex(A::AbstractArray, I...)` はインデックスを1つの線形インデックスに効率的に変換し、
上記のメソッドを呼び出します。一方、 ``LinearSlow()`` 配列では、 ``ndims(A)`` ``Int`` インデックスを使用して、
サポートされている次元ごとにメソッドを定義する必要があります。例えば、ビルトインの ``SparseMatrixCSC`` 型は2つの次元しかサポートしないため、
:func:`getindex(A::SparseMatrixCSC, i::Int, j::Int)` を定義します。 :func:`setindex!` についても同様です。

.. 
  Returning to the sequence of squares from above, we could instead define it as a subtype of an ``AbstractArray{Int, 1}``:

上記の平方数のシーケンスに戻り、それを ``AbstractArray{Int, 1}`` のサブタイプとして定義することもできます。:

.. doctest::

    julia> immutable SquaresVector <: AbstractArray{Int, 1}
               count::Int
           end
           Base.size(S::SquaresVector) = (S.count,)
           Base.linearindexing{T<:SquaresVector}(::Type{T}) = Base.LinearFast()
           Base.getindex(S::SquaresVector, i::Int) = i*i;

.. 
  Note that it's very important to specify the two parameters of the ``AbstractArray``; the first defines the :func:`eltype`, and the second defines the :func:`ndims`.  That supertype and those three methods are all it takes for ``SquaresVector`` to be an iterable, indexable, and completely functional array:

``AbstractArray`` の2つのパラメータを指定することは非常に重要となります。最初のパラメータは :func:`eltype` を定義し、
2番目は :func:`ndims` を定義します。その上位タイプと3つのメソッドは、 ``SquaresVector`` が反復可能、インデックス可能、
および機能的な配列となるために必要な要素の全てです。:

.. testsetup::

    srand(1);

.. doctest::

    julia> s = SquaresVector(7)
    7-element SquaresVector:
      1
      4
      9
     16
     25
     36
     49

    julia> s[s .> 20]
    3-element Array{Int64,1}:
     25
     36
     49

    julia> s \ rand(7,2)
    1×2 Array{Float64,2}:
     0.0151876  0.0179393

.. 
  As a more complicated example, let's define our own toy N-dimensional sparse-like array type built on top of ``Dict``:

より複雑な例として、 ``Dict`` 上に構築されたN次元のまばらな配列型を定義してみましょう。:

.. doctest::

    julia> immutable SparseArray{T,N} <: AbstractArray{T,N}
               data::Dict{NTuple{N,Int}, T}
               dims::NTuple{N,Int}
           end
           SparseArray{T}(::Type{T}, dims::Int...) = SparseArray(T, dims)
           SparseArray{T,N}(::Type{T}, dims::NTuple{N,Int}) = SparseArray{T,N}(Dict{NTuple{N,Int}, T}(), dims)
    SparseArray{T,N}

    julia> Base.size(A::SparseArray) = A.dims
           Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims) = SparseArray(T, dims)
           # Define scalar indexing and indexed assignment
           Base.getindex{T,N}(A::SparseArray{T,N}, I::Vararg{Int,N})     = get(A.data, I, zero(T))
           Base.setindex!{T,N}(A::SparseArray{T,N}, v, I::Vararg{Int,N}) = (A.data[I] = v)

.. 
  Notice that this is a ``LinearSlow`` array, so we must manually define :func:`getindex` and :func:`setindex!` at the dimensionality of the array.  Unlike the ``SquaresVector``, we are able to define :func:`setindex!`, and so we can mutate the array:

これは ``LinearSlow`` 配列であるため、配列の次元数で :func:`getindex` と :func:`setindex!` をマニュアルで定義する必要があります。
``SquaresVector`` とは異なり、 :func:`setindex!` を定義することができるため、配列を変更することができます。:


.. doctest::

    julia> A = SparseArray(Float64,3,3)
    3×3 SparseArray{Float64,2}:
     0.0  0.0  0.0
     0.0  0.0  0.0
     0.0  0.0  0.0

    julia> rand!(A)
    3×3 SparseArray{Float64,2}:
     0.28119   0.0203749  0.0769509
     0.209472  0.287702   0.640396
     0.251379  0.859512   0.873544

    julia> A[:] = 1:length(A); A
    3×3 SparseArray{Float64,2}:
     1.0  4.0  7.0
     2.0  5.0  8.0
     3.0  6.0  9.0

.. 
  The result of indexing an ``AbstractArray`` can itself be an array (for instance when indexing by a ``Range``). The ``AbstractArray`` fallback methods use :func:`similar` to allocate an ``Array`` of the appropriate size and element type, which is filled in using the basic indexing method described above. However, when implementing an array wrapper you often want the result to be wrapped as well:

``AbstractArray`` のインデックス結果は、それ自体が配列である可能性があります（たとえば、 ``Range`` によるインデックスの場合）。
``AbstractArray`` フォールバックメソッドは :func:`similar` を使用して、適切なサイズと、
前述の基本的なインデックスメソッドを使用して埋められた要素型の ``Array`` を割り当てます。
しかし、配列ラッパーを実装するときには、結果をラッピングした方がよい場合もしばしばあります。:

.. doctest::

    julia> A[1:2,:]
    2×3 SparseArray{Float64,2}:
     1.0  4.0  7.0
     2.0  5.0  8.0

.. 
  In this example it is accomplished by defining ``Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`` to create the appropriate wrapped array. (Note that while ``similar`` supports 1- and 2-argument forms, in most case you only need to specialize the 3-argument form.) For this to work it's important that ``SparseArray`` is mutable (supports ``setindex!``). :func:`similar` is also used to allocate result arrays for arithmetic on ``AbstractArrays``, for instance:

この例では、 ``Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`` を定義することで、
適切にラッピングされた配列を作成します（ ``similar`` は1または2引数の形をとりますが、
ほとんどのケースでは3引数で専門性を高める必要があります）。これが機能するようにするためには、
``SparseArray`` が変更可能であることが重要です（ ``setindex!`` をサポートしています）。
:func:`similar` は、 ``AbstractArrays`` での演算に結果の配列を割り当てるためにも使用されます。例えば、:


.. doctest::

    julia> A + 4
    3×3 SparseArray{Float64,2}:
     5.0   8.0  11.0
     6.0   9.0  12.0
     7.0  10.0  13.0

.. 
  In addition to all the iterable and indexable methods from above, these types can also interact with each other and use all of the methods defined in the standard library for ``AbstractArrays``:

上記の反復可能なメソッドとインデックス可能なメソッドに加えて、これらの型は相互にやりとりして、
``AbstractArrays`` の標準ライブラリで定義されている全てのメソッドを使用することもできます。:

.. doctest::

    julia> A[SquaresVector(3)]
    3-element SparseArray{Float64,1}:
     1.0
     4.0
     9.0

    julia> dot(A[:,1],A[:,2])
    32.0

.. 
  If you are defining an array type that allows non-traditional indexing
  (indices that start at something other than 1), you should specialize
  ``indices``.  You should also specialize ``similar`` so that the
  ``dims`` argument (ordinarily a ``Dims`` size-tuple) can accept
  ``AbstractUnitRange`` objects, perhaps range-types ``Ind`` of your own
  design.  For more information, see :ref:`devdocs-offsetarrays`.
  
従来とは異なるインデックス（1以外から始まるインデックス）を許容する配列型を定義する場合、
インデックスを特殊化する必要があります。また、 ``dims`` 引数（通常は ``Dims`` サイズタプル）が
``AbstractUnitRange`` オブジェクトを許容できるように、おそらく独自の設計のrange型 ``Ind`` を、
同様に特殊化する必要があります。詳細は :ref:`devdocs-offsetarrays` を参照ください。
