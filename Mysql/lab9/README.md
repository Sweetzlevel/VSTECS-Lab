# MySQL Enterprise Edition Lab8
MySQL Replication

# Preparation
|Create VMname| Role    |
|------------------------|------------------------|
|[uxmaster](../lab1) |[Application Server]
|[uxnode2]|[Cluster-Node1] 
|[uxnode3]|[Cluster-Node2]
|[uxnode4]|[Cluster-Node3]

## Install Mysql Server All Node
```
rpm -Uhv /opt/packages/mysql/*.rpm
systemctl enable mysqld
service mysqld start
mysql -p`grep "temporary password" /var/log/mysqld.log | awk '{ print $NF }'`
```
mysql>
```
alter user root@'localhost' identified by 'MySQL8.0';
SELECT * FROM mysql.component;
uninstall component 'file://component_validate_password';
alter user root@'localhost' identified with mysql_native_password by '';
create user root@'%' identified with mysql_native_password by ''; grant all privileges on *.* to root@'%' with grant option;
flush privileges;
```
# Install Mysql Innodb cluster by mysqlsh 
```
mysqlsh
```
MySQL  JS >
```

dba.configureInstance('u1node2.vstecs.local:3306');
dba.configureInstance('u1node3.vstecs.local:3306');
dba.configureInstance('u1node4.vstecs.local:3306');
dba.checkInstanceConfiguration('u1node2.vstecs.local:3306');
dba.checkInstanceConfiguration('u1node3.vstecs.local:3306');
dba.checkInstanceConfiguration('u1node4.vstecs.local:3306');
\connect u1node2.vstecs.local:3306
cluster =dba.createCluster('cl'); 
cluster.addInstance('u1node3.vstecs.local:3306');
cluster.addInstance('u1node4.vstecs.local:3306'); 
cluster.status();
\q
```

# Master 
```
rpm -Uvh /opt/packages/router/*.rpm
su - opc
mysqlrouter --bootstrap uxnode2:3306 --directory cl
/home/opc/cl/start.sh
```
## Demo ###