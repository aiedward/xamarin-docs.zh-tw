---
title: 驗證使用者與 Azure Active Directory B2C
description: Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。 本文示範如何使用 Microsoft 驗證程式庫和 Azure Active Directory B2C 將取用者身分識別管理整合到行動裝置應用程式。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: f17a6ad012aff81674db943b7d65e65ba77dca52
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>驗證使用者與 Azure Active Directory B2C

_Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。本文示範如何使用 Microsoft 驗證程式庫和 Azure Active Directory B2C 將取用者身分識別管理整合到行動裝置應用程式。_

![](~/media/shared/preview.png "這個 API 已發行目前前")

> [!NOTE]
> [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)仍處於 preview 階段，但適合在生產環境中使用。 不過，那里可能重大變更 API、 內部快取格式，可能會影響您的應用程式的程式庫的其他機制。

## <a name="overview"></a>總覽

Azure Active Directory B2C 是可讓取用者登入您的應用程式的身分識別管理服務消費者導向應用程式，請：

- 使用其現有的社交帳戶 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。 這些認證會參照為*本機*帳戶。

將 Azure Active Directory B2C 身分識別管理服務整合至行動應用程式的程序如下所示：

1. 建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱[在 Azure 入口網站中建立的 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/active-directory-b2c-get-started/)。
1. 註冊行動裝置應用程式與 Azure Active Directory B2C 租用戶。 註冊程序將指派**應用程式識別碼**可唯一識別您的應用程式和**重新導向 URL** ，可用來直接回應給您的應用程式。 如需詳細資訊，請參閱[Azure Active Directory B2C： 註冊您的應用程式](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。
1. 建立註冊和登入的原則。 此原則會定義取用者註冊和登入期間，將會瀏覽體驗和應用程式將會收到的權杖的內容也會指定在成功註冊或登入。 如需詳細資訊，請參閱[Azure Active Directory B2C： 內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。
1. 使用[Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)(MSAL) 行動裝置應用程式與 Azure Active Directory B2C 租用戶起始驗證工作流程中。

> [!NOTE]
> 將 Azure Active Directory B2C 身分識別管理整合到行動裝置應用程式，以及 MSAL 也可用來將 Azure Active Directory 身分識別管理整合到行動裝置應用程式。 這可以透過行動應用程式向 Azure Active Directory 在[應用程式註冊入口網站](https://apps.dev.microsoft.com/)。 註冊程序將指派**應用程式識別碼**可唯一識別，使用 MSAL 時應指定此應用程式。 如需詳細資訊，請參閱[如何註冊應用程式與 v2.0 端點](/azure/active-directory/develop/active-directory-v2-app-registration/)，和[驗證您行動應用程式使用 Microsoft 驗證程式庫](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/)Xamarin 部落格上。

MSAL 使用裝置的網頁瀏覽器來執行驗證。 這會改善應用程式的可用性，因為使用者只需要登入之後的每個裝置，改善匯率的登入和授權流程應用程式中。 裝置瀏覽器也提供改善的安全性。 使用者完成驗證程序之後，控制會傳回至應用程式，從 web 瀏覽器 索引標籤。達到此目的，請註冊自訂的 URL 配置所傳回的驗證程序，然後偵測並一旦傳送時，處理自訂的 URL 重新導向 URL。 如需有關如何選擇自訂 URL 配置的詳細資訊，請參閱[選擇原生應用程式重新導向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/)。

> [!NOTE]
> 向作業系統註冊自訂的 URL 配置，以及處理結構描述的機制是每個平台專屬的。

每個要求傳送至 Azure Active Directory B2C 租用戶指定*原則*。 原則描述取用者身分識別體驗，例如註冊，或登入。 例如，註冊原則可讓透過下列設定來設定 Azure Active Directory B2C 租用戶的行為：

- 取用者可以使用登入應用程式的帳戶類型。
- 收集資料需要從取用者在註冊期間。
- 多重要素驗證。
- 註冊頁面內容。
- 行動應用程式接收時已執行原則的權杖宣告。

Azure Active Directory 租用戶可以包含多個不同的類型，然後可以視需要在應用程式中使用的原則。 此外，原則可以跨應用程式，可讓您定義及修改取用者身分識別的經驗，而不需要變更您的程式碼重複使用。 如需原則的詳細資訊，請參閱[Azure Active Directory B2C： 內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

## <a name="setup"></a>安裝程式

Microsoft 驗證程式庫 (MSAL) NuGet 程式庫必須加入至可攜式類別程式庫 (PCL) 專案和 Xamarin.Forms 方案中的平台專案中。 下列章節提供使用 MSAL 通訊與 Azure Active Directory B2C 租用戶從行動應用程式的其他安裝指示。

### <a name="portable-class-library"></a>可攜式類別庫

MSAL 不支援 Windows Phone 8.1，並因此 PCLs 取用 MSAL 就必須移除此目標。 這可以透過使用 Profile7 PCLs 的重定目標。 如需有關 PCL 的詳細資訊，請參閱[可攜式類別庫簡介](~/cross-platform/app-fundamentals/pcl.md)。

### <a name="ios"></a>iOS

在 iOS 上，已向 Azure Active Directory B2C 的自訂 URL 配置必須登錄在**Info.plist**，如下列螢幕擷取畫面所示：

![](azure-ad-b2c-images/customurl-ios.png "註冊自訂的 URL 結構描述，在 iOS 上")

當 Azure Active Directory B2C 完成授權要求時，它會重新導向至已註冊的重新導向 URL。 因為 URL 中使用自訂配置，則會導致啟動行動應用程式的 iOS，在 URL 中傳遞做為啟動參數，就會處理由`OpenUrl`的應用程式的覆寫`AppDelegate`類別，下列程式碼所示範例：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

中的程式碼`OpenURL`方法可確保，控制權會傳回 MSAL 一旦驗證工作流程的互動部分已結束。

### <a name="android"></a>Android

在 Android 上，已向 Azure Active Directory B2C 的自訂 URL 配置必須登錄在**AndroidManifest.xml**，藉由新增`<activity>`內現有項目`<application>`項目。 `<activity>`項目會指定`IntentFilter`上`Activity`處理配置，而且下列範例所示：

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

當 Azure Active Directory B2C 完成授權要求時，它會重新導向至已註冊的重新導向 URL。 因為 URL 中使用自訂配置，則會導致啟動行動應用程式的 Android，在 URL 中傳遞做為啟動參數，就會處理由`microsoft.identity.client.BrowserTabActivity`。 請注意，`data android:scheme`屬性必須設定為向 Azure Active Directory B2C 應用程式的自訂 URL 配置。

此外，`MainActivity`類別必須修改，如下列程式碼範例所示：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

`OnCreate`方法修改指派`UIParent`執行個體`App.UiParent`屬性。 這可確保在目前活動的內容中的驗證流程，會發生。

中的程式碼`OnActivityResult`方法可確保，控制權會傳回 MSAL 一旦驗證工作流程的互動部分已結束。

### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台上不需要額外設定才能使用 MSAL。

## <a name="initialization"></a>初始化

Microsoft 驗證程式庫使用屬於`PublicClientApplication`起始驗證工作流程的類別。 範例應用程式會宣告並初始化`public`名為此類型的屬性`ADB2CClient`，請在`AuthenticationProvider`類別。 下列程式碼範例示範如何初始化這個屬性：

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

當行動應用程式已向 Azure Active Directory B2C 租用戶註冊時，註冊程序指派給**應用程式識別碼**。 必須指定此識別碼`PublicClientApplication`建構函式，連同`Authority`包含基底 URL，以及 Azure Active Directory B2C 原則来執行的常數。

## <a name="signing-in"></a>登入

範例應用程式中的登入 畫面是以下列螢幕擷取畫面所示：

![](azure-ad-b2c-images/login.png "登入頁面")

登入與社交身分識別提供者，或使用本機帳戶，會允許。 雖然 Microsoft，Google、 Facebook，如上所示，可用做為社交身分識別提供者，可以也使用其他身分識別提供者。

下列程式碼範例示範如何叫用的登入程序：

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

`AcquireTokenAsync`方法會啟動裝置的網頁瀏覽器，並顯示所指定原則參考透過 Azure Active Directory B2C 原則中定義的驗證選項`Constants.Authority`常數。 此原則會定義取用者會在註冊過程，和登入體驗和應用程式將會收到在成功註冊或登入的宣告。

結果`AcquireTokenAsync`方法呼叫是`AuthenticationResult`執行個體。 如果驗證成功，`AuthenticationResult`執行個體會包含識別權杖會在本機快取。 如果驗證不成功，`AuthenticationResult`執行個體會包含資料，表示驗證失敗的原因。

在範例應用程式，如果驗證成功，`TodoList`巡覽到頁面時。

## <a name="silent-re-authentication"></a>無訊息的重新驗證

當`LoginPage`在此範例應用程式會出現，嘗試擷取使用者權杖，而不會顯示任何驗證的使用者介面。 這與達成`AcquireTokenSilentAsync`方法，如下列程式碼範例所示：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

`AcquireTokenSilentAsync`方法會嘗試從快取中，擷取使用者權杖，而不需要使用者登入。 這會處理其中適合的語彙基元可能已經會出現在快取先前工作階段的案例。 如果成功，嘗試取得權杖`TodoList`巡覽到頁面時。 如果嘗試取得語彙基元不成功，會發生任何事，使用者必須選擇要起始新的驗證工作流程。

## <a name="signing-out"></a>登出

下列程式碼範例示範如何叫用登出程序：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

這會清除本機快取的所有驗證語彙基元。

## <a name="summary"></a>總結

本文示範如何使用 Microsoft 驗證程式庫 (MSAL) 和 Azure Active Directory B2C 將取用者身分識別管理整合到行動裝置應用程式。 Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。


## <a name="related-links"></a>相關連結

- [AzureADB2CAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)
