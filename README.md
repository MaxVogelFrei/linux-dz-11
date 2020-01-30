# linux-dz-11
Мониторинг

Настроить дашборд с 4 графиками
1) память
2) процессор
3) диск
4) сеть

[результат](linux-dz-11.PNG)

## Проброс порта в vagrant
Для доступа к веб-интерфейсу перенаправляю порт 8080 на 80 порт сервера ZABBIX
```yaml
Vagrant.configure(2) do |config|

  config.vm.network "forwarded_port", guest: 80, host: 8080,
    auto_correct: true
```
## Установка ZABBIX
```bash
yum install epel-release -y
yum update -y
yum install zabbix40-server-mysql zabbix40-web-mysql zabbix40-agent mariadb-server -y
systemctl start mariadb
systemctl enable mariadb
```
### базовая настройка БД
```bash
mysql_secure_installation
mysql -uroot -p
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> grant all privileges on zabbix.* to zabbix@localhost identified by '####';
mysql> quit;
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/schema.sql
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/images.sql
mysql -uzabbix -p zabbix < /usr/share/zabbix-mysql/data.sql
```
### настройка сервера ZABBIX
```bash
vi /etc/zabbix/zabbix_server.conf

DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=#####

systemctl start zabbix-server
systemctl enable zabbix-server-mysql
```
### исправляю timezone в httpd
```bash
vi /etc/httpd/conf.d/zabbix.conf
systemctl start httpd
systemctl enable httpd
```
### проверяю запрос данных количества операций чтения/записи
```bash
zabbix_get -s 127.0.0.1 -k "vfs.dev.write[all,ops,avg1]"
zabbix_get -s 127.0.0.1 -k "vfs.dev.read[all,ops,avg1]"
```
