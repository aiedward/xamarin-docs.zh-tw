---
title: iOS 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: be378a60a9d9a7b206b64f07ee70edb432cec8e3
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935650"
---
# <a name="ios-platform-specifics"></a>iOS 平台特性

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。_

在 iOS 上，Xamarin.Forms 會包含下列平台特性：

- 模糊的任何支援[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。 如需詳細資訊，請參閱 <<c0> [ 套用模糊](#blur)。
- 控制是否要將頁面標題顯示為頁面巡覽列中的大型標題。 如需詳細資訊，請參閱 <<c0> [ 顯示的大型標題](#large_title)。
- 確保內容該頁面位於而言是安全的所有 iOS 裝置的螢幕區域。 如需詳細資訊，請參閱 <<c0> [ 啟用安全區域版面配置輔助線](#safe_area_layout)。
- 半透明的導覽列中。 如需詳細資訊，請參閱 <<c0> [ 進行導覽列半透明](#translucent_navigation_bar)。
- 控制是否狀態列文字的色彩上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整成符合要求的導覽列的明暗度。 如需詳細資訊，請參閱 <<c0> [ 調整狀態列文字的色彩模式](#status_bar_color_mode)。
- 確保所輸入文字融入[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)藉由調整字型大小。 如需詳細資訊，請參閱 <<c0> [ 調整項目的字型大小](#adjust_font_size)。
- 控制中的項目選取項目發生時[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 如需詳細資訊，請參閱 <<c0> [ 控制選擇器項目選取](#picker_update_mode)。
- 設定狀態軸可見度[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 如需詳細資訊，請參閱 <<c0> [ 頁面上設定狀態列可見性](#set_status_bar_visibility)。
- 控制是否[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)處理觸控筆勢，或將它傳遞給它的內容。 如需詳細資訊，請參閱 < [ScrollView 中延遲內容修飾](#delay_content_touches)。
- 設定分隔符號樣式[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 < [ListView 上設定分隔符號樣式](#listview-separatorstyle)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 停用舊版色彩模式](#legacy-color-mode)。

<a name="blur" />

## <a name="applying-blur"></a>套用模糊

此平台專屬用以模糊分層在其下的內容，而由在 XAML 中設定[ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/)附加屬性的值[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來與套用模糊效果[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)提供四個列舉值： [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None)， [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight)， [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light)，以及[ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark)。

結果是，指定[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)會套用至[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)執行個體，哪些模糊[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)分層其下方：

![](ios-images/blur-effect.png "模糊效果平台專屬")

<a name="large_title" />

## <a name="displaying-large-titles"></a>顯示大型標題

此平台專屬用來顯示為導覽列中，使用 iOS 11 或更新版本的裝置上的大型標題的頁面標題。 大型標題靠左對齊，而使用較大的字型，且會轉換成標準的標題使用者一開始捲動的內容，以便有效率地使用螢幕面積。 但是，在橫向模式下，標題將返回到導航欄的中心以優化內容佈局。 它由在 XAML 中設定`NavigationPage.PrefersLargeTitles`附加屬性`boolean`值：

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

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，可讓您控制是否要啟用大型標題。

前提是大型的項目上啟用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，在巡覽堆疊中的所有頁面會都顯示大型的標題。 此行為可覆寫頁面上設定`Page.LargeTitleDisplay`附加屬性的值`LargeTitleDisplayMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

從 C# 使用 fluent API 或者，覆寫頁面行為：

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

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間上控制項的大型標題行為[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，與`LargeTitleDisplayMode`提供三個可能的列舉型別值：

- `Always` – 強制字型與導覽列中使用大型的格式大小。
- `Automatic` – 為上一個項目，在巡覽堆疊中使用相同的樣式 （大型或小型）。
- `Never` -強制執行規則、 小型格式導覽列的使用。

颾魤 ㄛ`SetLargeTitleDisplay`方法可用來切換列舉值，藉由呼叫`LargeTitleDisplay`方法，以傳回目前`LargeTitleDisplayMode`:

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

![](ios-images/large-title.png "模糊效果平台專屬")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>啟用安全區域版面配置輔助線

此平台專屬用來確保頁面內容位於而言是安全的所有使用 iOS 11 和更新版本裝置的螢幕區域。 具體來說，它會幫助，以確定該內容不由裝置圓角邊角、 家用的指標或在 iPhone X 上的感應器外罩裁剪。它由在 XAML 中設定`Page.UseSafeArea`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetUseSafeArea`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，可讓您控制是否要啟用安全區域版面配置輔助線。

結果是螢幕的網頁內容可以位於安全適用於所有 Iphone 區域：

[![](ios-images/safe-area-layout.png "安全區域版面配置輔助線")](ios-images/safe-area-layout-large.png#lightbox "安全區域版面配置輔助線")

> [!NOTE]
> Apple 所定義的安全區域設定時，會在 Xamarin.Forms [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/)屬性，而且會覆寫這個屬性的所有已設定的舊值。

安全區域可以藉由擷取自訂其[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)具有值`Page.SafeAreaInsets`方法，從[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間。 然後可以修改為所需和重新指派給`Padding`頁面的建構函式中的屬性或[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)覆寫：

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

## <a name="making-the-navigation-bar-translucent"></a>進行瀏覽列半透明

此平台專屬用來變更巡覽列的透明度，且由在 XAML 中設定[ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) ; 附加屬性`boolean`值：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用來進行瀏覽列半透明。 颾魤 ㄛ [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)還原成預設狀態的導覽列的方法和[ `SetIsNavigationBarTranslucent`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/)方法可用來切換瀏覽列透明度，藉由呼叫[ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

結果是，可以變更導覽列的透明度：

![](ios-images/translucent-navigation-bar.png "平台專屬的半透明的導覽列")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>調整文字的色彩模式狀態列

此平台特定的控制項上是否色彩的狀態列文字[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整成符合要求的導覽列的明暗度。 它由在 XAML 中設定[ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/)附加屬性的值[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

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

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間中，控制項上是否色彩的狀態列文字[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)調整以符合亮度的導覽列中，使用[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)提供兩個可能值的列舉型別：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – 表示不應該調整的狀態列文字色彩。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – 表示狀態列的文字色彩應符合的導覽列的明暗度。

颾魤 ㄛ [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/)方法可用來擷取目前的值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)套用至列舉型別[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

結果是在狀態列上的文字色彩[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)可以調整以符合導覽列的明暗度。 在此範例中，狀態列的文字色彩變更為使用者之間的切換[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "狀態列文字的色彩模式平台專屬")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>調整項目的字型大小

此平台專屬用來調整字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)以確保在控制項中，以符合不經的文字。 它由在 XAML 中設定[ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) ; 附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用來調整以確保它符合不經文字的字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). 颾魤 ㄛ [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法，以停用此平台特定，並[ `SetAdjustsFontSizeToFitWidth`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/)方法可用來切換藉由呼叫調整字型大小[ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

結果是字型大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)會縮放以確保在控制項中，以符合不經的文字：

![](ios-images/entry-font-size.png "調整項目大小的平台特定字型")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>控制選擇器的項目選取

此平台特定的控制項中的項目選取項目發生時[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)，讓使用者指定的項目選取瀏覽控制項，項目時，或只要**完成**已按下。 它由在 XAML 中設定`Picker.UpdateMode`附加屬性的值`UpdateMode`列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Picker.SetUpdateMode`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，用來控制何時發生的項目選取，請使用`UpdateMode`提供兩個可能值的列舉型別：

- `Immediately` – 使用者瀏覽中的項目，就會發生的項目選取[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 這是在 Xamarin.Forms 中的預設行為。
- `WhenFinished` – 使用者已按下之後，才會發生的項目選取**完成**按鈕[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。

颾魤 ㄛ`SetUpdateMode`方法可用來切換列舉值，藉由呼叫`UpdateMode`方法，以傳回目前`UpdateMode`:

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

## <a name="setting-the-status-bar-visibility-on-a-page"></a>設定 [狀態] 列在頁面上的可見性

此平台專屬用來設定 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，其中包含能夠控制狀態列如何進入或離開`Page`。 它由在 XAML 中設定`Page.PrefersStatusBarHidden`附加屬性的值`StatusBarHiddenMode`列舉型別，並選擇性地`Page.PreferredStatusBarUpdateAnimation`附加屬性的值`UIStatusBarAnimation`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`方法，請在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間，用來設定 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)藉由指定的其中一個`StatusBarHiddenMode`列舉值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`並`StatusBarHiddenMode.False`值設定狀態 列可見性，不論裝置方向和`StatusBarHiddenMode.Default`值會隱藏垂直 compact 的環境中的 狀態 列。

結果是在 [狀態] 列的可見性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)可以設定：

![](ios-images/hide-status-bar.png "狀態列可見性平台專屬")

> [!NOTE]
> 在  [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，指定`StatusBarHiddenMode`列舉值也會更新所有的子頁面上的 [狀態] 列。 所有其他[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-衍生型別，指定的`StatusBarHiddenMode`列舉值只會更新目前頁面上的 [狀態] 列。

`Page.SetPreferredStatusBarUpdateAnimation`方法用來設定 [狀態] 列如何進入或離開[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)藉由指定的其中一個`UIStatusBarAnimation`列舉值： `None`， `Fade`，或`Slide`。 如果`Fade`或是`Slide`指定列舉值，0.25 的第二個動畫執行狀態列進入或離開`Page`。

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>ScrollView 中的延遲內容修飾

隱含的計時器會在觸控筆勢開始時觸發[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)在 iOS 上和`ScrollView`決定，是否應該處理筆勢，或將它傳遞給其內容為基礎的計時器範圍內中的使用者動作。 根據預設，iOS`ScrollView`延遲內容的風格，但這可能會導致問題，在某些情況下使用`ScrollView`時應該不 winning 筆勢的內容。 因此，此平台特定的控制項是否`ScrollView`處理觸控筆勢，或將它傳遞給它的內容。 它由在 XAML 中設定`ScrollView.ShouldDelayContentTouches`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `ScrollView.SetShouldDelayContentTouches`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空間，是用來控制[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)處理觸控筆勢，或將它傳遞給它的內容。 颾魤 ㄛ`SetShouldDelayContentTouches`方法可用來切換藉由呼叫延遲內容修飾`ShouldDelayContentTouches`傳回是否延遲內容修飾的方法：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

結果是[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)可以停用延遲接收內容的風格，因此，在此案例[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)接收筆勢而不是[ `Detail`](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面的[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "ScrollView 延遲內容觸及特定平台")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

<a name="listview-separatorstyle" />

## <a name="setting-the-separator-style-on-a-listview"></a>設定分隔符號樣式的 ListView

此平台特定的控制項是否在儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整的寬度`ListView`。 它由在 XAML 中設定[ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty)附加屬性的值[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否中儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整寬度`ListView`，以[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)提供兩個可能值的列舉型別：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – 表示預設 iOS 分隔符號行為。 這是在 Xamarin.Forms 中的預設行為。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – 表示分隔符號會取自的某一邊`ListView`之間。

結果是，指定[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)值套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，這會控制的儲存格之間的分隔符號寬度：

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle 平台專屬")

> [!NOTE]
> 分隔符號樣式已設定為一旦`FullWidth`，就無法變更回`Default`在執行階段。

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用舊版色彩模式

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此平台專屬停用此舊版的色彩模式，以便即使檢視已停用，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](ios-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="summary"></a>總結

這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。


## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
