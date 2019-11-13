# MySQL Enterprise Edition Lab4
MySQL Enterprise Backup

# Preparation
[Create New Instance](../lab1#create-new-mysql-instance)

### Started with mysqld_safe
```
sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cfg 2>&1 &>/dev/null &

```
### Create sample data 
```
mkdir /lab/script
cat << EOF > /lab/script/init.sh
mysql -t -S /lab/mysql_home01/mysqld.sock << EOL
create database if not exists mydb;
use mydb;
create table if not exists mytable1 (f1 int not null primary key, f2 varchar(20)
);
create table if not exists mytable2 (f1 int not null auto_increment primary key,
 f2 varchar(20));
m
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
session.runSql('show table status');
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

### Create Backup Folder
```
mkdir -p /lab/backup/dump
mkdir -p /lab/backup/full
mkdir -p /lab/backup/log

```
### Dump all data 
```
time mysqldump -uroot -h127.0.0.1 -P3310 --all-databases --set-gtid-purged=auto --triggers --routines --events --single-transaction > /lab/backup/dump/mydump.sql

```
### Backup data 
```
time mysqlbackup --port=3310 --host=127.0.0.1 --protocol=tcp --user=root --backup-dir=/lab/backup/full --with-timestamp --backup-image=image3310.img --compress backup-to-image > /lab/backup/log/mybackup.log 2>&1

cat /lab/backup/log/mybackup.log
```

### View data dir 
```
mysql -uroot -h127.0.0.1 -P3310 -e "select @@datadir\G" |grep datadir|cut -f2 -d\  

```
Note: Path Source

### View image folder 
```
mysql -uroot -h127.0.0.1 -P3310  -e " select backup_id, backup_destination, from_unixtime(left(consistency_time_utc,10) + right(consistency_time_utc,6)/1000000) as backup_time, (end_time_utc - start_time_utc)/1000000 as duration from mysql.backup_history where backup_format='IMAGE' and backup_type='FULL' order by backup_time desc limit 1\G" |  grep backup_destination|cut -f2 -d\"
```
Note: Path Destination

 ### Copy auto files from data dir to image folder 
 cp [Path Source]/auto.cnf [Path Destination]
 
 # Restore 
 ## Preparation 
 ```
 mysql -uroot -h127.0.0.1 -P3310 -e "shutdown;"
 rm -rf /lab/mysql_home01/*
 mkdir -p /lab/restore 
 ls -lt /lab/backup/full/*/*.img|sed -n '1 p'|awk '{print $9;}' 
 ```
 Note:show image to restore 
 
 ## Restore Image 
 ```
 mysqlbackup --defaults-file=[BACKUP PATH]/my.cfg \
        --backup-dir=/lab/restore \
        --backup-image= [IMAGE PATH]\
        image-to-backup-dir
```
 ## Uncompress Image 
``` 
 mysqlbackup --defaults-file=[BACKUP PATH]/my.cfg \
        --backup-dir=/lab/restore  \
        --uncompress \
        apply-log
 ```
 ## Copy back
 ```
  mysqlbackup --defaults-file=[IMAGE PATH]/my.cfg \
        --force --backup-dir=/lab/restore \
        copy-back
  cp -f [IMAGE PATH]/my.cfg /lab/mysql_home01/ 
  cp -f [IMAGE PATH]/auto.cfg /lab/mysql_home01/
 ```
 ## Start Server 
 ```
 sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cfg 2>&1 &>/dev/null &
 ```
 
 
 
 

