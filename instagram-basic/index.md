# testing instagram basic api on localhost

<!--more-->

因 Meta Develop 開發政策，強制所有的用戶端 OAuth 都要使用 https 的 URL 才能被 Meta 認定為有效的 OAuth 重新導向 URL ，這會導致我們在地端測試時，會需要 https 的 URL 供我們做測試，這邊我們使用 ngrok 來產生一組 https 的 URL ，並帶著各位如何在地端使用 ngrok 來測試 instagram basic api  

## 建立 instagram 的應用程式

### 在 Meta 開發人員工具創建應用程式

到 [Meta for Developers](https://developers.facebook.com/?no_redirect=1) 建立應用程式  

![create application](https://imgur.com/SqaqiXa.png)  

再來需要選擇應用程式的類型及填寫名稱，這邊需要注意類型的選擇會導致該應用程式可使用的產品、權限及功能，[詳情可以參考官方文件](https://developers.facebook.com/docs/development/create-an-app/app-dashboard/app-types)  

![app type](https://imgur.com/1xwkv4U.png)  

點選 instagram 基本顯示的設定  

![set up](https://imgur.com/dIVtYPY.png)  

在 instagram 基本顯示 > 基本顯示 中建立新的應用程式  

![create instagram basic app](https://imgur.com/FPtEXIA.png)  

### 新增 instagram 測試人員

完成上面的步驟後，點選左邊選項中的 角色 > instagram 測試人員的按鈕，輸入你要用來測試的 instagram 帳號  

![add test](https://imgur.com/7qNpQSo.png)  

這時登入網頁版的 instagram ，在 設定 > 應用程式和網站 > 測試員邀請 ，會發現應用程式的邀請確認，點選接受就成功成為應用程式的測試人員  

![allow test](https://imgur.com/BDULvxO.png)  

## 建立一個 web 服務

這邊以 golang 搭建一個簡單的網頁並把服務放在 `80 port`  

```go
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/ping", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "pong")
    })

    log.Fatal(http.ListenAndServe(":80", nil))
}
```  

把服務起起來並確認頁面有正常顯示，以上面範例，在瀏覽器
載入 `localhost:80/ping` 會出現以下圖例  

![localhost:80/ping](https://imgur.com/KXqoinl.png)  

## 設定 ngrok

### 安裝 ngrok

先到 [ngrok 官網](https://ngrok.com/download) 下載對應的版本並註冊登入會員，初次安裝需先驗證 ngrok 的 agent，到 Your Authtoken 執行 Command Line 的指令，會將 ngrok 的 Authtoken 保存在你的電腦，授於 agent 使用 ngrok 所有功能的權限  

![ngrok](https://imgur.com/NE6GzRh.png)  

完成後執行 `$ ngrok http {port}` ，就可以將 `localhost:{port}` 的服務對外開放，因為我服務使用 `80 port` 的關係，所以我要執行 `$ ngrok http 80` ，接著會看到以下圖片的樣子，就代表成功將地端的 `80 port` 對外開放，而紅色框框的 https 網址為你地端服務對外的網址，這時其他人就可以透過該網址連到你的網頁  

![command line](https://imgur.com/2PikB8H.png)  

### 設定應用程式

回到 Meta for Developers 左邊選項的 instagram 基本顯示 > 基本顯示 去設定 `用戶端 OAuth 設定`、`取消授權`、`資料刪除要求`，在圖中的三個欄位填入 ngrok 產生的 https 網址  

![ngrok http 80](https://imgur.com/gtl7jDv.png)  

## 取得 instagram basic api 的 token

### 驗證測試人員

建構授權的視窗網址，`{app-id}` 為 instagram 基本顯示 > 基本顯示 > instagram 應用程式編號， `{redirect-uri}` 為`有效的 OAuth 重新導向 URI`的網址（*網址必須完全相同*）  

```url
https://api.instagram.com/oauth/authorize
  ?client_id={app-id}
  &redirect_uri={redirect-uri}
  &scope=user_profile,user_media
  &response_type=code
```  

以我們剛剛產生的 https 網址為例  

```url
https://api.instagram.com/oauth/authorize
  ?client_id=135421602932815
  &redirect_uri=https://6e02-220-135-89-54.jp.ngrok.io/ping
  &scope=user_profile,user_media
  &response_type=code
```  

在瀏覽器載入網址後，會出現應用程式要求權限的說明  

![要求權限](https://imgur.com/LPaPfJd.png)  

點選允許後，會跳轉到你在應用程式設定的`重新導向 URI`的頁面，這時請觀察該網頁的網址  

![redirect url](https://imgur.com/W2obeKW.png)  

網址中的 `code` 到 `#_` 之間為測試人員的授權碼  

```url
https://6e02-220-135-89-54.jp.ngrok.io/ping?code=AQAODGH_DePavKuPRK0UKy5nMnSBYG_EEjOcodAUC5Q-wVS0uP5SDjFNgNWdw2a4plybIndmH0CuPKvx5k-IDNlVh4sX2qjao1LKGRykYNvpn_6gTtYWYPDrcJR0Lt5JrWHdo5Nj70cBg6VMDeW-vyzEAXzgnfgUTg3FeOfdJ2vlBenmCsMqwCrOS2SSQzMLEGK1B7osZz7W8ckAGkiyYZUGTOTlIDDuy4UOpLJ_PJd_4g#_
```  

### 將授權碼換成 token

將 `{app-id}` 、 `{app-secret}` 、 `{redirect-uri}` 、 `{code}` 換成你的 instagram 應用程式編號、 instagram 應用程式密鑰 、重新導向 URI 、授權碼，並輸入指令傳送請求  

```curl
curl -X POST https://api.instagram.com/oauth/access_token \
-F client_id={app-id} \
-F client_secret={app-secret} \
-F grant_type=authorization_code \
-F redirect_uri={redirect-uri} \
-F code={code}
```  

成功時，會回傳以下 json 格式  

```json
{ "access_token": "IGQVJV...", "user_id": 178414005245354752 }
```  

例如：  

![terminal](https://imgur.com/5m0ZSpv.png)  

這時候就可以用 `access_token` 和 `user_id` 去串接 instagram basic api 了  

## Reference

[Meta for Developers](https://developers.facebook.com/docs/instagram-basic-display-api/getting-started)  
[產品開發 | 如何在Localhost實作與測試FB Login SDK功能](https://medium.com/alexchanglife/%E7%94%A2%E5%93%81%E9%96%8B%E7%99%BC-%E5%A6%82%E4%BD%95%E5%9C%A8localhost%E6%B8%AC%E8%A9%A6fb-login-sdk%E5%8A%9F%E8%83%BD-mac-5098973b9901)  
