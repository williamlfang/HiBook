# CentOS7 升级 gcc

> **[info] 利用 yum 升级**
>
> 可以直接使用 `yum` 进行升级操作，需要相应的替换版本号即可。

## 安装

```bash
yum install centos-release-scl -y
yum install devtoolset-7 -y
scl enable devtoolset-7 bash 
gcc --version 
```

> **[warning] 注意**
>
> 在centos的devtoolset库中 最新的为 devtoolset-7，所以我们以后可以自己改数字安装最新的版本 
> 
> scl enable devtoolset-7 bash 
>
> 如果使用的是zsh则使用 
> scl enable devtoolset-7 zsh 

## 重新做软链接


```bash
ln -sf /opt/rh/devtoolset-7/root/usr/bin/gcc /usr/bin/gcc
ln -sf /opt/rh/devtoolset-7/root/usr/bin/g++ /usr/bin/g++

gcc -v

Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/opt/rh/devtoolset-7/root/usr/libexec/gcc/x86_64-redhat-linux/7/lto-wrapper
Target: x86_64-redhat-linux
Configured with: ../configure --enable-bootstrap --enable-languages=c,c++,fortran,lto --prefix=/opt/rh/devtoolset-7/root/usr --mandir=/opt/rh/devtoolset-7/root/usr/share/man --infodir=/opt/rh/devtoolset-7/root/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-shared --enable-threads=posix --enable-checking=release --enable-multilib --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --with-gcc-major-version-only --enable-plugin --with-linker-hash-style=gnu --enable-initfini-array --with-default-libstdcxx-abi=gcc4-compatible --with-isl=/builddir/build/BUILD/gcc-7.3.1-20180303/obj-x86_64-redhat-linux/isl-install --enable-libmpx --enable-gnu-indirect-function --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
Thread model: posix
gcc version 7.3.1 20180303 (Red Hat 7.3.1-5) (GCC)
```
