## 使用 DOckerfile 定制镜像
Dockerfile是一个文本文件，包含一条一条指令，每条指令构建一层。

1. 在一个空白文件夹中，新建 `Dockerfile`:
    ```shell
    $ mkdir mynginx
    $ cd mynginx
    $ touch Dockerfile
    ```
    其内容为:
    ```shell
    FROM nginx
    RUN echo '<h1>Hello , Docker!</h1>' > /usr/share/nginx/html/index.html
    ```
2. 所谓定制镜像，那么一定是以一个镜像为基础，在其上进行定制，所以基础镜像一定要指定，而 `FROM` 就是指定基础镜像，所以在一个Dockerfile中， `FROM` 是必备的指令，且必须位于第一行。

3. Docker 存在一个特殊的镜像，名为: `scratch` 。这个镜像并不存在，它表示一个虚拟的镜像、空白的镜像:
    ```shell
    FROM scratch
    ...

    ```
    如果以 `scratch` 为基础镜像，则表示接下来所写的指令，是作为镜像的第一层。

4. `RUN` 命令是用来执行命令行命令的，格式有两种：

    - shell 格式: `RUN <shell 命令>`，就像直接在命令行输入命令一样

    - exec 格式：`RUN ["可执行文件"， “参数1”， “参数2”]`

    注意，尽可能的使用最少的RUN命令，因为每一个RUN都会构造一层，这是没有必要的，因为Union FS是有最大层数限制的，可以使用 `&&` 连接的命令尽量使用，并且最后应该清理掉无关的文件。举个栗子：

    ```shell
    # 不好的写法，构建了7层
    FROM debian:jessie
    RUN apt-get update
    RUN apt-get install -y gcc libc6-dev make
    RUN wget -O redis.tar.gz "http://download.redis.io/releases/redi
    s-3.2.5.tar.gz"
    RUN mkdir -p /usr/src/redis
    RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
    RUN make -C /usr/src/redis
    RUN make -C /usr/src/redis install

    # 好的写法，仅仅一层，并且清理了无关文件
    FROM debian:jessie
    RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/r
    edis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-component
    s=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps

    ```

5. 构建镜像：
    ```shell
    # -t name:tag
    $ docker build -t nginx:v3 .
    Sending build context to Docker daemon  2.048kB
    Step 1/2 : FROM nginx
    ---> 62f816a209e6
    Step 2/2 : RUN echo '<h1>Hello , Docker!</h1>' > /usr/share/nginx/html/index.html
    ---> Running in 3528c3def08f
    Removing intermediate container 3528c3def08f
    ---> 4ab1eb69ae5d
    Successfully built 4ab1eb69ae5d
    Successfully tagged nginx:v3

    ```
    从输出可以看到一条指令执行一步。

6. 