# ECR Private Registry Authentication

ECR(Elastic Container Registry) 是一種安全、可擴充且可靠的 AWS 託管容器映像服務，每個 AWS 帳號都有一個預設的 Private ECR Registry ，本文會介紹在 EC2 環境中如何向 Private Registry 驗證 Docker 用戶端的身份，並從 Private Registry push/pull 映像檔。  

<!--more-->

## Introduction

前陣子重新建置測試環境，卻發現 EC2 時常會拉不到新的映像檔，詳細了解後，發現是因為我們取得的 token 是有時效性的，導致當 token 過期就無法從 Private Registry 拉取映像檔，這邊介紹了幾種取得驗證 token 的方法並教你如何避免發生 token 過期的問題。  

## 如何在 EC2 上從 Private Registry push/pull 映像檔

在 EC2 有 ECR 相關 policy 且已有安裝 docker 的環境下，有以下三種方法可以達到目的。  
1. AWS CLI  
2. HTTP API  
3. Amazon ECR Credential Helper  

### AWS CLI

透過 AWS CLI 的 [get-login-password](https://docs.aws.amazon.com/cli/latest/reference/ecr/get-login-password.html) 指令將身份驗證的 token 傳遞給 `docker login` 指令，並指定要驗證的 ECR URI 。  

```aws ecr get-login-password | docker login --username AWS --password-stdin {account_id}.dkr.ecr.ap-northeast-1.amazonaws.com```  

執行完指令後，查看 `/home/user/.docker/config.json` 會有一組 token ，docker client 在 push/pull 映像檔時，就會拿這組 token 去驗證。  
```json
{
	"auths": {
		"<account_id>.dkr.ecr.<region>.amazonaws.com": {
			"auth": "******************"
		}
	}
}
```  

>透過 CLI 取得的 token 其時效性只有 12 小時

### HTTP API

先使用 CLI 取得 token 。
```
TOKEN=$(aws ecr get-authorization-token --output text --query 'authorizationData[].authorizationToken')
```  

呼叫 ECR API 時帶入 token 。
```
curl -i -H "Authorization: Basic $TOKEN" https://{account_id}.dkr.ecr.{region}.amazonaws.com/v2/{repository}/tags/list
```  

### Amazon ECR Credential Helper

依照環境[安裝](https://github.com/awslabs/amazon-ecr-credential-helper?tab=readme-ov-file#installing) credential helper 後並更新 `/home/user/.docker/config.json` 內容。  
```json
{
	"credHelpers": {
		"public.ecr.aws": "ecr-login",
		"<account_id>.dkr.ecr.<region>.amazonaws.com": "ecr-login"
	}
}
```  

更新完 `config.json` ，就可以直接 push/pull ，不用執行 `docker login` ，也不用擔心 token 過期的問題，因為 `ecr-login` 會告訴 docker client 從 credential helper 拿取 token ，而 credential helper 會自動幫你更新過期的 token ，避免發生過期的問題。  

#### 注意事項

若原先是用 `get-login-password` 取得 token ，想轉換成 credential helper 時，需執行 `docker logout` 否則 docker client 依舊會使用原有的 token 去做驗證的動作。  
```
docker logout {account_id}.dkr.ecr.{region}.amazonaws.com/{repository}
```  

## 總結

以上三種方式皆可以取得驗證 Private Registry 的 token ，而 CLI 與 API 的方法都是透過 `get-login-password` 取得，都是有時效性的，想避免 token 過期，就直接使用 credential helper 吧。  

## Reference

- [Private registry authentication](https://docs.aws.amazon.com/zh_tw/AmazonECR/latest/userguide/registry_auth.html)
- [Ways to Authenticate for Pull/Push Private Container Images from AWS](https://mgufrone.medium.com/ways-to-authenticate-for-pull-push-private-container-images-from-aws-21dacabee143)

