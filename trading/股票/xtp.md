# 中泰证券·XTP 配置教程

## 申请服务器

### 流程

-   签署《XTP 交易系统申请通用材料》
    -   XTP 交易系统需求选择
        -   [x] XTP.极速通：用于 Windows 下的可视化交易终端
        -   [x] XTP.Monitor：需要配合 Qt 开发
        -   [x] vn.py(目前只支持 Level1 行情)
    -   程序名称填写: HiQuant
    -   设备部署
        -   [x] 使用方式：VPN + 堡垒机
        -   [x] 机房选择：**需要特别强调上海机房与南方机房两地登录功能**

### 账户信息


## 配置服务器

### 申请使用　root 权限

-   通过发送邮件给 [@中泰证券 徐士杰：xusj01@zts.com.cn]，申请安装以下软件包

    ```bash
    #!/bin/bash
    # @Author: william
    # @Date:   2019-05-22 21:24:50
    # @Last Modified by:   “williamlfang”
    # @Last Modified time: 2019-06-14 10:40:17
    
    yum -y install vim gcc gcc-c++ kernel-devel git cmake bzip2 htop
    yum -y install python-devel libxslt-devel libffi-devel openssl-devel python-pip
    yum -y install bzip2-devel.x86_64
    
    # 安装 mysql
    yum install mysql
    yum install mariadb-server mariadb
    yum install mysql-devel
    
    ## 启动 mariadb.service 引擎
    systemctl start mariadb.service
    
    ## =============
    ## install talib
    ## =============
    cd /home/admin/xtp-pkgs
    ls |grep ta-lib
    tar -xf ta-lib-0.4.0-src.tar.gz
    cd ta-lib
    ./configure --prefix=/usr
    make -j
    sudo make install
    sudo echo "/usr/local/lib" >> /etc/ld.so.conf
    sudo ldconfig
    ```

-   如果忘記添加，可以自己在 admin 下面操作

    ```bash
    ## 如果忘記添加，可以自己在 admin 下面操作
    ## 查找 libta_lib.so.0
    find / -name libta_lib.so.0
    ## 打開 .bashrc
    vim ~/.bashrc
    export LD_LIBRARY_PATH=/home/admin/opt/lib:$LD_LIBRARY_PATH
    ```

### 普通用户安装程序

```bash
## -----------------------------------------------------------------------------
mkdir -p ~/Boost
cd ~/Boost
wget https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.tar.bz2
tar jxvf boost_1_66_0.tar.bz2
cp -r boost_1_66_0 boost_1_66_0_python3
# rm -rf boost_1_66_0
cd boost_1_66_0_python3
./bootstrap.sh --prefix=~/Boost/boost_1_66_0_python3 --with-python=/usr/bin/python3.6


tar jxvf boost_1_55_0.tar.bz2
cd boost_1_55_0
./bootstrap.sh --prefix=~/Boost/boost_1_55_0 --with-python=/usr/bin/python2.7

./b2 && ./b2 install
## -----------------------------------------------------------------------------

## -----------------------------------------------------------------------------
## zsh
## Build and install ncurses
wget ftp://ftp.gnu.org/gnu/ncurses/ncurses-6.1.tar.gz
tar xf ncurses-6.1.tar.gz
cd ncurses-6.1
./configure --prefix=/home/admin/opt CXXFLAGS="-fPIC" CFLAGS="-fPIC"
make -j && make install

wget -O zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download
# tar xf zsh.tar.xz
# cd zsh
tar zxvf zsh-5.7.1.tar.gz
cd zsh-5.7.1
./configure --prefix="/home/admin/opt" \
    CPPFLAGS="-I/home/admin/opt/include" \
    LDFLAGS="-L/home/admin/opt/lib"
make -j && make install

## >> .bashrc
export PATH=/home/admin/opt/bin:$PATH
export SHELL=`which zsh`
[ -f "$SHELL" ] && exec "$SHELL" -l

## 安装 git
mkdir -p ~/lib/perl5
cd ExtUtils-MakeMaker-7.36
perl Makefile.PL PREFIX=~/lib/perl5
make
make test
make install
echo "export PERL5LIB=~/lib/perl5:/home/admin/lib/perl5/share/perl:/home/admin/lib/perl5/share/perl5" >> ~/.zshrc

cd git-2.8.0
make configure
./configure --prefix=/home/admin/opt
make -j
make install

## 安装 tmux
wget https://github.com/libevent/libevent/releases/download/release-2.1.8-stable/libevent-2.1.8-stable.tar.gz
tar zxvf libevent-2.1.8-stable.tar.gz
cd libevent-2.1.8-stable
./configure --prefix=/home/admin/opt  --disable-shared
make -j & make install

cd ..
tar -xf tmux-2.8.tar.gz
cd tmux-2.8
CFLAGS="-I/home/admin/opt/include -I/home/admin/opt/include/ncurses" LDFLAGS="-L/home/admin/opt/lib -L/home/admin/opt/include/ncurses -Wl,-rpath=/home/admin/opt/lib" ./configure --prefix=/home/admin/opt
make -j && make install
## -----------------------------------------------------------------------------
```


## 配置 VPN

## 数据交互

