---
title: Xamarin.Forms Shell 頁面設定
description: Shell 類別會定義可用來設定 Xamarin.Forms Shell 應用程式中頁面外觀的附加屬性。 這包括設定頁面色彩、停用導覽列、停用索引標籤列，以及在導覽列中顯示檢視。
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
ms.openlocfilehash: 411c87c25701521bf27fbb863b02a90f8e523574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305049"
---
# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.Forms Shell 頁面設定

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

`Shell` 類別會定義可用來設定 Xamarin.Forms Shell 應用程式中頁面外觀的附加屬性。 這包括設置頁面顏色、設置頁面表示模式、禁用導航欄、禁用選項卡欄以及顯示導航欄中的視圖。

## <a name="set-page-colors"></a>設定頁面色彩

`Shell` 類別定義下列附加屬性，可用來在 Shell 應用程式中設定頁面色彩：

- 型別為 `Color` 的 `BackgroundColor` 定義 Shell 色彩中的背景色彩。 色彩將不會填入 Shell 內容的背景。
- 型別為 `Color` 的 `DisabledColor` 定義用來為遭停用文字和圖示上色之色彩。
- 型別為 `Color` 的 `ForegroundColor` 定義用來為文字和圖示上色之色彩。
- 型別為 `Color` 的 `TitleColor` 定義用於目前頁面標題之色彩。
- 型別為 `Color` 的 `UnselectedColor` 定義用於 Shell 色彩中未選取文字和圖示的色彩。

所有這些屬性都由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件支援,這意味著這些屬性可以是數據綁定的目標,並使用 XAML 樣式進行樣式設置。 此外，這些屬性可以使用階層式樣式表 (CSS) 來設定。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

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

## <a name="set-page-presentation-mode"></a>設定頁面展示模式

預設情況下,當使用 方法導航到頁面時`GoToAsync`, 將發生小型導航動畫。 但是,可以通過`Shell.PresentationMode`將 附加屬性[`ContentPage`](xref:Xamarin.Forms.ContentPage)設置`PresentationMode`為 枚 舉成員之一來更改此行為:

- `NotAnimated`指示頁面將顯示沒有導航動畫。
- `Animated`指示頁面將顯示導航動畫。 這是附加屬性的`Shell.PresentationMode`預設值。
- `Modal`表示頁面將顯示為模態頁。
- `ModalAnimated`指示頁面將顯示為模式頁面,帶有導航動畫。
- `ModalNotAnimated`指示頁面將顯示為模態頁面,沒有導航動畫。

> [!IMPORTANT]
> 該`PresentationMode`類型是標記枚舉。 這意味著枚舉成員的組合可以在代碼中應用。 但是,為了便於在 XAML 中`ModalAnimated`使用,`Animated``Modal`成員是`ModalNotAnimated`和 成員的`NotAnimated`組合`Modal`,並且成員是 和成員的組合。 有關標記枚舉的詳細資訊,請參閱[枚舉類型作為位標誌](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags)。

以下 XAML 範`Shell.PresentationMode`例在[`ContentPage`](xref:Xamarin.Forms.ContentPage)上設定附加 的屬性:

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

在此範例中,當使用[`ContentPage`](xref:Xamarin.Forms.ContentPage)`GoToAsync`方法導航到頁面時,將設置為顯示為模態頁。

## <a name="enable-navigation-bar-shadow"></a>啟用導覽列陰影

類`Shell``NavBarHasShadow`定義 附加的`bool`屬性類型 ,用於控制導航欄是否具有陰影。 默認情況下,該屬性的值在`false`iOS`true`和 Android 上。

雖然此屬性可以在子類`Shell`物件上設置,但也可以在要啟用導航欄陰影的任何頁面上設置此屬性。 例如,以下 XAML 顯示啟用導覽列[`ContentPage`](xref:Xamarin.Forms.ContentPage)陰影的 :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

這將導致啟用導航欄陰影。

## <a name="disable-the-navigation-bar"></a>停用瀏覽列

`Shell` 類別定義 `NavBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示導覽列。 根據預設，該屬性的值為 `true`。

雖然此屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏導覽列的任何頁面上。 例如,以下 XAML 顯示了關閉的導[`ContentPage`](xref:Xamarin.Forms.ContentPage)覽列 :

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，導覽列變成是隱藏的：

![在 iOS 和 Android 上使用不可見導航列的殼牌頁面的螢幕截圖](configuration-images/navigationbar-invisible.png "具有不可見導覽列的外殼頁面")

## <a name="disable-the-tab-bar"></a>停用索引標籤列

`Shell` 類別定義 `TabBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示索引標籤列。 根據預設，該屬性的值為 `true`。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏索引標籤列的任何頁面上。 例如,以下 XAML 顯示關閉的選項卡列[`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，索引標籤列變成是隱藏的：

![在 iOS 和 Android 上使用不可見的選項卡欄的殼牌頁面的螢幕截圖](configuration-images/tabbar-invisible.png "具有不可見選項卡列的外殼頁面")

## <a name="display-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

該`Shell`類`TitleView`定義 附加的屬性`View`類型 , 使任何 Xamarin.Forms[`View`](xref:Xamarin.Forms.View)都可以顯示在導航列中。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但也可以將它設定在想要在導覽列中顯示檢視的任何頁面上。 例如,以下 XAML 顯示在[`Image`](xref:Xamarin.Forms.Image)的導航 列[`ContentPage`](xref:Xamarin.Forms.ContentPage)中顯示 。

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

![在 iOS 和 Android 上,帶有標題檢視的殼牌頁面的螢幕截圖](configuration-images/titleview.png "標題檢視的外殼頁面")

> [!IMPORTANT]
> 如果已經將導覽列設定為隱藏 (使用 `NavBarIsVisible` 附加屬性)，就不會顯示標題檢視。

許多檢視不會顯示在導航列中,除非[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)使用和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性指定檢視的大小,或者使用[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)屬性指定檢視的位置。

由於[`Layout`](xref:Xamarin.Forms.Layout)類派生自[`View`](xref:Xamarin.Forms.View)類 ,因此`TitleView`可以將 附加屬性設置為顯示包含多個視圖的佈局類。 同樣,[`ContentView`](xref:Xamarin.Forms.ContentView)由於類別最終派生自[`View`](xref:Xamarin.Forms.View)類別 ,因此`TitleView`可以將額外屬性設定為顯示包含單一檢視的`ContentView`。

## <a name="page-visibility"></a>頁面可見度

命令程式尊重頁面可見性,使用[`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)屬性設置。 因此,當頁面的屬性`IsVisible`設置為`false`它時,它將在 Shell 應用程式中不可見,並且無法導航到它。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
