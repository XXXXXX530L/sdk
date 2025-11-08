# 协议规则

- 传输方式：HTTP
- 数据格式：JSON
- 签名算法：MD5
- 字符编码：UTF-8

# 1.SDK下载地址

[SDK下载地址](https://github.com/XXXXXX530L/sdk/blob/main/bitpay-sdk-1.0-release.jar)



# 2.SDK依赖导入

- 把sdk放入到 app/libs 下，完整路径为 app/libs/bitpay-sdk-1.0-release.jar

- 在app/build.gradle下添加如下依赖
  
  ```gradle
  dependencies {
      implementation files('libsbitpay-sdk-1.0-release.jarr')
  }
  ```
  
  

# 3.SDK使用案例

```kt
val request = BitPayCreateOrderRequestDTO()
request.pid = 1001L
request.type = "alipay"
request.out_trade_no = "TEST202511070012"
request.notify_url = "http://127.0.0.1:8080/test/notify"
request.return_url = ""
request.name = "钱包充值"
request.money = "1.00"
try{
    val tradOrder: BitPayCreateOrderResultDTO = BitePayClient.getInstance().create(request)
    Log.i("MAIN", tradOrder.payUrl)
}catch (e: BitPayBizException){
    Log.e("MAIN", "${e.message}", e)
}
```

- 1.创建请求 `BitPayCreateOrderRequestDTO`

- 2.调用 `BitePayClient.getInstance().create(request)` 请求接口

- 3.捕获异常 `BitPayBizException`，`e.message`获取到具体异常情况

- 4.正常返回 `BitPayCreateOrderResultDTO`

###### 3.1 BitPayCreateOrderRequestDTO 内容说明

| 字段     | 变量名          | 必填  | 类型     | 示例值                               | 描述                  |
| ------ | ------------ | --- | ------ | --------------------------------- | ------------------- |
| 商户ID   | pid          |     | Long   | 1001                              |                     |
| 支付方式   | type         |     | String | alipay                            | 微信：wxpay，支付宝：alipay |
| 商户订单号  | out_trade_no |     | String | 20251108151343351                 |                     |
| 异步通知地址 | notify_url   |     | String | http://www.pay.com/notify_url.php | 服务器异步通知地址           |
| 跳转通知地址 | return_url   |     | String | http://www.pay.com/return_url.php | 页面跳转通知地址            |
| 商品名称   | name         |     | String | 钱包充值                              | 最大长度127             |
| 商品金额   | money        |     | String | 1.00                              | 单位：元，最多2位小数         |

###### 3.2 BitPayCreateOrderResultDTO 内容说明

| 字段      | 变量名          | 类型     | 示例值                                              | 描述                  |
| ------- | ------------ | ------ | ------------------------------------------------ | ------------------- |
| 商户ID    | pid          | Long   | 1001                                             |                     |
| 支付方式    | type         | String | alipay                                           | 微信：wxpay，支付宝：alipay |
| 商户订单号   | out_trade_no | String | 20251108151343351                                |                     |
| 商品金额    | money        | String | 1.00                                             | 单位：元，最多2位小数         |
| 支付跳转url | payUrl       | String | https://openapi.alipay.com/gateway.do?app_id=... | 可用于h5直接跳转           |



# 4、支付结果通知

**通知类型**：服务器异步通知（`notify_url`）
**请求方式**：GET

## 请求参数说明

| 字段名    | 变量名          | 必填  | 类型     | 示例值                              | 描述                   |
| ------ | ------------ | --- | ------ | -------------------------------- | -------------------- |
| 商户ID   | pid          | 是   | Int    | 1001                             |                      |
| 易支付订单号 | trade_no     | 是   | String | 20160806151343349021             | 易支付订单号               |
| 商户订单号  | out_trade_no | 是   | String | 20160806151343349                | 商户系统内部的订单号           |
| 支付方式   | type         | 是   | String | alipay                           | 微信：wxpay，支付宝：alipay  |
| 商品名称   | name         | 是   | String | VIP会员                            |                      |
| 商品金额   | money        | 是   | String | 1.00                             |                      |
| 支付状态   | trade_status | 是   | String | TRADE_SUCCESS                    | 只有 TRADE_SUCCESS 是成功 |
| 业务扩展参数 | param        | 否   | String |                                  |                      |
| 签名字符串  | sign         | 是   | String | 202cb962ac59075b964b07152d234b70 | 签名算法                 |
| 签名类型   | sign_type    | 是   | String | MD5                              | 默认为MD5               |

> 收到异步通知后，需要返回 `success` 以表示服务器接收到了订单通知。



# 5.【API】 查单地址

URL地址：https://gw.yzszmy.cn/api/open/api/query_trade?out_trade_no={商户订单号}

返回结果(json)：

| 字段名    | 变量名          | 类型     | 示例值                  | 描述                         |
| ------ | ------------ | ------ | -------------------- | -------------------------- |
| 返回状态码  | code         | Int    | 200                  | 200 为成功，其他值为失败             |
| 返回信息   | msg          | String | 查询订单号成功!             |                            |
| 易支付订单号 | trade_no     | String | 20250912192727000001 | 易支付订单号                     |
| 商户订单号  | out_trade_no | String | 20160806151343351    | 商户系统内部的订单号                 |
| 支付方式   | type         | String | wxpay                | 支付方式列表                     |
| 商户ID   | pid          | Int    | 1001                 | 发起支付的商户 ID                 |
| 创建订单时间 | createTime   | String | 2025-09-12 19:27:27  |                            |
| 完成交易时间 | payTime      | String | 2025-09-12 19:27:35  |                            |
| 商品名称   | name         | String | VIP会员                |                            |
| 商品金额   | money        | String | 1.00                 |                            |
| 支付状态   | trade_status | String | TRADE_SUCCESS        | TRADE_SUCCESS 为支付成功，其他为非成功 |
| 业务扩展参数 | param        | String |                      | 默认为空                       |



# 6.MD5签名算法

1、将发送或接收到的所有参数按照参数名ASCII码从小到大排序（a-z），sign、sign_type、和空值不参与签名！

2、将排序后的参数拼接成URL键值对的格式，例如 `a=b&c=d&e=f`，参数值不要进行url编码。

3、再将拼接好的字符串与商户密钥KEY进行MD5加密得出sign签名参数，`sign = md5 ( a=b&c=d&e=f + KEY )` （注意：+ 为各语言的拼接符，不是字符！），md5结果为小写。



# 7.支付方式列表

| 调用值    | 描述  |
| ------ | --- |
| wxpay  | 微信  |
| alipay | 支付宝 |
