# nginx搭建

## 1. nginx单机版普通方式搭建示例

**安装过程：**

    yum install -y wget gcc gcc-c++ make pcre pcre-devel zlib zlib-devel openssl openssl-devel

    cd /usr/local/src

    wget 'http://nginx.org/download/nginx-1.14.2.tar.gz'

    tar -zxvf nginx-1.14.2.tar.gz

    cd nginx-1.14.2

    ./configure --prefix=/usr/local/nginx

    make && make install

**手动启动和关闭Nginx：**

    环境变量：export PATH=$PATH:/usr/local/nginx/sbin/

    检查Nginx配置： nginx -t

    启动Nginx：nginx

    关闭Nginx：nginx -s stop

**验证Nginx安装是否成功：**

	
    查看进程： ps -ef | grep nginx

    nginx日志

    浏览器访问: 访问80端口 
**使用systemctl管理Nginx：**

    vim /usr/lib/systemd/system/nginx.service

输入以下内容：

    [Unit]
    Description=nginx
    After=network.target
    [Service]
    Type=forking
    ExecStart=/usr/local/nginx/sbin/nginx
    [Install]
    WantedBy=multi-user.target

启动命令： systemctl start nginx

停止命令： systemctl stop nginx

重启命令： systemctl restart nginx
   
## 2.nginx代理php示例：

nginx的默认配置无法处理php程序： /usr/local/nginx/html/test.php


<?php
  echo "taobao zabbix";
?>

nginx+php-fpm结合的配置


    location / {

                root   html;

                index  index.html index.htm index.php;

            }


    location ~ \.php$ {
        
                root           html;
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }





