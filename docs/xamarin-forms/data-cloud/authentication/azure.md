---
title: 使用 Azure 行動應用程式驗證使用者
description: 這篇文章說明如何使用 Azure Mobile Apps 管理驗證程序，在 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331313"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>使用 Azure 行動應用程式驗證使用者

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Azure Mobile Apps 會使用各種外部識別提供者來支援應用程式使用者，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory 驗證和授權。資料表可以設定權限，限制只有已驗證使用者的存取。這篇文章說明如何使用 Azure Mobile Apps 管理驗證程序，在 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

具有 Azure Mobile Apps 管理驗證程序，在 Xamarin.Forms 應用程式中的程序如下所示：

1. 註冊身分識別提供者的站台的 Azure 行動應用程式，然後設定 Mobile Apps 後端中的 提供者產生的認證。 如需詳細資訊，請參閱 <<c0> [ 註冊應用程式進行驗證，並設定應用程式服務](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services)。
1. 定義新的 URL 配置，Xamarin.Forms 應用程式，可讓驗證系統驗證程序完成之後，重新導向回到 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱 <<c0> [ 將您的應用程式新增至允許的外部重新導向 Url](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl)。
1. 只有經過驗證的用戶端，請限制對 Azure Mobile Apps 後端的存取。 如需詳細資訊，請參閱 <<c0> [ 限制已驗證的使用者權限](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users)。
1. 叫用從 Xamarin.Forms 應用程式的驗證。 如需詳細資訊，請參閱 <<c0> [ 將驗證新增至可攜式類別庫](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library)，[將驗證新增至 iOS 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app)，[將驗證新增至 Android 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)，和[將驗證新增至 Windows 10 應用程式專案](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects)。

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

在過去，行動應用程式也使用內嵌的 web 檢視，來執行驗證與身分識別提供者。 這不會再建議，原因如下：

- 裝載 web 檢視的應用程式可以存取使用者的完整驗證認證，不只是適用於應用程式的授權授與。 應用程式在更強大的認證比所需，可能會增加受攻擊面，應用程式的存取權，這樣會違反最低權限的原則。
- 主應用程式無法擷取使用者名稱和密碼、 自動送出表單和略過使用者同意，並複製工作階段 cookie，並用來以使用者身分執行已驗證的動作。
- 內嵌的 web 檢視不分享其他應用程式或裝置的網頁瀏覽器，要求會被視為較差的使用者體驗的每個授權要求的登入使用者的驗證狀態。
- 某些授權端點會採取步驟來偵測並封鎖來自 web 檢視的授權要求。

替代方法是使用裝置的網頁瀏覽器來執行驗證，也就是最新版本所採用的方法[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。 使用適用於驗證要求的裝置瀏覽器可改善應用程式的可用性，因為使用者只需要登入身分識別提供者一次每個裝置，改善應用程式中的登入和授權流程的轉換率。 裝置的瀏覽器也提供改良的安全性，因為應用程式都能檢查及修改內容，在 web 檢視中，但不是顯示在瀏覽器的內容。

## <a name="using-an-azure-mobile-apps-instance"></a>使用 Azure 行動應用程式執行個體

[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`MobileServiceClient`類別，可供 Xamarin.Forms 應用程式用來存取 Azure Mobile Apps 執行個體。

範例應用程式會使用 Google 作為身分識別提供者，允許使用者使用 Google 帳戶登入 Xamarin.Forms 應用程式。 雖然 Google 作為身分識別提供者，這篇文章中，方法是同樣適用於其他識別提供者。

<a name="logging-in" />

### <a name="logging-in-users"></a>登入使用者

範例應用程式中的 [登入] 畫面如下列螢幕擷取畫面所示：

![](azure-images/login.png "登入頁面")

Google 作為身分識別提供者，而各種其他身分識別提供者可以使用，包括 Facebook、 Microsoft、 Twitter 和 Azure Active Directory。

下列程式碼範例示範如何叫用登入程序：

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

`App.Authenticator`屬性是`IAuthenticate`由每個平台特定專案所設定的執行個體。 `IAuthenticate`介面會指定`AuthenticateAsync`必須由每個平台專案提供的作業。 因此，叫用`App.Authenticator.AuthenticateAsync`方法執行`IAuthenticate.AuthenticateAsync`平台專案中的方法。

所有平台`IAuthenticate.AuthenticateAsync`方法會呼叫`MobileServiceClient.LoginAsync`方法，以顯示登入介面和快取資料。 下列程式碼範例示範`LoginAsync`iOS 平台的方法：

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

在所有平台，`MobileServiceAuthenticationProvider`列舉型別用來在驗證程序中指定將用於身分識別提供者。 當`MobileServiceClient.LoginAsync`叫用方法時，Azure Mobile Apps 會藉由顯示所選的提供者的登入頁面，並藉由使用身分識別提供者的成功登入後產生驗證權杖，會起始驗證流程。 `MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，將會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性會提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和使用者驗證權杖。 驗證權杖會納入為 Azure Mobile Apps 執行個體，讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作所做的所有要求。

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

`App.Authenticator`屬性是`IAuthenticate`由每個 platformproject 所設定的執行個體。 `IAuthenticate`介面會指定`LogoutAsync`必須由每個平台專案提供的作業。 因此，叫用`App.Authenticator.LogoutAsync`方法執行`IAuthenticate.LogoutAsync`平台專案中的方法。

所有平台`IAuthenticate.LogoutAsync`方法會呼叫`MobileServiceClient.LogoutAsync`方法來取消驗證身分識別提供者的登入的使用者。 下列程式碼範例示範`LogoutAsync`iOS 平台的方法：

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

當`IAuthenticate.LogoutAsync`叫用方法時，設定身分識別提供者的任何 cookie 已清除，之前`MobileServiceClient.LogoutAsync`取消驗證登入的使用者身分識別提供者會叫用方法。

## <a name="summary"></a>總結

這篇文章說明如何使用 Azure 行動應用程式管理驗證程序，在 Xamarin.Forms 應用程式。 Azure Mobile Apps 會使用各種外部識別提供者來支援應用程式使用者，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory 驗證和授權。 `MobileServiceClient`類別用於 Xamarin.Forms 應用程式所控制的存取權的 Azure Mobile Apps 執行個體。


## <a name="related-links"></a>相關連結

- [TodoAzureAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [將驗證新增至 Xamarin.Forms 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
