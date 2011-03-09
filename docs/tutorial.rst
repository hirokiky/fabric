.. -*- coding: utf-8-unix -*-

====================
概要とチュートリアル
====================

Fabricへようこそ！

このドキュメントはFabricの機能を巡る怒涛のツアーとその使い方のクイックガイドです。
追加ドキュメント（いたるところにリンクを張りました）は :ref:`使い方 <usage-docs>` にあります。-- 必ず確認してください。


Fabricって何？
===============

``README`` にあるように:

    .. include:: ../README
        :end-before: It provides


さらに特筆すべき点として、Fabricは:

* **任意のPython関数** を **コマンドライン** 経由で実行できるツールです
* サブルーチンのライブラリ(低レベルライブラリの上に構成されています)は **簡単** かつ **Pythonic** に SSH 経由でシェルコマンドを実行できるようにします

当然のことながら、ほんどのユーザーは Python の関数や **タスク** を記述、実行したり、リモートサーバとの交信を自動化するために、上記 2 つの機能を組み合わせて Fabric を使用しています。以下に見ていきましょう。


Hello, ``fab``
==============

"いつものやつ" が無いとチュートリアルになりませんよね::

    def hello():
        print("Hello world!")

これを ``fabfile.py``x という Python モジュールファイルに保存すると、この関数を fab ツール(Fabric の一部としてインストールされます)から実行することができるようになり、期待した通りの結果が得られます::

    $ fab hello
    Hello world!

    Done.

これでおしまいです。この機能によって、API をインポートしないでも (非常に)基本的なビルドツールとして Fabric を使用することができます。

.. note::

   ``fab`` ツールは、単にあなたの fabfile をインポートして、その中で定義した関数(群)を実行します。これはマジックでもなんでもありません -- 通常の Python スクリプトでできることは、fabfile でもできるということです！

.. seealso:: :ref:`execution-strategy`, :ref:`tasks-and-imports`, :doc:`usage/fab`


タスク引数
==========

ちょうど普通にPythonプログラミングをしているときみたいに、タスクに実行時パラメータを渡すと割と便利です。
Fabricにはシェル互換の記法でこれをサポートしています: ``<task name>:<arg>,<kwarg>=<value>,...``
不自然な例ではありますが、さきほどの例を拡張して、あなたにhelloと言わせてみましょう::

    def hello(name="world"):
        print("Hello %s!" % name)

デフォルトでは、 ``fab hello`` をすると拡張前と同じように動作します。
しかし今の拡張でパーソナライズできます::

    $ fab hello:name=Jeff
    Hello Jeff!

    Done.

引数呼び出しは、Pythonでのプログラミングで既に行われているように推測されます::

    $ fab hello:Jeff
    Hello Jeff!

    Done.

当面は引数はPythonの中では常に文字列として扱われ、リストのような複雑な型に対しては文字列操作などが必要になってくると思います。
今後のバージョンでは型キャストシステムを導入して、こういった操作を簡単に出来るようにしようと思っています。

.. seealso:: :ref:`task-arguments`

ローカルコマンド
================

上で見たように ``fab`` では、お馴染の ``if __name__ == "__main__"`` の数行を、ただ単に保存しているだけです。このコマンドは主に、シェルコマンドの実行やファイル転送などの関数(や **操作** )を含む Fabric の API と合わせて使用するために設計されています。

それでは仮想の Web アプリケーション用 fabfile をビルドしてみましょう。通常 fabfile は、プロジェクトのルートに配置することをお勧めします::

    .
    |-- __init__.py
    |-- app.wsgi
    |-- fabfile.py <-- our fabfile!
    |-- manage.py
    `-- my_app
        |-- __init__.py
        |-- models.py
        |-- templates
        |   `-- index.html
        |-- tests.py
        |-- urls.py
        `-- views.py

.. note::

   ここでは Django アプリケーションを使っていますが、これはただの例でしかありません -- Fabric は自身の SSH ライブラリ以外には、どんな外部のコードベースにも依存していません。

手始めに、テストを実行してアプリケーションのコピーをまとめれば、デプロイの準備が整うと思います::

    from fabric.api import local

    def prepare_deploy():
        local("./manage.py test my_app")
        local("git add -p && git commit")

この処理の出力は、だいたい次のようになると思います::

    $ fab prepare_deploy
    [localhost] run: ./manage.py test my_app
    Creating test database...
    Creating tables
    Creating indexes
    ..........................................
    ----------------------------------------------------------------------
    Ran 42 tests in 9.138s

    OK
    Destroying test database...

    [localhost] run: git add -p && git commit

    <interactive Git add / git commit edit message session>

    Done.

このコード自体はわかりやすいものでしょう: Fabric API 関数 `~fabric.operations.local` をインポートして、これを用いてローカルのシェルコマンドを実行します。他の Fabric API も同様で -- 全てただの Python コードです。

.. seealso:: :doc:`api/core/operations`, :ref:`fabfile-discovery`


自分の好きなように書く
======================

Fabric は "ただの Python" なので、自分の好きなように fabfile を書くことができます。例えば、ひとつのタスクを複数のサブタスクに分割すると便利です::

    from fabric.api import local

    def test():
        local("./manage.py test my_app")

    def commit():
        local("git add -p && git commit")

    def prepare_deploy():
        test()
        commit()

``prepare_deploy`` は以前と同じように呼び出すことができますが、今では、必要な場合には、サブタスクのひとつを実行させることもできます。

失敗
====

基本的なケースはうまく動作しましたが、テストが失敗した場合には何が起こるのでしょうか？おそらく、その時点で処理を止めて、デプロイ前に失敗した箇所を修正したいと思うでしょう。

Fabric は操作によって呼ばれたプログラムの返り値をチェックして、正常に終了しなかった場合は処理を終了します。テストのひとつがエラーだった場合に何が起こるか見てみましょう::

    $ fab prepare_deploy
    [localhost] run: ./manage.py test my_app
    Creating test database...
    Creating tables
    Creating indexes
    .............E............................
    ======================================================================
    ERROR: testSomething (my_project.my_app.tests.MainTests)
    ----------------------------------------------------------------------
    Traceback (most recent call last):
    [...]

    ----------------------------------------------------------------------
    Ran 42 tests in 9.138s

    FAILED (errors=1)
    Destroying test database...

    Fatal error: local() encountered an error (return code 2) while executing './manage.py test my_app'

    Aborting.

素晴しい！自分達は何もする必要がありませんでした: Fabric が失敗を検知して処理を中断するので ``commit`` タスクは実行されません。

.. seealso:: :ref:`Failure handling (usage documentation) <failures>`

失敗時のハンドリング
--------------------

しかし、より柔軟にして、ユーザーに選択をさせたい場合はどうするのでしょうか？ :ref:`warn_only` という設定(もしくは **環境変数** 、通常は **env var** と略される)で、処理の中断を警告表示にさせ、柔軟なエラーハンドリングができるようになります。

それでは ``test`` 関数でこの設定を有効にして、 `~fabric.operations.local` 呼び出しの結果を検査してみましょう::

    from __future__ import with_statement
    from fabric.api import local, settings, abort
    from fabric.contrib.console import confirm

    def test():
        with settings(warn_only=True):
            result = local('./manage.py test my_app', capture=True)
        if result.failed and not confirm("Tests failed. Continue anyway?"):
            abort("Aborting at user request.")

    [...]

新機能の追加にあたって、新しい事柄をいくつかご紹介します。

* Python 2.5 において ``with：`` を使うには ``__future__`` をインポートする必要があります。
* `~fabric.contrib.console.confirm` 関数を含む Fabric の `contrib.console <fabric.contrib.console>` サブモジュールは、シンプルな yes/no プロンプト処理に使用されます。
* `~fabric.context_managers.settings` コンテクストマネージャは、特定のコードブロックへの設定反映に使用されます。
* `~fabric.operations.local` のようなコマンド実行処理は、(``.failed`` や ``.return_code`` のような)実行結果情報を持ったオブジェクトを返します。
* `~fabric.utils.abort` 関数は、手動での中断処理実行に使用されます。

しかしながら、追加した機能の複雑さにもかかわらず、その処理を辿るのはまだ非常に簡単で、現在では、はるかに柔軟性に富んでいます。

.. seealso:: :doc:`api/core/context_managers`, :ref:`env-vars`


コネクションの作成
==================

fabfileに要石を置いてラップしてみましょう: ``deploy`` タスクはサーバ上のコードが最新かを確認するタスクです::

    def deploy():
        code_dir = '/srv/django/myproject'
        with cd(code_dir):
            run("git pull")
            run("touch app.wsgi")

ここで再度、いくつか新しい概念をご紹介します:

* FabricはただのPythonです -- だから普通の変数は文字列が挿入されたPythonコードを自由に使うことが出来ます
* `~fabric.context_managers.cd` で簡単にコマンドの前に ``cd /to/some/directory`` を呼び出すことが出来ます
* `~fabric.operations.run` は `~fabric.operations.local` に似ていますが、ローカルではなくリモートの操作が出来ます。 which is similar to  but

またファイルの戦闘で新しい関数をimportする必要があることに注意してください::

    from __future__ import with_statement
    from fabric.api import local, settings, abort, run, cd
    from fabric.contrib.console import confirm

これらの変更を踏まえたうえで、デプロイしてみましょう::

    $ fab deploy
    No hosts found. Please specify (single) host string for connection: my_server
    [my_server] run: git pull
    [my_server] out: Already up-to-date.
    [my_server] out:
    [my_server] run: touch app.wsgi

    Done.

fabfileに接続情報をまったく与えていないので、Fabricは実行時に確認してきます。
接続先の定義はSSHに似た"ホスト文字列"(``user@host:port``) を使います。
ローカルユーザ名をデフォルトで使います -- この例ではホスト名 ``my_server`` だけを指定する必要があります。


リモートインタラクティビティ
----------------------------

ソースコードをチェックアウト済みだった場合に ``git pull`` はうまく動作します --
しかし最初のデプロイではどうでしょうか。このケースを扱うのよさそうなので、最初の ``git clone`` をしてみましょう::

    def deploy():
        code_dir = '/srv/django/myproject'
        with settings(warn_only=True):
            if run("test -d %s" % code_dir).failed:
                run("git clone user@vcshost:/path/to/repo/.git %s" % code_dir)
        with cd(code_dir):
            run("git pull")
            run("touch app.wsgi")

上の例で `~fabric.operations.local` を使って呼んだように `~fabric.operations.run`
でも綺麗にシェルコマンドに基づいたPythonレベルでのロジックを書いてみましょう。
しかしながら、ここで面白いのは ``git clone`` の呼び出しの部分です:
ここではGitサーバにレポジトリにアクセスするのにGitのSSHメソッドを使っているので、
リモートの `~fabric.operations.run` 自体にも認証が必要になります。

Fabricの過去バージョン（と同様の高レベルSSHライブラリ）ではリモートプログラムをlimboを使って実行していました。
limboではローカル端末から触ることが出来ませんでした。
これはリモートプログラムに対してパスワードの入力等のインタラクティブな操作が重要な場合に問題があります。

Fabric 1.0以降ではこの壁を取っ払い、いつもリモートに話しかけることが出きるようになりました。
では更新した ``deploy`` タスクをGitチェックアウトをしていない新しいサーバ上で実行したらで何が起きるか見てみましょう::

    $ fab deploy
    No hosts found. Please specify (single) host string for connection: my_server
    [my_server] run: test -d /srv/django/myproject

    Warning: run() encountered an error (return code 1) while executing 'test -d /srv/django/myproject'

    [my_server] run: git clone user@vcshost:/path/to/repo/.git /srv/django/myproject
    [my_server] out: Cloning into /srv/django/myproject...
    [my_server] out: Password: <enter password>
    [my_server] out: remote: Counting objects: 6698, done.
    [my_server] out: remote: Compressing objects: 100% (2237/2237), done.
    [my_server] out: remote: Total 6698 (delta 4633), reused 6414 (delta 4412)
    [my_server] out: Receiving objects: 100% (6698/6698), 1.28 MiB, done.
    [my_server] out: Resolving deltas: 100% (4633/4633), done.
    [my_server] out:
    [my_server] run: git pull
    [my_server] out: Already up-to-date.
    [my_server] out:
    [my_server] run: touch app.wsgi

    Done.

``Password`` プロンプトに注目してください -- これはWebサーバ上でのリモートが ``git`` の呼び出しによるもので、Gitサーバへのパスワードを訊いているところです。
これでパスワードをタイプして、普通にcloneを続けられます。

.. seealso:: :doc:`/usage/interactivity`


.. _defining-connections:

あらかじめコネクションを定義する
--------------------------------

実行時に接続先を指定する方法はすぐにすたれてしまうので、Fabricではfabfileかコマンドラインにそれを指定する方法をいくつか帝京しています。
ここではすべてのやり方には降れませんが、もっとも一般的なやり方を紹介します:
グローバルホストリスト :ref:`env.hosts <hosts>` を設定します。

:doc:`env <usage/env>` はグローバルな辞書のようなオブジェクトで、Fabricの設定から値を引っ張ってくることもできるし、属性を指定して書くこともできます。
（実際上で見たように `~fabric.context_managers.settings` はこれの単純なラッパです）
これでfabfileの先頭でモジュールレベルで変更することが出来ます::

    from __future__ import with_statement
    from fabric.api import *
    from fabric.contrib.console import confirm

    env.hosts = ['my_server']

    def test():
        do_test_stuff()

``fab`` がfabfileを読み込むときに、 ``env`` の修正の部分が実行されて、設定変更が反映されます。
最終結果は上のコードのようになります: ``deploy`` タスクは ``my_server`` に対して実行します。

この方法で一度に複数のリモートシステムに接続する際にどのマシンで実行するかFabricに伝える事ができます:
``env.hosts`` はリストなので、 ``fab`` はリストに指定されているマシンに1つずつ接続してタスク実行していきます。

.. seealso:: :doc:`usage/env`, :ref:`host-lists`


おわりに
========

完成したfabfileはまだかなり短いですが、動作はします。
ここにできたプログラムの全体像を見せます::

    from __future__ import with_statement
    from fabric.api import *
    from fabric.contrib.console import confirm

    env.hosts = ['my_server']

    def test():
        with settings(warn_only=True):
            result = local('./manage.py test my_app', capture=True)
        if result.failed and not confirm("Tests failed. Continue anyway?"):
            abort("Aborting at user request.")

    def pack():
        local('tar czf /tmp/my_project.tgz .')

    def prepare_deploy():
        test()
        pack()

    def deploy():
        put('/tmp/my_project.tgz', '/tmp/')
        with cd('/srv/django/my_project/'):
            run('tar xzf /tmp/my_project.tgz')
            run('touch app.wsgi')

このfabfileはFabricの機能をふんだんに利用しています:

* fabfileタスクを定義して :doc:`fab <usage/fab>` で実行しています
* ローカルのシェルコマンドを `~fabric.operations.local` を使って呼び出します
* `~fabric.context_managers.settings` を使ってenv varsを変更します
* コマンドが失敗した時の処理をしたり、ユーザにプロンプトを出したり、手動で停止したりできます
* そしてホストのリストを定義して、リモートコマンドを `~fabric.operations.run` で実行できます

しかしながら、ここで紹介してないことがもっとたくさんあります！
いくつも挙げた"see also"のリンクをぜひ見てみてください。
そして :ref:`表紙のページ <documentation-index>` にあるドキュメントを見てください。

読んでくれてありがとうございました！
