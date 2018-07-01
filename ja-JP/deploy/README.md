# Deploy!

> **補足** このチャプターはちょっと難しいことが沢山書かれています。 頑張って最後までやりきってください。デプロイはウェブサイトを開発するプロセスの上で、とても重要な部分ですが、躓きやすいポイントも多く含まれています。 チュートリアルの途中にこのチャプターを入れています。そういった躓きやすい箇所はメンターに質問して、あなたが作っているウェブサイトをオンラインでみれるようにしてください。 言い換えれば、もし時間切れでワークショップ内でチュートリアルを終わらせることができなかったとしても、この後のチュートリアルはきっと自分で終わらせることができるでしょう。

今のところ、あなたが作ったサイトは、あなたのコンピューターでしかみることができません。 ここでは、デプロイの方法を学びましょう！ デプロイとは、あなたが作っているアプリケーションをインターネットで公開することです。あなた以外の人もウェブサイトを見ることができるようになりますよ. :)

これまでに学んだとおり、ウェブサイトはサーバーに置かれています。 インターネットで利用できる多くのサーバー プロバイダーがありますが、私達は [PythonAnywhere](https://www.pythonanywhere.com/) を使用します。 PythonAnywhere は、多くの人がアクセスするものではない小さいアプリケーションを無料で公開できますので今のあなたには最適でしょう。

使用するその他の外部サービスは [GitHub](https://www.github.com) コードのホスティング サービスです。 他にも色々ありますが、ほとんどのプログラマはGitHubのアカウントを持っています。そしてあなたも今そうなります。

これらの 3 つの場所はあなたに重要です。 ローカル コンピューターは、開発およびテストを行う場所になります。 変更に満足したら、GitHub 上で、プログラムのコピーを配置します。 あなたのウェブサイトは、PythonAnywhere になります、GitHub からコードの新しいコピーを取得することによって更新されます。

# Git

> **特徴：**もし、既にgitのインストールをやっている場合は再度行う必要はありません。-あなたは次のセクションにスキップしてあなたのGitリポジトリの作成を開始します。

{% include "deploy/install_git.md" %}

## Starting our Git repository

Gitはリポジトリ（または略して「レポ」）と呼ばれる特定のファイルに変更をセットし追跡します。 私たちのプロジェクトを開始しましょう。 あなたのコンソールを開き、`djangogirls` ディレクトリでこれらのコマンドを実行します。

> **備考：**リポジトリを初期化する前に `pwd` (OSX/Linux) または `cd` (Windows) コマンドで現在の作業ディレクトリを確認してください。 `Djangogirls` フォルダーにする必要があります。

{% filename %}command-line{% endfilename %}

    $ git init
    Initialized empty Git repository in ~/djangogirls/.git/
    $ git config --global user.name "Your Name"
    $ git config --global user.email you@example.com
    

gitリポジトリを初期化することは、プロジェクトごとに1回だけ行う必要があります（ユーザー名と電子メールをもう一度入力する必要はありません）。

Git のすべてのファイルおよびこのディレクトリ内のフォルダーの変更を追跡しますが、無視するいくつかのファイルがあります。 ベース ディレクトリの `.gitignore` という名前のファイルを作成することによってこれを行います。 あなたのエディターを開き、次の内容で新しいファイルを作成します。

{% filename %}.gitignore{% endfilename %}

    *.pyc
    *~
    __pycache__
    myvenv
    db.sqlite3
    /static
    .DS_Store
    

`.Gitignore` トップレベルの"djangogirls"フォルダーとして保存します。

> **備考：**ファイル名の先頭のドットは重要です! もしそのファイルを作るのが難しいなら、（Macをお使いの型はFinderからドット（ . ）で始まるファイルが作られていません。）そういう時はエディタでSave Asから作成すれば問題ありません。
> 
> `.gitignore </ code>ファイルで指定したファイルの1つが<code> db.sqlite3 </ code>です。 そのファイルはローカルデータベースで、すべての投稿が保存されます。 PythonAnywhere上のあなたのウェブサイトは別のデータベースを使用しているので、これをあなたのリポジトリには追加したくありません。  データベースは開発マシンのようにSQLiteにすることができますが、通常はSQLiteよりも多くのサイト訪問者に対処できるMySQLと呼ばれるものを使用します。 どちらの方法でも、SQLiteデータベースをGitHubのコピーとして無視することで、これまでに作成したすべての投稿はそのまま残し、ローカルでのみ利用できるようになりますが、プロダクションに再び追加する必要があります。 あなたは、あなたのブログの実際の投稿をあなたのブログから削除することを恐れることなく、さまざまなことをテストできる良い遊び場としてローカルデータベースを考えるべきです。</p>
</blockquote>

<p><code> git add </ code>をする前に、何が変更されたか確認するために、<code> git status </ code>コマンドを使用する事をおすすめします。 これは間違ったファイルを追加またはコミットなど思いもかけない事を止めるために役立ちます。 <code>Git status` コマンドは、untracked/modifed/staged filesおよび多くについての情報を返します。 出力は次のようになります。
> 
> {% filename %}command-line{% endfilename %}
> 
>     $ git status
>     On branch master
>     
>     Initial commit
>     
>     Untracked files:
>       (use "git add <file>..." to include in what will be committed)
>     
>             .gitignore
>             blog/
>             manage.py
>             mysite/
>     
>     nothing added to commit but untracked files present (use "git add" to track)
>     
> 
> 最終的に我々 は、変更内容を保存します。本体に移動し、これらのコマンドを実行します。
> 
> {% filename %}command-line{% endfilename %}
> 
>     $ git add --all .
>     $ git commit -m "My Django Girls app, first commit"
>      [...]
>      13 files changed, 200 insertions(+)
>      create mode 100644 .gitignore
>      [...]
>      create mode 100644 mysite/wsgi.py
>     
> 
> ## Pushing your code to GitHub
> 
> [ GitHub.com ](https://www.github.com)にアクセスし、新しい無料のユーザーアカウントを登録します。 （もしあなたがすでにワークショップの準備でそれをしていたら、それは素晴らしいことです！）
> 
> そして、新しいリポジトリに "my-first-blog"の名前で新しいリポジトリを作成します。 "READMEで初期化する"チェックボックスをオフのままにし、.gitignoreオプションを空白にして（手動で行っています）、ライセンスをNoneのままにしておきます。
> 
> ![](images/new_github_repo.png)
> 
> > **注</ strong> ` my-first-blog </ code>という名前は重要です。何か他のものを選択することもできますが、以下の手順では何度も繰り返す必要があります。他の名前を選択した場合は、 毎回それを置き換えてください。 できれば、<code>my-first-blog`の名前にしておきましょう。</p> </blockquote> 
> > 
> > 次の画面では、あなたの repo のクローンの URL が表示されます。"HTTPS"のバージョンを選択してコピーし端末にそれを貼り付けます。
> > 
> > ![](images/github_get_repo_url_screenshot.png)
> > 
> > GitHub上のGitリポジトリにコンピュータのGitリポジトリを接続する必要があります。
> > 
> > コンソールに次のように入力します（`＆lt; your-github-username＆gt; </ code>をGitHubアカウントの作成時に入力したユーザー名に置き換えます）。</p>

<p>{% filename %}command-line{% endfilename %}</p>

<pre><code>$ git remote add origin https://github.com/<your-github-username>/my-first-blog.git
$ git push -u origin master
`</pre> 
> > 
> > あなたのGitHubのユーザー名とパスワードを入力すると、次のように表示されます：
> > 
> > {% filename %}command-line{% endfilename %}
> > 
> >     Username for 'https://github.com': hjwp
> >     Password for 'https://hjwp@github.com':
> >     Counting objects: 6, done.
> >     Writing objects: 100% (6/6), 200 bytes | 0 bytes/s, done.
> >     Total 3 (delta 0), reused 0 (delta 0)
> >     
> >     To https://github.com/ola/my-first-blog.git
> >      * [new branch]      master -> master
> >     Branch master set up to track remote branch master from origin.
> >     
> > 
> > <!--TODO: maybe do ssh keys installs in install party, and point ppl who dont have it to an extension -->
> > 
> > コードは GitHub 上で公開されます。 それをチェックしにいってください。 これは、[ Django ](https://github.com/django/django)、<a href = "https://github.com/DjangoGirls/tutorial/にあります。 "> Django Girls Tutorial </a>などのオープンソースソフトウェアプロジェクトも、GitHubでコードをホストしています。 :)
> > 
> > # PythonAnywhereでブログを設定する
> > 
> > ## PythonAnywhere アカウントにサインアップする
> > 
> > > **備考：**あなたがすでにPythonAnywhereのアカウントを以前に作成しインストールの手順をふんでいたら、再びそれを行う必要はありません。
> > 
> > {% include "deploy/signup_pythonanywhere.md" %}
> > 
> > ## PythonAnywhere でサイトを設定する
> > 
> > メインの[ PythonAnywhere Dashboard ](https://www.pythonanywhere.com/)のロゴをクリックし、「Bash」コンソールを起動するオプションを選択します。これがPythonAnywhereバージョンです あなたのコンピュータのコマンドラインと同じようなものです。
> > 
> > ![The 'New Console' section on the PythonAnywhere web interface, with a button for 'bash'](images/pythonanywhere_bash_console.png)
> > 
> > > **備考：**PythonAnywhere は Linux に基づきますので、Windows の場合は、あなたのコンピュータ上で進行中のものと少し違って見えるでしょう。
> > 
> > PythonAnywhereにWebアプリケーションをデプロイするには、コードをGitHubからプルし、PythonAnywhereでそれを認識してWebアプリケーションとして提供するように設定する必要があります。 それを手動で行う方法もありますが、PythonAnywhereはそれをすべて行うヘルパーツールを提供しています。 まず、インストールしてみましょう。
> > 
> > {% filename %}PythonAnywhere command-line{% endfilename %}
> > 
> >     $ pip3.6 install --user pythonanywhere
> >     
> > 
> > ` pythonanywhereを収集する</ code>のようなものをいくつか出力し、最終的に<code> pythonanywhere-（...）</ code>をインストールしたという行で終わるはずです。</p>

<p> GitHub からアプリを自動的に構成するためのヘルパーを実行します。 PythonAnywhereのコンソールに次のように入力します（<code>＆lt; your-github-username＆gt; </ code>の代わりにGitHubユーザー名を使用することを忘れないでください）：</p>

<p>{% filename %}PythonAnywhere command-line{% endfilename %}</p>

<pre><code>$ pa_autoconfigure_django.py https://github.com/<your-github-username>/my-first-blog.git
`</pre> 
> > 
> > 上記を実行すると、あなたは以下の事ができるようになります。：
> > 
> > - GitHubからコードをダウンロードする
> > - 自分のPC上のように、PythonAnywhereでのvirtualenvを作成している
> > - 一部のデプロイメント設定で設定ファイルを更新する
> > - ` manage.py migrate </ code>コマンドを使ってPythonAnywhereにデータベースを設定する</li>
<li>静的ファイルの設定（これについては後で学習します）</li>
<li>PythonAnywhereでAPIを使用してWebアプリケーションを提供するように設定する</li>
</ul>

<p>PythonAnywhereでは、これらすべてのステップは自動化されていますが、他のサーバプロバイダと同じ手順を経なければなりません。  今注目すべき重要な点は、PythonAnywhere上のデータベースが、自分のPC上のデータベースとはまったく別物であることです。つまり、異なる投稿と管理者アカウントを持つことができます。</p>

<p>その結果、自分のコンピュータで行ったように、<code> createsuperuser </ code>で管理者アカウントを初期化する必要があります。 PythonAnywhereがあなたのためにあなたのvirtualenvを自動的に起動したので、あなたがする必要があるのは以下の通りです：</p>

<p>{% filename %}PythonAnywhere command-line{% endfilename %}</p>

<pre><code>(ola.pythonanywhere.com) $ python manage.py createsuperuser
`</pre> 
> >     管理者の詳細を入力します。 PythonAnywhere上のパスワードをより安全にしたい場合を除き、混乱を避けるために自分のコンピュータで使用しているのと同じものを使用することをお勧めします。
> >     
> >     PythonAnywhereのコードをlsを使って見てみることもできます：
> >     
> >     {% filename %}PythonAnywhere command-line{% endfilename %}
> >     
> >         (ola.pythonanywhere.com) $ ls
> >         blog  db.sqlite3  manage.py  mysite  static
> >         (ola.pythonanywhere.com) $ ls blog/
> >         __init__.py  __pycache__  admin.py  forms.py  migrations  models.py  static
> >         templates  tests.py  urls.py  views.py
> >         
> >     
> >     また、「ファイル」タブに移動し、PythonAnywhereに組み込まれているファイルブラウザを使用して移動することもできます。
> >     
> >     ## You are now live!
> >     
> >     あなたのサイトは現在、インターネット上に生存しているはずです！ PythonAnywhereの「Web」タブをクリックしてリンクを取得します。 あなたはあなたが望む誰とでもこれを共有することができます:)
> >     
> >     > **注</ strong>これは初心者向けのチュートリアルです。このサイトを展開する際には、セキュリティの観点からは理想的ではない、いくつかのショートカットを取り上げました。 このプロジェクトを構築するか、新しいプロジェクトを開始する場合は、[ Djangoデプロイメント チェックリスト](https://docs.djangoproject.com/ja/1.11/howto/deployment/checklist/)をご覧ください。</p> </blockquote> 
> >     > 
> >     > ## デバッギングのヒント
> >     > 
> >     >  pa_autoconfigure_django.py </ code>スクリプトの実行中にエラーが表示された場合は、次のような原因が考えられます。</p>

<ul>
<li>PythonAnywhere APIトークンの作成を忘れている</li>
<li>あなたのGitHubのURLを間違えている</li>
<li><em>Could not find your settings.py</ em>というエラーが表示された場合は、おそらくGitにすべてのファイルを追加できなかったか、 GitHubが正常に終了しました。  この場合はGitセクションをもう一度見てください</li>
</ul>

<p>サイトにアクセスしようとするとエラーが表示された場合、最初にデバッグ情報を探す場所は<strong>エラーログ</ strong>です。 PythonAnywhereの<a href="https://www.pythonanywhere.com/web_app_setup/"> Webタブ</a>には、このリンクがあります。 そこにエラーメッセージがあるかどうかを確認してください。 最新のものは一番下にあります。</p>

<p><a href="http://help.pythonanywhere.com/pages/DebuggingImportError"> PythonAnywhereヘルプサイトの一般的なデバッグのヒント</a>もあります。</p>

<p>つまづいた時は、コーチに助けを求めましょう。</p>

<h1>Check out your site!</h1>

<p>サイトのデフォルトページでは、ローカルコンピュータと同じように「It worked！」と表示されます。 URLの最後に<code> / admin / </ code>を追加すると、管理サイトに移動します。 ユーザー名とパスワードでログインすると、新しい投稿をサーバーに追加することができます。</p>

<p>いくつかの投稿を作成したら、ローカルの設定（PythonAnywhereではなく）に戻ることができます。 ここから、変更を加えるためにあなたのローカルセットアップで作業する必要があります。 GitHubを開き、変更をライブWebサーバーにプルダウンします。 これにより、ライブWebサイトを破壊することなく作業して実験することができます。 かなりクールですね？！</p>

<p>ふつうは、 サーバーの導入はWeb開発の最も難しい部分の1つであり、作業を開始するまで数日かかることがよくあります。 しかし、あなたは実際のインターネット上で、あなたのサイトを動かす事ができました！</p>