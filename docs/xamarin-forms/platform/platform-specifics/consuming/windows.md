---
title: Windows 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章會示範如何使用 Windows 平台特性 Xamarin.Forms 內建。
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 43a681350035c3e965798bd63f49cd39f472ebfd
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998410"
---
# <a name="windows-platform-specifics"></a>Windows 平台特性

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。這篇文章會示範如何使用 Windows 平台特性 Xamarin.Forms 內建。_

在通用 Windows 平台 (UWP)，Xamarin.Forms 包含下列平台特性：

- 設定工具列的放置選項。 如需詳細資訊，請參閱 <<c0> [ 變更頁面工具列位置](#toolbar_placement)。
- 摺疊[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)導覽列中。 如需詳細資訊，請參閱 <<c0> [ 摺疊導覽列中 MasterDetailPage](#collapsable_navigation_bar)。
- 啟用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 的警示。 如需詳細資訊，請參閱 <<c0> [ 顯示 JavaScript 警示](#webview-javascript-alert)。
- 啟用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)與拼字檢查引擎互動。 如需詳細資訊，請參閱 <<c0> [ 啟用 SearchBar 拼字檢查](#searchbar-spellcheck)。
- 正在偵測讀取順序，從文字中的內容[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 如需詳細資訊，請參閱 <<c0> [ 偵測內容的讀取順序](#inputview-readingorder)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 停用舊版色彩模式](#legacy-color-mode)。
- 啟用在點選 筆勢支援[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 <<c0> [ 啟用點選 筆勢支援在 ListView 中](#listview-selectionmode)。
- 啟用顯示在頁面圖示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具列。 如需詳細資訊，請參閱 <<c0> [ 啟用圖示 TabbedPage](#tabbedpage-icons)。
- 設定存取金鑰[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 設定 VisualElement 便捷鍵](#visualelement-accesskeys)。

<a name="toolbar_placement" />

## <a name="changing-the-page-toolbar-placement"></a>變更頁面工具列位置

此平台專屬用來變更工具列的位置上[ `Page` ](xref:Xamarin.Forms.Page)，而且由在 XAML 中設定[ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty)附加屬性的值[`ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)列舉型別：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

`Page.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來設定工具列位置中，使用[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)列舉型別提供三個值： [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default)， [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top)，以及[ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom)。

結果就是指定的工具列位置，會套用至[ `Page` ](xref:Xamarin.Forms.Page)執行個體：

[![](windows-images/toolbar-placement.png "工具列位置平台專屬")](windows-images/toolbar-placement-large.png#lightbox "工具列放置平台專屬")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>摺疊 MasterDetailPage 導覽列

此平台專屬用來摺疊導覽列上[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，而且由在 XAML 中設定[ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty)並[ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)附加屬性：

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來指定摺疊樣式[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)提供兩個列舉值： [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full)並[ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial)。 [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double))方法用來指定部分摺疊的導覽列的寬度。

結果是，指定[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)會套用至[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)執行個體，也指定的寬度：

[![](windows-images/collapsed-navigation-bar.png "摺疊導覽列平台專屬")](windows-images/collapsed-navigation-bar-large.png#lightbox "摺疊導覽列中特定的平台")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>顯示 JavaScript 警示

啟用此平台專屬[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 的警示。 它由在 XAML 中設定[ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) ; 附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

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

`WebView.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否啟用 JavaScript 的警示。 颾魤 ㄛ`WebView.SetIsJavaScriptAlertEnabled`方法可用來切換 JavaScript 警示藉由呼叫[ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*)方法來傳回是否已啟用：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

結果是 UWP 訊息對話方塊中，可以顯示 JavaScript 警示：

![WebView JavaScript 警示平台專屬](windows-images/webview-javascript-alert.png "WebView JavaScript 警示平台專屬")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>啟用 SearchBar 拼字檢查

啟用此平台專屬[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)與拼字檢查引擎互動。 它由在 XAML 中設定[ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) ; 附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，將拼字檢查工具，開啟和關閉。 颾魤 ㄛ`SearchBar.SetIsSpellCheckEnabled`方法可用來切換拼字檢查工具，藉由呼叫[ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar}))方法來傳回是否已啟用拼字檢查工具：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

結果會是該文字方塊中輸入[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)可以拼字檢查，與使用者所表示的拼字不正確：

![SearchBar 拼字檢查平台專屬](windows-images/searchbar-spellcheck.png "SearchBar 拼字檢查特定平台")

> [!NOTE]
> `SearchBar`類別內[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間也有[ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*)並[ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*)可用來啟用和停用的方法拼字檢查`SearchBar`分別。

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>偵測內容中讀取順序

此平台特定啟用雙向文字中的讀取順序 （由左到右或從右至左） [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)動態偵測到的執行個體。 它由在 XAML 中設定[ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (如`Entry`並`Editor`執行個體) 或[ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) ; 附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否在內容中偵測到的讀取順序[ `InputView` ](xref:Xamarin.Forms.InputView). 颾魤 ㄛ`InputView.SetDetectReadingOrderFromContent`方法可用來切換讀取順序從內容呼叫來偵測是否[ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView}))方法來傳回目前的值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

結果是[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)執行個體可以有動態偵測到其內容的讀取順序：

[![偵測從平台專屬內容的讀取順序的 InputView](windows-images/editor-readingorder.png "InputView 偵測從平台專屬內容的讀取順序")](windows-images/editor-readingorder-large.png#lightbox "InputView 偵測從的讀取順序平台專屬的內容")

> [!NOTE]
> 不同於設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性中，偵測從其文字內容的讀取順序不會影響檢視內的文字的對齊方式的檢視表的邏輯。 相反地，它就會調整配置版面的雙向文字區塊的順序。

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>停用舊版色彩模式

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此平台專屬停用此舊版的色彩模式，以便即使檢視已停用，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](windows-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>啟用在 ListView 中的點選手勢支援

通用 Windows 平台上，預設 Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)會使用原生`ItemClick`事件，以回應互動，而不是原生`Tapped`事件。 這可提供協助工具功能，讓 Windows 朗讀程式和鍵盤可以互動`ListView`。 不過，它也會呈現在任何點選手勢`ListView`無法運作。

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，是用來控制中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)可以回應與點選手勢，[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)提供兩個可能值的列舉型別：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 表示`ListView`就會引發原生`ItemClick`事件處理互動，並因此提供協助工具功能。 因此，Windows 朗讀程式和鍵盤可以互動`ListView`。 不過中的項目`ListView`無法回應點選手勢。 這是預設行為，如`ListView`通用 Windows 平台上的執行個體。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 表示`ListView`就會引發原生`Tapped`來處理互動的事件。 因此中的項目`ListView`可以回應點選手勢。 不過，沒有任何協助工具功能，並因此 Windows 朗讀程式和鍵盤不能與互動`ListView`。

> [!NOTE]
> `Accessible`並`Inaccessible`的選取模式是互斥的而且您必須選擇可供存取[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可以回應點選手勢。

颾魤 ㄛ [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可以用來傳回目前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

結果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)會套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，哪些控制項是否中的項目`ListView`點選手勢，可回應，因此是否原生`ListView`引發`ItemClick`或`Tapped`事件。

<a name="tabbedpage-icons" />

## <a name="enabling-icons-on-a-tabbedpage"></a>啟用 TabbedPage 圖示

此平台專屬可讓頁面上要顯示的圖示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具列上，並提供的功能，可選擇性地指定圖示大小。 它由在 XAML 中設定[ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) ; 附加屬性`true`，以及選擇性地設定[ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) ; 附加屬性[ `Size`](xref:Xamarin.Forms.Size)值：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

`TabbedPage.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來開啟標頭圖示，開啟或關閉。 [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size))方法會選擇性地指定標頭圖示大小[ `Size` ](xref:Xamarin.Forms.Size)值。

颾魤 ㄛ`TabbedPage`類別內`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`命名空間也有[ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*)方法，可讓標頭圖示[ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*)標頭圖示，會停用的方法和[ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*)方法會傳回`boolean`值，指出是否啟用標頭圖示。

結果會是該頁面，可以顯示圖示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具列上，選擇性地設定為所需大小的圖示大小：

![TabbedPage 圖示啟用平台專屬](windows-images/tabbedpage-icons.png "TabbedPage 圖示啟用平台專屬")

<a name="visualelement-accesskeys" />

## <a name="setting-visualelement-access-keys"></a>設定 VisualElement 存取金鑰

存取金鑰會藉由提供應用程式的顯示 UI，透過觸控式鍵盤而不是透過使用者快速瀏覽並與其互動以直覺的方式改善可用性和通用 Windows 平台上的應用程式的協助工具的鍵盤快速鍵或滑鼠。 也就是一或多個英數字元的索引鍵，通常依序按下 Alt 鍵的組合。 鍵盤快速鍵自動支援使用單一的英數字元的存取金鑰。

存取金鑰祕訣則浮動的控制項，包括存取金鑰旁邊顯示的徽章。 每個存取金鑰的提示包含英數字元的索引鍵的啟動相關聯的控制項。 當使用者按下 Alt 鍵時，則會顯示存取金鑰祕訣。

此平台專屬用來指定存取金鑰[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 它由在 XAML 中設定[ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty)為英數字元值，並選擇性地設定附加屬性[ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty)附加屬性值為[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)列舉[ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) ; 附加屬性`double`，而[ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) ;附加屬性`double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來設定存取金鑰值，如`VisualElement`。 [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement))方法，選擇性地指定要用於顯示存取金鑰祕訣，位置[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)列舉提供下列可能的值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – 表示 存取金鑰的小費放置將會由作業系統決定。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) -指出存取關鍵提示會出現上述的上邊緣`VisualElement`。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) -指出存取關鍵提示會出現如下的下邊緣`VisualElement`。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) -指出存取關鍵提示會出現右邊的右緣`VisualElement`。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – 表示存取關鍵提示會顯示左邊的左邊緣`VisualElement`。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) -指出的中心點會出現重疊的存取金鑰祕訣`VisualElement`。

> [!NOTE]
> 通常[ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto)關鍵提示放置已足夠，其中包括支援的自適性使用者介面。

[ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))並[ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))方法可用於更細微的控制的存取金鑰祕訣位置。 引數`SetAccessKeyHorizontalOffset`方法會指示如何向移動的存取金鑰提示左或向右和引數`SetAccessKeyVerticalOffset`方法指出目前存取關鍵提示上移或下移的方式。

>[!NOTE]
> 在設定的存取金鑰的位置時，就無法設定存取關鍵提示位移`Auto`。

颾魤 ㄛ [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))，以及[ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))可用方法若要擷取存取金鑰值和它的位置。

結果是存取重要的提示，可以顯示任何旁邊[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)定義的執行個體存取金鑰，藉由按下 Alt 鍵：

![VisualElement 存取金鑰的平台專屬](windows-images/visualelement-accesskeys.png "VisualElement 存取金鑰的特定平台")

當使用者啟用存取金鑰，藉由按下 Alt 鍵後面跟著存取金鑰的預設動作`VisualElement`就會執行。 例如，當使用者啟動的存取金鑰上[ `Switch` ](xref:Xamarin.Forms.Switch)，則`Switch`切換。 當使用者啟動上的存取金鑰[ `Entry` ](xref:Xamarin.Forms.Entry)，則`Entry`取得焦點。 當使用者啟動上的存取金鑰[ `Button` ](xref:Xamarin.Forms.Button)，事件處理常式，如[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)執行事件。

如需存取金鑰的詳細資訊，請參閱[存取金鑰](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Windows 平台特性 Xamarin.Forms 內建。 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
