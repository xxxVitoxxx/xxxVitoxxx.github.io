# JWT

JWT 是 JSON Web Token 的簡寫，它是一種開放標準 [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519)，此標準定義了一種緊湊且獨立的方式，用 JSON 物件在雙方之間安全地傳輸信息。  
<!--more-->

## 什麼是 JWT

簡單來說，它是一個 JSON 格式的加密字串，其中包含的 Secret 允許我們可以驗證不同服務的請求者，是一種在有效時間內，可以利用 token 要求對應操作權限的一種方法。  

## 什麼時候會使用 JWT

- Authorization  
  這是使用 JWT 最常見的場景，當用戶登入後，服務端會發給用戶 JWT ，往後用戶的請求只要帶著 JWT ，使用戶就可以訪問該 JWT 可以訪問的服務與資源。  
- Information Exchange  
  因為 JWT 可以簽章，所以它是雙方之間傳輸資訊的好方法，舉例來說，使用公/私鑰，你可以確認請求者是不是正確的人，此外，由於簽章是使用 Header 與 Payload ，你還可以驗證內容是否被篡改過。  

## JWT 結構

JWT 是由三個部位並用 `.` 組成的，分別是：  
- Header  
- Payload  
- Signature  

完整的 JWT 如下：  

![](https://imgur.com/X2f2IA9.png)

### Header

Header 由兩個部分組成，分別是 token 的型態（也就是 JWT）跟使用的簽章演算法  

例如：  

```json
{
    "typ": "JWT",
    "alg": "HS256"
}
```  

這個 json 會以 `Base64Url` 編碼成 JWT 的第一個部位（xxxxx）  

### Payload

Payload 包含 Claims ， Claims 是關於實體（通常是用戶）和附加數據的聲明， Claims 有三種類型： *registered*, *public* and *private* claims 。  

- Registered Claims  
它是一組由 [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519#:~:text=May%202015%0A%0A%0A4.1.-,registered%20claim%20names,-The%20following%20Claim) 定義的聲明，沒有強制性要使用，但建議使用，可以提供一組有用的、可互相操作的聲明，例如： iss, exp, sub, aud 。  

- Public Claims  
由 RFC 7519 以外的人定義的聲明，且被公開在 [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml#:~:text=RFC7519%2C%20Section%204.1.7%5D-,name,%5BRFC9321%2C%20Section%203.2.3%5D,-JWT%20Confirmation%20Methods) ，例如： preferred_username 。  

- Private Claims  
供 JWT 發行者與用戶雙方使用的自定義聲明，既不是 registered 也不是 public claims，這意味著在你的應用程式之外，該聲明可能無法被正確的理解，因為別的應用程式可能沒有使用相同名稱的聲明或使用聲明的方法與你不同。  

一個 Payload 範例如：  

```json
{
    "sub": "1234567890",
    "name": "vito",
    "exp": 15323232, // the expiration time on or after which the JWT must not be accepted for processing
    "iat": 14567766 // when the token was issues
}
```  

Payload 會透過 `Base64Url` 編碼成 JWT 的第二個部位（yyyyy）。  

>請注意，對於已簽章的 token ，雖然受到防止篡改的保護，但它是任何人都可以解讀的，所以請不要將敏感信息（密碼），放在 Header 或 Payload ，除非它是加密過的。  

### Signature

為了創建 Signature ，必須採用已編碼過的 Header 、已編碼過的 Payload 、 Secret 、 Header 指定的演算法並將其進行簽章。  

例如，使用 HMAC SHA256 算法，會依照以下方式創建 Signature（zzzzz）  

```
HMACSHA256(
    base64UrlEncode(header) + "." + 
    base64UrlEncode(payload),
    secret
)
```  

Signature 用於驗證信息沒有被篡改過，對於使用私鑰簽章的 token ，它還可以驗證 JWT 的請求者  

### 組合成 JWT 結構

由已用 Base64Url 編譯過的 Header 、 Payload 、 Signature ，三個 Base64-URL 字串組合而成，可以很容易的在 HTML 和 HTTP 環境中傳遞，與基於 XML 標準（如 SAML） 相比更加緊湊。  

下面展示一個 JWT ，他對前面的 Header 、 Payload 進行編碼，並使用 secret 進行簽名  

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6InZpdG8iLCJleHAiOjE1MzIzMjMyLCJpYXQiOjE0NTY3NzY2fQ.
4FgwaKyx5eGFkJUoEVD_Po5p9jjjWOcl4YnjpDBwZPs
```  

你可以使用 [JWT Debugger](https://jwt.io/) 驗證、解碼和生成 JWT  

![](https://imgur.com/Hf0e2Jo.png)  

## JWT 如何運作

在身份驗證中，會如下圖所示，當用戶成功登入後，會回傳一個 JSON Web Token ，之後在有限時間內，可以使用 token 做特定的操作或請求，不需重複執行登入的動作，由於 token 是憑證，因此必須注意防止出現安全問題，通常不應保留過期的 token  

```markdown
{{< mermaid >}}
sequenceDiagram;
autonumber
participant C as Client
participant A as Authentication
participant S as Server
C->>+A: login with account and password
A->>+S: get account information
S-->>-A: reply
activate A
A->>A: authenticate and generate signature
A-->>A: reply result
deactivate A
A-->>-C: reply JWT if have a valid signature
{{< /mermaid >}}
```  

1. 用戶使用帳號密碼登入  
2. 驗證用戶帳號密碼的正確性  
3. 回覆驗證結果  
4. 如果驗證成功會產生 signature  
5. 得到 JWT  
6. 如果驗證成功，用戶會得到 JWT  

每當用戶想訪問受保護的路由或資源時，用戶都應發送 JWT ，因為 JWT 不記名的關係，所以通常會在驗證的 header 中使用 Bearer 模式（如果利用 Authorization header 傳送 token ，就不會有 CORS 問題，因為它不使用 cookie），內容如下：  

```
Authorization: Bearer <token>
```  

由於缺乏安全性，[不應該將敏感資訊儲存在瀏覽器中](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage)  

## 用 Go 實作 JWT 身份驗證

### 產生 JWT

當用戶成功登入後，會透過 `generateJWTToken()` 產生 JWT 。  

```go
type JWTClaims struct {
    Email string `json:"email"`
    jwt.RegisteredClaims
}

func generateJWTToken(username string) (string, error) {
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, JWTClaims{
        Email: "xxxVitoxxx@gmail.com",
        RegisteredClaims: jwt.RegisteredClaims{
            NotBefore: jwt.NewNumericDate(time.Now()),
            IssuedAt:  jwt.NewNumericDate(time.Now()),
            ExpiresAt: jwt.NewNumericDate(time.Now().Add(1 * time.Minute)),
        },
    })
    return token.SignedString(Secret)
}
```  

第 1 行： 宣告你定義的 Claims 。  
第 7 行： 先用指定的簽章演算法和定義的 Cliams 去建立 token 。  
第 10~12 行： 定義要使用的 Registered Claims 。  
第 15 行： 將 token 進行簽章，並回傳 JWT 。  

### 驗證 JWT

這邊將驗證 JWT 的方法寫成 middleware ，讓需有 JWT 權限才能訪問的 API 會先經過該 middleware 驗證 JWT ，驗證成功後才能獲取 API 資源。  

```go
func middlewareVerifyJWT() gin.HandlerFunc {
    return func(c *gin.Context) {
        auth := c.GetHeader("Authorization")
        if auth == "" {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"message": "missing authorization"})
            return
        }

        bearer := strings.Split(auth, " ")
        if bearer[0] != "Bearer" || len(bearer) != 2 || len(bearer[1]) == 0 {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"message": "invalid authorization"})
            return
        }

        tokenDecode, err := jwt.ParseWithClaims(bearer[1], &JWTClaims{}, func(token *jwt.Token) (interface{}, error) {
            return Secret, nil
        })
        if err != nil {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"message": "invalid token"})
            return
        }

        _, ok := tokenDecode.Claims.(*JWTClaims)
        if !ok || !tokenDecode.Valid {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"message": "invalid token"})
            return
        }

        c.Next()
    }
}
```  

第 3~7 行： 從 request 的 header 取得 Authorization 的資料，並檢查 Authorization 是否有資料。  
第 9~13 行： 檢查是否為 Bearer token ，且 token 是否有資料。  
第 15~21 行： 透過自定義的 Claims 對 token 進行解析，並確認解析是否有錯誤。  
第 23~27 行： 驗證 tokenDecode 的 Claims 是否是我們自定義的 JWTClaims 型態，及解析完的 token 是否有效。  

[完整程式碼](https://github.com/xxxVitoxxx/notes-and-issue/blob/main/example/JWT/main.go)  

## Reference

- [jwt.io](https://jwt.io/introduction)  
- [搭配上面一起](https://dev.to/moe23/intro-to-jwt-mcb)
- [JWT(JSON Web Token) — 原理介紹](https://medium.com/%E4%BC%81%E9%B5%9D%E4%B9%9F%E6%87%82%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/jwt-json-web-token-%E5%8E%9F%E7%90%86%E4%BB%8B%E7%B4%B9-74abfafad7ba)  
- [淺談JWT的安全性與適用情境](https://medium.com/mr-efacani-teatime/%E6%B7%BA%E8%AB%87jwt%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E9%81%A9%E7%94%A8%E6%83%85%E5%A2%83-301b5491b60e)
