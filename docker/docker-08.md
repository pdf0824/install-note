# Dockerfile指令详解

## COPY
格式:
```shell
COPY [--chown=<user>:<group>] <源路径>... <目标路径>

COPY [--chown=<user>:<group>] ["<源路径1>",..."<目标路径>"]
```

复制将会将从构建上下文目录的 `<源路径>` 复制到新一层的镜像的目标路径中，源路径可以有多个，也可以是通配符。

目标路径可以是镜像的绝对路径，也可是相对于 `工作目录` 的相对路径（`工作目录`可以使用 `WORKDIR` 指令来指定 ），目标目录不需要先行创建，不存在会在复制前创建。

## ADD
ADD的源路径可以是一个URL，Docker会自动去下载，并复制到目标路径。

但是如果下载的是一个压缩包，则需要另外写RUN命令解压缩。所以还不如直接写RUN命令，通过wget等下载同时解压缩。

如果源路径是一个tar压缩文件，压缩格式格式为 `gzip` 、`bzip`以及`xz`情况下，ADD会解压缩后复制到目标路径。所以如果不想解压缩，就不能使用ADD。

## CMD

CMD也有两种格式：
  
  - shell格式： `CMD <命令>`
  - exec 格式： `CMD ["可执行文件"， "参数1", "参数2", ...]`
  - 参数列表格式： `CMD ["参数1", "参数2", ...]`，在指定了 `ENRTYPOINT` 指令后，使用CMD指定具体的参数。

在指令格式上，推荐使用 exec 格式，这类格式会被转为JSON数组，因此一定要用双引号而不能用单引号。

shell 格式的命令会被转为 `sh -c` 的参数进行执行。比如: `CMD echo $HOME`,在实际执行中，会变为: `CMD ["sh", "-c", "ECHO $HOME"]`。

所以，使用shell格式就要注意前台执行和后台执行的区别。`sh -c`执行完毕，Docker容器主进程也就执行完毕退出，Docker 容器也就退出了。

## ENTRYPOINT

入口点，目的和CMD一样，都是指定容器启动程序和参数。

ENTRYPOINT在运行时也可以被取代，不过要通过`docker run --entrypoint`指定。

当指定了`ENTRYPOINT`之后，`CMD`的含义变为了`ENTRYPOINT`的参数。

## VOLUME 定义匿名卷

格式为:

- VOLUME ["<路径1>", "<路径2>", ...]
- VOLUME <路径>

为了不将外部存储的数据写入到容器中，保持容器的无状态化，可以定义一些匿名卷，比如：

```shell
VOLUME /data
```

这样在任何情况下，写入到 `/data` 目录下的信息都不会记入进容器的存储层，从而保持了容器的无状态化，在容器运行时，可以覆盖此值，比如：

```shell
docker run -d -v mydata:/data xxx
```

这样 `mydata` 就代替了Dockerfile中定义的匿名卷`/data`。

## EXPOSE 声明端口

EXPOSE 仅仅只是声明容器可能打算使用这些端口，这样有这几个好处：

- 帮助镜像使用者理解这个镜像的守护端口
- 另一个用处则是在运行时使用随机端口映射时,也就是 `docker run -P`
时,会自动随机映射 `EXPOSE` 的端口。


## WORKDIR 指定工作目录

每一个指令相当于一层，每一层之间互不干扰，所以有些命令会理解错误导致失败，比如：

```shell
RUN cd /app
RUN echo "hello world" > world.txt
```

然后你会发现，在容器内，根本找不到 `/app/world.txt` 文件，这就是因为两个指令相处的环境是不一样的，第一个指令和第二个指令并没有关系，这个时候就应该指定工作目录。

## USER 指定用户

格式： `USER <用户名>[:<用户组>]`

USER命令改变在其之后的指令的执行身份。

## HEALTHCHECK 健康检查

格式：

- `HEALTHCHECK [选项] CMD [命令]` ：设置健康检查的命令
- `HEALTHCHECK NONE` : 如果基础镜像有健康检查指令，使用此命令可以屏蔽

当在一个镜像指定了 HEALTHCHECK 指令后,用其启动容器,初始状态会为
starting ,在 HEALTHCHECK 指令检查成功后变为 healthy ,如果连续一定次数失败,则会变为 unhealthy 。

`HEALTHCHECK` 支持以下选项:

- `--interval=<间隔>`: 两次健康检查的时间间隔，默认30s
- `--timeout=<间隔>`: 健康检查的超时时间，超出后视为失败，默认30s
- `--retries=<次数>`: 当连续失败指定次数之后，状态变为 `unhealthy`，默认3次。

在 `HEALTHCHECK [选项] CMD [命令]` 中CMD后面，也是分为shell格式和exec格式，命令的返回值决定这次检查的成功或者是失败。0是成功，1是失败，2是保留不使用这个值。

为了排除故障，健康检查的输出(stdout、stderr)都会存在健康状态里面，可以使用 `docker inspect` 来查看:

```shell
docker inspect --format '{{json .State.Health}}' web|python -m json.tool
```

## ONBUILD 为他人做嫁衣裳

这个命令后面跟的指令在构建时并不会执行，但是当别人以此命令为基础命令进行构建时就会执行。




