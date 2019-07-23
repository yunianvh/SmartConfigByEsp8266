# SmartConfigByEsp8266|SmartConfig一键配网在硬件端的具体实现|ESP8266一键配网在Arduino的具体实现|玉念聿辉
@[TOC](目录)
### 前言
<hr/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;继上次写智能配网的文章已经过去差不多三个月，中间忙一下其他项目也就没有继续跟进了，不过好在这过程中顺便熟悉了一下硬件开发，今天重拾起来可能会给文章增添更多的营养价值吧。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下面是关于Arduino中ESP8266的SmartConfig一键配网相关记录，以便加深对智能家居一整套工作流程的理解。

### smartconfig工作原理
<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所谓SmartConfig就是手机App端发送包含WIFI用户名以及密码的UDP广播包，智能终端（开启了sniffer混杂模式）的WIFI芯片可以接收到该UDP包，通过接收到的UDP包解密出WIFI用户名密码，然后智能硬件配置收到的用户名密码到指定的WIFI上（[详情见](https://blog.csdn.net/qq_35350654/article/details/88911965)）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190723134945862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MzUwNjU0,size_16,color_FFFFFF,t_70#pic_center)

### beginSmartConfig()
<hr/>

函数说明：
```
/**
 * 启动配网模式
 * @return bool 是否启动配网模式成功
 */
bool beginSmartConfig();
```

### stopSmartConfig()
<hr/>

函数说明：
```
/**
 * 停止Smartconfig
 * @return bool 是否停止配网模式成功
 */
bool stopSmartConfig();
```

### smartConfigDone()
<hr/>

函数说明：
```
/**
 * 查找状态看是否配网完成
 * @return bool 是否启动配网模式成功
 */
bool smartConfigDone();
```

### 实例
<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我这里是直接使用的ESP8266-12F的开发板，上面集成了Arduino，可直接通过USB进行程序烧录，减少ESP8266和Arduino板子之间的连接工作，大大减少了开发工作量。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果你不是直接使用开发板，或者不知道如何在arduino中搭建ESP8266插件，可参见：https://www.jianshu.com/p/c6f8b59762b6， 当然ESP8266开发板某宝上一搜一大把，价格大多在20-100不等；

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在这个例子中我们只需要直接将板子用USB和电脑连接上即可，如果是第一次使用Arduino可能需要安装驱动（[详情见](https://blog.csdn.net/qq_35350654/article/details/94876016)）；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190723140139846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MzUwNjU0,size_16,color_FFFFFF,t_70#pic_center)


实例代码：
```
 void setup()
{
  //开启串口
  Serial.begin(115200);
  //启动配网模式
  smartConfig();
}
 
void loop()
{
  delay(100);
  Serial.println("loop");
}
/**
 * 一键配网关键代码
 */
void smartConfig()
{
  WiFi.mode(WIFI_STA);
  Serial.println("\r\nWait for Smartconfig");
  delay(2000);
  // 等待配网
  WiFi.beginSmartConfig();
 
 while (1)
  {
    Serial.print(".");
    delay(500);
    if (WiFi.smartConfigDone())
    {
      Serial.println("SmartConfig Success");
      Serial.printf("SSID:%s\r\n", WiFi.SSID().c_str());
      Serial.printf("PSW:%s\r\n", WiFi.psk().c_str());
      WiFi.setAutoConnect(true);  // 设置自动连接
      break;
    }
  }
 
  Serial.println("");
  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将代码烧录到板子上后，我们需要一个APP来进行配网，[ESP Touch](https://www.espressif.com/zh-hans/products/software/esp-touch/resources)  |  [SmartConfigAPP](https://pan.baidu.com/s/1eSMpA2e?errno=0&errmsg=Auth%20Login%20Sucess&&bduss=&ssnerror=0&traceid=) 都可以，我前面文章也提供了APP的[源码连接1](https://github.com/Life1412378121/EsptouchDemo) | [2](https://github.com/tingyouwu/SmartConfig)；

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下图是配网成功后请求天气预报的串口输出信息，代码见Demo处下来，麻烦star：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190723143850795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MzUwNjU0,size_16,color_FFFFFF,t_70#pic_center)
### Demo
<hr/>
SmartConfig配网成功后请求获取天气预报：

CSDN Demo：https://blog.csdn.net/qq_35350654/article/details/96980688 <br/>
GitHub Demo：https://github.com/Life1412378121/SmartConfigByEsp8266

<br/>
<br/>
作者：玉念聿辉 &nbsp;&nbsp; 编辑：吴明辉
<hr/>

文章参见：[大神1](https://blog.csdn.net/dpjcn1990/article/details/92830098)、[大神2](https://www.jianshu.com/p/c6f8b59762b6)；
