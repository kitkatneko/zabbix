# Install Zabbix 5 on Centos 7

  ## Check your release info and network
    cat /etc/redhat-release
    ip r

  ## Install the latest epel repo
    yum install epel-release -y
    yum -y update

  ## Get Zabbix and install
    rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
    yum clean all
    yum install zabbix-server-mysql zabbix-agent
    yum install centos-release-scl
  ## Enable for front end
    vi /etc/yum.repos.d/zabbix.repo
    
    [zabbix-frontend]
    enabled=1
  ## Install DB
    yum install zabbix-web-mysql-scl zabbix-apache-conf-scl
    yum -y install mariadb-server
    systemctl start mariadb
    systemctl enable mariadb
  ## Create and import the DB
  `mysql -u root -p`
    
    MariaDB [(none)]> create database zabbix_db character set utf8 collate utf8_bin;
    MariaDB [(none)]> create user zabbix_user@localhost identified by 'passw0rd123';
    MariaDB [(none)]> grant all privileges on zabbix_db.* to zabbix_user@localhost;
    MariaDB [(none)]> flush privileges;
    MariaDB [(none)]> \q
    
  ## Point to the DB
  `vi /etc/zabbix/zabbix_server.conf`
  
    DBName=zabbix_db
    DBUser=zabbix_user
    DBPassword=passw0rd123
  ## Poke holes
    systemctl restart zabbix-server zabbix-agent httpd rh-php72-php-fpm
    systemctl enable zabbix-server zabbix-agent httpd rh-php72-php-fpm
    firewall-cmd --add-service={http,https} --permanent
    firewall-cmd --add-port={10051/tcp,10050/tcp} --permanent
    firewall-cmd --reload
    
  ## Disable selinux
  `vi /etc/selinux/config`
  
    set SELINUX=disabled
    
  and `reboot`
  
  
