Perlをはじめよう
===

01-01 Perlのセットアップ
---

perlbrew/cpanm編

### perlbrewで複数のインタプリターを管理

  perlbrew(App::perlbrew)をインストール
  基本的にユーザ権限で入れる

  >$ curl -kL http://install.perlbrew.pl | bash
  
  .bashrcの#source ~/perl5/perlbrew/etc/bashrc をコメントアウト
  source ~/.bashrc

  perlbrew自身をupgrade

  >$ perlbrew self-upgrade (なにかupgradeできないけど..., yusukebeさんも0.44とかでself-upgradeできないってあるのでたぶん古いperlbrewの問題ぽい)
  0.59から問題の0.60の修正版0.61にself-upgradeできた！

  インストール可能なPerlバージョン表示

  >$ perlbrew available

  インタプリターインストール
  http://blog.64p.org/entry/2013/03/13/150202
  http://blog.yappo.jp/yappo/archives/000812.html

  >$ perlbrew install perl-5.16.3 --as perl-5.16  
  $ perlbrew list

  インタプリターのスイッチ

  >$ perlbrew switch perl-5.16  
  perl -v  
  This is perl 5, version 16, subversion 3 (v5.16.3) built for darwin-2level

  インストール名をデフォルトの perl-5.16.3 などとせず、perl-5.16 と minor version を含めないようにする。

  セキュリティフィックスがでた場合の処理

  >$ perlbrew upgrade-perl

  todo: ここ以下は、Webアプリ編にいれる
  globalなsite_perlにライブラリをインストールすると、クリーンにしたい時に面倒なので、
  新しいsite_perl領域を作っておく（気持ちの問題）

  >$ perlbrew lib create basic
  $ perlbrew list  
  * perl-5.16 (5.16.3)  
  perl-5.16@basic  
  perlbrew switch perl-5.16@basic  
  perlbrew list

  DBD::mysqlはcartonと違ってインストールfailed
  mysql_configは見つけているけど、testuserが指定してないため、こけている
  cpanm --prompt DBD::mysql
  l(ook)
  perl Makefile.PL --testuser=root --testpassword=root
  make
  make test
  make install
  でいける
  テスト接続をskipしてもいい



### production環境ではperl-buildが良い

  本番環境ではperlbrew/plenvの様なシェルを利用した複数環境の切り替えは
  運用時逆に複雑になり扱いにくいので、perlインタプリタを入れやすくするツール
  perl-buildがおすすめ。

  perl-build command is FatPacker ready
  >% curl https://raw.github.com/tokuhirom/Perl-Build/master/perl-build | perl - 5.16.2 /opt/perl-5.16/  



