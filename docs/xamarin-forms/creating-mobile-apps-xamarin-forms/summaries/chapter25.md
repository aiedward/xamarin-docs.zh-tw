---
title: "第 25 章摘要。 頁面須"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: bbe960357d9180df90a4423d6acfdf3f869d1b77
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-25-page-varieties"></a>第 25 章摘要。 頁面須

到目前為止所見兩個類別衍生自`Page`:`ContentPage`和`NavigationPage`。 本章提供其他兩個：

- [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) 管理兩個頁面、 主圖形和詳細資料
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 管理多個存取透過索引標籤的子頁面

這些頁面型別提供更趨精密完美的巡覽選項，比`NavagationPage`述[章 24。頁面瀏覽](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>Master 和詳細資料

[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)定義兩個屬性的型別`Page`: [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)。 通常您會設定這些內容的每個`ContentPage`。 `MasterDetailPage`顯示，並切換這兩個分頁。

有兩個基本切換這兩個分頁的方式：

- *分割*master 和詳細資料的並排顯示
- *popover*詳細資料頁面涵蓋或部分內容涵蓋了主要位置頁面

有數個變化的*popover*方法 (*投影片*，*重疊*，和*交換*)，但這些通常是平台相依。 您可以設定[ `MasterDetailBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)屬性`MasterDetailPage`成員[ `MasterBehavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterBehavior/)列舉型別：

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Default/)
- [`Split`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Split/)
- [`SplitOnLandscape`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnLandscape/)
- [`SplitOnPortrait`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnPortrait/)
- [`Popover`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Popover/)

不過，此屬性沒有在手機上的任何作用。 電話一定 popover 行為。 只有平板電腦和桌上型電腦的 windows 可以有分割的行為。

### <a name="exploring-the-behaviors"></a>瀏覽行為

[ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)範例可讓您試驗不同的裝置上的預設行為。 程式包含兩個不同`ContentPage`衍生項目之 master 和詳細資料 (與`Title`屬性上設定)，以及衍生自另一個類別`MasterDetailPage`，將它們合併。 詳細資料頁面住`NavigationPage`因為 UWP 程式不會運作。

Windows 8.1 和 Windows Phone 8.1 的平台需要點陣圖設為`Icon`主版頁面的屬性。

### <a name="back-to-school"></a>回學校

[ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)範例採用稍有不同的方法來建構以顯示從學生程式[ **SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫。

`Master`和`Detail`屬性，會使用視覺化樹狀結構中定義[SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)檔案，衍生自`MasterDetailPage`。 這種安排可讓資料繫結設定成介於 master] 和 [詳細資料頁面。

XAML 檔案也設定[ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)屬性`MasterDetailPage`至`True`。 這會導致要顯示在啟動; 主版頁面根據預設，會顯示詳細資料頁面。 [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)檔案集`IsPresented`至`false`從選取項目時`ListView`主版頁面中。 這時會顯示詳細資料頁面：

[![三個螢幕擷取畫面的學校和詳細資料](images/ch25fg09-small.png "詳細資料頁面從 MasterDetailPage")](images/ch25fg09-large.png "MasterDetailPage 從詳細資料頁面")

### <a name="your-own-user-interface"></a>使用者介面

Xamarin.Forms 提供使用者介面，用於 master 和詳細資料檢視之間切換，雖然您可以提供您自己。 若要這樣做：

- 設定[ `IsGestureEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsGestureEnabled/)屬性`false`撥動停用
- 覆寫[ `ShouldShowToolbarButton` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton()/)方法，並傳回`false`隱藏 Windows 8.1 和 Windows Phone 8.1 上的工具列按鈕。

您必須提供方法來切換主要和詳細資料頁面中，例如所示範[ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)範例。

[ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)範例示範另一個方法使用`TapGestureRecognizer`master 和詳細資料頁面上。

## <a name="tabbedpage"></a>TabbedPage

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)是您可以使用索引標籤之間切換的分頁的集合。 它衍生自`MultiPage<Page>`並定義任何公用屬性或它自己的方法。 [`MultiPage<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)不過，未定義屬性：

- [`Children`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) 類型的屬性 `IList<T>`

填滿這個`Children`的頁面物件的集合。

另一種方法可讓您定義`TabbedPage`子系十分類似`ListView`使用自動產生索引標籤式的頁面這兩個屬性：

- [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) 型別 `IEnumerable`
- [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) 型別 `DataTemplate`

不過，這種方法不適用於 iOS 集合包含多個項目時。

`MultiPage<T>` 定義另外兩個屬性，可讓您追蹤目前檢視的頁面：

- [`CurrentPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.CurrentPage/) 型別的`T`、 參考至頁面
- [`SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.SelectedItem/) 型別的`Object`、 參考中的物件至`ItemsSource`集合

`MultiPage<T>` 也會定義兩個事件：

- [`PagesChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.PagesChanged/) 當`ItemsSource`集合變更
- [`CurrentPageChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.CurrentPageChanged/) 當變更檢視的頁面

### <a name="discrete-tab-pages"></a>離散的索引標籤頁

[ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)範例包含三個不同的方式顯示色彩的三個索引標籤式頁面。 每個索引標籤`ContentPage`衍生項目，然後`TabbedPage`衍生物[DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)結合三個頁面。

會出現在每個頁面`TabbedPage`、`Title`屬性是否需要在索引標籤中指定的文字和 Apple Store 需要圖示可用，所以`Icon`屬性會設為 iOS:

[![個別索引標籤式色彩的三個螢幕擷取畫面](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png "TabbedPage")

[ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)範例已列出所有學生的首頁。 當一位學生點選時，這會巡覽至`TabbedPage`衍生作品， [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)，可結合三個`ContentPage`物件在其視覺化樹狀結構，其中可讓您輸入該學生的注意事項。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)範例會使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)檔案集`DataTemplate`屬性`TabbedPage`至視覺化樹狀結構開頭的`ContentPage`包含之屬性的繫結`NamedColor`（包括的繫結`Title`屬性)。

不過，這是在 iOS 上有問題。 可以顯示只有少數的項目，而且沒有任何好的方法，讓它們有圖示。



## <a name="related-links"></a>相關連結

- [第 25 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第 25 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [主版詳細資料頁面](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [索引標籤式的頁面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
