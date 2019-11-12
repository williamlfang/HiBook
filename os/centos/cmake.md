# 安装代码

```bash
wget https://cmake.org/files/v3.15/cmake-3.15.4.tar.gz
tar zxvf cmake-3.*
cd cmake-3.15.4
./bootstrap --prefix=/usr/local
make -j$(nproc) && make install

cmake --version
```bash
