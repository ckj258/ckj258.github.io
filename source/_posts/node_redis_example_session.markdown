---
layout: post
title: "redis实用之session管理"
date: 2017年1月22日16:38:22
comments: true
reward: false
tags: 
	- node.js学习
---


在游戏服务器里，登录服务器是必不可少的一块  	
运用redis设置带时效的缓存，可以很好的解决session管理的问题。。
<!-- more -->
## sessionManager
### 用户登录
```	javascript
/**
 * 用户登录
 */
function LoginIn(userinfo) {
	redishelper.setValueWithExpire(getNewSessionId(),userinfo,SESSION_EXPIRE);

}
```
### 用户登出
``` javascript
/**
 * 用户登出
 */
function loginOut(session_id) {
	redishelper.getVaule(session_id,function(err,redis_result) {
if(!err)
{
	saveInfoToDB(redis_result);
	////将缓存存储到数据库
}
		redishelper.del(session_id);
});

}
}
```

### 判断用户是否登录
```
/**
 * 判断用户是否登录
 */
function isLogin(session_id, callback) {
	(function(session_id){
		redishelper.getVaule(session_id, function(err, redis_result){
			if(err) {
				callback(false, agreement.redisErrorDesc);
				return ;
			}
			if(redis_result != undefined) {
				callback(true, JSON.parse(redis_result));
				return ;
			}
			callback(false, agreement.invalidSessionIdDesc);
		});
	})(session_id);

}
```

### 获取一个会话ID
```
/**
 * 获取一个会话ID
 */
function getNewSessionId() {
	if(startVaule === 0) {
		startVaule = parseInt(new Date().getTime());
	}
	startVaule++;
	return SESSION_ID_HEAD + startVaule;
}
```

### 调用示例
```
function login(user_id, passwords, callback) {

		user_db.isUserIdExit(dbmanager.getClientS(), user_id, passwords,function(errorcode, result) {
			if(errorcode === 0) {
				successGetUserLoginInfo(result,callback);
			}else {
				if(errorcode === 201) {
					callback(agreement.dbErrorDesc);
				}else {
					callback(agreement.noUserInfoDesc);
				}
			}
		});
}
// 去DB查询用户信息并更新缓存
function successGetUserLoginInfo(result, callback) {
	(function(result){
		player_logic.getPlayerInfo(result.user_id, result.fortune_id, function(success, user_result){
			var clientResult;
			if(success) {
				var session_id = session_manager.getNewSessionId();
				var newResult = createUserSessionInfo(session_id, result, user_result);
				session_manager.LogicIn(newResult,session_id);
				clientResult = getLoginSuccessInfo(newResult.session_id);
			} else {
				clientResult = agreement.dbErrorDesc;
			}
			console.log('clientResult : ' + JSON.stringify(clientResult));
			callback(JSON.stringify(clientResult));
		});
	})(result);
}
```






