---
title: "用python寄信！使用Sendgrid API"
date: 2021-07-20T01:19:01+08:00
draft: false
---

在python內要寄信的話有幾種方式，最廣為人知的應該是smtplib。但最近我將網站上線至GCP的時候發現平台把smpt的port給封鎖，於是google了一下發現要使用第三方的api服務才有辦法寄信，便找到的Twilio的sendgrid。就順便寫一篇文章來教大家怎麼用吧！

首先到[Sendgrid](https://sendgrid.com/pricing/)的pricing頁面瞭解一下方案，可以得知免費方案上限為一天100封email，雖說看起來不多，但其實對個人來說也是夠用了。那就辦了帳號繼續下一步吧！

    Outline
    *申請帳號，驗證Domain
    *設定Sender
    *創建API
    *Python實作
    *參考文件

### 申請帳號，驗證Domain

創完帳號且登入後會進到Dashboard的畫面。在左下角找到Settings -> Sender Authentication會進到驗證發信郵件的部分。

![](https://cdn-images-1.medium.com/max/2000/1*goK_r-vIRCq3JkAIOypK0w.jpeg)
*圖示連結*

進入畫面後你會看到以下的畫面，這邊就需要你有一個自己的Domain來做相關的DNS設定。

![](https://cdn-images-1.medium.com/max/3392/1*GLpB8DiOpjrs5Bi0LFU4jw.jpeg)
*網域設定畫面，這邊因為我已經驗證了所以畫面右方會有相關設定過的紀錄。*

點擊Authenticate Your Domain，輸入你的Domain代管服務提供者，我自己是用cloudflare。

![](https://cdn-images-1.medium.com/max/3396/1*irBZajR3omFLxSeUcGRlbw.jpeg)
*設定Domain*

下一個畫面就輸入你的Domain就好。

接下來就要把sendgrid提供的CNAME紀錄加到你的DNS設定裡。這邊其實官網寫得蠻清楚的，就按照他給的複製貼上即可。

![](https://cdn-images-1.medium.com/max/3416/1*tujIB14meqmieYf7f-9Rpw.jpeg)
*DNS setting*

新增完之後回到Sendgird，選擇驗證，如果都設定好就會看到成功訊息囉。

![](https://cdn-images-1.medium.com/max/3306/1*_fPat08HoLQh77HIsEuYHA.jpeg)
*驗證成功嚕*

接下來就繼續設定Sender。

### 設定Sender

回到剛剛的Sender Authentication頁面選擇Verify a Single Sender，按照表格輸入資訊

要注意這邊的from name 就是這個信箱寄出時，收信人那邊顯示的名子。from email address就是之後寄信的郵件（後面要用剛剛設定的domain），下面地址就隨便填，最後幫這個信箱取一個暱稱就可以了。

（至於reply to 那邊我自己也沒有太深入的研究，但目前是收不到回信的）

![](https://cdn-images-1.medium.com/max/2000/1*XHhOfI22Sb5kZG3SM2oUvQ.jpeg)
*新增Sender*

接下來要創建一個API key。

### 創建API

到左邊導覽列的地方選擇Email API -> Integration Guide。會看到這個畫面：

![](https://cdn-images-1.medium.com/max/2124/1*jOg7l7B-jOZSbLTK8SaEjQ.jpeg)
*API Key 初始介面*

那一開始有提到這次的教學是要使用sendgrid的api，所以就不使用smtp了。

點選web api之後會要你選擇你的語言，我們這邊就選python。

下一個頁面會要你打一個名子，這個名子就是供你自己辨認而已，打完後按create會看到這個畫面：

![](https://cdn-images-1.medium.com/max/2230/1*g_ImzGdu3DEUKHut7p-Y4A.jpeg)

最後要從python發一封測是信給sendgrid來完成整個api的啟用。

到這邊Sendgrid的部分就大致結束了，接下來要進到python的部分了。

但這個畫面先留著，等等還會用到。

### python實作

首先直接到你的IDE新增一個send.py檔案，然後在terminal輸入

    pip install sendgrid

來安裝sendgrid套件。

接著到剛剛新增的send.py檔輸入以下內容

    # using SendGrid's Python Library# [https://github.com/sendgrid/sendgrid-python](https://github.com/sendgrid/sendgrid-python)
    from sendgrid import SendGridAPIClient
    from sendgrid.helpers.mail import Mail 
    message = Mail(
        from_email='剛剛創建的sender',    
        to_emails='用來收信的信箱',
        subject='Sending with Twilio SendGrid is Fun',
        html_content='<strong>and easy to do anywhere, even with Python</strong>')
    try:
        sg = SendGridAPIClient('你的API KEY')
        response = sg.send(message)
        print(response.status_code)
        print(response.body)
        print(response.headers)
    except Exception as e:
        print(e)
    # 有稍微修改官方給的範例

像以下

![](https://cdn-images-1.medium.com/max/2796/1*tdLILelZha6fkrVSIS8CJQ.jpeg)

輸入完成後回到剛剛的sendgrid頁面，點選 Verify Integration。這時我們需要從python寄一封測試信，所以直接執行剛剛的send.py檔案。

這樣應該就可以成功透過sendgrid的寄出一封信了。

可以到自己的收信信箱看有沒有收到

![](https://cdn-images-1.medium.com/max/3186/1*xp9ANgV33k9WiHsci8BasQ.jpeg)

![](https://cdn-images-1.medium.com/max/2000/1*GQO3bpBEVBJI1NXQi-ZwJQ.jpeg)

到這邊我們就完成全部的設定了。

以上是這次的分享，有任何問題都歡迎留言指教，謝謝 ！

### 參考文件

1. [sendgrid的github](https://github.com/sendgrid/sendgrid-python)
