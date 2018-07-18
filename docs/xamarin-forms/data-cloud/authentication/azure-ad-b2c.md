---
title: 使用 Azure Active Directory B2C 驗證使用者
description: Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。 這篇文章會示範如何使用 Microsoft 驗證程式庫和 Azure Active Directory B2C 整合到行動應用程式的取用者身分識別管理。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b6989782c438ec41911cc9317d9f911d6518132d
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38872719"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 驗證使用者

_Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。這篇文章會示範如何使用 Microsoft 驗證程式庫和 Azure Active Directory B2C 整合到行動應用程式的取用者身分識別管理。_

![](~/media/shared/preview.png "此 API 是目前發行前版本")

> [!NOTE]
> [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)仍處於預覽狀態，但適合在生產環境中使用。 不過，那里可能重大變更的 API、 內部快取格式，以及其他的程式庫，可能會影響您的應用程式的機制。

## <a name="overview"></a>總覽

Azure Active Directory B2C 是可讓取用者登入您的應用程式的身分識別管理服務取用者導向應用程式，如：

- 使用現有的社交帳戶 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。 這些認證指*本機*帳戶。

將 Azure Active Directory B2C 身分識別管理服務整合到行動應用程式的程序如下所示：

1. 建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/active-directory-b2c-get-started/)。
1. 註冊行動裝置應用程式與 Azure Active Directory B2C 租用戶。 註冊程序會指派**應用程式識別碼**可唯一識別您的應用程式，以及**重新導向 URL** ，可用來將回應導回到您的應用程式。 如需詳細資訊，請參閱 < [Azure Active Directory B2C： 註冊您的應用程式](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。
1. 建立註冊和登入原則。 這項原則會定義取用者註冊和登入期間將經歷的體驗，並也會指定的應用程式將收到的權杖內容上成功註冊或登入。 如需詳細資訊，請參閱 < [Azure Active Directory B2C： 內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。
1. 使用[Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) 行動裝置應用程式與 Azure Active Directory B2C 租用戶起始的驗證工作流程中。

> [!NOTE]
> 以及將 Azure Active Directory B2C 身分識別管理整合到行動裝置應用程式中，MSAL 也可用來將 Azure Active Directory 身分識別管理整合到行動裝置應用程式。 這可藉由與在 Azure Active Directory 中註冊的行動應用程式[應用程式註冊入口網站](https://apps.dev.microsoft.com/)。 註冊程序會指派**應用程式識別碼**可唯一識別您的應用程式，應該會在使用 MSAL 時所指定。 如需詳細資訊，請參閱 <<c0> [ 如何使用 v2.0 端點註冊 app](/azure/active-directory/develop/active-directory-v2-app-registration/)，並[驗證您行動應用程式使用 Microsoft Authentication Library](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) Xamarin 部落格上。

MSAL 會使用裝置的網頁瀏覽器來執行驗證。 這可改善應用程式的可用性，因為使用者只需要登入之後每個裝置，提高轉換率的登入和授權流程的應用程式中。 裝置的瀏覽器也會提供改進的安全性。 使用者完成驗證程序之後，控制項將會傳回給應用程式從 web 瀏覽器索引標籤。這之後，即可註冊重新導向 URL 時所傳回的驗證程序，然後偵測並處理自訂的 URL 之後它會傳送, 自訂的 URL 配置。 如需有關選擇自訂 URL 配置的詳細資訊，請參閱[選擇原生應用程式重新導向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/)。

> [!NOTE]
> 向作業系統註冊自訂的 URL 配置，以及處理結構描述的機制是每個平台專用的。

指定每個要求傳送至 Azure Active Directory B2C 租用戶*原則*。 原則描述取用者身分識別體驗，例如註冊或登入。 比方說，註冊原則可讓透過下列設定來設定 Azure Active Directory B2C 租用戶的行為：

- 取用者可以用來登入應用程式的帳戶類型。
- 若要在註冊期間會從取用者收集的資料。
- Multi-factor authentication。
- 註冊頁面內容。
- 行動應用程式收到此原則已執行時的權杖宣告。

Azure Active Directory 租用戶可以包含不同的類型，然後用於您的應用程式所需的多個原則。 此外，原則可以跨應用程式，可讓您定義及修改取用者身分識別體驗，而不需要變更您的程式碼重複使用。 如需有關原則的詳細資訊，請參閱 < [Azure Active Directory B2C： 內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

## <a name="setup"></a>安裝程式

Microsoft Authentication Library (MSAL) NuGet 程式庫必須加入至可攜式類別庫 (PCL) 專案和 Xamarin.Forms 方案中的平台專案中。 下列各節提供使用 MSAL 來與 Azure Active Directory B2C 租用戶從行動應用程式進行通訊的其他安裝指示。

### <a name="portable-class-library"></a>可攜式類別庫

需要使用 MSAL 的 Pcl 的目標重定為使用 Profile7。 如需有關 PCL 的詳細資訊，請參閱[可攜式類別庫簡介](~/cross-platform/app-fundamentals/pcl.md)。

### <a name="ios"></a>iOS

在 iOS 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須登錄於**Info.plist**，如下列螢幕擷取畫面所示：

![](azure-ad-b2c-images/customurl-ios.png "註冊在 iOS 上的自訂 URL 配置")

Azure Active Directory B2C 完成授權要求，它會重新導向至已註冊的重新導向 URL。 因為 URL 會使用自訂配置，這樣可以在 iOS 中啟動行動應用程式，在 URL 中傳遞做為啟動參數，其中由處理`OpenUrl`應用程式的覆寫`AppDelegate`類別，下列程式碼所示範例：

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

中的程式碼`OpenURL`方法可確保一旦驗證工作流程的互動部分已結束 msal 會傳回控制項。

### <a name="android"></a>Android

在 Android 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須登錄於**AndroidManifest.xml**，藉由新增`<activity>`內的現有項目`<application>`項目。 `<activity>`項目會指定`IntentFilter`上`Activity`，處理配置中，與下列範例所示：

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

Azure Active Directory B2C 完成授權要求，它會重新導向至已註冊的重新導向 URL。 因為 URL 會使用自訂配置，這樣可以在 Android 中啟動行動應用程式，在 URL 中傳遞做為啟動參數，其中由處理`microsoft.identity.client.BrowserTabActivity`。 請注意，`data android:scheme`屬性必須設為 已向 Azure Active Directory B2C 應用程式的自訂 URL 配置。

颾魤 ㄛ `MainActivity` ，也必須經過修改類別，如下列程式碼範例所示：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
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

`OnCreate`方法會修改指派`UIParent`執行個體`App.UiParent`屬性。 這可確保驗證流程，會發生在目前活動的內容中。

中的程式碼`OnActivityResult`方法可確保一旦驗證工作流程的互動部分已結束 msal 會傳回控制項。

### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台上不需要額外設定，才能使用 MSAL。

## <a name="initialization"></a>初始化

Microsoft 驗證程式庫使用的成員`PublicClientApplication`起始驗證工作流程的類別。 範例應用程式會宣告並初始化`public`這種屬性，名為`ADB2CClient`，請在`AuthenticationProvider`類別。 下列程式碼範例示範如何初始化這個屬性：

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

當行動裝置應用程式已向 Azure Active Directory B2C 租用戶註冊時，註冊程序指派**應用程式識別碼**。 此識別碼必須指定`PublicClientApplication`建構函式，連同`Authority`包含基底 URL 和 Azure Active Directory B2C 原則来執行的常數。

## <a name="signing-in"></a>登入

範例應用程式中的 [登入] 畫面如下列螢幕擷取畫面所示：

![](azure-ad-b2c-images/login.png "登入頁面")

登入與社交識別提供者，或使用本機帳戶、 允許的。 Microsoft、 Google 和 Facebook，使用如上所示，為社交識別提供者，可以也使用其他身分識別提供者。

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

`AcquireTokenAsync`方法會啟動裝置的網頁瀏覽器，並顯示透過參考的原則由 Azure Active Directory B2C 原則中定義的驗證選項`Constants.Authority`常數。 此原則會定義取用者在註冊期間和登入時，會經歷的體驗以及應用程式會在成功註冊或登入的宣告。

結果`AcquireTokenAsync`方法呼叫是`AuthenticationResult`執行個體。 如果驗證成功，`AuthenticationResult`執行個體將包含識別權杖，這會在本機快取。 如果驗證不成功，`AuthenticationResult`執行個體將會包含資料，指出驗證失敗的原因。

在範例應用程式，如果驗證成功，`TodoList`網頁被巡覽。

## <a name="silent-re-authentication"></a>無訊息的重新驗證

當`LoginPage`在此範例應用程式會出現，嘗試擷取使用者權杖，而不會顯示任何驗證的使用者介面。 這透過`AcquireTokenSilentAsync`方法，如下列程式碼範例所示：

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

`AcquireTokenSilentAsync`方法會嘗試從快取中，擷取使用者權杖，而不需要使用者登入。 這個方法會處理案例中，適合的語彙基元已可能出現在快取中之前的工作階段。 如果成功，嘗試取得權杖`TodoList`網頁被巡覽。 如果嘗試取得權杖失敗，會發生任何事，使用者必須起始新的驗證工作流程的選擇。

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

這會清除所有本機快取驗證權杖。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Microsoft Authentication Library (MSAL) 和 Azure Active Directory B2C 整合到行動應用程式的取用者身分識別管理。 Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。


## <a name="related-links"></a>相關連結

- [AzureADB2CAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
