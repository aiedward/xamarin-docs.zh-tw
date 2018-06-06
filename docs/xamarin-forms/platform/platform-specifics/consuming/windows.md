---
title: Windows 平台內容
description: 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。 本文將示範如何使用 Windows 平台的內容內建 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 7299de658a3491928e9bbeaa4dd192a8e95c435e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732797"
---
# <a name="windows-platform-specifics"></a>Windows 平台內容

_平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。本文將示範如何使用 Windows 平台的內容內建 Xamarin.Forms。_

在通用 Windows 平台 (UWP)，Xamarin.Forms 包含下列平台特性：

- 設定工具列放置選項。 如需詳細資訊，請參閱[變更工具列位置](#toolbar_placement)。
- 摺疊[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)導覽列。 如需詳細資訊，請參閱[MasterDetailPage 導覽列摺疊](#collapsable_navigation_bar)。
- 啟用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 警示。 如需詳細資訊，請參閱[顯示 JavaScript 警示](#webview-javascript-alert)。
- 啟用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)與拼字檢查引擎互動。 如需詳細資訊，請參閱[啟用 SearchBar 拼字檢查](#searchbar-spellcheck)。
- 偵測從文字中的內容讀取順序[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 如需詳細資訊，請參閱[偵測從內容的讀取順序](#inputview-readingorder)。
- 停用上支援的舊版色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[停用傳統色彩模式](#legacy-color-mode)。
- 啟用中的點選手勢支援[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱[啟用點選手勢支援在 ListView 中](#listview-selectionmode)。

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>變更工具列位置

平台專屬用來變更工具列的位置上[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，且由在 XAML 中設定[ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/)附加屬性的值[`ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)列舉型別：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

`Page.On<Windows>`方法會指定平台專屬只會在 Windows 上執行。 [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)命名空間，用來設定與工具列位置[ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)列舉型別提供三個值： [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default/)， [ `Top` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top/)，和[ `Bottom` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom/)。

結果是指定的工具列位置，會套用至[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)執行個體：

[![](windows-images/toolbar-placement.png "工具列位置平台專屬")](windows-images/toolbar-placement-large.png#lightbox "工具列放置平台專屬")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>摺疊 MasterDetailPage 導覽列

平台專屬用來摺疊導覽列上[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)，且由在 XAML 中設定[ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/)和[ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)附加屬性：

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>`方法會指定平台專屬只會在 Windows 上執行。 [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)命名空間，用來與指定的摺疊樣式， [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)提供兩個列舉值： [ `Full` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full/)和[ `Partial` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial/)。 [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/)方法用來指定部分摺疊的導覽列的寬度。

結果是，指定[ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)套用至[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)執行個體，也指定寬度：

[![](windows-images/collapsed-navigation-bar.png "摺疊平台專屬的巡覽列")](windows-images/collapsed-navigation-bar-large.png#lightbox "摺疊平台專屬的巡覽列")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>顯示 JavaScript 警示

啟用此平台專屬[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 警示。 它由在 XAML 中設定[ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty)附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

`WebView.On<Windows>`方法會指定平台專屬只會在通用 Windows 平台上執行。 [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否已啟用 JavaScript 警示。 此外，`WebView.SetIsJavaScriptAlertEnabled`方法可以用來切換藉由呼叫的 JavaScript 警示[ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*)方法來傳回是否已啟用：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

結果是 UWP 訊息對話方塊中，可以顯示 JavaScript 警示：

![特定平台的 WebView JavaScript 警示](windows-images/webview-javascript-alert.png "特定平台的 WebView JavaScript 警示")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>啟用 SearchBar 拼字檢查

啟用此平台專屬[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)與拼字檢查引擎互動。 它由在 XAML 中設定[ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty)附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>`方法會指定平台專屬只會在通用 Windows 平台上執行。 [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間中，開啟拼字檢查工具和關閉。 此外，`SearchBar.SetIsSpellCheckEnabled`方法可以用來切換拼字檢查，藉由呼叫[ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar}))方法來傳回是否已啟用拼字檢查：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

結果是文字方塊中輸入該文字[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)能夠拼字檢查，與正在向使用者指示的拼字不正確：

![SearchBar 拼字檢查平台專屬](windows-images/searchbar-spellcheck.png "SearchBar 拼字檢查特定平台")

> [!NOTE]
> `SearchBar`類別[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間也有[ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*)和[ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*)可用來啟用和停用的方法拼字檢查`SearchBar`分別。

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>偵測從內容讀取順序

平台專屬啟用雙向文字中的讀取順序 （由左到右或從右至左） [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)動態偵測到的執行個體。 它由在 XAML 中設定[ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (如`Entry`和`Editor`執行個體) 或[ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty)附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法會指定平台專屬只會在通用 Windows 平台上執行。 [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制讀取順序偵測到內容中是否[ `InputView` ](xref:Xamarin.Forms.InputView). 此外，`InputView.SetDetectReadingOrderFromContent`方法可以用來切換是否讀取順序中偵測到內容呼叫[ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView}))方法以傳回目前的值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

結果是[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)執行個體可以有動態偵測到其內容的讀取順序：

[![偵測平台專屬內容從讀取順序的 InputView](windows-images/editor-readingorder.png "InputView 偵測平台專屬內容從讀取順序")](windows-images/editor-readingorder-large.png#lightbox "InputView 偵測從讀取順序平台專屬內容")

> [!NOTE]
> 不同於設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性、 檢視偵測到來自其文字內容的讀取順序將不會影響檢視內文字的對齊方式的邏輯。 相反地，它就會調整所在的雙向文字區塊配置版面的順序。

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用傳統色彩模式

一些 Xamarin.Forms 檢視功能舊版色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 回溯相容性，這種傳統的色彩模式仍受支援的檢視表的預設行為。

平台專屬停用這個傳統的色彩模式，，以便即使已停用檢視，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加屬性`false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>`方法會指定平台專屬只會在 Windows 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))方法可以用來傳回是否已停用舊版的色彩模式。

結果是，好讓使用者在檢視上所設定的色彩即使仍停用檢視時，就可以停用舊版的色彩模式:

![](windows-images/legacy-color-mode-disabled.png "停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>啟用 ListView 中的點選手勢支援

通用 Windows 平台上，依預設 Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)使用原生`ItemClick`事件回應互動，而不是原生`Tapped`事件。 這可提供協助工具功能，讓 Windows [朗讀程式] 和鍵盤可以互動`ListView`。 不過，它也會呈現任何內部的點選手勢`ListView`無法運作。

此平台特定的控制項是否中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)點選手勢，可回應，因此是否原生`ListView`引發`ItemClick`或`Tapped`事件。 它由在 XAML 中設定[ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加屬性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)列舉型別：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法會指定平台專屬只會在通用 Windows 平台上執行。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)是否可用來控制命名空間中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)可以點選手勢，以回應[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)提供兩個可能值的列舉：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 表示`ListView`會引發原生`ItemClick`事件處理互動，並因此提供協助工具功能。 因此，Windows [朗讀程式] 和鍵盤可以互動`ListView`。 不過中的項目`ListView`點選 [筆勢] 無法回應。 這是預設行為，如`ListView`通用 Windows 平台上的執行個體。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 表示`ListView`會引發原生`Tapped`事件來處理互動。 因此中的項目`ListView`可以回應點選手勢。 不過，沒有任何協助工具功能，並因此 Windows [朗讀程式] 和鍵盤無法互動`ListView`。

> [!NOTE]
> `Accessible`和`Inaccessible`選取模式是互斥的而且您必須選擇可存取[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可以回應點選手勢。

此外， [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可以用來傳回目前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

結果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，可控制是否中的項目`ListView`點選手勢，可回應，因此是否原生`ListView`引發`ItemClick`或`Tapped`事件。

## <a name="summary"></a>總結

本文示範如何使用 Windows 平台的內容內建 Xamarin.Forms。 平台特性可讓您使用才有特定的平台，而不需要實作自訂轉譯器或影響的功能。

## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
