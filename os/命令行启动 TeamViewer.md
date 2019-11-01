> **[info] TeamViewer**
>
> 通过命令行控制远程服务器的 TeamViewer，实现获取账户、设置密码等功能。
>


# 连接远程服务器

我们通过 `TeamViewer` 连接服务器，可以很方便的监控到远程服务器的运行状况，比如交易系统的界面是否正常启动、相关的 `crontab` 操作是否及时开启等。

可是，如果 `TeamVierer` 由于某种原因而终端了（比如不小心把进程停止掉、其他程序的干扰等），这时候我们便无法通过界面化的操作来连接。那么剩下的唯一方法连接远程服务器，即通过 `ssh` 通道来连接。而正是通过 `ssh` 我们可以使用命令行来控制远程服务器，进行相应的操作。

那么问题来了，我们也可以通过命令行来控制开启 `TeamViewer` 么？如果是可以，可是我们无法看到界面，又怎么来获取相应的接入密码呢？

# 命令行控制 `TeamViewer`

其实，`TeamViewer` 自带提供了一套命令行的操作。这里我们想要

- 把原来的 `TeamViewer` 进程关闭掉
- 然后再开启一个新的进程
- 把相应的 `TeamViewer` 连接账户打印到终端
- 允许设置密码

如此一来，我们便可以通过账户和接入密码再重新通过界面化登录远程服务器了。

以下的命令我均假定使用 `root` 账户权限。

## 停止/开启

```{bash}
## 启动或停止 daemon
sudo teamviewer --daemon stop

sudo teamviewer --daemon start
```


## 打印账号

```{bash}
## 打印 id
sudo teamviewer --info print id
```

## 设置密码

```{bash}
## 设置密码
sudo teamviewer --passwd <password>
```

```{bash}
william@william-PC ~ $ ps -aux | grep teamviewer
root     13073  1.4  0.1 186112 14112 ?        Sl   11:57   0:00 /opt/teamviewer/tv_bin/teamviewerd -d
william  13238  1.4  0.0   6356  3360 ?        Ss   11:57   0:00 /opt/teamviewer/tv_bin/wine/bin/wineserver
william  13266  0.3  0.1 1128472 15612 ?       Sl   11:57   0:00 /opt/teamviewer//tv_bin/TVGuiSlave.64 31 1
william  13267  1.6  0.4 141060 33760 ?        Sl   11:57   0:00 /opt/teamviewer//tv_bin/TVGuiDelegate 31 1
william  13314  0.0  0.0  15932   932 pts/5    S+   11:57   0:00 grep --color=auto teamviewer
william@william-PC ~ $ sudo teamviewer --daemon stop

systemctl stop teamviewerd.service

william@william-PC ~ $ sudo teamviewer --daemon start

systemctl start teamviewerd.service

william@william-PC ~ $ sudo teamviewer --info print id

 TeamViewer                           12.0.76279  (DEB) 

 teamviewerd status                   ● teamviewerd.service - TeamViewer remote control daemon
   Loaded: loaded (/etc/systemd/system/teamviewerd.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2018-01-26 11:57:38 CST; 9s ago
  Process: 13377 ExecStart=/opt/teamviewer/tv_bin/teamviewerd -d (code=exited, status=0/SUCCESS)
 Main PID: 13380 (teamviewerd)
   CGroup: /system.slice/teamviewerd.service
           ├─13380 /opt/teamviewer/tv_bin/teamviewerd -d
           ├─13401 c:\TeamViewer\TeamViewer.exe                                                                                    
           ├─13544 /opt/teamviewer/tv_bin/wine/bin/wineserver
           ├─13559 C:\windows\system32\services.exe                                                                                    
           ├─13563 C:\windows\system32\explorer.exe /desktop                                                                                    
           ├─13572 /opt/teamviewer//tv_bin/TVGuiSlave.64 31 1
           └─13573 /opt/teamviewer//tv_bin/TVGuiDelegate 31 1

Jan 26 11:57:37 william-PC systemd[1]: Starting TeamViewer remote control daemon...
Jan 26 11:57:38 william-PC systemd[1]: teamviewerd.service: PID file /var/run/teamviewerd.pid not readable (yet?) after start: No such file or directory
Jan 26 11:57:38 william-PC systemd[1]: Started TeamViewer remote control daemon.
Jan 26 11:57:38 william-PC su[13403]: Successful su for william by root
Jan 26 11:57:38 william-PC su[13403]: + ??? root:william
Jan 26 11:57:38 william-PC su[13403]: pam_unix(su:session): session opened for user william by (uid=0) 

 TeamViewer ID:                        337563997 

william@william-PC ~ $ sudo teamviewer --passwd helloworld123

ok
```
