---
title: 存取圖形 API
description: 本文件說明如何將 Azure Active Directory 驗證新增至使用 Xamarin 建置行動應用程式。
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189935"
---
# <a name="accessing-the-graph-api"></a>存取圖形 API

請遵循下列步驟來使用 Graph API 從 Xamarin 應用程式內：

1. [使用 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*入口網站，然後
2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步驟 3： 將 Active Directory 驗證新增至應用程式

在您的應用程式中，加入參考**Azure Active Directory Authentication Library (Azure ADAL)** 使用 NuGet 套件管理員，在 Visual Studio 或 Visual Studio for mac。
請確定您選取**顯示發行前版本套件**包含這個封裝，它仍處於預覽狀態。

> [!IMPORTANT]
> 注意:Azure ADAL 3.0 目前為預覽版，可能有重大變更的最終版本發行之前。 


![](graph-images/06.-adal-nuget-package.jpg "加入 Azure Active Directory Authentication Library (Azure ADAL) 的參考")

在您的應用程式中，您現在必須新增下列類別層級變數所需的驗證流程。

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

請注意下面是`commonAuthority`。 驗證端點時`common`，您的應用程式變成**多租用戶**，這表示任何使用者可以使用登入使用其 Active Directory 認證。 在驗證之後，該使用者在他們自己的 Active Directory 的內容上運作，– 也就是他們會看到他的 Active Directory 相關的詳細資料。

### <a name="write-method-to-acquire-access-token"></a>撰寫方法來取得存取權杖

下列程式碼 （適用於 Android) 會開始驗證，並在完成時指派中的結果`authResult`。 IOS 和 Windows Phone 實作稍有不同： 第二個參數 (`Activity`) 不同，在 iOS 上，且不在 Windows Phone 上。

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

在上述程式碼中，`AuthenticationContext`負責 commonAuthority 的驗證。 它有`AcquireTokenAsync`方法，為資源需要存取，在此情況下會採用參數`graphResourceUri`， `clientId`，和`returnUri`。 應用程式將會回到`returnUri`驗證完成時。 此程式碼仍適用於所有平台，不過，最後一個參數， `AuthorizationParameters`，將會在不同的平台上不同，而且會負責控管的驗證流程。

如果是 Android 或 iOS，我們將傳遞`this`參數來`AuthorizationParameters(this)`共用內容，而在 Windows 中不含任何參數當做傳遞新`AuthorizationParameters()`。

### <a name="handle-continuation-for-android"></a>處理適用於 Android 的接續

完成驗證之後，流程應該傳回至應用程式。 在 Android 它由下列程式碼的情況下這應新增至**MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>處理適用於 Windows Phone 的接續

適用於 Windows Phone 修改`OnActivated`方法中的**App.xaml.cs**檔案中使用下列程式碼：

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

現在如果您執行應用程式時，您應該會看到 [驗證] 對話方塊。
驗證成功後，它會要求您的權限，才能存取資源 （在我們的案例 Graph API）：

![](graph-images/08.-authentication-flow.jpg "驗證成功後，它會要求您的權限存取我們的案例 Graph API 中的資源")

如果驗證會成功，而且您已獲授權可存取資源應用程式，您應該會看到`AccessToken`並`RefreshToken`中的下拉式`authResult`。 這些 token 包括才能進一步 API 呼叫以及與 Azure Active Directory 的授權在幕後進行。

![](graph-images/07.-access-token-for-authentication.jpg "這些 token 包括才能進一步 API 呼叫，以及在幕後與 Azure Active Directory 授權")

例如，下列程式碼可讓您從 Active Directory 取得使用者清單。 您可以取代您的 Web API 是由 Azure AD 保護 Web API URL。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

