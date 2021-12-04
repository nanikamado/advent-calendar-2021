---
title: '$\LaTeX$ で書いたようにしか見えない HTML を作った話'
text-title: LaTeX で書いたようにしか見えない HTML を作った話
author: まど
date: 2021年12月5日
---

## はじめに
- この記事は [UEC Advent Calendar 2021](https://adventar.org/calendars/6400) の5日目の記事です。

  昨日はykm11さんの、[レポートは 提出期限 までに出せ それにつけても 金の欲しさよ（65537プロダクション）](https://ykm11.hatenablog.com/entry/2021/12/04/235756)でした。電通大の近くに温泉があるのは知りませんでした。これはかなりのお役立ち情報です。
- この記事は10月ごろに書いた記事に加筆・修正を加えたものです。完全新作じゃなくてごめんなさい。
- iPadやiPhoneのブラウザで表示すると、ページの境目にある1文字が消えるバグがあります。公開してから気付きました。他のブラウザでも不具合があるかもしれません。PC版のChromeかFireFoxで見てください。余裕を持って書かないからこうなるんですよね\...

## 要約
LaTeX 討伐のため、Markdown を LaTeX 風HTMLに変換するスクリプトを書いたが、LaTeX を倒すことはできなかった。しかし、頑張ればHTML/CSSでも LaTeX の代替となり得ることは分かった。

## この変なHTMLは何？
これは、LaTeX で書いたようにしか見えない HTMLです。

数式を表示することもできますし、

$$:a
f'(x)=lim_{Delta x->0}(f(x+Delta x)-f(x))/(Delta x)
$$

表も一応 LaTeX 風です。[注釈もLaTeX風です。]{.footnote}

| AsciiMath  |          $\LaTeX$           |     表示      |
| :--------: | :-------------------------: | :-----------: |
|   `a/b`    |         `\frac ab`          |   $:a a/b$    |
|   `ab/c`   |         `a\frac bc`         |   $:a ab/c$   |
:AsciiMath と LaTeX の比較 {#tbl:table1}

このままでも十分な LaTeX ですが、ブラウザの印刷機能でPDFにすればもっと LaTeX になります。ブラウザは PC 版の Chrome か Firefox が良いです。

## 作った経緯
LaTeXにはファンが多いですが、私は LaTeX を書くのが苦手です。できることなら LaTeX を書きたくありません。今まで、どうしてもLaTeXを使ってレポートを書く必要がある場合は、Markdown を Pandoc で LaTeX に変換する方法を使っていました。この方法では一旦 LaTeX を経由してから PDF にするため、LaTeX の全コマンドが使えるという大きなメリットがあり、大変お世話になりました。

しかし、この方法には次のような欠点がありました。

- デザインを調整・変更するためには LaTeX の知識が必要なため、デサインの調整・変更が実質できない。
- 数式は LaTeX のコマンドを使って書く必要があるが、LaTeX は書きたくない。
- LaTeX に依存しているため、なにか不具合が起こったときに LaTeX の知識が必要になるかもしれない。

そこで、Markdown を LaTeX 風のデザインのHTMLに変換するスクリプトを書きました。（LaTeX 風のデザインにしたのはその方が面白そうだったからです。別に LaTeX のデザインが好きだからというわけではないんだからね。）

HTMLなので、CSSで自由にデザインの調節・変更ができます。

## 完成したもの
完成したスクリプトは、[https://github.com/nanikamado/tex-html](https://github.com/nanikamado/tex-html) に置いておきました。

スクリプト自体は、Pandoc にコマンドライン引数を渡しているだけの簡単なものなのですが、依存しているコマンドが [pandoc](https://github.com/jgm/pandoc)、[pandoc-crossref](https://github.com/lierdakil/pandoc-crossref)、[pandoc-asciimath-filter](https://github.com/nanikamado/asciimathml-pandocfilter)、[pandoc-katex](https://github.com/nanikamado/node-pandoc-katex) と4つもあってインストールが大変なので、Homebrewのパッケージにまとめました。

Linuxの方は、(Homebrewをインストールしていない場合は) [公式サイト](https://brew.sh/) にしたがって Homebrew をインストールしてから、

```
brew install nanikamado/tap/tex-html
```

でインストールできます。

Macは持っていないので試していませんが、多分同じ方法でインストールできると思います。WindowsでもWSLを使えば同じ方法でインストールできるかもしれませんが、詳しくないので分かりません。

**とはいえ、このスクリプトを使ってレポートを書くのはおすすめしません。**理由は後述します。

## 使い方

次のように実行してください。
```
tex-html Markdownのファイル名 -o HTMLのファイル名
```

もしくは、次のように標準入力・標準出力を使うこともできます。

```
tex-html <Markdownのファイル名 >HTMLのファイル名
```


## 書けるもの
普通のMarkdownで書けるものに加えて、数式も書くことができます。

数式は、AsciiMathか LaTeX で書くことができるようにしました。AsciiMath というのは、書けることを数式に絞るとこで LaTeX を書きやすくしたような言語です。できることは\LaTeXより少ないものの、LaTeX よりも簡潔に数式を書くことができます。AsciiMathの書き方は [asciimath.org](http://asciimath.org/) から見れます。

Markdown内で次のように `$$` を使うとLaTeXで数式が書けます。数式の表示には、$\KaTeX$ というライブラリを使っています。どのコマンドが使えるかは[KaTeXのドキュメント](https://katex.org/docs/supported.html)で確認してください。

```
$$
f'\left(x\right)=\lim_{\Delta x\to0}\frac{f\left(x+\Delta x\right)-f\left(x\right)}{\Delta x}
$$
```

数式をAsciiMathで書きたいときは、次のように始めの `$$` の後ろに `:a` を付けてください。

```
$$:a
f'(x)=lim_{Delta x->0}(f(x+Delta x)-f(x))/(Delta x)
$$
```

インラインの数式は、次のように、1つの`$`で囲んでください。

```
$\frac12$←これはLaTeXで、$:a 1/2$←これはAsciiMathです。
```
表示:

>> $\frac12$←これはLaTeXで、$:a 1/2$←これはAsciiMathです。

また、コードブロック、表、画像にキャプションを付けたり、コードブロック、表、画像、数式に番号をふって文中から参照することもできます。

## 仕組み
HTMLで LaTeX を模倣する場合、各ページの上や下についているヘッダーや注釈、ページナンバーをどうやって表示するのかというのが問題になります。
HTMLをブラウザで印刷すると、ブラウザがページの上や下にタイトルやページ番号などを付けてくれたりしますが、大体の場合そういうのは見た目が LaTeX っぽくありません。

LaTeX は、文章がページによって分割されることを前提に作られていますが、HTML/CSSはそうではありません。ここに大きな隔たりがあるのです。

そこで登場するのが、[CSS Paged Media Module Level 3](https://www.w3.org/TR/css-page-3/) というCSSの規格です。これは、文章が紙やPDFに印刷されて、ページによって分割されたとき、どのようにコンテンツが表示されるべきかを、CSSによって詳細に記述することができるようにするための規格です。これを使えば、LaTeXのページ番号やヘッダー・フッターをHTML/CSSで再現することができます。しかし、この規格はまだ標準化団体が案として考えている段階なので、Chromeなどの各ブラウザには実装されていないません。

そこで、この機能を無理やり使えるようにしてくれるPaged.jsというJavaScriptライブラリを利用しました。

Paged.jsは、HTMLが読み込んでいるすべてのCSSをパースして、Paged Media 3 の機能が使われていたら、HTML要素を追加したりCSSを書き換えたりしてその機能を使えるようにするという、なかなかに豪快な JavaScript ライブラリです。

この記事にページ番号が付いていたり、各ページの上にタイトルが表示されているのはPaged.jsのおかげです。内容を各ページに分割することもPaged.jsがやっています。本当は、Webページとして表示するときはページで分割されていない普通のWebページとして表示して、印刷するときだけページ分けするようにしてほしいのですが、そういうことはできないみたいです。

## 問題点
Pandocと Paged.jsによってMarkdownからLaTeX風のHTMLを作ることには一応成功しましたが、以下のような問題点があります。

- Chromeで印刷する時、表が複数ページにまたがると表示がおかしくなる（Paged.js のバグ）
- コードブロックが複数ページにまたがると、行番号の表示がおかしくなる（Paged.js のバグ）
- そもそも Paged.js の開発が止まっているように見える

他にもいろいろ不具合があると思います。このスクリプトを使ってレポートを書くのをおすすめしないのはそのためです。レポートのデザインをCSSで調整する必要がないのであれば、MarkdownをLaTeXに変換してPDFにする方法を使ったほうが良いと思います。その方が不具合が少ないですし、LaTeXのコマンドが使えるので便利です。

とはいえ、HTML/CSSがLaTeXの代わりとして使えるかもしれないということはこの記事で伝えられたと思います。私はそれだけで満足です。~~あの忌々しいLaTeXを駆逐するため、共に闘いましょう。~~

## 補足
- Pandocとは、あるマークアップ言語で書かれた文章を他のマークアップ言語に変換するためのツールです。
- HomebrewはMacのパッケージマネージャーという印象があるかもしれませんが、実はLinuxでも使えます。aptやdnf、pacmanなどのパッケージマネージャーは、コマンドを`/usr/bin/` などにインストールするため、aptとdnf一緒に使おうとしたりすると競合がおきてしまいますが、HomebrewをLinuxで使う場合コマンドは`/home/linuxbrew/.linuxbrew/bin/` のような専用のディレクトリにインストールされるので、aptなどと一緒に使っても不具合があまり起きません。(たまにコマンドのバージョンや中身が違ったりして問題が発生する場合はあるかもしれませんが、めったに起こらないと思います。) 
  
  普通Linux向けにアプリを配布する場合、いろいろなディストリビューション用のパッケージをたくさん用意しないといけませんが、今回はめんどくさかったので、Homebrewにしました。
  
  こういうディストリビューションに依存しないパッケージマネージャーは他にもあって、Nixとかも有名です。
- Homebrewでインストールしたコマンドが、元からあるコマンドを隠してしまうことがあるので気をつけてください。例えば、`tex-html` はRubyを使っているので、 `brew install nanikamado/tap/tex-html` をすると、依存としてRubyがインストールされます。Homebrewは、他のパッケージマネージャーがインストールしたコマンドのことは気にしないので、既にRubyが別のパッケージマネージャーでインストールされていたとしても、新しくRubyをインストールします。なので、Rubyのバージョンが変わってしまったりします。これを回避したい場合は、環境変数 `PATH` の中のディレクトリの並び順を変えてください。
- Docker使えよっていう意見があると思います。私もそう思います。でもDockerの使い方が分かりません。ごめんなさい。
- CSS Paged Media Module Level 3 を使えるようにするツールは、Paged.jsの他にもいくつかあります。[それらのツールを一覧にしてまとめているサイト]((https://print-css.rocks/lessons))があって、そのサイトでは以下の7つが紹介されていました。

    - PDFreactor
    - Prince
    - Antenna-house
    - Weasy-print
    - Paged.js
    - Typoset.sh
    - Vivliostyle

  この中で、Weasy-print、Paged.js、Vivliostyle、以外はプロプライエタリです。非営利の利用は無料になっているものもありますが、各種パッケージマネージャーで配信されていなかったり、無料で利用する場合でもライセンスキーを取得しなければならなかったりしていろいろ面倒だったので、これらは使いませんでした。

  Vivliostyleは Running elements という機能をサポートしていないのですが、Running elements は使いたいので、これも除外です。

  Weasy-printは、上のサイトでは Running elements をサポートしていると書いてあったのですが、試してみたらクラッシュしたのでこれも除外します。(どうやら、Running elementsの処理にバグがあって、特定の条件でクラッシュするようです。[github.com/Kozea/WeasyPrint/issues/1239](https://github.com/Kozea/WeasyPrint/issues/1239))

  残ったのがPaged.jsでした。
- LaTeX を $\LaTeX$ と書くか LaTeX と書くかは迷いましたが、$\LaTeX$ と書くとそこだけ浮いているような感じになってしまったので、LaTeX と書くことにしました。