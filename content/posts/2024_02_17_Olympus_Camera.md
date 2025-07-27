---
title: "Olympus Camera"
date: 2024-02-17T12:48:31+08:00
tags: ["Python", "coroutine",'asyncio','project']
categories: ["程式"]
draft: false
---
# 緣由
自己的相機Olympus，平常要讀取照片，通常用電腦直接讀取sd記憶卡，

這次在mac筆電上想看照片，但又沒辦法讀取sd，

因為相機有支援wifi功能，在手機或平板也是使用官方OM Image Share app做圖片讀取，

但mac無法安裝app，於是動手寫了一個小專案從wifi連線進入抓圖

# 實作方式
主要參考[這篇文章](https://thedigitalstory.com/2016/11/connect-olympus-camera-via-wifi.html
)，發現連上wifi後，可以從瀏覽器進入http://192.168.0.10，連入相機的內部後端網站，
平常提供給官方app連線

但是沒有提供任何的多圖下載功能，只能單張右鍵下載，所以寫一個python小專案，用asyncio+爬蟲的方式將大量圖片抓回電腦

# 有意思的地方
因為是相機的內部，所以被鎖定最大連線數只能為2

這點跟平常的後端網站的差異巨大，畢竟相機的core不能跟電腦的core相比，所以在這邊即使採用coroutine，速度也是很慢

近年新款的機型不知道連線數會不會支援更多？

# Github
https://github.com/hanksky12/OlympusCameraImage