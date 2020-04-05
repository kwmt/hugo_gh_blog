
+++
title= "dockerコンテナのNameを変更するには"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Docker"]
tags = ["Docker"]
keywords = [""]
+++

## 方法

```
$ docker rename <CONTAINER ID> <NEW NAME>
```

`<CONTAINER ID>` には、 `docker ps -a` で表示されるIDを指定。
`<NEW NAME>` には、変更したい名前を指定する。

## 例
### 変更前

```
% docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
8618bc25dc49        ubuntu:16.10        "/bin/bash"         15 minutes ago      Exited (0) 15 minutes ago                       prickly_brown
```

### 変更後

```
% docker ps -a                          
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
8618bc25dc49        ubuntu:16.10        "/bin/bash"         21 minutes ago      Exited (0) 21 minutes ago                       rename_test
```

NAMESの `prickly_brown` が `rename_test` に変わってるのがわかると思います。

## 参考

https://docs.docker.com/engine/reference/commandline/rename/

