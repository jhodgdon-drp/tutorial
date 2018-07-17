{% set warning_icon = '<span class="glyphicon glyphicon-exclamation-sign" style="color: red;" aria-hidden="true" data-toggle="tooltip" title="An error is expected when you run this code!" ></span>' %}

# アプリケーションを拡張しよう

もう、ウェブサイトを作るのに必要な全ての章は終わりました。どのようにモデル、URL、ビュー、テンプレートを書いたら良いかわかっていますし、またウェブサイトの作り方もわかります。

さあ練習しましょう！

ブログに最初に必要なものはおそらく、記事を表示するページですよね。

もう`Post`モデルが入っていますから、`models.py`は追加する必要はありません

## 投稿の詳細へのテンプレートリンクを作成する

`blog/templates/blog/post_list.html`ファイルに次のようにリンクを追加しましょう： {% filename %}blog/templates/blog/post_list.html{% endfilename %}

```html
{% extends 'blog/base.html' %}

{% block content %}
    {% for post in posts %}
        <div class="post">
            <div class="date">
                {{ post.published_date }}
            </div>
            <h1><a href="">{{ post.title }}</a></h1>
            <p>{{ post.text|linebreaksbr }}</p>
        </div>
    {% endfor %}
{% endblock %}
```

{% raw %}投稿リストの投稿のタイトルから投稿の詳細ページへのリンクが必要です。 投稿の詳細ページにリンクするように`<h1><a href="">{{ post.title }}</a></h1>` {% endraw %}

{% filename %}{{ warning_icon }} blog/templates/blog/post_list.html{% endfilename %}

```html
<h1><a href="{% url 'post_detail' pk=post.pk %}">{{ post.title }}</a></h1>
```

{% raw %}` {％url 'post_detail' pk = post.pk％}`を説明します。 `{% %}`という表記は、Djangoのテンプレートタグを使用していることを意味しています。 次にURLを作成するものを使用します！

`post_detail`の部分は、Djangoが`blog/urls.py`にname = post_detailのURLを指定していることを意味します

pk = post.pkの部分は、 pkは主キーの略で、データベースの各レコードの一意の名前です。 Postモデルでプライマリキーを指定しなかったので、Djangoは私たちのために1つのキーを作成します（デフォルトでは、レコードごとに1、2、3と数字が増えます）。 私たちの記事はPostオブジェクトの他のフィールド（タイトル、作者など）にアクセスするのと同じ方法で、post.pkを書くことによって主キーにアクセスします！

さて、私たちがhttp://127.0.0.1:8000/に行くと、（`post_detail`のURLまたは*view*をまだ持っていないので、 >）。 それは次のようになります：

![NoReverseMatch error](images/no_reverse_match2.png)

## 投稿の詳細へのURLを作成する

`post_detail` *ビュー*用に`urls.py`にURLを作成しましょう！

最初の投稿の詳細がこの**URL**に表示されるようにします：http://127.0.0.1:8000/post/1/

Djangoが`post_detail`という名前の*表示*を指すように`blog/urls.py`ファイルにURLを作ってください。 `blog/urls.py``url(r'^post/(?P<pk>\d+)/$', views.post_detail, name='post_detail',`行を追加します。 にファイルにコピーします。 ファイルは次のようになるでしょう。

{% filename %}{{ warning_icon }} blog/urls.py{% endfilename %}

```python
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.post_list, name='post_list'),
    url(r'^post/(?P<pk>\d+)/$', views.post_detail, name='post_detail'),
]
```

この部分`^post/(？P<pk>\d)/$`は難しく見えますが、心配する必要はありません。

- `^`は「文字列の開始」を意味します。
- `post/`というのは、最初の後に**post**と**/**という単語が含まれることを意味します。 ここまでは順調ですね。
- `(?P<pk>\d+)` -この部分はトリッキーです。 これは、Djangoがあなたがここに置いたすべてを、`pk`という変数としてビューに転送することを意味します。 （これは`blog/templates/blog/post_list.html`でプライマリキー変数に与えた名前と一致します）！`\d` 数字で、文字ではありません（0と9の間のすべてです）。 `+`は、そこに1つ以上の数字が必要であることを意味します。 したがって、`http://127.0.0.1:8000/post//`のようなものは無効ですが、`http://127.0.0.1:8000/post/1234567890/`は 完全にOK！
- `/` - もう一度**/**を入力する必要があります。
- `$` -「終わり」!を意味します。

つまり、ブラウザに`http://127.0.0.1:8000/post/5/`を入力すると、Djangoは*view*を探していると理解します。`post_detail`に移動し、`pk`が`5`と同じ情報をその*view*に転送します。

[Ok] を我々 は `blog/urls.py` に新しい URL パターンを追加しました! ページを更新しましょう：http://127.0.0.1:8000/ Boom！ サーバーが再び実行を停止しました。 コンソールを見てください - 予想通り、もう一つのエラーがあります！

![AttributeError](images/attribute_error2.png)

あなたは次のステップが何であるか覚えていますか？ もちろん：ビューを追加する！ですね。

## 投稿の詳細ビューを追加する

今回は*view*に追加のパラメータ`pk`が与えられます。 私たちの*view*はそれを捕らえる必要がありますか？ そこで関数を`def post_detail(request、pk):`として定義します。 urls（`pk`）で指定した名前とまったく同じ名前を使用する必要があることに注意してください。 この変数を省略すると、エラーが発生します。

今、私たちは1つだけのブログ投稿を取得したいと考えています。 これを行うには、次のようにクエリーセットを使用できます。

{% filename %}{{ warning_icon }} blog/views.py{% endfilename %}

```python
Post.objects.get(pk=pk)
```

しかし、このコードには問題があります。 与えられた`主キー`（`pk`）で`Post`が存在しない場合、非常に醜いエラーが発生します。

![DoesNotExist error](images/does_not_exist2.png)

私たちはそれを望んでいません！ しかしもちろん、Djangoには、それを処理するものがあります：`get_object_or_404`。 与えられた`pk`に`Post`がない場合、`Page Not Found 404`のページが表示されます。

![Page not found](images/404_2.png)

自分用の`Page not found`ページを作成することもできます。 しかし、それは現在非常に重要ではないので、私たちはそれをスキップします。

`views.py`ファイルに*view*を追加してください。

`blog/urls.py`では`views.post_detail`というビューを参照する`post_detail`という名前のURLルールを作成しました。 これは、Djangoが`blog/views.py`内の`post_detail`というビュー機能を使うことを意味します。

`blog/views.py`を開き、他の`from`行の近くに次のコードを追加する必要があります。

{% filename %}blog/views.py{% endfilename %}

```python
from django.shortcuts import render, get_object_or_404
```

ファイルの最後に*view*を追加します：

{% filename %}blog/views.py{% endfilename %}

```python
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'blog/post_detail.html', {'post': post})
```

ページを更新してみましょう：http://127.0.0.1:8000/

![Post list view](images/post_list2.png)

出来ましたね！ しかし、あなたはブログのポストタイトルのリンクをクリックするとどうなりますか？

![TemplateDoesNotExist error](images/template_does_not_exist2.png)

あらいやだ！ 別のエラー！ しかし、私たちはすでにそれに対処する方法をすでに知っていますね。 そう！テンプレートを追加する必要があります！

## 投稿の詳細へのテンプレートリンクを作成する

`blog/templates/blog`に`post_detail.html`というファイルを作成します。

こんな感じですね。

{% filename %}blog/templates/blog/post_detail.html{% endfilename %}

```html
{% extends 'blog/base.html' %}

{% block content %}
    <div class="post">
        {% if post.published_date %}
            <div class="date">
                {{ post.published_date }}
            </div>
        {% endif %}
        <h1>{{ post.title }}</h1>
        <p>{{ post.text|linebreaksbr }}</p>
    </div>
{% endblock %}
```

もう一度`base.html`を拡張します。 `content`ブロックでは、投稿のpublished_date（存在する場合）、タイトル、およびテキストを表示します。 しかし、私たちはいくつかの重要なことについて議論すべきですよね？

{% raw %}`{% if ... %} ...  {％endif％}`は、何かをチェックしたいときに使用できるテンプレートタグです。 (`if ... else...` Introduction to Pythonのチャプターでやってこを覚えていますか？）このシナリオでは我々はポストの`published_date`が空ではないかどうかを確認します。{% endraw %}

これで、`TemplateDoesNotExist`がなくなったかどうか確認してページを更新できます。

![Post detail page](images/post_detail2.png)

イェーイ！うまくできていますね！

# Deploy time!

あなたのウェブサイトがまだPythonAnywhere上で動作するかどうかを確認してみましょう。

{% filename %}command-line{% endfilename %}

    $ git status
    $ git add --all .
    $ git status
    $ git commit -m "Added view and template for detailed blog post as well as CSS for the site."
    $ git push
    

それから、[PythonAnywhere Bash console](https://www.pythonanywhere.com/consoles/)で：

{% filename %}command-line{% endfilename %}

    $ cd ~/<your-pythonanywhere-username>.pythonanywhere.com
    $ git pull
    [...]
    

(`<your-pythonanywhere-username>`の部分を、自分の実際のPythonAnywhereのユーザー名に角カッコをはずして置き換えることを忘れずに)

## サーバー上の静的ファイルの更新

PythonAnywhereのようなサーバは、（CSSファイルのような）「静的ファイル」をPythonファイルとは違って扱うのが好きです。なぜなら、それらが高速に読み込まれるように最適化できるからです。 その結果、CSSファイルを変更するたびに、サーバー上で追加のコマンドを実行して、更新するように指示する必要があります。 コマンドは`collectstatic`です。

あなたが使用している`source myenv/bin/activate`コマンドと同じです（PythonAnywhereはこれを行うために`workon`というコマンドを使用します） あなた自身のコンピュータで）：

{% filename %}command-line{% endfilename %}

    $ workon <your-pythonanywhere-username>.pythonanywhere.com
    (ola.pythonanywhere.com)$ python manage.py collectstatic
    [...]
    

`manage.py collectstatic`コマンドは、`manage.py migrate`のようなものです。 私たちはコードをいくつか変更してから、Djangoにサーバの静的ファイルのコレクションまたはデータベースに変更を適用するよう指示します。

いずれにしても、[Webタブ](https://www.pythonanywhere.com/web_app_setup/)にアクセスして、**Reload**を押す準備が整いました。

そしてdeployします! おめでとうございます :)