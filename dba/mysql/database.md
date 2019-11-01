<!--  -->
# 通用数据库
这些数据库用于存放通用的数据信息，如
- `dev` 数据库存放了：
    - 期货交易日期
    - 股票交易日期

## dev

```mysql
###############################################################################
## 

CREATE DATABASE `dev` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

## =============================================================================
## ChinaFuturesCalendar
CREATE TABLE IF NOT EXISTS dev.ChinaFuturesCalendar(
    nights Date,		## 夜盘
    days Date NOT NULL	## 日盘
)
## =============================================================================
```




<!--  -->
# 中国期货数据库

## china_futures_bar

```mysql

```



## `china_futures_info`



<!--  -->

# 中国股票数据库
