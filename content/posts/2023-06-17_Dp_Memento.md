---
title: "設計模式-備忘錄模式"
date: 2023-06-17T14:06:36+08:00
tags: ["知識",'行為模式']
categories: ["程式"]
series: ["設計模式"]
draft: false
---

# Memento-備忘錄模式

## 分類
行為模式-Behavioral Patterns

## 主要角色
Originator、Memento、Caretaker

## 目的
將Originator的部分狀態做儲存，可以做狀態回朔

## 如何互動
在Originator內部，將狀態記錄到Memento生成物件，交由Caretaker管理，在需要的時候，
從Caretaker拿回Memento回復到之前紀錄

## GitHub練習
https://github.com/hanksky12/DesignPatterns/tree/main/Memento

## 實務上
未運用過，沒經驗可分享

