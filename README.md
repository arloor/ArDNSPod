# ddnspod

基于DNSPod用户API实现的纯Shell动态域名客户端，适配网卡地址。

# 使用方式

(在centos7 上测试通过

原理说明，定时调用DNSPOD（腾讯云）的api，更新DNSPOD（腾讯云）中的域名解析记录。

因此，要满足如下3个前提条件：

- 有一个域名在DNSPOD（腾讯云）腾讯云中解析
- 登陆DNSPOD后台，增加一个token
- 新建一个A记录，例如xxx.arloor.com 指向 127.0.0.1，之后这个A记录就会定时地被脚本修改（如果不做这个，会失败）

其中提到的token和A记录会需要写进dns.conf中，下面是如何在nat vps上部署这个脚本：

```shell
systemctl status crond
systemctl enable crond
systemctl restart crond

sudo su
cd /usr/local
git clone https://github.com/arloor/ddnspod.git
cd ddnspod
cp dns.conf.example dns.conf
# vi dns.conf  #编辑dns.conf
# ---- arToken="8xx74,69a5fxxxxxxxxxxxxx75b0ecd1e"  #修改为自己的
# ---- arDdnsCheck "arloor.com" "xxx"               #修改为自己的
# --------------------------------------------------------------
echo "* * * * * root /usr/local/ddnspod/ddnspod.sh &>> /root/ddns.log" >> /etc/crontab
cd 
```

现在，每分钟会执行一次

```shell
/usr/local/ddnspod/ddnspod.sh &>>  /root/ddns.log
```

从而检查公网ip，自动修改A记录指向该nat机器的公网ip。可以通过`tailf /var/log/cron`命令查看crontab定时任务的运行情况。

# 最近更新

2019/3/25
- 使用360的api获取nat机器的公网ip，适用于natcloud、nathosts、uovz等商家出售的nat机器。

2015/2/24
- 增加token鉴权方式 (by wbchn)

2015/7/7
- 使用D+服务获取域名解析

2016/2/25
- 增加配置文件，分离脚本与配置，适配内网。
- 加入Mac支持
- sed脚本POSIX化，可跨平台

2016/3/23
- 进一步POSIX化，支持Mac和大部分Linux发行版
- 更改配置文件格式

# Credit

Original: anrip

This version maintained by ProfFan
