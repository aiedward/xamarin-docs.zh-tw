---
title: 使用 Azure Active Directory B2C 進行使用者驗證
description: Azure Active Directory B2C 提供雲端取用者導向 web 與行動應用程式的身分識別管理。 本文說明如何使用 Azure Active Directory B2C 身分識別管理整合到行動應用程式中使用 Microsoft Authentication Library。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417940"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 進行使用者驗證

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active Directory B2C 提供雲端取用者導向 web 與行動應用程式的身分識別管理。本文說明如何使用 Azure Active Directory B2C 身分識別管理整合到行動應用程式中使用 Microsoft Authentication Library。_

## <a name="overview"></a>總覽

Azure Active Directory B2C (ADB2C) 是身分識別管理服務的消費者端應用程式。 它可讓使用者登入您的應用程式使用其現有的社交帳戶或自訂的認證，例如電子郵件或使用者名稱和密碼。 自訂認證的帳戶指_本機_帳戶。

將 Azure Active Directory B2C 身分識別管理服務整合到行動應用程式的程序如下所示：

1. 建立 Azure Active Directory B2C 租用戶
1. 向 Azure Active Directory B2C 租用戶註冊您的行動應用程式
1. 建立原則來註冊和登入，忘了密碼使用者流程
1. 若要開始您的 Azure Active Directory B2C 租用戶中的驗證工作流程中使用 Microsoft Authentication Library (MSAL)。

> [!NOTE]
> Azure Active Directory B2C 支援多個身分識別提供者，包括 Microsoft、 GitHub、 Facebook、 Twitter 和更多功能。 如需有關 Azure Active Directory B2C 功能的詳細資訊，請參閱 < [Azure Active Directory B2C 文件](/azure/active-directory-b2c/)。
>
> Microsoft 驗證程式庫支援多個應用程式架構及平台。 MSAL 功能的相關資訊，請參閱[Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) GitHub 上。

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>設定 Azure Active Directory B2C 租用戶

若要執行範例專案，您必須建立 Azure Active Directory B2C 租用戶。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/active-directory-b2c-get-started/)。

一旦您建立租用戶，您將需要**租用戶名稱**並**租用戶識別碼**設定行動應用程式。 當您建立您的租用戶 URL 時，產生的網域定義的租用戶識別碼和名稱。 如果您產生的租用戶 URL 是`https://contoso20190410tenant.onmicrosoft.com/`**租用戶識別碼**是`contoso20190410tenant.onmicrosoft.com`並**租用戶名稱**是`contoso20190410tenant`。 在 Azure 入口網站中找到租用戶網域，依序按一下**目錄和訂用帳戶篩選**上方功能表中。 下列螢幕擷取畫面顯示 Azure 目錄和訂用帳戶篩選器按鈕和租用戶網域：

[![Azure 目錄和訂用帳戶篩選器檢視中的租用戶名稱](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

在範例專案中，編輯**Constants.cs**檔案來設定`tenantName`和`tenantId`欄位。 下列程式碼顯示應該設定這些值的方式您租用戶的網域是否`https://contoso20190410tenant.onmicrosoft.com/`，這些值取代為您的入口網站中的值：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>向 Azure Active Directory B2C 註冊行動裝置應用程式

行動應用程式必須向租用戶，它才能連接並驗證使用者。 註冊程序會指派一個唯一**應用程式識別碼**應用程式，以及**重新導向 URL** ，指示在驗證之後，應用程式的回應。 如需詳細資訊，請參閱[Azure Active Directory B2C:註冊您的應用程式](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。 您必須知道**應用程式識別碼**指派給您的應用程式，它會列在 [屬性] 檢視中的應用程式名稱之後。 下列螢幕擷取畫面顯示如何尋找應用程式識別碼：

[![在 Azure 應用程式的 [屬性] 檢視中的應用程式識別碼](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft 驗證程式庫預期**重新導向 URL**您的應用程式是您**應用程式識別碼**加上文字"msal"，且後面接著"auth"的端點。 如果您的應用程式識別碼 」 1234abcd"，完整的 URL 應`msal1234abcd://auth`。 請確定您的應用程式已啟用**原生用戶端**設定，並建立**自訂重新導向 URI**使用您的應用程式識別碼，如下列螢幕擷取畫面所示：

![在 Azure 應用程式的 [屬性] 檢視中的自訂重新導向 URI](azure-ad-b2c-images/azure-redirect-uri.png)

將使用的 URL，稍後在這兩個 Android **ApplicationManifest.xml**和 iOS **Info.plist**。

在範例專案中，編輯**Constants.cs**檔案來設定`clientId`欄位設為您**應用程式識別碼**。 下列程式碼示範如何設定此值應該是否您的應用程式識別碼是`1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>建立註冊和登入的原則，以及忘記的密碼原則

原則是使用者在完成的工作，例如建立帳戶或重設密碼的體驗。 原則也會指定應用程式收到使用者經驗所傳回的權杖的內容。 您必須設定兩個帳戶的原則，註冊和登入及重設密碼。 Azure 有內建原則，可簡化建立一般的原則。 如需詳細資訊，請參閱[Azure Active Directory B2C:內建原則](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

當您完成原則設定時，您應該在兩個原則 **（原則） 的使用者流程**Azure 入口網站中的檢視。 下列螢幕擷取畫面示範如何在 Azure 入口網站中的兩個設定的原則：

![Azure 使用者流程 （原則） 中的兩個設定的原則檢視](azure-ad-b2c-images/azure-application-policies.png)

在範例專案中，編輯**Constants.cs**檔案來設定`policySignin`和`policyPassword`欄位以反映您在原則安裝期間所選擇的名稱：

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

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>使用 Microsoft Authentication Library (MSAL) 以進行驗證

Microsoft Authentication Library (MSAL) NuGet 套件必須新增至共用、.NET Standard 專案和 Xamarin.Forms 方案中的平台專案中。 包含 MSAL`PublicClientApplicationBuilder`類別，建構一個物件遵守`IPublicClientApplication`介面。 MSAL 會利用`With`子句，以提供其他參數的建構函式和驗證方法。

在範例專案中，程式碼後置**App.xaml**定義靜態屬性，名為`AuthenticationClient`並`UIParent`，並具現化`AuthenticationClient`建構函式中的物件。 `WithIosKeychainSecurityGroup`子句提供 iOS 應用程式的安全性群組名稱。 `WithB2CAuthority`子句提供的預設值**授權單位**，或將用來驗證使用者的原則。 下列範例示範如何具現化`PublicClientApplication`:

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
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

`OnAppearing`中的事件處理常式**LoginPage.xaml.cs**程式碼後置呼叫`AcquireTokenSilentAsync`重新整理之前已登入的使用者的驗證權杖。 在驗證程序會重新導向至`LogoutPage`如果成功和不執行任何動作失敗。 下列範例示範中的無訊息重新驗證程序`OnAppearing`:

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

`OnLoginButtonClicked` （按一下 [登入] 按鈕時引發） 事件處理常式呼叫`AcquireTokenAsync`。 MSAL 程式庫會自動開啟行動裝置瀏覽器，並瀏覽至登入頁面。 登入 URL，請呼叫**授權單位**，是租用戶名稱的組合中定義的原則**Constants.cs**檔案。 如果使用者選擇忘記的密碼 選項中，被傳回到應用程式與例外狀況，可用來啟動忘了密碼體驗。 下列範例示範在驗證程序：

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

`OnForgotPassword`方法登入程序十分類似，但會實作自訂原則。 `OnForgotPassword` 使用的不同多載`AcquireTokenAsync`，可讓您可提供特定**授權單位**。 下列範例示範如何提供自訂**授權單位**時取得權杖：

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

驗證的最後一步是登出程序。 `OnLogoutButtonClicked`方法在使用者按下登出按鈕時呼叫。 它的所有帳戶中執行都迴圈，並確保其權杖已經失效。 下列範例示範如何實作登出：

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

在 iOS 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須登錄於**Info.plist**。 MSAL 會預期要遵守特定的模式，先前所述的 URL 配置[行動應用程式向 Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 下列螢幕擷取畫面顯示在自訂的 URL 配置**Info.plist**。

![「 註冊自訂的 URL 配置，在 iOS 上 」](azure-ad-b2c-images/customurl-ios.png)

MSAL 也需要在 iOS 上，註冊在 Keychain 權利**Entitilements.plist**，如下列螢幕擷取畫面所示：

![「 設定在 iOS 上的應用程式的權利 」](azure-ad-b2c-images/entitlements-ios.png)

Azure Active Directory B2C 完成授權要求，它會重新導向至已註冊的重新導向 URL。 自訂的 URL 配置會產生在 iOS 中啟動行動應用程式，然後在 URL 中傳遞做為啟動參數，其中由處理`OpenUrl`應用程式的覆寫`AppDelegate`類別和 msal 的體驗就會將控制權。 `OpenUrl`實作以下列程式碼範例所示：

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

在 Android 上，使用 Azure Active Directory B2C 註冊的自訂 URL 配置必須登錄於**AndroidManifest.xml**。 MSAL 會預期要遵守特定的模式，先前所述的 URL 配置[行動應用程式向 Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 下列範例顯示在自訂的 URL 配置**AndroidManifest.xml**。

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
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

`MainActivity`必須修改類別，以提供`UIParent`物件，以應用程式在`OnCreate`呼叫。 Azure Active Directory B2C 完成授權要求，它將重新導向至已註冊的 URL 配置，從**AndroidManifest.xml**。 Android 的呼叫會產生的已註冊的 URI 配置`OnActivityResult`方法的 url 做為啟動參數，其中由處理`SetAuthenticationContinuationEventArgs`方法。

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

不需要額外設定，才能在通用 Windows 平台上使用 MSAL

## <a name="run-the-project"></a>執行專案

在虛擬或實體裝置上執行應用程式。 點選**登入**按鈕應該會開啟瀏覽器並瀏覽的頁面，您可以在此登入或建立帳戶。 完成之後登入程序，您應該會傳回至應用程式的登出頁面。 下列螢幕擷取畫面顯示使用者登在 Android 和 iOS 上執行的畫面：

![「 Azure ADB2C 登在 Android 和 iOS 上的畫面 」](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>相關連結

- [AzureADB2CAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 驗證程式庫文件](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
