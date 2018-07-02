---
title: Xamarin.Forms 深度剖析
description: 本文會檢查使用 Xamarin.Forms 開發應用程式的基本概念。 涵蓋的主題包含 Xamarin.Forms 應用程式的結構、架構和應用程式基本概念，以及使用者介面。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d97aa580-1eb9-48b3-b15b-0d7421ea7ae
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2018
ms.openlocfilehash: 7c8eee5fc7075f23221c06dab29b83b1d5e01ffc
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269066"
---
# <a name="xamarinforms-deep-dive"></a>Xamarin.Forms 深度剖析

在 [Xamarin.Forms 快速入門](~/xamarin-forms/get-started/hello-xamarin-forms/quickstart.md)中，建置了 Phoneword 應用程式。 本文會檢閱已經建立的項目，以了解 Xamarin.Forms 應用程式運作方式的基本概念。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 是 Microsoft 功能強大的 IDE。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等等。 本文著重在搭配 Xamarin 外掛程式使用一些基本的 Visual Studio 功能。

Visual Studio 會將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Phoneword 應用程式是由一個包含四個專案的方案所組成，如下列螢幕擷取畫面所示。

![](deepdive-images/vs/solution.png "Visual Studio 方案總管")

這些專案包括：

- Phoneword - 此專案是包含所有共用程式碼和共用 UI 的 .NET Standard 程式庫專案。
- Phoneword.Android - 此專案包含 Android 專用程式碼，並且是 Android 應用程式的進入點。
- Phoneword.iOS - 此專案容納 iOS 專用的程式碼，而且是 iOS 應用程式的進入點。
- Phoneword.UWP - 此專案容納通用 Windows 平台 (UWP) 專用的程式碼，而且是 UWP 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 應用程式的結構

以下螢幕擷取畫面示範 Visual Studio 中 Phoneword .NET Standard 程式庫專案的內容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard 專案內容")

此專案具有 [相依性] 節點，其中包含 [NuGet] 和 [SDK] 節點。 [NuGet] 節點包含已新增到專案中的 Xamarin.Forms NuGet 套件，而 [SDK] 節點則包含 `NETStandard.Library` 中繼套件，此中繼套件參考一組定義 .NET Standard 的完整 NuGet 套件。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

Visual Studio for Mac 是免費的開放原始碼 IDE，類似 Visual Studio。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等等。 如需 Visual Studio for Mac 的詳細資訊，請參閱 [Visual Studio for Mac 簡介](/visualstudio/mac/)。

Visual Studio for Mac 遵循 Visual Studio 的做法，將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Phoneword 應用程式由一個包含三個專案的方案所組成，如以下螢幕擷取畫面所示。

![](deepdive-images/xs/solution.png "Visual Studio for Mac 方案窗格")

這些專案包括：

- Phoneword - 此專案是包含所有共用程式碼和共用 UI 的 .NET Standard 程式庫專案。
- Phoneword.Droid - 此專案容納 Android 專用的程式碼，而且是 Android 應用程式的進入點。
- Phoneword.iOS - 此專案容納 iOS 專用的程式碼，而且是 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 應用程式的結構

以下螢幕擷取畫面顯示 Visual Studio for Mac 中 Phoneword .NET Standard 程式庫專案的內容：

![](deepdive-images/xs/library-project.png "Phoneword .NET Standard 程式庫專案內容")

此專案具有 [相依性] 節點，其中包含 [NuGet] 和 [SDK] 節點。 [NuGet] 節點包含已新增到專案中的 Xamarin.Forms NuGet 套件，而 [SDK] 節點則包含 `NETStandard.Library` 中繼套件，此中繼套件參考一組定義 .NET Standard 的完整 NuGet 套件。

-----

此專案也包含多個檔案：

- **App.xaml** - `App` 類別的 XAML 標記，可定義應用程式的資源字典。
- **App.xaml.cs** - `App` 類別的程式碼後置，負責將應用程式在每個平台上顯示的第一個頁面初始化，以及處理應用程式生命週期事件。
- **IDialer.cs** - `IDialer` 介面，指定 `Dial` 方法必須由任何實作類別提供。
- **MainPage.xaml** - `MainPage` 類別的 XAML 標記，定義應用程式啟動時所顯示之頁面的 UI。
- **MainPage.xaml.cs** - `MainPage` 類別的程式碼後置，其中包含使用者與頁面互動時所執行的商務邏輯。
- **PhoneTranslator.cs** - 商務邏輯，負責將電話文字轉換為從 **MainPage.xaml.cs** 叫用的電話號碼。

如需有關 Xamarin.iOS 應用程式結構的詳細資訊，請參閱 [Xamarin.iOS 應用程式的結構](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy)。 如需有關 Xamarin.Android 應用程式結構的詳細資訊，請參閱 [Xamarin.Android 應用程式的結構](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>架構和應用程式基本概念

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin.Forms 應用程式的架構方式與傳統的跨平台應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖顯示此 Phoneword 應用程式關聯性的概觀：

![](deepdive-images/vs/architecture.png "Phoneword 架構")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Xamarin.Forms 應用程式的架構方式與傳統的跨平台應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖顯示此 Phoneword 應用程式關聯性的概觀：

![](deepdive-images/xs/architecture.png "Phoneword 架構")

-----

若要將啟動程式碼的重複使用發揮到極致，Xamarin.Forms 應用程式有一個名為 `App` 的單一類別，此類別負責在每個平台上具現化應用程式將顯示的第一頁，如下列程式碼範例所示：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Phoneword
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new MainPage();
        }
        ...
    }
}
```

此程式碼會將 `App` 類別的 `MainPage` 屬性設定為 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 類別的新執行個體。 此外，[`XamlCompilation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/) 屬性還會開啟 XAML 編譯器，以便將 XAML 直接編譯成中繼語言。 如需詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>啟動每個平台上的應用程式

### <a name="ios"></a>iOS

若要在 iOS 中啟動 Xamarin.Forms 初始頁面，Phoneword.iOS 專案會包含繼承自 `FormsApplicationDelegate` 類別的 `AppDelegate` 類別，如下列程式碼範例所示：

```csharp
namespace Phoneword.iOS
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init ();
            LoadApplication (new App ());
            return base.FinishedLaunching (app, options);
        }
    }
}
```

`FinishedLaunching` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在 `LoadApplication` 方法的呼叫設定根檢視控制器之前，先在應用程式中載入 iOS 專用的 Xamarin.Forms 實作。

### <a name="android"></a>Android

為了在 Android 中啟動 Xamarin.Forms 初始頁面，Phoneword.Droid 專案包含了使用 `MainLauncher` 屬性來建立 `Activity` 的程式碼，其中活動會從 `FormsAppCompatActivity` 類別繼承，如以下程式碼範例所示：

```csharp
namespace Phoneword.Droid
{
    [Activity(Label = "Phoneword", 
              Icon = "@mipmap/icon", 
              Theme = "@style/MainTheme", 
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        internal static MainActivity Instance { get; private set; }

        protected override void OnCreate(Bundle bundle)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(bundle);
            Instance = this;
            global::Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication(new App());
        }
    }
}
```

`OnCreate` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在載入 Xamarin.Forms 應用程式之前，先在應用程式中載入 Android 專用的 Xamarin.Forms 實作。 此外，`MainActivity` 類別會在 `Instance` 屬性中儲存對自己的參考。 `Instance` 屬性稱為本機內容，是從 `PhoneDialer` 類別參考的屬性。

## <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 應用程式中，初始化 Xamarin.Forms 架構的 `Init` 方法是從 `App` 類別叫用的：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

如此會在應用程式中載入 UWP 專用的 Xamarin.Forms 實作。 如下列程式碼範例所示，Xamarin.Forms 的初始頁面是由 `MainPage` 類別所啟動：

```csharp
namespace Phoneword.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Phoneword.App());
        }
    }
}
```

Xamarin.Forms 應用程式是以 `LoadApplication` 方法來載入。

> [!NOTE]
> 通用 Windows 平台 (UWP) 應用程式可以使用 Xamarin.Forms 建置，但只能在 Windows 上使用 Visual Studio 建置。

## <a name="user-interface"></a>使用者介面

有四個主要的控制項群組可用來建立 Xamarin.Forms 應用程式的使用者介面。

1. **頁面** - Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 Phoneword 應用程式使用 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 類別顯示單一畫面。 如需有關頁面的詳細資訊，請參閱 [Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **版面配置** - Xamarin.Forms 版面配置是將檢視構成邏輯結構所使用的容器。 Phoneword 應用程式使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 類別，以水平堆疊排列控制項。 如需有關版面配置的詳細資訊，請參閱 [Xamarin.Forms 版面配置](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **檢視** - Xamarin.Forms 檢視是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 Phoneword 應用程式會使用 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)、[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 和 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控制項。 如需有關檢視的詳細資訊，請參閱 [Xamarin.Forms 檢視](~/xamarin-forms/user-interface/controls/views.md)。
1. **資料格** - Xamarin.Forms 資料格是在清單中用於項目的特定元素，並描述如何在清單中繪製每個項目。 Phoneword 應用程式不會使用任何資料格。 如需有關資料格的詳細資訊，請參閱 [Xamarin.Forms 資料格](~/xamarin-forms/user-interface/controls/cells.md)。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

在任何平台上執行 Phoneword 應用程式時，它會顯示對應至 Xamarin.Forms 中 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 的單一畫面。 `Page` 在 Android 中代表「ViewGroup」、在 iOS 中代表「檢視控制器」、在通用 Windows 平台上則代表「頁面」。 Phoneword 應用程式也會將代表 `MainPage` 類別的 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 物件具現化，其 XAML 標記如下列程式碼範例所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                         xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                         x:Class="Phoneword.MainPage">
        ...
        <StackLayout>
            <Label Text="Enter a Phoneword:" />
            <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
            <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
            <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
</ContentPage>
```

`MainPage` 類別會使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控制項自動在大小不同的螢幕上排列控制項。 每個子項目都是以它們加入的順序，一個接著一個地垂直放置。 `StackLayout` 控制項包含一個在頁面上顯示文字的 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控制項、一個接受使用者文字輸入的 [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 控制項，以及兩個用來執行程式碼以回應觸控事件的 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控制項。

如需有關 Xamarin.Forms 中 XAML 的詳細資訊，請參閱 [Xamarin.Forms XAML 基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 XAML 中定義的物件可以引發程式碼後置檔案中處理的事件。 下列程式碼範例在 `MainPage` 類別的程式碼後置中示範 `OnTranslate` 方法，執行此類別可回應在 [翻譯] 按鈕上引發的 [`Clicked`](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) 事件。

```csharp
void OnTranslate(object sender, EventArgs e)
{
    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
        callButton.IsEnabled = true;
        callButton.Text = "Call " + translatedNumber;
    } else {
        callButton.IsEnabled = false;
        callButton.Text = "Call";
    }
}
```

`OnTranslate` 方法會將 phoneword 翻譯成其對應的電話號碼，並在回應中，設定 [撥號] 按鈕的屬性。 XAML 類別的程式碼後置檔案可以存取在 XAML 中使用獲指派名稱及 `x:Name` 屬性定義的物件。 指派給此屬性的值與 C# 變數具有相同的規則，因為該值的開頭必須是字母或底線，而且不得包含內嵌的空格。

[翻譯] 按鈕與 `OnTranslate` 方法會以 `MainPage` 類別的 XAML 標記連接：

```xaml
<Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中導入的其他概念

適用於 Xamarin.Forms 的 Phoneword 應用程式導入了幾個未涵蓋在本文中的概念。 這些概念包括：

- 啟用和停用按鈕。 變更 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 的 [`IsEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) 屬性可以將它開啟或關閉。 例如，下列程式碼範例會停用 `callButton`：

    ```csharp
    callButton.IsEnabled = false;
    ```

- 顯示警示對話方塊。 當使用者按下撥號**按鈕**時，Phoneword 應用程式會顯示一個「警示對話方塊」，其中包含撥號或取消撥號的選項。 [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) 方法用來建立對話方塊，如下列程式碼範例所示：

    ```csharp
    await this.DisplayAlert (
            "Dial a Number",
            "Would you like to call " + translatedNumber + "?",
            "Yes",
            "No");
    ```

- 透過 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 類別存取原生功能。 Phoneword 應用程式會使用 `DependencyService` 類別，將 `IDialer` 介面解析為平台專屬的電話撥號實作，如 Phoneword 專案中的下列程式碼範例所示：

    ```csharp
    async void OnCall (object sender, EventArgs e)
    {
        ...
        var dialer = DependencyService.Get<IDialer> ();
        ...
    }
    ```

  如需有關 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 類別的詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

- 透過 URL 撥打電話。 Phoneword 應用程式會使用 `OpenURL` 啟動系統電話應用程式。 URL 是由 `tel:` 首碼後面緊接著要撥打的電話號碼所組成，如 iOS 專案中的下列程式碼範例所示：

    ```csharp
    return UIApplication.SharedApplication.OpenUrl (new NSUrl ("tel:" + number));
    ```

- 調整平台版面配置。 [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) 類別可讓開發人員針對各個平台，自訂應用程式版面配置和功能，如下列在不同平台上使用不同 [`Padding`](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) 值以正確顯示每個頁面的程式碼範例所示：

    ```xaml
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms" ... >
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        ...
    </ContentPage>
    ```

  如需有關平台調整的詳細資訊，請參閱[裝置類別](~/xamarin-forms/platform/device.md)。

## <a name="testing-and-deployment"></a>建置和部署

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 為應用程式偵錯是應用程式開發週期的共同部分，有助於診斷程式碼問題。 如需詳細資訊，請參閱[設定中斷點](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/)、[逐步執行程式碼](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/)，以及[將資訊輸出至記錄視窗](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/)。

模擬器是開始部署與測試應用程式的絕佳位置，而且具備用於測試應用程式的實用功能。 不過，使用者將不會在模擬器中取用最終的應用程式，因此應該及早且經常在實際裝置上測試應用程式。 如需有關 iOS 裝置佈建的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)。 如需有關 Android 裝置佈建的詳細資訊，請參閱[設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="summary"></a>總結

本文已檢查過使用 Xamarin.Forms 開發應用程式的基本概念。 涵蓋的主題包含 Xamarin.Forms 應用程式的結構、架構和應用程式基本概念，以及使用者介面。

在本指南的下一節中，將會延伸應用程式以包含多個畫面，即可探索更進階的 Xamarin.Forms 架構和概念。
