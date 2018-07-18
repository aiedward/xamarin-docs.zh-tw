---
title: 使用 Azure Mobile Apps 的整合 Azure Active Directory B2C
description: Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。 這篇文章會示範如何使用 Azure Active Directory B2C 來提供驗證和授權的 Azure Mobile Apps 執行個體使用 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: cafc1e78779dc393fa0409daa08b3daa8948a1ee
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815673"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>使用 Azure Mobile Apps 的整合 Azure Active Directory B2C

_Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。這篇文章會示範如何使用 Azure Active Directory B2C 來提供驗證和授權的 Azure Mobile Apps 執行個體使用 Xamarin.Forms。_

![](~/media/shared/preview.png "此 API 是目前發行前版本")

> [!NOTE]
> [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)仍處於預覽狀態，但適合在生產環境中使用。 不過，那里可能重大變更的 API、 內部快取格式，以及其他的程式庫，可能會影響您的應用程式的機制。

## <a name="overview"></a>總覽

Azure Mobile Apps 可讓您使用與行動裝置的驗證、 離線同步處理和推播通知支援裝載在 Azure App Service 中的可擴充後端開發應用程式。 如需有關 Azure Mobile Apps 的詳細資訊，請參閱 <<c0> [ 使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)，並[驗證的使用者，使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是可讓取用者登入您的應用程式的身分識別管理服務取用者導向應用程式，如：

- 使用現有的社交帳戶 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。 這些認證指*本機*帳戶。

如需有關 Azure Active Directory B2C 的詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

Azure Active Directory B2C 可用來管理 Azure 行動應用程式的驗證工作流程。 使用此方法時，身分識別管理體驗在雲端中完整定義，並可以進行修改，而不需要變更您的行動應用程式程式碼。

有兩個可以在整合 Azure Active Directory B2C 租用戶與 Azure Mobile Apps 執行個體時採用的驗證工作流程：

- [用戶端管理](#client_managed)– 在此方法的驗證程序與 Azure Active Directory B2C 租用戶，Xamarin.Forms 行動應用程式起始，並將收到的驗證權杖傳遞至 Azure Mobile Apps 執行個體。
- [伺服器管理](#server_managed)– 在這種方法將 Azure Mobile Apps 執行個體，請使用起始驗證程序，透過 web 為基礎的工作流程的 Azure Active Directory B2C 租用戶。

在這兩種情況下，Azure Active Directory B2C 租用戶所提供的驗證體驗。 在範例應用程式中，這會導致登入畫面中的下列螢幕擷取畫面所示：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登入頁面")

登入與社交識別提供者，或使用本機帳戶、 允許的。 雖然 Microsoft、 Google 和 Facebook 社交識別提供者，在此範例中所做，可以也使用其他身分識別提供者。

## <a name="setup"></a>安裝程式

不論使用的驗證工作流程，整合 Azure Active Directory B2C 租用戶與 Azure Mobile Apps 執行個體的初始程序如下所示：

1. 建立 Azure Mobile Apps 執行個體。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 啟用 Azure 行動應用程式執行個體和 Xamarin.Forms 應用程式中的驗證。 如需詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。
1. 建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

請注意，Microsoft Authentication Library (MSAL) 時，必須使用用戶端管理的驗證工作流程。 MSAL 會使用裝置的網頁瀏覽器來執行驗證。 這可改善應用程式的可用性，因為使用者只需要登入之後每個裝置，提高轉換率的登入和授權流程的應用程式中。 裝置的瀏覽器也會提供改進的安全性。 使用者完成驗證程序之後，控制項將會傳回給應用程式從 web 瀏覽器索引標籤。這之後，即可註冊重新導向 URL 時所傳回的驗證程序，然後偵測並處理自訂的 URL 之後它會傳送, 自訂的 URL 配置。 如需使用 MSAL 來與 Azure Active Directory B2C 租用戶進行通訊的詳細資訊，請參閱[驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

<a name="client_managed" />

## <a name="client-managed-authentication"></a>用戶端管理的驗證

在用戶端管理的驗證中，Xamarin.Forms 行動應用程式，請連絡 Azure Active Directory B2C 租用戶，以起始驗證流程。 之後成功登入 Azure Active Directory B2C 租用戶會傳回再登入 Azure Mobile Apps 執行個體期間所提供的身分識別權杖。 這可讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure Mobile Apps 執行個體上執行動作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租用戶設定

如需用戶端管理的驗證工作流程中，應，如下所示設定 Azure Active Directory B2C 租用戶：

- 包含原生用戶端。
- 設定自訂重新導向 URI 可唯一識別行動裝置應用程式，後面接著的 URL 配置`://auth/`。 如需有關選擇自訂 URL 配置的詳細資訊，請參閱[選擇原生應用程式重新導向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri)。

下列螢幕擷取畫面示範這項設定：

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Azure Active Directory B2C 設定")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "Azure Active Directory B2C 設定")

後面接著以便回覆 URL 設為相同的自訂 URL 配置，也必須設定租用戶 Azure Active Directory B2C 中使用的原則`://auth/`。 下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Azure Active Directory B2C 原則")

### <a name="azure-mobile-app-configuration"></a>Azure 行動應用程式組態

如需用戶端管理的驗證工作流程中，Azure Mobile Apps 執行個體應該如下所示設定：

- 應該開啟 app Service 驗證。
- 當要求未經驗證時要採取的動作應該設定為**登入 Azure Active Directory**。

下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Azure 行動應用程式組態")

Azure Mobile Apps 執行個體也應該設定為與 Azure Active Directory B2C 租用戶進行通訊。 這可藉由啟用**進階**模式的 Azure Active Directory 驗證提供者中，使用**用戶端識別碼**正在**應用程式識別碼**Azure 的Active Directory B2C 租用戶，而**簽發者 Url**正在 Azure Active Directory B2C 原則的中繼資料端點。 下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "進階組態的 azure 行動應用程式")

### <a name="signing-in"></a>登入

下列程式碼範例示範如何起始用戶端管理的驗證工作流程：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

Microsoft Authentication Library (MSAL) 用來與 Azure Active Directory B2C 租用戶起始的驗證工作流程中。 `AcquireTokenAsync`方法會啟動裝置的網頁瀏覽器，並顯示透過參考的原則由 Azure Active Directory B2C 原則中定義的驗證選項`Constants.Authority`常數。 此原則會定義取用者在註冊期間和登入時，會經歷的體驗以及應用程式會在成功註冊或登入的宣告。

結果`AcquireTokenAsync`方法呼叫是`AuthenticationResult`執行個體。 如果驗證成功，`AuthenticationResult`執行個體將包含識別權杖，這會在本機快取。 如果驗證不成功，`AuthenticationResult`執行個體將會包含資料，指出驗證失敗的原因。 如需如何使用 MSAL 來與 Azure Active Directory B2C 租用戶進行通訊的資訊，請參閱[驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

當`MobileServiceClient.LoginAsync`叫用方法時，Azure Mobile Apps 執行個體收到包裝在身分識別權杖`JObject`。 有效的語彙基元表示 Azure Mobile Apps 執行個體不需要起始自己的 OAuth 2.0 驗證流程的目前狀態。 相反地，`MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，將會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性會提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和驗證權杖的使用者，可以使用，直到它到期為止。 驗證權杖會納入為 Azure Mobile Apps 執行個體，讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure Mobile Apps 執行個體上執行動作所做的所有要求。

### <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用用戶端管理的登出程序：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消驗證與 Azure Mobile Apps 執行個體中，使用者，則會從 MSAL 所建立的本機快取清除所有的驗證權杖。

<a name="server_managed" />

## <a name="server-managed-authentication"></a>伺服器管理的驗證

伺服器管理的驗證，在 Xamarin.Forms 應用程式，請連絡 Azure Mobile Apps 執行個體，它會使用 Azure Active Directory B2C 租用戶中的 B2C 原則所定義，顯示登入頁面來管理 OAuth 2.0 驗證流程。 之後成功登入，Azure Mobile Apps 執行個體傳回語彙基元，讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure Mobile Apps 執行個體上執行動作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租用戶設定

伺服器管理的驗證工作流程應，如下所示設定 Azure Active Directory B2C 租用戶：

- 包含 web 應用程式/web API，並允許隱含流程。
- Azure 行動應用程式的位址設定回覆 URL，後面接著`/.auth/login/aad/callback`。

下列螢幕擷取畫面示範這項設定：

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Azure Active Directory B2C 設定")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "Azure Active Directory B2C 設定")

租用戶也必須設定，讓位址的 Azure 行動應用程式中，設定 回覆 URL 是 Azure Active Directory B2C 中使用的原則後面`/.auth/login/aad/callback`。 下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Azure Active Directory B2C 原則")

### <a name="azure-mobile-apps-instance-configuration"></a>Azure Mobile Apps 執行個體組態

伺服器管理的驗證工作流程，Azure Mobile Apps 執行個體應該如下所示設定：

- 應該開啟 app Service 驗證。
- 當要求未經驗證時要採取的動作應該設定為**登入 Azure Active Directory**。

下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Azure 行動應用程式組態")

Azure Mobile Apps 執行個體也應該設定為與 Azure Active Directory B2C 租用戶進行通訊。 這可藉由啟用**進階**模式的 Azure Active Directory 驗證提供者中，使用**用戶端識別碼**正在**應用程式識別碼**Azure 的Active Directory B2C 租用戶，而**簽發者 Url**正在 Azure Active Directory B2C 原則的中繼資料端點。 下列螢幕擷取畫面示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "進階組態的 azure 行動應用程式")

### <a name="signing-in"></a>登入

下列程式碼範例示範如何起始伺服器管理的驗證工作流程：

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

當`MobileServiceClient.LoginAsync`叫用方法時，Azure Mobile Apps 執行個體執行此連結的 Azure Active Directory B2C 原則，後者再起始 OAuth 2.0 驗證流程。 請注意，每個`AuthenticateAsync`方法是特定平台。 不過，每個`AuthenticateAsync`方法會使用`MobileServiceClient.LoginAsync`方法並指定 Azure Active Directory 租用戶將用於驗證程序。 如需詳細資訊，請參閱 <<c0> [ 登入使用者](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，將會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性會提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和驗證權杖的使用者，可以使用，直到它到期為止。 驗證權杖會納入為 Azure Mobile Apps 執行個體，讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure Mobile Apps 執行個體上執行動作所做的所有要求。

### <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用的伺服器管理的登出程序：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消驗證使用者的 Azure Mobile Apps 執行個體。 如需詳細資訊，請參閱 <<c0> [ 出使用者記錄](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out)。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Azure Active Directory B2C 來提供驗證和授權的 Azure Mobile Apps 執行個體使用 Xamarin.Forms。 Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。


## <a name="related-links"></a>相關連結

- [TodoAzureAuth ServerFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 行動應用程式驗證使用者](~/xamarin-forms/data-cloud/authentication/azure.md)
- [使用 Azure Active Directory B2C 驗證使用者](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
