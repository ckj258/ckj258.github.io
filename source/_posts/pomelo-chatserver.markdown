---
layout: post
title: "pomelo学习入门笔记"
date: 2016-12-20 16:55:19
comments: true
reward: false
tags: 
	- pomelo
---
github : https://github.com/ckj258/PomeloClient/game-server

## 前言					
pomelo是一个游戏服务器框架，使用很简单。		
它包括基础开发框架和一系列相关工具和库，可以帮助开发者省去游戏开发中枯燥的重复劳动和底层逻辑工作，		
让开发者可以更多地去关注游戏的具体逻辑，大大提高开发效率。	

<!-- more -->

## 项目结构
![](/assets/image/pomelo_screenshot3.png)  	
../app/：         存放游戏逻辑脚本
../config/:       存放配置文件 
../logs/:         存放历史日志
../node_modules/: 存放用到的npm包 
../app.js：       pomelo入口
../package.json:  npm包配置文件


## 运行架构
![](/assets/image/multi-chat.png) 


## 代码架构
app.js中声明了"connector","gate","chat"服务器		
服务器配置信息在config目录下，servers.json配置具体的应用服务器信息。		
在配置文件中，分为development和production两种环境，表示开发环境和产品环境		
```
app.configure('production|development', 'connector', function(){
	app.set('connectorConfig',
		{
			connector : pomelo.connectors.hybridconnector,
			heartbeat : 3,
			useDict : true,
			useProtobuf : true
		});
});

app.configure('production|development', 'gate', function(){
	app.set('connectorConfig',
		{
			connector : pomelo.connectors.hybridconnector,
			useProtobuf : true
		});
});

// app configure
app.configure('production|development', function() {
	// route configures
	app.route('chat', routeUtil.chat);

	// filter configures
	app.filter(pomelo.timeout());
});
```

### "chat"服务器
../servers/chat目录下包含hander和remote两个文件夹，
其中hander下主要存放监听器，负责接收客户端发送请求做相应处理

```
handler.send = function(msg, session, next) {
	var rid = session.get('rid');
	var username = session.uid.split('*')[0];
	var channelService = this.app.get('channelService');
	var param = {
		msg: msg.content,
		from: username,
		target: msg.target
	};
	channel = channelService.getChannel(rid, false);

	//the target is all users
	if(msg.target == '*') {
		channel.pushMessage('onChat', param);
	}
	//the target is specific user
	else {
		var tuid = msg.target + '*' + rid;
		var tsid = channel.getMember(tuid)['sid'];
		channelService.pushMessageByUids('onChat', param, [{
			uid: tuid,
			sid: tsid
		}]);
	}
	next(null, {
		route: msg.route
	});
};
```
send方法主要功能是接收客户端发送的聊天信息，并广播给同channel下玩家，回调函数next()返回客户端错误码		

至于remote，当有客户端连接到connector上后，connector会向chat.remote发起远程过程调用，chat.remote会将登录的用户，加到对应的channel中		



