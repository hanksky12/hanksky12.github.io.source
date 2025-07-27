---
title: "WebSocket"
date: 2025-05-11T11:23:51+08:00
tags: ["知識"]
categories: ["程式"]
series: ["Computer Science"]
draft: false
---
# 緣由
因寫side project重新接觸到WebSocket，順便理清以前模糊的概念


# 關於WebSocket Vs HTTP
### 一樣
1. 都是應用層的協定
2. 都是基於TCP的連線
3. 都走在80/443 port
4. 都有未加密版本ws://  http:// 和加密版本wss:// https://

### 不一樣
1. WebSocket是雙向通訊(server可以主動向client)，HTTP是單向通訊
2. WebSocket是持久連線，HTTP是短暫連線
3. WebSocket是基於事件(OnOpen,OnMsg)，HTTP是基於請求
4. WebSocket是基於frame，HTTP是基於request/response

# 解決什麼問題?
1. HTTP協定是單向，如果server端有事件要通知前端，只能等client端不斷的輪詢server端
2. 而WebSocket是雙向的，初步建立連線後，後續的溝通是即時且不用重建TCP連線


# 建立連線
雖然協議是ws://或wss://，但實際建立連線是基於HTTP升級，所以port才會跟http相同

### Client端
1. client溝通時握手階段一定是http get方法，header裡面會有一個Upgrade的欄位和相關資訊(套件實作會自動帶)
2. 因為是雙向溝通，也要定義當server傳事件OnOpen,OnMsg,OnClose,OnError的處理方式

### Server端
1. server端啟動的時候，先啟動一個HTTP server，使用ws套件去定義當有ws請求時，對每種事件的處理方式OnOpen,OnMsg,OnClose,OnError
2. 在特定路由get方法下，將HTTP請求轉換成ws請求，通常此時會將http get帶過來的身份驗證token內容放入ws內，提供後續OnOpen的使用
3. 不管哪一個http路由升級成ws，在OnMsg的請求內是無法分辨從哪一個路由過來的，所以要自己去實作
4. 因為是長連線，不用每個訊息都帶身份驗證