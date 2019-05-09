---
layout: post
title: "CentOS 7 LAMP安装记录"
date: 2019-05-05
excerpt: "CentOS 7 LAMP安装记录"
tags: [Linux, LAMP, basic]
comments: true
---

> 参考文档：  
> [随缘盛世 - Centos7安装PHP、MySQL、Apache](https://www.cnblogs.com/shengChristine/p/9293996.html)  
> [菜菜生活 - CentOS 7 安装 PHP 7.2](http://www.caicaishouyou.com/2019/02/22/centos-7-安装-php-7-2/)  
> [Aaron - CentOS常用的文件操作命令总结](https://www.haorooms.com/post/centeros_wj_zj)  
> [IT热血青年 - Linux下彻底卸载mysql 图解教程](http://blog.itblood.com/completely-uninstall-the-mysql-under-linux-graphic-tutorials.html)  

_使用软件：
虚拟机：[VMware Workstation 15](https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html)
Linux 版本：[CentOS 7.6](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso)_

### 常用命令

-   ls -l  显示文件的详细信息
-   find 查找文件
-   cd    打开文件夹
-   cd /  返回根目录
-   cat   打开文件
-   crontab 定时任务
-   vi 编辑模式
    -   按键 Esc  退出模式
    -   按键 I  插入模式
    -   按键u 模式外使用，撤销所有修改
    -   :wq 保存当前修改并退出 vi
    -   :q! 放弃所有修改并退出 vi
    -   Ctrl+u 删除当前行

### 问题集解

-   使用 <code># yum -y install epel-release</code> 显示 'you need to be root to perform this commond'
    -   输入变更使用者指令 <code># su</code> 回车，输入密码，切换为 root 用户
-   安装 MySQL 后尝试初次登录显示 'Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)'
    -   我遇到的原因是 **服务未启动** ，那么依次执行
        1.  <code># service mysqld start</code> 启动MySQL
            运行结果：
            Redirecting to /bin/systemctl start  mysqld.service
        2.  <code># lsof -i:3306</code> 查询端口 3306 是否开启
            运行结果：
            COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
        3.  <code># service mysqld status</code> 查看 mysqld 服务运行状态
            运行结果：
            mysqld.service - MySQL Community Server
            Loaded: loaded
            Active: active (running)
        4.  再次输入 <code># mysql -u root</code> ，登录成功
-   Apache 启动报错 'AH00558: httpd: Could not reliably determine...'
    -   打开 apache 的配置文件 httpd.conf，查找 '#ServerName www.example.com:80'，复制粘贴去掉 # ，将 'ServerName' 改成可用域名或整句配置改为 'ServerName localhost:80'
    -   使用 'apachectl -t' 检查 Apache 配置
    -   使用 'service httpd restart' 重启服务

### PHP 7.0 安装

1.  安装 epel ( _Extra Packages for Enterprise Linux_  )
    -   <code># yum -y install epel-release</code>
2.  安装 php 及扩展（方法一）
    -   <code># rpm -Uvh <https://mirror.webtatic.com/yum/el7/webtatic-release.rpm></code> 获取 php 7.0 rpm 包
    -   <code>yum search php70w</code> 查看可安装扩展
    -   <code># yum install {扩展名}</code> 安装扩展
    -   安装完成后可使用 <code>php -v</code> 查看当前 php 版本
        ##### 注意：使用 Systemctl 命令启动 php 可能会报错  'Commond not found'
3.  卸载相关
    -   <code># rpm -qa | grep php</code> 查看已安装的 php rpm 包
    -   <code># rpm -e 扩展名</code> 卸载 rpm 包

### MySQL 安装

1.  执行指令获取 MySQL 5.6 repo 源：
    -   <code># wget <http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm></code>
2.  安装下载好的 MySQL repo rpm 包
    -   <code># rpm -ivh mysql-community-release-el7-5.noarch.rpm</code>
3.  安装 MySQL
    -   <code># yum install mysql-community-server</code>
4.  MySQL 基本配置
    -   <code># mysql -u root</code> 进入 MySQL（默认）
    -   <code>mysql> set password for 'root'@'localhost' =password('root');</code> 更改密码
        运行结果：Query OK, 0 rows affected (0.00 sec)
    -   <code>mysql> mysql -u root -p</code> 设置密码后的登录指令
        在之后的密码输入框中输入密码回车登录
5.  MySQL 基本配置
    -   <code># mysql -u root</code> 进入 MySQL（默认）
    -   <code>mysql> set password for 'root'@'localhost' = password('root');</code> 更改密码
        运行结果：Query OK, 0 rows affected (0.00 sec)
    -   <code># mysql -u root -p</code> 设置密码后的登录指令
        在之后的密码输入框中输入密码回车登录
    -   <code>mysql> exit;</code> 退出 MySQL 指令
    -   <code># vi /etc/rc.local</code> 在编辑区域内输入 <code>service mysqld start</code> 后保存退出，这样就可以实现开机启动 MySQL 服务
6.  卸载相关
    -   <code># rpm -qa | grep -i mysql</code> 查看已安装的 MySQL rpm 包
    -   <code># rpm -e 扩展名</code> 卸载 rpm 包
    -   <code># find / -name mysql</code> 查找 MySQL 相关文件夹
    -   <code># rm -rf 文件夹路径</code> 删除文件夹
        ##### 注意：卸载期间有些会报错，提示缺少相关依赖，可以使用指令 <code>rpm -e –-nodeps 扩展名</code> 不检查依赖关系直接卸载
7.  指令相关
    ！**一定要注意加分号<code>;</code>**
    -   <code>mysql> show databases;</code> 显示所有数据库
    -   <code>mysql> SELECT DISTINCT CONCAT('User: "',user,'"@"',host,'";') As query FROM mysql.user;</code> 查找数据库中的所有用户
    -   <code>mysql> show grants for 'username'@'%|ip';</code> 查看数据库中某个用户的权限

### Apache 安装

1.  执行指令：
    -   <code># yum install httpd</code>
2.  启动 Apache
    -   <code># systemctl start httpd</code>
3.  打开网址出现 'Testing 123' Apache HTTP Server Test Page powered by CentOS 页面就是安装成功啦
4.  指令相关
    -   <code># systemctl status httpd.service</code> 检查 httpd 运行状态
    -   <code># systemctl start httpd.service</code> 启动服务

<!-- ### 编辑 httpd.conf 绑定域名

不同系统存放配置文件的路径不同，CentOS 中的配置文件 httpd.conf 存放在 /etc/httpd/conf 中，使用 <code>vi /etc/httpd/conf/httpd.conf</code> 进行编辑
首先是常用项修改，将 <code>AllowOverride none</code> 改为 <code>AllowOverride All</code>
按照文件的示例在文本下方添加以下代码，再根据自己的网站配置进行一定修改

    <VirtualHost *:80>
        ServerAdmin webmaster@dummy-host.example.com
        DocumentRoot /www/docs/dummy-host.example.com
        ServerName dummy-host.example.com
        ErrorLog logs/dummy-host.example.com-error_log
        CustomLog logs/dummy-host.example.com-access_log common
    </VirtualHost>

**ServerName 后面只能加一个域名，要重复绑定则需要添加多加多个VirtualHost模块。** -->

### 绑定域名

不同系统存放配置文件的路径不同，CentOS 中的配置文件 httpd.conf 存放在 /etc/httpd/conf 中，使用 <code>vi /etc/httpd/conf/httpd.conf</code> 进行编辑。  
首先是常用项修改，将 <code>AllowOverride none</code> 改为 <code>AllowOverride All</code>。  
到 /etc/httpd/conf.d 中新建 vhost.conf 文件，在文本下方添加以下代码，再根据自己的网站配置进行一定修改。如果网站使用了 SSL 加密，则 'VirtualHost \*:80' 的端口改为443。注意 httpd.conf 文本要有该文件的添加记录，如 'Include conf/vhosts.conf'，或者 'IncludeOptional conf.d/\*.conf'。   

    <VirtualHost *:80>
        ServerAdmin webmaster@dummy-host.example.com
        DocumentRoot 网站文件目录
        ServerName 域名
        ErrorLog logs/dummy-host.example.com-error_log
        CustomLog logs/dummy-host.example.com-access_log common
    </VirtualHost>

**ServerName 后面只能加一个域名，要重复绑定则需要添加多加多个VirtualHost模块。**

## 改配置文件要重启 \*100遍！
