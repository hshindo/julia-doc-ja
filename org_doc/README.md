Juliaドキュメント README
========================

<!-- Julia's documentation is written in reStructuredText, a good reference for which
is the [Documenting Python](http://docs.python.org/devguide/documenting.html)
chapter of the Python Developer's Guide. -->

JuliaのドキュメントはreStructuredTest(reST)で書かれています。reST自体の解説は[python公式](http://docs.python.org/devguide/documenting.html)が参考になります。


<!-- Prerequisites for building the documentation -->
ビルドに必要なもの
------------------

<!-- The documentation is built using [Sphinx](http://sphinx.pocoo.org/) and LaTeX.
On ubuntu, you'll need the following packages installed: -->
ドキュメントは[Sphinx](http://sphinx.pocoo.org/)とLaTeXでビルドされます。
ubuntuの場合、以下のパッケージをインストールする必要があります。

    latex-cjk-all
    texlive
    texlive-lang-cjk
    texlive-latex-extra

<!-- On OS X, you can install MacTex using the GUI installer -->
OS Xの場合はGUIからMacTexをインストールしてください。


<!-- Building the documentation -->
ドキュメントのビルド
--------------------------

<!-- Build the documentation by running -->
以下のコマンドでビルドできます。

    $ make html
    $ make latexpdf

訳注:
    ビルドにあたってセクションの重複に対する警告が出ますがこれは元の文書と同様です。
    成功しない場合は手動でSphinxのdependencyを入れるとうまくいく場合があります。(`pip install -r requirements.txt`)


<!-- File layout -->
ファイルのレイアウト
--------------------

    conf.py             Sphinxの設定ファイル
    stdlib/             julia標準ライブラリのドキュメント

Sphinx拡張とテーマ
------------------
<!-- The extensions to Sphinx and the theme are in the
https://github.com/JuliaLang/JuliaDoc repository, and can also be used to style
package documentation. -->
Sphinx拡張とテーマは以下のリポジトリにあります。
https://github.com/JuliaLang/JuliaDoc
パッケージのドキュメントに対して使用することも可能です。
