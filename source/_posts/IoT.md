---
title: IoT
date: 2022-11-20 15:44:58
tags: [IoT, Web, MQTT, 物联网]
categories: [嵌入式, 物联网]
top: 
---

- [IoT](#iot)
  - [题目要求](#题目要求)
  - [使用说明](#使用说明)
  - [设计思路](#设计思路)
  - [文档](#文档)
  - [TODO LIST](#todo-list)
  - [初步实现](#初步实现)
    - [2022.11.22](#20221122)


# IoT

<center>
    <font color=red>
        <b>
            IoT 2022 期末作业
        </b>
    </font>
</center>

## 题目要求
![](https://s2.loli.net/2022/12/02/WRoc18ew3uBCyY4.png)

## 使用说明

## 设计思路

## 文档

## TODO LIST
1. [x] 2022.11.22: 项目初始化
2. [ ] 完成发布和订阅端的基本通信功能
3. [ ] 实现发布端的数据采集功能
4. [ ] 实现订阅端的数据展示功能
5. [ ] 实现数据的持久化存储
6. [ ] 实现机器学习模型的训练和预测
7. [ ] 实现数据的可视化展示
## 初步实现

### 2022.11.22
1. 创建一个新的项目
```zsh
pnpm init
```

2. 添加mqtt依赖
```zsh
pnpm add mqtt
```

3. 创建index.js
```zsh
touch index.js
```

4. 添加start脚本 
```zsh
vim package.json
```

```json
{
  "name": "iot",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "mqtt": "^4.2.8"
  }
}
```

5. index.js的内容
```js
const mqtt = require('mqtt');

const host = 'broker.emqx.io';
const port = '1883';
const clientId = `mqtt_${Math.random().toString(16).slice(3)}`;

const connectUrl = `mqtt://${host}:${port}`;
const client = mqtt.connect(connectUrl, {
  clientId,
  clean: true,
  connectTimeout: 4000,
  username: 'emqx',
  password: 'public',
  reconnectPeriod: 1000,
});

const topic = '/nodejs/mqtt';
client.on('connect', () => {
  console.log('Connected');
  client.subscribe([topic], () => {
    console.log(`Subscribe to topic '${topic}'`);
  });
  client.publish(topic, 'nodejs mqtt test', {qos: 0, retain: false}, (error) => {
    if (error) {
      console.error(error);
    }
  });
});
client.on('message', (topic, payload) => {
  console.log('Received Message:', topic, payload.toString());
});
```

6. 运行
```zsh
pnpm start
```

7. 在MQTTX中订阅`/nodejs/mqtt`主题，可以看到消息
8. IOS端使用MQTT Tool订阅`/nodejs/mqtt`主题，可以看到消息
