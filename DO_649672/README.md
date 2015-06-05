# 起因

2015.06.04 从DigitalOcean收到了一个Ticket，我的第一台VPS利用SSH客户端对第三方服务器展开了DDoS攻击。为了阻止攻击继续，VPS的网络被digitalocean给关闭了。

# 对策

## 确定Hacker登陆方法

由于VPS的网络被关闭后，只能通过DigitalOcean的Web Console来访问VPS。使用管理员账号成功登陆VPS后，通过

```Shell
last
```

来显示最近登陆VPS的用户。

然后发现除了本人正常用于登陆用的账号（以及ip地址外）。git账号最近被人登陆过。

## 确定攻击进程

通过网络监视命令确认vps上开启的异常端口

```Shell
lsof -i | less
```

发现多个squid64的进程向外发送数据

## 删除异常进程

首先通过ps -ef 来确认所在位置然后使用killall

```Shell
ps -ef | less
ls -al /proc/xxx/exec
sudo killall squid64
```

## 将hacker遗留攻击脚本删除

通过上一部的ps方法，和ls -al /proc/xxx/exec (此处xxx为pid)锁定了攻击脚本所在目录。将这些攻击脚本直接delete

## 强化被hack账号

  + 修改git账号密码
  + 关闭git账号的login权限
  + 将登陆git账号的ip地址写入/etc/host.deny
  
## 提交修复记录。

重启VPS后，通过

```Shell
ps aux
```

确认是否仍有异常进程后，提交修复记录给DigitalOcean的Support Team来恢复网络。 提交资料内容

  * last command‘s screenshot
  * lsof command's screenshot
  * ps command's screenshot
  * ps -aux 's screenshow when after reboot vps
  
以上。

参考资料

  1. [http://www.jianshu.com/p/97b9dc47b88c](http://www.jianshu.com/p/97b9dc47b88c)
