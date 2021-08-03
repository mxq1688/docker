#### 网络命令
```
docker network ls #查看网络列表
docker network inspect 'networkId' # 查看网络详情
```
#### docker-compose命令
```
和docker基本类似
  docker-compose images
  docker-compose ps

```
#### docker-compose.yml
>定义和运行由多个容器组成的应用
```
version: "3"
services:
  nginx:
    image: nginx
    ports:
      - "80:80"
    depends_on:
      - php
    volumes:
      - "$PWD/nginx/default.conf:/etc/nginx/conf.d/default.conf"
      - "$PWD/html:/usr/share/nginx/html"
    networks:
      - app_net
    container_name: "compose-nginx"
  php:
    build: ./php
    image: php:fpm
    ports:
      - "9000:9000"
    volumes:
      - "$PWD/html:/var/www/html"
    networks:
      - app_net
    container_name: "compose-php"
  mysql:
    image: mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=123456
    networks:
      app_net:
        ipv4_address: 10.10.10.1
    container_name: "compose-mysql"
networks:
  app_net:
    driver: bridge
    ipam:
      config:
        - subnet: 10.10.0.0/16
volumes:
  redis-data:
```
#### 参数详解
```
build image
  如果同时指定了两者，会将build出来的镜像打上名为image标签，默认打上名为项目名_服务名的标签（如test_php）
  image优先级大于build,如果image指定的镜像存在直接使用，没定义image会使用默认的 项目名_服务名的标签
build:
  context: ./dir
  dockerfile: Dockerfile
  args:
    buildno: 1
  使用context指令指定Dockerfile所在文件夹的路径
  使用dockerfile指令指定Dockerfile文件名
  使用arg指令指定构建镜像时的变量，构建好的镜像内不存在此环境变量

depends_on:
  解决容器的依赖、启动先后的问题。

links:
  连接到其他容器。注意：不推荐使用该指令

network_mode
  设置网络模式。使用和docker run的--network参数一样的值。
  network_mode:"bridge"
  network_mode:"host"
  network_mode:"none"
networks:
  配置容器连接的网络。
  services:
    some-service:
      networks:
        - some-network
  networks:
    some-network:  

volumes:
  - /var/lib/mysql              # 只是指定一个路径，Docker 会自动在创建一个数据卷（这个路径是容器内部的）。
  - /opt/data:/var/lib/mysql    # 使用绝对路径挂载数据卷
  - ./cache:/tmp/cache          # 以 Compose 配置文件为中心的相对路径作为数据卷挂载到容器。
  - ~/configs:/etc/configs/:ro  # 使用用户的相对路径（~/ 表示的目录是 /home/<用户目录>/ 或者 /root/）。
  - dbdata:/var/lib/mysql       # # dbdata 为 volumes 顶级键定义的目录, 在此处直接调用
  针对第5条：
    在services同级设置volumes
      volumes:
        dbdata:   # 定义在 volume, 可在所有服务中调用

volumes_from:
  – service_name
  – container_name
  从另一个服务或容器挂载它的所有卷。

expose:
 - "3000"
 - "8000"
  暴露端口，但不映射到宿主机，只被连接的服务访问

environment:
  设置环境变量。你可以使用数组或字典两种格式。
  environment:
    RACK_ENV: development
    SESSION_SECRET:
  environment:
    - RACK_ENV=development
    - SESSION_SECRET  
```