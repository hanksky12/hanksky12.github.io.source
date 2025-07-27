---
title: "Python Flask 架構"
date: 2023-09-30T15:31:47+08:00
tags: ["知識","Python"]
categories: ["程式"]
draft: false
---

## flask網站開發
 之前讀過這本O'Reilly的 Flask Web Development(2 Ed.)，就很喜歡裡面建構大型應用的方式，可擴展性很高，

 雖說flask主打輕巧易用，但有意識的建構下，也可慢慢建構成與Django相抗衡的網站，且套件和建構的選用也更自由，

 例如 如果想把網站以DDD方式建構，Django就完全做不到，

 為了真正做到好維護，方便以後擴展性的網站 ，也去參考其他靜態語言常用框架的分層結構，發現java或C#(尤其是C#)，分層真的多，
 引入各種值物件的定義與介面，來解耦和各種分層
 
 若以python想去模擬靜態語言的web框架方式，會去違背我最喜歡的python精神=>「優雅」、「明確」、「簡單」，

 最後決定以廣義分層架構，來實作後端api，既不會過多的分層，但也適度的做出分類


https://github.com/hanksky12/scalability_flask/tree/main/backend

- app
    - config
    - const
    - controller
      - api
        - topic1
        - topic2
      - main
    - models
    - schemas
      - fields
      - schemas
    - services
    - exceptions
    - interface
    - utils
      - web
- run_app.py
