+++
categories = ["Docker"]
date = "2016-02-07T13:35:31+09:00"
tags = ["golang", "Go言語", "Docker"]
title = "Go言語で作ったAPIサーバーがのったコンテナをDockerで使ってみる"

+++


# Mac OSにdocker-toolboxをインストールし、VM(machine)を立ち上げる

1.<a href="https://www.docker.com/products/docker-toolbox" target=_blank>docker-toolbox</a>	をインストールします。


2.インストールが完了すると、 **docker quickstart terminal** というのがありますのでそれを起動します。

すると、ターミナルが立ち上がり、下記のようなログが流れます。


```bash
bash --login '/Applications/Docker/Docker Quickstart Terminal.app/Contents/Resources/Scripts/start.sh'
Last login: Thu Jan 21 15:14:12 on ttys017
% ~
% bash --login '/Applications/Docker/Docker Quickstart Terminal.app/Contents/Resources/Scripts/start.sh'


Creating CA: /Users/kwmt/.docker/machine/certs/ca.pem
Creating client certificate: /Users/kwmt/.docker/machine/certs/cert.pem
Running pre-create checks...
Creating machine...
(default) Copying /Users/kwmt/.docker/machine/cache/boot2docker.iso to /Users/kwmt/.docker/machine/machines/default/boot2docker.iso...
(default) Creating VirtualBox VM...
(default) Creating SSH key...

(default) Starting the VM...
(default) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect Docker to this machine, run: /usr/local/bin/docker-machine env default


                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/


docker is configured to use the default machine with IP 192.168.99.100
For help getting started, check out the docs at https://docs.docker.com
```

完了したら、`default`というVM(machineと呼ぶことにする)が作成され、machineが起動している状態になります。 

# `docker-machine`コマンドについて 

machineを起動・停止したり、状態を確認するには、`docker-machine`コマンドを使用します。

* machineの状態を確認するには、 `ls` コマンドを実行します。

```bash
% docker-machine ls
NAME      ACTIVE   URL          STATE     URL                         SWARM   DOCKER   ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v1.9.1  
```

**default**というmachine名で、STATEがRunningなので起動していることが確認できます。


* machineが起動しているかどうかは`status`コマンドで確認できます。

```bash
% docker-machine status <machine名>
Running
```

* もし起動していなかった場合は、`start`コマンドを実行します。

```bash
% docker-machine start <machine名>
```

* machineが起動しているのを止めたい場合は、 `stop` コマンドを実行します.

```bash
% docker-machine stop <machine名>
```

* machineにsshログインするには、`ssh`コマンドを実行します。

```bash
% docker-machine ssh <machine名>
```


* Mac OSのshellからmachine上のコンテナを操作するには、 `env` コマンドで表示される環境変数をmac側のshellに設定する必要があります。
`eval` コマンドを使って設定すると簡単です。

```bash
% eval $(docker-machine env default)
```

これで、 `ssh`コマンドでmachineに接続しなくても、macのターミナルから`docker`コマンドが使えるようになります。


# Goで作ったAPIサーバーのコンテナを作成

コンテナにGoとMySQLをインストールし、それと
<a href="https://github.com/techvein/gozen" target=_blank>gozen</a>(オレオレWebフレームワークというかMVCテンプレートみたいなものですが)を使ってコンテナを作成してみたいと思います。



## <a id="create_container_by_self"></a>Dockerfileを使ってコンテナを作成するには

1.作業ディレクトリに移動します。

```bash
% cd ~/work (←なんでもよい)
```

2.Dockerfileを作成します。

```bash
% touch Dockerfile
```

内容は<a href="https://github.com/kwmt/makunouchi-gozen/blob/master/Dockerfile" target=_blank>github</a>をみてください。



3.Dockerfileを元に`build`コマンドでビルドします。

```bash
$ docker build -t <REPOSITORY>  <DockerfileのPATH>
(例 docker build -t kwmt/makunouchi-gozen  .)
```


4.コンテナイメージが作成されたか確認するには、`images`コマンドを使用します。


```bash
$ docker images
```

```bash
REPOSITORY              TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
kwmt/makunouchi-gozen   latest              1339114a1094        About a minute ago   977.5 MB
buildpack-deps          jessie-scm          d6fc4b5b4917        5 days ago           292 MB
```




## 作成したコンテナイメージを <a href="https://hub.docker.com/" target=_blank>Docker Hub</a>にpushするには

docker loginコマンドでログインして、pushするだけ。

```bash
% docker login
Username: kwmt
Password: 
Email: foo@gmail.com
Login Succeeded
% docker push kwmt/makunouchi-gozen
The push refers to a repository [docker.io/kwmt/makunouchi-gozen] (len: 1)
1339114a1094: Pushed 
0477ae0365f0: Pushed 
~~~~~ 

```
40分ぐらいかかった。。。

## Docker Hubにあるコンテナイメージを使うには

(ご注意)手順が増えてしまいました。自分が知らないだけでもっと手順は短くなると思ってます。分かり次第、修正します。


1.適当な作業ディレクトリ(例:work)に移動し、コンテナイメージを起動します。

```bash
% docker run --name="gozen_docker" -p 8080:9000 -d -it kwmt/makunouchi-gozen 
```

明示的にpullしなくてもローカルになければ、DockerHubから探してpullしてくれます。


1-1.現在起動中のコンテナを確認するには `ps` コマンドを使用します。

```bash
% docker ps
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS              PORTS                              NAMES
a43200ebc23e        kwmt/makunouchi-gozen   "/bin/bash"         About an hour ago   Up About an hour    3306/tcp, 0.0.0.0:8080->9000/tcp   gozen_docker
```


2.コンテナの/gopathにあるデータをホストにコピーします。

```bash
% docker cp gozen_docker:/gopath .
```

3.コピーが終わったら、立ち上げ直します。(*TODO:* 最初からvオプション付けるとホスト側にソースがないので、ソースがない状態が共有されてしまい、`docker cp`してもホスト側にコピーされない。この手順はなくしたい)

3-1.いったんコンテナとイメージは削除します。

```bash
% docker rm -f $(docker ps -a -q) && docker rmi -f $(docker images -q)
```

3-2.ホストとコンテナのVolumeを共有するオプション(-v)をつけて起動します。

```bash
% docker run --name="gozen_docker" -v $(pwd)/gopath:/gopath -p 8080:9000 -d  -it kwmt/makunouchi-gozen
```


4.コンテナにログインして、サーバー起動のための準備をします。(*TODO:* 本来ならこれもDockerfileに埋め込みたい)


```bash
% docker exec -it gozen_docker bash
```

4-1.MySQLサーバーを起動します。(*TODO:* docker-composeなど使ってDBサーバーを別コンテナ化したら不要になるかも？)

```bash
$ /etc/init.d/mysql start
```

4-2.DB・ユーザー作成(*TODO:* Dockerfileとかで何とかしてこの手順を無くしたい)

```bash
$ mysql -u root -prootpass < db/setup/mysql.sql 
```

4-3.マイグレーションを実行します。

```bash
$ cd $GOPATH/src/gozen
$ goose up (insert sample data into DB)
```


5.Intellij IDEAを使用する場合は、ホスト側(Mac側)で実行してください。 `$GOPATH/src/gozen/vendor/src` に `$GOATH` のシンボリックリンクをはります。これにより、Intellijでglideでパッケージ管理されてるライブラリの補完が効くようになります。

```bash
% cd work/gopath/src/gozen
% ./symlinkVendor.sh 
```

6.コンテナ側で、APIサーバーを起動します.

```bash
$ cd $GOPATH/src/gozen
$ ./build.sh
```

7.確認します。サンプルとして `/api/users/1`にアクセスしてみてください。

コンテナにログインしている場合は、localhost:9000で確認。

```bash
$ curl http://localhost:9000/api/users/1
{"Id":1,"Name":"田中"}
```

コンテナの外から確認するなら、`docker-machine ip default` でipアドレスを確認してアクセスする。

```bash
% curl $(docker-machine ip default):8080/api/users/1
{"Id":1,"Name":"田中"}
```


## コンテナやイメージを削除するには

* イメージ削除

```bash
$ docker rmi -f <IMAGE ID>
```

-fは強制的に削除します。

* 全イメージ削除

```bash
$ docker rmi -f $(docker images -q)
```

imagesコマンドの-qオプションは、Image Idのみ表示するオプションです。

* 全コンテナ削除

```bash
$ docker rm -f $(docker ps -a -q)
```






## エラーが出た時の対策

### docker runやpull imageをpullするときにNetwork timed outと出た場合

#### 現象
docker runやpullで pull中に下記のエラーが出た
```bash
% docker pull golang
Using default tag: latest
Pulling repository docker.io/library/golang
Network timed out while trying to connect to https://index.docker.io/v1/repositories/library/golang/images. You may want to check your internet connection or if you are behind a proxy.
```

#### 対策

```bash
$ docker-machine restart default && eval $(docker-machine env default)
```

[Network timed out while trying to connect to https://index.docker.io](http://stackoverflow.com/a/32023104)
## 参考
* <a href="https://docs.docker.com/engine/installation/mac/" target=_blank>Installation on Mac OS X</a>
* <a href="http://bit.ly/1P1KkW7" target=_blank>Mac OSX で Docker 入門 (Docker Toolbox版) - fugafuga.write</a>
* <a href="http://bit.ly/1S1Alns">Docker 入門ハンズオン資料 - Qiita</a>




