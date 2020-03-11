---
title: 第25章的摘要。 頁面種類
description: 使用 Xamarin 建立 Mobile Apps：第25章的摘要。 頁面種類
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760540"
---
# <a name="summary-of-chapter-25-page-varieties"></a>第25章的摘要。 頁面種類

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

到目前為止，您已看到兩個衍生自 `Page`： `ContentPage` 和 `NavigationPage`的類別。 這一章提供兩個其他專案：

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)管理兩個頁面，主要和詳細資料
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)管理透過索引標籤存取的多個子頁面

這些頁面類型提供比第24章所討論的 `NavagationPage` 更複雜的導覽選項[。頁面導覽](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>主要和詳細資料

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)會定義 `Page`類型的兩個屬性： [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master)和[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)。 一般來說，您會將每個屬性設定為 `ContentPage`。 `MasterDetailPage` 會在這兩個頁面之間顯示和切換。

有兩個基本的方法可以在這兩個頁面之間切換：

- 主要和詳細資料並存的*分割*
- 詳細資料頁面涵蓋或部分涵蓋主版頁面的*popover*

*Popover*方法有數種變化（投影*片*、重*迭*和*交換*），但它們通常是平臺相依的。 您可以將 `MasterDetailPage` 的[`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)屬性設定為[`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior)列舉的成員：

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

不過，此屬性不會對手機造成任何影響。 電話一律會有 popover 的行為。 只有平板電腦和桌面視窗可以有分割行為。

### <a name="exploring-the-behaviors"></a>探索行為

[**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)範例可讓您試驗不同裝置上的預設行為。 此套裝程式含主要和詳細資料的兩個不同 `ContentPage` 衍生專案（同時設定了 `Title` 屬性）和另一個衍生自結合它們之 `MasterDetailPage` 的類別。 詳細資料頁面會包含在 `NavigationPage` 中，因為 UWP 程式不會有任何作用。

Windows 8.1 和 Windows Phone 8.1 平臺需要將點陣圖設定為主版頁面的 `Icon` 屬性。

### <a name="back-to-school"></a>重回學校

[**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)範例會使用稍微不同的方法來設計程式，以顯示[**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫中的學生。

`Master` 和 `Detail` 屬性會使用[SchoolAndDetailPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)中的視覺化樹狀結構（衍生自 `MasterDetailPage`）來定義。 這種相片順序可讓您在主要和詳細資料頁面之間設定資料系結。

該 XAML 檔案也會將 `MasterDetailPage` 的[`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性設定為 `True`。 這會導致主版頁面在啟動時顯示;預設會顯示詳細資料頁面。 當您從主版頁面的 `ListView` 中選取專案時， [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)檔案會將 `IsPresented` 設定為 `false`。 [詳細資料] 頁面隨即顯示：

[![學校和詳細資料的三重螢幕擷取畫面](images/ch25fg09-small.png "MasterDetailPage 的詳細資料頁面")](images/ch25fg09-large.png#lightbox "MasterDetailPage 的詳細資料頁面")

### <a name="your-own-user-interface"></a>您自己的使用者介面

雖然 Xamarin 會提供使用者介面來切換主要和詳細資料檢視，但是您可以提供自己的。 若要這樣做：

- 將 [ [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) ] 屬性設定為 [`false`] 以停用輕刷
- 覆寫[`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton)方法並傳回 `false`，以隱藏 Windows 8.1 和 Windows Phone 8.1 上的工具列按鈕。

接著，您必須提供在主版和詳細資料頁面之間切換的方法，例如[**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)範例所示範的。

[**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)範例會示範在主版和詳細資料頁面上使用 `TapGestureRecognizer` 的另一種方法。

## <a name="tabbedpage"></a>TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)是頁面的集合，您可以使用索引標籤在其間切換。 它衍生自 `MultiPage<Page>`，而且不會定義其本身的公用屬性或方法。 不過， [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)會定義屬性：

- `IList<T>` 類型的[`Children`](xref:Xamarin.Forms.MultiPage`1.Children)屬性

您會在此 `Children` 集合中填入頁面物件。

另一種方法可讓您使用這兩個屬性（自動產生索引標籤式頁面），來定義 `TabbedPage` 子系，就像 `ListView` 一樣：

- 類型為 [ 的 `ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)`IEnumerable`
- 類型為 [ 的 `ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)`DataTemplate`

不過，當集合包含多個專案時，此方法不會在 iOS 上正常運作。

`MultiPage<T>` 會定義兩個屬性，讓您能夠追蹤目前觀看的頁面：

- 類型 `T`的[`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) ，參考頁面
- 類型 `Object`的[`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) ，參考 `ItemsSource` 集合中的物件

`MultiPage<T>` 也會定義兩個事件：

- `ItemsSource` 集合變更時[`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)
- 當已查看的頁面變更時[`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)

### <a name="discrete-tab-pages"></a>離散索引標籤頁

[**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)範例包含三個索引標籤式頁面，會以三種不同的方式顯示色彩。 每個索引標籤都是 `ContentPage` 的衍生，而 `TabbedPage` 的衍生[DiscreteTabbedColorsPage。 xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)結合了這三個頁面。

針對出現在 `TabbedPage`中的每個頁面，必須要有 `Title` 屬性，才能在索引標籤中指定文字，而 Apple Store 也需要使用圖示，因此會針對 iOS 設定 `Icon` 屬性：

[![離散索引標籤式色彩的三向螢幕擷取畫面](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)範例有一個首頁，其中列出所有學生。 當學生按下時，會流覽至 `TabbedPage` 的衍生[`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)，將三個 `ContentPage` 物件併入其視覺化樹狀結構中，其中一個可輸入該學生的一些附注。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)範例會使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫中的[`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別。 [MultiTabbedColorsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)會將 `TabbedPage` 的 `DataTemplate` 屬性設定為以 `ContentPage` 為開頭的視覺化樹狀結構，其中包含 `NamedColor` 屬性的系結（包括 `Title` 屬性的系結）。

不過，這在 iOS 上會有問題。 只有幾個專案可以顯示，而且沒有適當的方式可以提供圖示。

## <a name="related-links"></a>相關連結

- [第25章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第25章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [主版-詳細資料頁面](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [索引標籤式頁面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
