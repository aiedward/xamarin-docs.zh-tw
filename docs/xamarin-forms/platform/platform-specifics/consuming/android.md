---
title: "Android 平台特性"
description: "平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。 本文示範如何使用 Android 平台-細節建 Xamarin.Forms。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: 739ee4ebeb3176d23ab1eb911baaab31a26252c4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="android-platform-specifics"></a>Android 平台特性

_平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。本文示範如何使用 Android 平台-細節建 Xamarin.Forms。_

在 Android 上，Xamarin.Forms 會包含下列平台特性：

- 設定螢幕小鍵盤的作業模式。 如需詳細資訊，請參閱[設定彈性的鍵盤輸入模式](#soft_input_mode)。
- 啟用快速捲動[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)如需詳細資訊，請參閱[啟用 ListView 中的快速捲動](#fastscroll)。
- 啟用在頁面之間撥動[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 如需詳細資訊，請參閱[啟用撥動頁之間的中 TabbedPage](#enable_swipe_paging)。
- 控制疊置順序的視覺項目來判斷繪製順序。 如需詳細資訊，請參閱[控制提高權限的視覺化項目](#elevation)。
- 停用[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)頁面生命週期事件上暫停和繼續分別使用 AppCompat 的應用程式。 如需詳細資訊，請參閱[停用 Disappearing 和出現網頁生命週期事件](#disable_lifecycle_events)。

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

`TabbedPage.On<Android>`方法會指定平台專屬只會在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來啟用 在頁面之間撥動[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 此外，`TabbedPage`類別`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空間也有[ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)方法可讓此平台特定，和[ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)停用的方法此平台專屬。 [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/)附加屬性，以及[ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/)方法，用來設定應保留在目前頁面的任一邊閒置狀態的頁面數目。

結果是透過頁面顯示該撥動分頁[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)已啟用：

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>控制視覺元素的權限提高

平台專屬是用來控制 API 21 為目標的提高權限或疊置順序，在應用程式上的視覺項目或更高。 提高的權限視覺項目決定其繪製順序，與視覺化元素有 occluding 視覺項目具有較低的 Z 值較大的 Z 值。 它由在 XAML 中設定`Elevation.Elevation`附加屬性`boolean`值：

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
            <Button Text="Button Above BoxView - Click Me" android:Elevation.Elevation="10"/>
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

`Button.On<Android>`方法會指定平台專屬只會在 Android 上執行。 `Elevation.SetElevation`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空間，用來提高的權限的視覺項目設定為可為 null `float`。 此外，`Elevation.GetElevation`方法可以用來擷取的視覺元素的高度值。

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

## <a name="summary"></a>總結

本文示範如何使用 Android 平台-細節建 Xamarin.Forms。 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。


## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
