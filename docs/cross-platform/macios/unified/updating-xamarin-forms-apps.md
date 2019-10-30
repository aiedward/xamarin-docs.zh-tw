---
title: Updating Existing Xamarin.Forms Apps
description: This document describes the steps that must be followed to update a Xamarin.Forms app from the Classic API to the Unified API.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: dad1b7173e302931455887fdaa4730347f0e5e55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015006"
---
# <a name="updating-existing-xamarinforms-apps"></a>Updating Existing Xamarin.Forms Apps

_Follow these steps to update an existing Xamarin.Forms app to use the Unified API and update to version 1.3.1_

> [!IMPORTANT]
> Because Xamarin.Forms 1.3.1 is the first release that supports the Unified API, the entire solution should be updated to use the latest version at the same time as migrating the iOS app to Unified. This means that in addition to updating the iOS project for Unified support, you'll also need to edit code in _all_ the projects in the solution.

The update is performed in two steps:

1. Migrate the iOS app to the Unified API using Visual Studio for Mac's build in migration tool.

    - Use the migration tool to automatically update the project.

    - Update iOS native APIs as outlined in the instructions to [update iOS apps](~/cross-platform/macios/unified/updating-ios-apps.md) (specifically in custom renderer or dependency service code).

2. Update the entire solution to Xamarin.Forms version 1.3.

    1. Install the Xamarin.Forms 1.3.1 NuGet package.

    2. Update the `App` class in the shared code.

    3. Update the `AppDelegate` in the iOS project.

    4. Update the `MainActivity` in the Android project.

    5. Update the `MainPage` in the Windows Phone project.

## <a name="1-ios-app-unified-migration"></a>1. iOS App (Unified Migration)

Part of the migration requires upgrading Xamarin.Forms to version 1.3, which supports the Unified API. In order for the correct assembly references to be created, we first need to update the iOS project to use the Unified API.

### <a name="migration-tool"></a>Migration Tool

Click on the iOS project so that it's selected, then choose **Project > Migrate to Xamarin.iOS Unified API...** and agree to the warning message that appears.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Choose Project > Migrate to Xamarin.iOS Unified API... and agree to the warning message that appears")

This will automatically:

- Change the project type to support the Unified 64-bit API.
- Change the framework reference to **Xamarin.iOS** (replacing the old **monotouch** reference).
- Change the namespace references in the code to remove the `MonoTouch` prefix.
- Update the **csproj** file to use the correct build targets for the Unified API.

**Clean** and **Build** the project to ensure there are no other errors to fix. No further action should be required. These steps are explained in more detail in the [Unified API docs](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Update native iOS APIs (if required)

If you have added additional iOS native code (such as custom renderers or dependency services) you may need to perform additional manual code fixes. Re-compile your app and refer to the [Updating Existing iOS Apps instructions](~/cross-platform/macios/unified/updating-ios-apps.md) for additional information on changes that may be required. [These tips](~/cross-platform/macios/unified/updating-tips.md) will also help identify changes that are required.

## <a name="2-xamarinforms-131-update"></a>2. Xamarin.Forms 1.3.1 Update

Once the iOS app has been updated to the Unified API, the rest of the solution needs to be updated to Xamarin.Forms version 1.3.1. 包括：

- Updating the Xamarin.Forms NuGet package in each project.
- Changing the code to use the new Xamarin.Forms `Application`,  `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), and `FormsApplicationPage` (Windows Phone) classes.

These steps are explained below:

### <a name="21-update-nuget-in-all-projects"></a>2.1 Update NuGet in all Projects

Update Xamarin.Forms to 1.3.1 pre-release using the NuGet Package Manager for all projects in the solution: PCL (if present), iOS, Android, and Windows Phone. It is recommended that you **delete and re-add** the Xamarin.Forms NuGet package to update to version 1.3.

> [!NOTE]
> Xamarin.Forms version 1.3.1 is currently in *pre-release*. This means you must select the **pre-release** option in NuGet (via a tick-box in Visual Studio for Mac or a drop-down-list in Visual Studio) to see the latest pre-release version.

> [!IMPORTANT]
> If you are using Visual Studio, ensure the latest version of the NuGet Package Manager is installed. Older versions of NuGet in Visual Studio will not correctly install the Unified version of Xamarin.Forms 1.3.1. Go to **Tools > Extensions and Updates...** and click on the **Installed** list to check that the **NuGet Package Manager for Visual Studio** is at least version 2.8.5. If it is older, click on the **Updates** list to download the latest version.

Once you've updated the NuGet package to Xamarin.Forms 1.3.1, make the following changes in each project to upgrade to the new `Xamarin.Forms.Application` class.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 Portable Class Library (or Shared Project)

Change the **App.cs** file so that:

- The `App` class now inherits from `Application`.
- The `MainPage` property is set to the first content page you wish to display.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

We have completely removed the `GetMainPage` method, and instead set the `MainPage` *property* on the `Application` subclass.

This new `Application` base class also supports the `OnStart`, `OnSleep`, and `OnResume` overrides to help you manage your application's lifecycle.

The `App` class is then passed to a new `LoadApplication` method in each app project, as described below:

### <a name="23-ios-app"></a>2.3 iOS App

Change the **AppDelegate.cs** file so that:

- The class inherits from `FormsApplicationDelegate` (instead of `UIApplicationDelegate` previously).
- `LoadApplication` is called with a new instance of `App`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 Android App

Change the **MainActivity.cs** file so that:

- The class inherits from `FormsApplicationActivity` (instead of `FormsActivity` previously).
- `LoadApplication` is called with a new instance of `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 Windows Phone App

We need to update the **MainPage** - both the XAML and the codebehind.

Change the **MainPage.xaml** file so that:

- The root XAML element should be `winPhone:FormsApplicationPage`.
- The `xmlns:phone` attribute should be *changed* to `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

An updated example is shown below - you should only have to edit these things (the rest of the attributes should remain the same):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Change the **MainPage.xaml.cs** file so that:

- The class inherits from `FormsApplicationPage` (instead of `PhoneApplicationPage` previously).
- `LoadApplication` is called with a new instance of the Xamarin.Forms `App` class. 您可能需要完整限定此參考，因為 Windows Phone 已定義自己的 `App` 類別。

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>疑難排解

您偶爾會在更新 Xamarin. Forms NuGet 套件之後看到類似的錯誤。 當 NuGet 更新程式未完全從您的 **.csproj**檔案中移除較舊版本的參考時，就會發生這種情況。

>您的\_專案 .csproj：錯誤：此專案參考了這部電腦上遺失的 NuGet 套件。 啟用 NuGet 套件還原以下載它們。  如需詳細資訊，請參閱 http://go.microsoft.com/fwlink/?LinkID=322105 。 遺失的檔案為../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win + net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin. Forms. .targets。 （您的\_專案）

若要修正這些錯誤，請在文字編輯器中開啟 **.csproj**檔案，並尋找參考舊版 Xamarin 的 `<Target` 元素，例如如下所示的元素。 您應該從 **.csproj**檔案手動刪除此整個專案，並儲存變更。

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

移除這些舊的參考之後，專案應該會成功建立。

## <a name="considerations"></a>考量

如果應用程式依賴一或多個元件或 NuGet 套件，則將現有的 Xamarin. form 專案從 Classic API 轉換成新的 Unified API 時，應該考慮下列事項。

### <a name="components"></a>元件

您包含在應用程式中的任何元件也必須更新為 Unified API，否則當您嘗試編譯時，將會發生衝突。 針對任何包含的元件，以支援 Unified API 的 Xamarin 元件存放區中的新版本取代目前的版本，並執行全新的組建。 作者尚未轉換的任何元件，都只會在元件存放區中顯示32位的警告。

### <a name="nuget-support"></a>NuGet 支援

雖然我們已將 NuGet 的變更提供給 Unified API 的支援，但並沒有新版本的 NuGet，因此我們正在評估如何取得 NuGet 來辨識新的 Api。

在該時間之前，您必須將包含在專案中的任何 NuGet 套件，切換到支援統一 Api 的版本，之後再執行全新的組建。

> [!IMPORTANT]
> If you have an error in the form _"Error 3 Cannot include both 'monotouch.dll' and 'Xamarin.iOS.dll' in the same Xamarin.iOS project - 'Xamarin.iOS.dll' is referenced explicitly, while 'monotouch.dll' is referenced by 'xxx, Version=0.0.000, Culture=neutral, PublicKeyToken=null'"_ after converting your application to the Unified APIs, it is typically due to having either a component or NuGet Package in the project that has not been updated to the Unified API. You'll need to remove the existing component/NuGet, update to a version that supports the Unified APIs and do a clean build.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 Xamarin iOS 應用程式的64位組建

對於已轉換為 Unified API 的 Xamarin iOS 行動應用程式，開發人員仍然必須從應用程式的選項啟用64位電腦的應用程式建立。 如需有關啟用64位組建的詳細指示，請參閱啟用[32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md#enable-64)檔中的**64 位組建應用程式**。

## <a name="summary"></a>總結

現在，您應該將 Xamarin 應用程式更新為版本1.3.1，並將 iOS 應用程式遷移至 Unified API （在 iOS 平臺上支援64位架構）。

如上所述，如果您的 Xamarin 應用程式包含機器碼（例如自訂轉譯器或相依性服務），則這些可能也需要更新，才能使用[Unified API 中引進](~/cross-platform/macios/index.md)的新類型。

## <a name="related-links"></a>相關連結

- [更新 iOS 應用程式](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [更新秘訣](~/cross-platform/macios/unified/updating-tips.md)
- [Classic vs Unified API differences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
