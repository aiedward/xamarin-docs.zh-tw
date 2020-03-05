---
title: 在 Xamarin Forms 應用程式中偵測深色模式
description: 在任何 Xamarin. Forms 應用程式中，都可以使用 Resourcedictionary、DynamicResources 和 platform 知識的組合來支援深色模式。
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291619"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>在 Xamarin Forms 應用程式中偵測深色模式

Xamarin 應用程式可以使用可讓您支援[主題](theming.md)的相同策略，來回應作業系統主題變更。 主要的差異在於主題的變更如何觸發。 深色模式指的是一組更廣泛的外觀喜好設定，可以在作業系統層級設定，而哪些應用程式應該會遵循並立即回應。

在您的 Xamarin 中使用深色模式的最低需求。表單應用程式如下：

- iOS 13 或更新版本。
- Android 9 或更新版本（Android 9 支援可供您查詢的面板模式）。
- Android 10 或更新版本（Android 10 會通知應用程式的模式變更）。
- UWP v 10.0.10240.0 或更新版本。
- Xamarin。表單（任何版本）。

支援面板模式的程式，包括淺色和深色，如下所示：

1. 在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義整個應用程式所共用的資源。
2. 針對每個面板模式定義資源字典主題，以針對您想要變更的每個樣式提供特定的覆寫。
3. 在應用程式的**app.xaml**檔案中設定預設面板模式主題。
4. 使用 `DynamicResource` 標記延伸來為您的應用程式樣式，而您希望樣式在面板模式變更時做出反應。
5. 新增程式碼以接聽 OS 主題變更，並載入應用程式的對應主題。

下列螢幕擷取畫面顯示 [淺色] 和 [深色] 模式的主題頁面：

[![Ios 和 android 上主題應用程式主頁面的螢幕擷取畫面](theming-images/main-page-both-themes.png "主題應用程式的主頁面")](theming-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面")
[ ![ios 和 android 上主題應用程式詳細資料頁面的螢幕擷取畫面](theming-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](theming-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

## <a name="define-themes"></a>定義主題

遵循[主題指南](theming.md)，取得有關建立深色和淺色主題的逐步解說詳細資料。

## <a name="react-to-appearance-mode-changes"></a>回應面板模式變更

裝置上的面板模式可能會因為各種不同的原因而變更，這取決於使用者如何設定其喜好設定，包括明確選擇模式、一天的時間，或像是低光線的環境因素。 您必須新增平臺程式碼，以確保您的應用程式可以對這些變更做出回應，下列各節會更詳細地討論。

### <a name="android"></a>Android

在您應用程式的**MainActivity.cs**檔中，將 `ConfigChanges.UiMode` 欄位新增至 `Activity` 屬性中的 `ConfigurationChanges` 屬性，讓您的應用程式會收到 UI 模式變更的通知：

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

在相同的**MainActivity.cs**檔案中，覆寫 `OnConfigurationChanged` 方法：

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

在 iOS 上，面板模式變更會在 UIViewController 上收到通知，而表單則是 `ContentPage`。 若要覆寫該方法，請在您的 iOS 專案中建立名為 `PageRenderer.cs`的自訂轉譯器：

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

覆寫 `TraitCollectionDidChange` 方法，可讓您對 `UserInterfaceStyle` 變更採取動作。

### <a name="uwp"></a>UWP

在 UWP 上，將下列程式碼新增至應用程式的**MainPage.xaml.cs**檔案：

```csharp
public sealed partial class MainPage
{

    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>設定深色和淺色主題

遵循[主題](theming.md)指南之後，您可以在上述平臺程式碼的適當位置，輕鬆地為您的應用程式設定新的主題。 若要載入新的主題，只要以您選擇的主題資源字典取代應用程式的目前資源字典即可：

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>相關連結

- [主題（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin 中的動態樣式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
