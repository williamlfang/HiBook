# 源代码升级 Vim8.2

```bash
cd 
git clone https://github.com/vim/vim.git
cd vim

./configure --prefix=/usr --with-features=huge --enable-multibyte --enable-cscope --enable-pythoninterp
make -j && make install

vim -version
```

