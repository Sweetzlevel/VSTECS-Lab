# MySQL Enterprise Edition Lab7
MySQL Enterprise Monitor

# Preparation
Create new Server --> uxnode1
                    -->ip add 10.1.x.201

# Server [uxmaster]
||
|--------------------------|
| [Install MySQL](../lab1)
|[install Mysql Enterprise Monitor](../lab7#install-mysql-enterprise-monitor)

# Server [uxnode1]
[Install MySQL](../lab1) 

# Install MySQL Enterprise Monitor
```
service httpd stop
/opt/packages/mem/mysqlmonitor-8.0.0.8131-linux-x86_64-installer.bin
```
ALL <ENTER> except password (I put MySQL8.0)
![Connect to server](https://uxmaster:18443)



















```
Next: [ MySQL Enterprise Encryption](../lab8) 