#### docker-machine
>Docker Machine可以直接管理远程的docker主机，不论是虚拟机还是云主机
```
    创建主机
        docker-machine create --driver virtualbox default
    查看
        docker-machine ls
    管理某个主机
        docker-machine env default
        eval $(docker-machine env default) #设置当前的环境变量为某一个主机的信息
        
        然后就可以使用docker管理
    取消当前环境变量
        eval $(docker-machine env -u)
    设置一台新主机的环境变量：
        eval $(docker-machine env new-host)
    进入机器
        docker-machine ssh test

设置环境变量后,当前命令行窗口操作docker都将放入虚拟机
```
#### 修改主机Ip
```
echo "ifconfig eth1 192.168.99.100 netmask 255.255.255.0 broadcast 192.168.99.255 up" | docker-machine ssh [your machine name] sudo tee /var/lib/boot2docker/bootsync.sh > /dev/null

docker-machine regenerate-certs [your machine name]
```
#### swarm集群
```
    创建集群主机：
        docker-machine create -d virtualbox manager1
        docker-machine create -d virtualbox manager2
        docker-machine create -d virtualbox worker1
        docker-machine create -d virtualbox worker2

    在主节点 初始化swarm集群
        docker swarm init --advertise-addr ip
        运行后会生成一条以worker节点形式加入主节点的命令
    在主节点服务器生成以worker节点加入主节点的命令
        docker swarm join-token worker
    在主节点服务器生成以manager节点加入主节点的命令
        docker swarm join-token manager
    离开一个节点
        docker swarm leave
        
    在主节点上查看节点
        docker node ls
    在主节点上提升worker为manager
        docker node promote 节点名
        demote降级

    网络命令
        docker network ls   #查看网络
        docker network create --driver overlay 网络名  #在Swarm集群中创建overlay网络

    搭建单个服务
        docker service create --replicas 1 --network 网络名 -d -p 8080:80 --name mynginx nginx  
            #创建服务， --replicas 4表示创建服务的实例个数（默认是一个）
            #--network 指定服务网络
        docker service ls #查看服务列表
        docker service ps 服务名 #查看部署情况
        docker service inspect --pretty 服务名 #查看服务详情
        docker service rm 服务名 #删除服务

        docker service scale 服务名=2 # 服务扩展到俩个节点。

    部署多个集群服务 Docker Stack 
        docker-compose.yml
            version: "3"
            services:
            nginx:
                image: nginx
                ports:
                - 8088:80
                deploy:
                mode: replicated
                replicas: 4
            visualizer: #图形界面
                image: visualizer
                ports:
                - "8080:8080"
                volumes:
                - "/var/run/docker.sock:/var/run/docker.sock"
                deploy:
                replicas: 1
                placement:
                    constraints: [node.role == manager]
            portainer: #图形界面
                image: portainer
                ports:
                - "9000:9000"
                volumes:
                - "/var/run/docker.sock:/var/run/docker.sock"
                deploy:
                replicas: 1
                placement:
                    constraints: [node.role == manager]
        执行 docker stack deploy -c docker-compose.yml stack名
        docker stack ls #查看stack列表
```