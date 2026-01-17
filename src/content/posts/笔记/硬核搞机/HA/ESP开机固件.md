---
updated: 2026-01-17
title: ESP开机固件
published: 2026-01-17
description: ""
image: ""
tags:
  - HomeAssistant
category: 硬核搞机
draft: false
---
# ESP开机固件

```cpp
#define BLINKER_WIFI
#define BLINKER_MIOT_OUTLET
#include <Blinker.h>

char auth[] = "";  //云平台获取的key
char ssid[] = "";     //WiFi名称
char pswd[] = "";      // WiFi密码
int GPIO = 0;
bool oState = false;

BlinkerButton Button1("btn-abc");  //创建按钮组件，数据键

void button1_callback(const String& state) {
  Button1.text("get button state: ", state);
  BLINKER_LOG("get button state: ", state);
  if (state == BLINKER_CMD_BUTTON_TAP) {     //响应短按
    if (digitalRead(LED_BUILTIN) == HIGH) {  //若电脑是关闭状态（电脑没有通过APP启动）
      digitalWrite(LED_BUILTIN, LOW);
      digitalWrite(GPIO, HIGH);
      BLINKER_LOG("Button tap!");
      Blinker.delay(150);
      digitalWrite(GPIO, LOW);
      Button1.color("#FFC800");
      Button1.text("运行中");
      Button1.print();
    } else if (digitalRead(LED_BUILTIN) == LOW) {
      digitalWrite(LED_BUILTIN, HIGH);
      digitalWrite(GPIO, HIGH);
      BLINKER_LOG("Button tap!");
      Blinker.delay(150);
      digitalWrite(GPIO, LOW);
      Button1.color("#FFC800");
      Button1.text("关闭");
      Button1.print();
    }
  } else if (state == BLINKER_CMD_BUTTON_PRESSUP) {  //响应长按

    if (digitalRead(LED_BUILTIN) == LOW) {  //若电脑是开启状态（电脑通过APP启动）
      digitalWrite(GPIO, HIGH);             //继电器接通
      BLINKER_LOG("Button pressed!");
      Blinker.delay(5000);
      digitalWrite(LED_BUILTIN, HIGH);  //eps-01s灯灭
      digitalWrite(GPIO, LOW);          //5秒后继电器断开，相当于长按电脑开机键5秒
      Button1.color("#CCCCCC");
      Button1.text("已关闭");
      Button1.print();
    } else {                   //关闭状态时LED_BUILTIN为高电平，不亮
      Button1.text("没开机");  //按键提示“没开机”
      Button1.print();
      Blinker.delay(3000);
      Button1.color("#CCCCCC");
      Button1.text("已关闭");  //“没开机”变更为“已关闭”
      Button1.print();
    }
  }
}
void miotPowerState(const String& state, uint8_t num) {
  BLINKER_LOG("need set power state: ", state);
  if (state == BLINKER_CMD_ON) {
    digitalWrite(LED_BUILTIN, LOW);
    digitalWrite(GPIO, HIGH);
    Blinker.delay(150);
    digitalWrite(GPIO, LOW);
    BlinkerMIOT.powerState("on");
    BlinkerMIOT.print();
    oState = true;
  } else if (state == BLINKER_CMD_OFF) {
    digitalWrite(GPIO, HIGH);
    Blinker.delay(150);  //改成100-300，响应短按关机，需配合电脑电源键策略，默认策略可行；而且数值2000以上，小爱会提示出问题（会响应关机动作）
    digitalWrite(LED_BUILTIN, HIGH);
    digitalWrite(GPIO, LOW);
    BlinkerMIOT.powerState("off");
    BlinkerMIOT.print();
    oState = false;
  }
}

void miotQuery(int32_t queryCode)  //完全复制来的，写上，免得串口测试时一直提示没有…
{
  BLINKER_LOG("MIOT Query codes: ", queryCode);

  switch (queryCode) {
    case BLINKER_CMD_QUERY_ALL_NUMBER:
      BLINKER_LOG("MIOT Query All");
      BlinkerMIOT.powerState(oState ? "on" : "off");
      BlinkerMIOT.print();
      break;
    case BLINKER_CMD_QUERY_POWERSTATE_NUMBER:
      BLINKER_LOG("MIOT Query Power State");
      BlinkerMIOT.powerState(oState ? "on" : "off");
      BlinkerMIOT.print();
      break;
    default:
      BlinkerMIOT.powerState(oState ? "on" : "off");
      BlinkerMIOT.print();
      break;
  }
}
uint32_t hebt_time = 0;        //心跳包持续强制发送变量
uint32_t hebt_time_limit = 0;  //心跳包发送周期限制，时间戳变量
void heartbeat()               //用户心跳包回调函数(APP定时向设备发送心跳包请求{"get":"state"}，设备默认返回{"state":"online"},若用户有自定义状态需要在收到心跳包时返回, 可声明并在setup()中注册此回调函数)
{
  hebt_time = millis();  //APP请求一次心跳后，两分钟内持续发送的标志(赋值当前时间戳)
}
void my_heartbeat()  //用户心跳包内容函数(心跳包实际内容，不直接放在回调是因为这样可以在回调执行之后2分钟或更久时间内不停返回心跳包内容，实现数据实时刷新，防止混淆心跳时间戳和心跳频率限制时间戳)
{
  if (millis() - hebt_time_limit > 10000)  //当前减去上次大于10秒才能发，用于计时，最快5秒一次心跳(快于1秒一次会被拦截，串口显示MSESSAGE LIMIT)
  {
    /*这里放自己的心跳包内容，例如Num1.print之类*/

    hebt_time_limit = millis();  //hebt_time_limit用于计时，最快5秒一次心跳
  }
}
uint32_t offline_millis = 0;  //掉线时间戳
bool offline_flag = false;    //掉线触发标志
void setup() {
  Serial.begin(115200);                //初始化硬件串口UART0波特率115200bps(调试时需要相应的调整Aruino>工具>串口监视器>波特率为115200)
  BLINKER_DEBUG.stream(Serial);        //将Blinker库代码调试信息流打印到硬件串口
  BLINKER_DEBUG.debugAll();            //开启所有调试信息
  Blinker.begin(auth, ssid, pswd);     //调用Blinker库的开始成员函数，初始化Wifi连接(参数：授权码、WiFi名、密码)
  Blinker.attachHeartbeat(heartbeat);  //注册用户心跳包回调函数，Blinker库代码每30秒发送心跳包时会顺带执行此函数(此函数不用返回任何值)
  Serial.begin(115200);
  BLINKER_DEBUG.stream(Serial);

  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, HIGH);  //这块esp8266（指示灯）默认高电平关闭状态
  pinMode(GPIO, OUTPUT);
  digitalWrite(GPIO, LOW);  //继电器（指示灯）默认低电平关闭状态

  Blinker.begin(auth, ssid, pswd);
  BlinkerMIOT.attachPowerState(miotPowerState);
  BlinkerMIOT.attachQuery(miotQuery);
  Button1.attach(button1_callback);
  //  Button2.attach(button2_callback);
  Blinker.attachHeartbeat(heartbeat);
}

void loop() {
  Blinker.run();
  if (Blinker.connect())  //调用一下Blinker库的连接检测函数，若检测到MQTT服务器连接成功则执行以下用户代码
  {
    offline_millis = 0;
    offline_flag = false;  //在线，所以清空掉线状态
    //用户代码放这里：*****************************************************************************
    if (millis() - hebt_time < 120000) my_heartbeat();  //当前减去APP上次请求心跳小于两分钟强制连续发送心跳包

    //******************************************************************************************
  } else if (!Blinker.connected() && millis() > 180000)  //开机三分钟后，点灯库一旦连不上，就判掉线，触发一次
  {
    offline_flag = true;  //Blinker.connected()触发标志
  }
  if (millis() > 180000 && !offline_millis && offline_flag)  //开机后三分钟后，若触发，则记录第一次掉线时间，之后!offline_millis为假，不再触发
  {
    offline_flag = false;       //Blinker.connected()触发取消
    offline_millis = millis();  //记录掉线时间戳后!offline_millis为假，不再触发
  }
  if (millis() > 180000 && offline_millis && millis() - offline_millis >= 180000)  //开机三分钟后，掉线时间戳不为0，出现三分钟以上断连就重启
  {
    BLINKER_LOG_ALL(BLINKER_F("******************************************Reatart***************************************"));  //串口打印重启消息

    ESP.reset();  //ESP8266 硬件重启
                  //ESP.restart();//ESP32 freertos重启
  }
}
```
