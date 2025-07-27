---
title: "Process & Thread [二]"
date: 2024-07-25T21:27:34+08:00
tags: ["知識", "process","thread",'python','Coroutines']
categories: ["程式"]
series: ["計算機基礎"]
draft: false
---

## Python Global Interpreter Lock
一個GIL鎖，主要是在Cpython的解釋器上，在每個進程生成時，會有一個解釋器負責解釋
，對多線程有一把鎖，限制同時間只能有一個線程工作，用計數方式，釋放後，所有線程重新競爭GIL，或是遇到io主動釋放


# 多核心多線程任務
## CPU-bound
### 單進程
CPU-bound任務，想要節省資源，基本會用單進程，但這個解釋器，不管是多核心，或多線程，一次只能讓一個線程工作

### 例子
類似掃地或擦桌子這種任務，必須持續的做，不做就沒進度，但工作到一半，又會被GIL叫停，回來搶鎖再繼續工作
![](/web_img/process_and_thread6.jpg)

### 多進程
可以解決CPU-bound的問題，因為讓每個進程有自己的鎖
缺點:代價是資源的消耗，以及要另外創建資源來做跨進程的溝通

### 例子
![](/web_img/process_and_thread7.jpg)

## I/O-bound
在io任務中，因GIL遇到io會主動釋放，所以反而適用


# 協程
在python中雖然多協程，可以最大化降低開銷，但要主動去做切換，而多線程的切換，是交給電腦處理，
所以在I/O-bound任務如果太複雜，也可以用多線程來實作，更多時候，可以視情況混合使用


# 結論:要高性能處理任務

CPU-bound任務:多進程

I/O-bound任務:多協程，多線程
