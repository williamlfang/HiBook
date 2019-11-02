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

## `md.cpp`

```c++
/*
* @Author: william
* @Date:   2019-10-20 11:39:00
* @Last Modified 2019-11-01
* @Last Modified time: 2019-11-01 23:27:54
*/
#include "md.h"
#include "util.h"

using namespace std;

// ==== 基本的 inline 函数 =======================================================
inline std::string getTimestampString(void)
{
    using namespace std::chrono;

    // get current time
    auto now = system_clock::now();

    // get number of milliseconds for the current second / 1000
    // get number of microseconds for the current second / 1000000
    // (remainder after division into seconds)
    // auto ms = duration_cast<milliseconds>(now.time_since_epoch()) % 1000;
    auto ms = duration_cast<microseconds>(now.time_since_epoch()) % 1000000;

    // convert to std::time_t in order to convert to std::tm (broken time)
    auto timer = system_clock::to_time_t(now);

    // convert to broken time
    std::tm bt = *std::localtime(&timer);

    std::ostringstream oss;

    oss << std::put_time(&bt, "%Y-%m-%d %H:%M:%S"); // HH:MM:SS
    // oss << '.' << std::setfill('0') << std::setw(6) << ms.count();
    oss << '.' << std::setw(6) << std::setfill('0') << ms.count();

    return oss.str();
}

inline double cleanData( double data )
{
    return (data > 1e100 || data < 1e-100) ? 0:data;
}
// =========================================================================


//==== Api 主动函数调用 ======================================================
// 创建 API
void MdSpi::create() {
    mkdirRecursive( (char*)this->logpath.c_str() );
    api = CThostFtdcMdApi::CreateFtdcMdApi((char*)this->logpath.c_str());
    api->RegisterSpi(this);
    std::cout << "MdApi::Version:\t" << api->GetApiVersion() << '\n' << std::endl;
    api->RegisterFront( (char*)(this->md_address).c_str() );
}

// 注册前置
void MdSpi::registerFront(std::string address) {
    api->RegisterFront( (char*)address.c_str() );
}

// 启动初始化
void MdSpi::init() {
    api->Init();
}

// 账户请求登录
void MdSpi::reqUserLogin()
{

    CThostFtdcReqUserLoginField loginReq;
    memset(&loginReq, 0, sizeof(loginReq));

    strcpy(loginReq.UserID, this->userid);
    strcpy(loginReq.BrokerID, this->brokerid);
    strcpy(loginReq.Password, this->password);

    int rt = api->ReqUserLogin(&loginReq, ++reqid);
    if (!rt) {
        std::cout << ">>>>>>发送登录请求成功" << std::endl;
    }
    else {
        std::cerr << "--->>>发送登录请求失败" << std::endl;
    }
}

// 账户请求退出
void MdSpi::reqUserLogout()
{
    CThostFtdcUserLogoutField logoutReq = CThostFtdcUserLogoutField();
    memset(&logoutReq, 0, sizeof(logoutReq));
    strcpy(logoutReq.BrokerID, this->brokerid);
    strcpy(logoutReq.UserID, this->userid);
    int rt = api->ReqUserLogout(&logoutReq, ++reqid);
    if (!rt) {
        std::cout << ">>>>>>发送退出请求成功" << std::endl;
    }
    else {
        std::cerr << "--->>>发送退出请求失败" << std::endl;
    }
}

inline int MdSpi::subscribe(std::string instrumentID)
{   
    char* buffer = (char*) instrumentID.c_str();
    char* myreq[1] = { buffer };
    return api->SubscribeMarketData(myreq, 1);
}

inline int MdSpi::unSubscribe(std::string instrumentID)
{   
    char* buffer = (char*) instrumentID.c_str();
    char* myreq[1] = { buffer };
    return api->SubscribeMarketData(myreq, 1);
}

inline int MdSpi::subscribeMarketData(char *ppInstrumentID[], int nCount)
{
    return api->SubscribeMarketData( ppInstrumentID, nCount );
}

inline int MdSpi::unSubscribeMarketData(char *ppInstrumentID[], int nCount)
{
    return api->UnSubscribeMarketData( ppInstrumentID, nCount );
}
// =============================================================================

// 生成 csv 的文件头
void MdSpi::genHeader(std::string filePath)
{
    if ( !fileExists( filePath ) ) {
        std::ofstream outFile;
        outFile.open(filePath, std::ios::out);
        outFile << "Timestamp,TradingDay,UpdateTime,ExchangeID,InstrumentID,"
                << "LastPrice,PreSettlementPrice,PreClosePrice," 
                << "OpenPrice,HighestPrice,LowestPrice,ClosePrice,"
                << "UpperLimitPrice,LowerLimitPrice,SettlementPrice,"
                << "Volume,Turnover,"
                << "PreOpenInterest,OpenInterest,PreDelta,CurrDelta,"
                << "AskPrice1,AskVolume1,BidPrice1,BidVolume1,"
                << "AveragePrice"
                << std::endl;
        outFile.close();
    }
}

// 保存 csv 内容
void MdSpi::saveMarketData(CThostFtdcDepthMarketDataField *pDepthMarketData, std::string mkdataPath)
{
    std::cout << "==== MdSpi::saveMarketData" << std::endl;
    std::string timestamp = getTimestampString();
    std::cout 
        << "Timestamp:\t"      << timestamp << '\n'
        << "TradingDay:\t"     << pDepthMarketData->TradingDay     << '\n'
        << "UpdateTime:\t"     << pDepthMarketData->UpdateTime << "." << pDepthMarketData->UpdateMillisec << '\n'
        << "ExchangeID:\t"     << pDepthMarketData->ExchangeID     << '\n'
        << "ExchangeInstID:\t" << pDepthMarketData->ExchangeInstID << '\n'
        << "InstrumentID:\t"   << pDepthMarketData->InstrumentID   << '\n'
        << "LastPrice:\t"      << cleanData( pDepthMarketData->LastPrice )     << '\n'
        << "PreSettlementPrice:\t"      << cleanData( pDepthMarketData->PreSettlementPrice )     << '\n'
        << "PreClosePrice:\t"      << cleanData( pDepthMarketData->PreClosePrice )     << '\n'
        << "OpenPrice:\t"      << cleanData( pDepthMarketData->OpenPrice )     << '\n'
        << "HighestPrice:\t"   << cleanData( pDepthMarketData->HighestPrice )  << '\n'
        << "LowestPrice:\t"    << cleanData( pDepthMarketData->LowestPrice )   << '\n'
        << "ClosePrice:\t"     << cleanData( pDepthMarketData->ClosePrice )    << '\n'
        << "UpperLimitPrice:\t" << cleanData( pDepthMarketData->UpperLimitPrice ) << '\n'
        << "LowerLimitPrice:\t" << cleanData( pDepthMarketData->LowerLimitPrice ) << '\n'
        << "SettlementPrice:\t" << cleanData( pDepthMarketData->SettlementPrice ) << '\n'
        << "Volume:\t\t"       << cleanData( pDepthMarketData->Volume ) << '\n'
        << "Turnover:\t"       << cleanData( pDepthMarketData->Turnover ) << '\n'
        << "OpenInterest:\t"   << cleanData( pDepthMarketData->OpenInterest ) << '\n'
        << "AskPrice1:\t"      << cleanData( pDepthMarketData->AskPrice1 ) << '\n'
        << "AskVolume1:\t"     << cleanData( pDepthMarketData->AskVolume1 ) << '\n'
        << "BidPrice1:\t"      << cleanData( pDepthMarketData->BidPrice1 ) << '\n'
        << "BidVolume1:\t"     << cleanData( pDepthMarketData->BidVolume1 ) << '\n'
        ;

        // std::cout << mkdataPath << std::endl;
        std::string filePath = mkdataPath + pDepthMarketData->InstrumentID + ".csv";
        // std::cout << filePath << std::endl;
        std::ofstream outFile;
        // if ( !fileExists( filePath) ) {
        //     outFile.open(filePath, std::ios::out);
        //     outFile << "Timestamp,TradingDay,UpdateTime,ExchangeID,InstrumentID,LastPrice," 
        //             << "OpenPrice,HighestPrice,LowestPrice,ClosePrice,Volume,Turnover,"
        //             << "OpenInterest,UpperLimitPrice,LowerLimitPrice,"
        //             << "AskPrice1,AskVolume1,BidPrice1,BidVolume1"
        //             << std::endl;
        // } else {
        //     outFile.open(filePath, std::ios::app); // 文件追加写入 
        // }
        outFile.open(filePath, std::ios::app); // 文件追加写入 
        outFile 
            << timestamp << ','
            << pDepthMarketData->TradingDay << ','
            << pDepthMarketData->UpdateTime << '.' << pDepthMarketData->UpdateMillisec << ',' 
            << pDepthMarketData->ExchangeID     << ','
            << pDepthMarketData->InstrumentID   << ','
            << cleanData( pDepthMarketData->LastPrice )     << ','
            << cleanData( pDepthMarketData->PreSettlementPrice )     << ','
            << cleanData( pDepthMarketData->PreClosePrice )     << ','
            << cleanData( pDepthMarketData->OpenPrice )     << ','
            << cleanData( pDepthMarketData->HighestPrice )  << ','
            << cleanData( pDepthMarketData->LowestPrice )   << ','
            << cleanData( pDepthMarketData->ClosePrice )    << ','
            << cleanData( pDepthMarketData->UpperLimitPrice ) << ','
            << cleanData( pDepthMarketData->LowerLimitPrice ) << ','
            << cleanData( pDepthMarketData->SettlementPrice ) << ','
            << cleanData( pDepthMarketData->Volume ) << ','
            << cleanData( pDepthMarketData->Turnover ) << ','
            << cleanData( pDepthMarketData->PreOpenInterest ) << ','
            << cleanData( pDepthMarketData->OpenInterest ) << ','
            << cleanData( pDepthMarketData->PreDelta ) << ','
            << cleanData( pDepthMarketData->CurrDelta ) << ','
            << cleanData( pDepthMarketData->AskPrice1 ) << ','
            << cleanData( pDepthMarketData->AskVolume1 ) << ','
            << cleanData( pDepthMarketData->BidPrice1 ) << ','
            << cleanData( pDepthMarketData->BidVolume1 ) << ','
            << cleanData( pDepthMarketData->AveragePrice ) << std::endl;

        outFile.close();
}

//==== Spi 回调函数继承 ==========================================================
// 连接成功应答
void MdSpi::OnFrontConnected()
{
    std::cout << "=====建立网络连接成功=====" << std::endl;
    reqUserLogin();
}

// 连接成功应答
void MdSpi::OnFrontDisconnected(int nReason)
{
    std::cout << "=====建立网络连接失败=====" << std::endl;
}

void MdSpi::OnHeartBeatWarning(int nTimeLapse)
{
    std::cerr << "=====网络心跳超时=====" << std::endl;
    std::cerr << "距上次连接时间： " << nTimeLapse << std::endl;
}

void MdSpi::OnRspUserLogin(CThostFtdcRspUserLoginField *pRspUserLogin, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast)
{
    bool bResult = pRspInfo && (pRspInfo->ErrorID != 0);
    if ( !bResult ) {
        std::cout << "=====账户登录成功=====" << std::endl;
        std::cout << "交易日： " << pRspUserLogin->TradingDay << std::endl;
        std::cout << "登录时间： " << pRspUserLogin->LoginTime << std::endl;
        std::cout << "经纪商： " << pRspUserLogin->BrokerID << std::endl;
        std::cout << "帐户名： " << pRspUserLogin->UserID << std::endl;
        std::cout << "MaxOrderRef： " << pRspUserLogin->MaxOrderRef << std::endl;

        login_status = true;
        tradingday = api->GetTradingDay();
        mkdataPath += tradingday + "/";
        mkdirRecursive( const_cast<char*>(mkdataPath.c_str()) );
    } else {
        std::cerr << "返回错误--->>> ErrorID=" << pRspInfo->ErrorID 
                  << ", ErrorMsg=" << pRspInfo->ErrorMsg << std::endl;
    }
}

void MdSpi::OnRspUserLogout(CThostFtdcUserLogoutField *pUserLogout, CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast)
{
    bool bResult = pRspInfo && (pRspInfo->ErrorID != 0);
    if ( !bResult ) {
        std::cout << "=====账户登出成功=====" << std::endl;
        std::cout << "经纪商： " << pUserLogout->BrokerID << std::endl;
        std::cout << "帐户名： " << pUserLogout->UserID << std::endl;
    } else {
        std::cerr << "返回错误--->>> ErrorID=" << pRspInfo->ErrorID << ", ErrorMsg=" << pRspInfo->ErrorMsg << std::endl;
    }
}

void MdSpi::OnRspError(CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast)
{
    if (pRspInfo && pRspInfo->ErrorID != 0)
    {
        std::cout << "ErrorID: " << pRspInfo->ErrorID << "\t"
                  << "ErrorMsg: " << gb2312_to_utf8(pRspInfo->ErrorMsg) 
                  << std::endl;
    }
}

///深度行情通知
void MdSpi::OnRtnDepthMarketData(CThostFtdcDepthMarketDataField *pDepthMarketData)
{
    std::cout << "\n==== MdSpi::OnRtnDepthMarketData=====" << std::endl;
    if (pDepthMarketData)
    {   
        pool->enqueue(saveMarketData, pDepthMarketData, mkdataPath);
    }
};
// =============================================================================
```
