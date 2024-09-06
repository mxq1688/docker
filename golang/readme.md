#### 开发环境
```
go 安装的包持久化
    volumes:
        - /Users/meng/stu/docker/golang/pkg:/go/pkg
```
>宿主机运行
```
进入容器在运行
    docker exec -it <container_id> /bin/bash 

直接运行
    docker exec -it <container_id> go gun xxx/main.go
```
#### 生产环境
> 基于Dockerfile 创建镜像
```


```

brew install pkg-config
brew install libopusenc
brew install opusfile 