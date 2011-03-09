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

* The official downloads are located in Fabric's Redmine instance at
  http://code.fabfile.org/projects/fabric/files/. This is the spot you want
  to download from for operating system packages, as the only changing part of
  the URL will be the filename itself and the md5 hashes will be consistent.
* Our `Git repository viewer <http://git.fabfile.org>`_ provides downloads of
  all tagged releases. See the "Download" column, next to the "Tag" column in
  the middle of the front page. Please note that due to how cgit generates tag
  archives, the md5 sums will change over time, so use of this location for
  package downloads is not recommended.
* `Our GitHub mirror <http://github.com/bitprophet/fabric>`_ also has downloads
  of all tagged releases -- just click the 'Download' button near the top of
  the main page.
* `Fabric's PyPI page <http://pypi.python.org/pypi/Fabric>`_ offers manual
  downloads in addition to being the entry point for ``pip`` and
  ``easy-install``.


.. _source-code-checkouts:

ソースコードのチェックアウト
============================

The Fabric developers manage the project's source code with the `Git
<http://git-scm.com>`_ DVCS. To follow Fabric's development via Git instead of
downloading official releases, you have the following options:

* Clone the canonical Git repository, ``git://fabfile.org/fabric.git`` (note
  that a Web view of this repository can be found at `git.fabfile.org
  <http://git.fabfile.org>`_)
* Clone the official Github mirror/collaboration repository,
  ``git://github.com/bitprophet/fabric.git``
* Make your own fork of the Github repository by making a Github account,
  visiting `GitHub/bitprophet/fabric <http://github.com/bitprophet/fabric>`_
  and clicking the "fork" button.

.. note::

    If you've obtained the Fabric source via source control and plan on
    updating your checkout in the future, we highly suggest using ``python
    setup.py develop`` instead -- it will use symbolic links instead of file
    copies, ensuring that imports of the library or use of the command-line
    tool will always refer to your checkout.

For information on the hows and whys of Fabric development, including which
branches may be of interest and how you can help out, please see the
:doc:`development` page.


.. _pypm:

ActivePythonとPyPM
==================

Windows users who already have ActiveState's `ActivePython
<http://www.activestate.com/activepython>`_ distribution installed may find
Fabric is best installed with its package manager, ``pypm``. Below is example
output from an installation of Fabric 0.9.0 via ``pypm``::

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
