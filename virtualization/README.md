###### tags: `インフラ`

# 仮想環境勉強会

## はじめに

1. Homebrewというパッケージ管理システムを使いコマンドからvirtualboxをインストール
2. virtualboxを使いCentOS8の仮想環境を構築します。

## Homebrew

### Homebrewとは
「HomebrewはMacのパッケージ管理ツールです。Homebrewを使うことで、ターミナルからコマンドを実行するだけで、パッケージのインストールやアンインストールが簡単に行えます。

例えば、Homebrewを使わずにMacにJavaをインストールしようとすると、Javaの公式サイトにアクセスし、Javaをダウンロードし、ダウンロードファイルを起動してインストーラに従ってインストールする、、、といった面倒な手順が必要になります。それがHomebrewを使うと、たった１行のコマンドを実行するだけでインストールできてしまいます。Homebrew、便利なのです。

出典：[Homebrewとは？インストール方法や使い方をわかりやすく説明します](https://reasonable-code.com/homebrew/)

### Homebrewの特徴

* 1行叩くだけでパッケージがインストールできる
* パッケージの依存関係も管理してくれる

出典：[Homebrewって何？プログラミングをするならMacのパッケージ管理システムを導入しておこう](https://www.braveryk7.com/homebrew/)
## Homebrewのインストール
[Homebrew](https://brew.sh/index_ja)のページに記載されているコマンドをターミナルなどで実行しインストール
```shell=
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

## Homebrewを使ってVirtualboxをインストール
はじめにbrewでVirtualboxをインストールできるか確認する

ターミナルを開き下記コマンドを実行
```shell=
$ brew search virtualbox
```
virtualboxがあることが確認できたら、下記コマンドでインストール
```shell=
$ brew cask install virtualbox
```

上記コマンドでエラーが出た場合
システム環境設定→セキュリティとプライバシー→一般
一般の下の方に許可があるのでクリック
そしたらもう一度インストール
```shell=
$ brew cask install virtualbox
```

インストールできたら一覧表示で確認
```shell=
$ brew cask list
```
virtualboxがあればOK

## OSの種類

- windows:大学からもらえたはず？。ゲームなどのアプリケーションの多くが対応している
- Ubuntu:Linux系(Debian)のOS、初心者に優しいOS
- centos:Linux系のOS。サーバー用
- raspbian:ラズベリーパイというマイコンの会社のOS
その他にもいっぱいあるよ

## 仮想化とは
PCなどのハードウェア上に仮想的につくられた環境
仮想環境は物理的環境に左右されない
サーバを仮想化するケースでは、一台の物理的なサーバ上に仮想的に作り上げたサーバを複数稼働させることにより、1台のサーバを複数台のサーバとして稼動させることができる
仮想環境でコストを削減できる（仮想環境の導入によって、物理的なハードウェアを複数台数用意する必要がなくなる）
複数のOSが使える
複数のOSでサービスの稼動状態を確認できる。

## virtualboxとは
macやwindowsなどで他のOSを動かす（仮想化環境を作る）アプリケーション
OSのインストールファイルさえあればmacを動かしながらwindowsなどを動かすことが出来るので例えばwindowsにしか対応していないアプリを仮想化環境を通してmacのノートパソコンで動かせる

## virtualbox起動＆イメージの作成
### virtualbox起動
アイコンをクリックして起動

![](https://i.imgur.com/43syEjz.png)


### イメージの作成
左上の新規(N)をクリック

![](https://i.imgur.com/izafud8.png)



### OSの選択
名前,タイプ,バージョンがあるので記入していく
今回は、CentOS8を使うので
名前をCentOS8（名前は任意で決めれるよ）
タイプはLinux系
バージョンはRedHad(64-bit)
![](https://i.imgur.com/ovs1EAJ.png)



### メモリの設定
CentOS8は最低限1.5GB必要なので2GBに設定します
![](https://i.imgur.com/gKc60Tg.png)

### ハードディスクの設定
#### ハードディスクの選択
仮想ハードディスクを作成するを選択

![](https://i.imgur.com/e6z5MoP.png)

#### ハードディスクのファイルタイプを選択
VDIを選択

![](https://i.imgur.com/ztTcXg5.png)

#### 物理ハードディスクの設定
可変サイズを選択

![](https://i.imgur.com/gW6DAq1.png)

#### ファイルの場所とストレージサイズの設定
OSのイメージだけで8GBあるので倍の16GBに設定

![](https://i.imgur.com/n9GYQ8B.png)

これでイメージを作成終了

CentOS8が追加されていればOK

![](https://i.imgur.com/fZm0J8K.png)


## OSのインストール＆起動
### イメージにCentOS8をセットする
左上の設定をクリック

![](https://i.imgur.com/OnulLF2.png)

設定画面が出てくるので次の順番で作業します
1. ストレージタブを選択
2. 空を選択
3. ディスクアイコンをクリック
4. CentOS8のisoファイルを選択
5. OKをクリック

![](https://i.imgur.com/kLN45In.png)
### OSインストール
起動をクリック
![](https://i.imgur.com/Kfnsxuh.png)

Install CentOS Linux8を選択

![](https://i.imgur.com/NbNGWr3.png)

#### 言語の選択
日本語を選択

![](https://i.imgur.com/wpWU6ji.png)


#### 時刻選択
時刻を東京に設定

![](https://i.imgur.com/ZzjEhop.png)

![](https://i.imgur.com/f54sw77.png)

#### ソフトウェアの選択
![](https://i.imgur.com/ts9Vqpr.png)

最小限のインストールを選択

![](https://i.imgur.com/DRo3DSm.png)


*サーバー（GUI使用）とサーバーはvirtualboxが対応していない

#### インストール先の選択
![](https://i.imgur.com/03rPC89.png)

![](https://i.imgur.com/VUOCLS6.png)

#### ネットワークの設定

![](https://i.imgur.com/hiqCBpr.png)

Ethernetをオンに変更

![](https://i.imgur.com/XofRsKY.png)
#### インストール開始
全ての準備ができたらインストール開始をクリック

![](https://i.imgur.com/q40tMiv.png)

#### ユーザーの作成
![](https://i.imgur.com/oDxbsCu.png)

rootユーザーのパスワードを設定

![](https://i.imgur.com/gS5U9ey.png)

一般ユーザーの作成
フルネーム、ユーザー名は任意に決めてください
![](https://i.imgur.com/LpZMjWZ.png)

### エラー
しばらくすると以下の画面になります
![](https://i.imgur.com/bAosCxX.png)

インストールが終了後に再度インストールされてしまうのが原因
#### エラー対処
一度CentOS8を終了
![](https://i.imgur.com/ZMof3JJ.png)

1. 設定
2. ストレージ
3. 割り当ての除去

![](https://i.imgur.com/ViVksjZ.png)

下記のようにコントローラー：IDEの中に何もないことを確認

![](https://i.imgur.com/jn3hBHl.png)

### 再度、起動する
以下の画面が出るが何も押さなくてOK
![](https://i.imgur.com/xziUkSD.png)


すると勝手に起動します

![](https://i.imgur.com/0hHH3rA.png)

今回はこれで終了です
時間があればrootや一般ユーザーでログインしてどんなファイルがあるのか調べてみよう！

## 終わりに

インフラの勉強には仮想環境が欠かせません
CentOS以外のOSをダウンロードしてvirtualboxで仮想環境を構築してみるといいと思います。

