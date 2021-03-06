# Splunk-COVID19-Japan

COVID19 Japan Dashboard for Splunk

[Coronavirus Disease (COVID-19) Japan Tracker](https://covid19japan.com/) と [特設サイト 新型コロナウイルス 都道府県別の感染者数・感染者マップ｜NHK](https://www3.nhk.or.jp/news/special/coronavirus/data/) のデータを利用して Splunk のダッシュボードを作成しています。



## 対象プラットフォームと Requirements

* Linux のみを対象としています。
* シェルスクリプトを使用していますので、Windows ではデータの取得ができません。
* JSON データの取得には、wget コマンドを使用しています。



## インストール

`covid19-japan.spl` をインストールするか、`covid19-japan/` ディレクトリを `$SPLUNK_HOME/etc/apps/covid19-japan/` としてコピーしてください。

### I. (必須-選択) .spl ファイルによるインストール

* Web UI からのインストール
	1. ログインします。
	2. 「App の管理]-[ファイルから App をインストール]  
		ファイルを選択し、[アップロード]でファイルをアップロードします。

* CUI からのインストール
	1. `$SPLUNK_HOME/bin/splunk install app covid19-japan.spl`  
		ユーザ認証が必要になります。

### I. (必須-選択) ディレクトリのコピーによるインストール

1. (管理者権限などで) cp -r covid19-japan/ $SPLUNK_HOME/etc/apps/

### II. (必須-共通) セットアップの実行

セットアップページは用意していませんので、コマンドラインで `setupSplunkDir.sh` を実行します。

1. `cd $SPLUNK_HOME/etc/apps/covid19-japan/`
2. `sudo bin/setupSplunkDir.sh`
3. debug/refresh で再読込するか、Splunk を再起動します。


### III. (オプション-共通) Alternative ページの利用

"[Coronavirus Disease (COVID-19) Japan Tracker](https://covid19japan.com/)" のデータは、JSON データを取得してダッシュボードを構成していますが、代替用に Web スクレイピングによるデータのダッシュボードを用意しています。

このダッシュボード (COVID19 Japan Alternative) を利用する場合には、スクリプトによるデータ入力を有効にします。

1. `cd $SPLUNK_HOME/etc/apps/covid19-japan/lib`
2. (書き込み権限のあるユーザで) `pip3 -r requirements.txt -t .`
3. [設定]-[データ入力]-[スクリプト]  
	`$SPLUNK_HOME/etc/apps/covid19-japan/bin/updateCovid19JapanCSV.sh` を「有効」にします。

## ダッシュボード情報

1. COVID19 Japan
	1. [Coronavirus Disease (COVID-19) Japan Tracker](https://covid19japan.com/) をもとにした、日本全国のダッシュボード
2. COVID19 Japan Analysis  
	1. 死亡率、回復率、男女比、年代比などの分析ダッシュボード
3. COVID19 Japan (specific prefecture)  
	1. 各都道府県(選択) の個別ダッシュボード
4. COVID19 Japan NHK  
	1. [特設サイト 新型コロナウイルス 都道府県別の感染者数・感染者マップ｜NHK](https://www3.nhk.or.jp/news/special/coronavirus/data/) の非公式データを利用したダッシュボード



## 制限/注意事項

1. 取得した JSON データと CSV データは、取得日時をファイル名に付加して `/var/local/COVID19/Japan/{csv_source,json_source,NHK}` ディレクトリ下に保存しています。  
	Splunk で読み込んだ後は不要になりますので、適宜、削除あるいはアーカイブしてください。(logrotate の設定は用意していません。)

2. セットアップページは用意していません。必ず `setupSplunkDir.sh` を実行してください。

3. JSON データをそのまま読み込んで Splunk で加工しています。  
	その際、JSON の構造により、メモリの消費量が大きくなるため、SavedSearch (アラート) で CSV ファイルに加工しています。




## データソース

### COVID19 感染者データ

以下の二次データを利用しています。

* [Coronavirus Disease (COVID-19) Japan Tracker](https://covid19japan.com/)
	* [reustle/covid19japan-data - GitHub](https://github.com/reustle/covid19japan-data/)
		* [Full merged list of patient data for all of Japan.](https://data.covid19japan.com/patient_data/latest.json)
		* [Daily summary and Per-prefecture summary.](https://data.covid19japan.com/summary/latest.json)
		* [Tokyo per-ward/city summary.](https://data.covid19japan.com/tokyo/counts.json)
* [特設サイト 新型コロナウイルス 都道府県別の感染者数・感染者マップ｜NHK](https://www3.nhk.or.jp/news/special/coronavirus/data/)
	* [日本国内の感染確認者数の推移 累計値 (JSON)](https://www3.nhk.or.jp/news/special/coronavirus/data/47patients-data.json)
	* [日本国内の感染確認者数の推移 １日ごとの発表数 (JSON)](https://www3.nhk.or.jp/news/special/coronavirus/data/47newpatients-data.json)

一次データについては、各サイトのページをご覧ください。

### マップデータ

* [NipponMap](https://cran.r-project.org/web/packages/NipponMap/index.html)
* [地理院タイル](https://maps.gsi.go.jp/development/ichiran.html)

### 緯度経度データ

* [位置参照情報ダウンロードサービス(GISホームページ) (国土交通省国土政策局国土情報課)](http://nlftp.mlit.go.jp/isj/index.html)
* [Google マップ](https://www.google.co.jp/maps/)

### 地方自治体データ

* [総務省｜電子自治体｜全国地方公共団体コード](https://www.soumu.go.jp/denshijiti/code.html)
* [J-LIS 地方公共団体コード住所](https://www.j-lis.go.jp/spd/code-address/jititai-code.html)
* [市区町村名・コード | 政府統計の総合窓口](https://www.e-stat.go.jp/municipalities/cities)
* [住民基本台帳に基づく人口、人口動態及び世帯数調査 調査の結果 年次 2019年 | ファイル | 統計データを探す | 政府統計の総合窓口](https://www.e-stat.go.jp/stat-search/files?page=1&layout=datalist&toukei=00200241&tstat=000001039591&cycle=7&year=20190&month=0&tclass1=000001039601&result_back=1)

これらの地方自治体データと緯度経度データを統合して利用しています。

* LocalGovernmentCode.csv  
	地方自治体コード(都道府県名、市区町村名、それぞれ、「漢字」「ｶﾅ」「カナ」「かな」の変換用)
* latlongJapan.csv  
	緯度経度データ (大字町丁目コード、緯度、経度)
* PrefecturesList.csv  
	都道府県データ (人口等を含む)
* CapitalJapan.csv  
	都道府県データ (都道府県庁所在地住所、緯度、経度、Googleマップ 緯度、経度)

今回のダッシュボード作成では全ては利用していませんが、有用なので残してあります。


## 参考

* [「東京都 新型コロナウイルス感染症対策サイト」から派生した各ページとデータソースの状況 - Qiita](https://qiita.com/msi/items/fad800061808cc92060a)
