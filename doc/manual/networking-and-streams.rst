.. _man-networking-and-streams:

.. currentmodule:: Base

.. 
 ************************
  Networking and Streams
 ************************

************************
 ネットワークとストリーム
************************

.. 
 Julia provides a rich interface to deal with streaming I/O objects such as
 terminals, pipes and TCP sockets. This interface, though asynchronous at the
 system level, is presented in a synchronous manner to the programmer and it is
 usually unnecessary to think about the underlying asynchronous operation. This
 is achieved by making heavy use of Julia cooperative threading (:ref:`coroutine <man-tasks>`)
 functionality.

Juliaは、端末、パイプ、TCPソケットなどのストリーミングI/Oオブジェクトを処理する豊富なインターフェイスを提供します。
このインタフェースは、システムレベルでは非同期ですが、プログラマにとっては同期的に提示され、
通常は基本となる非同期操作について考える必要はありません。これは、Juliaのスレッド機能（ :ref:`コルーチン <man-タスク>` ）を
多く使用することによって実現されます。

.. 
 Basic Stream I/O
 ----------------

基本的なストリームI/O
------------------

.. 
 All Julia streams expose at least a :func:`read` and a :func:`write` method,
 taking the stream as their first argument, e.g.::

全てのJuliaのストリームは少なくとも、ストリームを第一引数とする :func:`read` と :func:`write` メソッドにさらされています。::

    julia> write(STDOUT,"Hello World");  # suppress return value 11 with ;
    Hello World
    julia> read(STDIN,Char)

    '\n'

.. 
 Note that :func:`write` returns 11, the number of bytes (in ``"Hello World"``) written to :const:`STDOUT`,
 but this return value is suppressed with the ``;``.

:func:`write` は、 :const:`STDOUT` に書き込まれている（ ``"Hello World"`` の）バイト数である
11を返しますが、この戻り値は ``;`` で抑止されています。

.. 
 Here Enter was pressed again so that Julia would read the newline. Now, as you can see from this example,
 :func:`write` takes the data to write as its second argument, while :func:`read` takes the type of the
 data to be read as the second argument.

ここではEnterが再度押され、Juliaが新しい行を読むようになります。この例からわかるように、 :func:`write` は2番目の引数として
書き込むデータをとり、一方で :func:`read` は2番目の引数として読み込むデータの型をとります。

.. 
 For example, to read a simple byte array, we could do::
 
例えば、単純なバイト配列を読み取るには、次のようにします。::

    julia> x = zeros(UInt8,4)
    4-element Array{UInt8,1}:
     0x00
     0x00
     0x00
     0x00

    julia> read!(STDIN,x)
    abcd
    4-element Array{UInt8,1}:
     0x61
     0x62
     0x63
     0x64

.. 
 However, since this is slightly cumbersome, there are several convenience methods provided. For example, we could have written the
 above as::

しかし、これはやや面倒なので、いくつかの便利なメソッドがあります。例えば、上記を以下のように記述することができます。::

    julia> read(STDIN,4)
    abcd
    4-element Array{UInt8,1}:
     0x61
     0x62
     0x63
     0x64

.. 
 or if we had wanted to read the entire line instead::

もしくは、代わりに行全体を読み込むことができます。::
  
  julia> readline(STDIN)
    abcd
    "abcd\n"

.. 
 Note that depending on your terminal settings, your TTY may be line buffered and might thus require an additional enter before the data
 is sent to Julia.

端末の設定によっては、TTYがラインバッファリングされるため、データがJuliaに送信される前に追加の入力が必要になる場合があります。

.. 
 To read every line from :const:`STDIN` you can use :func:`eachline`::

:const:`STDIN`からの全ての行を読むには、 :func:`eachline` を使用します。::

    for line in eachline(STDIN)
        print("Found $line")
    end

.. 
or :func:`read` if you wanted to read by character instead::

もしくは、文字で読みたい場合は :func:`read` を使用してください。::

    while !eof(STDIN)
        x = read(STDIN, Char)
        println("Found: $x")
    end

.. 
 Text I/O
 --------

テキストI/O
----------

.. 
 Note that the :func:`write` method mentioned above operates on binary streams.
 In particular, values do not get converted to any canonical text
 representation but are written out as is::

上記の :func:`write` メソッドは、バイナリストリームで動作することに注意してください。
特に、値は正規のテキスト表現に変換されず、そのまま出力されます。::

    julia> write(STDOUT,0x61);  # suppress return value 1 with ;
    a

.. 
 Note that ``a`` is written to :const:`STDOUT` by the :func:`write` function and
 that the returned value is ``1`` (since ``0x61`` is one byte).

``a`` は :func:`write` f関数によって :const:`STDOUT` に書き込まれ、
戻り値は（ ``0x61`` は1バイトのため） ``1`` であることに注意してください。

.. 
 For text I/O, use the :func:`print` or :func:`show` methods, depending on your needs (see the standard library reference for a detailed discussion of
 the difference between the two)::

テキストI/Oの場合は、必要に応じて :func:`print` または :func:`show` メソッドを使用してください（2つの違いの詳細については、
標準ライブラリのリファレンスを参照してください）。::

    julia> print(STDOUT,0x61)
    97

.. 
 IO Output Contextual Properties
 -------------------------------

IO出力コンテキストのプロパティ
-------------------------------

.. 
 Sometimes IO output can benefit from the ability to pass contextual information into show methods. The ``IOContext`` object provides this framework for associating arbitrary metadata with an IO object. For example, ``showcompact`` adds a hinting parameter to the IO object that the invoked show method should print a shorter output (if applicable).

IO出力は、コンテキスト情報をshowメソッドに渡す機能により、便利になることがあります。 ``IOContext`` オブジェクトは、
任意のメタデータをIOオブジェクトに関連付けるためのフレームワークを提供します。
例えば、 ``showcompact`` は、呼び出されたshowメソッドは（可能であれば）短い出力を出力すべきであるという
ヒントパラメータをIOオブジェクトに追加します。

.. 
 Working with Files
 ------------------

ファイルの操作
------------------

.. 
 Like many other environments, Julia has an :func:`open` function, which takes a filename and returns an :class:`IOStream` object
 that you can use to read and write things from the file. For example if we have a file, ``hello.txt``, whose contents
 are ``Hello, World!``::

他の多くの環境と同様に、Juliaは、ファイル名をとり、ファイルからの読み書きに使用できる :class:`IOStream` オブジェクトを返す、
:func:`open` 関数を持っています。例えば、ファイル内容が  ``Hello, World!`` である ``hello.txt`` ファイル　があるとします。::

    julia> f = open("hello.txt")
    IOStream(<file hello.txt>)

    julia> readlines(f)
    1-element Array{String,1}:
     "Hello, World!\n"

.. 
 If you want to write to a file, you can open it with the write (``"w"``) flag::
 
ファイルに書き込みたい場合は、書き込みフラグ（ ``"w"`` ）をつけて開くことができます。::

    julia> f = open("hello.txt","w")
    IOStream(<file hello.txt>)

    julia> write(f,"Hello again.")
    12

.. 
 If you examine the contents of ``hello.txt`` at this point, you will notice that it is empty; nothing has actually
 been written to disk yet. This is because the :class:`IOStream` must be closed before the write is actually flushed to disk::

この時点で ``hello.txt`` の内容を調べると、空であることがわかります。実際にはディスクにはまだ何も書き込まれていません。
これは、書き込みが実際にディスクに実行される前に :class:`IOStream` が閉じられている必要があるからです。::

    julia> close(f)

.. 
 Examining ``hello.txt`` again will show its contents have been changed.

``hello.txt`` をもう一度調べると、その内容が変更されたことがわかります。

.. 
 Opening a file, doing something to its contents, and closing it again is a very common pattern.
 To make this easier, there exists another invocation of :func:`open` which takes a function
 as its first argument and filename as its second, opens the file, calls the function with the file as
 an argument, and then closes it again. For example, given a function::

ファイルを開き、内容を変更し、もう一度閉じることは、非常に一般的なパターンです。これを簡単にするため、
:func:`open` の別の呼び出し方法があります。これは、関数を第一引数、ファイル名を第二引数としてとり、
ファイルを開いてファイルを引数として関数を呼び出し、ファイルを閉じます。例えば、::

    function read_and_capitalize(f::IOStream)
        return uppercase(readstring(f))
    end

.. 
 You can call::
以下を呼び出すことができます。::

    julia> open(read_and_capitalize, "hello.txt")
    "HELLO AGAIN."

.. 
 to open ``hello.txt``, call ``read_and_capitalize on it``, close ``hello.txt`` and return the capitalized contents.

``hello.txt`` を開き、  ``read_and_capitalize on it`` を呼び出し、 ``hello.txt`` を閉じて大文字の内容を返します。

.. 
 To avoid even having to define a named function, you can use the ``do`` syntax, which creates an anonymous
 function on the fly::

名前付き関数を定義しなくても済むようにするには、 ``do`` 構文を使用して、無名関数を作成します。::

    julia> open("hello.txt") do f
              uppercase(readstring(f))
           end
    "HELLO AGAIN."

.. 
 A simple TCP example
 --------------------

単純なTCPの例
--------------------

.. 
 Let's jump right in with a simple example involving TCP sockets. Let's first create a simple server::

TCPソケットを使った簡単な例を見てみましょう。まず最初に単純なサーバを作成しましょう。::

    julia> @async begin
             server = listen(2000)
             while true
               sock = accept(server)
               println("Hello World\n")
             end
           end
    Task (runnable) @0x00007fd31dc11ae0

    julia>

.. 
 To those familiar with the Unix socket API, the method names will feel familiar,
 though their usage is somewhat simpler than the raw Unix socket API. The first
 call to :func:`listen` will create a server waiting for incoming connections on the
 specified port (2000) in this case. The same function may also be used to
 create various other kinds of servers::

UnixソケットAPIに精通している方には、メソッド名には見覚えがあると思いますが、
その使用法はUnixソケットAPIよりもいくらか簡単です。 :func:`listen` の最初の呼び出しは、
この場合、指定されたポート（2000）で受信接続を待つサーバを作成します。
同じ機能を使用して、他のさまざまな種類のサーバを作成することもできます。::

    julia> listen(2000) # Listens on localhost:2000 (IPv4)
    TCPServer(active)

    julia> listen(ip"127.0.0.1",2000) # Equivalent to the first
    TCPServer(active)

    julia> listen(ip"::1",2000) # Listens on localhost:2000 (IPv6)
    TCPServer(active)

    julia> listen(IPv4(0),2001) # Listens on port 2001 on all IPv4 interfaces
    TCPServer(active)

    julia> listen(IPv6(0),2001) # Listens on port 2001 on all IPv6 interfaces
    TCPServer(active)

    julia> listen("testsocket") # Listens on a UNIX domain socket/named pipe
    PipeServer(active)

.. 
 Note that the return type of the last invocation is different. This is because
 this server does not listen on TCP, but rather on a named pipe (Windows)
 or UNIX domain socket. The difference is subtle and has to do with the
 :func:`accept` and :func:`connect` methods. The :func:`accept`
 method retrieves a connection to the client that is connecting on the server we
 just created, while the :func:`connect` function connects to a server using the
 specified method. The :func:`connect` function takes the same arguments as
 :func:`listen`, so, assuming the environment (i.e. host, cwd, etc.) is the same you
 should be able to pass the same arguments to :func:`connect` as you did to listen to
 establish the connection. So let's try that out (after having created the server above)::

最後の呼び出しの戻り値の型が異なることに注意してください。これは、このサーバーがTCPではなく、名
前付きパイプ（Windows）またはUNIXドメインソケットで受信するためです。この違いは微妙で、
:func:`accept` と :func:`connect` メソッドに関係しています。
:func:`accept` メソッドは、サーバ上で接続しているクライアントへの接続を取得し、
:func:`connect` 関数は指定されたメソッドを使用してサーバに接続します。
:func:`connect` 関数は、 :func:`listen` と同じ引数をとるため、
（ホスト、cwdなどの）環境が同じであると仮定すると、受信で接続が確立できたように、
:func:`connect` に引数を渡すことができるはずです。
（上記のサーバーを作成した後に）それを試してみましょう。::

    julia> connect(2000)
    TCPSocket(open, 0 bytes waiting)

    julia> Hello World

.. 
 As expected we saw "Hello World" printed. So, let's actually analyze what happened behind the scenes. When we called :func:`connect`, we connect to the server we had just created. Meanwhile, the accept function returns a server-side connection to the newly created socket and prints "Hello World" to indicate that the connection was successful.

予想どおり、「Hello World」が出力されました。では実際に何が起こったのかを分析してみましょう。 :func:`connect` を呼び出すと、
作成したサーバに接続します。一方、accept関数は新しく作成されたソケットにサーバー側接続を返し、接続が成功したことを示すために 
「Hello World」を出力します。

.. 
  A great strength of Julia is that since the API is exposed synchronously even though the I/O is actually happening asynchronously, we didn't have to worry callbacks or even making sure that the server gets to run. When we called :func:`connect` the current task waited for the connection to be established and only continued executing after that was done. In this pause, the server task resumed execution (because a connection request was now available), accepted the connection, printed the message and waited for the next client. Reading and writing works in the same way. To see this, consider the following simple echo server::
  
Juliaの大きな強みは、I/O は実際に非同期的に起こっていても、APIは同期的に公開されるため、
コールバックを心配する必要がなく、サーバーが動作することを確認する必要もないという点です。
:func:`connect` を呼び出すと、現在のタスクは接続が確立されるのを待ち、接続の確立後にのみ実行を継続します。
この一時停止では、サーバタスクは（接続要求が利用可能になったため）実行を再開し、接続を受け入れ、
メッセージを出力し、次のクライアントを待機します。読み書きも同じように動作します。
これを確認するため、次の単純なエコーサーバを見てみましょう。::

    julia> @async begin
             server = listen(2001)
             while true
               sock = accept(server)
               @async while isopen(sock)
                 write(sock,readline(sock))
               end
             end
           end
    Task (runnable) @0x00007fd31dc12e60

    julia> clientside=connect(2001)
    TCPSocket(open, 0 bytes waiting)

    julia> @async while true
              write(STDOUT,readline(clientside))
           end
    Task (runnable) @0x00007fd31dc11870

    julia> println(clientside,"Hello World from the Echo Server")
    Hello World from the Echo Server


.. 
  As with other streams, use :func:`close` to disconnect the socket::
  
他のストリームと同様に、 :func:`close` を使用してソケットを切断してください。::

    julia> close(clientside)

..
  Resolving IP Addresses
  ----------------------

IPアドレスの解決
----------------------

.. 
  One of the :func:`connect` methods that does not follow the :func:`listen` methods is ``connect(host::String,port)``, which will attempt to connect to the host
  given by the ``host`` parameter on the port given by the port parameter. It
  allows you to do things like::

:func:`listen` メソッドとはことなる動きをする :func:`connect` メソッドの1つは ``connect(host::String,port)`` であり、
これはポートパラメータに与えられたポート上の、ホストパラメータに与えられたホストに対して接続を行います。
これにより、以下のようなことが可能になります。::

    julia> connect("google.com",80)
    TCPSocket(open, 0 bytes waiting)

.. 
  At the base of this functionality is :func:`getaddrinfo`, which will do the appropriate address resolution::

この機能の基盤は :func:`getaddrinfo` であり、これは適切なアドレス解決を行います。::

    julia> getaddrinfo("google.com")
    ip"74.125.226.225"

