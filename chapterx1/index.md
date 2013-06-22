ChapterX1 -- Perlによるサーバ監視
===

01-01 リソース監視
---

ある程度サービスを運用していくにあたって、サーバのリソースの把握/監視ができると
システムのチューニングやサーバの増強のタイミングを把握する事が可能になります。

リソース監視ツールは多々ありますが、ここではPerl製のリソース監視ツール
CloudForecastを使って進めていきます。


### 01-01-01 Cloudforecastのインストール

最低限、system perlに必要なライブラリをインストールしておきます。

(以下、CentOS6での例)

  >$ yum install -y net-snmp net-snmp-utils net-snmp-devel net-snmp-perl rrdtool-perl perl-core perl-CGI

cloudforecastのソースをgithubから取得します。

  >$ git clone https://github.com/kazeburo/cloudforecast.git  
  cd cloudforecast
   
CloudForecastの追加モジュールインストールを局所的に行うため、cpanmをインストールします

  >$ wget --no-check-certificate http://cpanmin.us  
  chmod +x ./cpanm

CloudForecast依存モジュールではありませんが、有ると便利なモジュールを入れておきます。

  >$ ./cpanm -n -l extlib JSON::XS Proclet File::RotateLogs

CloudForecastの依存モジュールをインストールします。

  >$ ./cpanm -n -l extlib --installdeps .

データ保存ディレクトリ、ログ保存ディレクトリを作成します。

  >$ mkdir -p data logs

設定ファイルをrenameして、サーバ環境に合わせ書き換えます。

  >$ mv cloudforecast_sample.yaml cloudforecast.yaml  
  mv server_list_sample.yaml server_list.yaml

cloudforecast.yamlは全体の設定、server_list.yamlは監視対象のサーバの設定です。


### 01-01-02 SNMPの設定

SNMPとは、DARPAモデルに準じたIP ネットワーク上のネットワーク機器を監視（モニタリング）・制御するための情報の通信方法を定めるプロトコルです。(Wikipedia抜粋)

Cloudforecastは、基本的な部分は、SNMPを経由して監視対象の状態を取得しています。
その為、Cloudforecast側(マネージャ)と、監視対象側(エージェント)にSNMPの設定を正しく行う必要があります。

ここでは、CloudForecastをインストールしたマネージャ兼エージェント（自分を監視）として設定してみます。

SNMPのconfファイルを書き換えます。

/etc/snmp/snmpd.conf

  >com2sec local localhost public  
group local_grp v2c local  
view    all    included   .1   80    
access local_grp "" any noauth exact all none none  
syslocation Unknown (edit /etc/snmp/snmpd.conf)  
syscontact Root <root@localhost> (configure /etc/snmp/snmp.local.conf)  
dontLogTCPWrappersConnects yes   
disk / 10000  

デフォルトのsnmpd.confはコメントも多く見づらいので、こんな感じに最小限に書き換えてしまいます。

(要つっこみ！！)

SNMPDを再起動します。

  >$ service snmpd restart

snmpwalkを使用して、データが取得できるか確認してみます。

  >$ snmpwalk -c public -v 2c localhost

ずらーっとデータが表示されれば大体成功です



### 01-01-03 CloudForecastの起動

CloudForecastはシンプルな構成で、データ取得用巡回デーモンと、グラフ閲覧HTTPDを2つ走らせる必要があります。

データ取得用巡回デーモンの起動

  >$ CF_DEBUG=1 ./cloudforecast_radar -c cloudforecast.yaml -l server_list.yaml  

グラフ閲覧HTTPDの起動

  >$ CF_DEBUG=1 ./cloudforecast_web -p 5000 -c cloudforecast.yaml -l server_list.yaml

CF_DEBUG=1 を付けると、詳細なデバッグログで表示されます。
必要に付き、付けてみると便利です。

巡回デーモンは、起動後5分感覚で監視対象エージェントにデータを取得しにいきます。
グラフ閲覧HTTPDをポート5000番で起ち上げていますので、ブラウザからCloudForecastにアクセスできるようになっています。
グラフデータが表示されるようになるまで しばらく待ってみましょう。



01-02 死活監視
---

CloudForecastでは、死活監視を行い、監視対象がダウンした時に通知するという機能は今のところ無いようです。
そこで、ここではPerl製のシンプルな死活監視用ツール App::MadEyeを使い、簡単に死活監視を行ってみます。




01-03 閾値アラート
---

Amazon CloudWatchを使う
