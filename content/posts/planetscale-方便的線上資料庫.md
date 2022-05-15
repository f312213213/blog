---
title: "Planetscale 方便的線上資料庫"
date: 2022-05-12T16:56:28+08:00
draft: false
url: /posts/planetscale-easy-online-database
tags: [web, back-end]
---

最近在做學校資料庫課程的作業時需要有一個實際可用的資料庫，原本在 compute engine 上有開了一個 mysql ，但覺得連線速度太慢（免費方案只能在美國）所以發現了 Planetscale 這個線上資料庫。

## Planetscale簡介
Planetscale 是一個資料庫提供者，原本我們要用到資料庫時要自己有個 server 然後在上面跑 mysql 或其他資料庫。

Planetscale 就直接幫你完成這些步驟，點一點按鈕就給你一個資料庫的帳號密碼，直接可以連線。

## 怎麼使用
直接到 [Planetscale的官網](https://planetscale.com/) 註冊後到 Dashboard 裡創建一個新的庫，會有下面畫面。
![創建](/images/Screen%20Shot%202022-05-12%20at%2017.03.49.png)

輸入資料庫名字、區域後等他跑完就可以直接使用了。到右邊有個 connect 按鈕點下去會有彈窗給你相對應的連接指示。

這邊要注意要把密碼記下來，網站上的密碼只要連接過一次就不會顯示了。

連接的話用的是 mysql ，其他照著他給的資料輸入就可以連線（port 不用填）。
> 過程中碰到了一個小問題，planetscale 要求要 ssl 連線
> 
>可以到 [這裡](https://docs.planetscale.com/concepts/secure-connections) 來找相對應的 ca 路徑，填進你連線的參數裡。


## 酷炫功能
玩一玩網站發現裡面有 console 可以直接使用，覺得挺炫砲。
![直接有 console](/images/Screen%20Shot%202022-05-13%20at%2014.42.44.png)

