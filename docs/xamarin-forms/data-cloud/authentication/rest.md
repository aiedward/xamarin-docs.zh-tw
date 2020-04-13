---
title: 驗證 RESTful Web 服務
description: 基本身份驗證僅向具有正確憑據的用戶端提供對資源的訪問許可權。 本文介紹如何使用基本身份驗證來保護對 RESTful Web 服務資源的訪問。
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 65606b72c3944809a09b8c70b9cdbb5524e0f856
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388586"
---
# <a name="authenticate-a-restful-web-service"></a>驗證 RESTful Web 服務

_HTTP 支援使用多種身份驗證機制來控制對資源的訪問。基本身份驗證僅向具有正確憑據的用戶端提供對資源的訪問許可權。本文演示如何使用基本身份驗證來保護對 RESTful Web 服務資源的訪問。_

> [!NOTE]
> 在 iOS 9 及更高級別中,應用傳輸安全 (ATS) 強制 Internet 資源(如應用的後端伺服器)與應用之間的安全連接,從而防止敏感資訊意外洩露。 由於默認情況下,在為 iOS 9 構建的應用中啟用了 ATS,因此所有連接都必須遵守 ATS 安全要求。 如果連接不符合這些要求,它們將失敗,但有一個例外。
> 如果無法使用該協定和安全通信來獲取互聯網資源,`HTTPS`則可以選擇退出 ATS。 這可以通過更新應用的**Info.plist**檔來實現。 有關詳細資訊,請參閱[應用傳輸安全](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>透過 HTTP 對使用者進行身份驗證

基本身份驗證是 HTTP 支援的最簡單的身份驗證機制,涉及用戶端將使用者名和密碼作為未加密的 base64 編碼文字發送。 其運作如下：

- 如果 Web 服務收到受保護資源的請求,它將使用 HTTP 狀態代碼 401(訪問被拒絕)拒絕請求,並設置 WWW-身份驗證回應標頭,如下圖所示:

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- 如果 Web 服務收到受保護資源的請求`Authorization`,並且標頭設置正確,則 Web 服務會使用 HTTP 狀態代碼 200 進行回應,該狀態代碼 200 指示請求成功,並且請求的資訊在回應中。 此方案顯示在下圖中:

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> 基本身份驗證應僅在 HTTPS 連接上使用。 當通過 HTTP 連接`Authorization`使用時 ,如果攻擊者捕獲了 HTTP 流量,則可以輕鬆解碼標頭。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 要求中指定基本認證

基本身份驗證的使用指定如下:

1. 字串"基本"將添加到請求的`Authorization`標頭中。
1. 使用者名和密碼合併到格式為"使用者名:密碼"的字串中,然後對 base64 進行編碼並添加到`Authorization`請求的標頭中。

因此,使用「XamarinUser」的使用者名稱和「XamarinPassword」的密碼,標頭變為:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

類`HttpClient``Authorization``HttpClient.DefaultRequestHeaders.Authorization`可以在 屬性上設置標頭值。 由於`HttpClient`實體存在於多個要求中,`Authorization`因此標頭只需設定一次,而不是在發出每個請求時設定一次,如以下代碼範例所示:

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

然後,當請求 Web 服務操作時,`Authorization`請求用 標頭簽名,指示使用者是否具有調用該操作的許可權。

> [!IMPORTANT]
> 雖然此代碼將憑據存儲為常量,但它們不應以不安全的格式存儲在已發佈的應用程式中。

## <a name="processing-the-authorization-header-server-side"></a>處理授權標頭伺服器端

REST 服務應使用`[BasicAuthentication]`屬性 修飾每個操作。 此屬性用於分析`Authorization`標頭,並通過將 base64 編碼認證與儲存在*Web.config*中的值進行比較來確定其是否有效。雖然此方法適用於示例服務,但它需要擴展面向公眾的 Web 服務。

在IIS使用的基本身份驗證模組中,使用者會根據Windows認證。 因此,用戶必須在伺服器的域上具有帳戶。 但是,基本身份驗證模型可以配置為允許自定義身份驗證,其中使用者帳戶對外部源(如資料庫)進行身份驗證。 有關詳細資訊,請參閱ASP.NET網站上[ASP.NET Web API 中的基本身份驗證](https://www.asp.net/web-api/overview/security/basic-authentication)。

> [!NOTE]
> 基本身份驗證不是用於管理註銷。因此,註銷的標準基本身份驗證方法是結束會話。

## <a name="related-links"></a>相關連結

- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
