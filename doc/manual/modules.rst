.. _man-modules:

.. 
*********
 Modules
*********

*********
 モジュール
*********

.. index:: module, baremodule, using, import, export, importall

.. 
 Modules in Julia are separate variable workspaces, i.e. they introduce
 a new global scope. They are delimited syntactically, inside ``module
 Name ... end``. Modules allow you to create top-level definitions (aka
 global variables) without worrying about name conflicts when your code
 is used together with somebody else's. Within a module, you can
 control which names from other modules are visible (via importing),
 and specify which of your names are intended to be public (via
 exporting).

Juliaのモジュールは区別された変数ワークスペースです。例えば、新しいグローバルスコープを導入します。
それらは、 ``module Name ... end`` の中で構文的に区切られます。モジュールは、
コードが他の箇所で一緒に使用されている場合でも、名前の競合を心配することなく、
トップレベルの定義（別名グローバル変数）を作成することを可能にします。モジュール内では、
他のモジュールのどの名前が（インポートによって）表示されるかを制御したり、
どの名前を（エクスポートによって）公開するかを指定することができます。

.. 
 The following example demonstrates the major features of modules. It is
 not meant to be run, but is shown for illustrative purposes::

次の例は、モジュールの主な機能を示しています。これは実行されるためではなく、説明の目的で示されています。::

    module MyModule
    using Lib

    using BigLib: thing1, thing2

    import Base.show

    importall OtherLib

    export MyType, foo

    type MyType
        x
    end

    bar(x) = 2x
    foo(a::MyType) = bar(a.x) + 1

    show(io::IO, a::MyType) = print(io, "MyType $(a.x)")
    end

.. 
 Note that the style is not to indent the body of the module, since
 that would typically lead to whole files being indented.

通常ファイル全体がインデントされるため、コードではモジュールのボディ部を字下げしないことに注意してください。

.. 
 This module defines a type ``MyType``, and two functions. Function
 ``foo`` and type ``MyType`` are exported, and so will be available for
 importing into other modules.  Function ``bar`` is private to
 ``MyModule``.

このモジュールは型 ``MyType`` および2つの関数を定義します。関数 ``foo`` と型 ``MyType`` がエクスポートされるため、
他のモジュールにインポートすることもできます。ファンクション ``bar`` は ``MyModule`` 専用です。

.. 
 The statement ``using Lib`` means that a module called ``Lib`` will be
 available for resolving names as needed. When a global variable is
 encountered that has no definition in the current module, the system
 will search for it among variables exported by ``Lib`` and import it if
 it is found there.
 This means that all uses of that global within the current module will
 resolve to the definition of that variable in ``Lib``.

``using Lib`` ステートメントは、 ``Lib`` モジュールは必要な場合に名前解決が可能であることを意味します。
グローバル変数が現在のモジュールに定義されていない場合、システムは ``Lib`` によりエクスポートされた変数から検索し、
もし一致するものがあればそれをインポートします。これは、現在のモジュール内のグローバルの全ての利用が、
``Lib`` 内の変数の定義により解決されることを意味します。

.. 
 The statement ``using BigLib: thing1, thing2`` is a syntactic shortcut for
 ``using BigLib.thing1, BigLib.thing2``.

ステートメント ``using BigLib: thing1, thing2`` は、 ``using BigLib.thing1, BigLib.thing2`` の構文上のショートカットです。

.. 
 The ``import`` keyword supports all the same syntax as ``using``, but only
 operates on a single name at a time. It does not add modules to be searched
 the way ``using`` does. ``import`` also differs from ``using`` in that
 functions must be imported using ``import`` to be extended with new methods.

``import`` キーワードは、 ``using`` と同じ構文をサポートしますが、一度に1つの名前でしか動作しません。
``import`` は、 ``using`` が行うような方法では、検索されるモジュールを追加することはしません。
``import`` は、新しいメソッドで拡張するために ``import`` を使用してインポートする必要がある点で、
``using`` とは異なります。

.. 
 In ``MyModule`` above we wanted to add a method to the standard ``show``
 function, so we had to write ``import Base.show``.
 Functions whose names are only visible via ``using`` cannot be extended.

上記の ``MyModule`` では、標準の ``show`` 関数にメソッドを追加したいため、
``import Base.show`` を書く必要がありました。 ``using`` を使用することでのみ名前がわかる関数は、
拡張することができません。

.. 
 The keyword ``importall`` explicitly imports all names exported by the
 specified module, as if ``import`` were individually used on all of them.

``importall`` キーワードは、個々に対して ``importall`` が使われたかのように、
指定されたモジュールによってエクスポートされた全ての名前を明示的にインポートします。

.. 
 Once a variable is made visible via ``using`` or ``import``, a module may
 not create its own variable with the same name.
 Imported variables are read-only; assigning to a global variable always
 affects a variable owned by the current module, or else raises an error.

変数を ``using`` または ``import`` で表示すると、モジュールは同じ名前の変数を作成しないことがあります。
インポートされた変数は読み取り専用です。グローバル変数の割り当ては、常に現在のモジュールが所有する変数に影響しますし、
そうでない場合は、エラーが発生します。


.. 
Summary of module usage
^^^^^^^^^^^^^^^^^^^^^^^

モジュールの使用のサマリ
^^^^^^^^^^^^^^^^^^^^^^^

.. 
 To load a module, two main keywords can be used: ``using`` and ``import``. To understand their differences, consider the following example::

モジュールをロードするには、 ``using`` と ``import`` の2つの主要なキーワードを使用できます。それらの違いを理解するには、
次の例を考えてみてください。::

    module MyModule

    export x, y

    x() = "x"
    y() = "y"
    p() = "p"

    end

.. 
 In this module we export the ``x`` and ``y`` functions (with the keyword ``export``), and also have the non-exported function ``p``. There are several different ways to load the Module and its inner functions into the current workspace:

このモジュールは、 ``x`` および ``y`` 関数（ ``export`` キーワードを使用）をエクスポートし、
エクスポートされていない関数 ``p`` も持ちます。モジュールとその内部関数を現在のワークスペースにロードするには、
いくつかの方法があります。:

..
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 |Import Command                      | What is brought into scope                                                                    | Available for method extension                                         |
 +====================================+===============================================================================================+========================================================================+
 | ``using MyModule``                 | All ``export``\ ed names (``x`` and ``y``), ``MyModule.x``, ``MyModule.y`` and ``MyModule.p`` | ``MyModule.x``, ``MyModule.y`` and ``MyModule.p``                      |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``using MyModule.x, MyModule.p``   | ``x`` and ``p``                                                                               |                                                                        |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``using MyModule: x, p``           | ``x`` and ``p``                                                                               |                                                                        |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``import MyModule``                | ``MyModule.x``, ``MyModule.y`` and ``MyModule.p``                                             | ``MyModule.x``, ``MyModule.y`` and ``MyModule.p``                      |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``import MyModule.x, MyModule.p``  | ``x`` and ``p``                                                                               | ``x`` and ``p``                                                        |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``import MyModule: x, p``          | ``x`` and ``p``                                                                               | ``x`` and ``p``                                                        |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
 | ``importall MyModule``             |  All ``export``\ ed names (``x`` and ``y``)                                                   | ``x`` and ``y``                                                        |
 +------------------------------------+-----------------------------------------------------------------------------------------------+------------------------------------------------------------------------+


+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
|インポートコマンド                     | スコープにインポートされるもの                                                                               | 可能なメソッド拡張                                                        |
+====================================+=========================================================================================================+========================================================================+
| ``using MyModule``                 | 全ての ``export``\ された名前 （ ``x`` および ``y`` ）, ``MyModule.x``, ``MyModule.y`` および ``MyModule.p`` | ``MyModule.x``, ``MyModule.y`` および ``MyModule.p``                    |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``using MyModule.x, MyModule.p``   | ``x`` および ``p``                                                                                       |                                                                        |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``using MyModule: x, p``           | ``x`` および ``p``                                                                                       |                                                                        |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``import MyModule``                | ``MyModule.x``, ``MyModule.y`` および ``MyModule.p``                                                     | ``MyModule.x``, ``MyModule.y`` および ``MyModule.p``                    |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``import MyModule.x, MyModule.p``  | ``x`` および ``p``                                                                                       | ``x`` および ``p``                                                      |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``import MyModule: x, p``          | ``x`` および ``p``                                                                                       | ``x`` および ``p``                                                      |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
| ``importall MyModule``             |  全ての ``export``\ された名前 （ ``x`` および ``y`` ）                                                     | ``x`` および ``y``                                                      |
+------------------------------------+---------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+

.. 
 Modules and files
 -----------------

モジュールとファイル
-----------------

.. 
 Files and file names are mostly unrelated to modules; modules are associated
 only with module expressions.
 One can have multiple files per module, and multiple modules per file::

ファイルとファイル名はほとんどがモジュールとは無関係です。モジュールはモジュール式とのみ関連付けられます。
1モジュールに対し複数のファイルを持つことができ、1ファイルに対し複数のモジュールを持つことができます。::

    module Foo

    include("file1.jl")
    include("file2.jl")

    end

.. 
 Including the same code in different modules provides mixin-like behavior.
 One could use this to run the same code with different base definitions,
 for example testing code by running it with "safe" versions of some
 operators::

異なるモジュールに同じコードを組み込むことで、mixinのような動作が実現します。これを使用することで、
異なる基本定義を持つ同じコードを実行することができます。例えば、「安全な」演算子のバージョンでコードを
実行してテストをすることができます。::

    module Normal
    include("mycode.jl")
    end

    module Testing
    include("safe_operators.jl")
    include("mycode.jl")
    end


.. 
 Standard modules
 ----------------

標準モジュール
----------------

.. 
 There are three important standard modules: Main, Core, and Base.

モジュールには、Main、Core、Baseの3つの主要モジュールがあります。

.. 
 Main is the top-level module, and Julia starts with Main set as the
 current module.  Variables defined at the prompt go in Main, and
 ``whos()`` lists variables in Main.

Mainは最上位のモジュールであり、JuliaはカレントのモジュールとしてMainを設定しています。
プロンプトで定義された変数はMainに格納され、 ``whos()`` はMainの変数をリスト化します。

.. 
 Core contains all identifiers considered "built in" to the language, i.e.
 part of the core language and not libraries. Every module implicitly
 specifies ``using Core``, since you can't do anything without those
 definitions.

Coreには、使用している言語に「ビルトイン」とみなされる識別子、例えばライブラリではなくコア言語の一部が格納されます。
これらの定義なしには処理できないため、全てのモジュールは暗黙的に ``using Core`` を定義します。

.. 
 Base is the standard library (the contents of base/). All modules implicitly
 contain ``using Base``, since this is needed in the vast majority of cases.

Baseは標準ライブラリ（base/の内容）です。多くの場合に必要となるため、全てのモジュールは、暗黙的に ``using Base`` を持ちます。


.. 
 Default top-level definitions and bare modules
 ----------------------------------------------

デフォルトの最上位定義とベアモジュール
----------------------------------------------

.. 
 In addition to ``using Base``, modules also automatically contain a definition
 of the ``eval`` function, which evaluates expressions within the context of that module.

``using Base`` に加え、モジュールは、コンテキスト内の式を評価する ``eval`` 関数の定義を自動的に持ちます。

.. 
 If these default definitions are not wanted, modules can be defined using the
 keyword ``baremodule`` instead (note: ``Core`` is still imported, as per above).
 In terms of ``baremodule``, a standard ``module`` looks like this::

これらのデフォルト定義が望ましくない場合は、代わりにキーワード ``baremodule`` を使用してモジュールを定義することができます
（上記のように ``Core`` はインポートされます）。 ``baremodule`` に関して、標準モジュールは次のようになります。::

    baremodule Mod

    using Base

    eval(x) = Core.eval(Mod, x)
    eval(m,x) = Core.eval(m, x)

    ...

    end


.. 
 Relative and absolute module paths
 ----------------------------------

相対モジュールパスと絶対モジュールパス
----------------------------------

.. 
 Given the statement ``using Foo``, the system looks for ``Foo``
 within ``Main``. If the module does not exist, the system
 attempts to ``require("Foo")``, which typically results in loading
 code from an installed package.

``using Foo`` のステートメントが与えられた場合、システムは ``Main`` 内の ``Foo`` を検索します。
モジュールが存在しない場合、システムは ``require("Foo")`` を試み、
これはインストールされたパッケージからコードをロードします。

.. 
 However, some modules contain submodules, which means you sometimes
 need to access a module that is not directly available in ``Main``.
 There are two ways to do this. The first is to use an absolute path,
 for example ``using Base.Sort``. The second is to use a relative path,
 which makes it easier to import submodules of the current module or
 any of its enclosing modules::

しかし、モジュールの中にはサブモジュールが含まれているものがあり、
これは ``Main`` で直接利用できないモジュールにアクセスする必要がある場合があります。
これを行うには2つの方法があります。1つ目は ``using Base.Sort`` などの絶対パスを使用する方法です。
2つ目は、カレントモジュールまたはそのモジュールを含むサブモジュールのインポートを容易にする相対パスを使用する方法です。::

    module Parent

    module Utils
    ...
    end

    using .Utils

    ...
    end

.. 
 Here module ``Parent`` contains a submodule ``Utils``, and code in
 ``Parent`` wants the contents of ``Utils`` to be visible. This is
 done by starting the ``using`` path with a period. Adding more leading
 periods moves up additional levels in the module hierarchy. For example
 ``using ..Utils`` would look for ``Utils`` in ``Parent``'s enclosing
 module rather than in ``Parent`` itself.
 
ここではモジュール ``Parent`` にサブモジュール ``Utils`` が含まれ、
``Utils`` の内容は ``Parent`` のコードに対し表示される必要があります。
これは、 ``using`` パスをピリオドで開始することで行われます。先頭のピリオドを追加すると、
モジュール階層の追加レベルが上がります。例えば ``using ..Utils`` は、
``Parent`` 自体ではなく ``Parent`` に含まれるモジュール内で ``Utils`` を検索します。 

.. 
 Note that relative-import qualifiers are only valid in ``using`` and
 ``import`` statements.

相対インポート修飾子は、 ``using`` および ``import`` ステートメントでのみ有効であることに注意してください。

.. 
 Module file paths
 -----------------

モジュールファイルパス
-----------------

.. 
 The global variable LOAD_PATH contains the directories Julia searches for
 modules when calling ``require``. It can be extended using ``push!``::

グローバル変数LOAD_PATHには、 ``require`` を呼び出すときにJuliaがモジュールを検索するディレクトリが含まれています。
これは ``push!`` を使うことで拡張できます。::

    push!(LOAD_PATH, "/Path/To/My/Module/")

.. 
 Putting this statement in the file ``~/.juliarc.jl`` will extend LOAD_PATH
 on every Julia startup. Alternatively, the module load path can be
 extended by defining the environment variable JULIA_LOAD_PATH.

このステートメントを ``~/.juliarc.jl`` 内に記述すると、Juliaの起動時にLOAD_PATHを拡張します。
または、環境変数JULIA_LOAD_PATHを定義することでモジュールのロードパスを拡張することもできます。


.. 
 Namespace miscellanea
 ---------------------

ネームスペースの集まり
---------------------

.. 
 If a name is qualified (e.g. ``Base.sin``), then it can be accessed even if
 it is not exported. This is often useful when debugging.

名前が修飾されている場合（ ``Base.sin`` など）、エクスポートされていなくてもアクセスできます。
これはデバッグの際に便利です。

.. 
 Macro names are written with ``@`` in import and export statements, e.g.
 ``import Mod.@mac``. Macros in other modules can be invoked as ``Mod.@mac``
 or ``@Mod.mac``.

マクロ名は、 ``import Mod.@mac`` のように、インポートおよびエクスポートステートメントで ``@`` を使って記述されます。
他のモジュールのマクロは、 ``Mod.@mac`` または ``@Mod.mac`` として呼び出すことができます。

.. 
 The syntax ``M.x = y`` does not work to assign a global in another module;
 global assignment is always module-local.

構文 ``M.x = y`` は、別のモジュールでグローバルを割り当てることはできません。
グローバル割り当ては、常にモジュールローカルとなります。

.. 
 A variable can be "reserved" for the current module without assigning to
 it by declaring it as ``global x`` at the top level. This can be used to
 prevent name conflicts for globals initialized after load time.

変数は、上位で ``global x`` として宣言することによって、実際に割り当てることなく、
カレントモジュールの「予約済み」とすることができます。
これは、ロード後に初期化されたグローバルの名前の競合を防ぐために使用できます。

.. _man-modules-initialization-precompilation:

.. 
 Module initialization and precompilation
 ----------------------------------------

モジュールの初期化とプリコンパイル
----------------------------------------

.. 
 Large modules can take several seconds to load because executing all of
 the statements in a module often involves compiling a large amount of code.
 Julia provides the ability to create precompiled versions of modules
 to reduce this time.

モジュール内の全てのステートメントを実行すると、多くの場合、大量のコードをコンパイルする必要があるため、
大きなモジュールはロードに数秒かかることがあります。Juliaにはこの時間を短縮するために、
モジュールのプリコンパイルバージョンを作成する機能を提供します。

.. 
 To create an incremental precompiled module file, add
 ``__precompile__()`` at the top of your module file
 (before the ``module`` starts).
 This will cause it to be automatically compiled the first time it is imported.
 Alternatively, you can manually call ``Base.compilecache(modulename)``.
 The resulting cache files will be stored in ``Base.LOAD_CACHE_PATH[1]``.
 Subsequently, the module is automatically recompiled upon ``import``
 whenever any of its dependencies change;
 dependencies are modules it imports, the Julia build, files it includes,
 or explicit dependencies declared by ``include_dependency(path)`` in the module file(s).

増分プリコンパイルされたモジュールファイルを作成するには、
モジュールファイルの先頭（モジュールが起動する前）に ``__precompile__()`` を追加します。
これにより、最初にインポートされるときに自動的にコンパイルされます。
または、手動で ``Base.compilecache(modulename)`` を呼び出すこともできます。
処理結果のキャッシュファイルは ``Base.LOAD_CACHE_PATH[1]`` に格納されます。
その後、依存関係のいずれかが変更されると、モジュールはインポート時に自動的に再コンパイルされます。
依存関係は、インポートするモジュール、Juliaビルド、ファイル、
またはモジュールファイル内の ``include_dependency(path)`` で宣言された明示的な依存です。

.. 
 For file dependencies, a change is determined by examining whether the modification time (mtime)
 of each file loaded by ``include`` or added explicity by ``include_dependency`` is unchanged,
 or equal to the modification time truncated to the nearest second
 (to accommodate systems that can't copy mtime with sub-second accuracy).
 It also takes into account whether the path to the file chosen by the search logic in ``require``
 matches the path that had created the precompile file.

ファイル依存関係の場合、差異は、 ``include`` によりロードされた、もしくは ``include_dependency`` により
明示的に追加された各ファイルの変更時間（mtime）が変更されていないかを確認することにより判断されるか、
または最も近い秒に切り捨てられた変更時間（秒未満の制度でmtimeをコピーできないシステムに配慮するため）と
等しいかを調べることにより判断されます。また、検索ロジックによって選択されたファイルへのパスが、
プリコンパイルファイルを作成したパスと一致するかどうかも考慮されます。

.. 
 It also takes into account the set of dependencies already loaded into the current process
 and won't recompile those modules, even if their files change or disappear,
 in order to avoid creating incompatibilities between the running system and the precompile cache.
 If you want to have changes to the source reflected in the running system,
 you should call ``reload("Module")`` on the module you changed,
 and any module that depended on it in which you want to see the change reflected.

実行中のシステムとプリコンパイルキャッシュとの間の非互換性を避けるために、ファイルが変更または消滅したとしても、
すでにカレントプロセスにロードされた、およびモジュールをリコンパイルしない依存関係のセットについても考慮に入れます。
実行中のシステムに反映されたソースの変更をしたい場合は、変更したモジュール上、
および変更したモジュールに依存する変更を反映させたい他のモジュール上で ``reload("Module")`` を呼び出す必要があります。

.. 
 Precompiling a module also recursively precompiles any modules that are imported therein.
 If you know that it is *not* safe to precompile your module
 (for the reasons described below), you should put
 ``__precompile__(false)`` in the module file to cause ``Base.compilecache`` to
 throw an error (and thereby prevent the module from being imported by
 any other precompiled module).

また、モジュールをプリコンパイルすると、インポートされたモジュールも再帰的にプリコンパイルされます。
（下記の理由により）モジュールをプリコンパイルすることが安全でないと分かっている場合は、
``Base.compilecache`` によりエラーを発生させるために、モジュールファイル内に ``__precompile__(false)`` を
入れる必要があります（これにより、モジュールが他のプリコンパイルされたモジュールによりインポートされなくなります）。

.. 
 ``__precompile__()`` should *not* be used in a module unless all of its
 dependencies are also using ``__precompile__()``. Failure to do so can result
 in a runtime error when loading the module.

依存関係にあるもの全てが ``__precompile__()`` を使用していない限り、 ``__precompile__()`` を
モジュール内で使用するべきではありません。使用した場合は、モジュールをロードする際にランタイムエラーが発生する可能性があります。

.. 
 In order to make your module work with precompilation,
 however, you may need to change your module to explicitly separate any
 initialization steps that must occur at *runtime* from steps that can
 occur at *compile time*.  For this purpose, Julia allows you to define
 an ``__init__()`` function in your module that executes any
 initialization steps that must occur at runtime.
 This function will not be called during compilation
 (``--output-*`` or ``__precompile__()``).
 You may, of course, call it manually if necessary,
 but the default is to assume this function deals with computing state for
 the local machine, which does not need to be -- or even should not be --
 captured in the compiled image.
 It will be called after the module is loaded into a process,
 including if it is being loaded into an incremental compile
 (``--output-incremental=yes``), but not if it is being loaded
 into a full-compilation process.

しかし、モジュールがプリコンパイルで動作するようにするには、起動時に発生する初期化処理と、
コンパイル時に発生する処理を、明示的に分離する必要があります。この目的のため、
Juliaは、実行時に発生する必要がある初期化処理を実行する ``__init__()`` 関数をモジュール内に定義することができます。
この関数は、コンパイル時（ ``--output-*`` または ``__precompile__()`` ）には呼び出されません。
もちろん、必要に応じて手動で呼び出すこともできますが、デフォルトではこの関数は、
コンパイルされたイメージを取得する必要がないローカルマシンの処理状態を扱うことを想定されています。
これはモジュールがプロセスにロードされた後に呼び出され、これは増分コンパイル（ ``--output-incremental=yes`` ）に
ロードされているかどうかを含みますが、フルコンパイルプロセスにロードされているかは含みません。

.. 
 In particular, if you define a ``function __init__()`` in a module,
 then Julia will call ``__init__()`` immediately *after* the module is
 loaded (e.g., by ``import``, ``using``, or ``require``) at runtime for
 the *first* time (i.e., ``__init__`` is only called once, and only
 after all statements in the module have been executed). Because it is
 called after the module is fully imported, any submodules or other
 imported modules have their ``__init__`` functions called *before* the
 ``__init__`` of the enclosing module.

特に、モジュール内に ``function __init__()`` を定義した場合、Juliaは、モジュールが
（ ``import`` 、 ``using`` または ``require`` により）実行時にロードされた直後に ``__init__()`` を呼び出します
（  ``__init__`` は一度だけ呼び出され、かつモジュール内の全てのステートメントが実行された後のみに呼び出されます）。
モジュールが完全にインポートされた後に呼び出されるため、サブモジュールまたはその他のインポートされたモジュールには、
囲んでいるモジュールの  ``__init__`` より前の呼び出された ``__init__`` 関数を持ちます。

.. 
 Two typical uses of ``__init__`` are calling runtime initialization
 functions of external C libraries and initializing global constants
 that involve pointers returned by external libraries.  For example,
 suppose that we are calling a C library ``libfoo`` that requires us
 to call a ``foo_init()`` initialization function at runtime. Suppose
 that we also want to define a global constant ``foo_data_ptr`` that
 holds the return value of a ``void *foo_data()`` function defined by
 ``libfoo`` — this constant must be initialized at runtime (not at compile
 time) because the pointer address will change from run to run.  You
 could accomplish this by defining the following ``__init__`` function
 in your module::

``__init__`` の2つの典型的な使い方は、外部のCライブラリの実行時初期化関数の呼び出し、
および外部ライブラリから返されるポインタを含むグローバル定数を初期化することです。
例えば、実行時に ``foo_init()`` 初期化関数を呼び出す必要があるCライブラリ ``libfoo`` を呼び出すとします。
また、 ``libfoo`` によって定義された ``void *foo_data()`` 関数の戻り値を保持するグローバル定数 ``foo_data_ptr`` も
定義したいとします。ポインタアドレスは実行するごとに変わるため、この定数は（コンパイル時ではなく）実行時に
初期化する必要があります。これらは、モジュールに ``__init__`` 関数を定義することで実現できます。::

    const foo_data_ptr = Ref{Ptr{Void}}(0)
    function __init__()
        ccall((:foo_init, :libfoo), Void, ())
        foo_data_ptr[] = ccall((:foo_data, :libfoo), Ptr{Void}, ())
    end

.. 
 Notice that it is perfectly possible to define a global inside
 a function like ``__init__``; this is one of the advantages of using a
 dynamic language. But by making it a constant at global scope,
 we can ensure that the type is known to the compiler and allow it to generate
 better optimized code.
 Obviously, any other globals in your module that depends on ``foo_data_ptr``
 would also have to be initialized in ``__init__``.

``__init__`` のような関数内でグローバルを定義することは完全に可能であることに注意してください。
これは動的言語を使用する利点の1つです。しかし、これをグローバルスコープで不変にすることにより、
その型がコンパイラに認識され、より最適化されたコードを生成できるようにすることができます。
``foo_data_ptr`` に依存するモジュール内のその他のグローバルについても、 ``__init__`` で初期化する必要があります。

.. 
 Constants involving most Julia objects that are not produced by
 ``ccall`` do not need to be placed in ``__init__``: their definitions
 can be precompiled and loaded from the cached module image.
 This includes complicated heap-allocated objects like arrays.
 However, any routine that returns a raw pointer value must be called
 at runtime for precompilation to work
 (Ptr objects will turn into null pointers unless they are hidden inside an isbits object).
 This includes the return values of the Julia functions ``cfunction`` and ``pointer``.

``ccall`` によって生成されないほとんどのJuliaオブジェクトを含む定数は、 ``__init__`` に記述する必要はありません。
それらの定義は、プリコンパイルして、キャッシュされたモジュールイメージからロードすることができます。
これには、配列のような複雑なヒープ割り当てオブジェクトが含まれます。
しかし、未処理のポインタ値を返すルーチンは、プリコンパイルために実行時に呼び出さなければなりません
（Ptrオブジェクトは、isbitsオブジェクトの中に隠されていない限り、nullポインタになります）。
これには、Julia関数の ``cfunction`` および ``pointer`` が含まれます。

.. 
 Dictionary and set types, or in general anything that depends on the
 output of a ``hash(key)`` method, are a trickier case.  In the common
 case where the keys are numbers, strings, symbols, ranges, ``Expr``,
 or compositions of these types (via arrays, tuples, sets, pairs, etc.)
 they are safe to precompile.  However, for a few other key types, such
 as ``Function`` or ``DataType`` and generic user-defined types where
 you haven't defined a ``hash`` method, the fallback ``hash`` method
 depends on the memory address of the object (via its ``object_id``)
 and hence may change from run to run.
 If you have one of these key types, or if you aren't sure,
 to be safe you can initialize this dictionary from within your
 ``__init__`` function.
 Alternatively, you can use the ``ObjectIdDict`` dictionary type,
 which is specially handled by precompilation so that it is safe to
 initialize at compile-time.

ディクショナリおよびセット型、または一般的には ``hash(key)`` メソッドの出力に依存するものは、
扱いにくいケースです。keyが数値、文字列、記号、範囲、Expr、またはこれらの型の組み合わせ
（配列、タプル、セット、ペアなど）である一般的なケースでは、それらはプリコンパイルすることができます。
しかし、 ``Function`` や ``DataType`` や ``hash`` メソッドを定義していない一般的なユーザ定義型のような
他のいくつかのkeyの型では、フォールバック ``hash`` メソッドは（ ``object_id`` を介して）オブジェクトの
メモリアドレスに依存し、このために実行する度に変わります。これらのkeyの型のいずれかを持っているか、
またはわからない場合は、 ``__init__`` 関数の中からこのディクショナリを初期化することができます。
または、「ObjectIdDict」ディクショナリ型を使用することもできます。
これは、プリコンパイル時に処理されるため、安全にコンパイル時に初期化できます。

.. 
 When using precompilation, it is important to keep a clear sense of the
 distinction between the compilation phase and the execution phase.
 In this mode, it will often be much more clearly apparent that
 Julia is a compiler which allows execution of arbitrary Julia code,
 not a standalone interpreter that also generates compiled code.

プリコンパイルを使用する場合、コンパイルフェーズと実行フェーズの区別を明確にすることが重要です。
このモードでは、Juliaがコンパイルされたコードを生成するスタンドアロンのインタプリタではなく、
任意のJuliaコードを実行できるコンパイラであることが、はっきりとわかります。

.. 
 Other known potential failure scenarios include:

その他の既知の潜在的な障害シナリオは以下です。:

.. 
 1. Global counters (for example, for attempting to uniquely identify objects)
    Consider the following code snippet::

1. グローバルカウンタ（例えば、オブジェクトを一意に識別しようとする場合）次のコードの一部を考えてみましょう。::

    type UniquedById
        myid::Int
        let counter = 0
            UniquedById() = new(counter += 1)
        end
    end

.. 
    while the intent of this code was to give every instance a unique id,
    the counter value is recorded at the end of compilation.
    All subsequent usages of this incrementally compiled module
    will start from that same counter value.
  
このコードの目的は全てのインスタンスに一意のIDを与えることでしたが、
コンパイルの最後にカウンタ値が記録されます。このインクリメントするようにコンパイルされたモジュールの以降の使用は、
全て同じカウンタ値から開始されます。

.. 
   Note that ``object_id`` (which works by hashing the memory pointer)
   has similar issues (see notes on Dict usage below).

``object_id`` （メモリポインタをハッシュ化することによって動作する）にも同様の問題があることに注意してください
（下記のDictの使用方法に関する注意を参照してください）。

.. 
   One alternative is to store both ``current_module()`` and the current ``counter`` value,
   however, it may be better to redesign the code to not depend on this global state.

1つの代替案は、 ``current_module()`` と現在の ``counter`` 値の両方を格納することですが、
このグローバル状態に依存しないようにコードを再設計するほうがよい場合があります。

.. 
 2. Associative collections (such as ``Dict`` and ``Set``) need to be re-hashed in ``__init__``.
    (In the future, a mechanism may be provided to register an initializer function.)

2. 連結コレクション（ ``Dict`` および ``Set`` ）は ``__init__`` で再ハッシュ化する必要があります
  （将来、初期化関数を登録するための仕組みが提供されるかもしれません）。

.. 
 3. Depending on compile-time side-effects persisting through load-time.
    Example include:
    modifying arrays or other variables in other Julia modules;
    maintaining handles to open files or devices;
    storing pointers to other system resources (including memory);

3. 障害シナリオとして、ロード時間中に持続するコンパイル時間の副作用の依存があります。
   例として、配列またはそのJuliaモジュールの他の変数の変更、ファイルやデバイスを開くためのハンドルの維持、
   他のシステムリソース（メモリを含む）へのポインタの格納が挙げられます。

.. 
 4. Creating accidental "copies" of global state from another module,
    by referencing it directly instead of via its lookup path.
    For example, (in global scope)::

4. 障害シナリオとして、ルックアップパス経由ではなく直接参照することにより、
   別のモジュールからグローバル状態の「コピー」を偶発的に作成することが挙げられます。
   例えば、（グローバルスコープにおいて）::

       #mystdout = Base.STDOUT #= will not work correctly, since this will copy Base.STDOUT into this module =#
       # instead use accessor functions:
       getstdout() = Base.STDOUT #= best option =#
       # or move the assignment into the runtime:
       __init__() = global mystdout = Base.STDOUT #= also works =#

.. 
 Several additional restrictions are placed on the operations that can be done while precompiling code
 to help the user avoid other wrong-behavior situations:

コードのプリコンパイル中に実行できる、ユーザが誤った処理による問題を回避するための操作には、
いくつかの追加の制限があります。:

.. 
 1. Calling ``eval`` to cause a side-effect in another module.
    This will also cause a warning to be emitted when the incremental precompile flag is set.

1. 1つは他のモジュールに副作用を引き起こす、 ``eval`` の呼び出しです。増分プリコンパイルフラグが設定されている場合、警告が出されます。

.. 
 2. ``global const`` statements from local scope after ``__init__()`` has been started (see issue #12010 for plans to add an error for this)

2. ``__init__()`` の後のローカルスコープからの ``global const`` ステートメントが開始されました
   （これにエラーを追加するには、イシュー#12010を参照してください）。

.. 
 3. Replacing a module (or calling ``workspace()``) is a runtime error while doing an incremental precompile.

3. モジュールの置き換え（または ``workspace()`` の呼び出し）は、増分プリコンパイルをする際は実行時エラーとなります。

.. 
 A few other points to be aware of:

その他の注意する必要がある点は、:

.. 
 1. No code reload / cache invalidation is performed after changes are made to the source files themselves,
    (including by ``Pkg.update``), and no cleanup is done after ``Pkg.rm``

1. ソースファイル自体が変更された後（ ``Pkg.update`` を含む）は、コードのリロードやキャッシュの無効化は行われず、
   また ``Pkg.rm`` 後はクリーンアップは行われません。

.. 
 2. The memory sharing behavior of a reshaped array is disregarded by precompilation (each view gets its own copy)

2. 再構成された配列のメモリ共有処理は、プリコンパイルによって無視されます（各ビューは独自のコピーを取得します）。

.. 
 3. Expecting the filesystem to be unchanged between compile-time and runtime
    e.g. ``@__FILE__``/``source_path()`` to find resources at runtime,
    or the BinDeps ``@checked_lib`` macro. Sometimes this is unavoidable.
    However, when possible, it can be good practice to copy resources
    into the module at compile-time so they won't need to be found at runtime.

3. 実行時にリソースを調べる ``@__FILE__``/``source_path()`` またはBinDeps ``@checked_lib`` マクロなど、
   コンパイル時と実行時にファイルシステムが不変であることを期待します。しばしばこれは回避ができません。
   しかし、可能であれば、コンパイル時にリソースをモジュールにコピーすることで、実行時にリソースを見つける必要がなくなります。

.. 
 4. WeakRef objects and finalizers are not currently handled properly by the serializer
    (this will be fixed in an upcoming release).

4. WeakRefオブジェクトおよびファイナライザは、現在シリアライザによって正しく処理されていません
  （これは、今後のリリースで修正される予定です）。

.. 
 5. It is usually best to avoid capturing references to instances of internal metadata objects such as
    Method, MethodInstance, MethodTable, TypeMapLevel, TypeMapEntry
    and fields of those objects, as this can confuse the serializer
    and may not lead to the outcome you desire.
    It is not necessarily an error to do this,
    but you simply need to be prepared that the system will
    try to copy some of these and to create a single unique instance of others.

5. シリアライザを混乱させる可能性があり、期待する結果が得られない可能性があるため、
   通常、Method、MethodInstance、MethodTable、TypeMapLevel、TypeMapEntry、
   およびこれらのオブジェクトのフィールドなどの内部メタデータオブジェクトの
   インスタンスへの参照をキャプチャしないようにしてください。これを行うことは必ずしも間違いではありませんが、
   システムがこれらのいくつかをコピーし、一意のインスタンスを作成しようとすることに注意してください。

.. 
 It is sometimes helpful during module development to turn off incremental precompilation.
 The command line flag ``--compilecache={yes|no}`` enables you to toggle module precompilation on and off.
 When Julia is started with ``--compilecache=no`` the serialized modules in the compile cache are ignored when loading modules and module dependencies.
 ``Base.compilecache()`` can still be called manually and it will respect ``__precompile__()`` directives for the module.
 The state of this command line flag is passed to ``Pkg.build()`` to disable automatic precompilation triggering when installing, updating, and explicitly building packages.
 
増分プリコンパイルをオフにすることは、モジュール開発中に役立つことがあります。
コマンドラインフラグ ``--compilecache={yes|no}`` により、モジュールのプリコンパイルをオンまたはオフに切り替えることができます。
``--compilecache=no`` を指定してJuliaを起動すると、モジュールとモジュールの依存関係をロードするときに、
コンパイルキャッシュ内のシリアライズされたモジュールは無視されます。 ``Base.compilecache()`` は手動で呼び出すことができ、
モジュールの ``__precompile__()`` の命令を尊重します。このコマンドラインフラグの状態は ``Pkg.build()`` に渡され、
パッケージをインストール、更新、および明示的に構築する際の自動プリコンパイルトリガを無効にします。
 
