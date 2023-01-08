# 透過 pgAdmin 操作 Heroku PostgreSQL Database

<!--more-->

## 軟體

- pgAdmin  
- Heroku PostgreSQL project  
  
## 建立 postgres

登入Heroku，進到有Postgre專案的Resource，點擊Heroku Postgres  

![create postgres](https://imgur.com/aYB1jra.png)  

點選settings，我們需要 *'Host'*、*'Database'*、*'User'*、*'Port'*、*'Password'* 的資訊  

![setting postgres](https://imgur.com/zkv77Wh.png)  

## 建立與Heroku PostgreSQL資料庫的連結設定

開啟pgAdmin，在左上角的Servers點擊右鍵 →Create →Server…  

![establish connection](https://imgur.com/3GVZO9g.png)  

在General標簽頁中，Name 欄位輸入可識別的名稱  

![general](https://imgur.com/w420uUI.png)  

在*Connection*標簽頁中  
*Host name/address* 對應 Heroku Postgres的Host  
*Port* 對應 Heroku Postgres的Port  
*Maintenance database* 對應 Heroku Postgres的Database  
*Username* 對應 Heroku Postgres的User  
*Password* 對應 Heroku Postgres的Password  
*Save password* 勾選起來  

![setting](https://imgur.com/FxuMWJs.png)  

在*SSL*標簽頁中，*SSLmode* 選擇Allow  
關於其他選項可參考[文件](https://www.postgresql.org/docs/9.1/libpq-ssl.html)  

![allow](https://imgur.com/1dQ95S4.png)  

在*Advanced*標簽頁中  
*DB restriction*對應 Heroku Postgres的Database  
點擊下方 *Save*存儲  

![advanced](https://imgur.com/XS7wi5M.png)  

存儲後打開的畫面可以看到在Heroku PostgreSQL的所有資料表都在Tables標籤下  

## 利用SQL語法來查詢資料庫的資料

在左邊的框框點擊右鍵選擇*Query Tool*  

![query tool](https://imgur.com/5AlEfre.png)  

輸入SQL語法點擊右上方的三角形圖案執行命令  

![execute](https://imgur.com/2j5dc8u.png)  

