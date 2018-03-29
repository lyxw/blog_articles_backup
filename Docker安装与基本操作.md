# Docker安装与基本操作

## 0x01 核心概念

**Docker镜像**类似于虚拟机镜像，可以理解为一个面向Docker引擎的只读模板，包含了文件系统。镜像是创建Docker容器的基础。

**Docker容器**类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。容器是从镜像创建的应用运行实例，可以将其启动、停止、删除，而这些容器都是相互隔离、互不可见的。
镜像自身是只读的。容器从镜像启动的时候，Docker会在镜像的最上层创建一个可写层，镜像本身将保持不变。

**Docker仓库**类似于代码仓库，是Docker集中存放镜像文件的场所。Docker仓库分为公开仓库和私有仓库。

**注册服务器（Registry）**是存放仓库的地方，其上往往存放着多个仓库。每个仓库集中存放某一类镜像，往往包括多个镜像文件，通过不同的标签（tag）来进行区分。

## 0x02 Docker安装

### 1、Ubuntu

Ubuntu 14.04 版本官方软件源中已经自带了Docker包，较低版本需要先进行内核更新并重启系统：

```
$ sudo apt-get update
$ sudo apt-get install -y linux-image-generic-lts-raring linux-headers-generic-lts-raring
$ sudo reboot
```

安装Docker

```
$ sudo apt-get update
$ sudo apt-get install -y docker.io
$ sudo ln -sf /usr/bin/docker.io /usr/bin/docker
$ sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io
```

系统默认的Docker安装包相对较旧，可通过以下方式从Docker官方源安装最新版本。

```
$ sudo apt-get install apt-transport-https
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
$ sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
$ sudo apt-get update
$ sudo apt-get -y lxc-docker
```

### 2、CentOS

#### 2.1、CentOS 6

```
$ sudo yum install -y http://mirrors.yun-idc.com/epel/6/i386/epel-release-6-8.noarch.rpm
$ sudo yum install -y docker-io
```

#### 2.2、CentOS 7

```
$ sudo yum install -y docker
```

更新方式：自行通过源码编译安装；下载二进制文件更新。

### 3、Windows

Docker通过虚拟机方式支持Windows 7和8，前提是主机的CPU支持硬件虚拟化。

* 从[https://docs.docker.com/installation/windows/](https://docs.docker.com/installation/windows/)下载最新官方**Docker for Windows Installer**。

* 运行Installer，该过程将安装**VirtualBox**，**MSYS-git**，**boot2docker Linux ISO镜像**，以及**Boot2Docker管理工具**。

* 打开桌面的**Boot2Docker Start**程序，或者用以下命令：`Program Files > Boot2Docker for Windows`。此初始化脚本在第一次运行时需要输入一个SSH Key Passphrase（用于SSH密钥生成的口令），也可直接跳过。

* 此时Boot2Docker Start程序将连接至虚拟机中的Shell会话，Docker已经运行起来了。

### 4、Mac OS

Docker支持Mac OS 10.6 Snow Leopard及以上版本的Mac OS，同样需要Boot2Docker工具的支持。

* 从[https://docs.docker.com/installation/mac/](https://docs.docker.com/installation/mac/)下载最新官方**Docker for OS X Installer**。

* 运行安装包，该过程将安装一个**VirtualBox虚拟机**，**Docker本身**以及**Boot2Docker管理工具**。

* 安装成功，找到**Boot2Docker**并运行它，现在进行Boot2Docker的初始化：

```
$ boot2docker init
$ boot2docker start
$ $(boot2docker shellinit)
```

* 看到虚拟机在命令行窗口中启动运行，并显示Docker的启动信息，则说明Docker安装成功。当虚拟机初始化完毕后，可以使用`boot2docker stop`和`boot2docker start`来控制它。

* 注意：如果在命令行中看到如下提示信息：

```
To connect the Docker client to the Docker daemon, please set: export DOCKER_HOST=tcp://192.168.59.103:2375
```

可以执行提示信息中的语句：`export DOCKER_HOST=tcp://192.168.59.103:2375`在系统环境变量中设置Docker的主机地址。

## 0x03 docker基本操作

### 1、镜像

#### 1.1、获取镜像

命令`docker pull NAME[:TAG]`获取镜像。如不显式的指定TAG，则默认选择latest标签。

```
[root@localhost test]# docker pull ubuntu:14.04
14.04: Pulling from library/ubuntu
bae382666908: Pull complete 
29ede3c02ff2: Pull complete 
da4e69f33106: Pull complete 
8d43e5f5d27f: Pull complete 
b0de1abb17d6: Pull complete 
Digest: sha256:6e3e3f3c5c36a91ba17ea002f63e5607ed6a8c8e5fbbddb31ad3e15638b51ebc
Status: Downloaded newer image for ubuntu:14.04
```

#### 1.2、查看镜像信息

命令`docker images`列出本地主机上已有的镜像。

```
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              747cb2d60bbe        2 weeks ago         122MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
```

命令`docker inspect IMAGE_ID` 可以获取该镜像的详细信息。

```
[root@localhost test]# docker inspect dea1945146b9
[
    {
        "Id": "sha256:dea1945146b96542e6e20642830c78df702d524a113605a906397db1db022703",
        "RepoTags": [
            "ubuntu:14.04"
        ],
        "RepoDigests": [
            "ubuntu@sha256:6e3e3f3c5c36a91ba17ea002f63e5607ed6a8c8e5fbbddb31ad3e15638b51ebc"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2017-09-13T23:26:23.127186526Z",
        "Container": "53c2a8c09f3e5b092cb05b323a382b2935476bc84353b102922c5c14b2bd3305",
        "ContainerConfig": {
            "Hostname": "53c2a8c09f3e",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:2013c3d8a4f2376ef9d2922271c8bc6a913cb3109a34966dbaefe5057a9da766",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "17.06.2-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:2013c3d8a4f2376ef9d2922271c8bc6a913cb3109a34966dbaefe5057a9da766",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 187971405,
        "VirtualSize": 187971405,
        "GraphDriver": {
            "Data": {
                "RootDir": "/var/lib/docker/overlay/9da37272f7684597b528479a3f91389673cd37351fd5ce8f51668d3df02af8b1/root"
            },
            "Name": "overlay"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:c47d9b229ca4eaf5d3b85b6fa7f794d00910a42634dd0fd5107a9a937b13b20f",
                "sha256:bf59e7acf5c4eefb6b27e7973a8afa6214f7a6da99c20a94195665ab52d442b2",
                "sha256:48daf661d621ef19b6df743b9fcfcb50044ba34d0b8b5161f4061dba49902e9b",
                "sha256:4e1e6ac5b9d6cde7dabfdaa6a0e8f5a8986c4f8bb63689085fdfaca0ccae698d",
                "sha256:7fb9ba64f896b3a7001af9604a44243cfa663c84e414cd298ee8bc754feb5aa1"
            ]
        }
    }
]
```

命令`docker tag REPOSITORY:TAG`可为本地镜像添加新的标签。

```
[root@localhost test]# docker tag ubuntu:latest latest_ubuntu:latest
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              747cb2d60bbe        2 weeks ago         122MB
latest_ubuntu       latest              747cb2d60bbe        2 weeks ago         122MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
```

#### 1.3、搜索镜像

`docker search`命令搜索远端仓库中的共享镜像。

```
参数：
--automated=false 仅显示自动创建的镜像
--no-trunc=false 输出信息不截断显示
-s，--starts=0 指定仅显示评价为指定星级以上的镜像
```

下面以搜索mysql为例：

```
[root@localhost test]# docker search mysql
NAME                                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                                                  MySQL is a widely used, open-source relati...   5160      [OK]       
mariadb                                                MariaDB is a community-developed fork of M...   1598      [OK]       
mysql/mysql-server                                     Optimized MySQL Server Docker images. Crea...   360                  [OK]
percona                                                Percona Server is a fork of the MySQL rela...   298       [OK]       
hypriot/rpi-mysql                                      RPi-compatible Docker Image with Mysql          72                   
zabbix/zabbix-server-mysql                             Zabbix Server with MySQL database support       62                   [OK]
centurylink/mysql                                      Image containing mysql. Optimized to be li...   53                   [OK]
sameersbn/mysql                                                                                        48                   [OK]
zabbix/zabbix-web-nginx-mysql                          Zabbix frontend based on Nginx web-server ...   36                   [OK]
tutum/mysql                                            Base docker image to run a MySQL database ...   27                   
schickling/mysql-backup-s3                             Backup MySQL to S3 (supports periodic back...   16                   [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mysql-5   ubuntu-16-nginx-php-phpmyadmin-mysql-5          16                   [OK]
centos/mysql-57-centos7                                MySQL 5.7 SQL database server                   13                   
linuxserver/mysql                                      A Mysql container, brought to you by Linux...   12                   
openshift/mysql-55-centos7                             DEPRECATED: A Centos7 based MySQL v5.5 ima...   6                    
centos/mysql-56-centos7                                MySQL 5.6 SQL database server                   6                    
frodenas/mysql                                         A Docker Image for MySQL                        3                    [OK]
dsteinkopf/backup-all-mysql                            backup all DBs in a mysql server                3                    [OK]
circleci/mysql                                         MySQL is a widely used, open-source relati...   2                    
cloudfoundry/cf-mysql-ci                               Image used in CI of cf-mysql-release            0                    
astronomerio/mysql-sink                                MySQL sink                                      0                    [OK]
cloudposse/mysql                                       Improved `mysql` service with support for ...   0                    [OK]
ansibleplaybookbundle/rhscl-mysql-apb                  An APB which deploys RHSCL MySQL                0                    [OK]
astronomerio/mysql-source                              MySQL source                                    0                    [OK]
jenkler/mysql                                          Docker Mysql package                            0                    
```

#### 1.4、删除镜像

命令`docker rmi IMAGE[IMAGE...]`IMAGE可以为标签或ID。使用镜像ID来删除时，会先尝试删除所有指向该镜像的标签，然后删除该镜像文件本身。

> 注意：当有该镜像创建的容器存在时，镜像文件默认是无法被删除的。

`docker ps -a`命令可以看到本机上存在的所有容器。

强制删除镜像用`docker rmi -f IMAGE`命令。使用`-f`参数来强制删除一个存在容器依赖的镜像会造成一些遗留问题。正确做法是先删除依赖镜像的所有容器，再来删除镜像。删除容器用`docker rm CONTAINER_ID`命令。

```
[root@localhost test]# docker ps -a
CONTAINER ID      IMAGE             COMMAND          CREATED           STATUS                            PORTS        NAMES
a40fcdb8f675      ubuntu:latest     "/bin/bash"      5 minutes ago     Exited (127) About a minute ago                agitated_lalande
[root@localhost test]# docker rm a40fcdb8f675
a40fcdb8f675
[root@localhost test]# docker rmi 747cb2d60bbe
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:506e2d5852de1d7c90d538c5332bd3cc33b9cbd26f6ca653875899c505c82687
Deleted: sha256:747cb2d60bbecbda48aff14a8be5c8b913ca69318a6067e57c697f8a78dda06e
Deleted: sha256:ec1fd849ff0a8f0aa2fd1acc29ad5dabbc79b89f63b74a4f54e31a7b0a100aa1
Deleted: sha256:e3f6dffa20cf36460d23bfb22e17be6e5339891f8537f32db79887caf832048b
Deleted: sha256:c213ffdc9f7032702de5a8e9045fcce2353b7221ef6bf4509e02005cfc858f58
Deleted: sha256:3fddf55a451aa43707518f2d8788c12ee5eb1f1e3075433f5bcf4d445d5c275d
Deleted: sha256:0f5ff0cf6a1c53f94b15f03536c490040f233bc455f1232f54cc8eb344a3a368
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
```

#### 1.5、创建镜像

基于已有镜像的容器创建使用命令`docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]`命令。

```
参数：
-a，--author="" 作者信息
-m，--message"" 提交信息
-p，--pause=true 提交时暂停容器运行
```

举例如下：

```
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
[root@localhost test]# docker run -ti ubuntu:14.04 /bin/bash
root@098ab9bc25f8:/# touch test
root@098ab9bc25f8:/# exit
exit
[root@localhost test]# docker commit -m "Added a new file" -a "Docker Test" 098ab9bc25f8 test
sha256:68ceff3107a82641378dee544caeed48e6d544b4d74b525051381b7db7479d35
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test                latest              68ceff3107a8        16 seconds ago      188MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
```

基于本地模板导入使用`cat 镜像压缩包 | docker import -REPOSITORY:TAG`命令。

```
[root@localhost test]# ls
ubuntu-14.04-x86_64-minimal.tar.gz
[root@localhost test]# cat ubuntu-14.04-x86_64-minimal.tar.gz | docker import - ubuntu:14.04-server
sha256:ae63a9e87332075323de0e2cb3e12dd6ea8c6078e006dd937dc0d670c4a8773d
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04-server        ae63a9e87332        12 seconds ago      215MB
test                latest              68ceff3107a8        About an hour ago   188MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
```

#### 1.6、存出镜像

命令`docker save -o 压缩包保存名 REPOSITORY:TAG`可存出镜像。

```
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04-server        ae63a9e87332        12 seconds ago      215MB
test                latest              68ceff3107a8        About an hour ago   188MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
[root@localhost test]# docker save -o ubuntu_server.tar ubuntu:14.04
[root@localhost test]# ls
ubuntu_server.tar
```

#### 1.7、载入镜像

命令`docker load --input 压缩包保存名`或者`docker load < 压缩包保存名`可从导出的文件中重新载入镜像。

```
[root@localhost test]# ls
ubuntu_server.tar
[root@localhost test]# docker load --input ubuntu_server.tar 
Loaded image: ubuntu:14.04
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04-server        ae63a9e87332        4 minutes ago       215MB
test                latest              68ceff3107a8        About an hour ago   188MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
``` 

#### 1.8、上传镜像

可以使用`docker push NAME[:TAG]`上传镜像到仓库，默认上传到DockerHub官方仓库。第一次使用必须要先登录。

```
[root@localhost test]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04-server        ae63a9e87332        4 minutes ago       215MB
test                latest              68ceff3107a8        About an hour ago   188MB
ubuntu              14.04               dea1945146b9        6 weeks ago         188MB
[root@localhost test]# docker tag test:latest username/test:latest
[root@localhost test]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
ubuntu                14.04-server        ae63a9e87332        7 minutes ago       215MB
username/test         latest              68ceff3107a8        About an hour ago   188MB
test                  latest              68ceff3107a8        About an hour ago   188MB
ubuntu                14.04               dea1945146b9        6 weeks ago         188MB
[root@localhost test]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: username
Password: 
Login Succeeded
[root@localhost test]# docker push username/test:latest
The push refers to a repository [docker.io/username/test]
786a798121af: Pushed 
7fb9ba64f896: Pushed 
4e1e6ac5b9d6: Pushed 
48daf661d621: Pushed 
bf59e7acf5c4: Pushed 
c47d9b229ca4: Pushed 
latest: digest: sha256:c46955d49b5b7cf15528b94d5c4fb7028b61c52cf00ee4d14073a92379e2237f size: 1566
```

### 2、容器

#### 2.1、创建容器

可以使用`docker create`命令来新建一个容器，新建的容器处于停止状态，可以使用`docker start CONTAINER_ID`命令来启动它。

```
[root@localhost test]# docker create -it ubuntu:14.04
1fea7f4a60ec1f33298b69dbbe736c6a3509a3a39756d93c20914f451e857356
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND          CREATED           STATUS         PORTS   NAMES
1fea7f4a60ec     ubuntu:14.04     "/bin/bash"      17 seconds ago    Created                eager_blackwell
[root@localhost test]# docker start 1fea7f4a60ec
1fea7f4a60ec
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND          CREATED           STATUS         PORTS   NAMES
1fea7f4a60ec     ubuntu:14.04     "/bin/bash"      2 minutes ago     Up 16 seconds          eager_blackwell
```

#### 2.2、启动容器

启动容器有两种方式：基于镜像新建一个容器并启动；将在终止状态的容器重新启动。所需的命令主要为`docker run`，等价于先执行`docker create`，再执行`docker start`。

启动一个bash终端，允许用户进行交互，`-t`参数让Docker分配一个伪终端并绑定到容器的标准输入上，`-i`参数则让容器的标准输入保持打开，`-d`参数会让Docker容器在后台以守护形式运行，获取容器输出信息，可通过`docker logs`命令。

```
[root@localhost test]# docker run -t -i ubuntu:14.04 /bin/bash
root@eac23878c8bf:/# pwd
/
root@eac23878c8bf:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@eac23878c8bf:/# ps
   PID TTY          TIME CMD
     1 pts/0    00:00:00 bash
    15 pts/0    00:00:00 ps
root@eac23878c8bf:/# exit
exit
[root@localhost test]# docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello word; sleep 1; done"
2105fbb444b3b028a1b26ec5879d8d282ccc2fa7d0a9776a8a86e808d3414843
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND                  CREATED           STATUS             PORTS     NAMES
2105fbb444b3     ubuntu:14.04     "/bin/sh -c 'while..."   11 seconds ago    Up 10 seconds                confident_payne
eac23878c8bf     ubuntu:14.04     "/bin/bash"              2 minutes ago     Exited (0) 2 minutes ago     adoring_shockley
[root@localhost test]# docker logs 2105fbb444b3
hello word
hello word
hello word
hello word
...
[root@localhost test]# docker stop 2105
```

#### 2.3、终止容器

可以用`docker stop [-t|--time [=10]]`来终止一个运行中的容器。

`docker ps -a -q`查看处于终止状态的容器的ID信息。

`docker start`启动一个处于终止状态的容器。

`docker restart`将一个运行态的容器终止然后再重新启动它。

```
[root@localhost test]# docker stop 2105
2105
[root@localhost test]# docker ps -a -q
2105fbb444b3
eac23878c8bf
[root@localhost test]# docker start 2105
2105
[root@localhost test]# docker ps 
CONTAINER ID     IMAGE            COMMAND                  CREATED           STATUS           PORTS      NAMES
2105fbb444b3     ubuntu:14.04     "/bin/sh -c 'while..."   2 minutes ago     Up 9 seconds                confident_payne
[root@localhost test]# docker restart 2105
2105
[root@localhost test]# docker ps
CONTAINER ID     IMAGE            COMMAND                  CREATED           STATUS           PORTS      NAMES
2105fbb444b3     ubuntu:14.04     "/bin/sh -c 'while..."   3 minutes ago     Up 11 seconds               confident_payne
```

#### 2.4、进入容器

使用`-d`参数时容器会进入后台，用户无法看到容器中的信息。进入容器主要有以下三种方式。

`docker attach`当多个窗口同时attach到同一个容器时，所有窗口都会同步显示。当某个窗口因命令阻塞时，其他窗口也无法执行操作。

`docker exec`官方推荐使用。

nsenter工具，比较繁琐，暂时不做过多概述。

```
[root@localhost test]# docker run -tid ubuntu:14.04
0b59f3ef98d47454a314b02d598e8f425855266feb58141178b541fcbdcfc76d
[root@localhost test]# docker ps
CONTAINER ID     IMAGE            COMMAND        CREATED          STATUS          PORTS     NAMES
0b59f3ef98d4     ubuntu:14.04     "/bin/bash"    8 seconds ago    Up 8 seconds              elegant_ride
[root@localhost test]# docker attach elegant_ride
root@0b59f3ef98d4:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@0b59f3ef98d4:/# exit
exit
[root@localhost test]# docker run -tid ubuntu:14.04
a0bb97c292d1fe5d5e507a865c1925251c972dcee30fa65193c06e7ad2aabf0d
[root@localhost test]# docker ps
CONTAINER ID     IMAGE             COMMAND        CREATED          STATUS         PORTS     NAMES
a0bb97c292d1     ubuntu:14.04      "/bin/bash"    6 seconds ago    Up 6 seconds             brave_austin
[root@localhost test]# docker exec -ti a0bb97c292d1 /bin/bash
root@a0bb97c292d1:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@a0bb97c292d1:/# exit
exit
```

#### 2.5、删除容器

可以使用`docker rm [OPTIONS] CONTAINER [CONTAINER...]`命令删除处于终止状态的容器。

```
参数：
-f，--force=false 强行终止并删除一个运行中的容器
-l，--link=false 删除容器的连接，但保留容器
-v，--volumes=false 删除容器挂载的数据卷
```

举例如下：

```
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND         CREATED           STATUS                    PORTS      NAMES
a0bb97c292d1     ubuntu:14.04     "/bin/bash"     8 minutes ago     Up 8 minutes                         brave_austin
0b59f3ef98d4     ubuntu:14.04     "/bin/bash"     14 minutes ago    Exited (0) 8 minutes ago             elegant_ride
[root@localhost test]# docker rm 0b59f3ef98d4
0b59f3ef98d4
[root@localhost test]# docker rm -f a0bb97c292d1
a0bb97c292d1
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND         CREATED           STATUS              PORTS            NAMES
```

#### 2.6、导入导出容器

导出容器是指导出一个已经创建好的容器到一个文件，不管此时这个容器是否处于运行状态，可以使用`docker export CONTAINER`命令。

```
[root@localhost test]# docker run -tid ubuntu:14.04
547e74d47ec3eb30736aee22c1b7bf2acd6e71470a1ade4561007a10b3b14a36
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE            COMMAND          CREATED          STATUS           PORTS       NAMES
547e74d47ec3     ubuntu:14.04     "/bin/bash"      6 seconds ago    Up 5 seconds                 unruffled_jennings
[root@localhost test]# docker export 547 > test.tar
[root@localhost test]# ls
test.tar 
```

导出的文件又可以使用`docker import`命令导入，成为镜像。

```
[root@localhost test]# ls
test.tar
[root@localhost test]# cat test.tar | docker import - test/ubuntu:v1.0
sha256:1ab7c63a882a9bc13ff4673081a237ba8785d47ba69458bfe976c0e5fc7b9c99
[root@localhost test]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
test/ubuntu           v1.0                1ab7c63a882a        22 seconds ago      175MB
ubuntu                14.04-server        ae63a9e87332        2 hours ago         215MB
securityweekly/test   latest              68ceff3107a8        3 hours ago         188MB
ubuntu                14.04               dea1945146b9        6 weeks ago         188MB
```

### 3、仓库

仓库分为**公共仓库**和**私有仓库**。

#### 3.1、公共仓库

官方的公共仓库为[https://hub.docker.com](https://hub.docker.com)，大部分需求都可以通过再Docker Hub中直接下载镜像来实现。可以通过`docker login`命令输入用户名、密码、邮箱来完成注册和登录，认证信息保存在本地用户目录的.dockercfg中。其他操作与前面提到的相同。

#### 3.2、自动创建

**自动创建**（Automated Builds）功能对于需要经常升级景象内程序来说十分方便，用户通过Docker Hub指定跟踪一个目标网站（目前支持GitHub或BitBucket）上的项目，一旦项目发现新的提交，则自动执行创建。

```
配置自动创建的步骤：

1、创建并登录Docker Hub以及目标站点，在目标站点中连接账户到Docker Hub；
2、在Docker Hub中配置一个自动创建；
3、选取一个目标网站中的项目（需要含Dockerfile）和分支；
4、指定Dockerfile的位置，并提交创建；
5、之后可以在Docker Hub的“自动创建”页面中跟踪每次创建的状态。
```

#### 3.3、私有仓库

安装Docker后可通过官方提供的registry镜像来简单搭建一套本地私有仓库环境。

启动下载一个registry容器，创建本地的私有仓库服务。默认情况下会将仓库创建在容器的`/tmp/registry`目录下，可通过`-v`参数来将镜像文件存放在本地的指定路径上。此时，在本地将启动一个私有仓库服务，监听端口为`5000`。

```
[root@localhost test]# docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
Unable to find image 'registry:latest' locally
latest: Pulling from library/registry
49388a8c9c86: Pull complete 
638c4c5f80c0: Pull complete 
da6c9df08ef4: Pull complete 
ee7e568878e6: Pull complete 
386d4eddd833: Pull complete 
Digest: sha256:0694e05b6d0b5fed892ddc60358758bd8341c9a6497ac185f93fc4c93c689810
Status: Downloaded newer image for registry:latest
801a921f0e219b6a8535259c06525a6c441ac0528fc977f27504e7519210b88b
[root@localhost test]# docker ps -a
CONTAINER ID     IMAGE       COMMAND                  CREATED              STATUS              PORTS                    NAMES
801a921f0e21     registry    "/entrypoint.sh /e..."   About a minute ago   Up About a minute   0.0.0.0:5000->5000/tcp   fervent_edison
```

私有仓库的管理、使用与公共仓库类似。

### 4、数据管理

容器中管理数据主要有两种方式：**数据卷**（Data Volumes）和**数据卷容器**（Data Volumes Containers）。

#### 4.1、数据卷

数据卷是一个可供容器使用的特殊目录，它绕过文件系统，可提供很多有用的特性。

```
数据卷特性：

可在荣期间共享和重用；
对数据卷的修改会立即生效；
对数据卷更新不会影响镜像；
卷会一直存在直到没有容器使用。
```

在用`docker run`命令时，使用`-v`标记可以在容器内创建一个数据卷，多次使用`-v`标记可以创建多个数据卷。

```
[root@localhost test]# docker run -d -p 5000:5000 --name web -v /webapp training/webapp python app.py
932e1f1d871d8292bdc54d2b2007a776fb5286631f79577997604e870a40d441
```

`-v`标记也可以指定挂载一个本地的已有目录到容器中去作为数据卷。Docker挂载数据卷的默认权限试读写（rw），可通过`ro`指定为只读。

```
[root@localhost test]# docker run -d -p 5000:5000 --name web -v /src/webapp:/opt/webapp:ro /training/webapp python app.py
7e68a0a373a3907aae114114b0c7e479e7c793d3bdec1ed1773ed1f0dfa1db16
```

`-v`标记也可以从主机挂载单个文件到容器中作为数据卷。

> 注意：如果直接挂载一个文件到容器，使用文件编辑工具时，可能会造成文件inode的改变，从Docker 1.1.0起，这会导致报错误信息，所以推荐的方式是直接挂载文件所在的目录。

```
[root@localhost test]# docker run --rm -it -v ~/.bash_history:/.bash_history ubuntu:14.04 /bin/bash
root@43d2659bf299:/# 
```

上述命令可以记录在容器中输入过的命令历史了。

#### 4.2、数据卷容器

如果需要在容器间共享一些持续更新的数据，最简单的方式是使用数据卷容器。数据据容器其实就是一个普通的容器，专门用它提供数据卷供其它容器挂在使用。

创建一个数据卷容器`dadata`并在其中创建一个数据卷挂载到`/dbdata`。

```
[root@localhost ~]# docker run -it -v /dbdata --name dbdata ubuntu:14.04
root@417a47e1a78d:/# ls
bin   dbdata  etc   lib    media  opt   root  sbin  sys  usr
boot  dev     home  lib64  mnt    proc  run   srv   tmp  var
```

然后可在其他容器中使用`--volumes-from`来挂载`dbdata`容器中的数据卷。

```
[root@localhost ~]# docker run -it --volumes-from dbdata --name db1 ubuntu:14.04
root@7704b2f5a909:/# exit
exit
[root@localhost ~]# docker run -it --volumes-from dbdata --name db2 ubuntu:14.04
root@e675ebdac602:/# exit
exit
[root@localhost ~]# docker ps -a
CONTAINER ID     IMAGE             COMMAND         CREATED            STATUS                          PORTS   NAMES
e675ebdac602     ubuntu:14.04      "/bin/bash"     10 seconds ago     Exited (0) 6 seconds ago                db2
7704b2f5a909     ubuntu:14.04      "/bin/bash"     23 seconds ago     Exited (0) 15 seconds ago               db1
417a47e1a78d     ubuntu:14.04      "/bin/bash"     18 minutes ago     Exited (0) About a minute ago           dbdata
```

多个容器可挂载同一个数据卷到相同的`/dbdata`目录，容器任何一方在该目录下的写入，其他容器都可以看到。

```
root@417a47e1a78d:/# cd /dbdata
root@417a47e1a78d:/dbdata# touch test
root@417a47e1a78d:/dbdata# ls
test
[root@localhost ~]# docker run -it --volumes-from dbdata --name db2 ubuntu:14.04
root@e675ebdac602:/# ls
bin   dbdata  etc   lib    media  opt   root  sbin  sys  usr
boot  dev     home  lib64  mnt    proc  run   srv   tmp  var
[root@localhost ~]# ls dbdata/
test
```

可以多次使用`--volumes-from`参数来从多个容器挂载多个数据卷，还可以从其他已经挂载了容器卷的容器来挂载数据卷。使用`--volumes-from`参数所挂载数据卷的容器自身并不需要保持在运行状态。

```
[root@localhost ~]# docker run -d --name db3 --volumes-from db1 ubuntu:14.04
78b753e6d1a374a347160cfece7464901bed3d06b6ba8444b6bf793cd497fa80
```

如果删除了挂载的容器（包括dbdata、db1和db2），数据卷并不会被自动删除。如果要删除一个数据卷，必须在删除最后一个还挂载着它的容器时显式使用`docker rm -v`命令来指定同时删除关联的容器。

使用数据卷容器可以让用户在容器之间自由的升级和移动数据卷，也可以利用数据卷容器对其中的数据卷进行备份、恢复，以实现数据的迁移。

备份数据卷容器中的数据卷

```
[root@localhost ~]# docker run --volumes-from dbdata -v $(pwd):/backup --name worker ubuntu:14.04 tar cvf /backup/backup.tar /dbdata
[root@localhost ~]# ls
backup.tar
```

命令有点复杂，详细的命令分析如下：

```
首先利用ubuntu镜像创建了一个容器worker；
使用--volumes-from dbdata参数来让worker容器挂载dbdata容器的数据卷（即dbdata数据卷）；
使用-v $(pwd):/backup参数来挂载本地的当前目录到worker容器的/backup目录；
worker容器启动后，使用tar cvf /backup/backup.tar /dbdata命令来将/dbdata下内容备份为容器内的/backup/backup.tar，即宿主主机当前目录下的backup.tar。
```

恢复数据到一个容器

首先创建一个带有数据卷的容器dbdata2；

```
[root@localhost ~]# docker run -v /dbdata --name dbdata2 ubuntu:14.04 /bin/bash
[root@localhost ~]# docker ps -a
CONTAINER ID    IMAGE           COMMAND        CREATED            STATUS                      PORTS     NAMES
0d27d09f57de    ubuntu:14.04    "/bin/bash"    13 seconds ago     Exited (0) 12 seconds ago             dbdata2
```

然后创建另一个新的容器，挂载dbdata2的容器，并使用untar解压备份文件到所挂载的容器即可。

```
[root@localhost ~]# ls
backup.tar
[root@localhost ~]# docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
03b1be98f3f9: Pull complete 
Digest: sha256:3e8fa85ddfef1af9ca85a5cfb714148956984e02f00bec3f7f49d3925a91e0e7
Status: Downloaded newer image for busybox:latest
dbdata/
```

### 5、网络基础配置

#### 5.1、端口映射

在启动容器时，如果不指定对应参数，在容器外部是无法通过网络来访问容器内的网络应用和服务的。

当容器中运行的一些网络应用要让外部访问时，可以通过`-P`或`-p`参数来指定端口映射。`-P`标记会随机映射一个49000~49900的端口至容器内部开放的网络端口。

```
[root@localhost ~]# docker run -d -P training/webapp python app.py
3fcaa6c3a4985ffb4bc2b6063550ef3e7827877c4dda7dda814d23c9fd782739
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE             COMMAND           CREATED          STATUS          PORTS                     NAMES
3fcaa6c3a498     training/webapp   "python app.py"   2 minutes ago    Up 2 minutes    0.0.0.0:32768->5000/tcp   mystifying_lichterman
```

`-p`则可以指定要映射的端口，并且在一个指定端口上只能绑定一个容器。支持的格式有：`ip:hostPort:containerPort | ip:containerPort | hostPort:containerPort`

使用`hostPort:containerPort`格式将本地的指定端口映射到容器的指定端口。默认绑定本地所有接口上的所有地址。多次使用`-p`标签可以绑定多个端口。

```
[root@localhost ~]# docker run -d -p 5000:5000 -p 3000:80 training/webapp python app.py
dddb3ced729f593951bc42b4bf1fb69920bca23ff0d79557010ce89fff835c1a
[root@localhost ~]# docker ps -l
CONTAINER ID    IMAGE              COMMAND           CREATED           STATUS            PORTS                                          NAMES
dddb3ced729f    training/webapp    "python app.py"   6 seconds ago     Up 5 seconds      0.0.0.0:5000->5000/tcp, 0.0.0.0:3000->80/tcp   quizzical_agnesi
```

可以使用`ip:hostPort:containerPort`格式指定映射使用一个特定地址。

```
[root@localhost ~]# docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
4e6d5d31d00213b8d30c45f776b68d5f87bc96dcf87deab65e67386a0cec903b
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE              COMMAND            CREATED           STATUS            PORTS                      NAMES
4e6d5d31d002     training/webapp    "python app.py"    8 seconds ago     Up 7 seconds      127.0.0.1:5000->5000/tcp   quizzical_banach 
```

使用`ip::containerPort`绑定localhost的任意端口到容器的指定端口，本地主机会自动分配一个端口。

```
[root@localhost ~]# docker run -d -p 127.0.0.1::5000 training/webapp python app.py
9965b7919951f9a0febd6614a7b8392cb6b625cb3b612ea8975fc23ccc81ae69
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE              COMMAND            CREATED           STATUS          PORTS                       NAMES
9965b7919951     training/webapp    "python app.py"    5 seconds ago     Up 4 seconds    127.0.0.1:32768->5000/tcp   optimistic_mahavira
```

还可以使用`udp`标记来指定udp端口。

```
[root@localhost ~]# docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
e48e3dfd1b20b20514c2009ab7ee942e88020971cfbcf3e5525ac9ce18c0e3eb
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE              COMMAND            CREATED            STATUS           PORTS                                NAMES
e48e3dfd1b20     training/webapp    "python app.py"    8 seconds ago      Up 7 seconds     5000/tcp, 127.0.0.1:5000->5000/udp   gracious_colden
```

使用`docker port`来查看当前映射的端口配置，也可查看到绑定的地址。

```
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE              COMMAND            CREATED           STATUS           PORTS                                NAMES
e48e3dfd1b20     training/webapp    "python app.py"    8 seconds ago     Up 7 seconds     5000/tcp, 127.0.0.1:5000->5000/udp   gracious_colden
[root@localhost ~]# docker port gracious_colden 5000/udp
127.0.0.1:5000
```

#### 5.2、容器互联

**容器的连接（linking）系统**是除了端口映射外另一种可以与容器中应用进行交互的方式，它会在源和接收容器间创建一个隧道，接收容器可以看到源容器指定的信息。

连接系统是依据容器的名称来执行的，首先需要自定义一个好记的容器命名，使用`--name`标记可为容器自定义命名。

容器的名称是**唯一**的，如果命名已存在，需要先用`docker rm`来删除之前创建的同名容器。

在执行`docker run`的时候如果添加`--rm`标记，则容器在终止后会立刻删除，`--rm`和`-d`参数不能同时使用。

```
[root@localhost ~]# docker run -d -P --name web training/webapp python app.py
0fad509942452cf778130a4fe48ee1ee1b5b7ac90d1722e87a57bf935edcdcd8
[root@localhost ~]# docker ps -l
CONTAINER ID     IMAGE              COMMAND            CREATED           STATUS           PORTS                     NAMES
0fad50994245     training/webapp    "python app.py"    9 seconds ago     Up 8 seconds     0.0.0.0:32769->5000/tcp   web
[root@localhost ~]# docker inspect -f "{{ .Name }}" 0fad50994245
/web
```

使用`--link`参数可以让容器之间安全的进行交互。格式为：`--link name:alias`，其中name是要连接的容器的名称，alias是这个连接的别名。

Docker在两个互联的容器间创建了一个安全隧道，且不用映射它们的端口到宿主主机上，避免了暴露数据库端口到外部网络。

```
[root@localhost ~]# docker run -d --name db training/postgres
050e569cc50e20cb3b8d41b028204fa51a6d862850b6bb20b2390270de748e2b
[root@localhost ~]# docker run -d -P --name web --link db:db training/webapp python app.py
f8e68cadc083afad5401b001ee223ee9b47ebd447c8fcac71a961be13de39c8a
[root@localhost ~]# docker ps -a
CONTAINER ID      IMAGE                COMMAND                  CREATED              STATUS              PORTS                     NAMES
f8e68cadc083      training/webapp      "python app.py"          8 seconds ago        Up 7 seconds        0.0.0.0:32771->5000/tcp   web
050e569cc50e      training/postgres    "su postgres -c '/..."   About a minute ago   Up About a minute   5432/tcp                  db, web/db
```

可以看到自定义命名的容器：db和web，db容器的names列有db也有web//db，这表示web容器连接到db容器，允许web容器访问db容器的信息。

Docker通过两种方式为容器公开连接信息：**环境变量**；**更新/etc/hosts文件**。

使用`env`命令来查看web容器的环境变量。

```
[root@localhost ~]# docker run --rm  --name web2 --link db:db training/webapp env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=08f24c59f079
DB_PORT=tcp://172.17.0.3:5432
DB_PORT_5432_TCP=tcp://172.17.0.3:5432
DB_PORT_5432_TCP_ADDR=172.17.0.3
DB_PORT_5432_TCP_PORT=5432
DB_PORT_5432_TCP_PROTO=tcp
DB_NAME=/web2/db
DB_ENV_PG_VERSION=9.3
HOME=/root
```

其中DB_开头的环境变量是提供web容器连接db容器使用，前缀采用大写的连接别名。

除了环境变量，Docker还添加host信息到父容器的`/etc/hosts`文件。

```
[root@localhost ~]# docker run -ti --rm --link db:db training/webapp /bin/bash
root@924a157de540:/opt/webapp# cat /etc/hosts 
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	db 050e569cc50e
172.17.0.5	924a157de540
root@924a157de540:/opt/webapp# ping db
PING db (172.17.0.3): 56 data bytes
64 bytes from 172.17.0.3: icmp_seq=0 ttl=64 time=0.202 ms
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.157 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.160 ms
64 bytes from 172.17.0.3: icmp_seq=3 ttl=64 time=0.156 ms
^C--- db ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.156/0.169/0.202/0.000 ms
root@924a157de540:/opt/webapp# 
```

用户可连接多个子容器到父容器。

### 6、Dockerfile

**Dockerfile**是一个文本格式的配置文件，用户可使用Dockerfile快速创建自定义镜像。

#### 6.1、基本结构

Dockerfile由一行行命令语句组成，主要分为**基础镜像信息**、**维护者信息**、**镜像操作指令**和**容器启动时指令**这四部分，并支持以`#`开头的注释行。

```
# This dockerfile uses the ubuntu image
# VERSION 2 - EDITION 1
# Author: docker_user
# Command fromat: Instruction [arguments / command] ..

# 第一行必须指定基于的基础镜像
FROM ubuntu

# 维护者信息
MAINTAINER docker_user docker_user@email.com

# 镜像的操作指令
RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

# 容器启动时执行指令
CMD /usr/sbin/nginx
...
```

在ubuntu父镜像基础上安装inotify-tools、nginx、apache2、openssh-server等软件，创建一个新的Nginx镜像。

```
# Nginx
# 
# VERSION	0.0.1

FROM	ubuntu
MAINTAINER Victor View <victor@docker.com>

RUN apt-get update && apt-get install -y inotify-tools nginx apache2 openssh-server
```

基于ubuntu父镜像，安装firefox和vnc软件，启动后，可通过5900端口通过vnc方式使用firefox。

```
# Firefox over VNC
# 
# VERSION	0.3

FROM	ubuntu
# Install vnc, xvfb in order to create a 'fake' display and firefox
RUN apt-get update && apt-get install -y x11vnc xvfb firefox
RUN mkdir /.vnc
# Setup a password
RUN x11vnc -storepasswd 1234 ~/.vnc/passwd
# Autostart firefox (might not be the best way, but it does the trick)
RUN bash -c 'echo "firefox" >> /.bashrc'

EXPOSE 5900
CMD		["x11vnc", "-forever", "-usepw", "-create"]
```

#### 6.2、指令

指令的一般格式为`INSTRUCTION arguments`，下面分别做详细介绍。

**FROM**

格式为`FROM <image>`或`FROM <image>:<tag>`。第一条指令必须为`FROM`指令，在同一个Dockerfile中创建多个镜像时可以使用多个`FROM`指令（每个镜像一次）。

**MAINTAINER**

格式为`MAINTAINER <name>`，指定维护者信息。

**RUN**

格式为`RUN <command>`或`RUN ["executable", "param1", "param2"]`。前者将在shell终端中运行命令，即`/bin/sh -c`；后者则使用exec执行。指定使用其他终端可以通过第二种方式实现。

```
RUN ["/bin/bash", "-c", "echo hello"]
```

每条`RUN`指令将在当前镜像基础上执行指定命令，并提交为新的镜像，当命令较长时可使用`\`来换行。

**CMD**

支持三种格式：

* `CMD ["executable", "param1", "param2"]`，使用`exec`执行，推荐方式。

* `CMD command param1 param2`，在`/bin/sh`中执行，提供给需要交互的应用。

* `CMD ["param1", "param2"]`，提供给`ENTRYPOINT`的默认参数

指定启动容器时执行的命令，每个Dockerfile只能有一条`CMD`命令，如果指定了多条命令，只有最后一条会被执行。如果用户启动容器时指定了运行的命令则会覆盖掉`CMD`指定的命令。

**EXPOSE**

格式为`EXPOSE <port> [<port>...]`，告诉Docker服务端容器暴露的端口号，供互联系统使用。

在启动容器时需要通过`-P`Docker主机会自动分配一个端口转发到指定的端口；使用`-p`则可以具体指定哪个本地端口映射过来。

**ENV**

格式`ENV <key> <value>`，指定一个环境变量，会被后续`RUN`指令使用，并在容器运行时保持。

```
ENV PG_MAJOR	9.3
ENV PG_VERSION	9.3.4
RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgres && ...
ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH
```

**ADD**

格式`ADD <src> <dest>`，复制指定的<src>到容器中的<dest>，其中<src>可以是Dockerfile所在目录的一个相对路径（文件或目录）；也可以是一个URL；还可以是一个tar文件（自动解压为目录）。

**COPY**

格式`COPY <src> <dest>`，复制本地主机的<src>（为Dockerfile所在目录的相对路径，文件或目录）为容器中的<dest>，目标路径不存在时会自动创建。

**ENTRYPOINT**

配置容器启动后执行的命令，且不可被`docker run`提供的参数覆盖。有两种格式：

* `ENTRYPOINT ["executable", "param1", "param2"]`

* `ENTRYPOINT command param1 param2`，shell中执行。

每个Dockerfile中只能有一个`ENTRYPOINT`，当指定多个`ENTRYPOINT时`，只有最后一个生效。

**VOLUME**

格式`VOLUME ["/data"]`，创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

**USER**

格式`USER daemon`，指定运行容器时的用户名或UID，后续的`RUN`也会使用指定用户。

当服务不需要管理员权限时，可通过该命令指定运行用户，并可在之前创建所需要的用户。要临时获取管理员权限可使用`gosu`，而不推荐`sudo`。

**WORKDIR**

格式`WORKDIR /path/to/workdir`，为后续的`RUN`、`CMD`、`ENTRYPOINT`指令配置工作目录。

可以使用多个`WORKDIR`指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。

```
WORKDIR	/a
WORKDIR	b
WORKDIR	c
RUN pwd
# 最终路径为/a/b/c
```

**ONBUILD**

格式`ONBUILD [INSTRUCTION]`，配置当前所创建的镜像作为其他新创建的基础镜像时所执行的操作指令。

镜像image-A Dockerfile

```
[...]
ONBUILD	ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
[...]
```

基于image-A创建新镜像，新的Dockerfile中使用`FROM images-A`指定基础镜像时，会自动执行`ONBUILD`指令内容，等价于在后面添加了两条指令。

```
FROM images-A

# Automatically run the following
ADD . /app/src
RUN /usr/local/bin/python-build --dir /app/src
```

使用`ONBUILD`指令的镜像推荐在标签中注明。例如`ruby:1.9-onbuild`。

#### 6.3、创建镜像

编写完Dockerfile后，可以通过`docker build`命令来创建镜像。

基本格式为`docker build[选项] 路径`，该命令读取指定路径下（包括子目录）的Dockerfile，并将该路径下所有内容发送给Docker服务端，由服务端来创建镜像，因此，一般建议放置Dockerfile的目录为空目录。也可通过`.dockerignore`文件（每行添加一条匹配模式）来让Docker忽略路径下的目录和文件。

要指定镜像的标签信息，可通过`-t`选项。

```
# 指定Dockerfile所在路径为/tmp/docker_builder/，且希望生成镜像标签为build_repo/first_image
[root@localhost ~]# docker build -t build_repo/first_image /tmp/docker_builder/
```
