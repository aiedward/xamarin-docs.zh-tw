---
title: 驗證 RESTful Web 服務
description: 基本驗證可讓您只存取具有正確認證的用戶端的資源。 本文說明如何使用基本驗證來保護 RESTful web 服務資源的存取。
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98b59b1dc9e32b1af1be991055057b94223a4223
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563259"
---
# <a name="authenticate-a-restful-web-service"></a>驗證 RESTful Web 服務

_HTTP 支援使用數種驗證機制來控制資源的存取權。基本驗證可讓您只存取具有正確認證的用戶端的資源。本文示範如何使用基本驗證來保護 RESTful web 服務資源的存取。_

> [!NOTE]
> 在 iOS 9 及更新版本中，應用程式傳輸安全性 (ATS) 會強制網際網路資源之間的安全連線 (例如，應用程式的後端伺服器) 和應用程式，進而防止意外洩漏機密資訊。 因為 ATS 預設會在針對 iOS 9 建立的應用程式中啟用，所以所有連線都必須遵守 ATS 安全性需求。 如果連接不符合這些需求，則會失敗併發生例外狀況。
> 如果無法使用通訊 `HTTPS` 協定和網際網路資源的安全通訊，則可以選擇不使用 ATS。 這可以藉由更新應用程式的 **Info. plist** 檔案來達成。 如需詳細資訊，請參閱 [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>透過 HTTP 驗證使用者

基本驗證是 HTTP 所支援的最簡單驗證機制，牽涉到用戶端將使用者名稱和密碼傳送為未加密的 base64 編碼文字。 其運作如下：

- 如果 web 服務收到受保護資源的要求，它會拒絕要求，並顯示 HTTP 狀態碼 401 (拒絕存取) 並設定 WWW 驗證回應標頭，如下列圖表所示：

![基本驗證失敗](rest-images/basic-authentication-fail.png)

- 如果 web 服務收到受保護資源的要求，且 `Authorization` 已正確設定標頭，則 web 服務會以 HTTP 狀態碼200回應，這表示要求成功，且要求的資訊會在回應中。 此案例如下圖所示：

![基本驗證成功](rest-images/basic-authentication-success.png)

> [!NOTE]
> 基本驗證只能透過 HTTPS 連接使用。 使用於 HTTP 連線時， `Authorization` 如果攻擊者捕捉到 HTTP 流量，則可以輕鬆地將標頭解碼。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 要求中指定基本驗證

基本驗證的使用方式指定如下：

1. 字串 "Basic" 會新增至要求的 `Authorization` 標頭。
1. 使用者名稱和密碼會合並成格式為 "username： password" 的字串，然後以 base64 編碼並加入至 `Authorization` 要求的標頭。

因此，使用 ' XamarinUser ' 的使用者名稱和密碼 ' XamarinPassword ' 時，標頭會變成：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`類別可以設定屬性的 `Authorization` 標頭值 `HttpClient.DefaultRequestHeaders.Authorization` 。 因為 `HttpClient` 實例存在於多個要求中，所以 `Authorization` 標頭只需要設定一次，而不是在進行每個要求時，如下列程式碼範例所示：

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

然後，當對 web 服務作業提出要求時，就會使用標頭來簽署要求 `Authorization` ，指出使用者是否有權叫用該作業。

> [!IMPORTANT]
> 雖然這段程式碼會將認證儲存為常數，但不應該在已發行的應用程式中以不安全的格式儲存。

## <a name="processing-the-authorization-header-server-side"></a>處理授權標頭伺服器端

REST 服務應該使用屬性來裝飾每個動作 `[BasicAuthentication]` 。 這個屬性是用來剖析 `Authorization` 標頭，並藉由比較 *Web.config*中儲存的值，判斷 base64 編碼的認證是否有效。雖然此方法適用于範例服務，但它需要針對公眾對應的 web 服務進行擴充。

在 IIS 使用的基本驗證模組中，使用者會根據其 Windows 認證進行驗證。 因此，使用者必須擁有伺服器網域上的帳戶。 不過，您可以將基本驗證模型設定為允許自訂驗證，在此驗證中，使用者帳戶是針對外部來源（例如資料庫）進行驗證。 如需詳細資訊，請參閱 ASP.NET 網站上 [ASP.NET Web API 中的基本驗證](https://www.asp.net/web-api/overview/security/basic-authentication) 。

> [!NOTE]
> 基本驗證並非設計來管理登出。因此，登出的標準基本驗證方法是結束會話。

## <a name="related-links"></a>相關連結

- [使用 RESTful web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](/dotnet/api/system.net.http.httpclient)