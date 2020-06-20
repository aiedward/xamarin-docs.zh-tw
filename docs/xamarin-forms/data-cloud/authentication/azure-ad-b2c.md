---
title: 使用 Azure Active Directory B2C 驗證使用者
description: Azure Active Directory B2C 為取用者面向的 web 和行動應用程式提供雲端身分識別管理。 本文說明如何使用 Azure Active Directory B2C，將身分識別管理整合到具有 Microsoft 驗證程式庫的行動應用程式中。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 64529b81a375ee5a8cc8a96ec557c03401e60495
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130568"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 驗證使用者

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C 為取用者面向的 web 和行動應用程式提供雲端身分識別管理。本文說明如何使用 Azure Active Directory B2C，將身分識別管理整合到具有 Microsoft 驗證程式庫的行動應用程式中。_

## <a name="overview"></a>概觀

Azure Active Directory B2C （ADB2C）是取用者面向應用程式的身分識別管理服務。 它可讓使用者使用現有的社交帳戶或自訂認證（例如電子郵件或使用者名稱和密碼）來登入您的應用程式。 自訂認證帳戶稱為_本機_帳戶。

將 Azure Active Directory B2C 身分識別管理服務整合到行動應用程式的流程如下所示：

1. 建立 Azure Active Directory B2C 租使用者。
1. 向 Azure Active Directory B2C 租使用者註冊您的行動應用程式。
1. 建立註冊和登入的原則，以及忘記密碼的使用者流程。
1. 使用 Microsoft 驗證程式庫（MSAL）來啟動 Azure Active Directory B2C 租使用者的驗證工作流程。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

Azure Active Directory B2C 支援多個身分識別提供者，包括 Microsoft、GitHub、Facebook、Twitter 等等。 如需 Azure Active Directory B2C 功能的詳細資訊，請參閱[Azure Active Directory B2C 檔](/azure/active-directory-b2c/)。

Microsoft 驗證程式庫支援多個應用程式架構和平臺。 如需 MSAL 功能的相關資訊，請參閱 GitHub 上的[Microsoft 驗證程式庫](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>設定 Azure Active Directory B2C 租使用者

若要執行範例專案，您必須建立 Azure Active Directory B2C 租使用者。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure Active Directory B2C 的租](/azure/active-directory-b2c/active-directory-b2c-get-started/)使用者。

建立租使用者之後，您將需要租使用者**名稱**和**租使用者識別碼**來設定行動應用程式。 租使用者識別碼和名稱是由您在建立租使用者 URL 時所產生的網域所定義。 如果您產生的租使用者 URL 是 `https://contoso20190410tenant.onmicrosoft.com/` **租使用者識別碼**， `contoso20190410tenant.onmicrosoft.com` 而租使用者**名稱**是 `contoso20190410tenant` 。 按一下頂端功能表中的 [**目錄和訂**用帳戶] 篩選，尋找 Azure 入口網站中的租使用者網域。 下列螢幕擷取畫面顯示 [Azure 目錄和訂用帳戶篩選] 按鈕和租使用者網域：

[![Azure 目錄和訂用帳戶篩選器視圖中的租使用者名稱](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

在範例專案中，編輯**Constants.cs**檔案以設定 `tenantName` 和 `tenantId` 欄位。 下列程式碼顯示如果您的租使用者網域為，應如何設定這些值 `https://contoso20190410tenant.onmicrosoft.com/` ，請將這些值取代為您入口網站中的值：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>向 Azure Active Directory B2C 註冊您的行動應用程式

行動應用程式必須先向租使用者註冊，才能夠連線並驗證使用者。 註冊程式會將唯一的**應用程式識別碼**指派給應用程式，並將驗證之後將回應導向回應用程式的重新**導向 URL** 。 如需詳細資訊，請參閱[Azure Active Directory B2C：註冊您的應用程式](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。 您必須知道指派給應用程式的**應用程式識別碼**，這會列在 [屬性] 視圖中的應用程式名稱後面。 下列螢幕擷取畫面顯示哪裡可以找到應用程式識別碼：

[![Azure 應用程式屬性視圖中的應用程式識別碼](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft 驗證程式庫預期您的應用程式的重新**導向 URL**是您的**應用程式識別碼**，前面加上 "msal" 文字，後面接著稱為 "auth" 的端點。 如果您的應用程式識別碼是 "1234abcd"，則完整的 URL 應該是 `msal1234abcd://auth` 。 請確定您的應用程式已啟用**Native client**設定，並使用您的應用程式識別碼來建立**自訂重新導向 URI** ，如下列螢幕擷取畫面所示：

![Azure 應用程式屬性視圖中的自訂重新導向 URI](azure-ad-b2c-images/azure-redirect-uri.png)

稍後會在 Android **ApplicationManifest.xml**和 iOS **plist**中使用此 URL。

在範例專案中，編輯**Constants.cs**檔案，將欄位設定 `clientId` 為您的**應用程式識別碼**。 下列程式碼顯示如果您的應用程式識別碼為，應該如何設定此值 `1234abcd` ：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>建立註冊和登入原則，並忘記密碼原則

原則是使用者完成一項工作（例如建立帳戶或重設密碼）的經驗。 原則也會指定當使用者從經驗返回時，應用程式所收到的權杖內容。 您必須設定帳戶註冊和登入的原則，以及重設密碼。 Azure 具有內建原則，可簡化常見原則的建立。 如需詳細資訊，請參閱[Azure Active Directory B2C：內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

當您完成原則設定時，Azure 入口網站的 [**使用者流程（原則）** ] 視圖中應該有兩個原則。 下列螢幕擷取畫面示範 Azure 入口網站中設定的兩個原則：

![Azure 使用者流程（原則）視圖中的兩個已設定原則](azure-ad-b2c-images/azure-application-policies.png)

在範例專案中，編輯**Constants.cs**檔案以設定 `policySignin` 和欄位， `policyPassword` 以反映您在原則設定期間所選擇的名稱：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>使用 Microsoft 驗證程式庫（MSAL）進行驗證

「Microsoft 驗證程式庫」（MSAL） NuGet 封裝必須加入至方案中的共用、.NET Standard 專案和平臺專案 Xamarin.Forms 。 MSAL 包含一個 `PublicClientApplicationBuilder` 類別，它會建立遵守介面的物件 `IPublicClientApplication` 。 MSAL 會利用 `With` 子句來提供其他參數給函數和驗證方法。

在範例專案中， **app.xaml 的程式**代碼會定義名為和的靜態屬性，並具現化函式 `AuthenticationClient` `UIParent` `AuthenticationClient` 中的物件。 `WithIosKeychainSecurityGroup`子句會提供 iOS 應用程式的安全性群組名稱。 `WithB2CAuthority`子句會提供將用來驗證使用者的預設**授權**單位（或原則）。 `WithRedirectUri`如果指定多個 uri，子句會告訴 Azure 通知中樞實例要使用的重新導向 URI。 下列範例示範如何具現化 `PublicClientApplication` ：

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .WithRedirectUri($"msal{Constants.ClientId}://auth")
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

> [!NOTE]
> 如果您的 Azure 通知中樞實例只定義了一個重新導向 URI， `AuthenticationClient` 實例可能會在不使用子句指定重新導向 uri 的情況下工作 `WithRedirectUri` 。 不過，如果您的 Azure 設定擴充以支援其他用戶端或驗證方法，您應該一律指定此值。

`OnAppearing` **LoginPage.xaml.cs**程式碼後置中的事件處理常式會呼叫，以重新整理先前 `AcquireTokenSilentAsync` 已登入之使用者的驗證權杖。 如果成功，驗證程式會重新導向至， `LogoutPage` 而不會在失敗時執行任何作業。 下列範例顯示中的無訊息重新驗證程式 `OnAppearing` ：

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

`OnLoginButtonClicked`事件處理常式（按一下 [登入] 按鈕時引發）呼叫 `AcquireTokenAsync` 。 MSAL 程式庫會自動開啟行動裝置瀏覽器，並流覽至登入頁面。 登入 URL （稱為「**授權**單位」）是**Constants.cs**檔案中定義的租使用者名稱和原則的組合。 如果使用者選擇 [忘記密碼] 選項，則會將其傳回至具有例外狀況的應用程式，以啟動忘記密碼體驗。 下列範例顯示驗證程式：

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

`OnForgotPassword`方法類似于登入程式，但會執行自訂原則。 `OnForgotPassword`使用的不同多載 `AcquireTokenAsync` ，可讓您提供特定的**授權**單位。 下列範例顯示如何在取得權杖時提供自訂**授權**：

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

最後一項驗證就是登出程式。 `OnLogoutButtonClicked`當使用者按下 [登出] 按鈕時，會呼叫方法。 它會對所有帳戶執行迴圈，並確保其權杖已失效。 下列範例示範登出的執行方式：

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

在 iOS 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須在**Info. plist**中註冊。 MSAL 預期 URL 配置遵守特定模式，如先前在[Azure Active Directory B2C 註冊行動應用程式](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)中所述。 下列螢幕擷取畫面顯示**Info. plist**中的自訂 URL 配置。

![「在 iOS 上註冊自訂 URL 配置」](azure-ad-b2c-images/customurl-ios.png)

MSAL 也需要在**Entitilements**上註冊的 IOS Keychain 權利，如下列螢幕擷取畫面所示：

![「在 iOS 上設定應用程式權利」](azure-ad-b2c-images/entitlements-ios.png)

當 Azure Active Directory B2C 完成授權要求時，它會重新導向至已註冊的重新導向 URL。 自訂 URL 配置會導致 iOS 啟動行動應用程式，並以啟動參數的形式傳入 URL，其中會由應用程式類別的覆寫進行處理， `OpenUrl` 並將體驗的控制權傳回 `AppDelegate` 給 MSAL。 `OpenUrl`下列程式碼範例顯示此實作為：

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
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

在 Android 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須在**AndroidManifest.xml**中註冊。 MSAL 預期 URL 配置遵守特定模式，如先前在[Azure Active Directory B2C 註冊行動應用程式](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)中所述。 下列範例顯示**AndroidManifest.xml**中的自訂 URL 配置。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

您 `MainActivity` 必須修改類別，以便在 `UIParent` 呼叫期間將物件提供給應用程式 `OnCreate` 。 當 Azure Active Directory B2C 完成授權要求時，它會從**AndroidManifest.xml**重新導向至已註冊的 URL 配置。 已註冊的 URI 配置會導致 Android 呼叫 `OnActivityResult` 方法，並以 URL 作為啟動參數，以供 `SetAuthenticationContinuationEventArgs` 方法處理。

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平臺上使用 MSAL 時，不需要進行其他設定

## <a name="run-the-project"></a>執行專案

在虛擬或實體裝置上執行應用程式。 點擊 [**登**入] 按鈕應該會開啟瀏覽器，並流覽至您可以登入或建立帳戶的頁面。 完成登入程式之後，您應該會返回應用程式的 [登出] 頁面。 下列螢幕擷取畫面顯示在 Android 和 iOS 上執行的使用者登入畫面：

![「Android 和 iOS 上的 Azure ADB2C 登入畫面」](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>相關連結

- [AzureADB2CAuth （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 驗證程式庫檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
