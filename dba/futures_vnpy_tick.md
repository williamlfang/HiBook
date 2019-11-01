> **[info] 期货Tick数据处理项目: 166/fl/myData/vnpyRcpp/**
> 
> - 首先，使用程序获取每日的实盘 TickData，并存放到 `/data` 目录
> - 其次，在每日收盘后（15:30），定时执行处理脚本，根据一定的规则进行数据清洗
> - 由清洗完成的 Tick 数据汇总得到 `mbar`^[minute bar, 分钟数据]、`dbar`^[daily bar, 日行情数据]
> - 同时，脚本还会处理 InstrumentInfo（合约信息）、CommissionRate（手续费）
> - 程序会将行情数据存放到数据库
> - 最后，会有一个监控脚本检查当日数据的质量，如识别是否当日数据因网络中断产生断点、数据获取数量是否符合等。
> 
> @williamlfang

# TickData 数据

我们可以看一下具体的 TickData 长什么样子的

```bash
head -5 20191101.csv                                                                                         
TimeStamp,Date,Time,InstrumentID,ExchangeID,LastPrice,PreSettlementPrice,PreClosePrice,OpenPrice,HighestPrice,LowestPrice,ClosePrice,UpperLimitPrice,LowerLimitPrice,SettlementPrice,Volume,Turnover,PreOpenInterest,OpenInterest,PreDelta,CurrDelta,BidPrice1,BidPrice2,BidPrice3,BidPrice4,BidPrice5,AskPrice1,AskPrice2,AskPrice3,AskPrice4,AskPrice5,BidVolume1,BidVolume2,BidVolume3,BidVolume4,BidVolume5,AskVolume1,AskVolume2,AskVolume3,AskVolume4,AskVolume5,AveragePrice
20191031 20:35:21.705328,20191031,18:50:32.500,cu1912C51000,,11.0,4.0,11.0,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,2372.0,1.0,1.7976931348623157e+308,0,0.0,2428.0,2428.0,0.008649,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,0,0,0,0,0,0,0,0,0,0,0.0
20191031 20:35:21.723210,20191031,19:31:47.0,CF005P14000,,854.0,845.0,854.0,0.0,0.0,0.0,0.0,1390.0,300.0,1.7976931348623157e+308,0,0.0,198.0,198.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0,0,0,0,0,0,0,0,0,0,0.0
20191031 20:35:21.730680,20191031,18:50:32.500,cu2009C43000,,5266.0,5317.0,5266.0,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,7718.0,2915.0,1.7976931348623157e+308,0,0.0,20.0,20.0,0.855905,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,0,0,0,0,0,0,0,0,0,0,0.0
20191031 20:35:21.748171,20191031,18:50:32.500,ru2004P15750,,3738.0,3738.0,3738.0,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,4579.0,2896.0,1.7976931348623157e+308,0,0.0,0.0,0.0,-0.967829,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,1.7976931348623157e+308,0,0,0,0,0,0,0,0,0,0,0.0
```

# 项目结构

```bash
ll                                                                                      [14:38:34]
total 28K            
drwxr-xr-x.  2 fl fl 4.0K Sep 11 17:18 ./
drwxr-xr-x. 25 fl fl 4.0K Sep 18 17:13 ../
-rw-r--r--.  1 fl fl   62 Sep 13  2017 readme.md
-rw-r--r--.  1 fl fl  11K Sep 11 17:18 vnpyRcpp_00_main.R
-rw-r--r--.  1 fl fl 2.6K Sep  9 19:32 vnpyRcpp_05_info.R
```

## `vnpyRcpp_00_main.R`：主程序

在主程序设置相关的数据路径、交易日期等参数，然后取调用相应的执行函数。

### 设置数据所在路径

```r
## vnpyRcpp_00_main.R
## 这是主函数:
## 用于录入 vnpyData 的数据到 MySQL 数据库
##
##
## 注意:
##
## Author: fl@hicloud-investment.com
## CreateDate: 2017-07-12
## Revised: 2019-09-01
## 
# source( "/home/fl/myData/R/vnpyRcpp/vnpyRcpp_00_main.R" )
################################################################################

rm(list = ls())
ROOT_PATH <- '/home/fl/myData/R/vnpyRcpp'

################################################################################
## STEP 0: 初始化，载入包，设定初始条件
################################################################################
HISTORY_PATH <- "/home/fl/myData/R/ChinaFutures/HistoryData"
setwd( HISTORY_PATH )
source( sprintf( "%s/config/mySetting.R", HISTORY_PATH  ) )
Sys.setenv("PKG_CXXFLAGS"="-g -O3 -Wall -std=c++11")
sourceCpp( sprintf( "%s/config/myRcpp.cpp", HISTORY_PATH ), verbose = TRUE,
          cacheDir = "./tmp/Rcpp" )
## =============================================================================
```

### 定位数据

- 首先，会取识别当日的交易日期，如果不是交易日期，则程序自动退出；
- 其次，需要根据交易日期取匹配相应的 TickData 存放路径

```r

## =============================================================================
setwd( ROOT_PATH )

# dataPath <- "/data/ChinaFuturesTickData/FromAli/vn.data/TianMi3/nanhua/TickData"

dataPath <- "/data/ChinaFuturesTickData/From188/vn.data/TianMi3/nanhua/TickData"
# coloSource <- "TianMi3_From188"
## =============================================================================

## =============================================================================
tradingday <- format( Sys.Date(),"%Y%m%d" )
if (! tradingday %in% ChinaFuturesCalendar$days ) {
    stop( "NOT TradingDay !!!" )
}
tarball <- list.files(dataPath, pattern = '\\.tar\\.bz2', full.name = TRUE) %>% 
    grep( tradingday, .,  value = TRUE )
csvfile <- list.files(dataPath, pattern = '\\.csv$', full.name = TRUE) %>% 
    grep( tradingday, .,  value = TRUE )
if ( length( tarball ) != 1 & length( csvfile ) != 1 ) {
    stop( "NO tarball !!!" )
}

if ( length( csvfile ) == 1 ) {
    f <- csvfile
} else if ( length( tarball ) == 1 ) {
    f <- tarball
}
## =============================================================================
```

### 读取文件

```r
## =============================================================================
## 1.Reading tick
system.time( {
    tick <- read_from_vnpy( tradingday, f )
} )
## =============================================================================
```

其中，`read_from_vnpy` 要求输入交易日期、以及对应的 TickData 文件^[可以是 tarball, 或者 csv，函数会自动识别]。具体的函数代码可以在 `166/home/fl/myData/R/ChinaFutures/HistoryData/config/mySetting.R` 找到

```r
## =============================================================================
## FromVnpy
read_from_vnpy <- function( tradingday, datafile ) {
    if ( grepl( '\\.csv$',datafile ) ) {
        csvfile <- datafile
    } else {
        cmd <- sprintf( "tar jxvf %s --overwrite -C /tmp/ramdisk", datafile)
        system( cmd, ignore.stdout = TRUE )
        csvfile <- sprintf( "/tmp/ramdisk/%s.csv", tradingday)
    }

    if ( tradingday <= '20190101' ) {
        res <- fread( 
                csvfile,
                colClass = c( volume = 'numeric',
                              turnover = 'numeric'
                            )
            ) %>% 
            .[, .( 
                TimeStamp = timeStamp,
                TradingDay = tradingday,
                UpdateTime = time,
                InstrumentID = symbol,
                OpenPrice = openPrice, 
                HighestPrice = highestPrice, 
                LowestPrice = lowestPrice, 
                ClosePrice = closePrice,
                LastPrice = lastPrice,
                AskPrice1 = askPrice1,
                AskVolume1 = askVolume1,
                BidPrice1 = bidPrice1,
                BidVolume1 = bidVolume1,
                UpperLimitPrice = upperLimit, 
                LowerLimitPrice = lowerLimit,
                Volume = volume,
                Turnover = turnover,
                OpenInterest = openInterest,
                AveragePrice = averagePrice
            )]
    } else {
        res <- fread( 
                     csvfile,
                     colClass = c( Volume = 'numeric',
                                   Turnover = 'numeric'
                                 ) 
                    ) %>% 
            .[, .( 
                TimeStamp,
                TradingDay = tradingday,
                UpdateTime = Time,
                InstrumentID,
                OpenPrice, 
                HighestPrice, 
                LowestPrice, 
                ClosePrice,
                LastPrice,
                AskPrice1,
                AskVolume1,
                BidPrice1,
                BidVolume1,
                UpperLimitPrice, 
                LowerLimitPrice,
                Volume,
                Turnover,
                OpenInterest,
                AveragePrice
            )]
    }
    
    if ( !grepl( '\\.csv$',datafile ) ) {
        file.remove( csvfile )
    }

    return( res )
}
```
