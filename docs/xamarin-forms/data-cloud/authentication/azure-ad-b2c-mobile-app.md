---
title: 使用 Azure Mobile Apps 的整合 Azure Active Directory B2C
description: Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。 這篇文章會示範如何使用 Azure Active Directory B2C 來提供驗證和授權的 Azure Mobile Apps 執行個體使用 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 2f9cfceee4765dea946dfdac974ac2d56595ef94
ms.sourcegitcommit: 9aaae4f0af096cd136b3dcfbb9af591ba307dc25
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2019
ms.locfileid: "67398687"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>使用 Azure Mobile Apps 的整合 Azure Active Directory B2C

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure Active Directory B2C 提供雲端身分識別管理的消費者端應用程式。這篇文章說明如何使用 Azure Active Directory B2C 將身分識別管理整合到 Azure Mobile Apps 執行個體使用 Xamarin.Forms。_

## <a name="overview"></a>總覽

Azure Mobile Apps 可讓您使用 Azure App Service 中裝載的可擴充後端開發應用程式。 它支援行動裝置的驗證、 離線同步處理和推播通知。 如需有關 Azure Mobile Apps 的詳細資訊，請參閱 <<c0> [ 使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)，並[驗證的使用者，使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是取用者導向應用程式，可讓取用者身分識別管理服務：

- 使用現有的社交帳戶 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn） 登入。
- 建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。 這些認證指*本機*帳戶。

如需有關 Azure Active Directory B2C 的詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

Azure Active Directory B2C 可用來管理 Azure 行動應用程式的驗證工作流程。 此方法會設定在雲端中的身分識別管理，並允許變更，而不需修改應用程式程式碼。


Azure Mobile Apps 與 Azure Active Directory B2C 可讓兩種驗證方法：

- [用戶端管理](#client-managed-authentication)– Xamarin.Forms 行動應用程式與 Azure Active Directory B2C 租用戶，啟動驗證程序，並將收到的驗證權杖傳遞到 Azure Mobile Apps 執行個體。
- [伺服器管理](#server-managed-authentication)– Azure Mobile Apps 執行個體啟動驗證程序，透過 web 為基礎的工作流程中使用 Azure Active Directory B2C 租用戶。

在這兩種情況下，Azure Active Directory B2C 租用戶所提供的驗證體驗。 在範例應用程式的登入畫面看起來應該類似下列螢幕擷取畫面：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登入頁面")

此範例示範使用本機帳戶登入，但您也可以啟用 Microsoft、 Google、 Facebook 和其他識別提供者。

## <a name="setup"></a>安裝程式

這兩個工作流程中，使用 Azure Mobile Apps 整合的 Azure Active Directory B2C 租用戶的初始程序如下所示：

1. 在 Azure 入口網站中建立 Azure Mobile Apps 執行個體。 這會產生類似於範例專案的起始專案。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。
1. 啟用 Azure 行動應用程式執行個體和 Xamarin.Forms 應用程式中的驗證。 如需詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

> [!NOTE]
> 使用用戶端管理的驗證工作流程時，需要 Microsoft Authentication Library (MSAL)。 MSAL 會使用裝置的網頁瀏覽器來驗證。 在瀏覽器中完成驗證之後，將使用者重新導向自訂的 URL 配置。 應用程式註冊自訂的 URL 配置，使它能夠重新取得控制權的使用者體驗和回應的驗證。 如需使用 MSAL 來與 Azure Active Directory B2C 租用戶進行通訊的詳細資訊，請參閱[驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

## <a name="client-managed-authentication"></a>用戶端管理的驗證

在用戶端管理的驗證，Xamarin.Forms 應用程式，請連絡 Azure Active Directory B2C 租用戶，以開始驗證流程。 登入之後，Azure Active Directory B2C 租用戶會傳回身分識別權杖，提供給 Azure Mobile Apps 執行個體。 這可讓 Xamarin.Forms 應用程式，以執行需要已驗證的使用者權限的動作。

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Azure Active Directory B2C 用戶端管理的租用戶設定

如需用戶端管理的驗證工作流程中，應，如下所示設定 Azure Active Directory B2C 租用戶：

- 設定**包含原生用戶端**為 [是]。
- 設定自訂的重新導向 URI。 MSAL 文件建議使用"msal"結合您的應用程式識別碼，且後面接著":/ / 驗證 /"。 如需詳細資訊，請參閱 < [MSAL 用戶端應用程式重新導向 URI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri)。

下列螢幕擷取畫面示範這項設定：

[![[Azure Active Directory B2C 設定]](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "Azure Active Directory B2C 設定")

使回覆 URL 設為相同的自訂 URL 配置，也應該設定在 Azure Active Directory B2C 租用戶的登入原則。 這可藉由選取**執行使用者流程**在 Azure 入口網站中存取的原則設定。 儲存中繼資料 URL，您將需要它的行動應用程式組態，這個畫面上找到。 下列螢幕擷取畫面示範此設定，您應該將複製的 URL:

[![「 Azure Active Directory B2C 原則 」](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "Azure Active Directory B2C 原則組態")

### <a name="azure-mobile-app-configuration"></a>Azure 行動應用程式組態

如需用戶端管理的驗證工作流程中，設定 Azure Mobile Apps 執行個體如下所示：

- 應該開啟 app Service 驗證。
- 要求未通過驗證時所要採取的動作應該設定為**使用 Azure Active Directory 登入**。

下列螢幕擷取畫面示範這項設定：

[![[Azure Mobile Apps 驗證設定]](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "Azure Mobile Apps 的驗證設定")

Azure Mobile Apps 執行個體設定為 Azure Active Directory B2C 租用戶與通訊：

- 按一下 Azure Active Directory 組態，並啟用**進階**模式的 Azure Active Directory 驗證提供者。
- 設定**用戶端識別碼**要**APPLICATION-ID**的 Azure Active Directory B2C 租用戶。
- 設定**簽發者 Url**您先前在租用戶組態過程中複製的原則的中繼資料 url。

下列螢幕擷取畫面示範這項設定：

![[Azure Mobile Apps 的進階設定]](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>登入

下列程式碼範例會顯示金鑰的方法呼叫來啟動用戶端管理的驗證工作流程：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.AccessToken))
    {
        payload["access_token"] = authenticationResult.AccessToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

Microsoft Authentication Library (MSAL) 用來與 Azure Active Directory B2C 租用戶啟動驗證工作流程。 `AcquireTokenAsync`方法會啟動裝置的網頁瀏覽器，並顯示透過參考的原則由 Azure Active Directory B2C 原則中定義的驗證選項`Constants.AuthoritySignin`常數。 此原則會定義使用者的登入和註冊體驗，以及應用程式驗證成功後收到的宣告。

結果`AcquireTokenAsync`方法呼叫是`AuthenticationResult`執行個體。 如果驗證成功，`AuthenticationResult`執行個體將會包含存取權杖，這會在本機快取。 如果驗證不成功，`AuthenticationResult`執行個體將會包含資料，指出驗證失敗的原因。 如需如何使用 MSAL 來與 Azure Active Directory B2C 租用戶進行通訊的資訊，請參閱[驗證的使用者，使用 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

當`CurrentClient.LoginAsync`叫用方法時，Azure Mobile Apps 執行個體接收存取權杖，其包裝在`JObject`。 有效的語彙基元表示 Azure Mobile Apps 執行個體不需要啟動它自己的 OAuth 2.0 驗證流程的目前狀態。 相反地，`CurrentClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，將會儲存在`MobileServiceClient.CurrentUser`屬性。 這個屬性會提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表的已驗證的使用者和權杖，可以使用，直到它到期為止。 驗證權杖會納入 Azure Mobile Apps 的執行個體，讓 Xamarin.Forms 應用程式，以執行需要已驗證的使用者權限的動作所做的所有要求。

### <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用用戶端管理的登出程序：

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

`CurrentClient.LogoutAsync`方法取消驗證與 Azure Mobile Apps 執行個體中，使用者，則會從 MSAL 所建立的本機快取清除所有的驗證權杖。

## <a name="server-managed-authentication"></a>伺服器管理的驗證

伺服器管理的驗證，在 Xamarin.Forms 應用程式，請連絡 Azure Mobile Apps 執行個體，它會使用 Azure Active Directory B2C 租用戶中的 B2C 原則所定義，顯示登入頁面來管理 OAuth 2.0 驗證流程。 之後成功登入，Azure Mobile Apps 執行個體傳回可讓 Xamarin.Forms 應用程式來執行動作需要已驗證的使用者權限的權杖。

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Azure Active Directory B2C 伺服器管理的租用戶設定

伺服器管理的驗證工作流程應，如下所示設定 Azure Active Directory B2C 租用戶：

- 包含 web 應用程式/web API，並允許隱含流程。
- Azure 行動應用程式的位址設定回覆 URL，後面接著`/.auth/login/aad/callback`。

下列螢幕擷取畫面示範這項設定：

[![[Azure Active Directory B2C 設定]](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "Azure Active Directory B2C 設定")

使用 Azure Active Directory B2C 租用戶中的原則必須設定的回覆 URL。 做法是藉由設定 回覆 URL 位址的 Azure 行動應用程式，後面接著`/.auth/login/aad/callback`。 您也應該儲存中繼資料 URL，因為您的行動裝置應用程式組態需要它，請參閱此畫面的頂端。 下列螢幕擷取畫面示範此設定，您應該將儲存的中繼資料 URL:

![[Azure Active Directory B2C 原則設定]](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Azure Mobile Apps 執行個體組態

伺服器管理的驗證工作流程，Azure Mobile Apps 執行個體應該如下所示設定：

- 應該開啟 app Service 驗證。
- 要求未通過驗證時所要採取的動作應該設定為**使用 Azure Active Directory 登入**。

下列螢幕擷取畫面示範這項設定：

[![[Azure Mobile Apps 驗證設定]](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "Azure Mobile Apps 的驗證設定")

Azure Mobile Apps 執行個體也必須設定 Azure Active Directory B2C 租用戶與通訊：

- 按一下 Azure Active Directory 組態，並啟用**進階**模式的 Azure Active Directory 驗證提供者。
- 設定**用戶端識別碼**要**APPLICATION-ID**的 Azure Active Directory B2C 租用戶。
- **簽發者 Url**是您先前在租用戶組態過程中複製的原則的中繼資料 URL。

下列螢幕擷取畫面示範這項設定：

![[Azure Mobile Apps 的進階設定]](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>登入

下列程式碼範例示範如何啟動的伺服器管理的驗證工作流程：

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

當`CurrentClient.LoginAsync`叫用方法時，Azure Mobile Apps 執行個體執行連結的 Azure Active Directory B2C 原則啟動 OAuth 2.0 驗證流程。 每個`AuthenticateAsync`方法是特定平台。 不過，每個`AuthenticateAsync`方法呼叫`CurrentClient.LoginAsync`方法並指定 Azure Active Directory 租用戶將用於驗證程序。 如需詳細資訊，請參閱 <<c0> [ 登入使用者](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`CurrentClient.LoginAsync`方法會傳回`MobileServiceUser`執行個體，將會儲存在`CurrentClient.CurrentUser`屬性。 這個屬性會提供`UserId`和`MobileServiceAuthenticationToken`屬性。 這些代表已驗證的使用者和驗證權杖的使用者，可以使用，直到它到期為止。 驗證權杖會納入為 Azure Mobile Apps 執行個體，讓 Xamarin.Forms 應用程式需要已驗證的使用者權限的 Azure Mobile Apps 執行個體上執行動作所做的所有要求。

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


## <a name="related-links"></a>相關連結

- [TodoAzureAuth ClientFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 行動應用程式驗證使用者](~/xamarin-forms/data-cloud/authentication/azure.md)
- [使用 Azure Active Directory B2C 驗證使用者](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft Authentication Library nuget 套件](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft Authentication Library 文件](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
