# MySQL Enterprise Edition Lab7
MySQL Enterprise Monitor

# Preparation
Create new Server --> uxnode1
                    -->ip add 10.1.x.201

# Server [uxmaster]
||
|--------------------------|
| [Install MySQL](../lab1)
| [install MySQL Enterprise Monitor](../lab7#install-mysql-enterprise-monitor)

# Server [uxnode1]
||
|--------------------------|
| [Install MySQL](../lab1)
| [install Agent](../lab7#install-mysql-enterprise-agent--uxnode1)

# Install MySQL Enterprise Monitor
```
service httpd stop
/opt/packages/mem/mysqlmonitor-8.0.0.8131-linux-x86_64-installer.bin
```
ALL <ENTER> except password (I put MySQL8.0)
[Connect to server](https://uxmaster:18443)

### If Monitor Server is Rebooted 
```
/opt/mysql/enterprise/monitor/mysqlmonitorctl.sh start
```


# Install MySQL Enterprise agent @ uxnode1
```
ssh uxnode1
service httpd stop
mysql
```
mysql
```
create user mem@'%' identified by 'MySQL8.0'; grant all privileges on *.* to mem@'%'; flush privileges;
\q
````
```
/opt/packages/mem/mysqlmonitoragent-8.0.0.8131-linux-x86-64bit-installer.bin
Input 
    Hostname or IP address []: ==> uxmaster
    Tomcat SSL Port [18443]:   ==> Enter
    Agent Username [agent]:    ==> Enter
    Agent Password :           ==> Two Time of you password     
    ...
    All Enter 
    ...
    MySQL hostname or IP address [localhost]:   ==> u1node1
    MySQL Port [3306]:                          ==> Enter
    Admin User []:                              ==> mem
    Admin Password :                            
    Re-enter Password :
    Monitor Group []:                           ==> Enter
    ...
    All Enter 
    ...
After all done 
/etc/init.d/mysql-monitor-agent start
```


Next: [ MySQL Repication](../lab8) 
