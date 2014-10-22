Nebula 登入
================

目前 Nebula 2.0 支援的登入驗證是 [OAuth 2.0](http://oauth.net/2/)，你可以簡單的透過一些設定，使用 [CAMP](http://www.quanta-camp.com) 網站的帳號提供使用者作登入驗證，  

OAuth2 登入機制流程說明
----------------

![LogOnFlow](../../images/LogOnFlow.png)

如果需要與 [CAMP](http://www.quanta-camp.com) 網站登入帳號結合，可透過以下兩種方式來整合登入的帳號驗證

使用 Nebula SDK
----------------

在 Nebula SDK 中  
若專案為 Asp.Net Mvc 4 以前的版本，有提供一個 [Nebula.Web.Security](Nebula.Security.md) 的 Nuget Package
若專案為 Asp.Net Mvc 5，請使用 [Nebula.Web.Identity](Nebula.Web.Identity.md) 的 Nuget Package  
安裝後，就可以與 [CAMP](http://www.quanta-camp.com) 的帳號做整合

透過 Web Service
----------------

[OAuth 2.0](http://oauth.net/2/) 的規格是公開的。開發者也可以自行實做 OAuth2 來做驗證，或者採用熟悉的 Framework 來驗證

### OAuth 2.0 的角色定義

* **Resource Owner** - 可以授權別人去存取 Protected Resource 。如果這個角色是人類的話，則就是指使用者 (end-user)。
* **Resource Server** - 存放 Protected Resource 的伺服器，可以根據 Access Token 來接受 Protected Resource 的請求。
* **Client** - 代表 Resource Owner 去存取 Protected Resource 的應用程式。 "Client" 一詞並不指任何特定的實作方式（可以在 Server 上面跑、在一般電腦上跑、或是在其他的設備）。
* **Authorization Server** - 在認證過 Resource Owner 並且 Resource Owner 許可之後，核發 Access Token 的伺服器。

在 [CAMP](http://www.quanta-camp.com) 中，我們的 Resource Server 與 Authorization Server 是同一組伺服器  

伺服器網址為 https://www.quanta-camp.com/OpenId/  

### OAuth2 的基本流程

以下是抽象化的流程概觀，以比較宏觀的角度來描述，不是實際程式運作的流程（圖出自 Spec 的 Figure 1）：

```
+--------+                               +---------------+
|        |--(A)- Authorization Request ->|   Resource    |
|        |                               |     Owner     |
|        |<-(B)-- Authorization Grant ---|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(C)-- Authorization Grant -->| Authorization |
| Client |                               |     Server    |
|        |<-(D)----- Access Token -------|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(E)----- Access Token ------>|    Resource   |
|        |                               |     Server    |
|        |<-(F)--- Protected Resource ---|               |
+--------+                               +---------------+

                Figure 1: Abstract Protocol Flow
```

### 實做流程

-----
####  1. 使用者登入以及授權 (Authorization Code Flow)

此步驟，需要透過 HTTP 的呼叫，由 Client 端重新導向到 Authorization Server 的 Authorization Endpoint.  
若使用者未登入，將顯示登入畫面提供給使用者登入。若已經登入，將以 HTTP 302 的方式重導回參數中定義的 redirect_uri

回傳資料，用 GET 方式呼叫 redirec_uri，參數將以 QueryString 方式傳入。  
以下網址，為 https://www.quanta-camp.com/OpenId/ 的相對網址  

#### URI

**GET OAuth/Authorize**

#### 參數

參數名稱 | 必要/選擇性 | 內容值意義
 --- | --- | ---
client_id | 必要 | 產品代碼。<br/> 請先申請一個 CAMP 產品，取得產品代碼以及驗證碼。
redirect_uri | 必要 | 登入驗證後，HTTP 302 重導回 SaaS 的網址
response_type | 必要 | 'code'
state | 必要 | 通用參數，用來維持最初狀態。可自行定義。
scope | 選擇 | 通用參數，用來指定資源存取範圍。<br/>資源內容請參考下方資源網址 <br/>(網址請傳入完整的網址，非相對網址)。

##### 回應

回應範例：  

```
http://localhost/?code=Q8TJ%21IAAAAE9k7bl81oIIlqLW5wxZ3pcYHUziL86uB6NKizoCGKscIQEAAAEcjOnigP_pqgfWrUe2d8sug6tK_pmKVVs4x8xIQfsB-6Pw8uy5nuB_XYoQqiC3Fex7wmOo0qufNlQIZKTL8KmyAXNQFbTP4Dqun-Jsgy481yN1Yvkyb6bn1-49KScZ0T0Ycs8k_iZ15A9M1LscgIPrsqw3wbqbLvX_THg_d2OytF3INGTV5Df8wd_HpR27-MIuf7FR0cPBMaXsb8JTMkgzbGYH2uNc0KqXOwK6bo7F0GjzXZDujB4J7_6MHYFnV5IgQxLG0gPmI3ieiNEqA75E2WBVhX0mF9FH_im3JWfDrO1DUczmlufZqQcTOUzgBbl9viE75KgAn3YJIynCMQLLY9NAosxXhTh-Ue26GcaWl7OnbOMGkvk8ZQ5rk1X1VCE&state=abc
```

參數名稱 | 意義
 --- | ---
code | Authorization Code
state | 狀態值。服務將回傳參數中傳入的 state 資料。

-----
####  2. 取得 Access Token

SaaS 服務透過前一個步驟取得 Authorization Code 之後，接下來需要取得 Access Token，才可以存取 Resource 取得使用者的相關資料。

##### URI

**POST OAuth/Token**

##### 驗證

HTTP BASIC 驗證，帳號為**產品代碼**，密碼為**產品授權碼**

##### HEADER

Name | Value
 --- | ---
Content-Type | application/x-www-form-urlencoded
Accept | application/json

##### 參數 (POST)

Name | Value
 --- | ---
code | 請傳入前一步驟取得的 Authorization Code
redirect_uri | 請輸入與第一步驟相同的 URI
state | 請輸入與第一步驟相同的 state
grant_type | 'authorization_code'

##### Response

HTTP 200 - 成功
```javascript
{
    "access_token": "gAAAAG8vXEjz0VLsCVKHyjI4VBtfYklR_JwuH8S9QA0LkgGDTKSx7pvDG-eQ5WTeCSAWcHOeA8Ga3hK-_2KARznZq-4ZmaKh9m_GCjEoApBzhXeMXxo3L_KX9zrf7C8f4yXZu_crc6jNh1z98P1jytZvlXReHjXP0TYEDjQ71UfO5LC-RAEAAIAAAAChxEYuF1MMYdw5rauytPVWuF8IMx95-d6TqJ_5yaLWqUs3wxnQsnEMLKaXCDOmVpfm9gM4yMJUce_RiFXpOU4FPlmYlRMKKfug1ANnWLS2L9IW1XxsYH_Us1PArHBl7UuM_Zlke0Qa4Luwu1SrQxwUJ-OqhHsswOgQKu3PKXVSHvMBK5XX5RkUj491fDFaVQIXRWBbg0c7WEdO5vwJRbU6NaXIYryCHH6m6jbRAL6B7cerHhkC_RlkwVd6Ll6tjWlHKCUQNXUavIcl2INcvYDp7pYrZB4Ied7q5XG3Syy5xiw7mhVdA14ffeFDP3YWU0sixV_0hsJhCv3v84XjHobJ7fSkAwq_dSQ3knVJz4Ecykzlz_Eb84x5Oav3Cjg0nLSGg5gh74pmw5YlQmZzTVlSxLZpmQmEoJkv_VYUfA4dzg",
    "token_type": "bearer",
    "expires_in": 120,
    "refresh_token": "IOsJ!IAAAAPYdtDryZUvKpAPMl_l9x9p5dUZYiCtyEIvQP_-4S1M64QAAAAEgUaowK9ms3viCp_8g_j-4XqMAB4hE9Z996Ib5TUplGNuE-3a0LRWTE23Az2kdIR0--lOOm2MA9xwIncln180WcRpEeXvC_QIHvJCoocNbAN2sxzEI2wRjlmkXdKXiwAGSoKWRJkdthI7O2JrhvOFjhcEDxsnroRTA2VtEr6VR6sEZgJ2s3GsmwDWWDNiElCnuuu6hRy4mB0Gh_9j3-sm9GJz8eaecp-sVQqtwtrmBIYT97ydj-Upm_4vNOpU2G8dlEYyjmieELZs1OG3oxmWFuWXdDohpySZpygbtGPYp_Q",
    "scope": "http://www.quanta-camp.com/OpenId/Profile/UserIdentity"
}
```

參數名稱 | 意義
 --- | ---
access_token | Access Token. 存取 Resource 的必要參數<br/>請自行保存。
token_type | 'bearer', 用來表示，api 驗證傳遞 token 的方式
expires_in |  Token 有效時間，單位：秒。
refresh_token | Token 更新必要參數<br/>請自行保存。
scope | 回傳當初第一步驟要求的scope資料

-----
####  3. 存取資源

在 CAMP 中，我們提供以下的三種 Resource  

存取資源的方式，可透過 System.Net.WebRequest 或任何 HTTP Client 呼叫  

##### 使用者帳號

###### URI
**GET Profile/UserIdentity**  

###### Header

Name | Value
 --- | ---
Authorization | 'Bearer ' + Access Token

###### RESPONSE
HTTP 200
```javascript
{
    "UserId": "使用者登入帳號"
}
```

-----
##### 使用者資訊  

###### URI
**GET Profile/UserInfo**  

###### Header

Name | Value
 --- | ---
Authorization | 'Bearer ' + Access Token

###### RESPONSE
HTTP 200
```javascript
{
    "UserId": "使用者登入帳號",
    "Name": "使用者名稱",
    "EnglishName": "使用者英文名稱",
    "Email": "電子郵件",
    "CompanyId": "公司代碼"
}
```

-----  
##### 公司資訊

###### URI
**GET Profile/CompanyInfo**  

###### Header

Name | Value
 --- | ---
Authorization | 'Bearer ' + Access Token

###### RESPONSE
HTTP 200
```javascript
{
    "CompanyId": "公司代碼",
    "Name": "公司名稱"
}
```

-----
####  4. Refresh Token

當 Access Token 過期後，需要重新取得一個新的 Access Token.

##### URI

**POST OAuth/Token**

##### 驗證

HTTP BASIC 驗證，帳號為**產品代碼**，密碼為**產品授權碼**

##### Header

Name | Value
 --- | ---
code | 請傳入步驟 2 取得的 Refresh Token
grant_type | 'refresh_token'

##### Response

HTTP 200 - 成功

```javascript
{
    "access_token": "gAAAAEliWLz7kqoeuL_KMPChLXF6OHpo-hAnuDsC_NrrntzG7zO6nuEAWCxn6yCGL5huH3vKP1Jfwj7FovifZFdzOpB4TCfQxaHWSyZI1RChGzLH0jVJqsVKCnNkpKbyN0GvZWddQCB6lp8NxGL8tw32c6g7_4edzlGwmiWMTdNMZgOcRAEAAIAAAABwVbUFrTrdUcrqHSiwkr3bZqiSExr1w_x1qkVr5Wc9SvcxZpVrN9Jjy_B9o4U2xam8FfbfVshSbqit1RvhW6nbUDoci_fad9nUc0p1um15qncwsxYvJfGSgIoHOl3pQ4RhVmgNw0SXU0EUHHiFQe6RQ8Vt3fLnqe1h4Hdjb69kyq6QyNowcf3kKEuNq77HqWRMXvS7MxkTq6nd7IQ22vwGjnk7_ZCrjbfPC85j3RRCHznHs-clUEWzT0pd3Xlmq_APodCrzVE9CU44uiheMOV9ZM3kNcCvK5-SsVgnn3Ob0666t6cED9NFii1wxVxQE5FPOrBwaRp-WxTYQOeSGSkiHVxCtR6PwOTTqqV8NGhG-QTv-Hg_lhCcExtDcgoejiEVBEBFv1WzTYZ86iqRr5iVYaC1F5ukaGTdAlgF_PGYog",
    "token_type": "bearer",
    "expires_in": 120,
    "refresh_token": "IOsJ!IAAAAEzZQuV-F65rxc-6pti9iQMOKkQoYzD7ptYJkWEQ_cEA4QAAAAE-qTl-bvIzRbiGO7RMvikPQKCwS1wbaMjWL6WPTEv0TVaM_5hLojzXRV6vG98CxQPM7gVNvtiz-fcr9TINqtG5k5fgkr9egPjEgvLhIE4JlRO0UHBo9UJyxyXGQhTR3cJvlzcX_K2y4oLL1dqWK1beD0kWwqoz1KEbZJwLrTH53UUz6FCf0rw7OJ-W8FP5-PPV-LN95LHCQxZ_U7-G9RefZxuGXIE1Ih1rRyn4LMmMivv9AKM9e3ckyFFdrup_UYvCrm4lk5pzwDnnuQXA_har9Uj_l3IBZMEgj0MgoPE2ZA",
    "scope": "http://www.quanta-camp.com/OpenId/Profile/UserIdentity"
}
```

參數名稱 | 意義
 --- | ---
access_token | Access Token. 存取 Resource 的必要參數<br/>請自行保存。
token_type | 'bearer', 用來表示，api 驗證傳遞 token 的方式
expires_in |  Token 有效時間，單位：秒。
refresh_token | Token 更新必要參數<br/>請自行保存。
scope | 回傳當初第一步驟要求的scope資料
