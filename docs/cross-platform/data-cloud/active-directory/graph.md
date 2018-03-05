---
title: "存取 Graph API"
description: "使用 Active Directory 查詢 Graph API 使用 Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2b0e4a9466ab59ec27b957bf284a7d3895f6a4fc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="accessing-the-graph-api"></a>存取 Graph API

_使用 Active Directory 查詢 Graph API 使用 Xamarin_

請遵循下列步驟使用 Graph API 中的 Xamarin 應用程式中：

1. [向 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*入口網站，然後
2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步驟 3： 將 Active Directory 驗證加入至應用程式

應用程式中加入的參考**Azure Active Directory Authentication Library (Azure ADAL)**使用 NuGet 封裝管理員，在 Visual Studio 或 Visual Studio for mac。
請確定您選取**顯示發行前版本的封裝**包含這個封裝，因為它仍處於 preview 階段。

> [!IMPORTANT]
> 注意： Azure ADAL 3.0 目前是預覽，而且之前可能會有重大變更的最終版本發行。 


![](graph-images/06.-adal-nuget-package.jpg "加入 Azure Active Directory Authentication Library (Azure ADAL) 的參考")

在您的應用程式，您現在需要將下列類別層級變數所需的驗證流程。

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

請注意以下是`commonAuthority`。 當驗證端點是`common`，您的應用程式會變成**多租用戶**，這表示任何使用者可以使用登入使用其 Active Directory 認證。 在驗證之後，該使用者將自己的 Active Directory 的內容上運作，也就是他們會看到其 Active Directory 相關的詳細資料。

### <a name="write-method-to-acquire-access-token"></a>撰寫方法來取得存取權杖

下列程式碼 （適用於 Android) 將會啟動驗證，並在完成指派中的結果`authResult`。 IOS 和 Windows Phone 實作稍有不同： 第二個參數 (`Activity`) 不同，在 iOS 上，且不存在 Windows Phone 上。

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

在上述程式碼中，`AuthenticationContext`負責 commonAuthority 與驗證。 它有`AcquireTokenAsync`方法，為資源需要存取，在此情況下會採用參數`graphResourceUri`， `clientId`，和`returnUri`。 應用程式將會回到`returnUri`完成驗證。 此程式碼會保留相同的所有平台，不過，最後一個參數， `AuthorizationParameters`，將會在不同的平台上的不同，而且會負責控管的驗證流程。

在 Android 或 iOS，我們傳遞`this`參數`AuthorizationParameters(this)`來共用內容，而在 Windows 中傳遞不含任何參數時的新`AuthorizationParameters()`。

### <a name="handle-continuation-for-android"></a>處理適用於 Android 的接續

驗證完成後，流程應該傳回至應用程式。 如果它由下列程式碼的 Android，其中應該加入至**Weatherapp**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>處理適用於 Windows Phone 的接續

適用於 Windows Phone 修改`OnActivated`方法中的**App.xaml.cs**檔案與程式碼下方：

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

現在如果您執行應用程式，您應該會看到 [驗證] 對話方塊。
驗證成功後，它會要求您的權限來存取的資源 （在此案例 Graph API 中）：

![](graph-images/08.-authentication-flow.jpg "驗證成功後，它會要求您的權限存取我們的案例中 Graph API 中的資源")

如果驗證是成功，而且您已獲授權存取資源應用程式，您應該取得`AccessToken`和`RefreshToken`中的組合`authResult`。 需要進一步的 API 呼叫，以及在幕後使用 Azure Active Directory 授權這些語彙基元。

![](graph-images/07.-access-token-for-authentication.jpg "這些 token 包括才能進一步的 API 呼叫，以及在幕後使用 Azure Active Directory 授權")

例如，下列程式碼可讓您從 Active Directory 中取得使用者清單。 您可以取代您的 Web API 是 Azure AD 所保護的 Web API URL。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

