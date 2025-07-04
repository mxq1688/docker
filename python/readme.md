#### 开发环境
##### 方式1：持久化依赖目录
>映射 ./site-packages:/usr/local/lib/python3.12/site-packages后容器内无法使用pip
```
为什么：
    替换了容器内的完整Python包环境
    破坏了pip的工作环境

如何解决
    升级下python -m ensurepip --upgrade
```
##### 方式2：构建开发镜像
```
services:
  docker-python:
    build: .  # 使用Dockerfile构建镜像
    # image: "python:3.12-alpine"  # 注释掉
    volumes:
      - /Users/meng/work/yuan365_client_controller:/app
    # ... 其他配置保持不变

# 首次构建或依赖更新时
docker-compose build

# 正常启动
docker-compose up

```
#### 开发环境 宿主机运行py脚本
```
进入容器在运行
    docker exec -it <container_id> /bin/bash 
 
直接运行
    docker exec -it container_id python3 /path/to/script.py
```
#### 生产环境
> 基于Dockerfile 创建新镜像
```
requirements.txt

注意新build的镜像在开发环境下使用还是有上面映射的问题
```