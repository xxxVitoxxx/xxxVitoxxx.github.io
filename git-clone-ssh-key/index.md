# Git Clone With SSH Key

建立 ssh key 和 git clone 、 git push 教學
<!--more-->

GitHub 提供了兩種連線方式，分別是https與ssh，兩者最大的差別在於ssh可以立刻上傳，不需要輸入密碼，且安全性高。  

## 如何透過ssh連結GitHub

第一步：依作業系統下載對應的[Git](https://git-scm.com/downloads)(本示範系統是macos)  

![download git](https://imgur.com/uTG4NVn.png)  
(或透過Homebrew download: $ brew install git)  

第二步：產生金鑰  

`$ ssh-keygen -t rsa -b 4096 -C ' your id '`  

用ssh-keygen建立金鑰

![ssh keygen](https://imgur.com/toi8hAi.png)  

系統會請你輸入通行碼(若沒輸入直接回車，之後clone就不用輸入密碼)  

![pwd](https://imgur.com/VtHmiSV.png)  

再輸入一次通行碼後會顯示ssh的公鑰及私鑰路徑檔名  

![show_key](https://imgur.com/KTTN3ps.png)  

第三步：  

將id_rsa.pub(公鑰)的內容新增到GitHub的Settings的SSH keys  

![setting](https://imgur.com/QE0Lnr3.png)  
(公鑰的內容)  

`$ cat ./.ssh/id_rsa.pub`  

![key_content](https://imgur.com/jkXhMSp.png)  

![setting2](https://imgur.com/uKUHESf.png)  

點擊Add就大功告成！！！  

![ssh key](https://imgur.com/SsQRU5c.png)  

---

## clone

第一步：先在終端機進到你要建立專案的地方。  

`$ cd 要建立專案的地方`  

![create project](https://imgur.com/t46u7Mb.png)  

第二步：clone 專案到本機  

`$ get clone '專案ssh網址'`  

![git clone](https://imgur.com/Jcd7bcQ.png)

第三部：輸入通行碼(若當初有設定的話)  

![enter pwd](https://imgur.com/6RPh4xX.png)  

下圖表示成功將專案裡的檔案clone到主機  

![success](https://imgur.com/5N86kIz.png)  

clone下來的資料夾名稱會是你的GitHub的repository名稱  

![clone success](https://imgur.com/3oMawlR.png)  

---

## push

第一步：示範新增一個名為README.md的檔案  

![README](https://imgur.com/EM9vc7w.png)

並將新檔案丟到專案(xxxVitoxxx.github.io)的資料夾裡，並cd到專案的資料夾  

![new file](https://imgur.com/I5ry1TZ.png)  

第二步：確認專案的資料夾做了哪些更動  

`$ git status`  
(紅色字體代表有更改過的檔案)  

![git status](https://imgur.com/MbZff9e.png)  

第三步：將新增的檔案加到Git版本控管  

`$ git add .`  
(注意！！！這邊只是將專案資料夾的所有檔案加到Git工作目錄的索引中，尚未加到Git版本庫)。  

可以再輸入一次 $ git status 確認專案資料夾的檔案是否已成功更新(綠色字體代表成功更新檔案)  

![git status](https://imgur.com/lCSTgYv.png)  

第四步：為修改記錄命名一個名稱  

`$ git commit -m "commit message"`  

這邊將第一次修改記錄命名為'first commit'  

![git commit](https://imgur.com/WH006jr.png)  

`$ git log`  

可以查看該專案的修改紀錄  

![git log](https://imgur.com/UySCdlS.png)

第五步：  

`$ git push`  

將剛剛新增的檔案推到GitHub上  

![git push](https://imgur.com/s42oNdu.png)  

