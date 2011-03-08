.. -*- coding: utf-8-unix -*-

======
Fabric
======

About
=====

.. include:: ../README


インストール
============

Fabricの安定版は ``pip`` や ``easy_install`` 経由でインストールするのが最善です。
あるいはTGZやZIPのソースアーカイブを公式サイトからダウンロードすることもできます。
詳細なやり方やリンク先は :ref:`installation` にあります。

Fabricは最新安定版使うことをお勧めします。
リリースに関してはしばしば開発版と最新安定版の間の大きな機能差を埋めるために行われます。

しかしながら、もし最新のものが使いたいということであれば、
ソースコードをGitレポジトリからpullしたり、Github上でforkしたりできます。
:doc:`installation` にソースコードへのアクセス方法が書いてあります。


開発
====

Fabricの改善に興味があるハッカー（またはFabricの使い方やリリース方法に興味があるユーザ）は、 :doc:`development` を見てください。
寄与、レポジトリレイアウト、リリース戦略などについて全体的な情報が書いてあります。

.. _documentation-index:

ドキュメント
============

現在ドキュメントはすべてPython 2.5のユーザを念頭に入れています。
しかし来るPython 3.xとの互換性にも目を向けています。
このことからPython 2.4は対象外とし、すでにPython 2.6/2.7にアップグレードしたユーザ向けに次のようなパターンを示しています:

* ``from __future__ import with_statement``: "future import"はPython 2.5で ``with`` 構文を使うのに必要です -- この機能は頻繁に使うことになるでしょう。
  Python 2.6+のユーザはこの宣言を行う必要はありません。
* ``<true_value> if <expression> else <false_value>``: Pythonでの比較的新しい三項命令で、Python 2.5以上で使えます。
  Python 2.4以前では ``<expression> and <true_value> or <false_value>`` としてごまかしていました。
  （これは先に挙げたものと同じものというわけではなく、論理的に穴があります）
* ``print(<expression>)`` を ``print <expression>`` の代わりに使う: 使える時には ``print`` 構文のオプションである括弧を使います。
  これはPython 3.xとの互換性を高めるためです。（Python 3.xでは ``print`` は関数になります）

.. toctree::
    :hidden:

    tutorial
    installation
    development
    faq

チュートリアル
--------------

新しいユーザで／またはFabricの基本機能の概要を知りたい人は :doc:`tutorial` を参照してください。
それ以外のドキュメントは、少なくとも中身を一時的にでも欲知っているユーザを想定しています。

.. _usage-docs:

使い方
------

次のリストにはFabricの使い方（APIではない）を散りばめた文章（prose）を載せてあります。
これは :doc:`tutorial` で大まかに説明された概念を拡張して、より発展的な内容もカバーしています。

.. toctree::
    :maxdepth: 1
    :glob:

    usage/*

.. _faq:

FAQ
---

FAQに関しては、回答／解決策／弁明含め :doc:`faq` に記載してあります。

.. _api_docs:

APIドキュメント
---------------

Fabricでは2セットのAPIドキュメントを作っています。
ソースコードのdocstringから自動生成されたものです。（基本徹底的に書いています）

.. _core-api:

Core API
~~~~~~~~

**Core** APIは関数、クラス、メソッドとして緩く定義されています。
これらがFabricの基本構成ブロックを形成していて（ `~fabric.operations.run` や `~fabric.operations.sudo` など）、その上で他のもの（"contrib"セクションやユーザfabfilesなど）が構成されます。

.. toctree::
    :maxdepth: 1
    :glob:

    api/core/*

.. _contrib-api:

Contrib API
~~~~~~~~~~~

Fabricの **contrib** パッケージには、ユーザI/Oやリモートファイルの修正といったタスク向けに、一般的に役立つツール（よくユーザfabfileにマージされる）を含まれています。
Core APIは小さく、比較的変更が少ないままでありつづけようとしますが、このContribセクションはユースケースが解決され追加される度に（後方互換性は保つようにしますが）どんどん成長し発展していきます。

.. toctree::
    :maxdepth: 1
    :glob:

    api/contrib/*

過去バージョンからの変更点
--------------------------

.. toctree::
    :maxdepth: 1
    :glob:

    changes/*

ヘルプ
======

If you've scoured the :ref:`prose <usage-docs>` and :ref:`API <api_docs>`
documentation and still can't find an answer to your question, below are
various support resources that should help. We do request that you do at least
skim the documentation before posting tickets or mailing list questions,
however!

Mailing list
------------

The best way to get help with using Fabric is via the `fab-user mailing list
<http://lists.nongnu.org/mailman/listinfo/fab-user>`_ (currently hosted at
``nongnu.org``.) The Fabric developers do their best to reply promptly, and the
list contains an active community of other Fabric users and contributors as
well.

Twitter
-------

Fabric has an official Twitter account, `@pyfabric
<http://twitter.com/pyfabric>`_, which is used for announcements and occasional
related news tidbits (e.g. "Hey, check out this neat article on Fabric!").

.. _bugs:

Bugs/ticket tracker
-------------------

To file new bugs or search existing ones, you may visit Fabric's `Redmine
<http://redmine.org>`_ instance, located at `code.fabfile.org
<http://code.fabfile.org>`_. Due to issues with spam, you'll need to (quickly
and painlessly) register an account in order to post new tickets.

IRC
---

We maintain a semi-official IRC channel at ``#fabric`` on Freenode
(``irc://irc.freenode.net``) where the developers and other users may be found.
As always with IRC, we can't promise immediate responses, but some folks keep
logs of the channel and will try to get back to you when they can.

Wiki
----

There is an official Fabric `MoinMoin <http://moinmo.in>`_ wiki reachable at
`wiki.fabfile.org <http://wiki.fabfile.org>`_, although as of this writing its
usage patterns are still being worked out. Like the ticket tracker, spam has
forced us to put anti-spam measures up: the wiki has a simple, easy captcha in
place on the edit form.
