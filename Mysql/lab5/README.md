# MySQL Enterprise Edition Lab4
MySQL Enterprise Firewall

# Preparation
[Create New Instance](../lab1#create-new-mysql-instance)

## Started with mysqld_safe
```
sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cnf 2>&1 &>/dev/null &

```

# Install the firewall
```
mysql -S /lab/mysql_home01/mysqld.sock mysql < /lab/mysql/share/linux_install_firewall.sql
```


Next: [ MySQL Enterprise Firewall](../lab5) 