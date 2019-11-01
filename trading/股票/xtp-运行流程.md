# 中泰证券·XTP 配置教程

## 运行流程

整个流程一共分3个：
- 交易前：检查系统启动与运行情况
- 交易中：盘中突然情况处理
- 交易后：回传交易记录及股票数据


## 开盘前 09:00 -> trader

- 分别登录 `sh` 、`sz` 两地的中泰服务器机房，检查 `xtaTrader` 是否开启

  ```bash
  ## 查看进程是否启动
  ps aux |grep xtaTrader
  
  ## 检查交易日志是否正常
  cd myLog
  tailx yyyymmdd_xtaTrader.txt
  ```

## 开盘交易 09:30 -> researcher

- 检查开仓是否正常

## ~~收盘 15:20 -> trader~~

> **[danger]目前已经实现了自动化同步交易信号与数据库，不需要再做如此处理了。**

- 检查交易记录，并将两地的交易记录分别导出到本地服务器

  - 中泰服务器的交易记录放在

    ```bash
    ~/myVnpy/vnpy-1.9.1/trading/account/recording/XTP/yyyymmdd
    ```
    
  - 本地服务器(199)交易记录放在

    ```bash
    
    ```
  ## 上海
    /shared/xtp/recording/sh/yyyymmdd

    ## 深圳
    /shared/xtp/recording/sz/yyyymmdd

    ```
  
    ```

- 通知潘村当天的交易记录与持仓记录已同步到本地

## ~~同步交易信号 17：30 -> trader, researcher~~

> **[danger]目前已经实现了自动化同步交易信号与数据库，不需要再做如此处理了。**


- 交易信号写入 `mysql` 数据库：`pretrading.signals_stocks`

- 将本地数据库的当日交易信号导出 csv , 同步上传到中泰上海和深圳机房服务器，文件名规范为 `signals_yyyymmdd.csv`，其中 `yyyymmdd` 为次日的交易日期。比如，在 `2019-07-22 17:30`，我们需要把当天的交易信号提出出来，得到一个 `signals_20190723.csv` 的文件，内容如下

  ```bash
  head signals_20190723.csv 
  TradingDay,AccountID,StrategyID,StockID,Timer,Offset,Volume,Ranking
  2019-07-22,lyb,Stg_S_D_5_testing,002124,am,buy,300,15
  2019-07-22,lyb,Stg_S_D_5_testing,002138,am,buy,300,7
  2019-07-22,lyb,Stg_S_D_5_testing,002157,am,buy,300,4
  ```
  
- 把以上的 csv 文件翻在 `~/myVnpy/signals` 目录下面

- 把 csv 信号插入 MySQL 数据库，执行

  ```bash
  cd ~/myVnpy/
  
  ./insert_signals
  ```

