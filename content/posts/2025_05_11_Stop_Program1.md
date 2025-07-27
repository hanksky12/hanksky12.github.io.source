---
title: "優雅停止程式"
date: 2025-05-11T10:23:51+08:00
tags: ["知識", "程式停止", "OS訊號"]
categories: ["程式"]
draft: false
---
# 緣由
因為工作加入新專案，看到對停止程式有專門做處理，才回頭思考自己對這部分的研究不足


# 關於OS訊號
通常外部腳本，要停止process，首先應該用kill pid，其次才用kill -2 pid，最後才用kill -9 pid

| 方法          | 訊號             | 說明        | 是否可攔截 | 建議用途   |
|-------------| -------------- | --------- | ----- | ------ |
| `kill $pid` | `SIGTERM` (15) | 預設，優雅關閉   | ✅ 是   | ✅ 首選   |
| `kill -2`   | `SIGINT` (2)   | 模擬 Ctrl+C | ✅ 是   | 可選補救手段 |
| `kill -9`   | `SIGKILL` (9)  | 強制結束，無法攔截 | ❌ 否   | 最後手段   |
| `kill -0`   | `SIGCONT` (18) | 確認process是否存在 | ❌ 否   | 用來確認process是否存在 |


# sh腳本內使用
````
kill pid        # 預設 SIGTERM，請求優雅終止
sleep 2
kill -2 pid     # 若沒反應，可補發 SIGINT 看看
sleep 2
kill -9 pid     # 最後手段，強制結束
````


# 程式內
在主要process啟動後，每個gateway跑在不同的thread執行(看語言)，所以應該在main thread中，
設置signal捕捉SIGTERM和SIGINT，後續通知給其他thread，讓他們優雅關閉

# 何謂優雅關閉?
1. 在thread中，有等待main thread的停止通知
2. 收到通知後，將結束前的工作完成，可能是套件的stop調用或是自訂的業務邏輯
