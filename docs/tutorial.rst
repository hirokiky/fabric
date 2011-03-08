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


Task arguments
==============

It's often useful to pass runtime parameters into your tasks, just as you might
during regular Python programming. Fabric has basic support for this using a
shell-compatible notation: ``<task name>:<arg>,<kwarg>=<value>,...``. It's
contrived, but let's extend the above example to say hello to you personally::

    def hello(name="world"):
        print("Hello %s!" % name)

By default, calling ``fab hello`` will still behave as it did before; but now
we can personalize it::

    $ fab hello:name=Jeff
    Hello Jeff!

    Done.

Those already used to programming in Python might have guessed that this
invocation behaves exactly the same way::

    $ fab hello:Jeff
    Hello Jeff!

    Done.

For the time being, your argument values will always show up in Python as
strings and may require a bit of string manipulation for complex types such
as lists. Future versions may add a typecasting system to make this easier.

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

Let's start wrapping up our fabfile by putting in the keystone: a ``deploy``
task that ensures the code on our server is up to date::

    def deploy():
        code_dir = '/srv/django/myproject'
        with cd(code_dir):
            run("git pull")
            run("touch app.wsgi")

Here again, we introduce a handful of new concepts:

* Fabric is just Python -- so we can make liberal use of regular Python code
  constructs such as variables and string interpolation;
* `~fabric.context_managers.cd`, an easy way of prefixing commands with a
  ``cd /to/some/directory`` call.
* `~fabric.operations.run`, which is similar to `~fabric.operations.local` but
  runs remotely instead of locally.

We also need to make sure we import the new functions at the top of our file::

    from __future__ import with_statement
    from fabric.api import local, settings, abort, run, cd
    from fabric.contrib.console import confirm

With these changes in place, let's deploy::

    $ fab deploy
    No hosts found. Please specify (single) host string for connection: my_server
    [my_server] run: git pull
    [my_server] out: Already up-to-date.
    [my_server] out:
    [my_server] run: touch app.wsgi

    Done.

We never specified any connection info in our fabfile, so Fabric prompted us at
runtime. Connection definitions use SSH-like "host strings" (e.g.
``user@host:port``) and will use your local username as a default -- so in this
example, we just had to specify the hostname, ``my_server``.


Remote interactivity
--------------------

``git pull`` works fine if you've already got a checkout of your source code --
but what if this is the first deploy? It'd be nice to handle that case too and
do the initial ``git clone``::

    def deploy():
        code_dir = '/srv/django/myproject'
        with settings(warn_only=True):
            if run("test -d %s" % code_dir).failed:
                run("git clone user@vcshost:/path/to/repo/.git %s" % code_dir)
        with cd(code_dir):
            run("git pull")
            run("touch app.wsgi")

As with our calls to `~fabric.operations.local` above, `~fabric.operations.run`
also lets us construct clean Python-level logic based on executed shell
commands. However, the interesting part here is the ``git clone`` call: since
we're using Git's SSH method of accessing the repository on our Git server,
this means our remote `~fabric.operations.run` call will need to authenticate
itself.

Older versions of Fabric (and similar high level SSH libraries) run remote
programs in limbo, unable to be touched from the local end. This is
problematic when you have a serious need to enter passwords or otherwise
interact with the remote program.

Fabric 1.0 and later breaks down this wall and ensures you can always talk to
the other side. Let's see what happens when we run our updated ``deploy`` task
on a new server with no Git checkout::

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

Notice the ``Password:`` prompt -- that was our remote ``git`` call on our Web server, asking for the password to the Git server. We were able to type it in and the clone continued normally.

.. seealso:: :doc:`/usage/interactivity`


.. _defining-connections:

あらかじめコネクションを定義する
--------------------------------

Specifying connection info at runtime gets old real fast, so Fabric provides a
handful of ways to do it in your fabfile or on the command line. We won't cover
all of them here, but we will show you the most common one: setting the global
host list, :ref:`env.hosts <hosts>`.

:doc:`env <usage/env>` is a global dictionary-like object driving many of
Fabric's settings, and can be written to with attributes as well (in fact,
`~fabric.context_managers.settings`, seen above, is simply a wrapper for this.)
Thus, we can modify it at module level near the top of our fabfile like so::

    from __future__ import with_statement
    from fabric.api import *
    from fabric.contrib.console import confirm

    env.hosts = ['my_server']

    def test():
        do_test_stuff()

When ``fab`` loads up our fabfile, our modification of ``env`` will execute,
storing our settings change. The end result is exactly as above: our ``deploy``
task will run against the ``my_server`` server.

This is also how you can tell Fabric to run on multiple remote systems at once:
because ``env.hosts`` is a list, ``fab`` iterates over it, calling the given
task once for each connection.

.. seealso:: :doc:`usage/env`, :ref:`host-lists`


おわりに
========

Our completed fabfile is still pretty short, as such things go. Here it is in
its entirety::

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

This fabfile makes use of a large portion of Fabric's feature set:

* defining fabfile tasks and running them with :doc:`fab <usage/fab>`;
* calling local shell commands with `~fabric.operations.local`;
* modifying env vars with `~fabric.context_managers.settings`;
* handling command failures, prompting the user, and manually aborting;
* and defining host lists and `~fabric.operations.run`-ning remote commands.

However, there's still a lot more we haven't covered here! Please make sure you
follow the various "see also" links, and check out the documentation table of
contents on :ref:`the main index page <documentation-index>`.

Thanks for reading!
