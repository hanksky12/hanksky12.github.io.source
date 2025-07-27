---
title: "Message Queue[二] RabbitMq & Pika Pika"
date: 2023-11-18T07:09:33+08:00
tags: ["rabbit mq","pika",'message queue',"distributed task queue"]
categories: ["程式"]
series: ["Queue"]
draft: false
---
# 緣由
在工作上，因為需要導入發布訂閱模式，故在幾種常見的Mq中做選擇

## 發布/訂閱
|          | 速度  | 訊息保證          | 訊息大小 | 訊息量 |
|----------|-----|---------------|------|-----|
| redis    | 快   | 沒有            | 小    | 小   |
| rabbitMQ | 稍慢  | ACK 模式，確認拿走才刪 | 中    | 中   |
| kafka    | 有點慢 | 透過歷史紀錄        | 大    | 大   |

---
# RabbitMq
基於AMQP協議

## Simple 模式
producer將massage直接放到queue，一位consumer去消費

## Worker 模式
同Simple模式，但是改為多位consumer去消費，其中一位拿走，訊息就沒了，常見於分散式爬蟲的目標網址，
這邊還可以設定欲取數量

## Publish/Subscribe 模式
producer將massage送到exchange，藉由多個queue去綁定到exchange，一次送給多個queue，每個queue對應一位消費者

## Routing 模式
同Publish/Subscribe模式，多加了訊息的走向篩選機制

加入了routing key概念，producer發送時加入routing key，每個queue在綁定到exchange同時

先宣告自己要監聽的routing key，讓exchange可以知道訊息應該發送到哪些queue，篩選機制是要完全match的，才會送達


## Topics 模式
同Routing模式的升級版，篩選機制改用模糊比對

## RPC模式
在原producer發送訊息前，先定義要回傳的queue與關聯id，發送後，producer轉為consumer等待[處理後的訊息]回傳

在原consumer收到訊息後，對訊息做處理後，轉為producer，並根據要回傳的queue與關聯id，傳送[處理後的訊息]

https://github.com/hanksky12/RabbitMQ_Tutorials

---

# Port
:5672 server  :15672 後台

# Exchange 交換機
決定訊息走向的人，單純做篩選，訊息不停留在此

# Queue 貯列
會將訊息累積的人，當消費者不在線上或還未消費的，訊息會積累在此

# Ack 機制
可以在訊息處理完，決定此訊息是否已經消費完成，

用ack確認處理成功，或nack代表訊息接下來的走向，返回queue重新排隊，或者丟棄此訊息

# Dead Letter 死信機制

Why 死信要處理？

當處理訊息失敗，應該直接丟棄？

或重回queue排隊，可能一直每次失敗，導致漸漸阻塞queue


## 死信條件:
1. 訊息在queue上面超過max可停留時間
2. 訊息在queue上面超過max訊息數量
3. 消費時，使用nack或reject

當上面發生時，訊息會由queue去到死信交換機，死信exchange和queue與普通的一樣，
只是看在死信的消費者想要如何統一處理這些訊息

這邊要注意的是，死信的如何處理，由每個的queue去決定，不互相干涉



## 會刪除的queue
在rabbit mq裡面只有兩種機制
1. 在宣告queue的同時，設定auto_delete=True
2. 使用臨時queue，消費後，自動刪除

使用這兩種queue，都會導致消費者離線後，如果exchange還有送new message，會直接消失

# Heartbeats
在pika要特別注意，沒有實作重連機制，且客戶端與server端間，有心跳檢測，如果太久沒收到，會關閉Tcp連線，
導致訊息要送的時間跳出ConnectError，所以要try except，去自動重連，
在宣告連線參數時，可以設定HeartbeatsTimeOut，官方不建議設0(不檢測)，不是真正解決問題