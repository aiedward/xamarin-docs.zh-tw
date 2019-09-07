---
title: 第 25 章的摘要。 頁面變化
description: 使用 Xamarin 建立 Mobile Apps：第 25 章的摘要。 頁面變化
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760540"
---
# <a name="summary-of-chapter-25-page-varieties"></a>第 25 章的摘要。 頁面變化

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

到目前為止您已了解兩個類別衍生自`Page`:`ContentPage`和`NavigationPage`。 本章介紹其他兩個：

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理兩個頁面、 主版和詳細資料
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 管理多個存取透過索引標籤的子頁面

這些頁面類型提供更複雜的巡覽選項，比`NavagationPage`所述[第 24 章。頁面瀏覽](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>Master 和詳細資料

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)定義兩個屬性的型別`Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)並[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)。 通常您會設定這些屬性的每個`ContentPage`。 `MasterDetailPage`會顯示與這兩個頁面之間切換。

有兩種基本的方法，這兩個頁面之間切換：

- *分割*其中的主要和詳細資料會並排顯示
- *popover*其中涵蓋詳細資料頁面，或部分涵蓋的主要頁面

有數個變化*popover*方法 (*投影片*，*重疊*，以及*交換*)，但這些通常是平台相依。 您可以設定[ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)屬性`MasterDetailPage`成員[ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior)列舉型別：

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

不過，這個屬性沒有在手機上的任何作用。 電話一定 popover 行為。 只有平板電腦和桌上型電腦的 windows 可以有分割的行為。

### <a name="exploring-the-behaviors"></a>瀏覽行為

[ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)範例可讓您試驗不同的裝置上的預設行為。 程式包含兩個不同`ContentPage`衍生項目之 master 和詳細資料 (使用`Title`屬性上設定)，和另一個類別衍生自`MasterDetailPage`，將它們合併。 詳細資料頁面住`NavigationPage`因為 UWP 程式不會運作，而不需要它。

在 Windows 8.1 和 Windows Phone 8.1 平台需要點陣圖會設為`Icon`主版頁面的屬性。

### <a name="back-to-school"></a>回到學校

[ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)範例採用稍有不同的方法來建構程式，以顯示學生[ **SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫。

`Master`並`Detail`屬性中的視覺化樹狀結構以定義[SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)檔案，它是衍生自`MasterDetailPage`。 這種安排可讓資料繫結至設定成介於 master] 和 [詳細資料頁面。

XAML 檔案，也設定[ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性`MasterDetailPage`到`True`。 這會導致在啟動時，要顯示的主版頁面根據預設會顯示詳細資料頁面。 [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)檔中設定`IsPresented`要`false`從選取項目時`ListView`主版頁面中。 接著會顯示詳細資料頁面：

[![三重的螢幕擷取畫面的學校和詳細資料](images/ch25fg09-small.png "詳細資料頁面上，從 MasterDetailPage")](images/ch25fg09-large.png#lightbox "MasterDetailPage 從詳細資料頁面")

### <a name="your-own-user-interface"></a>使用者介面

Xamarin.Forms 提供使用者介面 master] 和 [詳細資料檢視之間切換，雖然您可以提供您自己。 方法如下：

- 設定[ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled)屬性設`false`撥動停用
- 覆寫[ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton)方法，並傳回`false`隱藏 Windows 8.1 和 Windows Phone 8.1 上的工具列按鈕。

如所示，您必須接著提供方法來切換主要和詳細資料 頁面中， [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)範例。

[ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)範例示範另一個方法使用`TapGestureRecognizer`master] 和 [詳細資料頁面上。

## <a name="tabbedpage"></a>TabbedPage

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)是您可以切換使用索引標籤的分頁的集合。 它衍生自`MultiPage<Page>`並定義任何公用屬性或其本身的方法。 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)不過，沒有定義屬性：

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 類型屬性 `IList<T>`

您在這裡填入`Children`頁面物件的集合。

另一種方法可讓您定義`TabbedPage`子系就像是`ListView`使用自動產生索引標籤式的頁面，這兩個屬性：

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 型別 `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 型別 `DataTemplate`

不過，這種方法無法運作也在 iOS 當集合包含多個項目。

`MultiPage<T>` 定義兩個的多個屬性，可讓您追蹤目前檢視的頁面：

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) 型別的`T`、 參考至頁面
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) 型別的`Object`參考中的物件、`ItemsSource`集合

`MultiPage<T>` 也會定義兩個事件：

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) 當`ItemsSource`集合變更
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) 當檢視的頁面變更

### <a name="discrete-tab-pages"></a>不連續的索引標籤頁

[ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)範例包含三個不同的方式顯示色彩的三個索引標籤式頁面。 每個索引標籤`ContentPage`系出物件，然後`TabbedPage`衍生[DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)結合了三個頁面。

每個頁面，會出現在`TabbedPage`，則`Title`屬性必要屬性索引標籤中，指定的文字和 Apple Store 需要圖示可用，因此`Icon`設定適用於 iOS:

[![個別索引標籤色彩的三個螢幕擷取畫面](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)範例有列出所有學生的首頁。 當學生點選時，這會導覽至`TabbedPage`系出物件[ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)，，其中包含三個`ContentPage`物件在其視覺化樹狀結構，其中可讓您輸入該學生的一些注意事項。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)範例會使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)檔中設定`DataTemplate`屬性`TabbedPage`要以視覺化樹狀結構開頭`ContentPage`，其中包含屬性的繫結`NamedColor`（包括繫結至`Title`屬性)。

不過，這是在 iOS 上有問題。 可以顯示只有幾個項目，而且沒有任何好的方法，以提供圖示。

## <a name="related-links"></a>相關連結

- [第 25 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第 25 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [主版詳細資料頁面](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [索引標籤式的頁面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
