# MySQL Enterprise Edition Lab8
MySQL Replication

# Preparation
|Create VMname| Role    |
|------------------------|------------------------|
|[uxmaster](../lab1) |[Master](../lab8#on-the-master)
|[uxnode1](../lab1) |[Slave](../lab8#on-the-master)

# On the MASTER
edit /etc/my.cnf 
```
log-bin
gtid_mode=ON
enforce-gtid-consistency
log-slave-updates
relay-log-recovery
binlog_format=mixed
server-id=1
```
restart mysql service 
```
service mysqld restart
```
```
mysql
```
mysql>
```
CREATE USER 'rpl'@'%' IDENTIFIED BY 'slave';
GRANT REPLICATION SLAVE ON *.* TO 'rpl'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
```
# On the SLAVE
edit /etc/my.cnf 
```
log-bin
gtid_mode=ON
enforce-gtid-consistency
log-slave-updates
relay-log-recovery
binlog_format=mixed
server-id=2
```
restart mysql service 
```
service mysqld restart
```
```
mysql
```
mysql>
```
CHANGE MASTER TO MASTER_HOST='u1master', MASTER_USER='rpl', MASTER_PASSWORD='slave', MASTER_AUTO_POSITION = 1;
SHOW warnings;
SHOW SLAVE STATUS\G
START slave;
SHOW SLAVE STATUS\G
SET GLOBAL general_log = 'ON';
\q

```
```
tail -f /var/lib/mysql/Workshop-???.log
```
## On the master
mysql>
```
create database replication;
use replication;
create table test_replication (id int NOT NULL auto_increment, data varchar(40), primary key (id));
insert into replication.test_replication (data) values ("test1"), ("test2");

show slave hosts;

show master status;
```
## On the salve
mysql>
```
select * from replication.test_replication;

show slave status\G

\q
```
```
tail -f /var/lib/mysql/Workshop-???.log
```

# Semi-Sync
## On the Master
mysql>
```
INSTALL PLUGIN rpl_semi_sync_master SONAME 'semisync_master.so';
show plugins;

SET GLOBAL rpl_semi_sync_master_enabled = 1; SET GLOBAL rpl_semi_sync_master_timeout = 10000;

```
## On the Slave
mysql>
```
INSTALL PLUGIN rpl_semi_sync_slave SONAME 'semisync_slave.so';

SET GLOBAL rpl_semi_sync_slave_enabled = 1;

STOP SLAVE IO_THREAD; START SLAVE IO_THREAD;

```
## On Both
mysql>
```
SHOW VARIABLES LIKE 'rpl_semi_sync%'; SHOW STATUS LIKE 'Rpl_semi_sync%';
```
# Test Repication
### Disable Network connection 
mysql>
```
insert into replication.test_replication (data) values ("Semi Sync broken....");

select * from replication.test_replication;

SHOW VARIABLES LIKE 'rpl_semi_sync%'; SHOW STATUS LIKE 'Rpl_semi_sync%';


```
### Enable Network connection 
mysql>
```

insert into replication.test_replication (data) values ("Semi Sync restore....");

SHOW VARIABLES LIKE 'rpl_semi_sync%'; SHOW STATUS LIKE 'Rpl_semi_sync%';

select * from replication.test_replication;
```
## On slave
```
select * from replication.test_replication;
```

Next: [InnoDB Cluster](../lab9) 
