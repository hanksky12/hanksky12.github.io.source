---
title: "Architecture_Patterns_With_Python基礎概念"
date: 2024-09-01T09:55:08+08:00
tags: ["DDD", "知識"]
categories: ["程式"]
series: ["DDD"]
draft: false
---

# Value Object 
## 值物件
### 定義:單存資料，沒有特定身份，通常讓他不可變
兩個物件相等的條件是:所有值相等

python 實作方式 dataclass

例子:兩個10元硬幣，他們就是相等，不必在乎是否是同一枚硬幣



# Entity (reference Object)
## 實體(在POEAA書裡也稱為參考物件)
### 定義:有一個長期的持久性身份，值可變
兩個物件相等的條件是:持久性身份相等

實作方式: id 欄位

例如: 兩個人，身分證號碼相同，就判斷是同一人，不必在乎是否名字或其他資料相同