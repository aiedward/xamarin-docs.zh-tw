---
title: Android 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章會示範如何使用 Android 平台特性 Xamarin.Forms 內建。
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 5ed11e4afb4c061eb7b9dd8f10c67090b4134888
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996382"
---
# <a name="android-platform-specifics"></a>Android 平台特性

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。這篇文章會示範如何使用 Android 平台特性 Xamarin.Forms 內建。_

在 Android 上，Xamarin.Forms 會包含下列平台特性：

- 設定螢幕小鍵盤的作業模式。 如需詳細資訊，請參閱 <<c0> [ 虛的鍵盤輸入模式設定](#soft_input_mode)。
- 啟用快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)如需詳細資訊，請參閱[啟用在 ListView 中的快速捲動](#fastscroll)。
- 啟用頁面間撥動[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 如需詳細資訊，請參閱 <<c0> [ 啟用撥動頁之間的中 TabbedPage](#enable_swipe_paging)。
- 控制疊置順序的視覺項目來判斷繪製順序。 如需詳細資訊，請參閱 <<c0> [ 控制提高權限的視覺項目](#elevation)。
- 停用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)並[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)頁面生命週期事件上暫停和繼續分別使用 AppCompat 的應用程式。 如需詳細資訊，請參閱 <<c0> [ 停用不會出現頁面生命週期事件與 Disappearing](#disable_lifecycle_events)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以顯示混合的內容。 如需詳細資訊，請參閱 <<c0> [ 啟用混合內容的 WebView 中](#webview-mixed-content)。
- 設定輸入的法編輯器選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱 <<c0> [ 設定項目輸入法編輯器選項](#entry-imeoptions)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 停用舊版色彩模式](#legacy-color-mode)。
- 使用預設填補和 Android 的按鈕的陰影值。 如需詳細資訊，請參閱 <<c0> [ 使用 Android 按鈕](#button-padding-shadow)。
- 設定工具列位置和色彩[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 如需詳細資訊，請參閱 <<c0> [ 設定 TabbedPage 工具列位置和色彩](#tabbedpage-toolbar)。

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>設定螢幕小鍵盤輸入的模式

此平台專屬用來設定螢幕小鍵盤輸入區域中，作業模式和由在 XAML 中設定[ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty)附加屬性的值[ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)列舉型別：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定螢幕小鍵盤輸入的區作業模式，與[ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)提供兩個值的列舉型別： [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan)並[ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize)。 `Pan`值使用[ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/)調整選項，不會調整視窗大小的輸入的控制項具有焦點時。 相反地，使目前的焦點不會遮住螢幕小鍵盤，便會上下移動視窗的內容。 `Resize`值使用[ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/)輸入的控制項具有焦點，以騰出供螢幕小鍵盤時調整視窗的調整選項。

結果是螢幕小鍵盤輸入的區域輸入的控制項具有焦點時，就可以設定作業模式：

[![](android-images/pan-resize.png "螢幕小鍵盤操作模式特定的平台")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>啟用快速捲動 ListView

此平台專屬用來啟用 快速捲動中的資料[ `ListView` ](xref:Xamarin.Forms.ListView)。 它由在 XAML 中設定`ListView.IsFastScrollEnabled`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `ListView.SetIsFastScrollEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來啟用中的資料進行快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)。 颾魤 ㄛ`SetIsFastScrollEnabled`方法可用來切換藉由呼叫快速捲動`IsFastScrollEnabled`方法來傳回是否已啟用快速捲動：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

結果是透過資料在該快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)可以啟用，如此會變更捲軸捲動方塊的大小：

[![](android-images/fastscroll.png "ListView FastScroll 平台專屬")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>啟用撥動 TabbedPage 頁面間

此平台專屬用來啟用與中的頁面之間的水平的手指筆勢撥動[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 它由在 XAML 中設定[ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) ; 附加屬性`boolean`值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來啟用在頁面之間撥動[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 颾魤 ㄛ`TabbedPage`類別內`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空間也有[ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))方法，可讓此平台特定，和[ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))停用的方法此平台專屬。 [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty)附加屬性，以及[ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32))方法，用來設定應保留在目前頁面的任一端閒置狀態的頁面數目。

結果會是所顯示的頁面，撥動逐頁[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)已啟用：

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>控制視覺元素的權限提高

此平台專屬是用來控制提高權限或疊置順序，在應用程式上的視覺元素的目標 API 21 或更新版本。 提高權限的圖形化元素會決定繪製的順序，與視覺項目有較大的 Z 值 occluding 視覺項目具有較低的 Z 值。 它由在 XAML 中設定`VisualElement.Elevation`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

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

`Button.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `VisualElement.SetElevation`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定可為 null 的提高權限之視覺項目的`float`。 颾魤 ㄛ`VisualElement.GetElevation`方法可用來擷取的圖形化元素的高度值。

結果是，讓有較大的 Z 值的視覺元素 occlude 視覺項目具有較低的 Z 值，您可以控制視覺元素的提高權限。 因此，在此範例中，第二個[ `Button` ](xref:Xamarin.Forms.Button)上方轉譯[ `BoxView` ](xref:Xamarin.Forms.BoxView)因為它具有較高的權限提高值：

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>停用消失與顯示的網頁生命週期事件

此平台專屬用來停用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)並[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)網頁事件，應用程式上的暫停和繼續分別使用 AppCompat 的應用程式。 此外，它能夠控制是否繼續執行後，會顯示螢幕小鍵盤，如果它顯示在暫停時，已提供螢幕小鍵盤的作業模式設定為[ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize)。

> [!NOTE]
> 請注意，預設會啟用這些事件以保留現有的事件所依賴的應用程式的行為。 停用這些事件，使符合 pre AppCompat 事件循環 AppCompat 事件循環。

此平台專屬可供在 XAML 中設定[ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty)， [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty)，以及[ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) 附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

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

`Application.Current.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空間，用來啟用或停用引發[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)網頁事件，當應用程式進入背景。 [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法用來啟用或停用引發[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)網頁事件，當應用程式從背景。 [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法可控制是否繼續執行後，會顯示螢幕小鍵盤，如果它顯示在暫停時，提供螢幕小鍵盤的作業模式設定為[ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

結果是[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)並[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)頁面事件不會引發應用程式暫停和繼續分別因而，如果螢幕小鍵盤時應用程式已暫停時，它也會顯示應用程式會繼續執行時：

[![](android-images/keyboard-on-resume.png "生命週期事件平台專屬")](android-images/keyboard-on-resume-large.png#lightbox "生命週期事件平台專屬")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>啟用在 WebView 中混合的內容

此平台特定的控制項是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以顯示混合的內容中的應用程式 API 21 或更新版本為目標。 混合的內容是透過 HTTPS 連線，一開始載入的但透過 HTTP 連線載入資源 （例如影像、 音訊、 視訊、 樣式表、 指令碼） 的內容。 它由在 XAML 中設定[ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加屬性的值[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否可以顯示混合的內容，與[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三個可能值的列舉型別：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將允許 HTTPS origin 從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將不允許從 HTTP 來源載入內容 HTTPS origin。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)會嘗試將相容的最新的裝置網頁瀏覽器的方法。 部分 HTTP 內容可能會允許 HTTPS origin 所應載入和其他類型的內容將會遭到封鎖。 每個作業系統版本，可能會變更的是被封鎖或允許的內容類型。

結果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值套用至[ `WebView` ](xref:Xamarin.Forms.WebView)，這會控制是否可以顯示混合的內容：

[![混合內容處理特定的平台的 WebView](android-images/webview-mixedcontent.png "混合內容處理特定的平台的 WebView")](android-images/webview-mixedcontent-large.png#lightbox "WebView 混合內容處理特定的平台")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>設定項目輸入法編輯器選項

此平台特定設定的輸入的法編輯器 (IME) 選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 這包括設定使用者的 [動作] 按鈕，在螢幕小鍵盤和與互動的角`Entry`。 它由在 XAML 中設定[ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty)附加屬性的值[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定的螢幕小鍵盤的輸入的法動作選項[ `Entry` ](xref:Xamarin.Forms.Entry)，具有[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) -表示沒有特定的動作索引鍵為必要項，而且其基礎的控制項將會產生其本身如果可以。 這會是`Next`或`Done`。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – 表示，沒有動作索引鍵都可以。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – 表示 [動作] 鍵將會執行 [執行] 作業，它們將文字的目標使用者輸入。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – 表示 [動作] 鍵執行 「 搜尋 」 作業，將使用者結果的搜尋文字輸入。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – 表示 [動作] 鍵將會執行 「 傳送 」 作業，傳遞到其目標的文字。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – 表示 [動作] 鍵，會執行"next"的作業，讓使用者可以將接受文字的下一個欄位。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – 表示 [動作] 鍵，會執行為 「 已完成 」 的作業，關閉螢幕小鍵盤。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – 表示 [動作] 鍵，會執行 「 之前 」 的作業，並將使用者帶到會接受文字的前一個欄位。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – 要選取 [動作] 選項的遮罩。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – 表示拼字檢查功能將既不了解使用者，也建議修正根據使用者具有先前輸入的內容。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – 表示 UI 應該不會全螢幕。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – 指出已擷取的文字將會顯示任何 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) -表示沒有 UI，將顯示的自訂動作。

結果是，指定[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)值套用至的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)，可設定的輸入的法編輯器的選項：

[![項目輸入方法編輯器平台專屬](android-images/entry-imeoptions.png "項目輸入方法編輯器平台專屬")](android-images/entry-imeoptions-large.png#lightbox "項目輸入方法編輯器平台專屬")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用舊版色彩模式

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此平台專屬停用此舊版的色彩模式，以便即使檢視已停用，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](android-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>使用 Android 的按鈕

此平台特定的控制項 Xamarin.Forms 按鈕使用的預設邊框距離和 Android 的按鈕的陰影值。 它由在 XAML 中設定[ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty)並[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty)附加屬性，以`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))並[`Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否 Xamarin.Forms 按鈕會使用預設值邊框距離和 Android 的按鈕的陰影值。 颾魤 ㄛ [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))並[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))方法可以用來傳回按鈕是否要使用的預設邊框距離值和預設陰影值，分別。

結果是 Xamarin.Forms 按鈕可以使用預設填補和 Android 的按鈕的陰影值：

![](android-images/button-padding-and-shadow.png "已停用舊版的色彩模式")

請注意，在上述每個螢幕擷取畫面[ `Button` ](xref:Xamarin.Forms.Button)有相同的定義，不同之處在於右側`Button`會使用預設填補和 Android 的按鈕的陰影值。

<a name="tabbedpage-toolbar" />

## <a name="setting-tabbedpage-toolbar-placement-and-color"></a>設定 TabbedPage 工具列位置和色彩

若要設定的位置和色彩的工具列上使用這些平台特性[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 它們都是在 XAML 中藉由設定[ `TabbedPage.ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.toolbarplacementproperty?view=xamarin-forms)附加屬性的值[ `ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms)列舉型別，而[ `TabbedPage.BarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.baritemcolorproperty?view=xamarin-forms)和[ `TabbedPage.BarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.barselecteditemcolorproperty?view=xamarin-forms)附加至屬性[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

或者，他們可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>`方法可讓您指定只會在 Android 上執行這些平台特性。 [ `TabbedPage.SetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.settoolbarplacement?view=xamarin-forms)方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定工具列位置[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，與[`ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms)列舉提供下列值：

- [`Default`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Default) – 表示工具列會放在頁面上的預設位置。 這是頁面的在電話上，頁面頂端和底部上其他裝置的習慣用語。
- [`Top`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Top) – 表示工具列位於頁面頂端。
- [`Bottom`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Bottom) – 表示工具列位於頁面底部。

颾魤 ㄛ [ `TabbedPage.SetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_)並[ `TabbedPage.SetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_)方法來分別設定工具列項目及選取的工具列項目的色彩。

> [!NOTE]
> [ `GetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.gettoolbarplacement?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetToolbarPlacement_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__)， [ `GetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__)，以及[ `GetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__)方法可以用來擷取的位置和色彩[ `TabbedPage`](xref:Xamarin.Forms.TabbedPage)工具列。

結果是可以設定工具列位置、 工具列項目的色彩和色彩選取的工具列項目的上[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="summary"></a>總結

這篇文章會示範如何使用 Android 平台特性 Xamarin.Forms 內建。 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
