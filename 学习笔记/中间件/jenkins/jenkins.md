jenkins

前置条件 

JDK、tomcat 

安装tomcat 

下载地址： https://tomcat.apache.org/download-90.cgi 

```
useradd tomcat --新增一个名为tomcat的用户  
passwd tomcat --给tomcat用户设置密码 
tar -zxvf apache-tomcat-9.0.8.tar.gz -C /usr/local/ --将tomcat解压到相应目录 
chown -R tomcat:tomcat /usr/local/apache-tomcat-9.0.8 --将整个目录的所属权转移给tomcat用户、 tomcat组 
```





 http://49.232.150.106:9080/jenkins/pluginManager/advanced 

安装插件cloudbees-folder失败，是因为下载的Jenkins.war里没有cloudbees-folder插件

需要去 https://updates.jenkins-ci.org/download/plugins/cloudbees-folder/ 下载一个插件

下载cloudbees-folder.hpi放在 tomcat 的 /webapps/jenkins/WEB-INF/detached-plugins/即可。

然后重启tomcat。问题解决





/var/lib/jenkins/hudson.model.UpdateCenter.xml



java -jar jenkins.war --ajp13Port=-1 --httpPort=8888

http://8.131.73.65:8888//pluginManager/advanced 