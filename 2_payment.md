# 2.Payment(/trade/v1)
What is QFPay_Oversea API?

Merchant applies Wechat and Alipay from QFPay.

QFPay person helps to do the backend configuration.

So Merchant can just change the payment type to do various type of payment.

For Alipay,one merchant linkes to one or two PID(Partner Identification ID).One PID for online,one PID for offline.

For Wechat,one merchant linkes to one sub_mchid(Sub Merchant ID).One sub_mchid can be both Online or Offline.Mchid is vendor mchid(QFPay),appid is vendor(QFPay) appid.

![image](https://github.com/linan0828/QFPAY_Oversea/blob/master/Contents/Pic/HowQFPayWorks.jpeg)

Level1：
It is visible to merchant.

Level2:
It is not visible to merchant,QFPay encapsulates the logic behind it.
So one pair of parameter can use be both used in Alipay and Wechat.
Only Chengdu System has mchid, Beijing System not needed.
 
 This payment API can not be used in a mobile phone app development.（No SDK provided）
 
 One appcode can only correspond to one notify and one return URL, even through one merchant have multiple payment types.(For example ,one merchant has both Alipay and Wechat payment type,merchant can not set two notify and two return URL separately.)
Wechat:The notify URL is set by QFPay person, return URL is set by the developer.
Alipay:The return and notify URL are set by QFPay person.
 
 Normal development process：
 
 1.Apply Wechat and Alipay account from Qfpay.
 
2.Qfpay provides appcode,key,mchid(optional) to merchant.

3.Merchant use Qfpay parameters to do the development.

4.Based on merchant application to Qfpay, merchant can use Wechat or Alipay payment based on pay_type.
If the pay_type is not available, it will show filter_error(获取用户支付通道失败)

5.Give Qfpay callback and return URL for settting.

6.If merchant uses Wechat official account payment, fill in
[公众号信息_钱方](https://github.com/QFPay/QFPAY_Oversea/blob/master/Contents/Wechat/OA/%E5%85%AC%E4%BC%97%E5%8F%B7%E4%BF%A1%E6%81%AF_%E9%92%B1%E6%96%B9.txt) and
[公众号支付绑定申请函](https://github.com/QFPay/QFPAY_Oversea/blob/master/Contents/Wechat/OA/%E5%85%AC%E4%BC%97%E5%8F%B7%E6%94%AF%E4%BB%98%E7%BB%91%E5%AE%9A%E7%94%B3%E8%AF%B7%E5%87%BD.doc)

Also watch 
[Wechat offical account payment English](https://github.com/QFPay/QFPAY_Oversea/blob/master/Contents/Wechat/OA/Wechat%20offical%20account%20payment%20English_171101.docx)
for more details.

Notes:

 
 [Example code](https://github.com/linan0828/QFPAY_Oversea/tree/master/Code):It has test parameters and code.
 
 
 
+ 1. /payment---------------Open a payment

| Field Name    | Description   | Required  | Notes 	 | Example  |
| ------------- |:-------------:| ---------:| ----------:| --------:|
|mchid  |QFPay mchid|Y|Do not confuse with Wechat mchid, they are totally different!Beijing System not needed.|BvDtmKJA5mx7GpN0 |
| pay_type |Payment type|Y| | 800208|
| out_trade_no |Reference number for merchant|Y| |1470020842103 |
| txdtm |Payment time| Y | |2016-08-01 11:07:22|
| txamt |Payment amount|Y|Unit is the minimum unit of the currency.If 1 HKD, txamt is 100 | 10|
| auth_code |Authentication code.| |Must pass in with offline(800108,800208).The length is usually 18 bits long.The length may change in the future. | |
| sub_openid |Wechat sub_openid| | Must pass in with Wechat OA account payment(800207)| |
| product_name|product name/good name| | | |
|valid_time|Order valid time||At least 300s(5mins),only for online payment scene.The number should be in 300s interval.Like 300s,600s,900s,etc|300|

Pay_type has the following parameters:

Note：800151,800152,800201,800207 are for online scene.

800101,800108,800208,800228 are for offline scene.


If customer payment currency is HKD, it means the customer must links with a HK ID and a HKD bank account in Hong Kong.

If customer payment currency is RMB, it means the customer must links with a China mainland ID and a RMB bank account in mainland China.

If customer payment currency is HKD+RMB, it means the customer can be both China mainland resident and Hong kong resident.





|Pay_type | Type of payment   |Comments |
| ------------- |:-------------:|:-------------:| 
|800151  |Alipay Website Payment|In a PC-based website|
|800152  |Alipay WAP Payment|In a mobile-based browser website|
|800108  |Alipay Barcode Payment|For bar code scanning gun in a store|
|800101  |Alipay Transaction QR Code Payment|Customer scan the dynamic QR code generated by Merchant.|
|800201  |Wechat Website Payment|In a PC-based website|
|800208  |Wechat  Barcode Payment|For bar code scanning gun in a store|
|800228  |Wechat HK  Barcode Payment|For bar code scanning gun in a store|
|800207  |Wechat H5|In Wechat browser|

Payment Example Response：

800151(RMB):After redirecet to pay_url,customer scan the QR code to finish the payment. 
```javascript
{"sysdtm": "2018-01-16 15:05:35", "resperr": "", "respmsg": "OK", "out_trade_no": "6167667", "syssn": "201801160902990017027307", "respcd": "0000", "pay_url": "https://intlmapi.alipay.com/gateway.do?out_trade_no=201801160902990017027307&service=create_forex_trade&sign_type=MD5&_input_charset=utf-8&sign=6922ae7ef17cb139ad32bb91492a0b2d&currency=HKD&notify_url=https%3A%2F%2Foverseaopenapi.qfpay.com%2Fnotify_receiver%2Fali%2F800151&total_fee=0.02&order_valid_time=600&partner=2088021017666931&order_gmt_create=2018-01-16+15%3A05%3A35&return_url=https%3A%2F%2Fosqt.qfpay.com%2Ftrade%2Fv1%2Falipay_online_return&subject=%E9%8A%85%E9%91%BC%E7%81%A3TEST%EF%BC%9A6167667"}
```  
800152(RMB):After redirecet to pay_url,customer does the payment in Alipay APP.
```javascript
{"sysdtm": "2018-01-16 15:09:06", "resperr": "", "respmsg": "OK", "out_trade_no": "10822320", "syssn": "201801160901990017027485", "respcd": "0000", "pay_url": "https://intlmapi.alipay.com/gateway.do?app_pay=Y&out_trade_no=201801160901990017027485&service=create_forex_trade_wap&_input_charset=utf-8&sign=20930d8320ab42e7cbddd2a167551ade&currency=HKD&timeout_rule=10m&notify_url=https%3A%2F%2Foverseaopenapi.qfpay.com%2Fnotify_receiver%2Fali%2F800152&total_fee=0.02&sign_type=MD5&partner=2088611221573217&return_url=https%3A%2F%2Fosqt.qfpay.com%2Ftrade%2Fv1%2Falipay_online_return_wap&subject=%E9%8A%85%E9%91%BC%E7%81%A3TEST%EF%BC%9A10822320"}
```  
800108(RMB+HKD):After the Merchant uses bar code scanning gun to get auth_code, and does the payment request.
```javascript
{"pay_type": "800108", "cardcd": "2088202283656563", "txdtm": "2018-01-16 15:12:20", "resperr": "", "txamt": "2", "respmsg": "ok", "out_trade_no": "16888127", "syssn": "201801160901990017027630", "txcurrcd": "HKD", "respcd": "0000", "sysdtm": "2018-01-16 15:12:22"}
``` 
800101（RMB+HKD）:Merchant needs to convert qrcode string response to a QR code picture.Customer scans the QR code to do the payment. Note: This is only applicable for offline scene.If use in online, Alipay has the right to close the account.
```javascript
{"sysdtm": "2018-01-16 15:42:18", "respmsg": "ok", "qrcode": "https://qr.alipay.com/bax0817145eenvpxpgvp807b", "pay_type": "800101", "resperr": "", "txdtm": "2018-01-16 15:42:16", "txamt": "2", "out_trade_no": "16577171", "syssn": "201801160901990017028777", "txcurrcd": "HKD", "respcd": "0000"}
```
800201(RMB):It can be used in a online shop, also in the offline scene.Merchant needs to convert qrcode string response to a QR code picture.Customer scans the QR code to do the payment. 
```javascript
{"pay_type": "800201", "out_trade_no": "83273364", "txdtm": "2018-01-16 15:18:20", "resperr": "", "txamt": "2", "respmsg": "ok", "sysdtm": "2018-01-16 15:18:22", "syssn": "201801160902990017027862", "txcurrcd": "HKD", "qrcode": "weixin://wxpay/bizpayurl?pr=LLddgP0", "respcd": "0000"}
```  
800208(RMB):After the Merchant uses bar code scanning gun to get auth_code, and does the payment request.When returns 1145, it means the customer needs to input the password.It does not mean the payment is failure.Merchant needs to do the query to confirm the payment status.
```javascript
{"pay_type": "800208", "out_trade_no": "12227571", "cardcd": "", "txdtm": "2018-01-16 15:48:08", "resperr": "\u4ea4\u6613\u5931\u8d25\uff0c\u8bf7\u8054\u7cfb\u5ba2\u670d(1145)", "txamt": "2", "respmsg": "\u9700\u8981\u7528\u6237\u8f93\u5165\u652f\u4ed8\u5bc6\u7801", "sysdtm": "2018-01-16 15:48:10", "syssn": "201801160901990017029000", "txcurrcd": "HKD", "respcd": "1145", "code_url": ""}
``` 
800228(HKD)
800207(RMB)


+ 2.  /close--------------close the order(Only applicable for Wechat Online payment，Alipay not applicable.)

Note:The default close time for Alipay online payment is 10 mins,users can set valid_time(close time) for alipay.

| Field Name    | Description   | Required  |  Example  |
| ------------- |:-------------:| ---------:|  --------:|
|mchid  |The code return by signup interface.Beijing System not needed.|Y|BvDtmKJA5mx7GpN0 |
|syssn|transaction serial number|out_trade_no or syssn must be passed in|201607280901020011216135|
|out_trade_no|external order number||1470020842103|
| txdtm |transaction time|Y | |
| txamt |transaction amount|Y  | 10|

+ 3.  /refund ------------refund the money
 
| Field Name     | Required  | Description|  
| ------------- |:-------------:| ---------:|
|syssn  |Y|original transaction number|
|out_trade_no  |Y|refund number,not the original transaction number.|
| txamt |Y|amount|
| txdtm |Y|request side time|
|mchid  |Y||


+ 4. /reversal-----------Only applicable for offline situation.

When in offline situation, there are network problems, use this interface.

| Field Name    |Required  | Description    | 
| ------------- |:-------------:| ---------:|
| mchid |Y| |
|syssn|Y|transaction serial number|
|out_trade_no|Y|external order number|
|txdtm|Y|transaction time|
|txamt|Y|transaction amount|

+ 5. /query------------query an order

|Field Name|Required|Description|Example
| ------------- |:-------------:| ---------:|---------:|
| mchid |Y| |BvDtmKJA5mx7GpN0
| syssn ||Transaction serial number,it supports batch query,use "," to delimit mutliple syssn |201607280901020011216135,201607280901020011216136
| out_trade_no ||out_trade_number,it supports batch query,use "," to delimit mutliple out_trade_number|1470020842103,1470020842104 
| pay_type || Payment type,it supports batch query,use "," to delimit mutliple pay_type|800201,800208
| respcd || |0000
| start_time | |Start time YYYY-MM-DD HH:mm:ss ,begin from this month.|2016-08-01 11:00:00
| end_time ||End time YYYY-MM-DD HH:mm:ss,end with this month |2016-08-21 11:00:00
| page||Page default 1 |1
|page_size||Default page size：10|20

Note:
If use start_time and end_time for query, syssn and out_trade_no can be ignored.




+ 6. /Pre order notification

For pre order ,when server get payment result, the result will be sent through pre-configured callback(notify) address（Note:The callback address is set by QFPay technical support）,and set signature in HTTP Header X-QF-SIGN,including the following data:

| Field Name    | Description   | Required  | Notes 	 | Example  |
| ------------- |:-------------:| ---------:| ----------:| --------:|
|Mchid  |The code return by signup interface.Beijing System not needed.| |If not fill in |BvDtmKJA5mx7GpN0 |
|syssn  |original transaction number|Y|
| pay_type |Payment type|Y | | 800208|
| out_trade_no |Reference number| Y| |1470020842103 |
| txdtm |Payment time| | | |
| txamt |Payment amount,unit is the minimum unit of the currency.| | | 10|
| udid |Device unique id| | | |
|respcd|Respond code|||0000|
|respmesg|Respond message||||
Note:The MD5 value is based on the return parameters of notify message.

+  7.Online Alipay payment redirect parameters

For Alipay online payment, after the transaction, it will redirect to the return_url set by QFPay(Note:Merchant needs to tell QFPay technical person with return_url information),with the GET method and the following parameters:

| Field Name    | Description   | Required  | Notes 	 | Example  |
| ------------- |:-------------:| ---------:| ----------:| --------:|
|Mchid  |The code return by signup interface.Beijing System not needed.| |If not fill in |BvDtmKJA5mx7GpN0 |
|syssn  |original transaction number|Y|
| pay_type |Payment type|Y | | 800208|
| out_trade_no |Reference number| Y| |1470020842103 |
| txdtm |Payment time| | | |
| txamt |Payment amount,unit is the minimum unit of the currency.| | | 10|
| udid |Device unique id| | | |
|respcd|Respond code|||0000|
|respmesg|Respond message||||


Example Response：
Query Payment: 
```javascript
{"respmsg": "", "resperr": "", "respcd": "0000", "data": [{"pay_type": "800208", "sysdtm": "2017-09-06 14:56:43", "order_type": "payment", "txcurrcd": "", "txdtm": "2017-09-06 14:56:43", "txamt": "10", "out_trade_no": "57003579", "syssn": "201709060902990042326492", "cancel": "0", "respcd": "0000", "errmsg": "\u4ea4\u6613\u6210\u529f"}], "page": 1, "page_size": 10}
```  
Do Reversal:
```javascript
{"resperr": "", "sysdtm": "2017-09-06 15:00:35", "orig_syssn": "201709060902990042326492", "respmsg": "ok", "out_trade_no": "57003579", "syssn": "201709060902990042326548", "respcd": "0000"}
``` 
Query Reversal syssn number:   
```javascript
{"respmsg": "", "resperr": "", "respcd": "0000", "data": [{"pay_type": "800208", "sysdtm": "2017-09-06 14:56:43", "order_type": "payment", "txcurrcd": "", "txdtm": "2017-09-06 14:56:43", "txamt": "10", "out_trade_no": "57003579", "syssn": "201709060902990042326492", "cancel": "1", "respcd": "0000", "errmsg": "\u4ea4\u6613\u6210\u529f"}], "page": 1, "page_size": 10}
``` 
Do Refund: 
```javascript
{"resperr": "", "txdtm": "2017-09-06 15:09:08", "txamt": "10", "respmsg": "ok", "out_trade_no": "98654216", "syssn": "201709060901990042326683", "respcd": "0000", "sysdtm": "2017-09-06 15:09:09", "orig_syssn": "201709060902990042326663"}
``` 
Query Original Refund syssn number:   
```javascript
{"respmsg": "", "resperr": "", "respcd": "0000", "data": [{"pay_type": "800208", "sysdtm": "2017-09-06 15:07:47", "order_type": "payment", "txcurrcd": "", "txdtm": "2017-09-06 15:07:46", "txamt": "10", "out_trade_no": "98654216", "syssn": "201709060902990042326663", "cancel": "2", "respcd": "0000", "errmsg": "\u4ea4\u6613\u6210\u529f"}], "page": 1, "page_size": 10}
```
Query New Refund syssn number:   
```javascript
{"respmsg": "", "resperr": "", "respcd": "0000", "data": [{"pay_type": "800208", "sysdtm": "2017-09-06 15:09:09", "order_type": "refund", "txcurrcd": "", "txdtm": "2017-09-06 15:09:08", "txamt": "10", "out_trade_no": "98654216", "syssn": "", "cancel": "0", "respcd": "0000", "errmsg": "\u4ea4\u6613\u6210\u529f"}], "page": 1, "page_size": 10}
```  
800201 success response:
```javascript
{"pay_type": "800201", "out_trade_no": "622536XX", "txdtm": "2017-11-20 XX:28:02", "resperr": "", "txamt": "10", "respmsg": "ok", "sysdtm": "2017-11-20 XX:28:02", "syssn": "20171120090299001477XXXX", "txcurrcd": "HKD", "qrcode": "weixin://wxpay/bizpayurl?pr=gFkBXXX", "respcd": "0000"}
```
Change qrcode response from string to a QR code. 

