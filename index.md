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
これは、LaTeX で書いたようにしか見えない HTML です。

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

このままでも十分な LaTeX ですが、ブラウザの印刷機能で PDF にすればもっと LaTeX になります。ブラウザは PC 版の Chrome か Firefox が良いです。

## 作った経緯
LaTeXにはファンが多いですが、私は LaTeX を書くのが苦手です。できることなら LaTeX を書きたくありません。今まで、どうしても LaTeX を使ってレポートを書く必要がある場合は、Markdown を Pandoc で LaTeX に変換する方法を使っていました。この方法では一旦 LaTeX を経由してから PDF にするため、LaTeX の全コマンドが使えるという大きなメリットがあり、大変お世話になりました。

しかし、この方法には次のような欠点がありました。

- デザインを調整・変更するためには LaTeX の知識が必要なため、デサインの調整・変更が実質できない。
- 数式は LaTeX のコマンドを使って書く必要があるが、LaTeX は書きたくない。
- LaTeX に依存しているため、なにか不具合が起こったときに LaTeX の知識が必要になるかもしれない。

そこで、Markdown を LaTeX 風のデザインの HTML に変換するスクリプトを書きました。（LaTeX 風のデザインにしたのはその方が面白そうだったからです。別に LaTeX のデザインが好きだからというわけではないんだからね。）

HTML なので、CSS で自由にデザインの調節・変更ができます。

## 完成したもの
完成したスクリプトは、[https://github.com/nanikamado/tex-html](https://github.com/nanikamado/tex-html) に置いておきました。

スクリプト自体は、Pandoc にコマンドライン引数を渡しているだけの簡単なものなのですが、依存しているコマンドが [pandoc](https://github.com/jgm/pandoc)、[pandoc-crossref](https://github.com/lierdakil/pandoc-crossref)、[pandoc-asciimath-filter](https://github.com/nanikamado/asciimathml-pandocfilter)、[pandoc-katex](https://github.com/nanikamado/node-pandoc-katex) と4つもあってインストールが大変なので、Homebrewのパッケージにまとめました。

Linux の方は、(Homebrew をインストールしていない場合は) [公式サイト](https://brew.sh/) にしたがって Homebrew をインストールしてから、

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
普通の Markdown で書けるものに加えて、数式も書くことができます。

数式は、AsciiMath か LaTeX で書くことができるようにしました。AsciiMath というのは、書けることを数式に絞るとこで LaTeX を書きやすくしたような言語です。できることはLaTeXより少ないものの、LaTeX よりも簡潔に数式を書くことができます。AsciiMath の書き方は [asciimath.org](http://asciimath.org/) から見れます。

Markdown 内で次のように `$$` を使うと LaTeX で数式が書けます。数式の表示には、$\KaTeX$ というライブラリを使っています。どのコマンドが使えるかは [KaTeX のドキュメント](https://katex.org/docs/supported.html)で確認してください。

```
$$
f'\left(x\right)=\lim_{\Delta x\to0}\frac{f\left(x+\Delta x\right)-f\left(x\right)}{\Delta x}
$$
```

数式を AsciiMath で書きたいときは、次のように始めの `$$` の後ろに `:a` を付けてください。

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

## Paged.js
HTML で LaTeX を模倣する場合、各ページの上や下についているヘッダーや注釈、ページナンバーをどうやって表示するのかというのが問題になります。
HTML をブラウザで印刷すると、ブラウザがページの上や下にタイトルやページ番号などを付けてくれたりしますが、大体の場合そういうのは見た目が LaTeX っぽくありません。

LaTeX は、文章がページによって分割されることを前提に作られていますが、HTML/CSS はそうではありません。ここに大きな隔たりがあるのです。

そこで登場するのが、[CSS Paged Media Module Level 3](https://www.w3.org/TR/css-page-3/) というCSSの規格です。これは、文章が紙や PDF に印刷されて、ページによって分割されたとき、どのようにコンテンツが表示されるべきかを、CSSによって詳細に記述することができるようにするための規格です。これを使えば、LaTeX のページ番号やヘッダー・フッターを HTML/CSS で再現することができます。しかし、この規格はまだ標準化団体が案として考えている段階なので、Chrome などの各ブラウザには実装されていないません。

そこで、この機能を無理やり使えるようにしてくれる Paged.js という JavaScript ライブラリを利用しました。

Paged.js は、HTML が読み込んでいるすべての CSS をパースして、Paged Media 3 の機能が使われていたら、HTML 要素を追加したり CSS を書き換えたりしてその機能を使えるようにするという、なかなかに豪快な JavaScript ライブラリです。

この記事にページ番号が付いていたり、各ページの上にタイトルが表示されているのは Paged.js のおかげです。内容を各ページに分割することも Paged.js がやっています。本当は、Web ページとして表示するときはページで分割されていない普通の Web ページとして表示して、印刷するときだけページ分けするようにしてほしいのですが、そういうことはできないみたいです。

## 問題点
Pandoc と Paged.js によって Markdown から LaTeX 風の HTML を作ることには一応成功しましたが、以下のような問題点があります。

- Chrome で印刷する時、表が複数ページにまたがると表示がおかしくなる（Paged.js のバグ）
- コードブロックが複数ページにまたがると、行番号の表示がおかしくなる（Paged.js のバグ）
- そもそも Paged.js の開発が止まっているように見える

他にもいろいろ不具合があると思います。このスクリプトを使ってレポートを書くのをおすすめしないのはそのためです。レポートのデザインを CSS で調整する必要がないのであれば、Markdown を LaTeX に変換して PDF にする方法を使ったほうが良いと思います。その方が不具合が少ないですし、LaTeXのコマンドが使えるので便利です。

とはいえ、HTML/CSS が LaTeX の代わりとして使えるかもしれないということは分かって頂けたと思います。私はそれだけで満足です。~~あの忌々しいLaTeXを駆逐するため、共に闘いましょう。~~

## 補足
- Pandoc とは、あるマークアップ言語で書かれた文章を他のマークアップ言語に変換するためのツールです。
- Homebrew は Mac のパッケージマネージャーという印象があるかもしれませんが、実はLinuxでも使えます。aptやdnf、pacmanなどのパッケージマネージャーは、コマンドを`/usr/bin/` などにインストールするため、aptとdnf一緒に使おうとしたりすると競合がおきてしまいますが、HomebrewをLinuxで使う場合コマンドは`/home/linuxbrew/.linuxbrew/bin/` のような専用のディレクトリにインストールされるので、aptなどと一緒に使っても不具合があまり起きません。(たまにコマンドのバージョンや中身が違ったりして問題が発生する場合はあるかもしれませんが、めったに起こらないと思います。) 
  
  普通、 Linux 向けにアプリを配布する場合、いろいろなディストリビューション用のパッケージをたくさん用意しないといけませんが、今回はめんどくさかったので、Homebrew にしました。
  
  こういうディストリビューションに依存しないパッケージマネージャーは他にもあって、Nixとかも有名です。
- Homebrew でインストールしたコマンドが、元からあるコマンドを隠してしまうことがあるので気をつけてください。例えば、`tex-html` は Ruby を使っているので、 `brew install nanikamado/tap/tex-html` をすると、依存としてRubyがインストールされます。Homebrew は、他のパッケージマネージャーがインストールしたコマンドのことは気にしないので、既に Ruby が別のパッケージマネージャーでインストールされていたとしても、新しくRubyをインストールします。なので、Ruby のバージョンが変わってしまったりします。これを回避したい場合は、環境変数 `PATH` の中のディレクトリの並び順を変えてください。
- Docker 使えよっていう意見があると思います。私もそう思います。でも Docker の使い方が分かりません。ごめんなさい。
- CSS Paged Media Module Level 3 を使えるようにするツールは、Paged.js の他にもいくつかあります。[それらのツールを一覧にしてまとめているサイト]((https://print-css.rocks/lessons))があって、そのサイトでは以下の 7 つが紹介されていました。

    - PDFreactor
    - Prince
    - Antenna-house
    - Weasy-print
    - Paged.js
    - Typoset.sh
    - Vivliostyle

  この中で、PDFreactor、Prince、Antenna-house、Typoset.sh はプロプライエタリです。非営利の場合は無料で利用できるものもありますが、各種パッケージマネージャーで配信されていなかったり、無料で利用する場合でもライセンスキーを取得しなければならなかったりしていろいろ面倒だったので、これらは使いませんでした。

  Vivliostyleは Running elements という機能をサポートしていないのですが、Running elements は使いたいので、これも除外です。

  Weasy-printは、上のサイトでは Running elements をサポートしていると書いてあったのですが、試してみたらクラッシュしたのでこれも除外します。(どうやら、Running elements の処理にバグがあって、特定の条件でクラッシュするようです。[github.com/Kozea/WeasyPrint/issues/1239](https://github.com/Kozea/WeasyPrint/issues/1239))

  残ったのがPaged.jsでした。
- LaTeX を $\LaTeX$ と書くか LaTeX と書くかは迷いましたが、$\LaTeX$ と書くとそこだけ浮いているような感じになってしまったので、LaTeX と書くことにしました。