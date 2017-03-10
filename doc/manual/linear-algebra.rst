.. currentmodule:: Base

.. 
 ****************
  Linear algebra
 ****************

****************
 線形代数
****************

.. 
 Matrix factorizations
 =====================

行列の分解
=====================

.. 
 `Matrix factorizations (a.k.a. matrix decompositions) <https://en.wikipedia.org/wiki/Matrix_decomposition>`_
 compute the factorization of a matrix into a product of matrices, and
 are one of the central concepts in linear algebra.

`行列の分解 <https://en.wikipedia.org/wiki/Matrix_decomposition>`_ は、
行列を行列の積に分解する計算であり、線形代数の中心概念の1つです。

.. 
 The following table summarizes the types of matrix factorizations that have been
 implemented in Julia. Details of their associated methods can be found
 in the :ref:`stdlib-linalg` section of the standard library documentation.

次の表は、Juliaに実装されている行列分解のタイプがまとめられています。関連するメソッドの詳細は、
標準ライブラリドキュメントの :ref:`stdlib-linalg` セクションを参照してください。

.. 
 ======================== ======
 :class:`Cholesky`        `Cholesky factorization <https://en.wikipedia.org/wiki/Cholesky_decomposition>`_
 :class:`CholeskyPivoted` `Pivoted <https://en.wikipedia.org/wiki/Pivot_element>`_ Cholesky factorization
 :class:`LU`              `LU factorization <https://en.wikipedia.org/wiki/LU_decomposition>`_
 :class:`LUTridiagonal`   LU factorization for Tridiagonal matrices
 :class:`UmfpackLU`       LU factorization for sparse matrices (computed by UMFPack)
 :class:`QR`              `QR factorization <https://en.wikipedia.org/wiki/QR_decomposition>`_
 :class:`QRCompactWY`     Compact WY form of the QR factorization
 :class:`QRPivoted`       Pivoted `QR factorization <https://en.wikipedia.org/wiki/QR_decomposition>`_
 :class:`Hessenberg`      `Hessenberg decomposition <http://mathworld.wolfram.com/HessenbergDecomposition.html>`_
 :class:`Eigen`           `Spectral decomposition <https://en.wikipedia.org/wiki/Eigendecomposition_(matrix)>`_
 :class:`SVD`             `Singular value decomposition <https://en.wikipedia.org/wiki/Singular_value_decomposition>`_
 :class:`GeneralizedSVD`  `Generalized SVD <https://en.wikipedia.org/wiki/Generalized_singular_value_decomposition#Higher_order_version>`_
 ======================== ======

======================== ======
:class:`Cholesky`        `コレスキー分解 <https://en.wikipedia.org/wiki/Cholesky_decomposition>`_
:class:`CholeskyPivoted` コレスキー分解の `ピボット <https://en.wikipedia.org/wiki/Pivot_element>`_ 
:class:`LU`              `LU分解 <https://en.wikipedia.org/wiki/LU_decomposition>`_
:class:`LUTridiagonal`   三重対角行列のLU分解
:class:`UmfpackLU`       スパース行列のLU分解（UMFPackで計算）
:class:`QR`              `QR分解 <https://en.wikipedia.org/wiki/QR_decomposition>`_
:class:`QRCompactWY`     CompacコンパクトなWY形式のQR分解t WY form of the QR factorization
:class:`QRPivoted`       `QR分解 <https://en.wikipedia.org/wiki/QR_decomposition>`_ のピボット
:class:`Hessenberg`      `ヘッセンベルク分解 <http://mathworld.wolfram.com/HessenbergDecomposition.html>`_
:class:`Eigen`           `スペクトル分解 <https://en.wikipedia.org/wiki/Eigendecomposition_(matrix)>`_
:class:`SVD`             `特異値分解 <https://en.wikipedia.org/wiki/Singular_value_decomposition>`_
:class:`GeneralizedSVD`  `一般化された特異値分解 <https://en.wikipedia.org/wiki/Generalized_singular_value_decomposition#Higher_order_version>`_
======================== ======

.. 
 Special matrices
 ================

特殊な行列
================

.. 
 `Matrices with special symmetries and structures <http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=3274>`_
 arise often in linear algebra and are frequently associated with
 various matrix factorizations.
 Julia features a rich collection of special matrix types, which allow for fast
 computation with specialized routines that are specially developed for
 particular matrix types.

`特別な対称性と構造を持つ行列 <http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=3274>`_ は
線形代数で頻繁に発生し、しばしば様々な行列分解を伴う。Juliaには特別な行列の豊富なコレクションがあり、
特定の行列用に特別に開発された特殊なルーチンで高速計算が可能です。

.. 
 The following tables summarize the types of special matrices that have been
 implemented in Julia, as well as whether hooks to various optimized methods
 for them in LAPACK are available.

次の表は、Juliaで実装されている特殊な行列のタイプと、LAPACKのさまざまな最適化されたメソッドへの接続が利用可能かどうかをまとめたものです。

.. 
 ======================== ==================================================================================
 :class:`Hermitian`       `Hermitian matrix <https://en.wikipedia.org/wiki/Hermitian_matrix>`_
 :class:`UpperTriangular` Upper `triangular matrix <https://en.wikipedia.org/wiki/Triangular_matrix>`_
 :class:`LowerTriangular` Lower `triangular matrix <https://en.wikipedia.org/wiki/Triangular_matrix>`_
 :class:`Tridiagonal`     `Tridiagonal matrix <https://en.wikipedia.org/wiki/Tridiagonal_matrix>`_
 :class:`SymTridiagonal`  Symmetric tridiagonal matrix
 :class:`Bidiagonal`      Upper/lower `bidiagonal matrix <https://en.wikipedia.org/wiki/Bidiagonal_matrix>`_
 :class:`Diagonal`        `Diagonal matrix <https://en.wikipedia.org/wiki/Diagonal_matrix>`_
 :class:`UniformScaling`  `Uniform scaling operator <https://en.wikipedia.org/wiki/Uniform_scaling>`_
 ======================== ==================================================================================

======================== ==================================================================================
:class:`Hermitian`       `エルミート行列 <https://en.wikipedia.org/wiki/Hermitian_matrix>`_
:class:`UpperTriangular` `上三角行列 <https://en.wikipedia.org/wiki/Triangular_matrix>`_
:class:`LowerTriangular` `下三角行列 <https://en.wikipedia.org/wiki/Triangular_matrix>`_
:class:`Tridiagonal`     `三重対角行列 <https://en.wikipedia.org/wiki/Tridiagonal_matrix>`_
:class:`SymTridiagonal`  対称三重対角行列
:class:`Bidiagonal`      `上/下二重対角行列 <https://en.wikipedia.org/wiki/Bidiagonal_matrix>`_
:class:`Diagonal`        `対角行列 <https://en.wikipedia.org/wiki/Diagonal_matrix>`_
:class:`UniformScaling`  `均等スケーリング演算子 <https://en.wikipedia.org/wiki/Uniform_scaling>`_
======================== ==================================================================================

.. 
  Elementary operations
  ---------------------

初等オペレーション
---------------------

.. 
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | Matrix type             | ``+`` | ``-`` | ``*`` | ``\`` | Other functions with           |
 |                         |       |       |       |       | optimized methods              |
 +=========================+=======+=======+=======+=======+================================+
 | :class:`Hermitian`      |       |       |       |   MV  | :func:`inv`,                   |
 |                         |       |       |       |       | :func:`sqrtm`, :func:`expm`    |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`UpperTriangular`|       |       |  MV   |   MV  | :func:`inv`, :func:`det`       |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`LowerTriangular`|       |       |  MV   |   MV  | :func:`inv`, :func:`det`       |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`SymTridiagonal` |   M   |   M   |  MS   |   MV  | :func:`eigmax`, :func:`eigmin` |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`Tridiagonal`    |   M   |   M   |  MS   |   MV  |                                |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`Bidiagonal`     |   M   |   M   |  MS   |   MV  |                                |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`Diagonal`       |   M   |   M   |  MV   |   MV  | :func:`inv`, :func:`det`,      |
 |                         |       |       |       |       | :func:`logdet`, :func:`/`      |
 +-------------------------+-------+-------+-------+-------+--------------------------------+
 | :class:`UniformScaling` |   M   |   M   |  MVS  |  MVS  | :func:`/`                      |
 +-------------------------+-------+-------+-------+-------+--------------------------------+

+-------------------------+-------+-------+-------+-------+--------------------------------+
|行列の種類　　　            | ``+`` | ``-`` | ``*`` | ``\`` | 最適化されたメソッドを持つ他       |
|                         |       |       |       |       | の関数                          |
+=========================+=======+=======+=======+=======+================================+
| :class:`Hermitian`      |       |       |       |   MV  | :func:`inv`,                   |
|                         |       |       |       |       | :func:`sqrtm`, :func:`expm`    |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`UpperTriangular`|       |       |  MV   |   MV  | :func:`inv`, :func:`det`       |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`LowerTriangular`|       |       |  MV   |   MV  | :func:`inv`, :func:`det`       |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`SymTridiagonal` |   M   |   M   |  MS   |   MV  | :func:`eigmax`, :func:`eigmin` |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`Tridiagonal`    |   M   |   M   |  MS   |   MV  |                                |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`Bidiagonal`     |   M   |   M   |  MS   |   MV  |                                |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`Diagonal`       |   M   |   M   |  MV   |   MV  | :func:`inv`, :func:`det`,      |
|                         |       |       |       |       | :func:`logdet`, :func:`/`      |
+-------------------------+-------+-------+-------+-------+--------------------------------+
| :class:`UniformScaling` |   M   |   M   |  MVS  |  MVS  | :func:`/`                      |
+-------------------------+-------+-------+-------+-------+--------------------------------+

.. 
  Legend:

凡例

.. 
  +------------+---------------------------------------------------------------+
  | M (matrix) | An optimized method for matrix-matrix operations is available |
  +------------+---------------------------------------------------------------+
  | V (vector) | An optimized method for matrix-vector operations is available |
  +------------+---------------------------------------------------------------+
  | S (scalar) | An optimized method for matrix-scalar operations is available |
  +------------+---------------------------------------------------------------+

+------------+---------------------------------------------------------------+
| M (matrix) | 行列 - 行列操作のための最適化されたメソッドが利用可能                 |
+------------+---------------------------------------------------------------+
| V (vector) | 行列 - ベクトル演算のための最適化されたメソッドが利用可能              |
+------------+---------------------------------------------------------------+
| S (scalar) | 行列 - スカラー演算のための最適化されたメソッドが利用可                |
+------------+---------------------------------------------------------------+

.. 
  Matrix factorizations
  ---------------------

行列分解
---------------------

.. 
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | Matrix type             | LAPACK | :func:`eig` | :func:`eigvals` | :func:`eigvecs` | :func:`svd` | :func:`svdvals` |
  +=========================+========+=============+=================+=================+=============+=================+
  | :class:`Hermitian`      |   HE   |             |       ARI       |                 |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`UpperTriangular`|   TR   |      A      |        A        |       A         |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`LowerTriangular`|   TR   |      A      |        A        |       A         |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`SymTridiagonal` |   ST   |      A      |       ARI       |       AV        |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`Tridiagonal`    |   GT   |             |                 |                 |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`Bidiagonal`     |   BD   |             |                 |                 |      A      |         A       |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
  | :class:`Diagonal`       |   DI   |             |        A        |                 |             |                 |
  +-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+

+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| 行列の種類                | LAPACK | :func:`eig` | :func:`eigvals` | :func:`eigvecs` | :func:`svd` | :func:`svdvals` |
+=========================+========+=============+=================+=================+=============+=================+
| :class:`Hermitian`      |   HE   |             |       ARI       |                 |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`UpperTriangular`|   TR   |      A      |        A        |       A         |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`LowerTriangular`|   TR   |      A      |        A        |       A         |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`SymTridiagonal` |   ST   |      A      |       ARI       |       AV        |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`Tridiagonal`    |   GT   |             |                 |                 |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`Bidiagonal`     |   BD   |             |                 |                 |      A      |         A       |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+
| :class:`Diagonal`       |   DI   |             |        A        |                 |             |                 |
+-------------------------+--------+-------------+-----------------+-----------------+-------------+-----------------+

.. 
  Legend:

凡例

.. 
  +--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
  | A (all)      | An optimized method to find all the characteristic values and/or vectors is available                                             | e.g. ``eigvals(M)``    |
  +--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
  | R (range)    | An optimized method to find the ``il``:sup:`th` through the ``ih``:sup:`th` characteristic values are available                   | ``eigvals(M, il, ih)`` |
  +--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
  | I (interval) | An optimized method to find the characteristic values in the interval [``vl``, ``vh``] is available                               | ``eigvals(M, vl, vh)`` |
  +--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
  | V (vectors)  | An optimized method to find the characteristic vectors corresponding to the characteristic values ``x=[x1, x2,...]`` is available | ``eigvecs(M, x)``      |
  +--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+

+--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
| A (all)      | 全ての特性値および/またはベクトルを見つける最適化されたメソッドが利用可能                                                                     | 例 ``eigvals(M)``      |
+--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
| R (range)    | ``ih``:sup:`th` 特性値を通じて ``il``:sup:`th` を見つける最適化されたメソッドが利用可能                                                    | ``eigvals(M, il, ih)`` |
+--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
| I (interval) | インターバル [``vl``, ``vh``] で特性値を見つける最適化されたメソッドが利用可能                                                              | ``eigvals(M, vl, vh)`` |
+--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+
| V (vectors)  | 特性値 ``x=[x1, x2,...]`` に対応する特性ベクトルを見つける最適化されたメソッドが利用可能                                                     | ``eigvecs(M, x)``      |
+--------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------+

.. 
  The uniform scaling operator
  ----------------------------

均等スケーリング演算子
----------------------------

.. 
  A :class:`UniformScaling` operator represents a scalar times the identity operator, ``λ*I``. The identity operator  :class:`I` is defined as a constant and is an instance of :class:`UniformScaling`. The size of these operators are generic and match the other matrix in the binary operations :obj:`+`, :obj:`-`, :obj:`*` and :obj:`\\`. For ``A+I`` and ``A-I`` this means that ``A`` must be square. Multiplication with the identity operator :class:`I` is a noop (except for checking that the scaling factor is one) and therefore almost without overhead.

:class:`UniformScaling` 演算子は、スカラーをアイデンティティ演算子の倍数 ``λ*I`` で表します。
アイデンティティ演算子 :class:`I` は定数として定義され、 :class:`UniformScaling` のインスタンスとなります。
これらの演算子のサイズは一般的であり、他の行列のバイナリ演算 :obj:`+` 、 :obj:`-` 、 :obj:`*` および :obj:`\\` と一致します。
``A+I`` と ``A-I`` の場合、 ``A`` は平行でなければならないことを意味します。
アイデンティティ演算子 :class:`I` の乗算はnoop（スケーリング係数が1であることを確認することを除く）であり、
オーバーヘッドはほとんどありません。
