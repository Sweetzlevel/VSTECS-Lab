# MySQL Enterprise Edition Lab4
MySQL Enterprise Backup

# Preparation
[Create New Instance](../lab1#create-new-mysql-instance)

### Started with mysqld_safe
```
sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cnf 2>&1 &>/dev/null &

```
### Create sample data 
```
mkdir /lab/script
cat << EOF > /lab/script/init.sh
mysql -t -S /lab/mysql_home01/mysqld.sock << EOL
create database if not exists mydb;
use mydb;
create table if not exists mytable1 (f1 int not null primary key, f2 varchar(20));
create table if not exists mytable2 (f1 int not null auto_increment primary key, f2 varchar(20));
EOL

mysqlsh --uri root:@127.0.0.1:3310 << EOL
session.runSql('set global general_log = false');
session.runSql('truncate table mydb.mytable1;')
session.runSql('truncate table mydb.mytable2;')
var i
session.runSql('set sql_log_bin=0');
for (i=0;i<10000;i++) {
    session.runSql('insert into mydb.mytable1 (f1, f2) values (' + i + ', "hello world");');
    session.runSql('insert into mydb.mytable2 (f2) values ("hello world");');
}
session.runSql('set sql_log_bin=1');
session.runSql('show table status');
EOL
mysqlsh --uri root:@127.0.0.1:3310 << EOL
var i
for (i=0;i<1000;i++) {
        session.runSql('set sql_log_bin=0');
        session.runSql('create table mydb.xmytable' + i + ' (primary key(f1)) select * from mydb.mytable1');
        session.runSql('set sql_log_bin=1');
}
EOL
EOF
chmod +x /lab/script/init.sh
```
### Run Script
```
/lab/script/init.sh
```
### Check table status @mydb
```
 mysql -S /lab/mysql_home01/mysqld.sock -e 'use mydb;show table status;'
```
###
```
mysql -S /lab/mysql_home01/mysqld.sock 
```
mysql>
```
create database backup;
create table backup.test (id int AUTO_INCREMENT PRIMARY KEY, text varchar(255));
insert into backup.test set text = "Full backup";
\q

```

### Create Backup Folder
```
mkdir -p /lab/backup/dump
mkdir -p /lab/backup/full
mkdir -p /lab/backup/inc
mkdir -p /lab/backup/log
mkdir -p /lab/backup/config
cp /lab/mysql_home01/*.cnf /lab/backup/config/
```
### Dump all data 
```
time mysqldump -uroot -h127.0.0.1 -P3310 --all-databases --set-gtid-purged=auto --triggers --routines --events --single-transaction > /lab/backup/dump/mydump.sql

```
### Drop database
```
mysql -S /lab/mysql_home01/mysqld.sock 
```
mysql>
```
drop database backup;
drop database mydb;
\q
```
### Import  data 
```
time mysql -S /lab/mysql_home01/mysqld.sock < /lab/backup/dump/mydump.sql
```


### Backup data 
```
time mysqlbackup --port=3310 --protocol=tcp --user=root --backup-dir=/lab/backup/full/ backup-and-apply-log
```

### Incremental data
```
mysql -S /lab/mysql_home01/mysqld.sock 
mysql> 
insert into backup.test set text = "Inc Backup";
\q
```
### Incremental backup
```
mysqlbackup --defaults-file=/lab/backup/config/my.cnf --port=3310 --protocol=tcp --user=root --incremental --incremental-base=dir:/lab/backup/full/ --incremental-backup-dir=/lab/backup/inc/ backup

```




