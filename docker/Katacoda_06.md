# Ignoring Files During Build

为了避免一些敏感的文件和目录被一起构建到我们的镜像中，我们可以添加一个 *.dockerignore* 的文件。

```Dockerfile
FROM alpine
ADD . /app
COPY cmd.sh /cmd.sh
CMD ["sh", "-c", "/cmd.sh"]
```

- `FROM alpine` 指令，基于镜像 alpine 构建
- `ADD . /app` 指令，拷贝当前目录中的文件到目标路径 */app*
- `COPY cmd.sh /cmd.sh` 指令，拷贝 *cmd.sh* 到目标路径 */cmd.sh*
- `CMD ["sh", "-c", "/cmd.sh"]` 指令，启动容器时默认运行命令 `sh -c /cmd.sh`
- > ADD 和 COPY 指令的主要功能都是复制文件到目标路径，但是 ADD 相比较 COPY 而言有两个特殊的功能，ADD 支持 URL 和压缩包自动解压（可能会导致一些额外的文件被添加到我们的镜像中），所以 ADD 命令要小心使用，一般情况下推荐使用 COPY 命令。

假设我们的 *Dockerfile* 目录中有下面几个文件。

- *Dockerfile*
- *cmd.sh*
- *passwords.txt*（这个属于敏感文件，我们肯定不想把它包含到我们构建的镜像中）

在没有 *.dockerignore* 文件的情况下，我们直接构建镜像。

```bash
$ docker build -t password
```

然后，使用 `ls` 命令查看我们的文件。

```bash
$ docker run password ls /app
Dockerfile
cmd.sh
passwords.txt
```

可以看到 *passwords.txt* 被包含到了我们构建的镜像中（这肯定不是我们想看到的）。

所以，我们可以把 *passwords.txt* 添加的我们的 *.dockerignore* 文件中。

```bash
$ echo passwords.txt >> .dockerignore
```

这时候我们重新构建镜像并使用 `ls` 命令查看我的文件。

```bash
$ docker build -t nopassword
$ docker run nopassword ls /app
Dockerfile
cmd.sh
```

> 如果我们在构建镜像时，RUN 命令需要用到 *passwords.txt* 文件，那么我们可以在单个 RUN 命令中完成 *passwords.txt* 的复制、执行和删除操作。**只有容器最终的状态才会被构建到我们的镜像中**。

使用 *.dockerignore* 其实也可以减少优化我们镜像的构建时间。

假设我们有一个 100M 的临时文件在 *Dockerfile* 的目录中，但是是我们在将要构建的镜像中不会使用这个文件。

- big-temp-file.img（100M）

在没有 *.dockerignore* 文件的情况下，我们直接构建我们的镜像。

```bash
docker build -t large-file-context 
```

此次构建可以顺利完成，但是因为有一个 100M 的临时文件，所以我们构建速度明显慢了许多（这里得自己体会）！

> 这里有一个比较明智的做法，就是忽略 *.git* 和在镜像中构建完成的依赖（比如 *node_modules*）。这些文件从来不会在容器运行时被使用，只会为构建镜像添加额外的开销。

然后让我们把这个 100M 的临时文件添加到 *.dockerignore* 文件中，重新构建我们的镜像。

```bash
$ echo big-temp-file.img >> .dockerignore
$ docker build -t no-large-file-context
```

可以感觉到我们镜像的构建速度明显提升了许多（这里得自己体会）。