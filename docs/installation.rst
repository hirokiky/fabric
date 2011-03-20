.. -*- coding: utf-8-unix -*-

==============
 インストール
==============

Fabricのインストールは pip_ （強く推奨） または `easy_install`_ （古いが安定している）で行うのが最善です。
あるいはOSのパッケージマネージャを使ってインストールするのもよいでしょう。
（パッケージ名は ``fabric`` または ``python-fabric`` となっているのが通例です）
また ``python setup.py install`` を :ref:`ダウンロードした <downloads>` り、 :ref:`クローン <source-code-checkouts>` したソースコード内で実行するのもよいでしょう。

.. _pip: http://pip.openplans.org
.. _`easy_install`: http://wiki.python.org/moin/CheeseShopTutorial

依存環境
========

Fabricのインストールをするには、主に4つのソフトウェアが必要になります:

* プログラミング言語 Python
* ``setuptools`` パッケージ／インストールライブラリ
* PyCrypto 暗号化ライブラリ
* Paramiko SSH2ライブラリ

各要件に関する詳細に関しては読み進めてください。

Python
------

Fabricには Python_ のバージョン2.5か2.6が必要です。
他のバージョンのPythonに関していくつか注意があります:

* **Python 2.4** のサポートは想定していません。
  理由は2.4が古いというのと、コンテキストマネージャや新しいモジュールなど多くの便利ツールがPython 2.5で書かれているからです。
  そうは言っても、実際にPython 2.5特有の機能がひどく多いというわけではありません。
  サードパーティのPython 2.4互換のforkにリンクをしています。 -- サポートは保証しませんが
* Fabricは **Python 3.x** 環境ではまだテストされていません。
  したがって、開発中のものとは互換性がありません。
  しかしながら少なくとも今後対応しようとはしていて（たとえば ``print`` の代わりに ``print()`` を使う）、依存ライブラリが対応すれば必ず3.xにポーティングします。

.. _Python: http://python.org

setuptools
----------

`setuptools`_ はPythonのインストール時に着いてきます。
もしなければ持ってこなければいけません。そのような場合には ``python-setuptools``, ``py25-setuptools`` みたいな名前のパッケージを持ってくることになります。
Fabricは将来的にはsetuptoolsや `distribute`_ プロジェクトへの依存を辞めるでしょうが、いまのところはsetuptoolsはインストールに必要です。

.. _setuptools: http://pypi.python.org/pypi/setuptools
.. _distribute: http://pypi.python.org/pypi/distribute

PyCrypto
--------

PyCrypto_ はParamikoが依存しているもので、ParamikoはSSHを実行するために低レベル（Cベースの）暗号化アルゴリズムを提供しています。
PyCryptoをインストールに際して関連事項がいくつかあります。Pythonパッケージツールとの互換性やC拡張であること自体がそれです。

.. _PyCrypto: http://www.amk.ca/python/code/crypto.html

.. _pycrypto-and-pip:

パッケージツール
~~~~~~~~~~~~~~~~

Fabricのインストールには ``pip`` を使うことを強く推奨します。
その理由としては新しいからということと、 ``easy_install`` よりも一般的に良いからということからです。
しかしながら、Pythonの特定のバージョンでは複数のバグがあり、 ``pip`` と PyCryptoがPyCrypto自身のインストールを妨げる可能性があります。特に以下の場合は注意してください:

* Python = 2.5.x
* PyCrypto >= 2.1
* ``pip`` < 0.8.1

これらが合致する場合に、 ``pip install Fabric`` や ``pip install PyCrypto`` をしようとすると ``No such file or directory`` というIOErrorに遭遇します。

これを直すには、単純に上の条件に一つでも当てはまらないようにすればよいだけです。
次の方法を試してください（優先順位が高い順に）:

* ``pip`` 0.8.1以上にアップグレードする。（ ``pip install -U pip`` を実行するなど）
* ``pip install PyCrypto==2.0.1`` として明示的にPyCrypto 2.0.1をインストールする。
  （このバージョンがFabricが問題なく動作して、インストールで問題が起きない最新バージョン）
* Python 2.6以上にアップグレードする

C拡張
~~~~~

DebianのaptレポジトリやRedHatのRPMなどコンパイル済みソースからインストールしている、あるいは :ref:`pypm <pypm>` を使っているのでなければ、PyCryptoをインストールするのにPythonのC拡張モジュールをソースからビルド出来る力も必要です。
UbuntuやMac OS Xのような **Unixベースプラットフォーム** のユーザは、よく ``python-dev`` あるいはその類のPython開発ライブラリと、典型的なCビルドツールチェーンが必要です。（たとえばMacでは開発者ツール／XCodeツール、UbuntuやDebian Linuxでは ``build-essential`` パッケージ -- 基本的には ``gcc``, ``make`` などのすべて）

**Windows** ユーザは :ref:`pypm` を使って、 Cygwin_ のようなC開発環境をインストールする、あるいは `voidspaceのPythonモジュールページ <http://www.voidspace.org.uk/python/modules.shtml#pycrypto>`_ からコンパイル済みWin32 PyCryptoパッケージを取得することをおすすめします。

.. _Cygwin: http://cygwin.com

.. note::

   Pythonが64bit環境なWindowsユーザでは、PyCryptoが依存している ``winrandom`` が適切にインストールされず、ImportErrorになることかもしれません。
   この場合、MS Visual Studioなどを使って ``winrandom`` を自分自身でコンパイル必要が出てきます。
   :issue:`194` にその情報があるので確認してください。

開発依存環境
------------

Fabricの開発に興味がある人（あるいはテストをするだけでも）は下記のパッケージもインストールする筆王があります:

* 下記の依存パッケージを素得するために git_ が Mercurial_ が必要です
* Nose_
* Coverage_
* PyLint_
* Fudge_
* Sphinx_

.. _git: http://git-scm.com
.. _Nose: http://code.google.com/p/python-nose/
.. _Coverage: http://nedbatchelder.com/code/modules/coverage.html
.. _PyLint: http://www.logilab.org/857
.. _Fudge: http://farmdev.com/projects/fudge/index.html
.. _Sphinx: http://sphinx.pocoo.org/

バージョン番号を含めて、最新のテスト／開発要件のリストを知りたい場合は、ソースにある ``requirements.txt`` ファイルを参照してください。
このファイルは ``pip`` で ``pip install -r requirements.txt`` として使うためにあるものです。

.. _Mercurial: http://mercurial.selenic.com/wiki/


.. _downloads:

ダウンロード
============

Fabricのソースコードをtar.gzやzipアーカイブで取得するには、次のサイトをどれかを確認してみてください:

* 公式ダウンロードはFabricのRedmineインスタンスを確認して下さい。
  ( http://code.fabfile.org/projects/fabric/files/ )
  ここからOS毎のパッケージをダウンロードできます。
  上記URLで変更がある場所はファイル名だけで、md5ハッシュ値は変更ありません。
* `Gitレポジトリビューワ <http://git.fabfile.org>`_ ではタグが打たれたリリースがダウンロード出来ます。
  "Download"カラムをクリックして、最初のページの真ん中にある"Tag"カラムをクリックしてください。
  cgitのタグアーカイブの生成方法によって、md5値がいつも変化するので、ここからダウンロードすることは推奨されていないことに注意してください。
* `GitHubのミラー <http://github.com/bitprophet/fabric>`_ でもタグが打たれたリリースをダウンロード出来ます。
  -- 最初のページの上の方にある'Download'ボタンをクリックするだけです。
* `FabricのPyPIページ <http://pypi.python.org/pypi/Fabric>`_ では ``pip`` や ``easy_install`` のエントリーポイントに加えてマニュアルのダウンロードも出来るようになっています。
  
.. _source-code-checkouts:

ソースコードのチェックアウト
============================

Fabric開発者はプロジェクトのソースコードを分散バージョン管理システムの `Git <http://git-scm.com>`_ で管理することが出来ます。
公式リリースをダウンロードするのではなくFabricの開発状況をGitで追いかける場合、次の選択肢があります。:

* 正式なGitリポジトリ ``git://fabfile.org/fabric.git`` をクローンする
  （このレポジトリは `git.fabfile.org <http://git.fabfile.org>`_ でWeb上から確認できます）
* 公式のGithubミラー／コラボレーションレポジトリをクローンする
  ``git://github.com/bitprophet/fabric.git``
* Githubアカウントを作って、Githubレポジトリで自分用にforkする
  `GitHub/bitprophet/fabric <http://github.com/bitprophet/fabric>`_

.. note::

   Fabricのソースをバージョン管理ツールから取得して、それを更新しようと考えているなら、代わりに ``python setup.py develop`` を使うことを強く推奨します。
   -- こうするとファイルをコピーするのではなくシンボリックリンクを使うので、ライブラリをimportしたりコマンドラインツールで呼び出す際に、常に自分のチェックアウトを参照するようになります。

Fabricの開発について、どのブランチが面白そうかとか、どう貢献できるかも含め、その方法や理由を知りたい場合は :doc:`development` を参照してください。

.. _pypm:

ActivePythonとPyPM
==================

すでにActiveStateの `ActivePython <http://www.activestate.com/activepython>`_ を使っているWindowsユーザは、パッケージマネージャとして ``pypm`` を使うのが最適でしょう。
以下は ``pypm`` 経由でFabric 0.9.0をインストールしたときの出力例です::

    C:\> pypm install fabric
    Ready to perform these actions:
    The following packages will be installed:
    fabric-0.9.0 pycrypto-2.0.1
    Get: [pypm.activestate.com] fabric 0.9.0-1
    Get: [pypm.activestate.com] pycrypto 2.0.1-1
    Installing fabric-0.9.0
    Fixing script
    C:\Users\<username>\AppData\Roaming\Python\Scripts\fab-script.py
    Installing pycrypto-2.0.1
