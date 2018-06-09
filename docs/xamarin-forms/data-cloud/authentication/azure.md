---
title: 驗證使用者與 Azure 行動應用程式
description: 本文說明如何使用 Azure 行動應用程式來管理驗證程序，在 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: fc6206a22d7527ea38a39ab034c424bfe7730abb
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241711"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>驗證使用者與 Azure 行動應用程式

_Azure 行動應用程式會使用各種不同的外部識別提供者來支援應用程式的使用者，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory 驗證和授權。權限可以在資料表上設定，以限制存取已驗證的使用者。本文說明如何使用 Azure 行動應用程式來管理驗證程序，在 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

需要管理驗證程序，在 Xamarin.Forms 應用程式中的 Azure 行動應用程式的程序如下所示：

1. 註冊您的 Azure 行動應用程式的身分識別提供者站台，然後設定行動應用程式後端中的 提供者產生的認證。 如需詳細資訊，請參閱[註冊您的應用程式進行驗證和設定應用程式服務](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services)。
1. 定義新的 URL 配置 Xamarin.Forms 應用程式，可讓驗證系統驗證程序完成之後，重新導向回到 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱[將您的應用程式新增至允許外部重新導向 Url](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl)。
1. 僅限驗證的用戶端在 Azure 行動應用程式後端限制存取。 如需詳細資訊，請參閱[限制已驗證的使用者權限](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users)。
1. 叫用從 Xamarin.Forms 應用程式的驗證。 如需詳細資訊，請參閱[將驗證新增至可攜式類別庫](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library)，[將驗證新增至該 iOS 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app)，[將驗證新增至 Android 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)，和[將驗證新增至 Windows 10 應用程式專案](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects)。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

在過去，行動應用程式使用內嵌的網頁檢視的身分識別提供者執行驗證。 這是不再建議原因如下：

- 裝載 web 檢視的應用程式可以存取使用者的完整驗證認證，不只適用於應用程式授權授與。 這樣會違反原則的最小權限，因為應用程式具有存取比所需，可能會增加受攻擊面，應用程式的功能更強大的認證。
- 主應用程式無法擷取使用者名稱和密碼、 自動送出表單和略過使用者同意，以及複製工作階段 cookie，並用來以使用者身分執行已驗證的動作。
- 內嵌的網頁檢視不與其他應用程式或裝置的網頁瀏覽器，要求登入為每個授權要求會被視為較差的使用者經驗的使用者共用的驗證狀態。
- 有些授權端點採取步驟來偵測及阻擋來自 web 檢視的授權要求。

替代方案是使用裝置的 web 瀏覽器來執行驗證，這是最新版本所採用的方法[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。 使用驗證需求的裝置瀏覽器改善應用程式的可用性，因為使用者只需要登入身分識別提供者一次每個裝置，改善應用程式中的登入和授權流程轉換率。 裝置瀏覽器也提供改善的安全性，因為應用程式都可以檢查和修改內容，在 web 檢視中，但不是在瀏覽器中顯示的內容。

## <a name="using-an-azure-mobile-apps-instance"></a>使用 Azure 行動應用程式執行個體

[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`MobileServiceClient`Xamarin.Forms 應用程式用來存取 Azure 行動應用程式執行個體的類別。

範例應用程式使用 Google 身分識別提供者，可讓使用者使用 Google 帳戶，登入 Xamarin.Forms 應用程式。 Google 身分識別提供者，在本文中使用，而方法也同樣適用於其他身分識別提供者。

<a name="logging-in" />

### <a name="logging-in-users"></a>登入使用者

範例應用程式中的登入畫面如下列螢幕擷取畫面所示：

![](azure-images/login.png "登入頁面")

雖然使用 Google 身分識別提供者，可以在使用各種不同的其他身分識別提供者包括 Facebook、 Microsoft、 Twitter 和 Azure Active Directory。

下列程式碼範例示範如何叫用的登入程序：

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

`App.Authenticator`屬性是`IAuthenticate`由每個平台專屬專案所設定的執行個體。 `IAuthenticate`介面會指定`AuthenticateAsync`必須在每個平台專案所提供的作業。 因此，叫用`App.Authenticator.AuthenticateAsync`方法執行`IAuthenticate.AuthenticateAsync`平台專案中的方法。

所有平台`IAuthenticate.AuthenticateAsync`方法呼叫`MobileServiceClient.LoginAsync`方法，以顯示 登入介面與快取資料。 下列程式碼範例示範`LoginAsync`iOS 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

下列程式碼範例示範`LoginAsync`Android 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

下列程式碼範例示範`LoginAsync`通用 Windows 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

在所有平台，`MobileServiceAuthenticationProvider`列舉型別用來指定將用於身分識別提供者驗證程序。 當`MobileServiceClient.LoginAsync`叫用方法，藉由顯示所選的提供者的登入頁面和所產生之驗證語彙基元成功登入身分識別提供者之後，Azure 行動應用程式會起始驗證流程。 `MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，其中會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者及使用者驗證語彙基元。 驗證權杖會納入到 Azure 行動應用程式執行個體，允許 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作所做的所有要求。

<a name="logging-out" />

### <a name="logging-out-users"></a>登出使用者

下列程式碼範例示範如何叫用登出程序：

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

`App.Authenticator`屬性是`IAuthenticate`由每個 platformproject 所設定的執行個體。 `IAuthenticate`介面會指定`LogoutAsync`必須在每個平台專案所提供的作業。 因此，叫用`App.Authenticator.LogoutAsync`方法執行`IAuthenticate.LogoutAsync`平台專案中的方法。

所有平台`IAuthenticate.LogoutAsync`方法呼叫`MobileServiceClient.LogoutAsync`方法來取消驗證登入的使用者身分識別提供者。 下列程式碼範例示範`LogoutAsync`iOS 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

下列程式碼範例示範`LogoutAsync`Android 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

下列程式碼範例示範`LogoutAsync`通用 Windows 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

當`IAuthenticate.LogoutAsync`叫用方法時，身分識別提供者來設定任何 cookie 已清除，之前`MobileServiceClient.LogoutAsync`取消驗證登入的使用者身分識別提供者會叫用方法。

## <a name="summary"></a>總結

本文說明如何使用 Azure 行動應用程式來管理驗證程序，在 Xamarin.Forms 應用程式。 Azure 行動應用程式會使用各種不同的外部識別提供者來支援應用程式的使用者，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory 驗證和授權。 `MobileServiceClient`類別可由 Xamarin.Forms 應用程式來控制存取權的 Azure 行動應用程式執行個體。


## <a name="related-links"></a>相關連結

- [TodoAzureAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [將驗證新增至 Xamarin.Forms 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
