> **[info] 期货Tick数据处理项目: 166/fl/myData/vnpyRcpp**
> 
> - 首先，使用程序获取每日的实盘 TickData，并存放到 `/data` 目录
> - 其次，在每日收盘后（15:30），定时执行处理脚本，根据一定的规则进行数据清洗
> - 由清洗完成的 Tick 数据汇总得到 `mbar`^[minute bar, 分钟数据]、`dbar`^[daily bar, 日行情数据]
> - 同时，脚本还会处理 InstrumentInfo（合约信息）、CommissionRate（手续费）
> - 程序会将行情数据存放到数据库
> - 最后，会有一个监控脚本检查当日数据的质量，如识别是否当日数据因网络中断产生断点、数据获取数量是否符合等。
> 
> @williamlfang


