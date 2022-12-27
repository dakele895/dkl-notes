# mysql环境搭建

## 1.mysql源码编译安装（V5.6示例）：

    yum install -y gcc gcc-c++ make tar openssl openssl-devel cmake ncurses ncurses-devel

    useradd -s /sbin/nologin mysql

    cd /usr/local/src

    wget 'https://cdn.mysql.com//Downloads/MySQL-5.6/mysql-5.6.39.tar.gz'

    tar -zxvf mysql-5.6.39.tar.gz
    
    cd mysql-5.6.39

    cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_EXTRA_CHARSETS:STRING=all -DWITH_DEBUG=0 -DWITH_SSL=yes -DWITH_READLINE=1 -DENABLED_LOCAL_INFILE=1

    make && make install

    cp support-files/mysql.server /etc/init.d/mysqld

    chmod a+x /etc/init.d/mysqld

**mysql环境变量配置:**

export PATH=$PATH:/usr/local/mysql/bin/

**mysql的启动步骤**

更新mysql配置

vim /etc/my.cnf

    [mysqld]
    bind-address=0.0.0.0
    port=3306
    datadir=/data/mysql
    user=mysql
    skip-name-resolve
    long_query_time=2
    slow_query_log_file=/data/mysql/mysql-slow.log
    expire_logs_days=2
    innodb-file-per-table=1
    innodb_flush_log_at_trx_commit = 2
    log_warnings = 1
    max_allowed_packet      = 512M
    connect_timeout = 60
    net_read_timeout = 120

    [mysqld_safe]
    log-error=/data/mysql/mysqld.log
    pid-file=/data/mysql/mysqld.pid

**mysql数据库初始化:**

    mkdir -pv /data/mysql
    chown -R mysql:mysql  /usr/local/mysql /data/mysql/
    yum install -y perl-Module-Install
    /usr/local/mysql/scripts/mysql_install_db --basedir=/usr/local/mysql --user=mysql  --datadir=/data/mysql/

**使用systemctl管理mysqld:**

vim /usr/lib/systemd/system/mysqld.service

    [Unit]
    Description=mysqld
    After=network.target
    [Service]
    Type=forking
    ExecStart=/etc/init.d/mysqld start
    [Install]
    WantedBy=multi-user.target

**启动mysql服务器:**

systemctl start mysqld

**验证Mysql是否启动:**

查看进程

查看监听

查看日志

**mysql的安全方式:**

mysql能限制ip访问、访问需要用户名密码、mysql权限还能再细分

默认让127.0.0.1访问，用户名root，不需要密码:

	mysql -uroot -h 127.0.0.1 -A

**mysql加密码:**
    
    mysqladmin -h 127.0.0.1 -u root password 'zabbixpwd'

	mysql -h 127.0.0.1 -uroot -pzabbixpwd -A

**mysql授权某ip登录:**

    GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.237.%' IDENTIFIED BY 'zabbixpwd' WITH GRANT OPTION;

    flush privileges;

**网络登录mysql:**

    mysql -h 自己的ip地址 -uroot 密码 -A
