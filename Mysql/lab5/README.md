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

## Preparation sakila database 
```
cd /opt/packages/
tar -zxvf sakila-db.tar.gz
mysql -S /lab/mysql_home01/mysqld.sock < sakila-db/sakila-schema.sql
mysql -S /lab/mysql_home01/mysqld.sock < sakila-db/sakila-data.sql

mysql -S /lab/mysql_home01/mysqld.sock
```
mysql>
```
SHOW GLOBAL VARIABLES LIKE 'mysql_firewall_mode';
CREATE USER 'fwuser'@'localhost'; GRANT ALL ON sakila.* TO 'fwuser'@'localhost';
CALL mysql.sp_set_firewall_mode('fwuser@localhost', 'RECORDING');
```

## Second Terminal
```
mysql -S /lab/mysql_home01/mysqld.sock --prompt="\u [\d]> " -u fwuser sakila
```
fwuser [sakila]>
```
SELECT first_name, last_name FROM customer WHERE customer_id = 1;
UPDATE rental SET return_date = NOW() WHERE rental_id = 1;
SELECT get_customer_balance(1, NOW());
```
## First Terminal
mysql>
```
SELECT * FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_USERS;
SELECT * FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_WHITELIST;
CALL mysql.sp_set_firewall_mode('fwuser@localhost', 'PROTECTING');
delete from mysql.firewall_whitelist where USERHOST = 'fwuser@localhost' and RULE = 'SHOW TABLES';
delete from mysql.firewall_whitelist where USERHOST = 'fwuser@localhost' and RULE = 'SHOW SCHEMAS';
CALL mysql.sp_reload_firewall_rules('fwuser@localhost');
SELECT * FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_WHITELIST;
SELECT * FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_USERS;
CALL mysql.sp_set_firewall_mode('fwuser@localhost', 'PROTECTING');
```
## Second Terminal
fwuser [sakila]>
```
SELECT first_name, last_name FROM customer WHERE customer_id = '48';
SELECT first_name, last_name FROM customer WHERE customer_id = 1 OR TRUE;
SHOW TABLES LIKE 'customer%';
SHOW TABLES;
TRUNCATE TABLE mysql.slow_log;
```
## First Terminal
mysql>
```
CALL mysql.sp_set_firewall_mode('fwuser@localhost', 'DETECTING');
\q
```
## Second Terminal
fwuser [sakila]>
```
show tables;
SELECT first_name, last_name FROM customer WHERE customer_id = 1 OR TRUE;
```

## First Terminal
```
tail /var/log/mysqld.log
```
```
mysql -S /lab/mysql_home01/mysqld.sock
```
mysql>
```
SHOW GLOBAL STATUS LIKE 'Firewall%';
show global variables like '%firewall%';
```
## Uninstall mysql firewall 
```
DROP TABLE mysql.firewall_whitelist;
DROP TABLE mysql.firewall_users;
UNINSTALL PLUGIN mysql_firewall;
UNINSTALL PLUGIN mysql_firewall_whitelist;
UNINSTALL PLUGIN mysql_firewall_users;
DROP FUNCTION set_firewall_mode;
DROP FUNCTION normalize_statement;
DROP FUNCTION read_firewall_whitelist;
DROP FUNCTION read_firewall_users;
DROP FUNCTION mysql_firewall_flush_status;     # MySQL 5.6.26 and up only
DROP PROCEDURE mysql.sp_set_firewall_mode;
DROP PROCEDURE mysql.sp_reload_firewall_rules; # MySQL 5.6.26 and up only
shutdown; 
\q
```

Next: [ MySQL Enterprise Firewall](../lab5) 