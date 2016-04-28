# 单节点服务器安装
--

Rancher使用基于Docker容器的部署方式。仅需简单的启动两个容器即可运行。一个容器用于管理Rancher服务，其他容器使用代理的方式管理主机或节点。

#### 需求
- 任何支持Docker1.10.3的Linux发行版。[RancherOS](http://docs.rancher.com/os/)，Ubuntu，RHEL/CentOS 7经过了更多测试
- 1GB 内存
- MySQL服务，并且设置 max_connections > 150

#### 启动Rancher服务器
在安装了Docker的服务器上可以很简单的使用命令启动Rancher服务

```
$ sudo docker run -d --restart=always -p 8080:8080 rancher/server
```

###### RANCHER UI
Rancher的UI和API将开放8080端口。Docker镜像下载后，还需要1-2分钟才可以成功启动并显示页面。

访问以下地址：`http://<SERVER_IP>:8080`，这里`<SERVER_IP>`是指运行Rancher服务并可用于访问的IP地址。

一旦UI处于启动和运行状态，就可以开始 [添加主机]({{site.baseurl}}/rancher/installing-rancher/add-host/) 。主机添加到Rancher后，您可以开始添加 [服务]() 或 使用 [Rancher catalog]() 启动模板。

#### 开启活动目录或OpenLDAP支持(TLS)
为了开启Rancher活动目录或OpenLDAP支持(TLS)，Rancher Server容器在启动时需要加载证书。您需要在运行Rancher Server的Linux主机上保存证书。

启动Rancher服务并使用绑定挂载卷的方式加载证书，证书在容器中 **必须** 使用`ca.crt`命名。

```
$ sudo docker run -d --restart=always -p 8080:8080 \
  -v /dir_that_contains_the_cert/cert.crt:/ca.crt rancher/server
```

您可以通过检查Rancher Server容器日志确认`ca.crt`已经成功传递给Rancher。

```
$ docker logs <server_container_id>
```

在日志的开始，会提示证书 `ca.crt` 已经添加。

```
DEFAULT_CATTLE_RANCHER_COMPOSE_WINDOWS_URL=https://releases.rancher.com/compose/beta/latest/rancher-compose-windows-386.zip
Adding ca.crt to Certs.
Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d....
done.
done.
[BOOTSTRAP] Starting Cattle
```

#### 绑定挂载MYSQL卷
如果你想将容器内数据库持久化在主机上，可以在启动Rancher Server时绑定并挂载MySQL卷。

```
$ sudo docker run -d -v <host_vol>:/var/lib/mysql --restart=always -p 8080:8080 rancher/server
```

使用这个命令，数据库将持久化在主机上。如果您是一个已有的Rancher容器，请参照我们的 [升级文档]() 。

#### 使用外部数据库
如果您更希望使用外部数据库运行Rancher Server，请参照如下操作连接Ranche到数据库。您需要一个已经创建好的数据库，但是不需要创建任何数据库对象，Rancher将会自动创建所有相关的数据库对象。

以下环境变量需要通过`docker run`命令启动Rancher Server来支持外部数据库。

- CATTLE\_DB\_CATTLE\_MYSQL\_HOST:  数据库实例的主机名或IP地址
- CATTLE\_DB\_CATTLE\_MYSQL\_PORT:  3306
- CATTLE\_DB\_CATTLE\_MYSQL\_NAME:  数据库名
- CATTLE\_DB\_CATTLE\_USERNAME:  用户名
- CATTLE\_DB\_CATTLE\_PASSWORD:  密码

> **注意：**
> 
> 数据库名称和用户必须已经存在，Rancher不会创建数据库。 

这是一个用于创建数据库和用户的SQL命令。

```
sql CREATE DATABASE IF NOT EXISTS cattle COLLATE = 'utf8_general_ci' CHARACTER SET = 'utf8'; GRANT ALL ON cattle.* TO 'cattle'@'%' IDENTIFIED BY 'cattle'; GRANT ALL ON cattle.* TO 'cattle'@'localhost' IDENTIFIED BY 'cattle';
```

创建数据库和用户后，使用环境变量启动Rancher Server。

```
$ sudo docker run -d --restart=always -p 8080:8080 \
    -e CATTLE_DB_CATTLE_MYSQL_HOST=<hostname or IP of MySQL instance> \
    -e CATTLE_DB_CATTLE_MYSQL_PORT=<port> \
    -e CATTLE_DB_CATTLE_MYSQL_NAME=<Name of Database> \
    -e CATTLE_DB_CATTLE_USERNAME=<Username> \
    -e CATTLE_DB_CATTLE_PASSWORD=<Password> \
    rancher/server
```

#### 使用HTTP代理环境启动Rancher Server
为了使用HTTP代理，需要修改Docker服务支持代理。在Rancher Server启动前，编辑`/etc/defalut/docker`文件指定您的代理并重新启动Docker服务。

```
$ sudo vi /etc/default/docker
```

在这个文件中编辑`#export http_proxy="http://127.0.0.1:3128/"`指向您的代理。保存修改并重新启动Docker服务，每个操作系统重新启动Docker服务的方式是不同的。

> **注意：**
> 
> 如果您使用systemd运行Docker，请参照Docker [文档](https://docs.docker.com/articles/systemd/#http-proxy) 配置HTTP代理。

为了加载 [Rancher catalog]() ，需要在启动Rancher Server时加载HTTP代理的环境变量信息。

```
$ sudo docker run -d \
    -e http_proxy=<proxyURL> \
    -e https_proxy=<proxyURL> \
    -e no_proxy="localhost,127.0.0.1" \
    --restart=always -p 8080:8080 rancher/server
```

如果不使用 [Rancher catalog]() ，则使用正常方式运行启动Rancher Server命令。

在Rancher中 [添加主机]({{site.baseurl}}/rancher/installing-rancher/add-host/) ，无需使用HTTP代理。
