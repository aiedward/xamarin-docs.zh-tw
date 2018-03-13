---
title: "更新現有 Xamarin.Forms 應用程式"
description: "請依照下列步驟來更新現有 Xamarin.Forms 應用程式使用統一的 API，並更新到版本 1.3.1"
ms.topic: article
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 48b8d1cf8e6242fde632bceec5d482f53037a954
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="updating-existing-xamarinforms-apps"></a>更新現有 Xamarin.Forms 應用程式

_請依照下列步驟來更新現有 Xamarin.Forms 應用程式使用統一的 API，並更新到版本 1.3.1_

> [!IMPORTANT]
> Xamarin.Forms 1.3.1 是第一版支援統一的 API，因為整個方案應該更新為在相同的時間，以將 iOS 應用程式移轉至統一使用最新版本。 這表示，除了更新整合支援的 iOS 專案，您也必須編輯程式碼中的_所有_方案中的專案。

更新是以兩個步驟執行：

1. 將 iOS 應用程式移轉至 Mac 的移轉工具中的組建使用 Visual Studio 統一的 API。

    - 使用移轉工具會自動更新專案。

    - 更新 iOS 原生應用程式開發介面中的指示所述[更新 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)（特別是在自訂轉譯器或相依性服務程式碼）。

2. Xamarin.Forms 1.3 版更新整個方案。

    1. 安裝 Xamarin.Forms 1.3.1 NuGet 套件。

    2. 更新`App`共用程式碼中的類別。

    3. 更新`AppDelegate`iOS 專案中。

    4. 更新`MainActivity`Android 專案中。

    5. 更新`MainPage`在 Windows Phone 專案。

## <a name="1-ios-app-unified-migration"></a>1.iOS 應用程式 （Unified 移轉）

在移轉需要升級 Xamarin.Forms 1.3 版，這支援統一的 API。 為了建立正確的組件參考，我們需要更新使用統一的 API 的 iOS 專案。

### <a name="migration-tool"></a>移轉工具

按一下 iOS 專案，讓它被選取，然後選擇 **專案 > 移轉到 Xamarin.iOS 統一的 API...**並同意所出現的警告訊息。

![](updating-xamarin-forms-apps-images/beta-tool1.png "選擇 [專案] >...請移轉到 Xamarin.iOS 統一的 API，並同意所出現的警告訊息")

如此便會自動：

 - 變更以支援整合 64 位元應用程式開發介面的專案類型。
 - 變更 framework 參考**Xamarin.iOS** (取代舊**monotouch**參考)。
 - 變更命名空間中的參考程式碼來移除`MonoTouch`前置詞。
 - 更新**csproj**檔案，以使用正確的建置目標的統一的 API。

**全新**和**建置**專案，以確保沒有任何其他錯誤修正。 任何進一步的動作應該不是必要的。 這些步驟會更詳細地說明[統一的 API 文件](~/cross-platform/macios/unified/updating-ios-apps.md)。

### <a name="update-native-ios-apis-if-required"></a>更新原生 iOS 應用程式開發介面 （如有必要）

如果您已經加入其他的 iOS 原生程式碼 （例如自訂轉譯器或相依性服務） 您可能需要執行其他手動的程式碼修正。 重新編譯您的應用程式，並參考[更新現有的 iOS 應用程式指示](~/cross-platform/macios/unified/updating-ios-apps.md)如需詳細資訊，可能需要的變更。 [這些提示](~/cross-platform/macios/unified/updating-tips.md)也有助於識別所需的變更。

## <a name="2-xamarinforms-131-update"></a>2.Xamarin.Forms 1.3.1 更新

一旦 iOS 應用程式已更新為統一的 API，其餘的解決方案需要更新為 Xamarin.Forms 1.3.1 版。 包括：

 - 正在更新每個專案中的 Xamarin.Forms NuGet 套件。
 - 變更程式碼來使用新的 Xamarin.Forms `Application`， `FormsApplicationDelegate` (iOS)、 `FormsApplicationActivity` (Android) 和`FormsApplicationPage`(Windows Phone) 類別。

下列步驟說明如下：

### <a name="21-update-nuget-in-all-projects"></a>2.1 更新所有專案中的 NuGet

更新 1.3.1 Xamarin.Forms 發行前版本方案中的所有專案使用 NuGet 封裝管理員： PCL （如果有的話）、 iOS、 Android 和 Windows Phone。 建議您**刪除並重新加入**更新至 1.3 版的 Xamarin.Forms NuGet 套件。

**注意：** Xamarin.Forms 1.3.1 版目前處於*發行前版本*。 這表示您必須選取**發行前版本**NuGet 中 （刻度方塊在 Visual Studio for Mac） 或 drop down list Visual Studio 中透過選項以查看最新的發行前版本。

> [!IMPORTANT]
> 如果您使用 Visual Studio，請確定已安裝最新版本的 NuGet 套件管理員。 NuGet Visual Studio 中的舊版本不會正確地安裝 Xamarin.Forms 1.3.1 統一版本。 移至**工具 > 擴充功能和更新...** ，然後按一下 **已安裝**清單，請檢查**for Visual Studio 的 NuGet 套件管理員**至少為版本 2.8.5。 如果較舊，按一下**更新**下載最新版本的清單。

一旦您已更新的 NuGet 套件至 Xamarin.Forms 1.3.1，進行下列變更每個專案升級至新`Xamarin.Forms.Application`類別。

### <a name="22-portable-class-library-or-shared-project"></a>2.2 可攜式類別庫 （或共用的專案）

變更**App.cs**檔案以便：

 - `App`類別會繼承自`Application`。
 - `MainPage`屬性設定為您想要顯示的第一個內容頁面。

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

我們已完全移除`GetMainPage`方法，並改為設定`MainPage`*屬性*上`Application`子類別。

這個新`Application`基底類別也支援`OnStart`， `OnSleep`，和`OnResume`覆寫，以協助您管理您的應用程式生命週期。

`App`類別接著會傳遞給新`LoadApplication`中每個應用程式專案，如下所述的方法：

### <a name="23-ios-app"></a>2.3 iOS 應用程式

變更**d**檔案以便：

 - 類別繼承自`FormsApplicationDelegate`(而不是`UIApplicationDelegate`之前)。
 - `LoadApplication` 呼叫時所使用的新執行個體`App`。

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

### <a name="23-android-app"></a>2.3 android 應用程式

變更**Weatherapp**檔案以便：

 - 類別繼承自`FormsApplicationActivity`(而不是`FormsActivity`之前)。
 - `LoadApplication` 呼叫時所使用的新執行個體 `App`

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

我們需要更新**MainPage** -XAML 和程式碼後置。

變更**MainPage.xaml**檔案以便：

 - 根 XAML 項目應該是`winPhone:FormsApplicationPage`。
 - `xmlns:phone`屬性應該*變更*至 `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

已更新的範例如下所示-您只需要編輯 （其餘的屬性應該維持不變） 下列項目：

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

變更**MainPage.xaml.cs**檔案以便：

 - 類別繼承自`FormsApplicationPage`(而不是`PhoneApplicationPage`之前)。
 - `LoadApplication` 使用 Xamarin.Forms 中的新執行個體呼叫`App`類別。 您可能需要完整限定這個參考，因為 Windows Phone 它本身的`App`類別已經定義過了。

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

偶爾您會看到更新的 Xamarin.Forms NuGet 套件之後，這類似的錯誤。 NuGet updater 並不會完全移除來自較舊版本的參考時發生您**csproj**檔案。

>您\_PROJECT.csproj： 錯誤： 此專案參考這部電腦所缺少的 NuGet 套件。 請啟用 NuGet 封裝還原，以下載。  如需詳細資訊，請參閱 http://go.microsoft.com/fwlink/?LinkID=322105。 遺失的檔案是.../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets。 (您\_專案)

若要修正這些錯誤，請開啟**csproj**檔案文字編輯器中，並尋找`<Target`舊版透過 Xamarin.Forms，例如如下所示的項目是指的項目。 您應該手動刪除這個整個項目從**csproj**檔案及儲存所做的變更。

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

一旦移除這些舊的參考，應該會順利建置專案。

## <a name="considerations"></a>考量

下列考量轉換現有 Xamarin.Forms 專案從傳統應用程式開發介面，新的統一的 api 如果該應用程式依賴一或多個元件或 NuGet 封裝時應該納入考量。

### <a name="components"></a>元件

您的應用程式中加入的任何元件也需要更新，以統一的 API，或當您嘗試編譯時，您會收到發生衝突。 任何包含的元件，以支援統一的 API 的 Xamarin 元件存放區中的新版本取代目前的版本，執行乾淨的組建。 尚未轉換作者，任何元件會顯示只有警告元件存放區中的 32 位元。

### <a name="nuget-support"></a>NuGet 支援

雖然我們提供 NuGet 使用統一的 API 支援的變更時，有尚未新版的 NuGet，所以我們會評估如何取得 NuGet 辨識新的 Api。

這段時間，就像元件，直到您必須切換任何您要加入專案，以支援統一的 Api 版本的 NuGet 封裝，之後執行乾淨的組建。

> [!IMPORTANT]
> **注意：**如果您在表單中有錯誤_"錯誤 3 不能在相同的 Xamarin.iOS 專案中包含 'monotouch.dll' 和 'Xamarin.iOS.dll'-'monotouch.dll' 正由時明確地參考 'Xamarin.iOS.dll' ' xxx，版本 = 0.0.000，Culture = neutral，PublicKeyToken = null'"_之後轉換您的應用程式，以統一的 Api，它通常是因為有尚未更新統一的 API 的專案中的元件或 NuGet 封裝。 您必須移除現有元件/NuGet、 更新為支援統一的 Api 版本，執行乾淨的組建。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 64 位元建置的 Xamarin.iOS 應用程式

Xamarin.iOS 行動應用程式已經轉換成統一的 API，開發人員仍必須啟用 64 位元電腦的應用程式，從應用程式的選項建立。 請參閱**啟用 64 位元建置的 Xamarin.iOS 應用程式**的[32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md#enable-64)建置文件，如需有關啟用 64 位元的詳細指示。

## <a name="summary"></a>總結

Xamarin.Forms 應用程式現在應該更新為版本 1.3.1 和 iOS 應用程式移轉至統一的 API （可支援 64 位元架構上的 iOS 平台）。

如上所述，如果 Xamarin.Forms 應用程式包括原生程式碼，例如自訂轉譯器或相依性服務，則這些也可能需要更新為使用新的型別[統一的 API 中導入](~/cross-platform/macios/index.md)。

## <a name="related-links"></a>相關連結

- [更新的 iOS 應用程式](~/cross-platform/macios/unified/updating-apps.md)
- [更新的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [更新的秘訣](~/cross-platform/macios/unified/updating-tips.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
