### 主机内核设置
**cat /etc/sysctl.conf**
```vm.overcommit_memory = 1
sysctl -p
```
> 是 Linux 内核参数，表示允许内存过量分配，即使当前物理内存不足，也会允许应用程序请求更多内存。
设置为1  的时候
内核无条件允许内存过量分配（最宽松策略），不进行检查。这可能导致内存严重不足时触发OOM killer杀掉某些进程，但对Redis非常有利。

### 编译安装redis
安装redis需要内存分配工具jemalloc
```
yum install jemalloc -y
或者手动编译安装
tar -xjf jemalloc-5.2.1.tar.bz2
cd jemalloc-5.2.1
./configure
make
make install
```

### 安装redis
```
yum groupinstall "Development Tools" -y
wget http://download.redis.io/redis-stable.tar.gz

tar -xzvf redis-stable.tar.gz
cd redis-stable
make
sudo make install
```
redis-server --version 来检查，正常返回即可


### 配置启动文件
```
# Redis 配置文件示例

# 网络设置
bind 0.0.0.0
# 允许所有 IP 连接 (可以更改为特定 IP 地址，例如 127.0.0.1 限制为本地访问)
port 6379
# Redis 监听端口，默认是 6379
requirepass zaq1ZAQ!
# 设置连接 Redis 时需要的密码

# 后台运行
daemonize yes
# 启用后台模式 (使 Redis 作为守护进程运行)

# 持久化设置
save 900 1
# 每 900 秒（15 分钟）如果至少有 1 次写操作发生，Redis 会保存 RDB 快照
save 300 10
# 每 300 秒（5 分钟）如果至少有 10 次写操作发生，Redis 会保存 RDB 快照
save 60 10000
# 每 60 秒，如果至少有 10000 次写操作发生，Redis 会保存 RDB 快照

appendonly yes
# 启用 AOF 持久化
appendfsync everysec
# 每秒同步 AOF 文件

# 内存管理
#maxmemory 2gb
# 限制 Redis 最大内存为 2GB
#maxmemory-policy allkeys-lru
# 超过最大内存时，Redis 会使用 LRU 策略淘汰键

# 日志设置
#logfile "/var/log/redis/redis.log"
# 设置日志文件路径
#loglevel notice
# 设置日志级别，可以是 debug、verbose、notice 或 warning

# 客户端输出缓冲区限制
#client-output-buffer-limit normal 0 0 0
# 正常客户端的输出缓冲区限制，设置为 0 表示无限制

# 数据库
#databases 16
# Redis 默认有 16 个数据库，可以通过数字来切换。默认使用数据库 0

# 保护模式
protected-mode yes
# 启用保护模式，如果没有密码，Redis 将不允许外部访问

# 进程设置
#pidfile /var/run/redis.pid
# 设置 Redis 进程的 PID 文件路径
#unixsocket /var/run/redis/redis.sock
# Unix 套接字连接路径，适用于同一机器上的应用程序
```
redis-cli -a zaq1ZAQ! 能登录即可

### 备库的设置
其他安装方式同上
启动配置文件中需要指定
```
#指定主库
replicaof  10.10.50.236 6379
masterauth zaq1ZAQ!
```
同样**redis-server  redis.conf**启动服务

 > 一主两从搭建完成后在主库执行 redis-cli -a zaq1ZAQ! info replication 返回备库已连接ip

### 哨兵节点的配置
vi sentinel.conf
```
#sentinel 监控主节点
sentinel monitor mymaster 10.10.50.236 6379 2
# 设置 Sentinel 的投票数（最小 2，表示需要两个 Sentinel 一致同意才能发生故障转移）
sentinel down-after-milliseconds mymaster 5000
# 设置密码（如果 Redis 主从启用了密码）
sentinel auth-pass mymaster zaq1ZAQ!
# 守护进程
daemonize yes
``` 
启动命令
**redis-sentinel sentinel.conf**

> 注意：这里# Generated by CONFIG REWRITE 后面的是哨兵自动读取配置的文件内容
自动生成的配置不会干扰你手动指定的核心配置，它们只是用于 Sentinel 自己的内部状态维护。但建议不要手动修改这些自动生成字段，否则 Sentinel 启动后需要重新探测节点状态，可能会造成短暂的不一致。

### systemctl启动项来管理redis服务，保障服务redis的开机自启
> 注意，如果要通过systemctl启动项来管理redis服务的话，需要先关闭手动拉起的redis-server redis.conf 的进程
编写启动项控制文件
```
vi /etc/systemd/system/redis.service

[Unit]
Description=Redis
After=network.target
[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /root/redis-stable/redis.conf --daemonize yes
ExecStop=/usr/local/bin/redis-cli -h 127.0.0.1 -p 6379 shutdown
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```
**systemctl enable redis
systemctl start redis**


