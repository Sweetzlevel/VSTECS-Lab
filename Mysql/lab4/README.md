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
show table status;
show create table mytable1\G
show create table mytable2\G
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
/lab/script/init.sh >> /dev/null 
```
