---
layout: post
title: "CentOS 7 LNMP安装记录"
date: 2019-05-05
excerpt: "CentOS 7 LNMP安装记录"
tags: [Linux, LNMP, basic]
comments: true
---

> 参考文档：  
> [webtatic - PHP 7.2 on CentOS/RHEL 7.5 via Yum](https://webtatic.com/packages/php72/)  
> [菜菜生活 - CentOS 7 安装 PHP 7.2](http://www.caicaishouyou.com/2019/02/22/centos-7-安装-php-7-2/)  
> [nixCraft - How to install and use Nginx on CentOS 7 / RHEL 7](https://www.cyberciti.biz/faq/how-to-install-and-use-nginx-on-centos-7-rhel-7/)  
> [runoob - Python pip 安装与使用 ](https://www.runoob.com/w3cnote/python-pip-install-usage.html)  
> [Packagist / Composer
中国全量镜像 - 如何安装 Composer ](https://pkg.phpcomposer.com/#how-to-install-composer)  
> [XxElza - supervisor 守护进程 ](https://note.youdao.com/ynoteshare1/index.html?id=37d5186c9d0c20f9e131d38ba0e2bc54&type=note)  

_使用环境：  
Linux 版本：[CentOS 7.6](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso)  
PHP 版本：[PHP 7.2](https://www.php.net/downloads.php)  
Python 版本：[Python 2.7](https://www.python.org/download/releases/2.7/)  
Nginx 版本：[Nginx 1.16.0](https://nginx.org/en/download.html)_

### 常用命令

### 问题集解
1.  使用 'pip install extern' 报错 'Unknown distribution option: 'python_requires''
    -   <code># pip install --upgrade pip setuptools</code> 更新 setuptools  
    参考 [IT菜鸟园 - python 错误 error: invalid command 'egg_info'](https://www.cnblogs.com/cainiaoit/p/8376773.html)  
2.  使用 'pip install extern' 报错 'no acceptable C compiler found in $PATH'
    -   <code># yum install gcc</code> 安装 C 编译器  
    参考 [Shun2018 - no acceptable C compiler found in $PATH](https://www.jianshu.com/p/5fbbe1435db2)  
3.  使用 'pip install extern' 报错 'fatal error: Python.h: No such file or directory'
    -   <code># yum install python-devel</code> 安装 python 的开发版  
    参考 [tianxiajianling - fatal error: Python.h: No such file or directory](https://blog.csdn.net/tianxiajianling/article/details/6636204)  
4.  使用 'curl -sS' 下载文件时报错 'curl: (35) Encountered end of file'
    -   服务器 443 端口未开启  
5.  使用 composer 时报错 '/usr/bin/env: php: No such file or directory'
    -   服务器 443 端口未开启  

### PHP 7.2 安装

1.  安装 epel
    -   <code># yum install epel-release </code>
2.  安装 php 及扩展
    -   <code># rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm </code> 获取 php 7.x rpm 包
    -   <code>yum search php72w</code> 查看可安装扩展
    -   <code># yum install 扩展名 </code> 安装扩展
        -   <code># yum install php72w php72w-cli php72w-fpm php72w-opcache php72w-cgi php72w-common php72w-devel php72w-mbstring php72w-pdo php72w-xml </code> 常用扩展
    -   安装完成后可使用 <code>php -v</code> 查看当前 php 版本
3.  启动 php-fpm 服务
    -   <code># systemctl enable php-fpm.service </code> 设置 php-fpm 开机自启
    -   <code># systemctl start php-fpm.service </code> 开启 php-fpm
4.  卸载相关
    -   <code># rpm -qa | grep php</code> 查看已安装的 php rpm 包
    -   <code># rpm -e 扩展名</code> 卸载 rpm 包

### Nginx 安装

1.  执行指令：  
    -   <code># yum install nginx</code>  
2.  启动 Nginx  
    -   <code># systemctl start nginx</code>  
3.  设置开机启动  
    -   <code># systemctl enable nginx</code>  
4.  打开网址出现 'Welcome to nginx' 页面就是安装成功啦  
5.  开启防火墙及 80、443 端口  
    - <code># systemctl start firewalld </code>  
    - <code># firewall-cmd --permanent --zone=public --add-service=http </code>  
    - <code># firewall-cmd --permanent --zone=public --add-service=https </code>  
    - <code># firewall-cmd --reload </code>  
6.  设置 php-fpm    
    - <code># egrep '^(user|group)' /etc/nginx/nginx.conf </code>通过 egrep 查询 nginx 服务器的用户和用户组  
    结果示例：user nginx  
    - <code># vi /etc/opt/remi/php72/php-fpm.d/www.conf </code>修改执行 php-fpm 的权限，设置用户和用户组为 nginx  

            user = nginx  
            group = nginx  

    - <code># systemctl restart php-fpm.service </code>保存修改后重启 php-fpm 服务  

7.  指令相关
    -   <code># systemctl status nginx</code> 检查 Nginx 运行状态  
    -   <code># systemctl restart nginx</code> 重启 Nginx  
    -   <code># systemctl stop nginx</code> 停止 Nginx  


### Composer 安装

1.  执行指令：
    -   <code># php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"</code> 下载安装脚本文件到当前目录  
    -   <code># php composer-setup.php</code> 执行安装过程
    -   <code># php -r "unlink('composer-setup.php');"</code> 删除安装脚本文件
    -   <code># mv composer.phar /usr/local/bin/composer</code> 移动上面下载的 composer.phar 到 /usr/local/bin文件夹中，并重命名为 composer  

## 更改配置文件要重启 \*100遍！
