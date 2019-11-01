> **[info] 需要使用腾讯·企业邮箱**
> 绑定微信后，可直接在微信接收邮件。

## Python 版本
> **[warning] 可以使用 `cython` 进行封装成 `.so` 文件，防止泄漏账户密码**

> # -*- coding: utf-8 -*-
> # @Author: william
> # @Date:   2018-07-09 17:08:25
> # @Last Modified 2019-10-30
> # @Last Modified time: 2019-10-30 16:00:40
> ## =============================================================================
> ## 使用 Python 通过
> ## 腾讯企业邮箱提供的授权嘛进行
> ## 发送邮件操作
> ##
> ## 1. 需要申请 腾讯企业邮箱 的授权验证码
> ##    1).登录 腾讯企业邮箱
> ##    2).点击 设置
> ##    3).点击微信绑定
> ##    4).在 安全登录 的设置里面,开启 安全登录
> ##    5).选择查看 客户端专用密码,复制为以下内容的登录密码
> ## =============================================================================

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

### 编写类

主要有两大类:
- `Logger`：日志类
- `mailme`：邮件类

### `cython` 封装

## R 版本

