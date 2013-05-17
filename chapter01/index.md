Chapter01 -- Perlをはじめよう
===

01-01 Perlのセットアップ
---

### 01-01-01 複数バージョンのPerlを使用

  OSで標準で入っているPerl(system perl)は、いくつかのツールから依存しているケースもあり、
  アプリケーションを多数ホストするのは、適切でない場合があります。
  開発で利用するPerlインタプリターは、開発用に独立していた方がよいところです。
  また、複数のバージョンで動作を確認出来る事も望ましいですね。
  そこで、ここでは**plenv**を使い、Perlインタプリターを1つのOS内に複数のバージョンをインストールし、管理してみます。
 
  plenvをインストールにはいくつかの方法があります

  CPANからの場合

  >$ sudo /usr/bin/perl -MCPAN -e shell  
  install App::plenv  

  homebrewからの場合

  >$ brew install plenv

  githubからの場合(plenv開発速いし、githubから入れるのがベスト)

  >$ git clone git://github.com/tokuhirom/plenv.git ~/.plenv

  plenvのインストールが完了したら、いくつかshell(bashの例)に設定します

  >$ echo 'export PATH="$HOME/.plenv/bin:$PATH"' >> ~/.bash_profile  
  $ echo 'eval "$(plenv init -)"' >> ~/.bash_profile  
  $ exec $SHELL -l  

  インストール可能なPerlバージョンの表示

  >$ plenv available  

  Perl 5.16.3をインストール

  >$ plenv install 5.16.3 

  通常 ~/.plenv/versions/5.16.3 にインタプリターがインストールされます。  
  しかもデフォルトではPerlソースに付属しているtestが走りません  
  テスト(make test)を実行したい場合は、--test オプションを追加する

  インストールしたPerlインタプリタを削除したい場合は？

  ~/.plenv/versions以下のperlバージョンをごっそり削除するだけでいいみたいです

  globalなインタプリタを指定

  >$ plenv global 5.16.3

  localなインタプリタを指定（あるディレクトリで実行した場合）
  >$ plenv local 5.14.3  
  $ ls  
  .perl-version  

  localの場合は、**.perl-version**ファイルがそのディレクトリにできています。
  この中に、指定したPerlのバージョンが記載されています。
  localしたディレクトリで、perl -v して見てください。

  少し進んだ使い方

  asオプションで複数の同バージョンを別名で入れたい時やビルドオプションを変えたい時に使えます

  >$ plenv install 5.16.3 -Dusethreads --as=perl516i

  ithreadsはplenvでインストールした時も通常では入りません。
  インタプリタのビルド時に、ビルドオプションを渡しましょう(-Dusethreads)  
  ithreadsについては、2009年の記事ですが、tokuhiromさんの記事が有用かと思います。
  http://blog.64p.org/entry/20090629/1246239835

  インストールしたモジュールの移動
  plenvを使えば, 異なるバージョンのperl間でモジュールを移動することができます。
  例えば, あるプロダクトの開発環境をperl-5.14.4から新しくインストールしたperl-5.16.3に変更するような場合
  下記の要領で、5.16.3側にモジュールを移行できるようです。
 
  >$ plenv migrate-modules 5.14.4 5.16.3

  asオプションを使い、定期的にアップデートする

  asオプションを使って、インストールする時に、マイナーバージョンを含めないように名前付けします。
  (例: あえて、低いマイナーバージョンをインストール)
  (--as=perl-5.16とか-含めると使えない

  >$ plenv install 5.16.1 --as=perl516

  マイナーバージョンをアップグレード(バイナリ互換あり)

  >$ plenv install 5.16.3 --as=perl516 --force


  plenv 1.5.0からの追加機能(2013-05-17 released!)

  plenv install -j 4 5.18.0-RC4 -DDEBUGGING=-g がオススメです。  
  plenv install 5.18.0-RC4 -DDEBUGGING=-g -j 4 か  
  ~/.plenv/build/ が build directory になった

  なお、plenv関係の記事は、plenv作者tokuhiromさんと、papixさんの記事が大変有用です。  
  http://blog.64p.org/entry/2013/01/23/081834   
  http://blog.papix.net/entry/2013/01/21/210327  


 

### 01-01-02 cpanmでライブラリを管理

  Perlには、標準でCPANモジュールを管理するcpanコマンドが付属していますが、
  CPANに加え、githubからのインストールや、バージョン指定インストール、など
  便利な機能が満載でかつ使いやすい **cpanm(App::cpanminus)** をインストールしてライブラリを管理します。

  cpanmをplenvからインストール

  >$ plenv install-cpanm 

  cpanm自体が古くなるなど、アップグレードしたい場合

  >$ cpanm --self-upgrade

  cpanmでモジュールをバージョン指定してインストールするなども可能です  
  ( モジュール名@バージョン番号を指定する 例)
  >$ cpanm Mojolicious@3.95

  その他、cpanmの詳しい使い方は、perldoc cpanm して、ドキュメントを見てみてください。

  **下記rehashは、plenv-v1.4.0以降から不要になりました**  
  何かcpanm経由でインストールした時には、rehashして状態を認識させる必要があります。
  そうでないと、インストールしたモジュールのコマンドなどが反映されないので多少注意が必要です。 
 
  >$ plenv rehash


### 01-01-03 cpanfileでのモジュール依存管理
  
  CPANモジュールを複数インストールしたい時など、cpanfileというファイルで管理すると大変楽になります。
  cpanfileでは、モジュール名と、バージョンを記述します。

  >requires 'Mojolicious', '== 3.95';  
  requires 'Proclet', '0';

  指定したモジュールを、cpanmでインタプリターglobalにインストールします。

  >$ cpanm --installdeps .  

  cpanfileのモジュールバージョンは、下記の様な指定ができます。

  '0'       指定無し  
  '1.00'    以上  
  '== 1.00' 固定  
  '!= 1.00' 以外  
  '>= 1.00' 以上  
  '<= 1.00' 以下  
  '> 1.00'  超過  
  '< 1.00'  未満  

  cpanfileについては、下記の記事が有用です。  
  http://www.slideshare.net/aloelight/cpanfile


### 01-01-04 Cartonでライブラリのインストールをクリーンにする

  参考
  http://blog.livedoor.jp/lestrrat-practical_modern_perl/archives/25002846.html

  今までのcpanmでのモジュールインストールは、Perlインタプリターのglobal領域(site_perl)
  にインストールするものでした。
  しかし、Perlアプリケーションを多く作って行くようになると、同じモジュールを利用していても、
  個々のアプリケーションで使用するバージョンを変えたい、などのケース出てきます。
  その様な場合、**Carton**を使い、Perlインタプリターglobalではなく、アプリケーションlocalにモジュールを
  インストールすることで対応する事ができます。

  Cartonをインストール

  >$ cpanm Carton

  次に、インストールしたいモジュールをcpanfileを用いて作成します。

  >$ cat cpanfile  
  requires 'Mojolicious', '== 3.95';

  >$ carton install  
  Successfully installed Mojolicious-3.95
  1 distribution installed
  Complete! Modules were installed into local
 
  となり、local/以下に実行コマンド含め、モジュール全てがインストールされます。
  また、carton.lockというファイル, .cartonディレクトリも作成されます。
  cpanfileとcarton.lockはバージョン管理ツールで管理するようにします。

  >$ git add cpanfile carton.lock

  carton経由でMojoliciousを起動してみます

  >$ carton exec -- morbo myapp.pl

  localで動作確認できましたね！
  では、これらをバージョン管理に追加し、pushします。その後デプロイしてみましょう。

  >$ git clone mojotest  
  $ cd mojotest  
  $ carton install  
  Installing modules using carton.lock (deployment mode)  
  Successfully installed Mojolicious-3.95  
  1 distribution installed  
  Complete! Modules were installed into local  

  この様にCartonは、開発時cpanfileからインストールを行い、デプロイ時はcarton.lockからインストールを行います。

  依存モジュールをネットワークからインストールするのではなく、あらかじめバンドルしておく事もできます。
  carton bundle

  http://blog.64p.org/entry/2013/02/07/113231

  >$ carton bundle  
  Bundling modules using cpanfile  
  Mojolicious-3.95  
  Complete! Modules were bundled into /Users/masakyst/tmp/perl/cart/local/cache  

  すると、local/cacheにtarをぶっ込んでくれる
  git add local/cache
  しておく
 
  デプロイ時には、
  >$ carton install --cached

  すると、ちゃんとlocal/cacheからインストールされているのがわかる  
  tail -f ~/.cpanm/build.log
  Working on Mojolicious
  Fetching file:///



01-02 Gitのセットアップ
---

不要かな


01-03 エディタと開発に便利なツール
---

### 01-03-01 ソースコードの検索にはack

   ackというPerl製のソースコードの検索ツールがあります。(find, grepのような)
 
   ackのインストール

   >$ cpanm App::Ack

   ack2.0のメジャーバージョンアップで,1系と変わった箇所もあるので、良く確認したほうがよさそうです。


### 01-03-02 vim/emacs/sublime text2
   ろくにvim pluginも使ってないし、sublime textも使ってない状態

### 01-03-03 インタラクティブモード

   Perlにも、Rubyのirbの様なインタファクティブシェルがいくつかあります。

   * perldebugger(標準)
   * perlsh(Term::ReadLine::Gnu)  一昔前の定番だが安定している
   * re.pl(Devel::REPL) use Mooseしているのが気にならなければ
   * pirl(Shell::Perl) 複数行入力できないので微妙
   * iperl(App::REPL) ベストと思いきや、評価順序に難あり？

   などがありあます。
   個人的におすすめなのは、標準のperldebuggerです。

   >$ perl -de 1

   とすると、対話的にPerlコードが実行できます。
   .bashrcなどにaliasしておくと、それなりにいい感じになります。
   
   > alias ipl='rlwrap perl -de 1' 


### 01-03-04 perltidyでソースコードを奇麗に統一

   http://perl-users.jp/articles/advent-calendar/2009/casual/09.html

   perl hacksかperl bestpracticeにも書いてある


### 01-03-05 Padre IDE

  Perl製のPerl専用IDEがあります。

  詳しくは、charsbarさんの記事が良いです。
  http://gihyo.jp/dev/serial/01/modern-perl/0017


  cpanm Padre

  Padre requires a perl built using threads!!!!!!(-Dusethreadsしないとだめ

! Installing the dependencies failed: Module 'Debug::Client' is not installed
! Bailing out the installation for Padre-0.98. Retry with --prompt or --force.
105 distributions installed

  という事で、Debug::Clientがmacだと0.25はいらない
  最近良くアップデートされている



