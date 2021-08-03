```
docker run 后的命令会覆盖CMD中的命令，RUN中的命令只会在docker build的时候执行

docker run -it 镜像名 command sh -s "执行命令"
```
#### docker简单配置
>配置
```
Docker:   账户mxq218
配置中国镜像：
    vi /etc/docker/daemon.json 不存在创建
    {
      "registry-mirrors": ["http://hub-mirror.c.163.com"]
    }
    或者：docker pull registry.docker-cn.com/myname/myrepo:mytag
    systemctl restart docker 重启docker
1、dockerClient客户端
2、Docker Daemon守护进程
3、Docker Image镜像
4、DockerContainer容器
```
#### docker命令
>镜像命令
```
    镜像名 = 仓库:标签（标签不写默认是：latest）
    查找镜像
        docker search httpd
    列出本机所有镜像
        docker image ls  或者docker images
    删除镜像文件
        docker image rm 镜像名  
        docker rmi 镜像名/镜像ID
        删除所有镜像
        docker rmi $(docker images -q)
    抓取镜像
        docker image pull  镜像名
        docker pull 
    查看镜像的历史记录
            docker histoty 镜像名  
    发布image文件
        docker login  docker logout
        为本地的image标注用户名和版本  
        用户名必须是docker上注册的账号  
        改名后的镜像id和之前的一样
        docker image tag [本地镜像名] [username]/[repository]:[tag]
    	    docker image tag nginx mxq218/nginx:v168
            或者在构建的时候
                docker image build -t [username]/[repository]:[tag] .
        docker image push [username]/[repository]:[tag]
            docker image push mxq218/nginx:v168
            
    构建镜像
        docker build -t 镜像名称:镜像标签 上下文路径
            忽略文件.dockerignore
            1、默认使用上下文路径中的Dockerfile文件
            2、指定Dockerfile路径
                -f 指定Dockerfile文件路径（可以不用Dockerfile为文件名）
            
        打包镜像
            docker save 镜像名 | gzip > alpine-latest.tar.gz
            docker save -o test.tar 镜像:版本
        加载打包镜像
            docker load -i alpine-latest.tar.gz
            docker load<test.tar
        
        从标准输入中读取Dockerfile进行构建
            docker build  -<  Dockerfile路径
            或
            cat Dockerfile | docker build -
```
>容器命令
```
    运行镜像（会产生一个容器）本地没有会从远程拉取经镜像
        docker run 镜像名 运行命令  
            docker container run 镜像名 运行命令
        docker run -it 镜像名 command  进入交互式容器
        docker run -d  镜像名 command  后台模式启动容器
            ps: -t: 在新容器内指定一个伪终端或终端。
                -i: 允许你对容器内的标准输入 (STDIN) 进行交互。
                --name 指定一个容器名
    查看正在运行的容器
        docker container ls
        docker ps    正在运行的容器
        docker container ls -l
        docker ps -l  查看最新创建的容器，只列出最后创建的。
        docker ps -a 查看所有容器 
        docker ps -q 只显示容器id
    查看容器内标准输出
        docker container logs [containerID]
        docker logs [containerID]
    停止容器
        docker container stop/kill [containerID]
        docker stop [container id]
    启动停止的容器
        docker container start [containerID]
        docker start/restart [container id]
    进入正在运行的容器
        docker exec -it [container id] /bin/bash
        docker attach [container id]  如果从这个容器退出，会导致容器的停止。
    从容器内退出
        exit     容器停止退出
        ctrl+P+Q 容器不停止退出
        ctrl+D   容器不停止退出
    删除容器
        docker container rm [container id]
        docker rm [container id]
        强制删除    
            docker rm -f [container id]
        批量删除操作：
            1、docker container rm $(docker ps -a|grep Exited|awk '{print $1}')
                docker ps -a | grep Exited | awk '{print $1}' 查询出退出的容器的id，（匹配第一个字段 awk '{print $1}'）
                xargs 是给命令传递参数的一个过滤器
                    docker ps -aq | xargs docker rm
            2、docker container rm $(docker ps -a -q) 
            3、docker container rm $(docker ps -qf status=exited) 
                docker ps -qf   -f筛选条件 
            4、docker container prune 删除孤立的容器
    查看容器详情
        docker inspect 容器名/容器id
    查看容器具体改动
        docker container diff  [containerID]
    查看容器内运行的进程
        docker top [containerID]
    加上容器的存储层，并构成新的镜像  新镜像id与之前的不一样
        docker container commit 
        docker commit options [containerID]/容器名 镜像名
            options:
                -a :提交的镜像作者；
                -c :使用Dockerfile指令来创建镜像；
                -m :提交时的说明文字；
                -p :在commit时，将容器暂停
    进入正在运行的容器
        docker exec -it [containerID] command
        docker exec -it [containerID] ls -l /var 直接查看容器目录
    
    从一个正在运行的容器中拷贝文件
        docker container cp 
        docker cp [containerID]:容器内目录  宿主机目录
        
    查看容器端口映射
        docker port 容器id  
        docker port 容器id  端口号   端口配置

    导出导入容器
        导出容器快照到本地文件
            docker export [container id] > nginx.tar 导出容器快照到本地文件 nginx.tar。
        导入容器快照为镜像
            docker import nginx-test.tar nginx:v1
            cat nginx-test.tar | docker import - nginx:v1
            
    容器链接：--link
        docker run --link的作用
        docker run --link可以用来链接2个容器，使得源容器（被链接的容器）和接收容器（主动去链接的容器）之间可以互相通信，并且接收容器可以获取源容器的一些数据，如源容器的环境变量
        --link <name or id>:alias 其中，name和id是源容器的name和id，alias是源容器在link下的别名。
        eg:
            docker run -dit -p 8080:80 --name myng nginx
            docker run -dit --link myng:mng alpine
            在alpine中就可以通过mng访问nginx,mng就是nginx的域名
            可以在alpine的Host文件中查看映射关系
    容器卷
 ```
 ####   
 >docker build -t 镜像名称:镜像标签 上下文路径
 ```
 创建Dockerfile：每一行都会产生一个镜像分层，可以使用\输入多行命令
    FROM 镜像
    MAINTAINER 镜像维护者的姓名和邮箱地址
        
    RUN（docker build时运行，可以有多个） 用于执行后面跟着的命令行命令
        1、shell格式
            RUN apt-get update \
                && apt-get install -y vim
        2、exec格式
            RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline
    COPY 复制指令，从上下文目录中复制文件或者目录到容器里指定路径
        格式：
            COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
            COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]
        [--chown=<user>:<group>]：可选参数，用户改变复制到容器内文件的拥有者和属组。
        eg:
            COPY dist /var/www/html 会直接把dist目录下的文件复制到/var/www/html
    ADD 格式与COPY一致
        在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>
    CMD 容器启动时默认执行的命令，docker run时运行，只能有一个，多个仅最后一个生效
        格式：shell exec
        CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖。
    ENTRYPOINT
        类似于 CMD 指令，但其不会被 docker run的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。
        格式：exec
        可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参
            ENTRYPOINT ["nginx", "-c"] # 定参
            CMD ["/etc/nginx/nginx.conf"] # 变参 
    ENV 环境变量
        格式：
            ENV <key> <value>
            ENV <key1>=<value1> <key2>=<value2>...
        RUN NODE_VERSION 7.2.0，在后续的指令中可以通过 $NODE_VERSION 引用
            ENV NODE_VERSION 7.2.0
            RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz"
    ARG 
        ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。
        构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖。
    VOLUME 定义匿名数据卷
        格式：
            VOLUME ["<路径1>", "<路径2>"...]
            VOLUME <路径>
        在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。
    EXPOSE 仅仅只是声明端口。但不映射到宿主机
        格式：
            EXPOSE <端口1> [<端口2>...]
        docker run -P 时，会自动随机映射 EXPOSE 的端口
    WORKDIR 指定工作目录
        WORKDIR <工作目录路径>
    USER
        用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在
        格式：USER <用户名>[:<用户组>]
    HEALTHCHECK
        用于指定某个程序或者指令来监控 docker 容器服务的运行状态
        格式
    ONBUILD
        格式：ONBUILD <其它指令>
        用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 FROM test-build ，这是执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令
 ```
 
#### 容器数据卷（目录映射）
```
 docker run 
    数据同步
        docker run -it -v 宿主机绝对路径:容器内目录 镜像名
    禁止容器内目录写操作
        docker run -it -v 宿主机绝对路径:容器内目录:ro 镜像名
 
 Dockerfile
    From centos
    VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
     
 ```
 >数据卷容器：命名的容器挂载数据卷，其他容器通过挂载这个（父容器）实现数据共享，挂载数据卷的容器，称之为数据卷容器
 ```
    上一步Dockerfile创建容器数据卷
        docker run -it --name dc01 mxq218/centos:v1 /bin/bash
    继承容器数据卷
        docker run -it --name dc02 --volumes-from dc01 mxq218/centos:v1 /bin/bash
        
    数据卷的生命周期一直持续到没有容器使用它为止
 ```
#### 自定义volume
```

```