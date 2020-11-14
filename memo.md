## Docker 基本 1

Docker file ⇒ Docker image ⇔ コンテナ

Docker file は Docker image の設計書のようなもの。(テキストファイル)
Docker image を Host(自分のマシン)にインストールし、Host の Docker を使って image からコンテナを作る。使用者はコンテナの中で作業する。

### Linux

Docker は Linux のコンテナ技術を利用なので、Linux の基礎知識が必要。

Unix をベースに Linux を 0 から開発したのが、リーナス・トーバルズ。

Mac は Unix ベースの OS なので、Docker と相性が良い。

#### Shell

Linux の OS には Kernel という核を持っており、ここに命令を出すことで OS を操作できる。しかし、ユーザは直接 Kernel に命令を実行することはできず、Kernel の周りの Shell に命令を出す。(bash,zsh なども Shell の一種)

Shell を使うのに必要なアプリケーションがターミナル。
ターミナルと Shell は別物なので注意。

#### 環境変数

OS 上で動くプロセス(ターミナル、ブラウザ etc)が情報を共有する為の変数。

> echo \$SHELL

環境変数の作り方(AGE という環境変数を作る)

> export AGE=20

環境変数の呼び出し方(\$を付けて呼び出す)

> echo \$AGE

### Linux コマンド

<path>に移動

> cd <path>

今のディレクトリを表示

> pwd

フォルダを作成

> mkdir <new folder>

ファイルを作成

> touch <new file>

カレントディレクトリのファイル、フォルダを表示

> ls

ファイルを削除

> rm <file>

フォルダを削除

> rm -r <folder>

### Docker Hub

Github の Docker 版のようなもの。

### Docker コマンド

Docker Hub にログインする

> docker login

Docker image を pull する。

> docker pull <image>

library というのは、

Host にある Docker image のリストを表示する。

> docker images

pull してきた image からコンテナを作る。

> docker run <image>

アクティブな Docker コンテナの一覧を表示する。

> docker ps(Process Status の略)

アクティブでない Docker コンテナの一覧も含めて表示する。

> docker ps -a(all の略)

### Docker image の Exit までの流れ

1.Docker image を docker run コマンドで実行

2.コンテナが作られる

3.プログラムが execute される(hello-world の場合はテキストを出力する)

4.プログラム終了後に exit される

### docker run hello-world 実行時に出るテキスト

```txt
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

#### docker run -it ubuntu bash

ubuntu とは linux の派生 OS

-it は bash 起動時に必要なおまじない(option)※後述

bash はコンテナ起動時に実行するプログラム

つまり、ubuntu を起動する際に bash をデフォルト実行するようなコマンド。(Docker run は image を自動でダウンロードしてくれる。)

```txt
PS C:\Users\KOHEI\Documents\GitHub\docker> docker run -it ubuntu bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
6a5697faee43: Pull complete
ba13d3bc422b: Pull complete
a254829d9e55: Pull complete
Digest: sha256:fff16eea1a8ae92867721d90c59a75652ea66d29c05294e6e2f898704bdb8cf1
Status: Downloaded newer image for ubuntu:latest
root@008ea564a6ac:/#
```

6a5697faee43: Pull complete
ba13d3bc422b: Pull complete
a254829d9e55: Pull complete
上記は image レイヤと呼ばれる。image は image レイヤから構成される。

XXXXX9X99XXX
6a5697faee43: Pull complete
ba13d3bc422b: Pull complete
a254829d9e55: Pull complete
コンテナが作られると、この image レイヤの一番上に新たなレイヤが作成される。

実業務では、ubuntu に対して、必要なプログラムをインストールして使うのが一般的(python とか)

> root@008ea564a6ac:/#

デフォルトではルート権限を持った状態でシェルが起動するので、注意が必要。

### Docker image を更新する方法

1.コンテナを更新して Docker image を作る

2.Docker file から Docker image を作る(一般的)

Ubuntu コンテナに入って bash を起動

> docker run -it ubuntu bash

test ファイルを作成

> root@008ea564a6ac:/# touch test

コンテナから抜けて、プロセスを消す。

> root@008ea564a6ac:/# exit

コンテナから抜けるが、プロセスを残す。(コマンド　 Ctrl +P+Q を以下の bash 上で実行)

> root@008ea564a6ac:/# read escape sequence

コンテナを再起動する

> docker restart <コンテナ ID>

コンテナに再度入る(docker run と違い、ID を指定する)

> docker exec -it <コンテナ ID> bash

コンテナを docker image に保存する

> docker commit <コンテナ ID> <新しい Image の名前:タグ名>

docker images で新しいタグの image が出来ていることが分かる。(docker commit <ID> ubuntu:updated)
ubuntu updated 7c67297bee50 9 seconds ago 72.9MB
ubuntu latest d70eaf7277ea 8 days ago 72.9MB

タグを省略した場合には、latest というタグが自動でセットされる。

新しい Image の名前はレポジトリ名と同一である必要がある。
docker commit <ID> ubuntu:updated というコマンドでは ubuntu という image 名が付けられるので、push する為には、kyamasan/ubuntu というレポジトリを作る必要がある。
(Docker はイメージ名を参照して push 先を探す仕組みの為。この辺りは github と異なる。)
もし間違えて image 作成をした場合には、レポジトリ名と一致させる為にイメージ名を変更する必要がある。

イメージ名を変更する

> docker tag <現在の image 名> <修正後の image 名>

docker tag ubuntu:updated kyamasan/my-first-repo

docker images で確認すると同じイメージ ID に複数のタグがついていることが分かる。
REPOSITORY TAG IMAGE ID CREATED SIZE
kyamasan/my-first-repo latest 7c67297bee50 23 minutes ago 72.9MB
ubuntu updated 7c67297bee50 23 minutes ago 72.9MB

Docker に push する

> docker push <username>/<レポジトリ名>

PS C:\Users\KOHEI\Documents\GitHub\docker> docker push kyamasan/my-first-repo
The push refers to repository [docker.io/kyamasan/my-first-repo]
e753f18c0472: Pushed
cc9d18e90faa: Layer already exists
0c2689e3f920: Layer already exists
47dde53750b4: Layer already exists

library/ubuntu と変わらない部分は push されず、Mount されていることが分かる。

Docker image を削除する。

> Docker rmi <image>

Docker image を pull する。

> docker pull <image>（kyamasan/my-first-repo:latest）

### イメージ名の構成

<hostname>:<port>/<username>/<repository>:<tag>

<hostname>:<port>のデフォルト値は registry-1.docker.io(DockerHub 以外のサービス(JFrog Artifactory など)を使用するのでなければ変更は不要)
<username>のデフォルト値は library
<tag>のデフォルト値は latest

#### exit と detach の違い

前者はコンテナから抜けるタイミングでプロセスを消すので、次回起動時に restart コマンドを入力する必要がある。

後者はプロセスを残さないので、次回作業をする時は attach コマンドを実行するだけでよい。

## Docker 基本 2

### docker run について

docker image から(なければダウンロードして)コンテナを作るコマンド
⇒ コンテナを作る＋スタートする(run = create + run)

コンテナを作成する

> docker create <image>

デフォルトで設定されたコマンドを実行する(実行後、コンテナは exit 状態になる。この時、実行結果は確認できない)

> docker start <container>

デフォルトで設定されたコマンドを実行する(実行結果は確認できる)

> docker start -a(attach) <container>

#### デフォルトで設定されたコマンドを変更する

> docker run -it <image> bash

この時、hello-world のように bash コマンドを持たないような場合は Error になる。

### -it とは?

docker run で bash を起動する為のオプション

-i:インプット可能(Host からコンテナへ入力を可能にする)
-t:表示が綺麗になる

#### LINUX の 3 つのチャネル

チャネルを使って Linux はプログラムと対話できる。

STDIN
⇒ コマンドをプログラムに送る(-i オプションが必要)

STDOUT
⇒ プログラムが結果をディスプレイに結果を返す

STDERR
⇒ プログラムが結果をディスプレイに結果(エラー)を返す

### コンテナの削除

一度 exit したコンテナを使用することは業務上少ないので、定期的に削除すると良い

コンテナの削除(up 状態のコンテナは削除できないので先に stop する。)

> docker rm <container>

コンテナの停止

> docker stop <container>

停止しているコンテナの全削除

> docker system prune

dangling とは、タグ付けされていないコンテナ

WARNING! This will remove:

- all stopped containers
- all networks not used by at least one container
- all dangling images
- all dangling build cache

### コンテナ内部のデータについて

当然ながら、コンテナ内部のファイルシステムは Host や他のコンテナとは分かれている。
Host とコンテナのファイルシステムを繋げる方法は後述。

### コンテナに名前を付ける方法

常に起動させておきたいようなコンテナには名前を付けておくとよい。

> docker run --name <name> <image>

### exit したコンテナをすぐに消す方法

foreground モード
docker run --rm <image>

### バックグラウンドでコンテナを動かす方法

detached モード

> docker run -d <image>

## Docker 基本 3

Docker file から Docker image を作る。

### ubuntu の Docker file

https://github.com/tianon/docker-brew-ubuntu-core/blob/fc9c4ef6e3d4891577936f0b103331e79e1e8281/bionic/Dockerfile

基本的に INSTRUCTION(FROM、ADD など)+ Arguments の形で書いていく。

### Dockerfile のビルド方法

指定したディレクトリ(カレントディレクトリなら.)から Dockerfile を自動的に検知してビルドして none という名前の docker image を作る。

> docker build <directory>

REPOSITORY TAG IMAGE ID CREATED SIZE
<none> <none> eb7edfa93a63 6 seconds ago 72.9MB

このような none 状態の image を dangling image という。

> docker images -f dangling=true

指定したディレクトリ(カレントディレクトリなら.)から Dockerfile を自動的に検知してビルドして 任意の名前の docker image を作る。

> docker build -t <name> <directory>

## Dockerfile 基本

FROM
ベースとなるイメージを決定(ほとんど、alpine か ubuntu のような OS を指定する)

RUN
Linux コマンドを実行して、カスタマイズができる。

CMD
コンテナのデフォルトコマンドを指定(executable は bin/bash のようなコマンド)
Dockerfile の最後に記述する。

> CMD ["executable", "param1", "param2"]

```Dockerfile
FROM ubuntu:latest
RUN touch test
RUN echo "hello world" > test
```

RUN,(COPY, ADD)コマンド ごとに image layer は追加されていく。その分 image の容量も膨大になっていく為、業務上課題になることが多い。
RUN の用途で最も多いのが、パッケージのインストール(apt-get)。
最小限の Layer を目指すことが重要。

echo "hello world"の image layer
touch test の image layer
ubuntu の image layer

### RUN と CMD の違い

どちらも Linux のコマンドを実行するものだが、
RUN はイメージを構成する為のコマンドを指定し、Layer を作る
CMD はデフォルトのコマンドを指定し、Layer を作らない
という違いがある。

### apt-get(16.0.4 以降は apt も可)

ubuntu のパッケージ管理コマンドは以下の 2 つをセットで用いるのが一般的。

インストールされるパッケージのリストを最新化(古いバージョンをダウンロードしない為)

> apt-get update

インストールを行う。インストール時の質問に全て yes で答える為に、-y オプションを指定することが多い。

> apt-get install <package>(引数は複数設定可能)

```Dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y \
curl \
nginx
```

### 最小限の Layer を目指す為に

RUN を分けるのではなく、コマンドを&&で繋げて 1 行で書く。(改行する場合は\(バックスラッシュ))

#### Cache の活用

RUN を分けて書くことによるメリットも存在する。
Layer 毎に Host にキャッシュが貯まっていくので、一度実行した Layer は再ビルド実行時にスキップできる。

一般的に Dockerfile をメンテナンスしている最中は、Cache を使って細かく RUN を分けていき、正しく通る Dockerfile が出来たら&&で 1 行で書く。

## Dockerfile 基本 2

docker build が実行されると、指定されたフォルダを Docker Daemon に渡す。このフォルダのことを、ビルドコンテキストと呼ぶ。
これによって、ビルドコンテキストにある Dockerfile 以外の情報を活用することができる。(COPY,ADD,ENTRYPOINT,ENV,WORKDIR)

### Docker Daemon

docker build、docker run などのコマンドは Docker CLI(Client)と呼ばれるプログラムで処理を行う。
Client で受け取ったコマンドは、docker daemon に渡され image やコンテナの処理を行う。docker daemon が存在する領域は Docker Host(Server)と呼ばれる。

このように Docker は Client-Server 構成となっている。

### Build Context

context とは、環境を意味する。
build に使わないファイルは原則 build context には入れない。
ADD や COPY で build context にあるファイルを image に持っていける。

### COPY

COPY を使うことで、build context にある Dockerfile 以外のファイルを image に渡すことができ、コンテナでも使用することができる。

> COPY <src> <path>

ADD も同じ事ができるが、単純なファイル、フォルダのコピーを行う場合は COPY、tar の圧縮ファイルをコピー＆解凍したい場合は ADD を使う。(ADD の方が上級者向け)

> COPY <src(tar ファイル)> <path>

### COPY 注意点

> COPY Gemfile Gemfile.lock /product-register/

複数のファイルの場合は /product-register ではなく、 /product-register/と最後にスラッシュを付ける。

### Dockerfile という名前のファイルが build context にない場合

Dockerfile.dev、Dockerfile.test などの名前を付けて Dockerfile としたい場合が存在する。

> docker build -f <filepath>(./Dockerfile.dev など)　<directory>

### ENTRYPOINT

CMD と密接な関係がある。基本的に CMD で事足りる。
ENTRYPOINT でもデフォルトのコマンドを指定できる。

CMD と違い、run 時に上書きができない。
ENTRYPOINT でデフォルトコマンドを指定した場合、その後に CMD でパラメタを指定する。(※CMD の役割が変わるという点に注意)

> ENTRYPOINT ["ls"]
> CMD ["param1", "param2"]

docker run の引数にはオプションしか取らなくなる。

```Dockerfile
FROM ubuntu:latest
RUN touch test
ENTRYPOINT ["ls"]
CMD [ "--help" ]
```

作られた image を run するときに、以下のようにコンテナをオプションのように指定できる。

> docker run <image> -la

> docker run <image> ※何も指定しなければ Dockerfile の CMD(--help)

### ENV

環境変数の設定をしてくれる。
2 通りの方法で書ける。

> ENV <key> <value>

> ENV <key>=<value>

```Dockerfile
FROM ubuntu:latest
ENV key1 messi
ENV key2=ronaldo
```

image を docker run して bash で env と確認する。

> docker run -it --rm <image> bash

> env

### WORKDIR

```Dockerfile
FROM ubuntu:latest
RUN mkdir sample_folder
RUN cd sample_folder
RUN touch sample_file
```

上記のファイルでは sample_folder に移動後に sample_file を作成しているので、sample_folder 内でファイルが作られるように見えるが、実は RUN コマンドは root 直下でしか実行されない。

&&で繋げて書けば、cd での移動先で処理を実行してくれる。

```Dockerfile
FROM ubuntu:latest
RUN mkdir sample_folder && cd sample_folder && touch sample_file
```

その他に WORKDIR を使って、指定した場所でコマンド実行することもできる。(mkdir しなくても、WORKDIR で指定したフォルダがなければ自動で作られる)

```Dockerfile
FROM ubuntu:latest
WORKDIR /sample_folder
RUN touch sample_file
```

## ホストとコンテナの関係

コンテナのファイルシステムとホストのファイルシステムを繋げることができる。その際アクセス権限を適切に設定する必要がある。

Web サーバのようなものをコンテナで立てていた場合、外部からアクセスする為の port をコンテナにも繋げる必要がある。

コンテナに割り当てられる CPU、メモリの上限を指定する。

これらは docker run のオプションで指定できる。

### -v オプション(重要)

通常、コンテナからホストのファイルシステムへはアクセスできない。ホストのファイルシステムをコンテナにマウントすることで、あたかもホストのファイルシステムがコンテナ内にあるかのように振る舞う　ことができる。

このオプションを使えば、ホスト側に置いたソースをコンテナで実行することができるなど、コンテナ容量を抑えて様々なことができるようになる。

```Dockerfile
FROM ubuntu:latest
RUN mkdir new_dir
```

> docker run -it -v C:\mounted_folder:/new_dir <image> bash

この時、コンテナにファイルがあるのではなく、あくまでホストのファイルがマウントされただけなので、実体はホスト側にあることに注意。

#### アクセス権限について(-u オプション)

コンテナにホストのファイルシステムをマウントすると、特に指定しない限り root 権限で何でも実行できてしまう。

#### パーミッションの見方

> ls -la

drwxrwxrwx
↓
d(ディレクトリ),-(ファイル),l(シムリンク)
r(読み取り),w(書き込み),x(実行)の権限が、所有者、所有者グループ、その他にあるか。

### -p オプション

コンテナを Host の Web サービスとして使うのはよくある使い方。
この時、Host のアドレスだけでは、コンテナがどのポートで待っているのか分からない。

その為、Host のポートとコンテナのポートを繋げる(パブリッシュする、という)必要がある。

> docker run -it -p 8888(ホストのポート、何番でも OK):8888(jupyter notebook のデフォルトポート) --rm jupyter/datascience-notebook bash

### --cpus --memory

> docker run -it --rm --cpus 4(論理コア数) --memory 2g(メモリ数) ubuntu bash

確認方法

> docker inspect <コンテナ ID>

## Docker 応用 1

コンテナに jupyter や Anaconda などの環境を構築する。
Docker を使うことで、Host に影響なく環境を構築できる。(ホストの要素によって起きうる Conflict を心配する必要がない)
他人に Dockerfile を渡すことで簡単に開発環境をコピーできる。

※wget:インターネットから HTTP 通信でプログラムをダウンロードするプログラム。これでアナコンダをダウンロードする。
※vim:ubuntu にはエディタが入っていないのでダウンロードする。
※anaconda:https://repo.anaconda.com/archive/
の中の好きなバージョン(-x86 が ubuntu に対応)を選んで wget でインストールする。wget でもホストから COPY する方法でも良いが、バージョン変更時に Dockerfile を変えるだけでよいので、wget がおすすめ。

### パスの通し方

\$PATH の中に環境変数を追加することで、コンピュータがプログラムをそのパスから探してきてくれる。:(コロン)で区切られている。

> root@778a53d59fb5:/opt/anaconda3# echo \$PATH

⇒ /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

$export PATH = /path/to/something:$PATH でパスを追加する。

anaconda3 をインストールしたフォルダの bin が追加すべきパスとなる。(大抵の場合 bin がそういった追加すべきパスになる)

> export PATH=/opt/anaconda3/bin:\$PATH

### インタラクティブなオプションの回避方法

> apt-get install -y 〇〇

sh が実行するプログラム(Anaconda3-2020.07-Linux-x86_64.sh)の一覧を確認する。

> sh -x Anaconda3-2020.07-Linux-x86_64.sh

-b run install in batch mode (without manual intervention),
it is expected the license terms are agreed upon
-f no error if install prefix already exists
-h print this help message and exit
-p PREFIX install prefix, defaults to /root/anaconda3, must not contain spaces.
-s skip running pre/post-link/install scripts
-u update an existing installation
-t run package tests after installation (may install conda-build)

-b と-p オプションを使う。

> sh Anaconda3-2020.07-Linux-x86_64.sh -b -p /opt/anaconda3

### Python のパッケージ管理ツール pip

ubuntu の apt-get と同じように python にも pip というパッケージ管理ツールが存在する。pip を Dockerfile で update すると便利。

> pip install --update pip

## docker-compose

複数のコンテナを同時に RUN する為の機能。

複数コンテナの起動だけではなく、docker run コマンドが長くなる時にも使える。

docker-compose.yml に docker run コマンドのオプションをあらかじめ書いておくことができる。

docker インストール時に自動でインストールされてるはず。

> docker-compose --version

### docker-compose.yml の書き方

docker-compose とは、コンテナを起動するときどのように起動するか、コンテナを書いていくもの。
docker build と docker run の引数を書いていくと思えばよい。

YAML(YAML ain't markup language)ファイル。
基本的にキーと value の組み合わせ。
version:'3'は決まり文句。
services:は、service をまとめて書く際に必要。
web というのは任意の名前。(web か app と書くことが多い。)

```yaml
ports:
  - '3000:3000'
  - '3001:3001'
```

パラメータが複数になる場合は、-とスペースを付けて記述する。
コーテーションは無くても問題ない。

```yaml
volumes:
  - '.:/product-register'
```

docker run -v では絶対パスでフォルダをマウントしていたが、yaml ファイルでは相対パスで記述する。誰が使用しても実行できるようにするため。

Dockerfile が入った procuct-register フォルダをマウントするので、.を指定する。

```yaml
tty:true
stdin_open:true
```

-t が tty:true
-i が stdin_open:true を意味する。

```yml
version: '3'
services:
  web:
    build: .
    ports:
      - '3000:3000'
    volumes:
      - '.:/product-register'
    tty: true
    stdin_open: true
```

### docker-compose コマンド

> docker build ⇔ docker-compose build

up コマンドはビルドも同時に行う。image が古い場合には docker-compose up --build オプションをつける。

> docker run image ⇔ docker-compose up

docker-compose up -d(デタッチトモード)が一般的。

また、docker-compose down でコンテナを stop して rm する。

> docker ps ⇔ docker-compose ps

> docker exec <container> <command> ⇔ docker-compose exec <service> <command>

docker-compose exec では-it は不要。(tty などの設定が行われている為。)。コンテナ ID でなく、サービス名を指定する点に注意。

#### rails アプリの作成方法(参考)

> rails new . --force --database=postgresql --skip-bundle

--force:全て上書きする
--database=postgresql:デフォルト DB ではなく PostgreSql を使用する
--skip-bundle:bundle インストールをスキップする(dockerfile で指定済み。)

> rails s -b 0.0.0.0

これで rails アプリが起動できる。

### その他

environment でコンテナ内の環境変数を設定する。
depends_on で指定したサービスを先に作成してくれる。
links で web から db にアクセスできるようになる。

```
db:
  image: postgres
  volumes:
    - 'db-data:/var/lib/postgresql/data'
```

postgres のイメージからコンテナを作り、コンテナの中に DB のデータを作るが、コンテナを消したら DB も消えるのは ×。

コンテナ側の var/lib/postgresql/data に postgresql の DB が作成されるので、それをホストの db-data(まだ存在しなくても問題ない)にマウントする。

```Dockerfile
valumes:
  db-data:
```

上でマウントした db-data というフォルダを DockerVolume とすることができる。docker でデータベースを作って、その情報をホストや別コンテナで共有したい場合に使用。

```Dokcerfile

version: '3'
valumes:
  db-data:
services:
  web:
    build: .
    ports:
      - '3000:3000'
    volumes:
      - '.:/product-register'
    environment:
      - 'DATABASE_PASSWORD=postgres'
    tty: true
    stdin_open: true
    depends_on:
      - db
    links:
      - db
  db:
    image: postgres
    volumes:
      - 'db-data:/var/lib/postgresql/data'

```

### docker volume

> docker volume --help

create Create a volume
inspect Display detailed information on one or more volumes
ls List volumes
prune Remove all unused local volumes
rm Remove one or more volumes

docker volume のリストを確認
⇒product-register_db-data という docker volume が作られていることが分かる。

> docker volume ls

DRIVER VOLUME NAME
local product-register_db-data

docker volume の情報を確認
⇒Mountpoint でデータの格納先が分かる。

> docker volume inspect product-register_db-data

[
{
"CreatedAt": "2020-11-14T09:04:45Z",
"Driver": "local",
"Labels": {
"com.docker.compose.project": "product-register",
"com.docker.compose.version": "1.27.4",
"com.docker.compose.volume": "db-data"
},
"Mountpoint": "/var/lib/docker/volumes/product-register_db-data/_data",
"Name": "product-register_db-data",
"Options": null,
"Scope": "local"
}
]

## CI/CD と docker
