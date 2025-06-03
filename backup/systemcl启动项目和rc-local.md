rc-local
rc-local.service 是一个 systemd 兼容层服务，用于在现代 Linux 系统中继续支持传统的 /etc/rc.local 脚本机制，使用户能在开机时运行自定义命令或脚本。
需要配置文件拥有可执行权限，自定义的脚本或者命令也可以持久化在这个配置文件中

```
chmod +x /etc/rc.d/rc.local

[root@qfusion1 lgd]# cat /etc/rc.d/rc.local
touch /var/lock/subsys/local
modprobe br_netfilter
iptables -P FORWARD ACCEPT
<自定义的命令>
``` 
<img width="456" alt="Image" src="https://github.com/user-attachments/assets/f1206eb8-8d9a-4e58-922d-306ef5c8708c" />