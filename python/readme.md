#### 开发环境
>映射 ./site-packages:/usr/local/lib/python3.12/site-packages后无法使用pip
```
升级下
python -m ensurepip --upgrade
```
>宿主机运行py脚本
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