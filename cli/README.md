###### tags: `インフラ`

# CLI(コマンドラインインターフェース)に慣れる勉強会

## はじめに
今回は前回のインフラ班の勉強会で作成したVirutalboxのゲストOSを使いコマンドラインの勉強したいと思います。

1. vboxを起動
1. 仮想環境勉強会で作成したcentOS8を起動


## 設定
### ゲストOS（CentOS8）のキーボード配列をUSに変更する
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

### 一般ユーザーの場合sudo権限を付与する
#### 一般ユーザーをwheel groupに追加

一般ユーザーでログインしている場合は下記コマンドでrootユーザーに変更

```shell
$ su -
```

パスワードを求められるのでrootのパスワードを入力

下記のコマンドでsudo権限のあるwheelグループに追加する
```shell
$ usermod -aG wheel ユーザー名
```
一般ユーザーに戻る
```shell
$ su - ユーザー名
```
```shell
$ groups
```
wheelが存在すればOK
ない場合は一度再起動してもう一度上記コマンドを打ってみよう

### OSを最新の状態にする

パッケージの最新のものにするため下記コマンドを実行
```shell
$ sudo dnf -y update
$ sudo dnf -y upgrade
```

新しく仮想環境を作るとき、必ずOSを最新の状態にしましょう。

## いろいろなコマンドを使ってみよう
以下の機能を使うと楽にコマンド操作できます
* コマンドはTabキーで補完できる
* コマンド履歴は矢印キー上で再度呼び出すことができる

### echo
echoは文字の出力するコマンド
```shell
$ echo "hello,world"
```
[Linuxコマンド集 - 【 echo 】 引数に与えられた文字列を表示する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230751/)

こんな使い方もできる
ファイルを作成するコマンドはtouchがあるが、echoでも作ることができます
```shell
$ echo "hello world" > hello_world.txt
```
### pwd
pwdは現在のディレクトリを表示するコマンド
```shell
$ pwd
```
[Linuxコマンド集 - 【 pwd 】 現在のディレクトリの場所を確認する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230864/?rt=nocnt)
### history
historyは今までに入力したコマンド履歴を表示するコマンド
```shell
$ history
```
[Linuxコマンド集 - 【 history 】 コマンドの実行履歴を表示する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230795/)

### ls
lsは今いるディレクトリのファイル一覧を表示するコマンド
```shell
$ ls
```
[Linuxコマンド集 - 【 ls 】 ファイルやディレクトリの情報を表示する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230820/)
### touch
touchはファイルの作成するコマンド
```shell
$ touch hello_world.txt
```
[touchコマンドについて詳しくまとめました 【Linuxコマンド集】](https://eng-entrance.com/linux-command-touch)

### vi
viはファイルを編集するコマンド
```shell
$ vi hello_world.txt
```
hello_world.txtのファイルに下記を記載しEscキーを押し、:wqで終了
```
hello_world
```

viはEscキーを押した後、入力するコマンドによって処理が変わる
詳しくは[ｖｉエディタの使い方](https://prev.net-newbie.com/linux/commands/vi.html)参照
| 　コマンド | 説明 |
| -------- | -------- | 
| :q   | セーブせずに終了    | 
| :q!    | 	変更した行もセーブせずに終了   | 
| :w    | 	セーブするが終了しない。    | 
| :wq    | 		セーブして終了    | 

### cat
catはファイルの中身を表示するコマンド
```shell
$ cat hello_world.txt
```
lessコマンドでもcatと同じようにファイルの中身をみることができる

[Linuxコマンド集 - 【 cat 】 ファイルを連結して標準出力に出力する：ITpro](http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230725/)
### less
```shell
$ less hello_world.txt
```
終了するときは``` q ```を押す
[【 less 】コマンド（基本編）――メッセージやテキストファイルを1画面ずつ表示する](https://www.atmarkit.co.jp/ait/articles/1702/09/news031.html)

### cp
cpはファイルのコピーをするコマンド
```shell
$ cp hello_world.txt copy.txt
```
[Linuxコマンド集 - 【 cp 】 ファイルやディレクトリをコピーする：ITpro](http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230734/)
### mv
mvはファイルの名前を変更するコマンド
```shell
$ mv copy.txt new.txt
```
[Linuxコマンド集 - 【 mv 】 ファイルやディレクトリの移動・名前の変更をする：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230844/)
### rm
rmはファイル、ディレクトリを削除するコマンド
```shell
$ rm new.txt
```
[Linuxコマンド集 - 【 rm 】 ファイルやディレクトリを削除する：ITpro](http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230871/)
### mkdir
mkdirはディレクトリを作成するコマンド
```shell
$ mkdir test
```
[Linuxコマンド集 - 【 mkdir 】 ディレクトリを作成する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230831/)
### cd
cdはディレクトリを移動するコマンド
```shell
$ cd test
```
pwdで確認してみよう

一つ上のディレクトリに移動するには
```shell
$ cd ..
```
[Linuxコマンド集 - 【 cd 】 ディレクトリを移動する：ITpro](
http://itpro.nikkeibp.co.jp/article/COLUMN/20060227/230726/)
#### ついでに覚えておこう

##### ディレクトリの記述方法
/ ディレクトリ名を区切る or rootディレクトリ
.. 上位ディレクトリ
. カレントディレクトリ

##### 絶対パス：ルートディレクトリを起点とした記述
ディレクトリの指定
```/hoem/centos/sysken```
ファイルの指定
```/hoem/centos/sysken/set.txt```
##### 相対パス：カレントディレクトリを起点とした記述
ディレクトリの指定
```sysken/test```
ファイルの指定
```sysken/test/set.txt```
### find
findは、ファイル名や更新日付などの情報を元にして、ファイルやディレクトリを検索するコマンド
```shell
$ find sysken
```
[【 find 】 ファイルやディレクトリを検索する | 日経 xTECH（クロステック）](
https://tech.nikkeibp.co.jp/it/article/COLUMN/20060227/230777/)

### grep
grepは、ファイルの中のテキストを検索したり、特定のキーワードを含んだファイルを見つけたりするコマンド。
[grepコマンドの詳細まとめました【Linuxコマンド集】](
https://eng-entrance.com/linux-command-grep)


```shell
$ grep "hello" hello_world.txt 
```

### パイプを使い二つのコマンドを同時に実行してみよう
```shell
$ dnf list installed | less
```
上記コマンドは取得しているパッケージを表示するコマンドにlessコマンドを追加することで1ページづつ表示できるようにしています

## alias
```shell
$ alias hello='echo "hello world"'
hello
```

以下のようにコマンドを短くすることができます
```shell
$ alias c='cd' 
```
cdのように短いコマンドならそのままでいいのですが、後々長いコマンドを日常的に使うようになるのでその時使って楽しよう

### 注意点
コマンドラインからの設定だと一度shellを落とすと設定が消えてしまいます
永続化させるには```.bashrc```の設定を変更しよう


## コマンド練習問題

### 練習問題1

1. echoを使ってI_love_CLIと表示してください

### 練習問題2

1. ユーザーディレクトリの隠しファイルを表示してください
ヒント：オプションコマンド

### 練習問題3
1. ファイル：sysken.txt
   テキスト：hello_sysken
   というファイルを作成
2. ファイルの中身を表示
3. sysken.txtをhello.txtに名前を変更
4. hello.txtを削除

### 練習問題4
1. dirディレクトリを作成
1. dirディレクトリのコピーnewdirを作成
1. newdirディレクトリに移動
1. 一つ上のディレクトリに移動

### 練習問題5
1. newdirディレクトリを削除してください



## C言語のファイルをコンパイルできる環境を作る

Cで書かれたファイルを実行するにはコンパイルしなければいけません
Cのファイルをコンパイルするには[GCC(GNU Compiler Collection)](https://www.sophia-it.com/content/GCC)を使用します


### gccインストール

一応、すでにインストールされているか確認
```shell
$ which gcc
```
no gcc inと出力されたら以下のコマンドを入力していきましょう
（gccはないですよって意味です）

下記のコマンドでgccの情報を見ることができます

```shell
$ dnf info gcc
```

下記のコマンドでgccのインストール
```shell
$ sudo dnf -y install gcc
```

## cファイルの作成、コンパイル、実行

以下のディレクトリ構造を作成してください
```shell
root/
　└ home/
      └ ユーザー名/
	    └ clang/
	         └ HelloWorld.c 
```
HelloWorld.cに記載する内容
```
#include <stdio.h>

void main(void){
    printf("Hello,World\n");
}
```

Cファイルを作成したのでコンパイルして実行する
```shell
$ gcc HelloWorld.c 　　<-コンパイル
$ ls -al 　　　　　　　　<-コンパイルされたa.outがあるか確認
$ ./a.out             <-実行
```
```
Hello,World 
```
と表示されればOK


## treeコマンドをインストールする
treeコマンドを使うことにより視覚的にディレクトリ構造を確認できます
演習問題の答え合わせが楽にできるのでインストールして使ってみよう
```shell
$ sudo dnf -y install tree
$ tree
```

## 演習
（）は難易度を表している
易しい、普通、難しいの3段階

### 演習問題1(易しい)

1. 以下のディレクトリ構造を作成
2. echoコマンドを使いtestにset.txtファイルを作成
ヒント: >
```
root/
　└ home/
      └ ユーザー名/
	    ├ test/
	    │
	    └ sysken/
```
上記の構造を作成したあと以下の構造に変更できたら成功
```
root/
　└ home/
     └ ユーザー名/
	    ├ test/
	    │	└set.txt
	    │
	    └ sysken/
	          
	 
```
### 演習問題2(易しい)

1. ユーザーディレクトリに移動 
1. 絶対パスでtestディレクトリのファイルやディレクトリを表示する
1. 相対パスでtestディレクトリのファイルやディレクトリを表示する
```
root/
　└ home/
     └ ユーザー名/
	    ├ test/
	    │	└set.txt 
	    │
	    └ sysken/
	 
```

### 演習問題3(易しい)

1. syskenフォルダにあるsetファイルをtestフォルダに移動させてみよう
```
root/
　└ home/
      └ ユーザー名/
	    ├ test/
	    │　　└set.txt
	    └ sysken/	 
```
上記を以下の構造に変更できたら成功
```
root/
　└ home/
     └ ユーザー名/
	    ├ test/
	    │	　
	    │
	    └ sysken/
	 	　└set.txt 
```

### 演習問題4(易しい)
1. syskenフォルダの下にdirディレクトリを作成
2. testディレクトリにあるset.txtをdirディレクトリにコピー
```
root/
　└ home/
     └ ユーザー名/
	    ├ test/
	    │	　└set.txt 
	    │
	    └ sysken/
	 
```
上記を以下の構造に変更できたら成功
```
root/
　└ home/
     └ ユーザー名/
	    ├ test/
	    │	　└set.txt 
	    │
	    └ sysken/
	 	   └ dir/
		      └set.txt 
```
### 演習問題5(易しい)
1. clangeディレクトリをlsコマンドで表示した内容をtxtファイルとして保存
2. ただし、lsで表示した内容をviなどを使って編集してはいけない

### 演習問題6(普通)
1. 以下のディレクトリ構造を作成する
```
root/
　└ home/
      └ ユーザー名/
	    ├ clange
	    │     └ M6.c
	    │
	    │
	    ├ aikoudai/
	    │
	    └ sysken/
	          └set.txt
	 
```

2. M6.cファイルを作成し以下のプログラムを作成する。
一次元配列を定義。10 個の数値を入力するし配列に順番に入れる。入力された順番と逆の順番で 10 個の数値を表示する。
4. 逆順で表示されていればOK


### 演習問題7(普通)
1. clangディレクトリにM7.cを作成し下記のプログラムを書け
1. 整数を 10 回入力し、小さい順に並べ替えて表示するプログラムを作成しなさい。
1. 小さい順に並び替えれてればOK 

### 演習問題8(普通）
1. clangディレクトリにM8.cを作成し下記のプログラムを書け
1. 0以上の整数値を5つ入力させ、それぞれの値に対して、次の形式でグラフを描くプログラムを作成せよ。
```shell
【実行例、下線部は入力例】
$ ./knock58
input data[0]: 7
input data[1]: 10
input data[2]: 14
input data[3]: 15
input data[4]: 21
7	:***** **
10	:***** ***** 
14	:***** ***** ****
15	:***** ***** ***** 
21	:***** ***** ***** ***** *

```

