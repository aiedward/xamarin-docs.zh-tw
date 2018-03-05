---
title: "驗證 RESTful Web 服務"
description: "HTTP 支援使用數種驗證機制，來控制資源的存取權。 基本驗證有正確的認證這些用戶端提供資源的存取權。 這篇文章會示範如何使用基本驗證來保護 RESTful web 服務資源的存取權。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 7aea74f95e8738cc415eaac3a5ac4f86b069d0f7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-a-restful-web-service"></a>驗證 RESTful Web 服務

_HTTP 支援使用數種驗證機制，來控制資源的存取權。基本驗證有正確的認證這些用戶端提供資源的存取權。這篇文章會示範如何使用基本驗證來保護 RESTful web 服務資源的存取權。_

伴隨 Xamarin.Forms 範例應用程式取用 Xamarin 裝載的 REST 服務，提供唯讀存取 web 服務。 因此，建立、 更新和刪除資料的作業不會改變應用程式中使用的資料。 不過，可裝載的 REST 服務版本，可以在*TodoRESTService*可以在那裡找到資料夾中的範例應用程式和設定服務的指示。 本版可裝載的 REST 服務提供完整建立、 更新、 讀取及刪除資料的存取。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>透過 HTTP 驗證使用者

基本驗證是支援 HTTP，最簡單的驗證機制，而且涉及用戶端傳送的使用者名稱和密碼為未加密的 base64 編碼的文字。 它的運作方式，如下所示：

- 如果 web 服務收到受保護資源的要求時，它會拒絕要求，而且出現 HTTP 狀態碼 401 （拒絕存取），並設定 WWW 驗證回應標頭，如下列圖表所示：

![](rest-images/basic-authentication-fail.png "基本驗證失敗")

- 如果 web 服務會接收要求受保護的資源，與`Authorization`標頭正確設定，web 服務回應的 HTTP 狀態碼 200，表示要求成功，以及所要求的資訊會在回應中。 下圖顯示此案例：

![](rest-images/basic-authentication-success.png "基本驗證成功")

> [!NOTE]
> 基本驗證應該只用於透過 HTTPS 連線。 當透過 HTTP 連線，使用<code>Authorization</code>標頭可以輕鬆地進行解碼，如果攻擊者擷取 HTTP 流量。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 要求中指定基本驗證

使用基本驗證的指定方式如下：

1. 「 基本 」 加入至字串`Authorization`要求標頭。
1. 使用者名稱和密碼會結合成一個字串的格式為"username: password"，然後是 base64 編碼，並加入至`Authorization`要求標頭。

因此，使用者名稱的 'XamarinUser' 和 'XamarinPassword' 的密碼，與標頭會變成：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`類別可以設定`Authorization`上的標頭值`HttpClient.DefaultRequestHeaders.Authorization`屬性。 因為`HttpClient`執行個體存在多個要求，`Authorization`標頭只需要設定一次，而不是時讓每個要求，如下列程式碼範例所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    client = new HttpClient ();
    ...
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

然後要求至 web 服務作業提出要求時使用簽署`Authorization`標頭，指出使用者是否有權叫用作業。

> [!NOTE]
> 雖然範例 REST 服務會將認證儲存為常數，它們應該不會儲存已發行的應用程式中不安全的格式。 [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet 提供安全地儲存認證的功能。 如需詳細資訊，請參閱[儲存和擷取裝置上的帳戶資訊](~/xamarin-forms/data-cloud/authentication/oauth.md)。


## <a name="processing-the-authorization-header-server-side"></a>處理授權標頭伺服器端

隨附的範例 REST 服務會裝飾使用每個動作`[BasicAuthentication]`屬性。 這個屬性由實作`BasicAuthenticationAttribute`類別在方案中，並將用來剖析`Authorization`標頭，並判斷 base64 編碼認證是否有效的比較中所儲存值*Web.config*.這個方法適用於範例服務時，它需要公開 web 服務延伸。

在 IIS 使用基本驗證模組中，使用者會針對其 Windows 認證進行驗證。 因此，使用者必須擁有伺服器的網域上的帳戶。 不過，可以設定基本驗證模型，以允許自訂驗證，會對外部來源，例如資料庫已驗證使用者帳戶。 如需詳細資訊，請參閱[中 ASP.NET Web API 的基本驗證](http://www.asp.net/web-api/overview/security/basic-authentication)ASP.NET 網站上。

> [!NOTE]
> 基本驗證不被設計成管理登出。因此，登出的標準基本驗證方法是結束工作階段。

## <a name="summary"></a>總結

本文示範如何建立 Xamarin.Forms 應用程式使用的 web 要求中新增基本驗證`HttpClient`類別。 基本驗證有正確的認證這些用戶端提供資源的存取權。 如需有關如何使用資訊[Xamarin.Auth](https://www.nuget.org/packages/Xamarin.Auth/)管理驗證程序中的 Xamarin.Forms 應用程式，讓使用者可以共用後端，在只具有其資料的存取權時，請參閱[驗證使用者身分識別提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)。


## <a name="related-links"></a>相關連結

- [TodoREST （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [使用 RESTful web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
