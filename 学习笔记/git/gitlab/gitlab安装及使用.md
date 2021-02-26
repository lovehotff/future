---
typora-root-url: ./
---

# gitlab安装及使用

安装Omnibus Gitlab-ce package（Omnibus综合性的意思，包含gitlab基础工具包，一键安装，自动化配置，简化初学者的安装成本，相对于源代码安装节省了很多时间以及不必要的麻烦。）

## 安装gitlab及组件

### **1、安装gitlab组件**

```
yum -y install curl policycoreutils openssh-server openssh-clients postfix
```

### **2、配置yum仓库**

```
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

### **3、下载启动postfix邮件服务（用于gitlab发送通知到管理者）**

```
yum install postfix
systemctl start postfix
systemctl enable postfix  --注：若报错则执行下面的步骤在进行操作
```

如设置开机自启动错误解决办法：

```
修改 /etc/postfix/main.cf的设置

inet_protocols = ipv4
 
inet_interfaces = all
```

修改这两个配置就好了参考的是下面的这个博客

<https://blog.csdn.net/abc409944643/article/details/102212435>

然后再启动就可以了，不会报错

以上命令是启动并设置开机自启

### **4、安装gitlab-ce社区版本**

```
yum install -y gitlab-ce
```

## 配置gitlab完成初始化工作

### **1、修改配置**

```
vi /etc/gitlab/gitlab.rb
```

 将external_url 'http://gitlab.example.com'地址修改成服务器的ip+port

修改nginx['listen_port'] = 自己端口号 

### **2、加载配置并重启gitlab**

首次启动也需要以下命令加载配置，完成初始化

```
sudo gitlab-ctl reconfigure
```

 重启gitlab

```
sudo gitlab-ctl restart
```

### **3、GitLab常用命令**

```
gitlab-ctl start    # 启动所有 gitlab 组件；
gitlab-ctl stop        # 停止所有 gitlab 组件；
gitlab-ctl restart        # 重启所有 gitlab 组件；
gitlab-ctl status        # 查看服务状态；
gitlab-ctl reconfigure        # 启动服务；
vim /etc/gitlab/gitlab.rb        # 修改默认的配置文件；
gitlab-rake gitlab:check SANITIZE=true --trace    # 检查gitlab；
gitlab-ctl tail        # 查看日志；
```

注：防火墙开放自己的端口，这边我使用的是9081端口。

```
firewall-cmd --add-port=9081/tcp --permanent
firewall-cmd --reload
```

## **配置gitlab邮件服务** 

### 1、配置邮箱服务的用途 

有合并请求时，邮件通知 账号注册时，邮件验证修改密码时，通过邮件修改 

### 2、配置步骤 

开启QQ邮箱的smtp服务(不建议使用163邮箱，发几次之后，就不能发送) 

设置--》账户--》smtp--》密保验证--》验证成功返回一串字符串，形状如（qxjnpaucsskvbgid） 

保存返回的字符串 

修改gitlab配置 

vim /etc/gitlab/gitlab.rb 

按/后输入smtp_enable，找到下面这一串文本，进行修改 

```
gitlab_rails['smtp_enable'] = true 
gitlab_rails['smtp_address'] = "smtp.qq.com" 
gitlab_rails['smtp_port'] = 465 
gitlab_rails['smtp_user_name'] = "你的qq地址" 
gitlab_rails['smtp_password'] = "开通smtp时返回的字符" 
gitlab_rails['smtp_domain'] = "qq.com" 
gitlab_rails['smtp_authentication'] = "login" 
gitlab_rails['smtp_enable_starttls_auto'] = true 
gitlab_rails['smtp_tls'] = true 
user['git_user_email'] = "你的qq地址" 
gitlab_rails['gitlab_email_from'] = 'qq邮箱'
```

### 3、测试邮件服务是否正常 

```
gitlab-rails console 
```

Notify.test_email('接收方邮件地址','邮件标题','邮件内容').deliver_now 

按回车，测试发送。

## 使用GitLab

### 1、登录

在浏览器的地址栏中输入ECS服务器的公网IP即可登录GitLab的界面，第一次登录使用的用户名和密码为 root 和 5iveL!fe。首次登录会强制用户修改密码。密码修改成功后，输入新密码进行登录

![](img\00.png)

### 2、上传本地代码到project-使用http

在GitLab的主页中新建一个Project

![](/img/01.png)

本地项目推送到gitlab使用http方式

```
git init
git add .
git commit -m "commit project"
git remote add origin http://139.224.50.180:9081/root/boot.git
git push -u origin master
--需要输入用户名及密码

```

### 3、上传本地代码到project-使用ssh

配置ssh，本地使用`ssh-keygen -t rsa`命令一直回车直到结束

![](/img/02.png)

在C:\Users\用户.ssh下产生两个文件：id_rsa和id_rsa.pub 

用记事本打开id_rsa.pub文件，复制内容，在gitlab.com的网站上到ssh密钥管理页面，添加新公钥，随便取个 

名字，内容粘贴刚才复制的内容。

![](/img/03.png)

本地项目推送到gitlab使用ssh方式

```
git init
git add .
git commit -m "commit project"
git remote add origin git@139.224.50.180:root/boot.git
git push -u origin master
--需要输入用户名及密码
```

### 4、开启注册邮箱验证 

admin area --》setting--》Sign-up restrictions--》勾选Send confirmation email on sign-up 

### 5、创建组 

首页->create a group

访问级别 

Private：只有组成员才能看到

Internal：只要登录的用户就能看到 

Public：所有人都能看到 

Guest：可以创建issue、发表评论，不能读写版本库 、Reporter：可以克隆代码，不能提交 

Developer：可以克隆代码、开发、提交、push Master：可以创建项目、添加tag、保护分支、添加项目成员、编辑项目 

Owner：可以设置项目访问权限 - Visibility Level、删除项目、迁移项目、管理组成员 

### **6、gitlab分支及标签的保护** 

为什么要保护分支？ 

保护特定的分支不被随便合并，以免影响相应的分支 

进入项目--> repository-->branches-->project setting 

注意 能push 就能merge ，相应的权限把握好（master分支设置只能masters可以合并） 

