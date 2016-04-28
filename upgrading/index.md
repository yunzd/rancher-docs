## 升级 Rancher
---

如果你运行Rancher服务端时 **没有** 使用 [外部数据库]({{site.baseurl}}/rancher/installing-rancher/installing-server/#external-db), Rancher服务端数据库在你的Rancher服务端容器内. 我们将要使用运行中的Rancher服务端容器来创建一个数据容器. 数据容器将会通过使用 `--volumes-from` 以用来启动新的Rancher服务端. 或者, 你能将数据库从容器中拷贝出来到主机的某个目录然后绑定挂载数据库.

> **注意:** 如果你使用了外部数据库, 你能停止原始的Rancher服务端容器然后使用相同的[外部数据库指引]({{site.baseurl}}/rancher/installing-rancher/installing-server/#external-db)来启动新版本的Rancher服务端.在新服务端启动运行后,你就能删除老的Rancher服务端容器了. 注意: 如果你仅停止容器,如果你的机器重启,容器将会由于有`--restart=always`而重新运行.


#### 通过创建数据容器升级Rancher

1. 停止容器.

   ```bash
   $ docker stop <container_name_of_original_server>
   ```

2. 创建一个 `rancher-data` 容器. 注意: 此步骤可以跳过,如果你过去已经升级过并且已经有了一个 `rancher-data` 容器.
    
   ```bash
   $ docker create --volumes-from <container_name_of_original_server> \
    --name rancher-data rancher/server:<tag_of_previous_rancher_server>
   ```

3. 拉取最近的Rancher服务端镜像. 注意: 如果你跳过此步骤然后尝试运行`最近的`镜像, 将不会自动拉取更新后的镜像.

   ```bash
   $ docker pull rancher/server:latest
   ```
   
4. 启动一个新的Rancher服务端并使用来自 `rancher-data` 容器的数据库. Rancher内的任何修改都将会保存到 `rancher-data` 容器.如果你在服务端件到了关于日志锁的异常,请参考[如何处理日志锁]({{site.baseurl}}/rancher/faqs/server/#databaselock).
    
    > **注意**: 取决于你运行了Rancher服务端多久, 某些数据库迁移可能花费比预计更长的时间.请不要在升级中间时停止升级,否则在下次升级时将会遇上数据库迁移错误
    
   ```bash
   $ docker run -d --volumes-from rancher-data --restart=always \
     -p 8080:8080 rancher/server:latest
   ```

    > **注意:** 如果你在原始的Rancher服务端安装时设置过任何环境参数或传入过[ldap认证]({{site.baseurl}}/rancher/installing-rancher/installing-server/#enabling-active-directory-or-openldap-for-tls), 你将需要在命令中加入这些环境变量或认证. 
    
5. 移除旧的Rancher服务端. 注意: 如果你仅停止了容器,容器将会由于有`--restart`而在你的机器重启后重新运行. 我们建议在你的升级成功后移除此容器.


#### 通过启动时使用绑定挂载升级Rancher

1. 停止运行中的Rancher服务端容器.

   ```bash
   $ docker stop <container_name_of_original_server>
   ```

2. 从服务端容器中拷贝数据库文件出来. 注意:如果你已经有数据库存储在主机上了,你能够跳过此步骤.另外, 如果数据库已经被拷贝到了容器外面,它将会在 /<path>/mysql/ 里面,因为Docker的拷贝方法如此. 在绑定挂载到容器里时一定要考虑到这一点. 如果你开始绑定挂载,你将不需要输入 mysql/.

   ```bash
   $ docker cp <container_name_of_original_server>:/var/lib/mysql <path on host>
   ```
   
3. 现在为文件夹设置 UID/GID 以便容器内的mysql用户对mysql挂载有正确的所有权.

   ```bash
   $ sudo chown -R 102:105 <path on host>
   ```

4. 开启新的服务端容器.

   ```bash
   $ docker run -d -v <path_on_host>:/var/lib/mysql -p 8080:8080 \
     --restart=always rancher/server:latest
   ```
  <br>

   > **注意:** 如果你是从前一个容器中拷贝出来的数据库,确保在主机路径最后是 '/' 将很重要.否则目录会在错误的地方结束.
   

### Rancher代理端

每个Rancher代理端版本都固定了相应的Rancher服务端版本.如果你升级了Rancher服务端并且Rancher代理端需要升级,它将会自动升级代理端到最新版本的Rancher代理端.

对于使用 `rancher/agent-instance` 镜像的任何东西,运行中的容器将获得升级,甚至是容器的镜像没有升级到最近版本.对于 _Network Agent_, 这将在有关网络的触发器触发时进行(例如, 添加另一个容器,删除一个容器).对于 _Load Balancers_,升级将在下一次负载均衡配置更新时进行(例如,目标实例重启,新的目标服务加入,主机名路由规则变更,负载均衡实例重启等).

#### 没有互联网接入的用户

没有互联网的用户需要将最新的 `rancher/agent-instance` 下载到他们自己的镜像库. 为了升级网络代理的版本,你需要通过UI手工停止网络代理端并移除他们. 在缺失网络代理端的主机上,任何触发网络的事件都将会引起一个新的网络代理端(最新版本)启动. 为了升级你的负载均衡的版本, 你将需要重新创建它们以便获取到最新版本的 `rancher/agent-instance`. 
