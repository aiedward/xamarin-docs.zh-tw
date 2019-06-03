---
title: Xamarin.Forms Shell 頁面設定
description: Shell 類別會定義可用來設定 Xamarin.Forms Shell 應用程式中頁面外觀的附加屬性。 這包括設定頁面色彩、停用導覽列、停用索引標籤列，以及在導覽列中顯示檢視。
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: 186e9af867e89c0130822593bb0d7ab071ee0505
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252412"
---
# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.Forms Shell 頁面設定

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

`Shell` 類別會定義可用來設定 Xamarin.Forms Shell 應用程式中頁面外觀的附加屬性。 這包括設定頁面色彩、停用導覽列、停用索引標籤列，以及在導覽列中顯示檢視。

## <a name="set-page-colors"></a>設定頁面色彩

`Shell` 類別定義下列附加屬性，可用來在 Shell 應用程式中設定頁面色彩：

- 型別為 `Color` 的 `BackgroundColor` 定義 Shell 色彩中的背景色彩。 色彩將不會填入 Shell 內容的背景。
- 型別為 `Color` 的 `DisabledColor` 定義用來為遭停用文字和圖示上色之色彩。
- 型別為 `Color` 的 `ForegroundColor` 定義用來為文字和圖示上色之色彩。
- 型別為 `Color` 的 `TitleColor` 定義用於目前頁面標題之色彩。
- 型別為 `Color` 的 `UnselectedColor` 定義用於 Shell 色彩中未選取文字和圖示的色彩。

所有這些屬性都受 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件支援，也就是說，這些屬性可以是資料繫結的目標，且可以使用 XAML 樣式來設定樣式。 此外，這些屬性可以使用階層式樣式表 (CSS) 來設定。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

> [!NOTE]
> 也有可定義索引標籤色彩的屬性。 如需詳細資訊，請參閱[索引標籤外觀](tabs.md#tab-appearance)。

下列 XAML 顯示在子類別化 `Shell` 類別中設定色彩屬性：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

在此範例中，除非在頁面層級覆寫色彩值，否則色彩值會套用至 Shell 應用程式中的所有頁面。

因為色彩屬性是附加屬性，所以可以在個別頁面上設定它們，若要在該頁面上設定色彩：

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

或者，可以使用 XAML 樣式來設定色彩屬性：

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

如需 XAML 樣式的詳細資訊，請參閱[使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

## <a name="disable-the-navigation-bar"></a>停用瀏覽列

`Shell` 類別定義 `NavBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示導覽列。 根據預設，該屬性的值為 `true`。

雖然此屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏導覽列的任何頁面上。 例如，下列 XAML 顯示如何為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 停用導覽列：

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，導覽列變成是隱藏的：

![iOS 和 Android 上包含隱藏導覽列之 Shell 頁面的螢幕擷取畫面](configuration-images/navigationbar-invisible.png "包含隱藏導覽列的 Shell 頁面")

## <a name="disable-the-tab-bar"></a>停用索引標籤列

`Shell` 類別定義 `TabBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示索引標籤列。 根據預設，該屬性的值為 `true`。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏索引標籤列的任何頁面上。 例如，下列 XAML 顯示如何為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 停用索引標籤列：

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，索引標籤列變成是隱藏的：

![iOS 和 Android 上包含隱藏索引標籤列之 Shell 頁面的螢幕擷取畫面](configuration-images/tabbar-invisible.png "包含隱藏索引標籤列的 Shell 頁面")

## <a name="display-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

`Shell` 類別定義 `TitleView` 附加屬性 (`View` 型別)，它可讓任何 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 在導覽列中顯示。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但也可以將它設定在想要在導覽列中顯示檢視的任何頁面上。 例如，下列 XAML 顯示如何在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的導覽列中顯示 [`Image`](xref:Xamarin.Forms.Image)：

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

這會導致影像顯示在頁面上的導覽列中：

![iOS 和 Android 上包含標題檢視之 Shell 頁面的螢幕擷取畫面](configuration-images/titleview.png "包含標題檢視的 Shell 頁面")

> [!IMPORTANT]
> 如果已經將導覽列設定為隱藏 (使用 `NavBarIsVisible` 附加屬性)，就不會顯示標題檢視。

除非使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性指定檢視的大小，或使用 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性指定檢視的位置，否則許多檢視不會出現在導覽列中。

由於 [`Layout`](xref:Xamarin.Forms.Layout) 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別，所以可設定 `TitleView` 附加屬性，顯示包含多個檢視的配置類別。 同樣地，由於 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別最終衍生自 [`View`](xref:Xamarin.Forms.View) 類別，所以可設定 `TitleView` 附加屬性，顯示包含單一檢視的 `ContentView`。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
