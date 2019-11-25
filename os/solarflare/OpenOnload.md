# 初探

##下载驱动

`OpenOnload` 原先是作为一个独立的开发项目存在，在[官网](https://www.openonload.org/download.html)可以获取相关的资讯，或者直接进入[下载网页](https://www.openonload.org/download/)获取指定版本的驱动。

```bash
https://www.openonload.org/download/

Index of /download/
../
ppc/                                               15-Feb-2016 11:19                   -
sfnettest/                                         29-Oct-2013 15:45                   -
sfptpd_reverse_ptp_preview/                        18-Aug-2017 10:59                   -
sysjitter/                                         30-May-2017 17:08                   -
efx_ioctl_timesync.h                               23-Jun-2016 16:50               10271
openonload-201502-ChangeLog.txt                    01-Mar-2015 11:34               65722
openonload-201502-README.txt                       01-Mar-2015 11:34                3011
openonload-201502-ReleaseNotes.txt                 01-Mar-2015 11:34               11998
openonload-201502-u1-ChangeLog.txt                 30-Mar-2015 16:35               67171
openonload-201502-u1-README.txt                    30-Mar-2015 16:35                3030
openonload-201502-u1-ReleaseNotes.txt              30-Mar-2015 16:35               14761
openonload-201502-u1.tgz                           30-Mar-2015 16:35             3061647
openonload-201502-u1.tgz.md5                       30-Mar-2015 16:35                  59
openonload-201502-u2-ChangeLog.txt                 04-Jun-2015 16:52               70484
openonload-201502-u2-README.txt                    04-Jun-2015 16:52                3161
openonload-201502-u2-ReleaseNotes.txt              04-Jun-2015 16:52               16556
openonload-201502-u2.tgz                           04-Jun-2015 16:52             3081875
openonload-201502-u2.tgz.md5                       04-Jun-2015 16:52                  59
openonload-201502-u3-ChangeLog.txt                 19-Aug-2015 15:54               70604
openonload-201502-u3-README.txt                    19-Aug-2015 15:54                3161
openonload-201502-u3-ReleaseNotes.txt              19-Aug-2015 15:54               16967
openonload-201502-u3.tgz                           19-Aug-2015 15:54             3276190
openonload-201502-u3.tgz.md5                       19-Aug-2015 15:54                  59
openonload-201502.tgz                              01-Mar-2015 11:34             3056445
openonload-201502.tgz.md5                          01-Mar-2015 11:34                  56
openonload-201509-ChangeLog.txt                    06-Oct-2015 16:26               79017
openonload-201509-README.txt                       06-Oct-2015 16:26                3145
openonload-201509-ReleaseNotes.txt                 06-Oct-2015 16:26               10035
openonload-201509-u1-ChangeLog.txt                 01-Feb-2016 15:51               84338
openonload-201509-u1-README.txt                    01-Feb-2016 15:51                3171
openonload-201509-u1-ReleaseNotes.txt              01-Feb-2016 15:51               12329
openonload-201509-u1.tgz                           01-Feb-2016 15:51             3367951
openonload-201509-u1.tgz.md5                       01-Feb-2016 15:51                  59
openonload-201509.tgz                              06-Oct-2015 16:26             3328928
openonload-201509.tgz.md5                          06-Oct-2015 16:26                  56
openonload-201606-ChangeLog.txt                    30-Jun-2016 17:14               90351
openonload-201606-README.txt                       30-Jun-2016 17:14                3162
openonload-201606-ReleaseNotes.txt                 30-Jun-2016 17:14                7501
openonload-201606-preview1.zip                     02-Jun-2016 19:06             4544551
openonload-201606-u1-ChangeLog.txt                 03-Nov-2016 17:33               94806
openonload-201606-u1-README.txt                    03-Nov-2016 17:33                3178
openonload-201606-u1-ReleaseNotes.txt              03-Nov-2016 17:33               13160
openonload-201606-u1.1-ChangeLog.txt               01-Feb-2017 15:01               95339
openonload-201606-u1.1-README.txt                  01-Feb-2017 15:01                3173
openonload-201606-u1.1-ReleaseNotes.txt            01-Feb-2017 15:01               13564
openonload-201606-u1.1.tgz                         01-Feb-2017 15:01             4416893
openonload-201606-u1.1.tgz.md5                     01-Feb-2017 15:01                  61
openonload-201606-u1.2-ChangeLog.txt               16-Mar-2017 16:34               96359
openonload-201606-u1.2-README.txt                  16-Mar-2017 16:34                3196
openonload-201606-u1.2-ReleaseNotes.txt            16-Mar-2017 16:34               14428
openonload-201606-u1.2.tgz                         16-Mar-2017 16:34             4451313
openonload-201606-u1.2.tgz.md5                     16-Mar-2017 16:34                  61
openonload-201606-u1.3-ChangeLog.txt               27-Jun-2017 10:12               97889
openonload-201606-u1.3-README.txt                  27-Jun-2017 10:12                3192
openonload-201606-u1.3-ReleaseNotes.txt            27-Jun-2017 10:12               16072
openonload-201606-u1.3.tgz                         27-Jun-2017 10:12             4916267
openonload-201606-u1.tgz                           03-Nov-2016 17:33             4605605
openonload-201606-u1.tgz.md5                       03-Nov-2016 17:33                  59
openonload-201606.tgz                              30-Jun-2016 17:14             4170978
openonload-201606.tgz.md5                          30-Jun-2016 17:14                  56
openonload-201710-ChangeLog.txt                    10-Oct-2017 15:32              108975
openonload-201710-README.txt                       10-Oct-2017 15:32                3194
openonload-201710-ReleaseNotes.txt                 10-Oct-2017 15:32                9522
openonload-201710-u1-ChangeLog.txt                 23-Jan-2018 14:58              109112
openonload-201710-u1-README.txt                    23-Jan-2018 14:58                3178
openonload-201710-u1-ReleaseNotes.txt              23-Jan-2018 14:58                9802
openonload-201710-u1.1-ChangeLog.txt               23-Apr-2018 16:08              109283
openonload-201710-u1.1-README.txt                  23-Apr-2018 16:08                3219
openonload-201710-u1.1-ReleaseNotes.txt            23-Apr-2018 16:08               10557
openonload-201710-u1.1.tgz                         23-Apr-2018 16:08             4879299
openonload-201710-u1.1.tgz.md5                     23-Apr-2018 16:08                  61
openonload-201710-u1.tgz                           23-Jan-2018 14:58             4890521
openonload-201710-u1.tgz.md5                       23-Jan-2018 14:58                  59
openonload-201710.tgz                              10-Oct-2017 15:32             4875622
openonload-201710.tgz.md5                          11-Oct-2017 07:50                  56
openonload-201805-ChangeLog.txt                    09-May-2018 08:38              118468
openonload-201805-README.txt                       09-May-2018 08:38                3282
openonload-201805-ReleaseNotes.txt                 09-May-2018 08:38               14680
openonload-201805-u1-ChangeLog.txt                 28-Aug-2018 09:51              118663
openonload-201805-u1-README.txt                    28-Aug-2018 09:51                3276
openonload-201805-u1-ReleaseNotes.txt              28-Aug-2018 09:51               15603
openonload-201805-u1.tgz                           28-Aug-2018 09:51             4659172
openonload-201805-u1.tgz.md5                       28-Aug-2018 09:51                  59
openonload-201805.tgz                              09-May-2018 08:38             4682883
openonload-201805.tgz.md5                          09-May-2018 08:38                  56
openonload-201811-ChangeLog.txt                    03-Dec-2018 23:39              129366
openonload-201811-README.txt                       03-Dec-2018 23:39                3384
openonload-201811-ReleaseNotes.txt                 03-Dec-2018 23:39                8249
openonload-201811-u1-ChangeLog.txt                 02-Jul-2019 10:53              133385
openonload-201811-u1-README.txt                    02-Jul-2019 10:53                3415
openonload-201811-u1-ReleaseNotes.txt              02-Jul-2019 10:53                9679
openonload-201811-u1.tgz                           02-Jul-2019 10:53             4978077
openonload-201811-u1.tgz.md5                       02-Jul-2019 10:53                  59
openonload-201811.tgz                              03-Dec-2018 23:39             4951381
openonload-201811.tgz.md5                          03-Dec-2018 23:39                  56
```

## demo

所有运行的demo都在我们事先下载得到的压缩包里面。一般来说

- files under the `gnu` directory are 32-bit (if these are built)
- files under the `gnu_x86_64` directory are 64-bit.

我们进入 `gnu_x86_64` 的文件夹即可。

```bash
## 进入相应的文件夹
cd openonload-201811/

## 源代码存放
cd scripts/
## 增加环境路径
export PATH="$PWD:$PATH"
## 使用 64 位进行编译
cd ../build/gnu_x86_64/tests/ef_vi/
## 先清理，然后安装
make clean && make

pwd
/root/openonload-201811/build/gnu_x86_64/tests/ef_vi

ls -alh

total 5304
-rw-r--r--. 1 root root      1 Nov 17 03:53 copy.depends
-rw-r--r--. 1 root root      0 Nov 17 03:53 copy.done
-rwxr-xr-x. 1 root root 343784 Nov 25 04:24 efforward
-rw-r--r--. 1 root root   2724 Nov 25 04:24 efforward.d
-rw-r--r--. 1 root root  79712 Nov 25 04:24 efforward.o
-rwxr-xr-x. 1 root root 387320 Nov 25 04:24 efforward_packed
-rw-r--r--. 1 root root   2745 Nov 25 04:24 efforward_packed.d
-rw-r--r--. 1 root root  86952 Nov 25 04:24 efforward_packed.o
-rwxr-xr-x. 1 root root 368680 Nov 25 04:24 efjumborx
-rw-r--r--. 1 root root   2670 Nov 25 04:24 efjumborx.d
-rw-r--r--. 1 root root  80384 Nov 25 04:24 efjumborx.o
-rwxr-xr-x. 1 root root 465904 Nov 25 04:24 eflatency
-rw-r--r--. 1 root root   5631 Nov 25 04:24 eflatency.d
-rw-r--r--. 1 root root 121064 Nov 25 04:24 eflatency.o
-rwxr-xr-x. 1 root root 326256 Nov 25 04:24 efrss
-rw-r--r--. 1 root root   2658 Nov 25 04:24 efrss.d
-rw-r--r--. 1 root root  67640 Nov 25 04:24 efrss.o
-rwxr-xr-x. 1 root root 421928 Nov 25 04:24 efsend
-rw-r--r--. 1 root root   5366 Nov 25 04:24 efsend_common.d
-rw-r--r--. 1 root root  33168 Nov 25 04:24 efsend_common.o
-rw-r--r--. 1 root root   5437 Nov 25 04:24 efsend.d
-rw-r--r--. 1 root root  51224 Nov 25 04:24 efsend.o
-rwxr-xr-x. 1 root root 427032 Nov 25 04:24 efsend_pio
-rw-r--r--. 1 root root   5446 Nov 25 04:24 efsend_pio.d
-rw-r--r--. 1 root root  45840 Nov 25 04:24 efsend_pio.o
-rwxr-xr-x. 1 root root 435640 Nov 25 04:24 efsend_pio_warm
-rw-r--r--. 1 root root   5563 Nov 25 04:24 efsend_pio_warm.d
-rw-r--r--. 1 root root  63160 Nov 25 04:24 efsend_pio_warm.o
-rwxr-xr-x. 1 root root 409200 Nov 25 04:24 efsend_timestamping
-rw-r--r--. 1 root root   5476 Nov 25 04:24 efsend_timestamping.d
-rw-r--r--. 1 root root  43816 Nov 25 04:24 efsend_timestamping.o
-rwxr-xr-x. 1 root root 410640 Nov 25 04:24 efsink
-rw-r--r--. 1 root root   2733 Nov 25 04:24 efsink.d
-rw-r--r--. 1 root root  97664 Nov 25 04:24 efsink.o
-rwxr-xr-x. 1 root root 369440 Nov 25 04:24 efsink_packed
-rw-r--r--. 1 root root   2736 Nov 25 04:24 efsink_packed.d
-rw-r--r--. 1 root root  65952 Nov 25 04:24 efsink_packed.o
-rw-r--r--. 1 root root    150 Nov 17 03:53 GNUmakefile
-rwxr-xr-x. 1 root root   5220 Nov 25 04:24 stats
-rw-r--r--. 1 root root   2623 Nov 25 04:24 utils.d
-rw-r--r--. 1 root root  79648 Nov 25 04:24 utils.o
```

## 主要应用

![](/home/william/Documents/hibook/os/solarflare/ef_apps.png)

