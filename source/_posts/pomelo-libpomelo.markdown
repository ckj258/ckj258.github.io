---
layout: post
title: "pomelo服务器与cocos2d交互"
date: 2016-12-13 14:30
comments: true
reward: true
tags: 
	- pomelo
---
github : https://github.com/ckj258/PomeloClient/

Pomelo 是基于 Node.js 的高性能、分布式游戏服务器框架。
非常适合用来开发轻量级手游客户端。    
之前一直在用pomelo-cocos2dx架构，在此记录一下使用心得。  


##运行项目

### Client

coco2dx引擎版本3.10，拷贝引擎文件至根目录编译，运行  

### Server

cd game-server</br>
npm install</br>
pomelo start</br>


<!-- more -->
##类详解

###PomeloSocket
负责与server的交互
主要方法
```
	void conConnect(const char*ServerIP, int ServerPort	);//建立连接
	void quit();										  //断开连接

/* ==============================================================================
 * 功能描述：发送事件,回调函数 typedef void (*pc_notify_cb_t)(const pc_notify_t* req, int rc);  rc表示错误码,无参数返回. 例用于上传游戏数据等等
 * 创 建 者：ckj
 * 创建日期：2016年12月13日17:30:49
 * ==============================================================================*/
	void sendEvents(const char* params, const char*remote, pc_notify_cb_t event_cb);
/* ==============================================================================
 * 功能描述：发送请求,回调函数 typedef void (*pc_request_cb_t)(const pc_request_t* req, int rc, const char* resp);  rc表示错误码,resp为服务器json结构. 例用于登录等需要返回结果的场景  
 * 创 建 者：ckj
 * 创建日期：2016年12月13日17:33:53
 * ==============================================================================*/
	void sendRequest(const char* params, const char*remote, pc_request_cb_t request_cb);
```


###PomeloHandler
观察者模式中的观察者，负责向游戏中注册监听的对象发放广播
```
	void addHandlerListen(PomeloListen* listen);//添加监听listen
```
### PomeloListen
观察者模式中的对象，一个纯虚类，负责接收消息
```
	virtual void handle_event(const char* msgId, const char* msgBody)=0;  //msgId为服务器notify分发的remote
```

##调用方法
因为pomelo基于分布式游戏服务器框架，所以进入游戏时最好通过http请求获取TCP连接ip和port
然后调用onConnect建立连接

游戏中将需要注册监听的对象继承PomeloListen，重写handle_event()方法即可  
具体示例见ChatDialog.hpp ChatDialog.cpp

##效果截图
![](/assets/image/pomelo_screenshot1.png)  
![](/assets/image/pomelo_screenshot2.png)  


界面粗糙，请勿见怪

##platform

android和mac自行链接对应库文件
END.     
2016年12月13日17:48:50 Dantel Chen.

