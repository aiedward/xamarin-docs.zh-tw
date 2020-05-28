---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d62e533d127294c77c0779c20fd9c78ef2231200
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135716"
---
# <a name="authenticate-a-restful-web-service"></a>驗證 RESTful Web 服務

_HTTP 支援使用數個驗證機制來控制資源的存取權。基本驗證僅提供資源的存取權給具有正確認證的用戶端。本文示範如何使用基本驗證來保護 RESTful web 服務資源的存取。_

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，藉此防止意外洩漏機密資訊。 由於預設會在針對 iOS 9 建立的應用程式中啟用 ATS，因此所有連線都將受限於 ATS 安全性需求。 如果連線不符合這些需求，則會失敗並產生例外狀況。
> 如果無法 `HTTPS` 針對網際網路資源使用通訊協定和安全通訊，可以退出宣告 ATS。 這可以藉由更新應用程式的**plist**檔案來達成。 如需詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>透過 HTTP 驗證使用者

基本驗證是 HTTP 所支援的最簡單驗證機制，其牽涉到用戶端傳送使用者名稱和密碼為未加密的 base64 編碼文字。 其運作如下：

- 如果 web 服務收到受保護資源的要求，它會拒絕要求，HTTP 狀態碼為401（拒絕存取），並設定 WWW 驗證回應標頭，如下圖所示：

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- 如果 web 服務收到受保護資源的要求，且 `Authorization` 標頭已正確設定，則 web 服務會以 HTTP 狀態碼200回應，這表示要求成功，且要求的資訊在回應中。 下圖顯示此案例：

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> 基本驗證只能透過 HTTPS 連接使用。 在 HTTP 連線上使用時， `Authorization` 如果 HTTP 流量是由攻擊者所捕捉，則可以輕鬆地將標頭解碼。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 要求中指定基本驗證

基本驗證的使用方式指定如下：

1. 字串 "Basic" 會新增至要求的 `Authorization` 標頭。
1. 使用者名稱和密碼會合並成格式為 "username： password" 的字串，然後以 base64 編碼並新增至 `Authorization` 要求的標頭。

因此，如果使用者名稱為 ' XamarinUser ' 且密碼為 ' XamarinPassword '，則標頭會變成：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`類別可以 `Authorization` 在屬性上設定標頭值 `HttpClient.DefaultRequestHeaders.Authorization` 。 因為 `HttpClient` 實例存在於多個要求中，所以 `Authorization` 標頭只需要設定一次，而不是在提出每個要求時，如下列程式碼範例所示：

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

接著，在對 web 服務作業提出要求時，會以標頭簽署要求 `Authorization` ，指出使用者是否有叫用作業的許可權。

> [!IMPORTANT]
> 雖然此程式碼會將認證儲存為常數，但不應以不安全的格式儲存在已發行的應用程式中。

## <a name="processing-the-authorization-header-server-side"></a>處理授權標頭伺服器端

REST 服務應使用屬性來裝飾每個動作 `[BasicAuthentication]` 。 這個屬性是用來剖析 `Authorization` 標頭，並藉由比較它們與儲存在*web.config*中的值來判斷 base64 編碼的認證是否有效。雖然此方法適用于範例服務，但它需要針對公開的 web 服務進行擴充。

在 IIS 所使用的基本驗證模組中，使用者會根據其 Windows 認證進行驗證。 因此，使用者必須在伺服器的網域上擁有帳戶。 不過，基本驗證模型可以設定為允許自訂驗證，其中使用者帳戶會針對外部來源（例如資料庫）進行驗證。 如需詳細資訊，請參閱 ASP.NET 網站上[ASP.NET Web API 中的基本驗證](https://www.asp.net/web-api/overview/security/basic-authentication)。

> [!NOTE]
> 基本驗證並非設計來管理登出。因此，用於登出的標準基本驗證方法是結束會話。

## <a name="related-links"></a>相關連結

- [使用 RESTful web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
