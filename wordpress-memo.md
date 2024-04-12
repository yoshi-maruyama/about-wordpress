# ちゃんと学ぶ WordPress テーマ開発講座

## wordpress 開発の環境を作ろう

.org から引っ張る

local を使って wordpress を立ち上げる
https://localwp.com/

local を立ち上げて admin 画面にて
wp mulitibyte patch をインストール・有効化
日本語文字のマルチバイドにおいて発生する不具合を修正してくれるもの。

## wordpress のテーマを知ろう

techport/app/public/wp-content/themes
がテーマが格納される場所。

テーマにはテンプレート階層という概念があって、これに従って表示されるテンプレートが決定する

既存のテーマを直接いじるのではなく、子テーマとして、既存のテーマ style.css で指定して、使われているテーマかそれよりも優先階層の高いページを作成して上書きをしていく

##　オリジナルのブログサイトを作ろう

https://startbootstrap.com/
からテンプレートを持ってきた

wordpress は index.php が実際にあるポジション目線ではなくて
階層がちょっと上のところ視点になる。
そのため、相対パスで他ファイルの読み込みをしようとするとうまく持ってこれない
そこで、絶対パスを使う

`http://techport.local/wp-content/themes/myblog/vendor/bootstrap/css/bootstrap.min.css`

wordpress では wordpress の開発チームが作成した特別な関数が最初から使えるようになっている
テンプレートタグというよ
例: the_title()

よくあるループするときの書き方
the_posts は投稿を一つ進めて、その進んだ結果は have_posts に影響する結構トリッキーな動きをする

```php
  <?php while (have_posts()) : the_post(); ?>
```

著者を返す
the_author()

アイキャッチ画像の設定とかを wordpress の管理仮面から作成できるが、それはそもそも使ってるテーマがサポートしているときに利用できる
functions.php

wordpress でなにかデバッグをしたいときに手っ取り早く画面に表示させる方法
`var_dump($id);`

パーマリンク：
固定ページを作成したときにその URL はタイトルと同一になる。
日本語だと不自由するから、固定ページ作成の設定で url を編集しておこう

作成したページを別の階層の下にするときはページ属性から

投稿の URL も
/%year%/%monthnum%/%post_id%/
がおすすめとのこと

パーマリンクを作成すると、htaccess というファイルが編集されるよ
apatch が必要なファイル
ないと not found になるよ

お問い合わせフォームを実装する
プラグインが必要
mw wp form がおすすめ(バリデーション周りの機能が豊富なのがよきポイント)
他にもおすすめはある

作成したフォームのショートコードを固定ページに貼り付けて公開する

ローカル環境ではメールサーバーは現在用意していないので、送信しても届かないよ

カテゴリーとタグ

カテゴリーは親カテゴリを指定することができる
カテゴリで階層を作ることができる

タグには階層を指定する機能はない

全ての投稿はカテゴリに入る。
デフォルトのカテゴリを指定をすることはできる

カテゴリは最大でも 10 個までにしておこう
あまり増やさない方が、サイトの見通しがいいよ

細かい調整はタグでやろう

カテゴリーページを作る
カテゴリの表示からカテゴリ一覧を表示できる

wp_title()
そのカテゴリのタイトル名を出す（区切り文字がデフォルトで着く）

タグのページを作成する

is_category()
今表示されているページがカテゴリページかどうかを判定する
（条件分岐タグ）

ページネーション
設定の表示設定から一ページに表示させる件数が指定できる

テストデータを入れる
theme-test-data-ja
ツール＞インポート

このテストデータはいろいろいじわるな設定がついているので、いろんなパターンの見た目のテストにも使えるよ

next_posts_link()
次のページネーションへのリンクを表示

get_next_posts_link()
と
str_replace を使ってリンクを作成できる

get_previous_posts_link()
`str_replace("<a", "<a class="btn btn-primary float-right", $link)`

メニューを作成

管理バーが上にあると見づらいときは
admin bar position というプラグインを入れてバーを下に下げておく

functions から register_nav_menues(["global_nav" => "グローバルナビゲーション"])
を入れることでナビゲーションを外観から操作できるようになる

メニューを表示

wp_nav_menu()
シンプルにメニューを表示

get_nav_menu_locations()
wp_get_nav_menu_items($menu->term_id)

エスケープ
データベースの中身をそのまま載せるのはあまり好ましく無い
esc_html($item->title)
文字列を html ではなくそのままの文字列として画面に表示させる
esc_attr()

ブログを仕上げよう

bloginfo("name")
サイトの名前

bloginfo("description")
サイトの説明

echo esc_url(home_url("/"))
サイトのルートのページへの url

コピーライト
`Copyright &copy; tomosta blog 2020`

## サイトの公開・引っ越し・バックアップと復元をしよう

wp-content とデータベースがいろいろ操作した結果、変更・追加される場所

wp-options にドメイン情報が入ってしまっているから、そのままはできない

all in one wp migration のプラグインを使って簡単に移行する
プラグインの機能を使って、エクスポートする

シン・クラウド for free をレンタルサーバーにしているよ

サーバー内で初期のワードプレスを構築する
サーバー側のワードプレスに all in one wp migration を入れてさっきエクスポーとしたものインポートする
すると、テーマもデータも変わる。
注意すべきは管理者としてログインするときのアカウントは local で構築したときのアカウントになる

独自ドメインの取得

ドメインを取得する

ドメイン取得には mumudomain を使うよ

A レコードの設定
www の A レコードとその ip アドレスも登録しておこう

独自ドメインを取得してから wordpress を入れないとあとでドメイン設定できないよ

テスト環境としてもう一個用意しておくと便利なこともあるよ

手作業で移行するときはファイル転送サービスを使う
filezilla を使うよ

- 手順

  wp-content フォルダを移行
  データベースのバックアップを取得
  データベースをインポートする
  内容を書き換える
  velvet blues update url で url を書き換える

デバッグ
ローカルでなにかエラーが起きた場合は詳細が表示されるが、公開のあとの場合に詳細な内容を表示する方法がある
wp-config の wp-debug の値を true にする

## wordpress テーマ開発の高度な知識

今回は underscores というテーマを使う

カスタマイズすることが前提のテーマになっている。
wordpress にインストールすると css とかがほとんど当たっていない html だけのシンプルなレイアウトが表示される
けどリンクとか、それらの機能は一通り揃ってる状態

投稿タイプを増やす

投稿、固定ページの他に商品という投稿のタイプを増やすことで、商品を管理するためのページ群を作成できる

functions に
register_post_type("item", ["label" => "商品", "public" => true])

カスタム投稿ページの個別ページを作成する
single-$posttype.phpのファイルを作成する
$posttype は上記の例だと item になる（single-item.php）

投稿は親ページとかはない
固定ページは親ページの指定ができ、ページの階層構造を作成できる

カスタム投稿はデフォルトでは投稿と似たような機能になる

カスタムの固定ページも作成することができる
supports => ["page-attributes"],
hierarchial => true

両方の性質をもったカスタム投稿タイプにすることもできる

カスタム投稿タイプを新エディタにする
show_in_rest => true

カスタム分類
カスタム投稿の item にカスタム分類を入れる

register_taxonomy("genre", "item", ["label" => "商品ジャンル", "hierarchical" => true, "show_in_rest" => true])

the_archive_title()
分類のタイトル名を取得

the_taxonomies()
記事が属するカスタム分類を表示

get_term_link($term)
そのタームのリンクを

get_terms("genre")
カスタム分類の全てのタームを取得する

カスタムフィールド
custom-fields => true

投稿ページの 3 点リーダーからカスタムフィールドを有効にする

一定の項目をもった表示を登録しておいて、
get_post_meta(get_the_ID(), "価格", true)
などとして、テンプレートに表示させる

カスタムフィールドのキーを同一のものを複数用意すると
配列で取ることができる
get_post_meta(get_the_ID(), "価格", false)

カスタムテンプレート

固定ページ

ページに Template Name: サイドバーなし
のコメントを入れて、固定ページのテンプレートにサイドバーなしを入力すると
そのページがレイアウトとして採用されるようになる（複数枚可）

ショートコード

読者目線における今日の日付などといった動的な値を投稿の中に入れるときに使える

ブロックのウィジェットからショートコードを選択できる

ショートコードは functions.php に入れる
add_shortcode("date", function() {
return date("Y 年 n 月 j 日")
});

`[date]`

ショートコードに the_title()のような画面に表示させるような wordpress は使えない
get_the_title()といった取得するだけの関数は使える

functions.php の中で無効な記述があると投稿が作成できなくなる。
十分注意しよう！

ショートコードにパラメータを入れる

パラメータがうまく渡されないときを考慮してデフォルトの値を付与しながらセットしてあげる

```
add_shortcode("sum", function($atts) {
  $atts = shortcode_atts([
    "x" => 0,
    "y" => 0,
  ], $atts, $sum);
  return $atts["x"] + $atts["y"]
});
```

[sum x="10" y="23"]

プラグインを作成しよう

app>plugins
のディレクトの中に入れたいプラグインを実装していく

これまで functions に作成してきたような機能は全てテーマに紐づくため、
別のテーマに変更してしまうと、その機能は引き継げない。
テーマを横断して共通の機能を入れたい場合にプラグインは有効

ディレクトリ作る
ファイル作る
指定のコメントを入れる

functions.php に記載していた内容をプラグインにうつしてそのプラグインを有効にしておくと、そのまま使えている状態になるよ

アクションフック

add_shortcode のような add\*\*\*の関数のこと

フィルターフック

入ってきた値を加工して返す
the_title()とかはこれにあたる

```
add_filter("the_title", function($title) {
  return "■" + $title
})
```

## ブロックエディター用のカスタムブロックを作成しよう

ブロックエディタ

投稿とかを作成するときに出る＋で入れられるもの

カスタムブロックの制作環境

node.js の環境を構築しておく。
wp-content/public に
npx @wordpress/create-block gutenpride
を実行する

足りないパッケージがあればインストールしてね。(postcss)

これでブロックの雛形ができる
