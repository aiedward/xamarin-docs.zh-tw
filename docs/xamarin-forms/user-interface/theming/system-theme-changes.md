---
title: 回應應用程式中的系統主題變更 Xamarin.Forms
description: Xamarin.Forms應用程式可以使用 OnAppTheme 類型和 DynamicResource 標記延伸來回應作業系統主題變更。
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b451fe004ca21c8770658f31c9c38253e073c259
ms.sourcegitcommit: 82eabb0eaa4a674897aa6d5e64efb91fd580c330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86100179"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>回應應用程式中的系統主題變更 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

裝置通常包含淺色和深色的主題，分別代表一組可在作業系統層級上設定的廣泛外觀喜好設定。 應用程式應該遵守這些系統主題，並在系統主題變更時立即回應。

視裝置設定而定，系統主題可能會因為各種不同的原因而變更。 這包括使用者要明確變更的系統主題，它會因為一天內的時間而變更，而且會因為環境因素（例如低光源）而變更。

Xamarin.Forms應用程式可以透過使用具有 `AppThemeBinding` 標記延伸的資源，以及 `SetAppThemeColor` 和擴充方法，來回應系統主題變更 `SetOnAppTheme<T>` 。

> [!IMPORTANT]
> 回應系統主題變更目前為實驗性，而且只能透過設定旗標來使用 `AppTheme_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

Xamarin.Forms若要回應系統主題變更，必須符合下列需求：

- Xamarin.Forms4.6.0.967 或更高版本。
- iOS 13 或更新版本。
- Android 10 （API 29）或更新版本。
- UWP 組建14393或更新版本。

下列螢幕擷取畫面顯示 iOS 和 Android 上的淺色和深色系統主題的主題頁面：

[![IOS 和 Android 上主題應用程式主頁面的螢幕擷取畫面](system-theme-changes-images/main-page-both-themes.png "主題應用程式的主頁面")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面") 
[ ![IOS 和 Android 上主題應用程式的詳細資料頁面螢幕擷取畫面](system-theme-changes-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

## <a name="define-and-consume-theme-resources"></a>定義和使用主題資源

淺色與深色主題的資源可以搭配 `AppThemeBinding` 標記延伸和 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 擴充方法使用。 使用這些方法，會根據目前系統主題的值自動套用資源。 此外，如果系統主題在應用程式執行時變更，則會自動更新取用這些資源的物件。

### <a name="appthemebinding-markup-extension"></a>AppThemeBinding 標記延伸

`AppThemeBinding`標記延伸可讓您根據目前的系統主題，取用如影像或色彩的資源：

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

在此範例中，當裝置使用其淺色主題時，第一個的文字色彩 [`Label`](xref:Xamarin.Forms.Label) 會設定為綠色，而當裝置使用其暗色調主題時，會設為紅色。 同樣地，會 [`Image`](xref:Xamarin.Forms.Image) 根據目前的系統主題來顯示不同的影像檔案。

此外，在中定義的資源 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以與 `StaticResource` 標記延伸搭配使用：

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

在此範例中，和樣式的背景色彩 [`Grid`](xref:Xamarin.Forms.Grid) [`Button`](xref:Xamarin.Forms.Button) 會根據裝置是否使用其淺色主題或深色主題而變更。

如需標記延伸的詳細資訊 `AppThemeBinding` ，請參閱[AppThemeBinding 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)。

### <a name="extension-methods"></a>擴充方法

Xamarin.Forms包含 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 擴充方法，可讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件回應系統主題變更。

`SetAppThemeColor`方法會根據 [`Color`](xref:Xamarin.Forms.Color) 目前的系統主題，指定要在目標屬性上設定的物件：

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label) 當裝置使用其淺色主題時，的文字色彩會設定為綠色，而當裝置使用其暗色調主題時，會設為紅色。

方法會根據 `SetOnAppTheme<T>` `T` 目前的系統主題，指定要在目標屬性上設定之類型的物件：

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) `lightlogo.png` 當裝置使用其淺色主題，以及 `darklogo.png` 裝置使用其暗色調主題時，會顯示。

## <a name="detect-the-current-system-theme"></a>偵測目前的系統主題

藉由取得屬性的值，可以偵測到目前的系統主題 `Application.RequestedTheme` ：

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

屬性會傳回 `RequestedTheme` `OSAppTheme` 列舉成員。 `OSAppTheme` 列舉會定義下列成員：

- `Unspecified`，表示裝置正在使用未指定的主題。
- `Light`，表示裝置正在使用其淺色主題。
- `Dark`，表示裝置正在使用其深色主題。

## <a name="set-the-current-user-theme"></a>設定目前的使用者主題

應用程式所使用的主題可以設定為 `Application.UserAppTheme` 類型的屬性，而 `OSAppTheme` 不論目前有哪些系統主題可運作：

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

在此範例中，應用程式會設定為使用針對系統深色模式定義的主題，而不論目前有哪些系統主題正在運作。

> [!NOTE]
> 將 `UserAppTheme` 屬性設定為， `OSAppTheme.Unspecified` 預設為作業系統主題。

## <a name="react-to-theme-changes"></a>回應主題變更

裝置上的系統主題可能會因為各種不同的原因而變更，視裝置的設定方式而定。 Xamarin.Forms當系統主題藉由處理事件而變更時，可以通知應用程式 `Application.RequestedThemeChanged` ：

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

`AppThemeChangedEventArgs`事件隨附的物件 `RequestedThemeChanged` 具有名為的單一屬性 `RequestedTheme` ，其類型為 `OSAppTheme` 。 您可以檢查此屬性以偵測要求的系統主題。

> [!IMPORTANT]
> 若要回應 Android 上的主題變更，您必須 `ConfigChanges.UiMode` 在類別的屬性中包含旗標 `Activity` `MainActivity` 。

## <a name="related-links"></a>相關連結

- [SystemThemes （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [AppThemeBinding 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.Forms使用 XAML 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
