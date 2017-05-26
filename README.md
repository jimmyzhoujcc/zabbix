# install mysql-server
rpm ivh mysql57-community-release-el7-11.noarch.rpm
yum repolist enabled | grep "mysql.*-community.*"
yum install mysql-community-server
systemctl enable mysqld
systemctl start mysqld
grep 'temporary password' /var/log/mysqld.log
mysql -uroot -p
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
create database zabbix character set utf8 collate utf8_bin;
grant all privileges on zabbix.* to zabbix@localhost identified by '<password>';
quit;

yum install zabbix-server-mysql zabbix-web-mysql -y
zcat /usr/share/doc/zabbix-server-mysql-3.2.*/create.sql.gz | mysql -uzabbix -p zabbix

# setting zabbix server
vi /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=<password>

setenforce 0
systemctl start zabbix-server
systemctl enable zabbix-server
systemctl stop firewalld
systemctl disable  firewalld

vi /etc/php.ini
date.timezone =  PRC

systemctl start httpd

yum install zabbix-agent
zabbix_agentd

# zabbix web username/password
Admin/zabbix
enable zabbix_server