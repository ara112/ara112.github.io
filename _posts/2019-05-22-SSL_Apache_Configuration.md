---
layout: post
title: "SSL Apache下的配置"
date: 2019-05-22
excerpt: "SSL Apache下的配置"
tags: [Website, LAMP, basic]
comments: true
---

> 参考文档：  
> [腾讯云 - 证书安装指引](https://cloud.tencent.com/document/product/400/4143)

_使用环境：  
Linux 版本：[CentOS 7.6](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso)  
PHP 版本：[PHP 7.2](https://www.php.net/downloads.php)  
Apache 版本：[Apache/2.4.6]()_  

### SSL 配置

1.  前提：已申请证书并下载证书压缩包  
2.  安装配置  
    -   将压缩包解压缩，把 Apache 文件夹内的三个文件 1_root_bundle.crt、2_www.domain.com.crt、3_www.domain.com.key 拷贝到服务器的 /etc/httpd/ssl 目录下，如无此目录可新建  
    -   <code># yum install mod_ssl </code> 安装 mod_ssl.so 模块  
    -   编辑 /etc/httpd/conf.d 目录下的 ssl.conf 配置文件  

            <VirtualHost 0.0.0.0:443>  
                DocumentRoot "/var/www/html"  
                ServerName www.domain.com  
                SSLEngine on  
                SSLCertificateFile /etc/httpd/ssl/2_www.domain.com.crt  
                SSLCertificateKeyFile /etc/httpd/ssl/3_www.domain.com.key  
                SSLCertificateChainFile /etc/httpd/ssl/1_root_bundle.crt  
            </VirtualHost>   

        DocumentRoot：网站目录  
        ServerName：网站域名  
        SSLEngine on：启用 SSL 功能  
        SSLCertificateFile：证书文件路径  
        SSLCertificateKeyFile：私钥文件路径  
        SSLCertificateChainFile：证书链文件路径

    -   <code># systemctl restart httpd.service </code> 重启 Apache 服务  
3.  问题相关   
    -   如果在重启 Apache 服务时报错 'Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.'，可依提示输入<code># systemctl status httpd.service </code>查看状态，获取错误详细信息。如果是复制粘贴的上方参考文档-腾讯云证书安装指引的参考代码，需注意检查**文件名与路径**是否一致。  


# 更改配置文件要重启 \* 100遍！
