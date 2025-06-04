### **登录**
```shell
psql -h 246.106.112.24 -p 5432 -U postgres -W 'ie4PqMrgd@qe9rpe'

有些容器内的环境变量可以直接获取密码
env |grep -i pass
```

### **备份**
```shell
gs_dump -h 127.0.0.1 -p 5432 -U postgres -F d -f ./webserver_dir webserver
```
> • -h 127.0.0.1：明确使用 TCP 连接
> • -p 5432：端口号（
> • -U postgres：连接用户
> • -F d：目录格式
> • -f ./webserver_dir：备份目标目录
> • webserver：按照数据库名来进行备份

恢复命令
```shell
 gs_restore -h 127.0.0.1 -p 5432 -U postgres -d webserver_backup ./webserver_dir 
```

###  操作数据库
``` shell
##看数据库列表
\l
##切换数据库
\c databasename
##删除数据库
drop databasename
##创建数据库
create database dbtest
```




