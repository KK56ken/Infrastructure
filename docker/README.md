
###### tags: `インフラ`　`Docker`　

# Dockerfile and Dockercompose.yml editing(docker)

## Last review
[Docker勉強会](https://www.canva.com/design/DAED6dNQnj0/dtsCXpG-yqhdZT_sRvrsTw/view#1)

## Today's goals
- [x] Dockerfileの書き方を理解する
- [x] Dockerfileを書けるようにする
- [x] Dockercompose.ymlの書き方を理解する
- [x] Dockercompose.ymlを書けるようにする

## What is dockerfile?
Docker上で動作させるコンテナの構成情報を記述するためのファイルです。
下のNo dockerfileを見ればわかる通り、コマンドで設定やインストールなどしなければいけないことを自動化してくれるfile

## How to write a dockerfile?
[世界一わかりみが深いコンテナ & Docker入門 〜 その3:Dockerfileってなに？ 〜](https://tech-lab.sios.jp/archives/19191)


## Setting up apache with docker
### No dockerfile
```shell=
docker run -d -it -p 8080:80 --name testweb centos:centos7
```
コンテナに入る
```shell=
docker exec -it testweb /bin/bash
```
Apacheインストール
```shell=
yum install httpd
```
phpインストール
```shell=
yum install php
```
Apache起動
```shell=
/usr/sbin/httpd
```
コンテナから抜ける
```shell=
exit
```
PHPファイル作成
test.php
```php=
<?php
  echo "hoge";
?>
```
test.phpのファイルをコンテナにコピー
```shell=
docker cp test.php testweb:/var/www/html
```
以下のURLでtest.phpが表示されることを確認する
http://localhost:8080/test.php

### With dockerfile
```dockerfile=
FROM centos:centos7　
  
RUN yum -y install httpd php
 
COPY test.php /var/www/html/
 
CMD ["/usr/sbin/httpd","-DFOREGROUND"]
```

### Dockerfile build and run
先ほど作成したdockerfileをbuildして、dockerイメージを作成する。

```shell=
$ docker build -t testphpimg .
```
-t オプションはtag付けするコマンド（今回はtestphpimgという名前をつけている）
※ 最後の.は現在のディレクトリのdockerfileをbuildすることを意味してる

作成したイメージをrunして、コンテナを作成
```shell=
$ docker run -d -p 8080:80 --name testweb testphpimg:latest
```

-p オプションでポートフォワーディングしている（MAC側のポート8080とコンテナ側の80ポートを繋げている）
そのため、mac上でlocalhostの8080にアクセスするとコンテナ側の80番ポートで起動しているはずのapache画面が表示される。
--name オプションはコンテナ名をtestwebに設定している
testphpimg:latestは先ほど作成したimageを指定している

## Hands-on
nginxのdockerイメージを作成。
その後、コンテナを作成し起動させる。
localhost8080にアクセスしてindex.htmlを表示させる。

1. dockerfileを作成
3. default.confの作成
4. appディレクトリの作成
5. appディレクトリ配下にindex.htmlを作成する
6. docker build ~~~
7. docker run ~~~

### dockerfile作成手順
1. FROMでOS指定 
今回はalpine　最新バージョンにしたい場合はOS:latest
1. RUNでnginxをインストール
2. ADDを使って設定ファイルをコンテナと同期する
3. EXPOSEを使ってポート開放
4. バックグラウンドで動かす
バックグラウンドで動作させる設定
```dockerfile=
RUN mkdir -p /run/nginx

CMD nginx -g "daemon off;"
```

### dockerfileの注意点
今回の場合は、OSはalpineを使っている
alpineはyumではなく、apk addでインストールできる
appはコンテナの/appと同期させる
default.confもコンテナの/etc/nginx/conf.d/default.confと同期させる

### index.html
```htmlembedded=
<html>
  <head> </head>
  <body>
    <p>Hello, World!</p>
  </body>
</html>
```
### default.conf
```nginx=
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /app;

        location / {
        }
}
```

## What is dockercompose?
Docker compose とは、複数のコンテナから成るサービスを構築・実行する手順を自動的にし、管理を容易にする機能
[docker-compose公式ドキュメント](https://docs.docker.jp/compose/toc.html)
[Docker compose ことはじめハンズオン](https://qiita.com/TsutomuNakamura/items/7e90e5efb36601c5bc8a)

## How to write a docker-compose.
[composeファイルリファレンス](https://docs.docker.jp/compose/compose-file.html)
### phpとmysqlの場合の設定
```dockerfile=
version: "3"

services:
  web:
    container_name: php #コンテナの名前を指定する  https://docs.docker.jp/compose/compose-file.html#build
    build:
      context: ./docker #コンテクスト（訳者注：内容物の意味）には Dockerfile があるディレクトリのパスや Git リポジトリの URL を指定します。
      dockerfile: dockerfile #Compose は構築時に別のファイルを使えます。構築時のパスも指定する必要があります。
    tty: true #docker-compose upしたコンテナを起動させ続ける。
    volumes: #dockerfileのCOPYと同じ　ただし、buildしなくても変更されたfileを検知し、コンテナと同期できる。
      - ./app:/var/www/html #https://www.linuxteacher.com/docker-add-vs-copy-vs-volume/
    ports: #ポートフォワーディングの設定　
      - "8080:80"
  db:
    container_name: mysql
    image: mysql #docker hubにあるイメージを取得してきている
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test
      MYSQL_USER: user
      MYSQL_PASSWORD: user
      TZ: "Asia/Tokyo"
    volumes:
      - ./db/data:/var/lib/mysql
      - ./db/my.cnf:/etc/mysql/conf.d/my.cnf
      - ./db/sql:/docker-entrypoint-initdb.d
    ports:
      - 33306:3306
    # コンテナに入らずmysqlにアクセスする方法 mysql --host 127.0.0.1 --port 33306 -u root -p
```
#### version
```
version: "3"
```
docker-composeのバージョンを設定できる
#### services
```
services:
  web:
  db:
```
いくつのサービス（コンテナ）を立てるのか決め、名前をつけることができる
#### container_name
```
container_name: php
```
コンテナの名前を指定する
[Compose ファイル・リファレンス](https://docs.docker.jp/compose/compose-file.html#build)
#### build
```
 build:
      context: ./docker 
      dockerfile: dockerfile
```
##### context
コンテクスト（訳者注：内容物の意味）には Dockerfile があるディレクトリのパスや Git リポジトリの URL を指定します。
##### dockerfile
Compose は構築時に別のファイルを使えます。構築時のパスも指定する必要があります。
#### tty
```
tty: true
```
trueに設定するとdocker-compose upしたコンテナを起動させ続けることができる。
#### volumes
```
volumes: 
    - ./app:/var/www/html
```
dockerfileのCOPYと同じ　ただし、buildしなくても変更されたfileを検知し、コンテナと同期できる。
[Docker ADD vs COPY vs VOLUME– [2020]](https://www.linuxteacher.com/docker-add-vs-copy-vs-volume/)
#### ports
```
ports: 
    - "8080:80"
```
ポートフォワーディングの設定
#### image(mysql)
```
image: mysql
```
docker hubにあるイメージを取得してきている
#### environment(mysql)
```
environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test
      MYSQL_USER: user
      MYSQL_PASSWORD: user
      TZ: "Asia/Tokyo"
```
mysqlの設定を記載
#### volumes(mysql)
```
volumes:
      - ./db/data:/var/lib/mysql
      - ./db/my.cnf:/etc/mysql/conf.d/my.cnf
      - ./db/sql:/docker-entrypoint-initdb.d
```
２行目でコンテナにあるデータベースのデータをローカルにマウントする（つまり、データを永続化できる）
３行目でmysqlの設定を共有している
４行目で初期データベースの設定をしている

#### ports(mysql)
```
ports:
      - 33306:3306
```
### docker-composeだけどdockerfileはあるよ
```dockerfile=
FROM php:7.2-apache
RUN apt-get update && \
    # PHPのExtensionをインストール．
    # mysqliは拡張モジュール PHPで簡単にDBにアクセスできるようにしている（クエリでアクセスできる）
    # mbstringは日本語対応させている
    docker-php-ext-install mysqli mbstring

WORKDIR /var/www/html
    #コンテナに入った時のディレクトリ

EXPOSE 80
    #明示的にどのポートが開放されているかわかるようにするため（この記述がなくてもポート開放できる模様）
```
## List of dockerfile commands
[Dockerfileによるビルド](http://www.tohoho-web.com/docker/dockerfile.html)
