---
layout: post
title: "node调用mysql"
date: 2017年1月24日15:04:40
comments: true
reward: false
tags: 
	- node.js学习
---


node.js与mysql交互。 

<!-- more -->
## npm安装
```
npm install mysql
```
## 使用案例
### 初始化
```	javascript
function initClient(callback) {
    option = {
        host : config.mysql_catchfish_debug.host,
        port :  config.mysql_catchfish_debug.port,
        user :  config.mysql_catchfish_debug.user,
        password :  config.mysql_catchfish_debug.password,
        database :  config.mysql_catchfish_debug.database
    };
    client = mysql.createConnection(option);
    client.connect(function (err) {
       if (err) {
            console.log('initClient [[mysql db]]error when connecting to db:requestConnectMySqlTime = ' + requestConnectMySqlTime + ';err = ', err);
            requestConnectMySqlTime++;
            setTimeout(initClient, 5*1000);// TODO :  if retry time over 3, need send mail
            callback(false);
       }else {
            requestConnectMySqlTime = 0;
            callback(true);
        }
    });

    client.on('error', function (err) {
        console.log('initClient db error', err);
        if (err.code === 'PROTOCOL_CONNECTION_LOST') { // 如果是连接断开，自动重新连接
            initClient(function(result){
                console.log('reconnect db result : ' + result);
            });
        }
    });
}

```

###  调用
```	javascript
function syncPlayerBaseFortuneInfo(client, sync_info, callback) {
  var values = [
    [sync_info.user_id, sync_info.coins, sync_info.diamonds, sync_info.exps, sync_info.turrent_level]
  ];
  client.query(' INSERT INTO user_fortune_' + sync_info.fortune_id + ' (user_id, coins, diamonds, exp, turrent_level) VALUES ? ' +
      ' ON DUPLICATE KEY UPDATE coins = VALUES(coins),' +
      ' diamonds = VALUES(diamonds) , exp = VALUES(exp), turrent_level = VALUES(turrent_level)',
  	   [values],
    function (err, results) {
        if (err) {
            callback(false);
            return;
        }
        callback(true);
    });
}
```








