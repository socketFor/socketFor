---
title: "ESP8266实例"
date: 2025-05-18
categories: 
  - "esp8266"
---

## arduino库指令集

[https://docs.arduino.cc/programming](https://docs.arduino.cc/programming)

## HTTP通讯

```
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <ArduinoJson.h>
#include "index.h"
#define AP_ssid   "TaichiMaker_WIFI" //这里改成你的设备当前环境下接入点名字
#define password  "12345678"         //这里改成你的设备当前环境下接入点密码

#define wifi_name1 "socket"
#define wifi_password1 "f71796316"

#define pinA D0
#define pinB D1

bool status = false;

ESP8266WebServer Server(80);//启动80端口，实例化ESP8266WebServer

//传递函数
void homepage() {
  // 通过FPSTR闪存文件发送"text/html"类型的数据到网页
  Server.send(200,"text/html",FPSTR(indexHtml));
  Serial.print("网站接入成功");

}

void contral(){
  String jsonData ="";
  Server.sendHeader("Access-Control-Allow-Origin", "*");
  String jsonStr = Server.arg("plain");  // 获取原始 JSON 字符串
  StaticJsonDocument<200> doc;
  deserializeJson(doc, jsonStr);
  
  String cod = doc["cod"];      // 提取字段
  String text = doc["text"];
  
  if(text=="up"){
    jsonData = "{\"status\":\"ok\"}";
    status = 0;

  }
  else{
    jsonData = "{\"status\":\"no\"}";
    status = 1;
  }
  Server.send(200, "application/json", jsonData); 
}

//按键控制灯

void Button(){
  if(digitalRead(pinB)==0){
    delay(20);
    while(digitalRead(pinB)==0);
    delay(20);
    status = !status;
  }
}

void setup() {
  // 启动串口通讯
  Serial.begin(9600); 
  pinMode(pinA,OUTPUT);
  pinMode(pinB,INPUT_PULLUP);
  digitalWrite(pinA,LOW);
  Serial.println();
  //配置接入点信息
  WiFi.mode(WIFI_AP_STA);
  //启动接入点模式，并设置账号和密码
  Serial.print("设置接入点中 ... ");
  //启动校验式网络（需要输入账号密码的网络）,通道为3，wifi隐藏，最大连接数=4
  WiFi.softAP(AP_ssid, password,3);

  Serial.println("");              // 通过串口监视器输出信息
  Serial.print("当前工作模式:");     // 告知用户设备当前工作模式
  Serial.println(WiFi.getMode());
  Serial.print("接入点名字:");    
  Serial.println(AP_ssid);         // 告知用户建立的接入点WiFi名
  Serial.print("接入点密码:");        
  Serial.println(password);        // 告知用户建立的接入点WiFi密码

  Serial.println("AP创建完成正在连接wifi");
  WiFi.begin(wifi_name1,wifi_password1);
  while(WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.print(".");
  }
  Serial.print(" ");
  Serial.print("wifi接入成功");
  Serial.print(WiFi.localIP());

//每当有客户端向服务器发送HTTP请求时，我们可以利用on函数来设置HTTP请求回调函数。
  Server.on("/",homepage);
  Server.on("/submit",HTTP_POST,contral);
  Server.begin();
  Serial.println("");
}

void loop() {
  Button();
  Server.handleClient();
  digitalWrite(pinA,status);
}
```

MQTT通讯

```
#include <Arduino.h>
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <regex>
#include <exception>
#include <String>
//  wifi名、密码
const char* WifiName = "ESP8266";
const char* WifiPassword = "12345678";

// MQTT
const char* MqttServer = "175.24.227.142";
unsigned int MqttPort = 1883;

//AP  Wifi
const char* GetWifiName = "socket";
const char* GetWifiPassword = "f71796316";

// 全局定义动态数组和长度记录
char* mqttPayload = nullptr;      // 字节数组指针要修改动态数组只能写nullptr
unsigned int payloadLength = 0;   // 存储实际数据长度
uint16_t flag = 0; //回调函数标志位
int rgbValues[3] = {0, 0, 0}; // 初始化为 {R, G, B}

const int pwmPinR = D0;  // GPIO14
const int pwmPinG = D1;  // GPIO14
const int pwmPinB = D2;  // GPIO14
int pwmValue = 0;

WiFiClient espClient;
PubSubClient client(espClient);

//提取数字
void GetNumber(void){
  uint32_t i=0;
  std::string text = mqttPayload;
  std::regex pattern("\\d+");
  std::smatch matches;
  //regex_search执行一遍会用正则找到一个数直达找不到为止，找到的数会存入matches中
  while(std::regex_search(text,matches,pattern)){
    Serial.println();
    rgbValues[i] =std::stoi(matches[0].str().c_str());
    //suffix()会获取匹配后剩余部分再次进入循环
    text = matches.suffix().str();
    i++;
  }
  Serial.print(rgbValues[0]);
  Serial.print("-");
  Serial.print(rgbValues[1]);
  Serial.print("-");
  Serial.print(rgbValues[2]);
}

//通过MQtt实现RGB
void RGBInit(void){
  GetNumber();
  analogWrite(pwmPinR, 255-rgbValues[0]);
  analogWrite(pwmPinG, 255-rgbValues[1]);
  analogWrite(pwmPinB, 255-rgbValues[2]);
}

//回调函数
void callback(char* topic, byte* payload, unsigned int length) {
    // 释放旧内存（避免内存泄漏）
  delete[] mqttPayload;
    // 动态分配新内存
  mqttPayload = new char[length];  // 创建精确长度的数组[6,8](@ref)

  for (unsigned int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
    mqttPayload[i] = payload[i];
  }
  payloadLength = length;
  Serial.println();
  //置标志位提醒收到数据进入GetNumber函数
  flag = 1;
}

//MQTT初始化
void MQTTInit(){
  client.setServer(MqttServer, MqttPort);
  client.setCallback(callback);
}

//WIFI初始化
void WifiInit(){
  WiFi.mode(WIFI_AP_STA);
  WiFi.softAP(WifiName,WifiPassword,3);
  Serial.println(" ");
  Serial.println("Wifi名字: "+String(WifiName));
  Serial.println("Wifi名字: "+String(WifiPassword));

  WiFi.begin(GetWifiName,GetWifiPassword);
  while(WiFi.status()!=WL_CONNECTED){
    delay(500);
    Serial.print(".");
  }
  Serial.println(" ");
  Serial.println("连接成功");
  Serial.println(WiFi.localIP());
}

void reconnect(){
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(pwmPinR,OUTPUT);
  pinMode(pwmPinG, OUTPUT);
  pinMode(pwmPinB, OUTPUT);

  WifiInit();
  MQTTInit();
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
  if(flag==1){
      RGBInit();
      flag = 0;
  }

}
```
