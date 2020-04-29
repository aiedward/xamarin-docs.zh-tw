---
title: 回應 Xamarin 中的系統主題變更。 Forms 應用程式
description: Xamarin： Forms 應用程式可以使用 OnAppTheme 類型和 DynamicResource 標記延伸來回應作業系統主題變更。
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: c524ac0809044e576a8d56561642f6c3bf2df4a4
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532839"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>回應 Xamarin 中的系統主題變更。 Forms 應用程式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

裝置通常包含淺色和深色的主題，分別代表一組可在作業系統層級上設定的廣泛外觀喜好設定。 應用程式應該遵守這些系統主題，並在系統主題變更時立即回應。

視裝置設定而定，系統主題可能會因為各種不同的原因而變更。 這包括使用者要明確變更的系統主題，它會因為一天內的時間而變更，而且會因為環境因素（例如低光源）而變更。

Xamarin 應用程式可以藉由定義具有`AppThemeColor`類別、 `OnAppTheme<T>`類別和`OnAppTheme`標記延伸的資源，來回應系統主題變更。 然後，這些資源應該會與`DynamicResource`標記延伸搭配使用。

> [!IMPORTANT]
> 回應系統主題變更目前為實驗性，而且只能透過設定`AppTheme_Experimental`旗標來使用。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

若要回應系統主題變更，必須符合下列需求：

- Xamarin. 表單4.6 或更高版本。
- iOS 13 或更新版本。
- Android 10 （API 29）或更新版本。
- UWP 組建14393或更新版本。

下列螢幕擷取畫面顯示 iOS 和 Android 上的淺色和深色系統主題的主題頁面：

[![Screenshot of the main page of a themed app, on iOS and Android](system-theme-changes-images/main-page-both-themes.png "主題應用程式的主頁面") ](system-theme-changes-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面") 
Ios 和 android 上主題應用程式之 [詳細資料] 頁面的 ios 和 android[![螢幕擷取畫面](system-theme-changes-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

## <a name="define-and-consume-theme-resources"></a>定義和使用主題資源

淺色與深色主題的資源可以使用`AppThemeColor`類別、 `OnAppTheme<T>`類別和`OnAppTheme`標記延伸來定義。 使用每種方法時，會根據目前系統主題的值自動套用這些資源。 此外，如果系統主題在應用程式執行時變更，則會自動更新取用這些資源的物件。

### <a name="appthemecolor"></a>AppThemeColor

`AppThemeColor`類別是用來定義[`Color`](xref:Xamarin.Forms.Color)淺色和深色系統主題的資源。 `AppThemeColor`應該在中定義資源[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)：

```xaml
<Application ...>
    <Application.Resources>
        <AppThemeColor x:Key="PageBackgroundColor"
                       Light="White"
                       Dark="Black" />
        <AppThemeColor x:Key="NavigationBarColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="PrimaryColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="SecondaryColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="PrimaryTextColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="SecondaryTextColor"
                       Light="White"
                       Dark="White" />
        <AppThemeColor x:Key="TertiaryTextColor"
                       Light="Gray"
                       Dark="WhiteSmoke" />
        <AppThemeColor x:Key="TransparentColor"
                       Light="Transparent"
                       Dark="Transparent" />
    </Application.Resources>
</Application>
```

每`AppThemeColor`個資源都必須`x:Key`有一個屬性，它會在中提供描述性[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)索引鍵。 `Light`和`Dark`屬性的值應該是[`Color`](xref:Xamarin.Forms.Color)物件。 此外， `Default`屬性可以設定為`Color` ，供取用物件預設使用。

`AppThemeColor`資源可以內嵌方式使用：

```xaml
<Label Text="This monkey reacts appropriately to ridiculous assertions and actions"
       TextColor="{DynamicResource PrimaryTextColor}" />
```

或者， `AppThemeColor`資源可以由隱含或明確[`Style`](xref:Xamarin.Forms.Style)物件取用：

```xaml
<Style TargetType="NavigationPage">
    <Setter Property="BarBackgroundColor"
            Value="{DynamicResource NavigationBarColor}" />
    <Setter Property="BarTextColor"
            Value="{DynamicResource SecondaryColor}" />
</Style>
```

> [!IMPORTANT]
> `AppThemeColor`資源應該使用`DynamicResource`標記延伸模組來使用。 這可確保當系統主題變更時，使用物件的外觀會更新。

### <a name="onappthemelttgt"></a>OnAppTheme&lt;T&gt;

`OnAppTheme<T>`類別是用來定義光源和深色系統主題之任何類型的資源。 `OnAppTheme<T>`資源應該在中定義[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，並將`T`引數指定為`x:TypeArguments`屬性的值：

```xaml
<Application ...>
    <Application.Resources>
        <OnAppTheme x:Key="ImageLogo"
                    x:TypeArguments="FileImageSource"
                    Light="lightlogo.png"
                    Dark="darklogo.png" />
    </Application.Resources>
</Application>
```

每`OnAppTheme<T>`個資源都必須`x:Key`有一個屬性，它會在中提供描述性[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)索引鍵。 和屬性的值應該是定義為`x:TypeArguments`屬性之類型的物件。 `Dark` `Light` 此外， `Default`屬性也可以設定為類型`T`的物件，以供取用物件預設使用。

`OnAppTheme<T>`資源可以內嵌方式使用：

```xaml
<Image Source="{DynamicResource ImageLogo}"
       Aspect="AspectFit"
       HeightRequest="200" /
```

或者， `OnAppTheme<T>`資源可以由隱含或明確[`Style`](xref:Xamarin.Forms.Style)物件取用：

```xaml
<Style x:Key="imageLogoStyle"
       TargetType="Image">
    <Setter Property="Source"
            Value="{DynamicResource ImageLogo}" />
    <Setter Property="Aspect"
            Value="AspectFit" />
</Style>
```

> [!IMPORTANT]
> `OnAppTheme<T>`資源應該使用`DynamicResource`標記延伸模組來使用。 這可確保當系統主題變更時，使用物件的外觀會更新。

### <a name="onapptheme-markup-extension"></a>OnAppTheme 標記延伸

`OnAppTheme`標記延伸可讓您根據目前的系統主題，指定要使用的資源，例如影像或色彩。 它提供與`OnAppTheme<T>`類別相同的功能，但具有更精確的標記法：

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Image Source="{OnAppTheme Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

在此範例中，當裝置使用其淺色[`Label`](xref:Xamarin.Forms.Label)主題時，第一個的文字色彩會設定為綠色，而當裝置使用其暗色調主題時，會設為紅色。 同樣地， [`Image`](xref:Xamarin.Forms.Image)會根據目前的系統主題來顯示不同的影像檔案。

如需`OnAppTheme`標記延伸的詳細資訊，請參閱[OnAppTheme 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)。

## <a name="detect-the-current-system-theme"></a>偵測目前的系統主題

藉由取得`Application.RequestedTheme`屬性的值，可以偵測到目前的系統主題：

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

`RequestedTheme`屬性會傳回`OSAppTheme`列舉成員。 `OSAppTheme` 列舉會定義下列成員：

- `Unspecified`，表示裝置正在使用未指定的主題。
- `Light`，表示裝置正在使用其淺色主題。
- `Dark`，表示裝置正在使用其深色主題。

## <a name="react-to-theme-changes"></a>回應主題變更

裝置上的系統主題可能會因為各種不同的原因而變更，視裝置的設定方式而定。 當系統主題藉由處理`Application.RequestedThemeChanged`事件而變更時，可以通知 Xamarin 應用程式：

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

事件隨附的`AppThemeChangedEventArgs`物件具有名為的單一屬性，其類型`OSAppTheme`為。 `RequestedTheme` `RequestedThemeChanged` 您可以檢查此屬性以偵測要求的系統主題。

## <a name="related-links"></a>相關連結

- [SystemThemes （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [OnAppTheme 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin 中的動態樣式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
