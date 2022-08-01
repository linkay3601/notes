# Data Containers

数据容器也是一个容器，只不过它唯一的责任是用来^^管理/存储^^数据。像其他容器一样，它可以被主机系统所管理。但是，当运行命令 `docker ps` 时，不会被显示。

创建一个数据容器，并使用 `-v` 选项绑定挂载 */config* 作为配置目录。

```bash
$ docker create -v /config --name dataContainer busybox
```

使用 `cp` 命令拷贝我们的配置文件到容器中的配置目录中。

```bash
$ docker cp config.conf dataContainer:/config/
```

启动另一个容器 ubuntu，并且使用 `-v` 绑定挂载上面数据容器中的卷。

```bash
$ docker run --volumes-from dataContainer ubuntu ls /config
```

> 如果 ubuntu 中的 */config* 目录已经存在，那么会被覆盖，可以同时绑定挂载多个卷。

使用 `export` 命令导出我们的数据容器。

```bash
$ docker export dataContainer > dataContainer.tar
```

在另一个装有 Docker 的环境中使用 `import` 命令导入我们的数据容器。

```bash
$ docker import dataContainer.tar
```