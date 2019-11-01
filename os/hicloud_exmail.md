> **[info] 需要使用腾讯·企业邮箱**
> 绑定微信后，可直接在微信接收邮件。

# 汉云·邮件系统

## Python 版本
> **[warning] 可以使用 `cython` 进行封装成 `.so` 文件，防止泄漏账户密码**

```python
# -*- coding: utf-8 -*-
# @Author: william
# @Date:   2018-07-09 17:08:25
# @Last Modified 2019-10-30
# @Last Modified time: 2019-10-30 16:00:40
## =============================================================================
## 使用 Python 通过
## 腾讯企业邮箱提供的授权码进行
## 发送邮件操作
##
## 1. 需要申请 腾讯企业邮箱 的授权验证码
##    1).登录 腾讯企业邮箱
##    2).点击 设置
##    3).点击微信绑定
##    4).在 安全登录 的设置里面,开启 安全登录
##    5).选择查看 客户端专用密码,复制为以下内容的登录密码
## =============================================================================
```

### 加载模块

```python
## =============================================================================
from datetime import datetime
import time
import os, sys
import logging, traceback
from logging import handlers

from email.header import Header
from email.mime.text import MIMEText
from email.utils import formataddr
from email.mime.multipart import MIMEMultipart
from email.mime.application import MIMEApplication

import smtplib
## =============================================================================
```

### 代码

主要有两大功能:
- `Logger`：日志类
- `mailme`：发送邮件命令

#### 日志
```python
class Logger(object):
    #日志级别关系映射
    level_relations = {
        'debug':logging.DEBUG,
        'info':logging.INFO,
        'warning':logging.WARNING,
        'error':logging.ERROR,
        'crit':logging.CRITICAL
    }

    def __init__(self, filename, level = 'debug', backCount = 0,
                 fmt = '%(asctime)s - %(pathname)s[line:%(lineno)d] - %(levelname)s: %(message)s'):
        self.logger = logging.getLogger(filename)
        format_str = logging.Formatter(fmt)#设置日志格式
        self.logger.setLevel(self.level_relations.get(level))#设置日志级别
        sh = logging.StreamHandler()#往屏幕上输出
        sh.setFormatter(format_str) #设置屏幕上显示的格式
        th = handlers.TimedRotatingFileHandler(filename=filename,backupCount=backCount,encoding='utf-8')#往文件里写入#指定间隔时间自动生成文件的处理器
        #实例化TimedRotatingFileHandler
        #interval是时间间隔，backupCount是备份文件的个数，如果超过这个个数，就会自动删除，when是间隔的时间单位，单位有以下几种：
        # S 秒
        # M 分
        # H 小时、
        # D 天、
        # W 每星期（interval==0时代表星期一）
        # midnight 每天凌晨
        th.setFormatter(format_str)#设置文件里写入的格式
        self.logger.addHandler(sh) #把对象加到logger里
        self.logger.addHandler(th)
## =============================================================================
```

#### 邮件命令

```python
def mailme(receiver, subject,
           msg = None, 
           file = None,
           attach = None,
           logpath = None,
           sender = None,
           password = None,
           showname = "汉云投资"):
    """
        使用腾讯企业邮箱发送信息
        @receiver   : 收件人, 以 list 形式传递进来
        @subject    : 邮件主题
        @msg        : 邮件内容
        @file       : 读取文件作为内容
        @attach     : 发送的附件
        @logpath    : 写入的 log 日子路径
        @sender     : [default]发件人, 
        @password   : [default]腾讯企业邮箱暗码
    """
    ## ---------------------------------------------  
    if not sender:
        sender = 'fl@hicloud-investment.com'
        password = '**********************'
    logFile = logpath if logpath else '/tmp'
    log = Logger(filename = '{}/{}.hicloud.log'.format(logFile, datetime.now().strftime( '%Y-%m-%d %H:%M' )),
                 level = 'debug').logger

    if isinstance(receiver, str):
        receiver = [receiver]
    ## ---------------------------------------------

    ## ---------------------------------------------
    smtpServer = 'smtp.exmail.qq.com' # 腾讯服务器地址
    log.info(u"准备发送邮件: \"{}\"...\n\t\t\t>>发送<< {}\n\t\t\t>>内容<< {}".format(
            subject, u'\n\t\t\t>>发送<< '.join(receiver), file))
    ## ---------------------------------------------

    ## ---------------------------------------------
    message = MIMEMultipart()
    if msg:
        msg = MIMEText(msg, 'plain', 'utf-8')
        message.attach(msg)
    if file:
        with open(file, 'r') as f:
            # puretext = MIMEText(f.read().decode('string-escape').decode("utf-8"),
            #                     'plain', 'utf-8')
            puretext = MIMEText(f.read(),
                                'plain', 'utf-8')
        message.attach(puretext)
    if attach:
        if isinstance(attach, str):
            attach = [attach]
        for tmp in attach:
            ## =========================================================
            attach_part = MIMEApplication(open(tmp, 'rb').read())
            attach_part.add_header('Content-Disposition', 'attachment',
                                        filename=tmp.split('/')[-1])
            message.attach(attach_part)
            ## =========================================================
    ## 如果是 tmp 临时
    if subject in ['subject', 'tmp']:
        with open('/tmp/subject.txt', 'r') as f:
            # subject = f.read().decode('string-escape').decode("utf-8")
            subject = f.read()
    ## ---------------------------------------------

    ## =============================================================================
    # 发件人收件人信息格式化 ，可防空
    # 固定用法不必纠结，我使用lambda表达式进行简单封装方便调用
    lam_format_addr = lambda name, addr: formataddr((Header(name, 'utf-8').encode(), addr))

    ## 显示:发件人
    message['From'] = lam_format_addr(showname, sender) # 腾讯邮箱可略
    ## 显示:收件人
    message['To'] =  Header(showname, 'utf-8')
    ## 主题
    # 腾讯邮箱略过会导致邮件被屏蔽
    message['Subject'] = Header(subject, 'utf-8').encode()

    tryNo = 0
    while tryNo <= 10:
        tryNo += 1
        ## =============================================================================
        try:
            # 服务端配置，账密登陆
            # 阿里云不支持
            # server = smtplib.SMTP(smtpServer, 25)

            ## ----------------------------------------------------
            # 腾讯邮箱支持SSL(不强制)， 不支持TLS。
            server = smtplib.SMTP_SSL(smtpServer, 465) # 按需开启
            # 调试模式，打印日志
            # server.set_debuglevel(1) # 按需开启
            ## ----------------------------------------------------

            # 登陆服务器
            server.login(sender, password)
            # 发送邮件及退出
            server.sendmail(sender, receiver, 
                            message.as_string()) #发送地址需与登陆的邮箱一致
            server.quit()
            log.info(u"\"{}\": 邮件发送成功...\n".format(subject))
            break
        except:
            print(traceback.format_exc())
            log.error(u"\"{}\": 邮件发送失败...\n".format(subject))
        ## =============================================================================
```


测试

```python
def test():
    mailme(receiver = ['fl@hicloud-investment.com']
           ,subject = 'hicloud: 测试'
           ,msg = '这是一个很无聊的测试邮件'
           ,file = '/home/william/Desktop/tmp.txt'
           ,attach = ['/home/william/Desktop/tmp.txt',
                   '/home/william/Desktop/tmp.csv']
           ,logpath = '/tmp/',
           showname = '汉云测试员'
           )
```

### `cython` 封装

```python
# -*- coding: utf-8 -*-
# @Author: williamlfang
# @Date:   2018-05-17 11:59:55
# @Last Modified 2019-10-30
# @Last Modified time: 2019-10-30 16:02:31

# python setup.py build_ext --inplace

from distutils.core import setup
from distutils.extension import Extension
from Cython.Build import cythonize

ext_modules=[
    Extension(name = "hicloud_exmail", sources = ["./hicloud_exmail.pyx"])
]

setup(
    name = "hicloudEmail",
    ext_modules = cythonize(ext_modules)
)
```


## R 版本

这里主要使用了一个软件包 `datawookie/emayili` 进行封装 `R6` 类。

```r
library(emayili)
library(magrittr)

mail <- function( from = "fl@hicloud-investment.com",
                  to = c("fl@hicloud-investment.com"),
                  subject = "",
                  body = "",
                  attachment = "",
                  host = "smtp.exmail.qq.com",
                  port = 25,  ## 465
                  username = "fl@hicloud-investment.com",
                  password = "**********************"
                ) {
    ## ---------------------------------------------------
    # First create a message object.
    email <- envelope()
    # Add addresses for the sender and recipient.
    email <- email %>%
      from(from) %>%
      to(to)
    # Add a subject.
    email <- email %>% subject(subject)
    # Add a body.
    email <- email %>% body(body)
    # Add an attachment.
    if ( nchar( attachment ) != 0 ) {
        email <- email %>% attachment(attachment)    
    }
    # Create a SMTP server object and send the message.
    smtp <- server(host, port, username, password)
    ## ---------------------------------------------------

    smtp(email, verbose = TRUE)
}
```
