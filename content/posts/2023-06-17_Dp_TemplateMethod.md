---
title: "設計模式-樣板模式"
date: 2023-06-17T14:06:36+08:00
tags: ["知識",'行為模式']
categories: ["程式"]
series: ["設計模式"]
draft: false
---

# Template Method-樣板模式

## 分類
行為模式-Behavioral Patterns

## 主要角色
Abstract Class、Concrete Class1、Concrete Class2

## 目的
提出共同流程到Abstract Class，避免重複程式碼

## 如何互動
在Abstract Class定義整個演算法流程，並實現部分方法，將特定方法留到Concrete Class再去實作

## GitHub練習
https://github.com/hanksky12/DesignPatterns/tree/main/TemplateMethod

## 實務上
在寫程式中，沒特別學習，自然而然就實現的模式，類似於小學數學的提出最大公因數
