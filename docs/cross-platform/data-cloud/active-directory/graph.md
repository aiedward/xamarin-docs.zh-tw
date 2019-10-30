---
title: 存取圖形 API
description: 本檔說明如何將 Azure Active Directory 驗證新增至以 Xamarin 建立的行動應用程式。
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7ccfc082f86d0a0c6f8d29a477101edb72f9c92f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016624"
---
# <a name="accessing-the-graph-api"></a>存取圖形 API

請遵循下列步驟，從 Xamarin 應用程式中使用圖形 API：

1. 在*windowsazure.com*入口網站上[向 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)，然後
2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步驟 3： 將 Active Directory 驗證新增至應用程式

在您的應用程式中，使用 Visual Studio 或 Visual Studio for Mac 中的 NuGet 套件管理員，將參考新增至**Azure Active Directory Authentication Library （AZURE ADAL）** 。
請務必選取 [**顯示發行前版本套件**以包含此套件]，因為它仍處於預覽狀態。

> [!IMPORTANT]
> 注意： Azure ADAL 3.0 目前為預覽狀態，而且在發行最終版本之前可能會有重大變更。 

![](graph-images/06.-adal-nuget-package.jpg "Add a reference to Azure Active Directory Authentication Library (Azure ADAL)")

在您的應用程式中，您現在需要新增驗證流程所需的下列類別層級變數。

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

這裡有一個要注意的事項 `commonAuthority`。 當驗證端點 `common`時，您的應用程式會變成**多租**使用者，這表示任何使用者都可以使用其 Active Directory 認證來登入。 驗證之後，該使用者將會在自己的 Active Directory 內容上工作，也就是他們會看到與 Active Directory 相關的詳細資料。

### <a name="write-method-to-acquire-access-token"></a>撰寫方法以取得存取權杖

下列程式碼（適用于 Android）會啟動驗證，並在完成時指派 `authResult`中的結果。 IOS 和 Windows Phone 的執行方式稍有不同：第二個參數（`Activity`）在 iOS 上不同，而且不存在於 Windows Phone 上。

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

在上述程式碼中，`AuthenticationContext` 會負責使用 commonAuthority 進行驗證。 它有一個 `AcquireTokenAsync` 方法，其採用參數作為需要存取的資源，在此案例中 `graphResourceUri`、`clientId`和 `returnUri`。 當驗證完成時，應用程式會回到 `returnUri`。 所有平臺的此程式碼都會保持相同，不過，最後一個參數 `AuthorizationParameters`在不同的平臺上將會不同，並且負責管理驗證流程。

在 Android 或 iOS 的案例中，我們會將 `this` 參數傳遞至 `AuthorizationParameters(this)` 以共用內容，而在 Windows 中，則會在沒有任何參數的情況下傳遞至新的 `AuthorizationParameters()`。

### <a name="handle-continuation-for-android"></a>處理 Android 的接續

驗證完成之後，流程應該會回到應用程式。 在 Android 案例中，它是由下列程式碼所處理，這應該新增至**MainActivity.cs**：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>處理 Windows Phone 的接續

針對 Windows Phone 使用下列程式碼修改**App.xaml.cs**檔案中的 `OnActivated` 方法：

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

現在，如果您執行應用程式，應該會看到 [驗證] 對話方塊。
成功驗證之後，它會要求您的許可權以存取資源（在我們的案例中為圖形 API）：

![](graph-images/08.-authentication-flow.jpg "Upon successful authentication, it will ask your permissions to access the resources in our case Graph API")

如果驗證成功，且您已授權應用程式存取資源，您應該會在 `authResult`中取得 `AccessToken` 和 `RefreshToken` 組合。 需要這些權杖才能進行進一步的 API 呼叫，並在幕後使用 Azure Active Directory 進行授權。

![](graph-images/07.-access-token-for-authentication.jpg "These tokens are   required for further API calls and for authorization with Azure Active Directory behind the scenes")

例如，下列程式碼可讓您從 Active Directory 取得使用者清單。 您可以使用 Azure AD 所保護的 Web API 來取代 Web API URL。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```
