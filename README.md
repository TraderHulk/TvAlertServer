### 						TradingView警报机器人自动交易脚本工具

### 0、介绍

​	本工具是接收TradingView策略信号的工具，目前在okx交易所可以做到自动交易，免费开源给粉丝朋友们，脚本中也附带一些pine 语言（TV编程语言）写的策略（电报群获取）可以仅供学习参考，不作为投资建议！！！欢迎start。

​	使用工具需要2个条件：第一个是要有TV会员账号；第二个是要有非大陆的服务器或者电脑；

​	

​	

### 1、安装部署工具步骤

​	以linux服务器为例

​		1)、购买境外服务器（至少2核1g，系统centos7以上即可，一年100多块钱）

​		2)、下载安装anaconda（不会登陆linux系统服务器的可以百度一下，或者进群免费咨询😄）

```python
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.05-Linux-x86_64.sh
```

​		安装anaconda参考链接：https://blog.csdn.net/Lin1169404361/article/details/123288482

​		3)、安装工具。

```python
cd TvAlertServer #进项目文件夹
pip install -r requirement.txt #安装依赖包
```

### 2、交易所设置

​	1）okx交易所交易设置：买卖模式

​	2）以“张”为单位下单，没有小数点的事情。

​	3）杠杆需要在okx app上设置好即可。

### 3、启动服务并测试

​	1）启动服务步骤：

```python
source source_run.sh start  #启动服务
source source_run.sh status  #查看启动状态，出现pid进程编号基本上启动成功啦
source source_run.sh stop  #停止服务
```

​	2）测试

​	测试有2个方式：

- 模拟tv给你的服务发警报信息进行下单

  工具test文件模块里有testTvAlterServer.py 可以直接本地电脑测试，原理是本地向服务器发请求，记得开梯子。测试之前需要把你替换一下py文件里的服务器ip地址即可。80端口是默认的。tv貌似只支持80端口。配置好后，运行它即可,然后查看是否下单。能正常下单就是成功啦。

  python testTvAlterServer.py

  comment的取值范围：

  ```python
  #做多 的comment 取值范围
  longComment = ["buy","long","entry_long","entry_buy","B","b","BUY","LONG"]
  #做空 的comment 取值范围
  shortComment = ["sell","short","entry_short","entry_sell","S","s","SELL","SHORT"]
  #平多 的comment 取值范围
  tpLongComment =  ["tp_buy","tp_long","TP_BUY","TP_LONG","TP-BUY","TP-LONG","close_buy","close_long","CLOSE_BUY","exit_buy","exit_long","EXIT_BUY"]
  #平空 的comment 取值范围
  tpShortComment = ["tp_sell","tp_short","TP_SELL","TP_SHORT","TP-SELL","TP-SHORT","close_sell","close_short","CLOSE_SELL","exit_sell","exit_short","EXIT_SELL"]
  #如果分批平仓（目前支持指定分批数量平仓）
  #后缀要有_batch，比如你现在是多仓10张，我想分批平仓先平5张，那么，comment = "tp_long_batch",以此类推。
  
  ```

  

- 配置tv警报信息进行下单

  至于怎么在tv上设置警报，可以看我的YouTube视频。目前消息模版是：以SUSHI-USDT-SWAP为例：

  如何生成TV警报消息，请移步白嫖客户端：

  https://github.com/TraderHulk/TvalertMessageGernerator
  
  ```json
  {
    "exchange":"okx",
    "symbol":"SUSHI-USDT-SWAP",
    "comment":"{{strategy.order.comment}}",
    "price":"{{strategy.order.price}}",
    "qty":"10", 
    "batch_qty":"5", 
    "api_key":"5c9e6f71d16c1d52e91", 
    "secretKey":"EF9BD66ED9F209B61D",
    "passphrase":"Hu18@",
    "diaccess_token":"",
    "keyword":""  
}
  ```

  注意：⚠️

  ​	1)、 comment ：这个是策略信号函数后面带的commet，因此如果你的策略里没有comment，需要你在后面添加上，不影响策略本身。举个例子：
  
  ```python
  if BT_Final_longCondition and Act_BT and testPeriod
      strategy.entry('long', strategy.long,  comment='entry_long')
  
  if BT_Final_shortCondition and Act_BT and testPeriod
      strategy.entry('short', strategy.short,  comment='entry_short')
  
  pips_corection = 1 / syminfo.mintick
  
  strategy.exit('Tsl', 'long', trail_points=math.abs(last_open_longCondition * (1 + tsi / 100) - last_open_longCondition) * pips_corection, trail_offset=high * (ts / 100) * pips_corection, loss=Act_sl ? math.abs(last_open_longCondition * (1 - sl / 100) - last_open_longCondition) * pips_corection : na, comment='tp_long')
strategy.exit('Tss', 'short', trail_points=math.abs(last_open_shortCondition * (1 - tsi / 100) - last_open_shortCondition) * pips_corection, trail_offset=low * (ts / 100) * pips_corection, loss=Act_sl ? math.abs(last_open_shortCondition * (1 + sl / 100) - last_open_shortCondition) * pips_corection : na, comment='tp_short')
  ```

  2）、qty  ：下单量，以“张”为单位，不能是小数、也不能低于最小最大下单量。

  ​	 batch_qty ：分批止盈止损下单量 ，以张数为单位。

  3)、交易所的api 信息 是通过webhook 发送到服务工具的，必须设置！！但不一定只设置在tv消息里，也可以直接设置在tv_alert_server_app.py 里的InputDataItem class  属性里 写死它，然后重启服务。最后模版里是空字符串即可。具体的可以进免费的量化机器人交流群咨询学习。
  
  

### 4、免责声明

​		本工具不保证策略收益，一切盈亏损失自行承担！禁止商用，仅供学习使用！如果违反，出了一切法律后果自负。

### 5、学习与交流

​	Telegram本人：https://t.me/hullk123

​	Telegram群：https://t.me/+bRIWTkW0RjAzYjc9

​	YouTube：https://www.youtube.com/watch?v=Sk1p_h_HKZA&t=3s

​	需要注册ok新用户的,请走这个链接，返佣30%，不需要维护小号：

​	  https://www.cnouyi.expert/join/28662096

​	注册后联系我，给我你的uid，和返佣接收usdt 地址！

​	我们的口号是：免费开源、一切皆免费！！！	



