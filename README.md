# linux-dz-11
Мониторинг

## Проброс порта в vagrant

## Уставнока ZABBIX

yum install epel-release -y
yum update -y
yum install zabbix40-server-mysql zabbix40-web-mysql zabbix40-agent mariadb-server -y
systemctl start mariadb
systemctl enable mariadb

### базовая настройка БД
mysql_secure_installation
mysql -uroot -p
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> grant all privileges on zabbix.* to zabbix@localhost identified by '####';
mysql> quit;
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/schema.sql
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/images.sql
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/data.sql

### настройка сервера ZABBIX
vi /etc/zabbix/zabbix_server.conf

DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=#####

systemctl start zabbix-server
systemctl enable zabbix-server-mysql


### исправляю timezone в httpd
vi /etc/httpd/conf.d/zabbix.conf
systemctl start httpd
systemctl enable httpd

### проверяю запрос данных о количестве операций чтения/записи

zabbix_get -s 127.0.0.1 -k "vfs.dev.write[all,ops,avg1]"
zabbix_get -s 127.0.0.1 -k "vfs.dev.read[all,ops,avg1]"


