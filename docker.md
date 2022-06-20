1.软件跨环境迁移问题 

2.安装docker

```text
# 1、yum 包更新到最新 
yum update
# 2、安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的 
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3、 设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 4、 安装docker，出现输入的界面都按 y 
yum install -y docker-ce
# 5、 查看docker版本，验证是否验证成功
docker -v
```

Docker架构

配置Docker镜像加速

命令

```text
docker自身
systemctl start docker//stop restart status enable 设置开机启动docker
镜像
docker images 查看全部镜像 -q 
docker search redis
docker pull redis
docker rmi id
docker 容器相关
docker run -it --name=c1 centos:7   //it退出容器后容器自动关闭
exit
docker ps 查看正在运行的容器 -a
docker run -id --name=c1 centos:7  //id容器不关闭
docker run -id --name=c2 centos:7//--name为容器命名

```

数据卷

```text
数据卷是宿主机中的一个目录或文件
当数据卷合容器目录绑定后，对方的修改会立即同步
一个数据卷可以被多个容器同时挂载
一个容器也可以被多个数据卷挂载
dOcker run -it --name=c1 -v /root/data:/root/data_container centos:7 
docker inspect c1//查看容器的细节
docker run -it --name=c3 -v/volume centos:7 /bin/bash
docker run -it --name=c1 --**volume** -from c3 centos:7 /bin/bash
docker run -it --name=c2 --**volume-from c3 centos:7 /bin/bash
```

docker应用部署

```text

部署mysql
部署tomacat
部署nginx
部署redis
```

dockerfile

```dockerfile
docker的镜像的本质是一个分层的文件系统
docker的centos镜像复用了操作系统的bootsgs，只有rootfs合其他镜像
镜像制作
docker commit 容器id 镜像名称：版本号
docker save -o 压缩文件名称 镜像名称：版本号
docker load -i 压缩文件名称

dockfile
from centos:7
maintainer pinkhat <@pinkhat@163.com>
run yum -y install vim
workdir /usr
cmd /bin/bash
docker bulid -f ./centos_dockerfile -t itheima_centos:1 .
docker run -it --name=c2 itheima_centos:1

```

使用docker compose编排nginx+springboot 项目

```docker
# Compose目前已经完全支持Linux、Mac OS和Windows，在我们安装Compose之前，需要先安装Docker。下面我 们以编译好的二进制包方式安装在Linux系统中。 
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置文件可执行权限 
chmod +x /usr/local/bin/docker-compose
# 查看版本信息 
docker-compose -version

# 二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose

mkdir ~/docker-compose
cd ~/docker-compose

编写 docker-compose.yml 文件
version: '3'
services:
  nginx:
   image: nginx
   ports:
    - 80:80
   links:
    - app
   volumes:
    - ./nginx/conf.d:/etc/nginx/conf.d
  app:
    image: app
    expose:
      - "8080"
创建./nginx/conf.d目录
mkdir -p ./nginx/conf.d
在./nginx/conf.d目录下 编写itheima.conf文件
server {
    listen 80;
    access_log off;

    location / {
        proxy_pass http://app:8080;
    }
   
}
在~/docker-compose 目录下 使用docker-compose 启动容器
docker-compose up
测试访问
http://192.168.149.135/hello


# 1、拉取私有仓库镜像 
docker pull registry
# 2、启动私有仓库容器 
docker run -id --name=registry -p 5000:5000 registry
# 3、打开浏览器 输入地址http://私有仓库服务器ip:5000/v2/_catalog，看到{"repositories":[]} 表示私有仓库 搭建成功
# 4、修改daemon.json   
vim /etc/docker/daemon.json    
# 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将私有仓库服务器ip修改为自己私有仓库服务器真实ip 
{"insecure-registries":["私有仓库服务器ip:5000"]} 
# 5、重启docker 服务 
systemctl restart docker
docker start registry
# 1、标记镜像为私有仓库的镜像     
docker tag centos:7 私有仓库服务器IP:5000/centos:7
 
# 2、上传标记的镜像     
docker push 私有仓库服务器IP:5000/centos:7

#拉取镜像 
docker pull 私有仓库服务器ip:5000/centos:7
```

docker容器虚拟化与传统虚拟机据有下退的资源隔离合分配优势

不同

容器虚拟化的是操作系统，虚拟机虚拟的是硬件

传统虚拟机可以运行不同的操作系统，容器只能运行同一类型操作系统





