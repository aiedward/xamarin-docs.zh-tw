---
title: 檢測 Xamarin 中的暗模式。
description: 任何 Xamarin.Forms 應用程式中都可以使用資源字典、動態資源和平台知識的組合支援暗模式。
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 7fe1a98e6a497a5791f26df2fc96d41781b71ef6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628304"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>檢測 Xamarin 中的暗模式。

Xamarin.Forms應用程式可以使用支援[主題](theming.md)的相同策略來回應作業系統主題更改。 主要區別在於如何觸發主題的變化。 暗模式是指可在操作系統級別設置的更廣泛的外觀首選項集,並且哪些應用程式應立即尊重和回應。

在 Xamarin.Forms 應用程式中使用深色模式的最低要求是:

- iOS 13 或更高。
- Android 9 或更高(Android 9 支援您可以查詢的外觀模式)。
- Android 10 或更高(Android 10 通知應用模式更改)。
- UWP v10.0.10240.0 或更高。
- Xamarin.窗體(任何版本)。

支援外觀模式(包括淺色和深色)的過程如下:

1. 定義整個應用程式在中[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)共用的資源。
2. 為每個外觀模式定義資源字典主題,該模式提供特定於要更改的每個樣式的覆蓋。
3. 在應用程式的**App.xaml**檔中設置預設外觀模式主題。
4. 使用`DynamicResource`標記擴展對應用程式進行樣式設置,您希望樣式在外觀模式更改時做出反應。
5. 添加代碼以偵聽操作系統主題更改,並載入應用程式的相應主題。

以下螢幕截圖顯示了主題頁面,用於淺色和深色模式:

[![主題應用程式主頁的螢幕截圖,在 iOS 和 Android 上](theming-images/main-page-both-themes.png "主題應用程式的首頁")](theming-images/main-page-both-themes-large.png#lightbox "主題應用程式的首頁")
[![主題應用程式的詳細資訊頁面的](theming-images/detail-page-both-themes.png "主題應用程式的詳細資訊頁面")Android 螢幕截圖](theming-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資訊頁面")

## <a name="define-themes"></a>定義主題

請按照[主題指南](theming.md)瞭解有關創建黑暗和淺色主題的逐步詳細資訊。

您可以在平台代碼的適當位置輕鬆為應用程式設置新主題。 要載入新主題,只需將應用程式的目前資源字典取代為您選擇的主題資源字典:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>偵測和應用程式主題

使用[`RequestedTheme`](~/essentials/app-theme.md) [Xamarin.基本(](~/essentials/index.md)版本 1.4.0 或更高版本)可以檢測當前運行的主題。 然後,您可以在新類或`App`類別建立說明器方法來設定主題:

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
        {
            // Change to dark theme
            // e.g. App.Current.Resources = new YourDarkTheme();
        }
        else
        {
            // Change to light theme
            // e.g. App.Current.Resources = new YourLightTheme();
        }
    }
}
```

## <a name="react-to-appearance-mode-changes"></a>回應外觀模式變更

設備上的外觀模式可能會由於各種原因而改變,具體取決於使用者配置首選項的方式,包括顯式選擇模式、時間或環境因素(如低光)。 您必須添加平台代碼,以確保應用程式能夠對這些更改做出反應,以下各節將更詳細地討論此更改。

### <a name="android"></a>Android

要在應用中支援深色模式,必須更新應用的主題,可在 中`Resources/values/styles.xml`找到的主題,以便`DayNight`從 主題繼承:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

如果您已升級到 AndroidX[的材料元件](https://www.nuget.org/packages/Xamarin.Google.Android.Material/)(1.1.0-rc2) 或更新,您可以使用:

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

在應用程式的**MainActivity.cs**檔案中`ConfigChanges.UiMode`,將該欄位`ConfigurationChanges``Activity`新增到 屬性中的屬性,以便通知應用 UI 模式變更:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

在同一**MainActivity.cs**檔案中,`OnConfigurationChanged`重寫 方法:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);
    App.ApplyTheme();
}
```

### <a name="ios"></a>iOS

在 iOS 上, 在 UIViewController 上通知外觀模式變更,在 Xamarin.窗體`ContentPage`中,該控制器是 。 為了重寫該方法,請在 iOS 項目中建立自訂呈現器,`PageRenderer.cs`稱為 :

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
                App.ApplyTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            App.ApplyTheme();
        }
    }
}
```

重寫`TraitCollectionDidChange`該方法使您能夠`UserInterfaceStyle`對 更改執行操作。

### <a name="uwp"></a>UWP

在 UWP 上,將以下代碼加入到應用程式的**MainPage.xaml.cs**檔案中:

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
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>相關連結

- [「範例」(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin 中的動態樣式。](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
