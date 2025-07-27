---
title: "Process & Thread [一]"
date: 2024-07-25T21:27:34+08:00
tags: ["知識", "process","thread",'python','Coroutines']
categories: ["程式"]
series: ["計算機基礎"]
draft: false
---

## 前言
之前在工作上，已經運用多線程，協程，但對於底層運行，還不到通透的地步，重新整理知識

# 基礎知識

## Program 程式
用IDE寫好的code，沒有執行，只是一份沒啟動的檔案

## Process 進程
管理資源的單位

當啟動Program之後，讀入記憶體，cpu開始運行，這份程式會在電腦取得一份資源

## Thread 線程
執行工作的單位(fake)

在1個Process內，可能有1~n個Thread，最少1個n個Thread，否則就沒人來執行Process

## CPU
執行工作的單位(real)

如果一次只能執行1個Thread，電腦就很難快起來，所以cpu將自己的時間切片，反覆在多個線程間切換，造成多工假象

## 例子:
一家早餐店Process，至少要有一個虛擬員工Thread，雖然表面上是員工，但其實老闆cpu是要親自下來執行，
當老闆cpu要從虛擬員工Thread1切換到虛擬員工Thread2，虛擬員工Thread1紀錄要暫時封存，
並把虛擬員工Thread2的紀錄重新讀取，才能知道剛剛工作到哪裡
![](/web_img/process_and_thread1.jpg)

## Multiprocessing 多進程
同樣的程式，想要加速，使用多進程會遇到資源的浪費，且切換效率很差，實務上盡量以多線程取代多進程

好處是:資源絕對獨立，一個崩潰不會影響另一個

## 例子:
為了讓做三明治產線加速，而另外開一家早餐店，老闆cpu要在兩家早餐店跑來跑去，很不划算
![](/web_img/process_and_thread2.jpg)

## Multithreading 多線程
在同一個Process，使用多個線程，造成的問題是資源共享，為何資源共享是問題?

好處是:會重複使用的資源可以放到共享，全域變數，靜態變數，減少開銷

壞處是:也因這樣可能造成dead lock和race condition，一個線程沒處理好，會讓整個Process停住

但只要注意好這些細節，在線程的切換上速度會比進程切換快

## 例子:
同一家早餐店，烤麵包機器可以共享，但原料麵包總量也會共享，虛擬員工Thread1拿了一個，也會造成虛擬員工Thread2的原料麵包總量減少
![](/web_img/process_and_thread3.jpg)


# Concurrent 併發
同一時間，只有一個任務在執行，但從整體來看，多個任務被細分，輪流執行一部分，造成多任務一起處理的假象，
這就是併發，單核心多線程的工作方式就是併發

# MultiCPU 多核心 
真正會讓速度增加，只有增加多核心，多一個老闆Cpu，才能同一時間有第二個人一起辦公，cpu的分派，是由os負責，
並沒有一個cpu負責特定的Process或Thread，且2個cpu，也只能造成2個Thread同時執行，若有第3個，也會有部分併發

## 例子:
多一個老闆Cpu，可以真正的增加工作效率
![](/web_img/process_and_thread4.jpg)

# Parallel 並行
同一時間，真正有多個任務同時執行，才能稱作並行，只有多核心cpu才能做到

# CPU密集型 CPU-bound
必須靠cpu算力來決定任務速度的上限，單純的計算

# I/O密集型 I/O-bound
任務常常與cpu以外的硬體溝通，造成cpu閒置，主因還是cpu與其他硬體速度巨大落差，
任務速度的上限被等待回應的時間影響 ，例如:檔案存取，網路請求

# Coroutines 協程
在1個Thread內，建立多個Coroutines來做到任務分工，協程的切換開銷，又比Thread切換更小，
更適用於多任務的切換，但畢竟是在同一個Thread，所以是採用併發方式，適用於io bound，
遇到io需等待，就切換到其他任務，當所有任務都處於等待時，就會一直loop，
等到某個任務有回應可以繼續執行

線程的切換由cpu決定，協程的切換由使用者來決定(在遇到io時，切換Coroutines)，所以可以自己控制
關鍵資源，避免同一個Thread內會遇到的共用資源競爭問題

![](/web_img/process_and_thread5.jpg)

# 結論:要高性能處理任務
CPU-bound任務:多線程開發(多核心)(Parallel) 

I/O-bound任務:多協程開發(Concurrent)

多進程還是有它的好處，每個都是獨立不互相影響，一個崩潰了，其他能正常運行也是可以看開發目的做使用