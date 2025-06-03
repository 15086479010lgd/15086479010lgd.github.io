### **rc-local**
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
### **启动项**
```
列出所有启动项
systemctl list-unit-files
停止
systemctl stop
自启动
systemctl enable
关闭自启动
systemctl disable
查看服务相关配置
systemctl cat servername

``` 
### fstab服务和rc-local.service的对比和推荐使用
-  我们常用的 /etc/fstab文件来控制磁盘挂载，本质上也是用的一种systemcl控制的启动项服务。一般发生在比较早期
名称为local-fs.target或者remote-fs.target
-  而rc-local.service中自定义的开机启动服务，一般发生在local-fs.target也就是fatab之后

> 结论就是以后挂盘持久化，尽量都使用fstab服务来控制，避免在一些依赖挂载路径的服务在挂载之前执行
> 包括之后所有正式环境 需要开启 自启动的自定义脚本或者命令 都推荐单独写systemctl控制文件来明确启动顺序

| 适用情况 | 推荐用法 |
|--------|--------|
| 快速测试开机自动脚本 | ✅ 使用 rc.local |
| 正式服务、自定义应用守护| 🚫 推荐写 .service 文件 |
| 修改网络参数等后期任务 |  ✅ 使用 rc.local |
| 启动顺序要求明确 | 🚫 推荐用 systemd 单元 + After=/Before= 控制 


### 补充

- 如何查看system启动控制的服务之间的依赖管理和前后顺序
```
systemctl list-dependencies serviceA.service

systemctl show serviceA.service | grep -E 'After=|Before='

##svg文件在浏览器中打开
systemd-analyze plot > boot.svg
``` 
<img width="456" alt="Image" src="https://github.com/user-attachments/assets/f1206eb8-8d9a-4e58-922d-306ef5c8708c" />

![Image](https://github.com/user-attachments/assets/64290542-d167-4a05-a8e6-04741e4668b7)