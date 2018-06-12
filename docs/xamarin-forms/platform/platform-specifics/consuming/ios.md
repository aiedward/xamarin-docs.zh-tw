---
title: iOS 平台特性
description: 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。 本文將示範如何使用 iOS 平台-細節建 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: dbcf97248c1d4537319691f6e18e0d41c931f423
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848261"
---
# <a name="ios-platform-specifics"></a>iOS 平台特性

_平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。本文將示範如何使用 iOS 平台-細節建 Xamarin.Forms。_

在 iOS、 Xamarin.Forms 會包含下列平台特性：

- 模糊的任何支援[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。 如需詳細資訊，請參閱[套用模糊](#blur)。
- 控制是否要將頁面標題顯示為頁面導覽列中的大型標題。 如需詳細資訊，請參閱[顯示大型標題](#large_title)。
- 確保內容該頁面位於而言是安全的所有 iOS 裝置的螢幕區域。 如需詳細資訊，請參閱[啟用安全的區域配置指南](#safe_area_layout)。
- 半透明的導覽列。 如需詳細資訊，請參閱[進行瀏覽列半透明](#translucent_navigation_bar)。
- 控制是否狀態列文字色彩上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整為符合導覽列的明暗度。 如需詳細資訊，請參閱[調整狀態列文字的色彩模式](#status_bar_color_mode)。
- 確保所輸入的文字放入[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)藉由調整字型大小。 如需詳細資訊，請參閱[調整項目的字型大小](#adjust_font_size)。
- 控制中的項目選取發生時就[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 如需詳細資訊，請參閱[控制選擇器項目選取](#picker_update_mode)。
- 在設定狀態軸的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 如需詳細資訊，請參閱[頁面上設定狀態軸的可見性](#set_status_bar_visibility)。
- 控制是否[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)處理觸控手勢，或將其傳遞給它的內容。 如需詳細資訊，請參閱[中 ScrollView 延遲內容修飾](#delay_content_touches)。
- 在設定的分隔符號樣式[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱[ListView 上設定分隔符號樣式](#listview-separatorstyle)。
- 停用上支援的舊版色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[停用傳統色彩模式](#legacy-color-mode)。

<a name="blur" />

## <a name="applying-blur"></a>套用模糊

平台專屬用於模糊分層其下的內容，並會在 XAML 中使用設定[ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/)附加屬性的值[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用於套用模糊效果，與[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)提供四個列舉值： [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None/)， [ `ExtraLight` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight/)， [ `Light` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light/)，和[ `Dark` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark/)。

結果是指定[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)套用至[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)執行個體，哪些模糊[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)分層式下方：

![](ios-images/blur-effect.png "模糊效果特定平台")

<a name="large_title" />

## <a name="displaying-large-titles"></a>顯示大型標題

平台專屬用來做為巡覽列，對於使用大於或等於 11 的 iOS 裝置上的大型標題顯示網頁的標題。 大型標題靠左對齊並使用較大的字型，並轉換至標準標題使用者一開始捲動內容，以便有效率地使用實際螢幕面積。 但是，在橫向模式下，標題將返回到導航欄的中心以優化內容佈局。 它由在 XAML 中設定`NavigationPage.PrefersLargeTitles`附加屬性`boolean`值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，可讓您控制是否要啟用大型標題。

前提是大型標題上的已啟用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，在巡覽堆疊中的所有頁面會都顯示大型標題。 此行為可覆寫頁面上設定`Page.LargeTitleDisplay`附加屬性的值`LargeTitleDisplayMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，頁面行為會覆寫從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間中，控制項的大型標題行為[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，與`LargeTitleDisplayMode`提供三個可能的列舉型別值：

- `Always` – 強制的巡覽列及字型以使用大型的格式大小。
- `Automatic` – 為上一個項目，在巡覽堆疊中使用相同的樣式 （大型或小型）。
- `Never` -強制使用一般的小型格式導覽列。

此外，`SetLargeTitleDisplay`方法可以用來切換列舉值，藉由呼叫`LargeTitleDisplay`方法，這個方法會傳回目前`LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

結果是，指定`LargeTitleDisplayMode`套用至[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，大型標題行為：

![](ios-images/large-title.png "模糊效果特定平台")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>啟用安全區域配置指南

平台專屬用來確保頁面內容位於而言是安全的所有使用 11 和更新版本的 iOS 裝置的螢幕區域。 具體來說，它有助於確定該內容不裁剪的裝置圓邊角、 住家的指標或感應器外罩 X 在 iPhone 上的。它由在 XAML 中設定`Page.UseSafeArea`附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `Page.SetUseSafeArea`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，可讓您控制是否啟用 「 安全區域的版面配置輔助。

結果為網頁內容可以位於螢幕可為 60x60 安全的區域：

[![](ios-images/safe-area-layout.png "安全區域的版面配置輔助")](ios-images/safe-area-layout-large.png#lightbox "安全區域的版面配置輔助")

> [!NOTE]
> Apple 所定義的安全區域 Xamarin.Forms 中用來設定[ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/)屬性，而且將覆寫先前的任何值的這個屬性尚未設定。

安全區域可以自訂藉由擷取其[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)值與`Page.SafeAreaInsets`方法從[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間。 它可以加以修改以所需，且重新指派給`Padding`頁面的建構函式中的屬性或[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)覆寫：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>進行導覽列半透明

平台專屬用來變更巡覽列的透明度且由在 XAML 中設定[ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/)附加屬性`boolean`值：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用來進行導覽列半透明。 此外， [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)還原為其預設狀態中，導覽列的方法和[ `SetIsNavigationBarTranslucent`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/)方法可以用來瀏覽列透明度切換藉由呼叫[ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

結果是，可以變更導覽列的透明度：

![](ios-images/translucent-navigation-bar.png "平台專屬的半透明的導覽列")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>調整的狀態列文字色彩模式

此平台特定的控制項是否狀態列文字色彩上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整為符合導覽列的明暗度。 它由在 XAML 中設定[ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/)附加屬性的值[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)列舉型別：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間中，控制項是否狀態列文字色彩上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整為比對導覽列的亮度與[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)提供兩個可能值的列舉：

- [`DoNotAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust/) – 表示不應調整狀態列上文字色彩。
- [`MatchNavigationBarTextLuminosity`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity/) – 表示文字色彩狀態列應符合的導覽列的明暗度。

此外， [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/)方法可以用來擷取目前的值[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)列舉套用至[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

結果是在狀態列上的文字色彩[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)可加以調整來比對的導覽列的明暗度。 在此範例中，狀態列文字色彩變更為使用者切換[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "狀態列文字色彩模式特定平台")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>調整項目的字型大小

平台專屬用來調整字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)以確保在控制項中，以符合不經的文字。 它由在 XAML 中設定[ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/)附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用來調整不經的文字，以確保它符合的字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). 此外， [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)此平台特定，會停用的方法和[ `SetAdjustsFontSizeToFitWidth`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/)方法可以用來切換藉由呼叫調整字型大小[ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

結果是字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)調整以確保在控制項中，以符合不經的文字：

![](ios-images/entry-font-size.png "調整項目大小的平台特定字型")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>控制選取器的項目選取

平台專屬控制項中的項目選取發生時就[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)，讓使用者指定的項目選取會在瀏覽項目在控制項中，或一次發生**完成**按下按鈕。 它由在 XAML 中設定`Picker.UpdateMode`附加屬性的值`UpdateMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `Picker.SetUpdateMode`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用於控制的項目選取發生的時間，與`UpdateMode`提供兩個可能值的列舉：

- `Immediately` -當使用者瀏覽中的項目，就會發生的項目選取[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 這是預設行為，在 Xamarin.Forms。
- `WhenFinished` – 使用者已按下之後，才會發生的項目選取**完成**按鈕[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。

此外，`SetUpdateMode`方法可以用來切換列舉值，藉由呼叫`UpdateMode`方法，這個方法會傳回目前`UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

結果是，指定`UpdateMode`套用至[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)，這會控制當項目選取項目，就會發生：

[![](ios-images/picker-updatemode.png "選擇器 UpdateMode 平台專屬")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>設定 [狀態列] 頁面上的可見性

平台專屬用來設定 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，其中包含能夠控制狀態列如何進入或離開`Page`。 它由在 XAML 中設定`Page.PrefersStatusBarHidden`附加屬性的值`StatusBarHiddenMode`列舉型別，並選擇性地`Page.PreferredStatusBarUpdateAnimation`附加屬性的值`UIStatusBarAnimation`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`方法，請在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間，用來設定 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)藉由指定的其中一個`StatusBarHiddenMode`列舉值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`和`StatusBarHiddenMode.False`設定狀態 列可見性，不論裝置方向的值和`StatusBarHiddenMode.Default`值會隱藏垂直 compact 的環境中的 狀態 列。

結果是在 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)可以設定：

![](ios-images/hide-status-bar.png "狀態列可視性平台專屬")

> [!NOTE]
> 在[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，指定`StatusBarHiddenMode`列舉值也會更新 [狀態] 列上所有的子頁面。 在所有其他[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-衍生型別，指定的`StatusBarHiddenMode`列舉值只會更新目前的頁面上的 [狀態] 列。

`Page.SetPreferredStatusBarUpdateAnimation`方法用來設定 [狀態] 列如何進入或離開[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)藉由指定的其中一個`UIStatusBarAnimation`列舉值： `None`， `Fade`，或`Slide`。 如果`Fade`或`Slide`指定列舉值，0.25 的第二個動畫執行狀態列進入或離開`Page`。

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>延遲的內容修飾 ScrollView 中

隱含的計時器觸發觸控筆勢一開始會處於[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)在 iOS 上和`ScrollView`決定時，是否應該處理手勢，或將它傳遞至其內容為基礎的計時器範圍內中的使用者動作。 根據預設，iOS`ScrollView`延遲內容風格，但這可能會導致問題，在某些情況下使用`ScrollView`不 winning 筆勢應有的內容。 因此，此平台特定的控制項是否`ScrollView`處理觸控手勢，或將其傳遞給它的內容。 它由在 XAML 中設定`ScrollView.ShouldDelayContentTouches`附加屬性`boolean`值：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 `ScrollView.SetShouldDelayContentTouches`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)是否可用來控制命名空間， [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)處理觸控手勢，或將其傳遞給它的內容。 此外，`SetShouldDelayContentTouches`方法可以用來切換內容修飾延遲藉由呼叫`ShouldDelayContentTouches`方法來傳回內容的工作是否會延遲：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

結果是[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)可以停用延遲接收內容的風格，因此，在此案例中[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)接收筆勢而不是[ `Detail`](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "ScrollView 延遲內容碰觸特定平台")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

<a name="listview-separatorstyle" />

## <a name="setting-the-separator-style-on-a-listview"></a>ListView 上設定的分隔符號的樣式

平台專屬控制是否在儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用的整個寬度`ListView`。 它由在 XAML 中設定[ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty)附加屬性的值[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否中的儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整寬度`ListView`，與[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)提供兩個可能值的列舉：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – 表示預設 iOS 分隔符號行為。 這是預設行為，在 Xamarin.Forms。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – 表示分隔符號會取自的某一邊`ListView`之間。

結果是，指定[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)值套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，這會控制的儲存格之間的分隔符號寬度：

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle 平台專屬")

> [!NOTE]
> 一旦分隔符號樣式設`FullWidth`，就無法變更回`Default`在執行階段。

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用傳統色彩模式

一些 Xamarin.Forms 檢視功能舊版色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 回溯相容性，這種傳統的色彩模式仍受支援的檢視表的預設行為。

平台專屬停用這個傳統的色彩模式，，以便即使已停用檢視，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加屬性`false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>`方法會指定平台專屬只會在 iOS 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法可以用來傳回是否已停用舊版的色彩模式。

結果是，好讓使用者在檢視上所設定的色彩即使仍停用檢視時，就可以停用舊版的色彩模式:

![](ios-images/legacy-color-mode-disabled.png "停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="summary"></a>總結

本文示範如何使用 iOS 平台-細節建 Xamarin.Forms。 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。


## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
