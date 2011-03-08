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

もし :ref:`prose <usage-docs>` や :ref:`API <api_docs>` にあるドキュメントを読み漁ってみて、もし解決策がみつからなかったら、次のいくつかあるサポート先を見てください。
しかし、チケットを切ったりメーリングリストに投稿する前に、少なくともドキュメントに目は通してください！

メーリングリスト
----------------

Fabricの使っていて助けが必要な場合には `fab-user mailing list 
<http://lists.nongnu.org/mailman/listinfo/fab-user>`_ に聞くのが最適です。
（いまは ``nongnu.org`` でホスティングされています）
Fabficの開発者はできる限り速く返信します。
メーリングリストにはFabricユーザのアクティブなコミュニティやコントリビュータも参加しています。

Twitter
-------

Fabricには公式Twitterアカウントがあります。
`@pyfabric <http://twitter.com/pyfabric>`_ です。
Twitterではアナウンスとちょっとしたニュースをお届けします。
（「やあ、このFabricについての記事はかなりいい感じだよ！」みたいな感じ）

.. _bugs:

バグ／チケットトラッカー
------------------------

新しいバグを登録したり、既存のバグを検索するには、Fabricの `Redmine 
<http://redmine.org>`_ インスタンスを確認してください。
場所は `code.fabfile.org <http://code.fabfile.org>`_ です。
スパム対策のため、新しいチケットを投稿するにはアカウント登録が必要です。
（すぐにできます）

IRC
---

準公式のIRCチャンネルも管理しています。
Freenode(``irc://irc.freenode.net``)で ``#fabric`` にいけば、開発者やユーザがいると思います。
IRCでは毎度のことですが、すぐに返信出来るとは限りません。
しかし誰かしらチャンネルのログを取ってくれていて、返信出きるときに知れくれるとおもいます。

Wiki
----

Fabricの公式Wikiは `MoinMoin <http://moinmo.in>`_ にあります。
`wiki.fabfile.org <http://wiki.fabfile.org>`_ にアクセスしてみてください。
これを書いている今も、Fabricの使い方が練られています。
チケットトラッカーと同様に、スパム対策として編集フォームに簡単なcaptchaがあります。
