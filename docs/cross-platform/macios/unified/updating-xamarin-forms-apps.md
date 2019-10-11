---
title: 更新現有的 Xamarin 應用程式
description: 本檔說明將 Classic API 的 Xamarin 應用程式更新至 Unified API 時必須遵循的步驟。
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 1820dfa1fb756ede6076fb61ad5eb4f6c9926fe8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280714"
---
# <a name="updating-existing-xamarinforms-apps"></a>更新現有的 Xamarin 應用程式

_請遵循下列步驟來更新現有的 Xamarin. Forms 應用程式，以使用 Unified API 並更新至版本1.3。1_

> [!IMPORTANT]
> 因為1.3.1 是第一個支援 Unified API 的版本，所以整個解決方案應該更新為使用最新版本，同時將 iOS 應用程式遷移至 [整合]。 這表示除了更新 iOS 專案的整合支援之外，您還需要編輯方案中_所有_專案的程式碼。

更新會以兩個步驟執行：

1. 使用遷移工具中 Visual Studio for Mac 的組建，將 iOS 應用程式遷移至 Unified API。

    - 使用遷移工具自動更新專案。

    - 更新 ios 原生 Api，如[更新 ios 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)（特別是在自訂轉譯器或相依性服務程式代碼）的指示中所述。

2. 將整個解決方案更新為 Xamarin. 表單版本1.3。

    1. 安裝 1.3.1 NuGet 套件。

    2. 更新共用`App`程式碼中的類別。

    3. 更新 iOS `AppDelegate`專案中的。

    4. 更新 Android `MainActivity`專案中的。

    5. 更新 Windows Phone `MainPage`專案中的。

## <a name="1-ios-app-unified-migration"></a>1. iOS 應用程式（整合遷移）

遷移的一部分需要將 Xamarin. form 升級至版本1.3，以支援 Unified API。 為了要建立正確的元件參考，我們必須先更新 iOS 專案，以使用 Unified API。

### <a name="migration-tool"></a>遷移工具

按一下 iOS 專案，使其已選取，然後選擇 [**專案] > 遷移至 [Xamarin] [Unified API ...** ]，並同意出現的警告訊息。

![](updating-xamarin-forms-apps-images/beta-tool1.png "選擇 [專案] > 遷移至 Xamarin. iOS Unified API...並同意出現的警告訊息")

這會自動：

- 變更專案類型，以支援整合的64位 API。
- 將架構參考變更為 [ **Xamarin** ] （取代舊的**monotouch**參考）。
- 變更程式碼中的命名空間參考，以`MonoTouch`移除前置詞。
- 更新 **.csproj**檔案，以使用 Unified API 的正確組建目標。

**清除**並**建立**專案，以確保沒有其他錯誤可修正。 不需要採取任何進一步的動作。 這些步驟會在[Unified API](~/cross-platform/macios/unified/updating-ios-apps.md)檔中更詳細地說明。

### <a name="update-native-ios-apis-if-required"></a>更新原生 iOS Api （如有需要）

如果您已新增額外的 iOS 機器碼（例如自訂轉譯器或相依性服務），您可能需要執行額外的手動程式碼修正。 重新編譯您的應用程式，並參閱[更新現有的 IOS 應用程式指示](~/cross-platform/macios/unified/updating-ios-apps.md)，以取得可能需要變更的其他資訊。 [這些秘訣](~/cross-platform/macios/unified/updating-tips.md)也會協助您識別所需的變更。

## <a name="2-xamarinforms-131-update"></a>2.Xamarin. 表單1.3.1 更新

將 iOS 應用程式更新為 Unified API 之後，解決方案的其餘部分必須更新為 Xamarin. 表單版本1.3.1。 包括：

- 在每個專案中更新 Xamarin. Forms NuGet 套件。
- 將程式碼變更為使用新的`Application`Xamarin、 `FormsApplicationDelegate` （iOS）、 `FormsApplicationActivity` （Android）和`FormsApplicationPage` （Windows Phone）類別。

這些步驟如下所述：

### <a name="21-update-nuget-in-all-projects"></a>2.1 更新所有專案中的 NuGet

使用適用于解決方案中所有專案的 NuGet 套件管理員，將 Xamarin 更新為1.3.1 發行前版本：PCL （如果有的話）、iOS、Android 和 Windows Phone。 建議您**刪除並重新新增**Xamarin. Forms NuGet 套件，以更新至1.3 版。

> [!NOTE]
> 1\.3.1 目前處於*發行前*版本。 這表示您必須選取 NuGet 中的**發行前**版本選項（透過 Visual Studio for Mac 中的刻度，或 Visual Studio 中的下拉式清單），以查看最新的發行前版本。

> [!IMPORTANT]
> 如果您使用 Visual Studio，請確定已安裝最新版的 NuGet 套件管理員。 Visual Studio 中的舊版 NuGet 將無法正確安裝1.3.1 的統一版本。 移至 [**工具] > [延伸模組和更新 ...** ]，然後按一下 [**已安裝**] 清單，檢查**Visual Studio 的 NuGet 套件管理員**是否至少為版本2.8.5。 如果較舊，請按一下 [**更新**] 清單以下載最新版本。

將 NuGet 套件更新為1.3.1 之後，請在每個專案中進行下列變更，以升級至新`Xamarin.Forms.Application`的類別。

### <a name="22-portable-class-library-or-shared-project"></a>2.2 可移植的類別庫（或共用的專案）

變更**App.cs**檔案，讓：

- 類別現在會繼承自`Application`。 `App`
- `MainPage`屬性會設為您想要顯示的第一個內容頁面。

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

我們已完全`GetMainPage`移除方法，而改為在子`MainPage` `Application`類別上設定*屬性*。

這個新`Application` `OnStart`的基類也支援、和`OnSleep` `OnResume`覆寫，可協助您管理應用程式的生命週期。

類別接著會傳遞至每個應用`LoadApplication`程式專案中的新方法，如下所述： `App`

### <a name="23-ios-app"></a>2.3 iOS 應用程式

變更**AppDelegate.cs**檔案，讓：

- 類別繼承自`FormsApplicationDelegate` （而不是`UIApplicationDelegate`先前的）。
- `LoadApplication`使用的新實例`App`呼叫。

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

### <a name="23-android-app"></a>2.3 Android 應用程式

變更**MainActivity.cs**檔案，讓：

- 類別繼承自`FormsApplicationActivity` （而不是`FormsActivity`先前的）。
- `LoadApplication`使用的新實例呼叫`App`

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

### <a name="24-windows-phone-app"></a>2.4 Windows Phone 應用程式

我們必須更新**MainPage** -XAML 和後置兩者。

變更**MainPage** ，以便：

- 根 XAML 元素應該是`winPhone:FormsApplicationPage`。
- 屬性應變更為 `xmlns:phone``xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

更新後的範例如下所示-您只需要編輯這些專案（其餘的屬性應該保持不變）：

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

變更**MainPage.xaml.cs**檔案，讓：

- 類別繼承自`FormsApplicationPage` （而不是`PhoneApplicationPage`先前的）。
- `LoadApplication`使用新的 Xamarin `App`類別實例呼叫。 您可能需要完整限定此參考，因為 Windows Phone 已定義自己`App`的類別。

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

>您\_的專案 .csproj：錯誤：此專案參考這部電腦上遺失的 NuGet 套件。 啟用 NuGet 套件還原以下載它們。  如需詳細資訊，請參閱 http://go.microsoft.com/fwlink/?LinkID=322105 。 遺失的檔案為../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win + net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin. Forms. .targets。 （您\_的專案）

若要修正這些錯誤，請在文字編輯器中開啟 **.csproj**檔案，並`<Target`尋找參考舊版 Xamarin 的元素，例如如下所示的元素。 您應該從 **.csproj**檔案手動刪除此整個專案，並儲存變更。

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
> 如果您的錯誤格式為「_錯誤3不能在相同的 Xamarin 中同時包含 ' monotouch ' 和 ' xamarin '，則會明確參考 ' monotouch '，而 ' ' 則會參考 ' xxx，Version = 0.0.000，Culture =中性，PublicKeyToken = null ' "_ 將您的應用程式轉換成統一的 api 之後，通常是因為專案中的元件或 NuGet 套件尚未更新為 Unified API。 您必須移除現有的元件/NuGet、更新為支援統一 Api 的版本，並執行全新的組建。

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
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
