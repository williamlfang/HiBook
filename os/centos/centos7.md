# CentOS7 操作系统

> 我将根据开发经验，从安装操作系统开始介绍，然后讲解如何配置相关文件、设定操作环境。到最后，我们将搭建一套定制化的操作系统，用于开发并运行程序化交易系统。该过程将全部使用脚本进行操作，从而保证在任何一台服务器上，都达到完全一样的配置环境。

## 为何使用 `CentOS`

在服务器操作系统领域，`Linux` 绝对是独领风骚的佼佼者，拥有开放兼容的社区神态、功能强大的开发环境以及持续高效的运行性能。而作为 `Linux` 的重要分支，`CentOS` 则凭借稳定的性能，占领了大部分的大型企业级别服务器操作市场。因此，目前几乎所有的企业均在服务器上部署了 `CentOS` 操作系统。我们公司目前所有的服务器均安装 `CentOS7`，这是目前最新的版本，有足够强大的社区技术支持，同时也适合采用更加强大的新技术。

<!--  -->

## 安装

<!--  -->

### 安装操作系统

可以使用 `utra-iso` 这个工具，先把操作系统的镜像文件写入 `U盘`。然后在服务器的启动项，选择从 `U盘` 启动，并按步骤进行相应的设置。这里有一点提示，最好是在安装系统的时候，能够把相应的分区设好，这样可以省去在后面重新设置，如果常用的目录
- `swap` 作为交换空间，防止内存不够时用来储存数据，可以安装内存的 `1:1` 的比例进行设置
- `/data/` 用于存放数据文件和数据库，磁盘空间要保证足够大
- `/home` 用于存放主目录，也可以相应的留够空间
- `/usr` 用于存放常用软件的头文件与动态链接库

### 安装常用软件包

我写了一个简单的状态提示，用于显示当前安装的软件信息:
```bash
#! /usr/bin/bash
## -------------------------------------------
msg() {
    info=""
    for x in $@;do
        info+="$x "
    done
    printf "\n\033[1;32m ==> $(date '+%H:%M:%S') ::=== ${info} ===:: \n\n\033[0m" 
}
## -------------------------------------------

```

#### 更换阿里源

```bash
## -------------------------------------------
## Ref: [超简单CentOS7 配置阿里云yum源](https://blog.csdn.net/ltx06/article/details/78030056)
msg "替换 CentOS7 阿里源"
## 1.定位目录
cd /etc/yum.repos.d/
## 2.下载阿里源
wget http://mirrors.aliyun.com/repo/Centos-7.repo
## 当前目录是/etc/yum.repos.d/，刚刚下载的Centos-7.repo也在这个目录上
ls -al
## 3.备份系统原来的repo文件
mv CentOS-Base.repo CentOS-Base.repo.bak
## 4.替换系统原理的repo文件
mv Centos-7.repo CentOS-Base.repo
## 5.执行yum源更新命令
yum clean all && yum makecache

## epel源
yum -y install epel-release
yum clean all && yum makecache

# 下载阿里开源镜像的epel源文件
wget -O /etc/yum.repos.d/epel-7.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all && yum makecache

yum update
## -------------------------------------------
```

#### 安装常用的开发工具

```bash

## -------------------------------------------
msg "安装常用软件包"
yum -y install gcc gcc-c++ kernel-devel git
yum -y install cmake bzip2 htop tldr pigz pbzip2
yum -y install bzip2-devel.x86_64
yum -y install libxslt-devel libffi-devel openssl-devel libcurl-devel
yum -y install python-devel python-pip
pip install tldr MySQL-python
sudo yum -y install R

# 安装 mysql
yum install -y mysql mariadb-server mariadb mysql-devel
## 启动 mariadb.service 引擎
## systemctl start|stop|restart mariadb.service
systemctl start mariadb.service

## 增加远程访问 MySQL 3306 端口
systemctl start firewalld.service
# 1.FirewallD防火墙开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
# 2.重启防火墙
systemctl restart firewalld.service
## -------------------------------------------
```



#### 安装 Teamviewer

```bash
## 解决编码问题
#vim /etc/environment
echo "
LC_ALL=zh_CN.UTF_8
LANG=zh_CN.UTF_8  
" >> /etc/environment

## -------------------------------------------
## Ref: [在CentOS 7.4上安装Teamviewer 14](https://blog.csdn.net/openeis/article/details/86286639)
msg "安装 Teamviewer"
## 1.下载 rpm 包： https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/
cd ~/Downloads
wget https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
## 2.安装 epel 的 rpm
rpm -Uvh epel-release-7-11.noarch.rpm
## 下载 Teamviewer.rpm
wget https://download.teamviewer.com/download/linux/teamviewer.x86_64.rpm
yum localinstall teamviewer.x86_64.rpm
## -------------------------------------------
```

#### 安装 Sublime Text 3

```bash
## -------------------------------------------
msg "安装 Sublime Text 3"
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
sudo yum-config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
sudo yum install sublime-text 
## -------------------------------------------
```

#### 安装 hdf5

```bash
## -------------------------------------------
msg "安装 hdf5"
## 下载地址: https://www.hdfgroup.org/ftp/HDF5/releases/
cd /tmp
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.5/src/hdf5-1.10.5.tar.gz
tar zxvf hdf5-1.10.5.tar.gz
cd hdf5-1.10.5
./configure --prefix=/usr
make -j && make install
make check-install
h5cc -showconfig |head
## -------------------------------------------
```



<!--  -->

## 配置

#### 增加用户/组

```bash
## -------------------------------------------
msg "CentOS 系统运维 ... 增加用户"
# 增加用户 trader
for u in trader fl lhg;do
    adduser $u
    passwd $u
done
## -------------------------------------------
```

#### 给指定的用户分配 sudo 权限

```bash
msg "CentOS 系统运维 ... 分配 sudo 权限"
sudo usermod -aG wheel trader
sed -i "/trader.*ALL=(ALL)/d" /etc/sudoers
sed -i "/root.*ALL=(ALL)/atrader  ALL=(ALL) NOPASSWD: ALL" /etc/sudoers
sed -i "/^%wheel.*ALL=(ALL).*ALL/d" /etc/sudoers
sed -i "/Allows people in group wheel to run all commands/a%wheel  ALL=(ALL) NOPASSWD: ALL" /etc/sudoers
```

#### 自动时间校准

```bash
msg "CentOS 系统运维 ... 时间校准"
yum install -y ntpdate
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
ntpdate us.pool.ntp.org
## 增加每日 03:00 开始执行自动时间校准
crontab -l | 
{ cat; echo "00 03 * * * /usr/sbin/ntpdate us.pool.ntp.org | logger -t NTP"; } | 
crontab -
crontab -l
```

#### 开放端口

使用 `firewall` 设置

```bash
# 1.FirewallD防火墙开放8787端口
firewall-cmd --zone=public --add-port=8787/tcp --permanent
# 2.重启防火墙
systemctl restart firewalld.service
```



#### 增加 MySQL 最大连接数

```bash
msg "CentOS 系统运维 ... MySQL 最大连接数"
## 1.可以通过配置/usr/lib/systemd/system/mariadb.service来调大打开文件数目。
sed -i "/LimitNOFILE/d" /usr/lib/systemd/system/mariadb.service
sed -i "/LimitNPROC/d" /usr/lib/systemd/system/mariadb.service 
sed -i "/\[Service\]/a## mysql\nLimitNOFILE=1024\nLimitNPROC=1024" /usr/lib/systemd/system/mariadb.service
## 2.修改 my.cnf 配置文件
sed -i "/max_connections/d" /etc/my.cnf
sed -i "/\[mysqld\]/amax_connections = 1024" /etc/my.cnf
## 

systemctl restart mariadb.service
```

#### 安装 Rstudio-server

```bash
msg "CentOS 系统运维 ... 安装 Rstudio-server"
mkdir -p ~/Downloads
cd ~/Downloads
wget https://download2.rstudio.org/server/centos6/x86_64/rstudio-server-rhel-1.2.1335-x86_64.rpm
sudo yum -y install rstudio-server-rhel-1.2.1335-x86_64.rpm
## 增加远程访问 Rstudio 8787 端口
# 1.FirewallD防火墙开放8787端口
firewall-cmd --zone=public --add-port=8787/tcp --permanent
# 2.重启防火墙
systemctl restart firewalld.service
## 1) check the process that used 8787
sudo fuser 8787/tcp
## 2) with the -k option to kill all process
sudo fuser -k 8787/tcp
## 3) start rstudio-server
sudo rstudio-server start
```

#### 安装 zsh-tmux-autojump

```bash
msg "CentOS 系统运维 ... 安装 zsh+tmux+autojump+powerline"
yum install -y zsh tmux
yum install -y --enablerepo=base,epel autojump-zsh

## 更改默认 shell
## ----------
chsh -s /bin/zsh trader

cd ~
echo `pwd`
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

pip install --user git+git://github.com/powerline/powerline

## -----------------------------------------------------------------------------
cd ~
mkdir -p Documents
cd Documents
git clone https://github.com/erikw/tmux-powerline.git
echo '
## =============================================================================
## https://github.com/erikw/tmux-powerline
set-option -g status on
set-option -g status-interval 2
set-option -g status-justify "centre"
set-option -g status-left-length 60
set-option -g status-right-length 150
set-option -g status-left "#(~/Documents/tmux-powerline/powerline.sh left)"
set-option -g status-right "#(~/Documents/tmux-powerline/powerline.sh right)"
set-window-option -g window-status-current-format "#[fg=colour235, bg=colour27]⮀#[fg=colour255, bg=colour27] #I ⮁ #W #[fg=colour27, bg=colour235]⮀"
## =============================================================================
' >> ~/.zshrc
## -----------------------------------------------------------------------------

mkdir -p ~/Documents
cd ~/Documents
git clone git://github.com/joelthelion/autojump.git
cd autojump
./install.py

# echo "[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && source ~/.autojump/etc/profile.d/autojump.sh" >> ~/.bashrc   

cd ~
mkdir -p .zsh
curl -L git.io/antigen > ~/.zsh/antigen.zsh

echo "
source ~/.zsh/antigen.zsh

# Bundles from the default repo (robbyrussell's oh-my-zsh).
antigen bundles <<EOBUNDLES
command-not-found
colored-man-pages
magic-enter
heroku
pip
lein
extract
tmux
ssh-agent
zsh-users/zsh-completions
zsh-users/zsh-autosuggestions
hlissner/zsh-autopair
zsh-users/zsh-syntax-highlighting
zsh-users/zsh-history-substring-search # load after zsh-syntax-highlighting
HeroCC/LS_COLORS
rupa/z
djui/alias-tips # Alias reminder when launching a command that is aliased
EOBUNDLES

# Tell Antigen that you're done.
antigen apply
" >> ~/.zshrc

source ~/.zshrc

## 处理路径显示
# ~/.oh-my-zsh/themes/agnoster.zsh-theme
# # Dir: current working directory
# ## 显示路径
# prompt_dir() {
#   ## 显示全部路径
#   #prompt_segment blue $CURRENT_FG '%~'
#   ## 只显示当前路径
#   prompt_segment blue $CURRENT_FG '%c'
# }
sed -i "s|.*prompt_segment blue \$CURRENT_FG '%~'|  prompt_segment blue \$CURRENT_FG '%c'|g" ~/.oh-my-zsh/themes/agnoster.zsh-theme
## hostname
# Context: user@hostname (who am I and where am I) 
# if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
## -------------------------------------------
```

## 自动化运维

