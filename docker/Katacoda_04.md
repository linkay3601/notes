# Dockerizing Node.js applications

```Dockerfile
FROM node:10-alpine

RUN mkdir -p /src/app

WORKDIR /src/app

COPY package.json /src/app/package.json

RUN npm install

COPY . /src/app

EXPOSE 3000

CMD [ "npm", "start" ]
```

- `FROM node:10-alpine` 指令，基于 node:10-alpine 镜像构建
- `RUN mkdir -p /src/app` 指令，运行命令 `mkdir -p /src/app`
- `WORKDIR /src/app` 指令， 指定工作目录
- > `WORKDIR` 可以确保所有之后的所有命令都是从我们应用相关联的目录执行。
- `COPY package.json /src/app/package.json` 指令，拷贝 *package.json* 到目标路径 */src/app/package.json*
- > 先把 *package.json* 拷贝到指定位置，可以让后续的命令 `npm install` 在后续构建中只有在 *package.json* 文件发生修改时才会执行，这样做可以利用镜像的缓存机制来减少构建时间。
- `RUN npm install` 指令，运行命令 `npm install`
- `COPY . /src/app` 指令，拷贝当前目录的所有文件到目标路径 */src/app*
- `EXPOSE 3000` 指令，暴露容器的 3000 端口
- `CMD [ "npm", "start" ]` 指令，在容器启动时默认运行命令 `npm start`

使用 `build` 命令和上面的 *Dockerfile* 构建自己的镜像。

```bash
$ docker build -t my-nodejs-app
```

运行刚才构建的镜像，并映射 3000 端口。

```bash
$ docker run -d --name my-running-app -p 3000:3000 my-nodejs-app
```

运行刚才构建的镜像，并定义环境变量 *NODE_ENV*。

```bash
$ docker run -d --name my-production-running-app -e NODE_ENV=production -p 3000:3000 my-nodejs-app
```

- -e 选项，定义自己的环境变量