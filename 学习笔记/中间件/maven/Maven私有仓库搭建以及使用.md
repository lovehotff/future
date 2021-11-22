# 1.使用Docker安装Nexus

* Docker search nexus

* docker pull docker.io/sonatype/nexus3

* ```
  mkdir -p /usr/local/nexus3/nexus-data
  chown -R 200 /usr/local/nexus3/nexus-data
   
  docker run -tid -p 8081:8081 --name nexus -e NEXUS_CONTEXT=nexus -v /usr/local/nexus3/nexus-data:/nexus-data  docker.io/sonatype/nexus3
  ```

-------------

# 2.原生安装和启动 nexus

1. 打开 / etc/profile，新增一行 export RUN_AS_USER=root；
2. 执行命令 source /etc/profile，使刚才的配置生效；
3. 从官网下载的是 nexus-2.14.5-02-bundle.tar.gz，执行 tar -zxvf nexus-2.14.5-02-bundle.tar.gz 解压后，里面有两个目录：nexus-2.14.5-02 和 sonatype-work(wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz)；
4. 进入 nexus-2.14.5-02/bin，执行./nexus start 启动 nexus；
5. 进入 nexus-2.14.5-02/logs，执行 tail -f wrapper.log 观察日志滚动信息，看到如下信息时 nexus 启动成功：

```
jvm 1    | 2018-01-13 21:38:44,671-0800 INFO  [jetty-main-1]  org.sonatype.nexus.webresources.internal.WebResourceServlet - Max-age: 30 days (2592000 seconds)
jvm 1    | 2018-01-13 21:38:44,698-0800 INFO  [jetty-main-1]  org.sonatype.nexus.bootstrap.jetty.InstrumentedSelectChannelConnector - Metrics enabled
jvm 1    | 2018-01-13 21:38:44,713-0800 INFO  [jetty-main-1]  org.eclipse.jetty.server.AbstractConnector - Started InstrumentedSelectChannelConnector@0.0.0.0:8081
jvm 1    | 2018-01-13 21:38:44,713-0800 INFO  [jetty-main-1]  org.sonatype.nexus.bootstrap.jetty.JettyServer - Running
jvm 1    | 2018-01-13 21:38:44,713-0800 INFO  [WrapperListener_start_runner]  org.sonatype.nexus.bootstrap.jetty.JettyServer - Started
jvm 1    | 2018-01-13 21:39:26,926-0800 INFO  [qtp984089572-47]  org.apache.shiro.nexus5727.FixedDefaultWebSessionManager - Global session timeout: 1800000 ms
jvm 1    | 2018-01-13 21:39:26,931-0800 INFO  [qtp984089572-47]  org.apache.shiro.session.mgt.AbstractValidatingSessionManager - Enabling session validation scheduler...
jvm 1    | 2018-01-13 21:39:26,944-0800 INFO  [qtp984089572-47]  org.apache.shiro.cache.ehcache.EhCacheManager - Using existing EHCache named [shiro-activeSessionCache]
jvm 1    | 2018-01-13 21:39:26,945-0800 INFO  [qtp984089572-45]  org.apache.shiro.nexus5727.FixedDefaultWebSessionManager - Global session timeout: 1800000 ms
```

# 二、登录 Nexus 进行配置



# 1、登录 nexus



​      访问：http://ip:8081/nexus 使用默认管理员身份登录，帐号：admin，密码：admin123


![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130152900654-1050282508.png)


##  2、进入管理界面
​      

![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130154444686-2001929600.png)
- Browse 可以查看当前有多少仓库，搭建好的 Nexus，默认会带有一些 maven 仓库，一般使用这些仓库就足够了。
- 默认仓库说明  

​     maven-central：maven 中央库，默认从 https://repo1.maven.org/maven2 / 拉取 jar


​     maven-releases：私库发行版 jar，初次安装请将 Deployment policy 设置为 Allow redeploy

​     maven-snapshots：私库快照（调试版本）jar

​     maven-public：仓库分组，把上面三个仓库组合在一起对外提供服务，在本地 maven 基础配置 settings.xml 或项目 pom.xml 中使用

- 仓库类型  
​      Group：这是一个仓库聚合的概念，用户仓库地址选择 Group 的地址，即可访问 Group 中配置的，用于方便开发人员自己设定的仓库。maven-public 就是一个 Group 类型的仓库，内部设置了多个仓库，访问顺序取决于配置顺序，3.x 默认 Releases，Snapshots，                 Central，当然你也可以自己设置。
​      Hosted：私有仓库，内部项目的发布仓库，专门用来存储我们自己生成的 jar 文件
​      3rd party：未发布到公网的第三方 jar (3.x 去除了)
​      Snapshots：本地项目的快照仓库
​      Releases： 本地项目发布的正式版本
​      Proxy：代理类型，从远程中央仓库中寻找数据的仓库（可以点击对应的仓库的 Configuration 页签下 Remote Storage 属性的值即被代理的远程仓库的路径），如可配置阿里云 maven 仓库
​     Central：中央仓库
​     Apache Snapshots：Apache 专用快照仓库 (3.x 去除了)


##  3、增加新的代理源
###第一步 按照步骤添加新的代理源
![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130154948336-886273.png)
---
###第二步选择添加 maven2 的代理

***![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130155307628-2089844715.png)***
---

###第三步添加代理（Cache 统一设置为 200 天 288000）

![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130155458779-1665135957.png)
​      
---
![img](https://img2018.cnblogs.com/blog/57115/201901/57115-20190130155916434-67749094.png)

### 第四步逐个增加常用代理
[![img](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1. aliyun
http://maven.aliyun.com/nexus/content/groups/public
2. apache_snapshot
https://repository.apache.org/content/repositories/snapshots/
3. apache_release
https://repository.apache.org/content/repositories/releases/
4. atlassian
https://maven.atlassian.com/content/repositories/atlassian-public/
5. central.maven.org
http://central.maven.org/maven2/
6. datanucleus
http://www.datanucleus.org/downloads/maven2
7. maven-central （安装后自带，仅需设置Cache有效期即可）
https://repo1.maven.org/maven2/
8. nexus.axiomalaska.com
http://nexus.axiomalaska.com/nexus/content/repositories/public
9. oss.sonatype.org
https://oss.sonatype.org/content/repositories/snapshots
10.pentaho
https://public.nexus.pentaho.org/content/groups/omni/
```