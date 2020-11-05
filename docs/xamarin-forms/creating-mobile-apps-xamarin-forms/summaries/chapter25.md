---
title: 第25章的摘要。 頁面變化
description: 建立 Mobile Apps Xamarin.Forms ：第25章的摘要。 頁面變化
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05b7821a08140dda411842c5f978be2027fcc033
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373966"
---
# <a name="summary-of-chapter-25-page-varieties"></a>第25章的摘要。 頁面變化

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

到目前為止，您已經看過兩個衍生自的類別 `Page` ： `ContentPage` 和 `NavigationPage` 。 這一章提供其他兩個：

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理兩頁、主要和詳細資料
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 管理透過索引標籤存取的多個子頁面

這些頁面類型提供比第 `NavagationPage` 24 章討論更精密的導覽選項 [。頁面流覽](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>主要和詳細資料

會 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 定義類型為的兩個屬性 `Page` ： [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 。 一般來說，您會將每個屬性都設定為 `ContentPage` 。 會 `MasterDetailPage` 顯示並切換這兩個頁面。

有兩個基本的方法可以在這兩個頁面之間切換：

- *分割* 主要和詳細資料並存的位置
- 詳細資料頁面涵蓋或部分涵蓋主版頁面的 *popover*

*Popover* 方法有幾種變化 (投影 *片* 、重 *迭* 和 *交換* ) ，但是這些通常是平臺相依的。 您可以將的 [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 屬性設定 `MasterDetailPage` 為列舉的成員 [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) ：

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

不過，這個屬性不會影響手機。 手機一律具有 popover 行為。 只有平板電腦和桌面視窗可以有分割行為。

### <a name="exploring-the-behaviors"></a>探索行為

[**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)範例可讓您在不同裝置上實驗預設行為。 此套裝程式含主要和詳細 (的兩個不同衍生專案， `ContentPage` 並 `Title` 在兩個) 上設定屬性，而另一個衍生自的類別則會 `MasterDetailPage` 結合它們。 詳細資料頁面會包含在中， `NavigationPage` 因為 UWP 程式不會運作。

Windows 8.1 和 Windows Phone 8.1 平臺需要將點陣圖設定為 `Icon` 主版頁面的屬性。

### <a name="back-to-school"></a>重回學校

[**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)範例會採用稍微不同的方法來建立程式，以顯示 [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)文件庫中的學生。

`Master`和 `Detail` 屬性會以[SchoolAndDetailPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)中的視覺化樹狀結構定義，其衍生自 `MasterDetailPage` 。 這種相片順序可讓您在主版和詳細資料頁面之間設定資料系結。

該 XAML 檔案也會將的 [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) 屬性設定 `MasterDetailPage` 為 `True` 。 這會導致主版頁面在啟動時顯示;依預設會顯示詳細資料頁面。 當[SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)您 `IsPresented` `false` `ListView` 在主版頁面中選取專案時，SchoolAndDetailPage.xaml.cs 檔會將設定為。 接著會顯示詳細資料頁面：

[![學校和詳細資料的三重螢幕擷取畫面](images/ch25fg09-small.png "來自 MasterDetailPage 的詳細資料頁面")](images/ch25fg09-large.png#lightbox "來自 MasterDetailPage 的詳細資料頁面")

### <a name="your-own-user-interface"></a>您自己的使用者介面

雖然 Xamarin.Forms 提供使用者介面，以便在主要和詳細資料檢視之間切換，但您可以提供自己的介面。 若要這樣做：

- 將 [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) 屬性設定為 `false` 以停用輕刷
- 覆寫 [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) 方法並返回， `false` 以隱藏 Windows 8.1 和 Windows Phone 8.1 上的工具列按鈕。

接著，您必須提供在主版和詳細資料頁面之間切換的方法，如 [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) 範例所示範。

[**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)範例會示範 `TapGestureRecognizer` 在主版和詳細資料頁面上使用的另一種方法。

## <a name="tabbedpage"></a>TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)是頁面的集合，您可以在使用索引標籤之間切換。 它衍生自 `MultiPage<Page>` ，並且不會定義其本身的公用屬性或方法。 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)但是，會定義屬性：

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 型別的屬性 `IList<T>`

您可以使用頁面物件來填滿此 `Children` 集合。

另一種方法可讓您定義子系，就 `TabbedPage` 像是 `ListView` 使用這兩個屬性自動產生索引標籤式頁面一樣：

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 型別為 `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 型別為 `DataTemplate`

不過，當集合包含一個以上的專案時，此方法不會在 iOS 上運作正常。

`MultiPage<T>` 定義兩個屬性，可讓您追蹤目前正在查看的頁面：

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)參考頁面的型別 `T`
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)類型的 `Object` ，參考集合中的物件。 `ItemsSource`

`MultiPage<T>` 也會定義兩個事件：

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) 當 `ItemsSource` 集合變更時
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) 當觀看的頁面變更時

### <a name="discrete-tab-pages"></a>離散索引標籤頁面

[**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)範例包含三個索引標籤式頁面，這些頁面會以三種不同的方式來顯示色彩。 每個索引標籤都是 `ContentPage` 衍生的，然後 `TabbedPage` 衍生 [DiscreteTabbedColorsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) 會結合三個頁面。

針對出現在中的每個頁面 `TabbedPage` ， `Title` 必須要有屬性才能指定索引標籤中的文字，而且 Apple Store 也需要使用圖示，才能為 `Icon` iOS 設定屬性：

[![離散索引標籤式色彩的三重螢幕擷取畫面](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)範例有一個首頁，其中會列出所有學生。 當學生進行點擊時，這會流覽至 `TabbedPage` 衍生的， [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml) `ContentPage` 在其視覺化樹狀結構中併入三個物件，其中一個可讓您輸入該學生的一些筆記。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)範例會使用 [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) [**Xamarin.Forms 書本套件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別。 [MultiTabbedColorsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)會將的 `DataTemplate` 屬性設定為開頭為的 `TabbedPage` 視覺化樹狀結構，其中 `ContentPage` 包含與 `NamedColor` `Title` 屬性) 系結的 (的屬性系結。

不過，這在 iOS 上是有問題的。 只有幾個專案可顯示，而且沒有提供圖示的好方法。

## <a name="related-links"></a>相關連結

- [第25章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第25章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [主版詳細資料頁面](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [索引標籤式頁面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
