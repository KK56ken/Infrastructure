
###### tags: `インフラ`

# SSH

## はじめに
VirtualboxのCentOS8に公開鍵でSSH接続する
## SSHとは
SSH(Secure SHell)は、ユーザ認証および通信データの内容を暗号化することにより、安全な（セキュア）通信を行うソフトウェア。

過去にリモートログインには、Telnetが使われていたこともありますがこれは暗号化されておらず平文で通信してしまうためセキュリティ上に問題があり、安全な通信を行うツールとしてsshが広く使われるようになった。

sshには商用のものとフリーのものがあります。CentOSには、オープンソース版のSSHである[OpenSSH](http://www.unixuser.org/~euske/doc/openssh/book/chap3.html)が搭載されている。


## 共通鍵暗号方式と公開鍵暗号方式
[共通鍵暗号と公開鍵暗号の解説とSSHでの認証手順](https://www.adminweb.jp/web-service/ssh/index4.html)

## sshを使用した通信の手順
1　ホスト認証 -> 2 ユーザ認証 -> 3 暗号化した通信

1. ホスト認証ではsshクライアントからsshサーバに接続すると、ユーザ認証の前に、まず接続先のホストが正しいかどうかを判断する「ホスト認証」が行われる。
2. ユーザ認証は、従来のユーザ名とパスワードによる「パスワード認証」or「公開鍵暗号方式」で行われるパスワード認証でも通信は暗号化されているため、アカウント情報が盗聴されて解読される可能性は低くい。
3. ユーザ認証が完了すると、それ以降の通信は全て共通鍵暗号方式で暗号化されて行われる。共通鍵暗号方式が使われるのは通信速度が早くなるから。

## 公開鍵暗号方式を用いたSSHの仕組み
[SSHの公開鍵認証における良くある誤解の話](https://qiita.com/angel_p_57/items/2e3f3f8661de32a0d432)
## sshの用途
* リモートログイン
	* アカウント情報や通信データを全て暗号化した安全なリモートログインが行える。
* リモートプログラムの実行
	* リモートコンピュータのプログラムを実行し、結果をローカルコンピュータ上に表示することができる。
* ファイル転送
	* scp,sftpというsshの機能を使用することで、リモートコンピュータとの間で、安全なファイル転送が行える。
* sshファイルシステム
	* sshファイルシステムは、sshを利用してリモートファイルシステムをローカルファイルシステム上にマウントすることを可能にするソフトウェア。従来のNFSに比べてより安全なネットワークのファイル共有が可能
* TCP/IPポートフォワーディング
	* 任意のポートをSSH経由で暗号化して転送することができる。「Xフォワーディング」という機能を使用してリモートのXアプリケーションをローカルで操作することも可能


## ゲストOS（CentOS8）にプライベートIPアドレスを割り振る
### virtualbox側のネットワーク設定
Vituralboxを起動しツールバーのファイル→ホストネットワークマネージャーを選択

![](https://i.imgur.com/iTVW1BK.png)

作成を押し（ネットワークアドレスを増やす）
![](https://i.imgur.com/njFKfv2.png)

新しく作成されているのを確認
![](https://i.imgur.com/UNbXh2g.png)
チェックボックスを押し有効にする
![](https://i.imgur.com/Pbu0QEu.png)


### VirtualMachine(ゲストOS)側のネットワーク設定

![](https://i.imgur.com/YEBcHlV.png)

![](https://i.imgur.com/NiVhSEN.png)

![](https://i.imgur.com/eeEZ0gm.png)

以上でネットワークの設定は終了


### ipが割り振られているか確認
CentOS8を起動し一般ユーザーでログイン
```shell
$ ip a
```
以上のコマンドでしっかりとIPアドレスが割り振られていることを確認する

## パスワード認証でssh接続する
クライアント（Mac）からアクセスするのでMACのターミナルでの作業です
IPアドレスを割り当てたので以下のコマンドでアクセスできるか確認する
```shell=
$ ssh ユーザ名@IPアドレス
```
作者の場合
```shell=
$ ssh ken@192.168.59.3
```
上記のコマンドを打つと正しいホストか聞いてくるのでyesを入力
次にユーザーのpasswordを入力するとログインできます

## 公開鍵認証でssh接続させるため公開鍵と秘密鍵を作成する
クライアント（MAC）の作業です
クライアントに公開鍵、秘密鍵を作成する
```shell=
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -f CentOS8_id_rsa
```
-tで暗号化形式を指定
-bで作成するbit数をしてする(長ければ長いだけ安全)
-fはファイル名の指定
以上のコマンドを入力するとパスワードを求められます。
設定しない場合はエンターキーを二回押す

公開鍵と秘密鍵が作成されているか以下のコマンドで確認
```shell=
$ ls
```
CentOS8_id_rsa　←　秘密鍵
CentOS8_id_rsa.pub　←公開鍵　
上記二つあればOK

## クライアントにキーペアを登録
```shell=
$ ssh-add -K ~/.ssh/CentOS8_id_rsa ←鍵の登録
```
## ゲストOS（CentOS8）のキーボード配列をUSに変更する
ホストOSのキーボードがUS配列、ゲストOSはJP配列なので本来入力したい文字とは、違う文字を入力されてしまう。なので、ゲストOSもUS配列に変更する。

一時的に変更する場合は以下のコマンドを実行すると反映されます。（再起動後は無効となります）
```shell
$ loadkeys us
```
再起動後も有効にする場合にはlocatectlコマンドで以下の設定を行ってください。 /etc/vconsole.confのKEYMAPに保存されます。
永続的な反映は下記のコマンド
```shell
$ localectl set-keymap us
```
今回は永続的変更をしましょう
## 鍵をサーバーに登録
```shell=
$ ssh-copy-id -i ~/.ssh/CentOS8_id_rsa.pub ユーザー名@IPアドレス
```
以下はCentOS8（リモートサーバ）で作業
```shell=
$ sudo vi /etc/ssh/sshd_config
```

```
PasswordAuthentication  no　←パスワードの認証を拒否する
ChallengeResponseAuthentication  no　　←パスワード認証をしてしまう可能性があるため否定
UsePAM  yes
```

## ゲストOS(CentOS8)のsshdを再起動
```shell
$ systemctl restart sshd
```


## 公開鍵でSSH接続
```shell=
$ ssh -i ~/.ssh/CentOS8_id_rsa ユーザー名@IPアドレス
```
これでssh接続できれば成功です

しかし毎回このコマンドを打つのは長いので省略します

## configファイルの作成

```shell
$ cd ~/.ssh
$ vim config
```
以下を記載する
```
Host CentOS8
 HostName IPアドレス
 Port 22
 User ユーザー名
 IdentityFile /Users/[Username]/.ssh/CentOS8_id_rsa
 IdentitiesOnly yes
 TCPKeepAlive yes
```
IPアドレスとユーザー名は各々変更してください
```shell
$ ssh CentOS8
```
上記のコマンドでアクセスできればOK

## セキュリティを向上させる(時間があれば)
### ポート変更
22番ポートを使っているとそのポートに[ブルートフォース攻撃](https://ja.wikipedia.org/wiki/%E7%B7%8F%E5%BD%93%E3%81%9F%E3%82%8A%E6%94%BB%E6%92%83)などで攻撃を受け接続されてしまう危険性があるので22番ポートは使わないようにしましょう




