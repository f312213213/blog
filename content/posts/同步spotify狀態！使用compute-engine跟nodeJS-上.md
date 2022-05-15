---
title: "同步spotify狀態！使用compute Engine跟nodeJS - 上"
date: 2022-05-03T23:26:36+08:00
draft: false
url: /posts/sync-spotify-with-web-1
tags: [javascript, web, back-end, project]
ShowToc: true
---
***
最近在規劃新的個人網站，正好看到 [Lee Robinson](https://leerob.io/) 的網站有做這個功能，想說我也來做做看，以下跟大家分享實作過程。
***

## 要如何得知最新的聽歌狀態？

### Spotify API
研究了一下後發現 Spotify 有提供這支 [API](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-the-users-currently-playing-track) 可以獲取最新的狀態。
再繼續看可以得知這支 API 需要用 accessToken 做身份的認證，而且時效只有一個小時（3600秒），所以如果要放到網站上勢必要做成可以自動 renew 的 api 來 call。

### 取得 accessToken
實作自動 renew 之前，要先來研究怎麼取得 accessToken ，[官方 doc](https://developer.spotify.com/documentation/general/guides/authorization/) 的解說其實挺清楚的。

#### 1. 申請一個 SPOTIFY APP 
要有 accessToken 要先有一個 spotify app，這部分比較簡單，就不多做介紹。 
詳細說明可以看[這裡](https://developer.spotify.com/documentation/general/guides/authorization/app-settings/)

#### 2. 用自己的 spotify 帳號登入這個 app
這邊就需要有一個後端伺服器來配合做登入的步驟。那因為官方範例用 nodeJS 我也就用 nodeJS 來實作了。

不囉唆，直接上 code。
```javascript
import express from "express";
import * as querystring from "querystring";

const clientId = '從 app dashboard 拿到的 clientId';
const redirectUri = 'http://localhost:3000/callback'
const port = 3000

const app = express()

app.listen(port, () => {})

app.get('/login', function(req, res) {
  const scope = 'user-read-currently-playing'
  return res.redirect('https://accounts.spotify.com/authorize?' +
      querystring.stringify({
        response_type: 'code',
        client_id: clientId,
        scope: scope,
        redirect_uri: redirectUri,
        state: '1234567888812345'
      }))
})

app.get('/callback', function(req, res) {
  const error = req.query.code || null
  const code = req.query.code || null
  const state = req.query.state || null

  if (error || !state || !code){
    return  res.send(error)
  }
  res.send('登入成功')
})
```
簡單說明一下，上面會開啟一個簡單的後端，有 `/login` 跟 `/callback` 兩個 entry。

從瀏覽器進入`/login`後會 redirect 到 spotify 的登入頁面（登入你剛剛建立的 app），登入完成後會 redirect 回 `/callback`。

redirect 回來的時候如果沒出問題的話會在 url query 裡會有 code 這個參數，這是拿來申請 accessToken 的關鍵。

#### 3. 把 `/callback` 拿到的 code 換成 accessToken
如果第二步驟完成的話現在手邊應該會有
1. spotify app 的 clientId
2. spotify app 的 clientSecret
3. `/callback` 的 code

確認沒問題後我們就來更改 `/callback` 的程式碼！

一樣上 code ！
```javascript
const getRefreshAndAccessToken = async (code) => {
  const params = new URLSearchParams();

  params.append('client_id', clientId)
  params.append('client_secret', clientSecret)
  params.append('grant_type', 'authorization_code')
  params.append('code', code);
  params.append('redirect_uri', redirectUri)
  try{
    const response = await axios({
      url: 'https://accounts.spotify.com/api/token',
      method: 'post',
      params
    });
    accessToken = response.data.access_token
    refreshToken = response.data.refresh_token
  } catch (e) {
    console.error(e)
  }
}

app.get('/callback', function(req, res) {
  const error = req.query.code || null
  const code = req.query.code || null
  const state = req.query.state || null

  if (error || !state || !code){
    return  res.send(error)
  }
  getRefreshAndAccessToken(code)
})
// 這兩個資料看要怎麼存都可以，因為程式不複雜，我是直接存成全域變數
let accessToken = null
let refreshToken = null
```
簡單講解一下，我把取得 token 的過程包成一個 getRefreshAndAccessToken function，裡面使用 axios 發 post request 給 spotify，如果一切正常 spotify 會回傳一個像下面的東西
```json
{
   "access_token": "NgCXRK...MzYjw",
   "token_type": "Bearer",
   "scope": "user-read-currently-playing",
   "expires_in": 3600,
   "refresh_token": "NgAagA...Um_SHo"
}
```
裡面重要的是 accessToken 跟 refreshToken，有了這兩個我們就可以繼續步驟了。但好奇的你一定想要試看看 accessToken 能不能用，所以先附上程式碼。
```javascript
const response = await axios({
      url: 'https://api.spotify.com/v1/me/player/currently-playing?market=tw',
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    })

// 如果你正在聽歌的話，會 log 出那首歌的資訊。
console.log(response.data)
```
到這邊就已經大致完成了！

### 自動 refresh accessToken
雖然可以取得資訊，但問題來了。spotify 回傳的資料裡明定了這個 accessToken 效期只有 3600 秒，所以一個小時後就自動失效，勢必要有方法讓程式有新的 accessToken 取用，於是我就想出了這個方法：
> 在每次取得聆聽資料前都 refresh 一次 token

程式碼如下
```javascript
app.get('/current-playing', (req, res) => {
  if (!accessToken) {
    return res.send('no access token')
  }
  getCurrentSong()
      .then(() => {
        return  res.send(data)
      })
})


const getCurrentSong = async () => {
  const params = new URLSearchParams();

  params.append('client_id', clientId)
  params.append('client_secret', clientSecret)
  params.append('grant_type', 'refresh_token');
  params.append('refresh_token', refreshToken);
  try {
    const responseForRenew = await axios({
      url: 'https://accounts.spotify.com/api/token',
      method: 'post',
      params
    });
    const responseForData = await axios({
      url: 'https://api.spotify.com/v1/me/player/currently-playing?market=tw',
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${responseForRenew.data.access_token}`
      }
    })
    if (responseForData.data) {
      return data = responseForData.data
    }
    data = {is_playing: false}
  } catch (e) {
    console.error(e.response.status)
  }
}
```

這樣我們每次進到 `/current-playing` 頁面的時候，就會先 refresh 一次 accessToken，在取得聽歌的資訊，確保每次的 request 帶的 token 都是有效力的。

***
篇幅原因分成上下兩篇，下一篇會敘述我如何將這個專案部署上雲端，並成功在網站裡顯示資訊！
