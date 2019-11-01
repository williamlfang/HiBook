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


## 配置 VPN

## 数据交互

