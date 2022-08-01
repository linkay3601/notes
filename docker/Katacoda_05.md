# Optimise Builds With Docker OnBuild

```Dockerfile
FROM node:7
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
ONBUILD COPY package.json /usr/src/app/
ONBUILD RUN npm install
ONBUILD COPY . /usr/src/app
CMD [ "npm", "start" ]
```

- `FROM node:7` 指令，基于镜像 node:7 构建
- `RUN mkdir -p /usr/src/app` 指令，运行命令 `mkdir -p /usr/src/app`
  id:: 614800de-e1db-42a7-a88b-fb9cf03d4ae6
- `WORKDIR /usr/src/app` 指令，指定工作目录 */usr/src/app*
- `ONBUILD COPY package.json /usr/src/app/` 指令，拷贝 *package.json* 到目标路径 */usr/src/app/*
- `ONBUILD RUN npm install` 指令，运行命令 `npm install`
- `ONBUILD COPY . /usr/src/app` 指令，拷贝当前目录中的文件到目标路径 */usr/src/app*
- > 具有 ONBUILD 指令前缀的指令在构建镜像的时候不会被执行，只有在构建镜像作为基础镜像的时候才会被执行。
- `CMD [ "npm", "start" ]` 指令，容器启动后默认运行命令 `npm start`

```Dockerfile
FROM node:7-onbuild
EXPOSE 3000
```

- `FROM node:7-onbuild` 指令，基于镜像 node:7-onbuild（也就是上面的镜像）构建
- > 使用具有 ONBUILD 指令的镜像构建，可以使我们的 *Dockerfile* 更简单并且更具复用性。在这里我们需要做的仅仅是暴露一个端口。
- `EXPOSE 3000` 指令，暴露容器的 3000 端口

使用上面的 *Dockerfile* 构建我们的 nodejs 镜像。

```bash
$ docker build -t my-nodejs-app
```

运行我们构建的镜像，并映射 3000 端口。

```bash
$ docker run -d --name my-running-app -p 3000:3000 my-nodejs-app
```