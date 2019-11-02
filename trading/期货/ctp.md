# CTP：期货交易的正规军

期货市场是目前唯一受到官方监管认可的程序化交易市场，从一开始由四大交易所独立提供交易接口，到后来由上期技术公司^[上期所的技术子公司]统一开发、并集成到一个包含了行情与交易两个通道的完整结构。从运行情况看，CTP 可以说是目前国内最先进、也是最为稳定的交易接口。因此，很多后来者如 XTP、Tora 也大都采用了类似的设计框架。

在后文中，我把行情类相关的简称为 `md`、交易类相关的简称为 `td`。

## Header File

交易所一共给出了4个头文件，

- `ThostFtdMdApi.h`是行情类头文件
- `ThostFtdTraderApi.h`是交易类头文件
- `ThostFtdUserApiDataType.h`是数据类型
- `ThostFtdUserApiStruct.h`是结构体

其中数据类型与结构为行情与交易通用。

## `.so` File

由于是在 `Linux` 操作系统环境下开发，我需要使用 `.so` 文件调用交易所提供的接口。这包含

- `libthostmduserapi_se.so`是行情调用接口
- `libthosttraderapi_se.so`是交易调用接口

以上两个动态链接库分别包含两大类：

- 主动发起调用类 `CThostFtdcMdApi`/`CThostFtdcTraderApi` 
- 回调响应类 `CThostFtdcMdSpi`/`CThostFtdcTraderSpi`

前者是需要我们继承类后进行主动发起，如连接前置、账户登录等；后者则是在完成连接状态后，由交易所发起、客户端进行响应。

