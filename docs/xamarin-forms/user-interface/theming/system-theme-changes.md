---
title: 回應應用程式中的系統主題變更 Xamarin.Forms
description: Xamarin.Forms 應用程式可以使用 OnAppTheme 類型和 DynamicResource 標記延伸來回應作業系統主題變更。
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 421fdc3eed86ed93fe0416843867553778abd0c5
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940521"
---
# <a name="respond-to-system-theme-changes-in-no-locxamarinforms-applications"></a>回應應用程式中的系統主題變更 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

裝置通常包含淺色和深色的主題，每個都參考一組可以在作業系統層級設定的廣泛外觀喜好設定。 應用程式應該遵守這些系統主題，並在系統主題變更時立即回應。

視裝置設定而定，系統主題可能會因各種原因而變更。 這包括使用者明確變更的系統主題、由於時間的變化而變更，以及因環境因素（例如低燈光）而變更。

Xamarin.Forms 應用程式可以藉由使用具有 `AppThemeBinding` 標記延伸的資源，以及 `SetAppThemeColor` 和延伸方法，來回應系統主題變更 `SetOnAppTheme<T>`  。

必須符合下列需求， Xamarin.Forms 才能回應系統主題變更：

- Xamarin.Forms 4.6.0.967 或更高。
- iOS 13 或更新版本。
- Android 10 (API 29) 或更新版本。
- UWP 組建14393或更新版本。
- macOS 10.14 或更新版本。

下列螢幕擷取畫面顯示在 iOS 和 Android 上適用于淺色和深色系統主題的主題頁面：

[![IOS 和 Android 上主題應用程式主頁面的螢幕擷取畫面](system-theme-changes-images/main-page-both-themes.png "主題應用程式的主頁面")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面") 
[ ![IOS 和 Android 上主題應用程式之詳細資料頁面的螢幕擷取畫面](system-theme-changes-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

## <a name="define-and-consume-theme-resources"></a>定義和使用主題資源

淺色和深色主題的資源可以搭配 `AppThemeBinding` 標記延伸和 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 擴充方法使用。 使用這些方法時，系統會根據目前系統主題的值自動套用資源。 此外，如果系統主題在執行應用程式時變更，則會自動更新使用這些資源的物件。

### <a name="appthemebinding-markup-extension"></a>AppThemeBinding 標記延伸

`AppThemeBinding`標記延伸可讓您根據目前的系統主題使用資源，例如影像或色彩：

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label) 當裝置使用淺色主題時，第一個的文字色彩會設為綠色，而當裝置使用其深色主題時，會設定為紅色。 同樣地，會 [`Image`](xref:Xamarin.Forms.Image) 根據目前的系統主題顯示不同的影像檔案。

此外，您 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以使用標記延伸來使用中定義的資源 `StaticResource` ：

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

如需標記延伸的詳細資訊 `AppThemeBinding` ，請參閱 [AppThemeBinding 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)。

### <a name="extension-methods"></a>擴充方法

Xamarin.Forms 包含 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 延伸方法，可讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件回應系統主題變更。

`SetAppThemeColor`方法會 [`Color`](xref:Xamarin.Forms.Color) 根據目前的系統主題，來指定要在目標屬性上設定的物件：

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label) 當裝置使用其淺色主題時，的文字色彩會設為綠色，而當裝置使用其深色主題時，會設為紅色。

`SetOnAppTheme<T>`方法會 `T` 根據目前的系統主題，來指定要在目標屬性上設定之類型的物件：

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) `lightlogo.png` 當裝置使用其淺色主題，以及 `darklogo.png` 裝置使用其深色主題時，會顯示。

## <a name="detect-the-current-system-theme"></a>偵測到目前的系統主題

您可以藉由取得屬性的值來偵測目前的系統主題 `Application.RequestedTheme` ：

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

屬性會傳回 `RequestedTheme` `OSAppTheme` 列舉成員。 `OSAppTheme` 列舉會定義下列成員：

- `Unspecified`，指出裝置使用未指定的主題。
- `Light`，指出裝置使用其淺色主題。
- `Dark`，表示裝置使用其深色主題。

## <a name="set-the-current-user-theme"></a>設定目前的使用者主題

應用程式所使用的主題可以設定為 `Application.UserAppTheme` 類型 `OSAppTheme` ，而不論目前有何種系統主題可用：

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

在此範例中，應用程式會設定為使用針對系統深色模式所定義的主題，而不論目前有何種系統主題正在運作。

> [!NOTE]
> 將屬性設為，以 `UserAppTheme` `OSAppTheme.Unspecified` 預設為作業系統主題。

## <a name="react-to-theme-changes"></a>回應主題變更

裝置上的系統主題可能因各種原因而變更，視裝置的設定方式而定。 Xamarin.Forms 藉由處理事件，可在系統主題變更時通知應用程式 `Application.RequestedThemeChanged` ：

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

`AppThemeChangedEventArgs`事件隨附的物件 `RequestedThemeChanged` 具有名為的單一屬性 `RequestedTheme` ，類型為 `OSAppTheme` 。 您可以檢查這個屬性來偵測所要求的系統主題。

> [!IMPORTANT]
> 若要回應 Android 上的主題變更，您必須 `ConfigChanges.UiMode` 在類別的屬性中包含旗標 `Activity` `MainActivity` 。

## <a name="related-links"></a>相關連結

- [SystemThemes (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [AppThemeBinding 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.Forms使用 XAML 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)