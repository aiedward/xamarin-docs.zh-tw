---
title: Android 平台特性
description: 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。 本文示範如何使用 Android 平台-細節建 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 05f1fc6158e9a20892ab4a4b49b33e4eac6bc5e5
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733057"
---
# <a name="android-platform-specifics"></a>Android 平台特性

_平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。本文示範如何使用 Android 平台-細節建 Xamarin.Forms。_

在 Android 上，Xamarin.Forms 會包含下列平台特性：

- 設定螢幕小鍵盤的作業模式。 如需詳細資訊，請參閱[設定彈性的鍵盤輸入模式](#soft_input_mode)。
- 啟用快速捲動[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)如需詳細資訊，請參閱[啟用 ListView 中的快速捲動](#fastscroll)。
- 啟用在頁面之間撥動[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 如需詳細資訊，請參閱[啟用撥動頁之間的中 TabbedPage](#enable_swipe_paging)。
- 控制疊置順序的視覺項目來判斷繪製順序。 如需詳細資訊，請參閱[控制提高權限的視覺化項目](#elevation)。
- 停用[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)頁面生命週期事件上暫停和繼續分別使用 AppCompat 的應用程式。 如需詳細資訊，請參閱[停用 Disappearing 和出現網頁生命週期事件](#disable_lifecycle_events)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以顯示混合的內容。 如需詳細資訊，請參閱[啟用混合內容在 WebView 中](#webview-mixed-content)。
- 輸入的法編輯器的設定選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱[設定項目輸入法編輯器選項](#entry-imeoptions)。
- 停用上支援的舊版色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[停用傳統色彩模式](#legacy-color-mode)。
- 使用預設填補和陰影的 Android 按鈕的值。 如需詳細資訊，請參閱[使用 Android 按鈕](#button-padding-shadow)。

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>設定螢幕小鍵盤輸入的模式

平台專屬用來設定作業模式的螢幕小鍵盤輸入區域中，且由在 XAML 中設定[ `Application.WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/)附加屬性的值[ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)列舉型別：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `Application.UseWindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來設定螢幕小鍵盤輸入的區作業模式，與[ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)提供兩個值的列舉： [ `Pan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/)和[ `Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/)。 `Pan`值使用[ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/)調整選項，不會調整視窗大小的輸入的控制項有焦點時。 相反地，視窗的內容會使目前的焦點不會遮住螢幕小鍵盤上下移動。 `Resize`值使用[ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/)調整選項，將視窗調整為輸入的控制項有焦點，以騰出供螢幕小鍵盤時。

結果是螢幕小鍵盤輸入的區域輸入的控制項有焦點時，就可以設定作業模式：

[![](android-images/pan-resize.png "作業模式平台專屬的螢幕小鍵盤")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>啟用 ListView 中快速捲動

平台專屬用來啟用快速捲動資料[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 它由在 XAML 中設定`ListView.IsFastScrollEnabled`附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法會指定平台專屬只會在 Android 上執行。 `ListView.SetIsFastScrollEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來啟用快速捲動資料[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 此外，`SetIsFastScrollEnabled`方法可以用來切換快速捲動藉由呼叫`IsFastScrollEnabled`方法來傳回是否已啟用快速捲動：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

結果是透過資料快速捲動[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)可以啟用，捲軸之捲動方塊大小的變更：

[![](android-images/fastscroll.png "ListView FastScroll 平台專屬")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>啟用撥動 TabbedPage 在頁面之間

平台專屬用來啟用與在頁面之間的水平手指筆勢撥動[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 它由在 XAML 中設定[ `TabbedPage.IsSwipePagingEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/)附加屬性`boolean`值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來啟用 [在頁面之間撥動[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 此外，`TabbedPage`類別`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空間也有[ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)方法可讓此平台特定，和[ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)停用的方法此平台專屬。 [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/)附加屬性，以及[ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/)方法，用來設定應保留在目前頁面的任一邊閒置狀態的頁面數目。

結果是透過頁面顯示該撥動分頁[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)已啟用：

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>控制視覺元素的權限提高

平台專屬是用來控制 API 21 為目標的提高權限或疊置順序，在應用程式上的視覺項目或更高。 提高的權限視覺項目決定其繪製順序，與視覺化元素有 occluding 視覺項目具有較低的 Z 值較大的 Z 值。 它由在 XAML 中設定`VisualElement.Elevation`附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

`Button.On<Android>`方法會指定平台專屬只會在 Android 上執行。 `VisualElement.SetElevation`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來提高的權限的視覺項目設定為可為 null `float`。 此外，`VisualElement.GetElevation`方法可以用來擷取的視覺元素的高度值。

結果是視覺元素的提高權限可以進行控制，以便有較大的 Z 值的視覺項目 occlude 視覺項目具有較低的 Z 值。 因此，在此範例中，第二個[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)呈現上述[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)因為它具有較高的權限提高值：

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>停用消失與顯示的網頁生命週期事件

平台專屬用來停用[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)網頁應用程式上的事件暫停及繼續分別使用 AppCompat 的應用程式。 此外，它包含控制項的功能是否如果它已暫停] 上顯示前提螢幕小鍵盤的作業模式設定為 [繼續後，顯示螢幕小鍵盤[ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/)。

> [!NOTE]
> 請注意，預設會啟用這些事件以保留現有的事件所依賴的應用程式的行為。 停用這些事件會讓比對前 AppCompat 事件循環 AppCompat 事件循環。

平台專屬以供在 XAML 中設定[ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/)， [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/)，和[ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/) 附加屬性`boolean`值：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

`Application.Current.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)命名空間，用來啟用或停用引發[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)網頁事件，當應用程式進入背景。 [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)方法用來啟用或停用引發[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)網頁事件，當應用程式會繼續從背景。 [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)方法可控制是否螢幕小鍵盤顯示 [繼續後，如果它已暫停] 上顯示提供螢幕小鍵盤的作業模式設定為[ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

結果是[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)網頁事件將不會引發在應用程式暫停和繼續分別但，如果螢幕小鍵盤時顯示應用程式已暫停，它也會顯示應用程式繼續時：

[![](android-images/keyboard-on-resume.png "生命週期事件平台專屬")](android-images/keyboard-on-resume-large.png#lightbox "生命週期事件平台專屬")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>啟用在 WebView 中的混合的內容

此平台特定的控制項是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以顯示混合的內容中的應用程式 API 21 或更新版本為目標。 混合的內容是透過 HTTPS 連線，一開始載入的但透過 HTTP 連線載入資源 （例如影像、 音訊、 視訊、 樣式表、 指令碼） 的內容。 它由在 XAML 中設定[ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加屬性的值[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否可以顯示混合的內容，與[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三個可能值的列舉型別：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將允許 HTTPS origin 從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將不允許 HTTPS origin 從 HTTP 來源載入內容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)會嘗試與最新的裝置網頁瀏覽器的方法相容。 可能會允許某些 HTTP 內容個 HTTPS 來源載入和其他類型的內容將會遭到封鎖。 內容是被封鎖或允許的類型可能會變更每個作業系統版本。

結果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值套用至[ `WebView` ](xref:Xamarin.Forms.WebView)，這會控制是否可以顯示混合的內容：

[![混合內容大小處理特定的平台的 WebView](android-images/webview-mixedcontent.png "混合內容大小處理特定的平台的 WebView")](android-images/webview-mixedcontent-large.png#lightbox "WebView 混合內容大小處理特定的平台")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>設定項目輸入法編輯器選項

平台專屬設定輸入的法 (ime) 選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 這包括設定螢幕小鍵盤，以互動的右下角的使用者動作按鈕`Entry`。 它由在 XAML 中設定[ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty)附加屬性的值[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定的螢幕小鍵盤的輸入的法動作選項[ `Entry` ](xref:Xamarin.Forms.Entry)，與[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉型別提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) -表示沒有特定的動作索引鍵為必要項，而且它基礎控制項將會產生其本身是否可以。 這會是`Next`或`Done`。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – 表示，沒有動作索引鍵會成為可用。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – 表示動作索引鍵會執行"go"的作業，它們接受文字的目標使用者輸入。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – 表示動作索引鍵執行 「 搜尋 」 作業，取得結果的搜尋文字使用者他們已輸入。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – 表示動作索引鍵將會執行 「 傳送 」 作業，將文字傳送到其目標。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – 表示動作索引鍵會使使用者將會接受文字的下一個欄位的 下一步 」 作業。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – 指示動作索引鍵將執行 「 完成 」 的作業，並關閉螢幕小鍵盤。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – 指示將執行之動作索引鍵的先前的欄位，將會接受文字使使用者的 「 之前 」 作業。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – 遮罩，以選取動作的選項。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – 表示，工具會深入了解使用者，都建議根據使用者具有先前輸入的內容。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – 表示 UI 不應全螢幕。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – 指出將擷取的文字中顯示 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) -表示沒有 UI，將顯示的自訂動作。

結果是，指定[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)值會套用到螢幕小鍵盤的[ `Entry` ](xref:Xamarin.Forms.Entry)，它會設定輸入的法編輯器選項：

[![項目輸入方法編輯器平台專屬](android-images/entry-imeoptions.png "項目輸入方法編輯器平台專屬")](android-images/entry-imeoptions-large.png#lightbox "項目輸入編輯器平台特定方法")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用傳統色彩模式

一些 Xamarin.Forms 檢視功能舊版色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 回溯相容性，這種傳統的色彩模式仍受支援的檢視表的預設行為。

平台專屬停用這個傳統的色彩模式，，以便即使已停用檢視，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加屬性`false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement}))方法可以用來傳回是否已停用舊版的色彩模式。

結果是，好讓使用者在檢視上所設定的色彩即使仍停用檢視時，就可以停用舊版的色彩模式:

![](android-images/legacy-color-mode-disabled.png "停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>使用 [Android] 按鈕

平台專屬控制 Xamarin.Forms 按鈕使用的預設填補和 Android 的按鈕陰影值。 它由在 XAML 中設定[ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty)和[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty)附加至屬性`boolean`值：

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))和[`Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，無論 Xamarin.Forms 按鈕是使用預設值是用來控制填補和陰影的 Android 按鈕的值。 此外， [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))和[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))方法可以用來傳回按鈕是否使用預設的填補值和預設陰影值，分別。

結果是 Xamarin.Forms 按鈕可以使用的預設填補和 Android 的按鈕陰影值：

![](android-images/button-padding-and-shadow.png "停用舊版的色彩模式")

請注意，在上述每個螢幕擷取畫面[ `Button` ](xref:Xamarin.Forms.Button)有相同的定義，但右邊`Button`會使用預設填補和陰影的 Android 按鈕的值。

## <a name="summary"></a>總結

本文示範如何使用 Android 平台-細節建 Xamarin.Forms。 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。

## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
