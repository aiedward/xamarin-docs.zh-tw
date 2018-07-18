---
title: 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 10adb46493a1cdbb6bc6a2fd67b5191633d7eeeb
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997295"
---
# <a name="platform-specifics"></a>平台特性

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。_

下列平台特有的功能內建 Xamarin.Forms:

|iOS|Android|Windows|
|--- |--- |--- |
|[VisualElement.BlurEffect](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur)|[Application.WindowSoftInputModeAdjust](~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode)|[Page.ToolbarPlacement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement)|
|[NavigationPage.PrefersLargeTitles](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)|[ListView.IsFastScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll)|[MasterDetailPage.CollapsedPaneWidth 和 MasterDetailPage.CollapseStyle](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar)|
|[Page.UseSafeArea](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout)|[TabbedPage.IsSwipePagingEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging)|[WebView.IsJavaScriptAlertEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#webview-javascript-alert)
|[NavigationPage.IsNavigationBarTranslucent](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar)|[VisualElement.Elevation](~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation)|[SearchBar.IsSpellCheckEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#searchbar-spellcheck)
|[NavigationPage.StatusBarTextColorMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode)|[Application.SendDisappearingEventOnPause、 Application.SendAppearingEventOnResume 和 Application.ShouldPreserveKeyboardOnResume](~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events)|[InputView.DetectReadingOrderFromContent Label.DetectReadingOrderFromContent](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#inputview-readingorder)
|[Entry.AdjustsFontSizeToFitWidth](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size)|[WebView.MixedContentMode](~/xamarin-forms/platform/platform-specifics/consuming/android.md#webview-mixed-content)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#legacy-color-mode)|
|[Picker.UpdateMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)|[Entry.ImeOptions](~/xamarin-forms/platform/platform-specifics/consuming/android.md#entry-imeoptions)|[ListView.SelectionMode](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#listview-selectionmode)|
|[Page.PrefersStatusBarHidden 和 Page.PreferredStatusBarUpdateAnimation](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#legacy-color-mode)|[TabbedPage.HeaderIconsEnabled 和 TabbedPage.HeaderIconsSize](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#tabbedpage-icons)|
|[ScrollView.ShouldDelayContentTouches](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches)|[Button.UseDefaultPadding 和 Button.UseDefaultShadow](~/xamarin-forms/platform/platform-specifics/consuming/android.md#button-padding-shadow)|[VisualElement.AccessKey、 VisualElement.AccessKeyPlacement、 VisualElement.AccessKeyHorizontalOffset 和 VisualElement.AccessKeyVerticalOffset](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys)|
|[ListView.SeparatorStyle](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#listview-separatorstyle)|[TabbedPage.ToolbarPlacement、 TabbedPage.BarItemColor 和 TabbedPage.BarSelectedItemColor](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)|
|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#legacy-color-mode)|
|[VisualElement.IsShadowEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#drop-shadow)|
|[Application.PanGestureRecognizerShouldRecognizeSimultaneously](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#simultaneous-pan-gesture)|

使用特定平台-透過 XAML，或透過 fluent 的程式碼 API 的程序如下所示：

1. 新增`xmlns`宣告或`using`指示詞[ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration)命名空間。
1. 新增`xmlns`宣告或`using`包含平台特定功能之命名空間指示詞：
    1. 在 iOS 上，這是[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間。
    1. 在 Android 上，這是[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間。 這是針對 Android 的 AppCompat [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空間。
    1. 在通用 Windows 平台，這是[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間。
1. 從 XAML，或使用的程式碼，適用於特定平台`On<T>`fluent API。 值`T`可以是[ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS)， [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android)，或[ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows)類型[ `Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration)命名空間。

> [!NOTE]
> 請注意，嘗試使用就無法使用的平台上的平台專屬不會導致錯誤。 相反地，程式碼會執行而不需要平台特定方式套用。

透過平台特性取用`On<T>`fluent 的程式碼 API 傳回[ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2)物件。 這可讓具有階層式方法的相同物件上叫用的多個平台特性。

如需有關平台特性的詳細資訊，請參閱[耗用平台特性](~/xamarin-forms/platform/platform-specifics/consuming/index.md)並[建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)。


## <a name="related-links"></a>相關連結

- [使用平台特性](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
