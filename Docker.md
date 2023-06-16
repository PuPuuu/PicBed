# Docker

```
docker system df #查看镜像、容器、数据卷所占用的空间
```

## 镜像篇

### dockerfile

#### 指令

- **FROM** 指定基础镜像

- **RUN** 用来执行命令行命令，有两种格式

  - *shell*格式：

    ```dockerfile
    RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
    ```

  - *exec*格式：

    ```dockerfile
    RUN ["可执行文件", "参数1", "参数2"]
    ```

  > Dockerfile 中每一个指令都会建立一层,连续多次使用RUN命令时，可以使用 `\`和`&&`将多条命令整合，使之构建成一层

- **COPY** 复制文件

  ```dockerfile
  COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]
  ```

- **CMD** 容器启动命令

  - *shell*格式：

    ```dockerfile
    CMD <命令>
    ```

  - *exec*格式：

    ```dockerfile
    CMD ["可执行文件", "参数1", "参数2"...]
    ```

  - 参数列表格式（在指定了 `ENTRYPOINT` 指令后，用 `CMD` 指定具体的参数）：

    ```
    CMD ["参数1", "参数2"...]
    ```

  在指令格式上，一般推荐使用 `exec` 格式，这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 `"`，而不要使用单引号

  如果使用 `shell` 格式的话，实际的命令会被包装为 `sh -c` 的参数的形式进行执行

  ```dockerfile
  CMD echo $HOME
  #实际执行中，会将其变更为
  CMD [ "sh", "-c", "echo $HOME" ]
  ```

  > 对于容器而言，其启动程序就是容器应用进程，容器就是为了主进程而存在的，主进程退出，容器就失去了存在的意义，从而退出，其它辅助进程不是它需要关心的东西。

- **ENTRYPOINT** `ENTRYPOINT` 的目的和 `CMD` 一样，都是在指定容器启动程序及参数。`ENTRYPOINT` 在运行时也可以替代，不过比 `CMD` 要略显繁琐，需要通过 `docker run` 的参数 `--entrypoint` 来指定。当指定了 `ENTRYPOINT` 后，`CMD` 的含义就发生了改变，不再是直接的运行其命令，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT` 指令

  - **场景一** 让镜像变成像命令一样使用。将CMD的命令转移到ENTRYPOINT，使在启动镜像时可以添加额外参数，如：

    ```dockerfile
    FROM ubuntu:18.04
    RUN apt-get update \
        && apt-get install -y curl \
        && rm -rf /var/lib/apt/lists/*
    CMD [ "curl", "-s", "http://myip.ipip.net" ]
    ==========after
    FROM ubuntu:18.04
    RUN apt-get update \
        && apt-get install -y curl \
        && rm -rf /var/lib/apt/lists/*
    ENTRYPOINT [ "curl", "-s", "http://myip.ipip.net" ]
    ==========
    此时可以使用
    docker run myip -i #使用-i额外参数
    ```

  - **场景二** 应用运行前的准备工作。启动容器就是启动主进程，但有些时候，启动主进程前，需要一些准备工作。这种情况下，可以写一个脚本，然后放入 `ENTRYPOINT` 中去执行，而这个脚本会将接到的参数（也就是 `<CMD>`）作为命令，在脚本最后执行。

    ```dockerfile
    FROM alpine:3.4
    ...
    RUN addgroup -S redis && adduser -S -G redis redis
    ...
    ENTRYPOINT ["docker-entrypoint.sh"]
    
    EXPOSE 6379
    CMD [ "redis-server" ]
    ```

- **ARG 构建参数** 构建参数和 `ENV` 的效果一样，都是设置环境变量。所不同的是，`ARG` 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。

- **VOLUME 挂载存储** 

  ```dockerfile
  VOLUME /data
  docker run -d -v mydata:/data xxxx #使用了 mydata 这个命名卷挂载到了 /data 这个位置
  ```

- **WORKFILE 指定工作目录** 使用 `WORKDIR` 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，如该目录不存在，`WORKDIR` 会帮你建立目录。

  ```dockerfile
  # 如果你的 WORKDIR 指令使用的相对路径，那么所切换的路径与之前的 WORKDIR 有关
  WORKDIR /a
  WORKDIR b
  WORKDIR c
  
  RUN pwd # /a/b/c
  ```

### 常用命令

#### docker image

```bash
docker image prune #删除未使用的镜像
```

#### docker diff

```bash
docker diff #查看镜像修改
```

#### docker commit

```bash
docker commit #在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像(调试用)

docker commit \
    --author "Tao Wang <twang2218@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
```

#### docker build

```dockerfile
docker build [选项] <上下文路径/URL/->

# 当构建的时候，用户会指定构建镜像上下文的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件

===
docker build -t hello-world https://github.com/docker-library/hello-world.git#master:amd64/hello-world 

#指定了构建所需的 Git repo，并且指定分支为 master，构建目录为 /amd64/hello-world/，然后 Docker 就会自己去 git clone 这个项目、切换到指定分支、并进入到指定目录后开始构建。
```

#### Docker save/load

```dockerfile
docker save alpine -o filename # 保存镜像

docker save alpine | gzip > alpine-latest.tar.gz # 压缩保存

docker load -i alpine-latest.tar.gz # 加载镜像

# 结合这两个命令以及 ssh 甚至 pv 的话，利用 Linux 强大的管道，我们可以写一个命令完成从一个机器将镜像迁移到另一个机器，并且带进度条的功能
docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'
```

### 参考链接

- `Dockerfile` 官方文档：https://docs.docker.com/engine/reference/builder/

- `Dockerfile` 最佳实践文档：https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

- `Docker` 官方镜像 `Dockerfile`：https://github.com/docker-library/docs

## 容器篇

### 启动

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（`exited`）的容器重新启动。

#### 新建启动

所需要的命令主要为 `docker run`。

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

- 检查本地是否存在指定的镜像，不存在就从 ***registry*** 下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

#### 启动已终止

可以利用 `docker container start` 命令，直接将一个已经终止（`exited`）的容器启动运行。

### 进入容器

- **attach**

  ```bash
  $ docker run -dit ubuntu
  243c32535da7d142fb0e6df616a3c3ada0b8ab417937c853a9e1c251f499f550
  
  $ docker container ls
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
  243c32535da7        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           nostalgic_hypatia
  
  $ docker attach 243c
  root@243c32535da7:/#
  ```

- **exec**

  ```bash
  $ docker run -dit ubuntu
  69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6
  
  $ docker container ls
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
  69d137adef7a        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           zealous_swirles
  
  $ docker exec -i 69d1 bash
  ls
  bin
  boot
  dev
  ...
  
  $ docker exec -it 69d1 bash
  root@69d137adef7a:/#
  ```

### 导入导出容器

```bash
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
7691a814370e        ubuntu:18.04        "/bin/bash"         36 hours ago        Exited (0) 21 hours ago                       test
$ docker export 7691a814370e > ubuntu.tar # 导出容器快照

$ cat ubuntu.tar | docker import - test/ubuntu:v1.0 # 导入容器
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```

### 删除

```bash
$ docker container rm trusting_newton # 来删除一个处于终止状态的容器
trusting_newton

$ docker container prune # 清理掉所有处于终止状态的容器
```

## 数据管理

```bash
docker volume create my-vol # 创建数据卷
docker volume ls # 查看数据卷
docker volume inspect my-vol # 查看数据卷信息
docker run -d -P \
    --name web \
    # -v my-vol:/usr/share/nginx/html \
    --mount source=my-vol,target=/usr/share/nginx/html \
    nginx:alpine # 启动一个挂在数据卷的容器
docker inspect web # 查看容器详细信息
docker volume rm my-vol # 删除数据卷
docker volume prune # 类似清理无用容器
```

```bash
# 主机目录挂载
docker run -d -P \
    --name web \
    # -v /src/webapp:/usr/share/nginx/html \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html \
    nginx:alpine

# Docker 挂载主机目录的默认权限是 读写，用户也可以通过增加 readonly 指定为 只读
docker run -d -P \
    --name web \
    # -v /src/webapp:/usr/share/nginx/html:ro \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html,readonly \
    nginx:alpine
```

## 网络相关

```
docker run -d -P nginx:alpine # -P为随机映射，-p为指定端口映射
docker run -d -p 80:80 nginx:alpine # hostPort:containerPort
docker run -d -p 127.0.0.1:80:80/udp nginx:alpine # 指定udp端口映射
docker port fa 80 # 查看当前映射的端口配置，也可以查看到绑定的地址
# 可以多次使用来绑定多个端口
docker run -d \
    -p 80:80 \
    -p 443:443 \
    nginx:alpine
```

