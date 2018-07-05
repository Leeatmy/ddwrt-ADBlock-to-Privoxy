# 基于 DD-Wrt 的广告过滤
### 利用「DD-Wrt」中的「Privoxy」实现「ADBlock」功能

- 基于方法来自 https://github.com/jamesmacwhite/ddwrt-adbp-to-privoxy

- 在本人的路由器上已经运行了2、3年时间，非常稳定。

- 选用DD-Wrt原因，版本更新非常勤快，多少年只遇过2、3个地雷版本，实际运行非常稳定，没有掉线过；不喜欢折腾。

## 准备App

- 文本编辑器 CotEditor

- SSH访问 Cyberduck

## 准备好一台支援DD-Wrt的路由器
> 下载列表中就可以查看支援哪台设备

DD-Wrt固件下载 https://dd-wrt.com/support/other-downloads/ 

点开此链接 =》点年份 例如`2018` =〉再点最新的日期 例如`06-29-2018-r36247` =》选择 自己的设备

刷机方法，这里省略。

## 刷好DD-Wrt后，要确保。
> 这是用`Cyberduck`利用`ssh`上传需要。

- `用户名`设定成`root`
- 开启`SSHd`（在服务界面） 与 `SSH管理` (在管理界面)
- 开启`JFFS2`
- 开启`本地DNS`（在服务界面）

## 在原来 `privoxy-blocklist.sh` 规则上加入
> 这里可以自行添加修改设置

```
ADBLOCKLISTS=" \
https://easylist-downloads.adblockplus.org/easylist.txt \
https://easylist-downloads.adblockplus.org/easyprivacy.txt \
https://easylist-downloads.adblockplus.org/fanboy-annoyance.txt \
https://easylist-downloads.adblockplus.org/fanboy-social.txt \
https://easylist-downloads.adblockplus.org/easylistchina.txt \
https://easylist-downloads.adblockplus.org/antiadblockfilters.txt \
https://easylist-downloads.adblockplus.org/malwaredomains_full.txt "
```

## 用Cyberduck上传`privoxy-blocklist.sh` 到 路由器的`/jffs/`中
- 选 `SFTP SSH文件协议`
- 输入 路由器地址 `192.168.xxx.xxx`
- 用户名 `root`
- 密码 

## 实际 设置 

![](https://raw.githubusercontent.com/Leeatmy/ddwrt-ADBlock-to-Privoxy/master/Screenshot/Screenshot1.png?raw=true)

对应`ADBLOCKLISTS` 有几个`ADBLOCKLISTS`就有几个`actionsfile`和`filterfile`。

> `服务` =》`Adblocking`

Custom Configuration

```
confdir /jffs/etc/privoxy
templdir /jffs/etc/privoxy/templates
logdir /var/log/privoxy
actionsfile match-all.action
actionsfile default.action
actionsfile user.action
actionsfile easylist.script.action
actionsfile easyprivacy.script.action
actionsfile fanboy-annoyance.script.action
actionsfile fanboy-social.script.action
actionsfile easylistchina.script.action
actionsfile malwaredomains_full.script.action
actionsfile antiadblockfilters.script.action
filterfile default.filter
filterfile easylist.script.filter
filterfile easyprivacy.script.filter
filterfile fanboy-annoyance.script.filter
filterfile fanboy-social.script.filter
filterfile easylistchina.script.filter
filterfile malwaredomains_full.script.filter
filterfile antiadblockfilters.script.filter
buffer-limit 10240
logfile logfile
tolerate-pipelining 1
enable-remote-toggle 0
enable-remote-http-toggle 0
enable-edit-actions 0
enable-compression 1
forwarded-connect-retries 0
accept-intercepted-requests 1
allow-cgi-request-crunching 0
listen-address 192.168.11.1:8118
default-server-timeout 60
keep-alive-timeout 300
socket-timeout 300
split-large-forms 0 
keep-alive-timeout 5 
handle-as-empty-doc-returns-ok 1
toggle 1
```

## 设置 每日自动更新 时间
![](https://raw.githubusercontent.com/Leeatmy/ddwrt-ADBlock-to-Privoxy/master/Screenshot/Screenshot2.png?raw=true)

> `管理` =》`Cron 附加任务`

前面的数字是分钟 后面的数字是小时

```
# Update Daily
55 5 * * * root sh /jffs/privoxy-blocklist.sh
```

## 查看是否工作
`http://config.privoxy.org/show-status`

![](https://raw.githubusercontent.com/Leeatmy/ddwrt-ADBlock-to-Privoxy/master/Screenshot/Screenshot%205.png?raw=true)

