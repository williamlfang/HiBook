# CTP 行情类

## `md.h`

```c++
#ifndef __MD_H__
#define __MD_H__
#include "util.h"

#include "ThostFtdcMdApi.h"

class MdSpi : public CThostFtdcMdSpi
{
private:
    // 建立 api
    CThostFtdcMdApi *api;
    int reqid = 1;

    std::string accountid;
    YAML::Node config = getConfig();

    // userid
    std::string _userid = getKeyValue("userid");
    char* userid = const_cast<char*>(_userid.c_str());
    // brokerid
    std::string _brokerid = getKeyValue("brokerid");
    char* brokerid = const_cast<char*>(_brokerid.c_str());
    // password
    std::string _password = getKeyValue("password");
    char* password = const_cast<char*>(_password.c_str());
    // authcode
    std::string _authcode = getKeyValue("authcode");
    char* authcode = const_cast<char*>(_authcode.c_str());
    // appid
    std::string _appid = getKeyValue("appid");
    char* appid = const_cast<char*>(_appid.c_str());
    //
    std::string md_address = getKeyValue("md_address");
    std::string td_address = getKeyValue("td_address");

public:
    MdSpi(std::string s): accountid( s )
    {   
        std::cout << "==== CTP登录账户: " 
                  << this->accountid 
                  << " ===="
                  << std::endl;
    };

    virtual ~MdSpi()
    {
        // api->Join();
        api->RegisterSpi(NULL);
        api->Release();
        api = NULL;
        delete pool;
    };

    ThreadPool *pool = new ThreadPool( config["setting"]["no_threads"].as<int>() );
    //==== 初始化的设定 ==========================================================
    bool login_status = false;
    std::string tradingday;
    std::string mkdataPath = config["setting"]["mkdata"].as<std::string>() + "/" + this->accountid + "/";
    std::string logpath = config["setting"]["logpath"].as<std::string>() + "/" + this->accountid + "/";

    // 创建 API
    void create();
    // 注册前置
    void registerFront(std::string address);
    // 启动初始化
    void init();

    // 获取交易日期
    std::string getTradingDay()
    {   
        tradingday = api->GetTradingDay();
        return tradingday;
    }
    // 提取 Key-Value
    std::string getKeyValue( std::string key )
    {   
        std::string value = config[accountid][key].as<std::string>();
        return value;
    }
    // =========================================================================


    //==== Api 主动函数调用 ======================================================
    // 账户请求登录
    virtual void reqUserLogin();
    // 账户请求退出
    virtual void reqUserLogout();
    virtual int subscribe(std::string instrumentID);
    virtual int unSubscribe(std::string instrumentID);
    virtual int subscribeMarketData(char *ppInstrumentID[], int nCount);
    virtual int unSubscribeMarketData(char *ppInstrumentID[], int nCount);

    // 多线程处理数据
    // 生成 csv 的文件头
    void genHeader(std::string filePath);
    // 保存 csv 内容
    static void saveMarketData(CThostFtdcDepthMarketDataField *pDepthMarketData, std::string mkdataPath);
    // =========================================================================


    //==== Spi 回调函数继承 ======================================================
    ///当客户端与交易后台建立起通信连接时（还未登录前），该方法被调用。
    void OnFrontConnected();
 
    ///当客户端与交易后台通信连接断开时，该方法被调用。当发生这个情况后，API会自动重新连接，客户端可不做处理。
    ///@param nReason 错误原因
    ///        0x1001 网络读失败
    ///        0x1002 网络写失败
    ///        0x2001 接收心跳超时
    ///        0x2002 发送心跳失败
    ///        0x2003 收到错误报文
    void OnFrontDisconnected(int nReason);

    ///心跳超时警告。当长时间未收到报文时，该方法被调用。
    ///@param nTimeLapse 距离上次接收报文的时间
    void OnHeartBeatWarning(int nTimeLapse);

    ///登录请求响应
    virtual void OnRspUserLogin(CThostFtdcRspUserLoginField *pRspUserLogin, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast);

    ///登出请求响应
    virtual void OnRspUserLogout(CThostFtdcUserLogoutField *pUserLogout, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast);

    ///请求查询组播合约响应
    virtual void OnRspQryMulticastInstrument(CThostFtdcMulticastInstrumentField *pMulticastInstrument, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

    ///错误应答
    virtual void OnRspError(CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast);

    ///订阅行情应答
    // virtual void OnRspSubMarketData(CThostFtdcSpecificInstrumentField *pSpecificInstrument, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

    ///取消订阅行情应答
    // virtual void OnRspUnSubMarketData(CThostFtdcSpecificInstrumentField *pSpecificInstrument, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

    ///订阅询价应答
    // virtual void OnRspSubForQuoteRsp(CThostFtdcSpecificInstrumentField *pSpecificInstrument, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

    ///取消订阅询价应答
    // virtual void OnRspUnSubForQuoteRsp(CThostFtdcSpecificInstrumentField *pSpecificInstrument, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

    ///深度行情通知
    virtual void OnRtnDepthMarketData(CThostFtdcDepthMarketDataField *pDepthMarketData);

    ///询价通知
    // virtual void OnRtnForQuoteRsp(CThostFtdcForQuoteRspField *pForQuoteRsp) {};
    // =========================================================================
};

#endif //
```