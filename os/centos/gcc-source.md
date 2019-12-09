# 准备

把需要下载和解压的文件都放在一个目录

```bash
mkdir -p ~/gcc7
cd gcc7

## 下载 gcc7.3
wget https://mirrors.ustc.edu.cn/gnu/gcc/gcc-7.3.0/gcc-7.3.0.tar.gz

## 下载依赖包
wget ftp://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.1.0.tar.bz2
wget https://ftp.gnu.org/gnu/mpc/mpc-1.0.3.tar.gz
wget https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz
```

# 安装

```bash
tar -xvf gcc-7.3.0.tar.gz

## 依赖包都放进去
cp gmp-6.1.0.tar.bz2 ./gcc-7.3.0
cp mpc-1.0.3.tar.gz ./gcc-7.3.0
cp mpfr-3.1.4.tar.bz2 ./gcc-7.3.0

## 进入 gcc编译目录，把需要的依赖包解压到这一层次
tar jxvf gmp-6.1.0.tar.bz2
tar zxvf mpc-1.0.3.tar.gz
tar jxvf mpfr-3.1.4.tar.bz2

## 建立链接
ln -s gmp-6.1.0 gmp 
ln -s mpfr-3.1.4 mpfr 
ln -s mpc-1.0.3 mpc

## 开始编译，由于不是 root, 需要通过 --prefix 指定路径
## 如果拥有 root 权限
## ./configure --prefix=/usr/local/gcc  --enable-bootstrap  --enable-checking=release --enable-languages=c,c++ --disable-multilib
## 如果没有 root 权限，需要安装到用户目录
./configure --disable-multilib --prefix=/home/trader/opt

## 开始安装，不要用 -j，可能会导致错误
make
make install
```

# 添加环境变量

```bash
export PATH=/home/trader/opt/bin:$PATH
export LD_LIBRARY_PATH=/home/trader/opt/lib:/usr/lib:$LD_LIBRARY_PATH
```

如果使用 `root` 权限进行安装，也可以设置全局参数

```bash
# 环境变量path
echo  "export PATH=/usr/local/gcc/bin:$PATH" >> /etc/profile.d/gcc.sh
source /etc/profile.d/gcc.sh

# 头文件
ln -sv /usr/local/gcc/include/ /usr/include/gcc

# 库文件
echo "/usr/local/gcc/lib64" >> /etc/ld.so.conf.d/gcc.conf
ldconfig -v
ldconfig -p |grep gcc
```
