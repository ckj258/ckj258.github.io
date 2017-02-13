---
layout: post
title: "node调用redis"
date: 2017-1-17 09:55:52
comments: true
reward: false
tags: 
	- node.js学习
---


REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。  
Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。   

<!-- more -->
## 安装
```
npm install redis
```
## 使用案例
### 初始化
```	javascript
var password = "ckj258";
var redis_host = "127.0.0.1";
var redis_port = 3101;
var redis_options = {"no_ready_check":config.redis.no_ready_check};

var client;

function init() {
	console.log('init redis ... ... ');
	client = redis.createClient(redis_port, redis_host, redis_options);
	client.on("error", function (err) {
  		console.log("redis meet Error " + err + ';' + redis_host + ';' + redis_port);
  		setTimeout(init, 5*1000);	// 5秒后重连
	});
	client.auth(password);
}
```

###  setVaule
```	javascript
function setVaule(key, values) {
	client.set(key, values);

}
```

###  setValueWithExpire
```	javascript
function setValueWithExpire(key, values) {
	client.set(key, values);
	client.expire(key, expire);	// 单位秒

}
```

###  getVaule
```	javascript
function getVaule(key, callback ) {
	client.get(key, function(err, redis_result) {
		callback(err, redis_result);
	});
}
```

###  delVaule
``` javascript
function del(key) {
	client.del(key);
}
```


###	 test
```  javascript
init();
setVaule("name","Dantel");
getVaule("name",function(err,redis_result)
{	
	if(!error)
	{
		console.log(redis_result);
	}
});
```

### 运行结果

![](/assets/image/node_redis_1.png)  







