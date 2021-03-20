###### tags: `インフラ`

# Sambaを使ってファイルサーバー構築
[TOC]
## はじめに

## Sambaとは
samba(サンバ)は、Windowsネットワークのファイルサーバ機能およびプリントサーバ機能をLinuxなどUNIX系OS上に実装したサーバソフトウェアです。名前は、Windowsのファイル共有プロトコルであるSMB(Server Message Block)に由来しています。オープンソースとして公開されている。
Macの場合、かつてはAppleShareという独自のファイル共有機能が標準で下が、現在ではSMBによるファイル共有が標準となっています。そのためMacからSambaで公開されているファイルシステムをマウントできます。

## 実際に構築していく
### Sambaのインストール
```shell=
$ sudo dnf -y install samba
```
以上で、次の3つのRPMパッケージがインストールされます。
|  パッケージ　| 説明 |
| -------- | -------- |
| samba   | Sambaのメインパッケージ    | 
| samba-common-tools   | Sambaのユーティリティ    | 
| samba-libs   | Sambaのライブラリ    | 



### Sambaに使われるサービス
Sambaサーバには、次の二つのsystemdサービスが必要です。
| サービス | 説明 |
| -------- | -------- |
| smb.service   | ファイル共有機能、プリンタ旧友機能を提供するサービス     | 
| nmb.service   | NetBIOSプロトコルの名前解決を行うサービス    | 

以上のサービスを起動する必要があるので以下のコマンドを実行
```shell=
$ sudo systemctl enable smb --now
```
```shell=
$ sudo systemctl enable nmb --now
```
### Firewallの設定でSambaを許可する
firewallのpublic zoneにsambaを追加する
```shell=
$ sudo firewall-cmd --permanent --zone=public --add-service=samba
```
上記で設定できたのでfirewallを再起動する
```shell=
$ sudo firewall-cmd --reload
```

### Sambaのユーザーを作成する
Sambaサーバでは、Linuxに登録されているアカウントとは別に、独自にユーザ管理を行っています。そのため、あらかじめSambaサーバにログインできるユーザーを登録しておく必要があります。それにはpdbeditコマンドを使用します。
＃1
```shell=
$ sudo pdbedit -a ユーザー名　
```
パスワードを求められるので新たに作ってください

### SELinuxのブーリアン値の設定
SELinuxを有効にしている場合、デフォルトでは、Sambaサーバからユーザーのホームディレクトリへのアクセスが許可されていません。
次のコマンドでブーリアン値「samba_enable_home_dirs」をオンにします。
＃1
```shell=
$ sudo setsebool -P samba_enable_home_dirs on
```
```shell=
$ sudo getsebool samba_enable_home_dirs
```

### macからアクセスする

#### macからアクセス
Finderの上部メニューから「移動」→「サーバへ接続」をクリック

サーバアドレスを入力する
```
smb://virtulboxのcentOS8のIPアドレス/先ほど作ったユーザー名
```
virtulboxのcentOS8のIPは以下で確認できる
```shell=
$ ip a 
```


