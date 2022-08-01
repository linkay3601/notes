# Ensuring Container Uptime With Restart Policies

Docker 认为任何以非 0 退出的容器都已经崩溃的，默认情况下已崩溃的容器将会保持崩溃状态。

我们来创建一个指定的容器，输出一个消息然后以 1 退出来模拟容器崩溃。

```bash
$ docker run -d --name restart-default scrapbook/docker-restart-example
```

如果你列出所有的容器，包括已经停止的，你将看到容器已经崩溃 `docker ps -a`。

然而，日志将会输出我们的消息，在真实场景中，希望有说明信息来帮助我们诊断问题。

```bash
$ docker logs restart-default
```

依赖于你自己的使用场景，重启一个失败的程序可能会修复这个问题。Docker 可以在它崩溃的时候，自动重启 Docker 一个指定的次数，在下面的例子中，Docker 将会在容器停止之前重启 3 次。

```bash
$ docker run -d --name restart-3 --restart=on-failure:3 scrapbook/docker-restart-example
```

- --restar=on-failure:# 选项：允许你指定 Docker 应该尝试的次数
- > 这里的 # 指得是次数。

我们可以查看日志，它输出了三次日志日志消息。

```bash
$ docker logs restart-3
```

最后，Docker 其实可以始终尝试重启崩溃的容器，在这种情况下，Docker 将会一直尝试重启容器直到容器被明确指定停止。

使用 *always* 标识去自动启动容器在它崩溃的时候，例如

```bash
$ docker run -d --name restart-always --restart=always scrapbook/docker-restart-example
```

你可以看到通过日志查看尝试的重启操作。

```bash
$ docker logs restart-always
```