# Persisting Data Using Volumes

创建一个 Redis 服务并持久化数据。

```bash
$ docker run -v /docker/redis-data:/data --name r1 -d redis redis-server --appendonly yes
```

- `-v`：映射主机中的目录 */docker/redis-data* 到容器中的目录 */data* 以实现数据的持久化存储

通过管道将数据传输到 Redis 服务中存储。

```bash
$ cat data | docker exec -i r1 redis-cli --pipe
```

- > 这里 data 是一个文件，里面的内容为是：`SET Key0 Value0`。

此时查看 */docker/redis-data* 目录，已经有一个文件 *appendonly.aof*。

使用相同的方法，将 */docker/redis-data* 映射到第二个容器并查看映射目录的内容。

```bash
$ docker run  -v /docker/redis-data:/backup ubuntu ls /backup
appendonly.aof
```

通过 `--volumes-from` 参数来共享数据卷。

```bash
$ docker run --volumes-from r1 -it ubuntu ls /data
```

- > 这里的 */data* 指的是容器 r1 中的目录，内容为：
	  ```txt
	  appendonly.aof
	  ```

通过 `:ro` 创建只读数据卷。

```bash
$ docker run -v /docker/redis-data:/data:ro -it ubuntu rm -rf /data
rm: cannot remove '/data/appendonly.aof': Read-only file system
```