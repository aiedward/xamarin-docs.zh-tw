---
title: 使用 Azure Active Directory B2C 驗證使用者
description: Azure Active Directory B2C 針對取用者面向的 web 和行動應用程式提供雲端身分識別管理。 本文說明如何使用 Azure Active Directory B2C 將身分識別管理整合到具有 Microsoft 驗證程式庫的行動應用程式。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c24c8eb2ea4801037621cdc82f49073c89115817
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374364"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 驗證使用者

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C 針對取用者面向的 web 和行動應用程式提供雲端身分識別管理。本文說明如何使用 Azure Active Directory B2C 將身分識別管理整合到具有 Microsoft 驗證程式庫的行動應用程式。_

## <a name="overview"></a>概觀

Azure Active Directory B2C (ADB2C) 是適用于消費者面向應用程式的身分識別管理服務。 它可讓使用者使用其現有的社交帳戶或自訂認證（例如電子郵件或使用者名稱和密碼）登入您的應用程式。 自訂認證帳戶稱為 _本機_ 帳戶。

將 Azure Active Directory B2C 身分識別管理服務整合到行動應用程式的程式如下所示：

1. 建立 Azure Active Directory B2C 租使用者。
1. 向 Azure Active Directory B2C 的租使用者註冊您的行動應用程式。
1. 建立註冊和登入的原則，以及忘記密碼使用者流程。
1. 使用 Microsoft 驗證程式庫 (MSAL) ，以 Azure Active Directory B2C 租使用者啟動驗證工作流程。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

Azure Active Directory B2C 支援多個身分識別提供者，包括 Microsoft、GitHub、Facebook、Twitter 等等。 如需 Azure Active Directory B2C 功能的詳細資訊，請參閱 [Azure Active Directory B2C 檔](/azure/active-directory-b2c/)。

Microsoft 驗證程式庫支援多種應用程式架構和平臺。 如需 MSAL 功能的相關資訊，請參閱 GitHub 上的 [Microsoft 驗證程式庫](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 。

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>設定 Azure Active Directory B2C 租使用者

若要執行範例專案，您必須建立 Azure Active Directory B2C 的租使用者。 如需詳細資訊，請參閱 [Azure 入口網站中的建立 Azure Active Directory B2C 的租](/azure/active-directory-b2c/active-directory-b2c-get-started/)使用者。

建立租使用者之後，您將需要租使用者 **名稱** 和 **租使用者識別碼** 來設定行動應用程式。 租使用者識別碼和名稱是由您建立租使用者 URL 時所產生的網域所定義。 如果您產生的租使用者 URL 是 `https://contoso20190410tenant.onmicrosoft.com/` **租使用者識別碼** `contoso20190410tenant.onmicrosoft.com` ，而且 **租使用者名稱** 為 `contoso20190410tenant` 。 按一下頂端功能表中的 [ **目錄和訂** 用帳戶] 篩選，以尋找 Azure 入口網站中的租使用者網域。 下列螢幕擷取畫面顯示 Azure 目錄和訂用帳戶篩選按鈕和租使用者網域：

[![Azure 目錄和訂用帳戶篩選視圖中的租使用者名稱](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

在範例專案中，編輯 **Constants.cs** 檔案來設定 `tenantName` 和 `tenantId` 欄位。 下列程式碼顯示如果您的租使用者網域是，如何設定這些值 `https://contoso20190410tenant.onmicrosoft.com/` ，請將這些值取代為您入口網站中的值：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 註冊您的行動應用程式

行動裝置應用程式必須先向租使用者註冊，才能連接和驗證使用者。 註冊程式會將唯一的 **應用程式識別碼** 指派給應用程式，以及在驗證之後將回應導向響應用程式的重新 **導向 URL** 。 如需詳細資訊，請參閱 [Azure Active Directory B2C：註冊您的應用程式](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。 您必須知道指派給您應用程式的 **應用程式識別碼** ，這會列在 [屬性] 視圖的應用程式名稱之後。 下列螢幕擷取畫面顯示在哪裡可以找到應用程式識別碼：

[![Azure 應用程式屬性視圖中的應用程式識別碼](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft 驗證程式庫預期您的應用程式的重新 **導向 URL** 會是您的 **應用程式識別碼** ，並以文字 "msal" 作為前置詞，後面接著稱為 "auth" 的端點。 如果您的應用程式識別碼是 "1234abcd"，則完整的 URL 應該是 `msal1234abcd://auth` 。 請確定您的應用程式已啟用 **原生用戶端** 設定，並使用您的應用程式識別碼建立 **自訂的重新導向 URI** ，如下列螢幕擷取畫面所示：

![Azure 應用程式屬性視圖中的自訂重新導向 URI](azure-ad-b2c-images/azure-redirect-uri.png)

稍後會在 Android **ApplicationManifest.xml** 和 iOS **plist** 中使用此 URL。

在範例專案中，編輯 **Constants.cs** 檔案，將欄位設定 `clientId` 為您的 **應用程式識別碼** 。 下列程式碼顯示如果您的應用程式識別碼為，應如何設定此值 `1234abcd` ：

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

原則是使用者完成工作（例如建立帳戶或重設密碼）的體驗。 原則也會指定當使用者從經驗返回時，應用程式所收到的權杖內容。 您必須設定帳戶註冊與登入的原則，以及重設密碼。 Azure 具有內建原則，可簡化常見原則的建立。 如需詳細資訊，請參閱 [Azure Active Directory B2C：內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

當您完成原則設定時，您的使用者流程中應該會有兩個原則 **(原則)** 在 Azure 入口網站中看到。 下列螢幕擷取畫面示範 Azure 入口網站中設定的兩個原則：

![Azure 使用者流程中有兩個設定的原則) 視圖 (原則](azure-ad-b2c-images/azure-application-policies.png)

在範例專案中，編輯 **Constants.cs** 檔案來設定 `policySignin` 和欄位， `policyPassword` 以反映在原則設定期間所選擇的名稱：

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

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>使用 Microsoft 驗證程式庫 (MSAL) 進行驗證

您必須將 Microsoft 驗證程式庫 (MSAL) NuGet 套件新增至方案中的共用、.NET Standard 專案和平臺專案 Xamarin.Forms 。 MSAL 包含的 `PublicClientApplicationBuilder` 類別，可讓物件遵守 `IPublicClientApplication` 介面。 MSAL 利用 `With` 子句，將其他參數提供給函式和驗證方法。

在範例專案中，適用于 app.xaml 的 **程式** 代碼後向會定義名為和的靜態屬性 `AuthenticationClient` ，並在函式中具現 `UIParent` 化 `AuthenticationClient` 物件。 `WithIosKeychainSecurityGroup`子句會提供 iOS 應用程式的安全性群組名稱。 `WithB2CAuthority`子句會提供將用來驗證使用者的預設 **授權** 單位或原則。 `WithRedirectUri`如果指定了多個 uri，子句會告訴 Azure 通知中樞實例要使用的重新導向 URI。 下列範例會示範如何具現化 `PublicClientApplication` ：

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
> 如果您的 Azure 通知中樞實例只定義了一個重新導向 URI，則 `AuthenticationClient` 實例可能會運作，而不需要使用子句來指定重新導向 uri `WithRedirectUri` 。 不過，如果您的 Azure 設定擴展為支援其他用戶端或驗證方法，您應該一律指定此值。

`OnAppearing` **LoginPage.xaml.cs** 程式碼中的事件處理常式會呼叫，以重新整理 `AcquireTokenSilentAsync` 之前已登入之使用者的驗證 token。 驗證程式會在成功時重新導向至， `LogoutPage` 並在失敗時進行任何動作。 下列範例顯示中的無訊息重新驗證程式 `OnAppearing` ：

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

`OnLoginButtonClicked`當按一下 [登入] 按鈕) 呼叫時，會引發事件處理常式 (`AcquireTokenAsync` 。 MSAL 程式庫會自動開啟行動裝置瀏覽器，並流覽至登入頁面。 登入 URL （稱為「 **授權** 單位」）是在 **Constants.cs** 檔中定義的租使用者名稱和原則的組合。 如果使用者選擇忘記密碼選項，則會將其傳回給應用程式，並出現例外狀況，這會啟動忘記密碼體驗。 下列範例顯示驗證程式：

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

`OnForgotPassword`方法類似于登入程式，但會執行自訂原則。 `OnForgotPassword` 使用不同的多載 `AcquireTokenAsync` ，可讓您提供特定的 **授權** 單位。 下列範例顯示如何在取得權杖時提供自訂 **授權** 單位：

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

驗證的最後一步是登出程式。 `OnLogoutButtonClicked`當使用者按下 [登出] 按鈕時，會呼叫方法。 它會在所有帳戶上迴圈，並確保其權杖已失效。 下列範例示範登出執行：

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

在 iOS 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須在 **plist** 中註冊。 MSAL 預期 URL 配置必須遵守特定模式，如先前在 [向 Azure Active Directory B2C 註冊您的行動應用程式](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)中所述。 下列螢幕擷取畫面顯示 **plist** 中的自訂 URL 配置。

![「在 iOS 上註冊自訂 URL 配置」](azure-ad-b2c-images/customurl-ios.png)

MSAL 也需要 iOS 上的 Keychain 權利，並在 **Entitilements** 中註冊，如下列螢幕擷取畫面所示：

![「在 iOS 上設定應用程式權利」](azure-ad-b2c-images/entitlements-ios.png)

當 Azure Active Directory B2C 完成授權要求時，它會重新導向至已註冊的重新導向 URL。 自訂 URL 配置會導致 iOS 啟動行動裝置應用程式，並傳入 URL 作為啟動參數（由應用程式類別的覆寫進行處理）， `OpenUrl` `AppDelegate` 並將體驗的控制權傳回給 MSAL。 `OpenUrl`下列程式碼範例顯示此實作為範例：

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

在 Android 上，您必須在 **AndroidManifest.xml** 中註冊使用 Azure Active Directory B2C 註冊的自訂 URL 配置。 MSAL 預期 URL 配置必須遵守特定模式，如先前在 [向 Azure Active Directory B2C 註冊您的行動應用程式](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)中所述。 下列範例顯示 **AndroidManifest.xml** 中的自訂 URL 配置。

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

`MainActivity`必須修改類別，才能在 `UIParent` 呼叫期間提供物件給應用程式 `OnCreate` 。 當 Azure Active Directory B2C 完成授權要求時，它會從 **AndroidManifest.xml** 重新導向至已註冊的 URL 配置。 已註冊的 URI 配置會導致 Android 以 `OnActivityResult` URL 作為啟動參數來呼叫方法，而此方法是由方法處理 `SetAuthenticationContinuationEventArgs` 。

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

不需要額外的設定，就能在通用 Windows 平臺上使用 MSAL

## <a name="run-the-project"></a>執行專案

在虛擬或實體裝置上執行應用程式。 點擊 [ **登** 入] 按鈕應該會開啟瀏覽器，並流覽至您可以登入或建立帳戶的頁面。 完成登入程式之後，您應該會回到應用程式的 [登出] 頁面。 下列螢幕擷取畫面顯示在 Android 和 iOS 上執行的使用者登入畫面：

![「Android 和 iOS 上的 Azure ADB2C 登入畫面」](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>相關連結

- [AzureADB2CAuth (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 驗證程式庫](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 驗證程式庫檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)