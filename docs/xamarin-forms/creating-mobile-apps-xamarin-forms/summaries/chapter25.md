---
title: 第25章摘要。 頁面變化
description: 使用 Xamarin.表單創建行動應用程式:第 25 章摘要。 頁面變化
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760540"
---
# <a name="summary-of-chapter-25-page-varieties"></a>第25章摘要。 頁面變化

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

到目前為止,您已經看到兩個來自 的`Page`類`ContentPage`派`NavigationPage`生 自 : 和 。 本章介紹另外兩章:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)管理兩頁,一個母版和一個細節
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)管理通過選項卡存取的多個子頁

與第 24 章中討論`NavagationPage`的頁面 類型相比,這些頁面類型提供了更複雜的導航選項[。頁面導覽](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)

## <a name="master-and-detail"></a>母版和詳細資訊

定義[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)類型`Page`[`Master`](xref:Xamarin.Forms.MasterDetailPage.Master):[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)與的兩個屬性。 通常,將每個屬性設定為`ContentPage`。 這`MasterDetailPage`兩個頁面之間的顯示和切換。

在這兩頁之間切換有兩種基本方法:

- *分割*與細節並排的位置
- 詳細資訊頁覆蓋或部分覆寫母版頁的*彈出器*

*彈出方法*(*幻燈片*、*重疊*和*交換*)有幾個變體,但這些變體通常依賴於平臺。 您可以將 的[`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)`MasterDetailPage`屬性 設定為枚舉[`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior)的成員 :

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

但是,此屬性對手機沒有影響。 手機總是有彈出行為。 只有平板電腦和桌面視窗可以具有拆分行為。

### <a name="exploring-the-behaviors"></a>探索行為

[**MasterDetail行為**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)範例允許您嘗試不同裝置上的默認行為。 程式包含兩個單獨的`ContentPage`母版和細節導數(兩者都設置`Title`了 屬性),另一個類派`MasterDetailPage`生自 它們。 詳細資訊頁包含在 中`NavigationPage`, 因為沒有 UWP 程式將無法工作。

Windows 8.1 和 Windows Phone 8.1 平臺要求`Icon`將位圖設置為母 版頁的屬性。

### <a name="back-to-school"></a>重回學校

[**"學校與細節"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)範例採用一種略有不同的方法構建程式,以顯示來自[**學校美術**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)圖書館的學生。

和`Master``Detail`屬性使用從`MasterDetailPage`派生的[SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)檔中的視覺樹定義。 這種排列允許在主頁和詳細資訊頁之間設置數據綁定。

這個 XAML[`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)檔案中`MasterDetailPage`設定`True`的屬性設定到 。 這將導致母版頁在啟動時顯示;因此,在啟動時,母版頁將顯示出來。預設情況下,將顯示詳細資訊頁。 [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)檔`IsPresented`集`false`從母`ListView`版頁 中選擇項時。 然後顯示詳細資訊頁:

[![學校和細節的三重截圖](images/ch25fg09-small.png "從母版詳細資訊頁面的詳細資訊頁面")](images/ch25fg09-large.png#lightbox "從母版詳細資訊頁面的詳細資訊頁面")

### <a name="your-own-user-interface"></a>您自己的使用者介面

儘管 Xamarin.Forms 提供了一個使用者介面,用於在主檢視和詳細資訊視圖之間切換,但您可以提供您自己的使用者介面。 若要這樣做：

- 將[`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled)屬性設定`false`為關閉輕掃
- 重寫該方法[`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton)並`false`返回 以隱藏 Windows 8.1 和 Windows Phone 8.1 上的工具列按鈕。

然後,必須提供在母版頁和詳細資訊頁之間切換的方法,例如[**「顏色詳細資訊」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)範例演示。

[**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)範例示範使用主`TapGestureRecognizer`版和詳細資訊頁上的另一種方法。

## <a name="tabbedpage"></a>TabbedPage

是[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)可以使用選項卡在其中切換的頁面的集合。 它派生自`MultiPage<Page>`並定義其自身的公共屬性或方法。 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)但是,定義屬性:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children)類型的屬性`IList<T>`

使用頁面物件`Children`填充此集合。

另一種方法允許您定義`TabbedPage`子級,就像使用這`ListView`兩個屬性自動產生選項卡式頁面一樣:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)類型`IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)類型`DataTemplate`

但是,當集合包含多個項時,此方法在 iOS 上不起作用。

`MultiPage<T>`定義另外兩個屬性,以便追蹤目前檢視的頁面:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)型`T`態 ,參考頁面
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)型`Object`態 ,引用`ItemsSource`集合中的物件

`MultiPage<T>`還定義了兩個事件:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)當`ItemsSource`集合變更時
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)檢視的頁面變更時

### <a name="discrete-tab-pages"></a>離散選項卡頁

[**離散標籤顏色**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)示例由三個選項卡式頁面組成,這些頁面以三種不同的方式顯示顏色。 每個選項卡都是一`ContentPage`個衍生物,`TabbedPage`然後 衍生的[離散標籤顏色頁面.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)合併了三個頁面。

對於中顯示的`TabbedPage``Title`每個頁面,屬性都需要在選項卡中指定文本,並且 Apple 應用商店也要求使用圖示`Icon`,因此該 屬性設置為 iOS:

[![離散標籤顏色的三重螢幕截圖](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[**"學生筆記"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)範例有一個主頁,其中列出了所有學生。 當一個學生被點擊時,這將導航到一`TabbedPage`個[`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)衍生 物,該`ContentPage`導數 在其可視化樹中包含三個物件,其中一個允許為該學生輸入一些註釋。

### <a name="using-an-itemtemplate"></a>使用項目樣本

[**多選項卡顏色**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)示例[`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)使用[**Xamarin.FormsBook.工具包**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類。 [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)檔`DataTemplate`將`TabbedPage`的屬性 設置到可`ContentPage`視化樹 ,開`NamedColor`頭包含對 屬性`Title`(包括對屬性的 綁定)的綁定。

但是,這在 iOS 上存在問題。 只能顯示幾個項目,並且沒有好的方法來給他們圖示。

## <a name="related-links"></a>相關連結

- [第25章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第25章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [母版詳細資訊頁面](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [選項卡式頁面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
