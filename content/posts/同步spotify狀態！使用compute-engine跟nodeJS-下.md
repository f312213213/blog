---
title: "同步spotify狀態！使用compute Engine跟nodeJS - 下"
date: 2022-05-15T21:17:12+08:00
draft: false
url: /posts/sync-spotify-with-web-2
tags: [javascript, web, back-end, project]
ShowToc: true
---
***
最近在規劃新的個人網站，正好看到 [Lee Robinson](https://leerob.io/) 的網站有做這個功能，想說我也來做做看，以下跟大家分享實作過程。
***

上一篇我們已經能在 local 能夠取得聆聽資訊了，這篇我們要來部署到 compute engine 上
（不使用 cloud function 的原因是我懶得再改程式碼了...不然應該可以輕量化一點的）

## 移植到 compute engine 上
首先要先創建一個 VM，到 [這裡](https://console.cloud.google.com/compute/instances) 創建。
可以看到下面的樣子
![](/images/compute-spotify/Screen%20Shot%202022-05-15%20at%2020.57.37.png)
名字可以照喜好填，但如果要免費使用的話機器種類要照著圖片的選取，並且開機磁碟要選擇『標準永久磁碟』，並且最高是30G，像下面那樣。
![](/images/compute-spotify/開機磁碟.png)
正確的話右邊估價的側邊欄會長這樣子
![](/images/compute-spotify/估價.png)
雖然估價感覺要付錢，但結算的時候系統會自動折抵掉。

接下來等個三分鐘左右就可以看到機器跑好了，可以看到下面的畫面。
![](/images/compute-spotify/創建完成.png)
點右邊那個 ssh 就可以用 GCP 的 ssh 直接連線這台 VM。

接著就要先安裝 node 跟 npm。
```shell
sudo apt-get update
sudo apt-get install nodejs npm
```
接著可以創建一個新資料夾，並在裡面開啟新的 npm 專案
```shell
mkdir "your_project_name"
cd "your_project_name"
npm init #然後接著照他的提示輸入資訊
```
好了之後就可以看到這個資料夾裡有一個 package.json 了，我們接著新增一個 server.js 然後把上一篇的程式碼貼近去。
同時也要記得裝 express
```shell
echo server.js
npm express
```

貼近去之後就可以執行啦
```shell
npm run start
```

## 設定 Nginx 伺服器
接著我們要讓外面能夠連結我們的 vm，所以用 Nginx 來當作入口。
```shell
sudo apt-get install -y nginx
cd /etc/nginx/sites-available
sudo nano default
```
修改成下面這樣
```
server {
  listen 80;
  server_name YOUR_SERVERS_IP_ADDRESS;

  location / {
    proxy_pass "http://127.0.0.1:8080";
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_cache_bypass $http_upgrade;
  }
}
```
接著重開服務
```shell
sudo service nginx restart
```

然後你就可以用 VM 的 ip 加上自己設定的路徑來取得現在的聽歌資訊，像下圖：
![](/images/compute-spotify/連線成功.png)

這樣就大功告成啦！！！

## 後續學習
1. 串接到自己的個人網站。
2. 可以研究怎麼把連線升級為 https。

## 資料來源
1. [GCP 免費額度](https://cloud.google.com/free)
2. [Nginx 伺服器連線](https://javascript.plainenglish.io/deploy-a-node-js-server-using-google-cloud-compute-engine-87268919de20)
3. [Lee Robinson](https://leerob.io/)



