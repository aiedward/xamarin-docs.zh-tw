---
title: "Azure 行動應用程式與整合 Azure Active Directory B2C"
description: "Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。 本文示範如何使用 Azure Active Directory B2C xamarin.forms 提供驗證和授權 Azure 行動應用程式執行個體。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 3a7d89d9b0f383d365b18364e5d902ee0642f395
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>Azure 行動應用程式與整合 Azure Active Directory B2C

_Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。本文示範如何使用 Azure Active Directory B2C xamarin.forms 提供驗證和授權 Azure 行動應用程式執行個體。_

![](~/media/shared/preview.png "這個 API 已發行目前前")

> [!NOTE]
> **請注意**: [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)仍處於 preview 階段，但適合在生產環境中使用。 不過，那里可能重大變更 API、 內部快取格式，可能會影響您的應用程式的程式庫的其他機制。

## <a name="overview"></a>總覽

Azure 行動應用程式可讓您開發應用程式與可擴充的後端裝載在 Azure 應用程式服務中，以支援行動裝置的驗證、 離線同步處理，及推播通知。 如需有關 Azure 行動應用程式的詳細資訊，請參閱[取用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)，和[驗證 Azure 行動應用程式的使用者](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是可讓取用者登入您的應用程式的身分識別管理服務消費者導向應用程式，請：

- 使用其現有的社交帳戶 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。 這些認證會參照為*本機*帳戶。

如需有關 Azure Active Directory B2C 的詳細資訊，請參閱[驗證使用者與 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

Azure Active Directory B2C 可以用來管理 Azure 行動應用程式的驗證工作流程。 使用此方法時，身分識別管理體驗完全在雲端中，定義，並可以修改而不需要變更您的行動應用程式程式碼。

有兩個可以整合 Azure Active Directory B2C 租用戶與 Azure 行動應用程式執行個體時採用的驗證工作流程：

- [用戶端管理](#client_managed)– 在這會很接近 Xamarin.Forms 行動應用程式會起始驗證處理與 Azure Active Directory B2C 租用戶，並將收到的驗證語彙基元傳遞至 Azure 行動應用程式執行個體。
- [伺服器管理](#server_managed)– 這種方法時 Azure 行動應用程式執行個體，請使用起始驗證程序，透過 web 架構的工作流程的 Azure Active Directory B2C 租用戶。

在這兩種情況下，由 Azure Active Directory B2C 租用戶提供的驗證體驗。 在範例應用程式中，這會導致登入畫面中的下列螢幕擷取畫面所示：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登入頁面")

登入與社交身分識別提供者，或使用本機帳戶，會允許。 Microsoft、 Google、 和 Facebook 搭配使用時即社交身分識別提供者在此範例中，可以也使用其他身分識別提供者。

## <a name="setup"></a>安裝程式

不論使用的驗證工作流程，整合 Azure Active Directory B2C 租用戶與 Azure 行動應用程式執行個體的初始程序如下所示：

1. 建立 Azure 行動應用程式執行個體。 如需詳細資訊，請參閱[取用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 啟用 Azure 行動應用程式執行個體和 Xamarin.Forms 應用程式中的驗證。 如需詳細資訊，請參閱[驗證 Azure 行動應用程式的使用者](~/xamarin-forms/data-cloud/authentication/azure.md)。
1. 建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱[驗證使用者與 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

請注意，Microsoft 驗證程式庫 (MSAL) 需要使用用戶端管理的驗證工作流程時。 MSAL 使用裝置的網頁瀏覽器來執行驗證。 這會改善應用程式的可用性，因為使用者只需要登入之後的每個裝置，改善匯率的登入和授權流程應用程式中。 裝置瀏覽器也提供改善的安全性。 使用者完成驗證程序之後，控制會傳回至應用程式，從 web 瀏覽器 索引標籤。達到此目的，請註冊自訂的 URL 配置所傳回的驗證程序，然後偵測並一旦傳送時，處理自訂的 URL 重新導向 URL。 如需使用 MSAL 通訊與 Azure Active Directory B2C 租用戶的詳細資訊，請參閱[驗證使用者與 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

<a name="client_managed" />

## <a name="client-managed-authentication"></a>管理用戶端驗證

管理用戶端驗證，在 Xamarin.Forms 行動應用程式會連絡起始驗證流程的 Azure Active Directory B2C 租用戶。 之後成功登入 Azure Active Directory B2C 租用戶傳回然後登入 Azure 行動應用程式執行個體期間提供識別權杖。 這可讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>設定 azure Active Directory B2C 租用戶

用於管理用戶端的驗證工作流程應，如下所示設定 Azure Active Directory B2C 租用戶：

- 包含原生用戶端。
- 設定自訂重新導向 URI 來唯一識別行動應用程式，後面接著 URL 配置`://auth/`。 如需有關如何選擇自訂 URL 配置的詳細資訊，請參閱[選擇原生應用程式重新導向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri)。

下列螢幕擷取畫面會示範這項設定：

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Azure Active Directory B2C Configuration")](azure-ad-b2c-mobile-app-images/client-flow-config.png "Azure Active Directory B2C Configuration")

使用中，回覆 URL 設為相同的自訂 URL 配置，以便租用戶也必須設定 Azure Active Directory B2C 的原則後面`://auth/`。 下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Azure Active Directory B2C 原則")

### <a name="azure-mobile-app-configuration"></a>Azure 行動應用程式組態

管理用戶端的驗證工作流程應，如下所示設定 Azure 行動應用程式執行個體：

- 應該開啟應用程式服務驗證。
- 當要求未經驗證時要採取的動作應該設定為**登入 Azure Active Directory**。

下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Azure 行動應用程式組態")

Azure 行動應用程式執行個體也應該設定為與 Azure Active Directory B2C 租用戶進行通訊。 這可藉由啟用**進階**Azure Active Directory 驗證提供者的模式與**用戶端識別碼**正在**應用程式識別碼**Azure 的Active Directory B2C 租用戶，而**簽發者 Url**正在 Azure Active Directory B2C 原則的中繼資料端點。 下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "進階組態的 azure 行動應用程式")

### <a name="signing-in"></a>登入

下列程式碼範例會示範如何在起始用戶端管理的驗證工作流程：

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

Microsoft 驗證程式庫 (MSAL) 用來起始驗證工作流程與 Azure Active Directory B2C 租用戶中。 `AcquireTokenAsync`方法會啟動裝置的網頁瀏覽器，並顯示所指定原則參考透過 Azure Active Directory B2C 原則中定義的驗證選項`Constants.Authority`常數。 此原則會定義取用者會在註冊過程，和登入體驗和應用程式將會收到在成功註冊或登入的宣告。

結果`AcquireTokenAsync`方法呼叫是`AuthenticationResult`執行個體。 如果驗證成功，`AuthenticationResult`執行個體會包含識別權杖會在本機快取。 如果驗證不成功，`AuthenticationResult`執行個體會包含資料，表示驗證失敗的原因。 如需如何使用 MSAL 通訊與 Azure Active Directory B2C 租用戶資訊，請參閱[驗證使用者與 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

當`MobileServiceClient.LoginAsync`叫用方法時，Azure 行動應用程式執行個體有收到包裝在身分識別語彙基元`JObject`。 有效的語彙基元表示 Azure 行動應用程式執行個體不需要起始自己的 OAuth 2.0 驗證流程存在。 相反地，`MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，其中會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和驗證權杖的使用者，可以使用，直到過期為止。 驗證權杖會納入到 Azure 行動應用程式執行個體，允許 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作所做的所有要求。

### <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用用戶端管理登出程序：

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

`MobileServiceClient.LogoutAsync`方法取消驗證使用者與 Azure 行動應用程式執行個體，則會從 MSAL 所建立的本機快取清除所有的驗證權杖。

<a name="server_managed" />

## <a name="server-managed-authentication"></a>管理伺服器的驗證

在 管理伺服器的驗證 Xamarin.Forms 應用程式，請連絡 Azure 行動應用程式執行個體，藉由顯示 B2C 原則中所定義的登入頁面上管理的 OAuth 2.0 驗證流程中使用 Azure Active Directory B2C 租用戶。 之後成功登入，Azure 行動應用程式執行個體傳回允許 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作的語彙基元。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>設定 azure Active Directory B2C 租用戶

管理伺服器的驗證工作流程應，如下所示設定 Azure Active Directory B2C 租用戶：

- 包含 web 應用程式/web API，並允許隱含的流程。
- 回覆 URL 設為 Azure 行動應用程式的位址，後面接著`/.auth/login/aad/callback`。

下列螢幕擷取畫面會示範這項設定：

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Azure Active Directory B2C Configuration")](azure-ad-b2c-mobile-app-images/server-flow-config.png "Azure Active Directory B2C Configuration")

使用中租用戶也必須設定，讓 Azure 行動應用程式的位址設定回覆 URL 是 Azure Active Directory B2C 的原則後面`/.auth/login/aad/callback`。 下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Azure Active Directory B2C 原則")

### <a name="azure-mobile-apps-instance-configuration"></a>Azure 行動應用程式執行個體組態

管理伺服器的驗證工作流程應，如下所示設定 Azure 行動應用程式執行個體：

- 應該開啟應用程式服務驗證。
- 當要求未經驗證時要採取的動作應該設定為**登入 Azure Active Directory**。

下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Azure 行動應用程式組態")

Azure 行動應用程式執行個體也應該設定為與 Azure Active Directory B2C 租用戶進行通訊。 這可藉由啟用**進階**Azure Active Directory 驗證提供者的模式與**用戶端識別碼**正在**應用程式識別碼**Azure 的Active Directory B2C 租用戶，而**簽發者 Url**正在 Azure Active Directory B2C 原則的中繼資料端點。 下列螢幕擷取畫面會示範這項設定：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "進階組態的 azure 行動應用程式")

### <a name="signing-in"></a>登入

下列程式碼範例示範如何啟動管理伺服器的驗證工作流程：

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

當`MobileServiceClient.LoginAsync`叫用方法時，Azure 行動應用程式執行個體執行此連結的 Azure Active Directory B2C 原則，後者再起始的 OAuth 2.0 驗證流程。 請注意，每個`AuthenticateAsync`方法適用於平台。 不過，每個`AuthenticateAsync`方法會使用`MobileServiceClient.LoginAsync`方法和指定的 Azure Active Directory 租用戶將用於驗證程序。 如需詳細資訊，請參閱[登入使用者](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`MobileServiceClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，其中會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和驗證權杖的使用者，可以使用，直到過期為止。 驗證權杖會納入到 Azure 行動應用程式執行個體，允許 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure 行動應用程式執行個體上執行動作所做的所有要求。

### <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用的管理伺服器的登入處理：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消驗證使用者與 Azure 行動應用程式執行個體。 如需詳細資訊，請參閱[記錄出使用者](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out)。

## <a name="summary"></a>總結

本文示範如何使用 Azure Active Directory B2C xamarin.forms 提供驗證和授權 Azure 行動應用程式執行個體。 Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。


## <a name="related-links"></a>相關連結

- [TodoAzureAuth ServerFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [驗證使用者與 Azure 行動應用程式](~/xamarin-forms/data-cloud/authentication/azure.md)
- [驗證使用者與 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)
