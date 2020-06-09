---
標題：「 Xamarin.Forms 頁面」描述：「 Xamarin.Forms 頁面代表跨平臺行動應用程式畫面。 本文列出包含在中的頁面。」 Xamarin.Forms
ms-chap： xamarin assetid： 9C8C710F-E312-420B-9324-A7A20CEDB7EC ms. 技術： xamarin-表單作者： davidbritch ms-chap： dabritch ms. date： 01/12/2016 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-pages"></a>Xamarin.Forms頁面

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_[Xamarin] 代表跨平臺行動應用程式畫面。_

以下所述的所有頁面類型都衍生自 Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) 類別。 這些視覺元素會佔用所有或大部分的畫面。 `Page`物件代表 `ViewController` iOS 中的和 `Page` 通用 Windows 平臺中的。 在 Android 上，每個頁面都會佔用像這樣的畫面 `Activity` ，但 Xamarin.Forms 頁面並*不*是 `Activity` 物件。

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin.Forms支援下列頁面類型：

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)是最簡單且最常見的頁面類型。 將 [`Content`](xref:Xamarin.Forms.ContentPage.Content) 屬性設定為單一 [`View`](views.md) 物件，其最常為 [`Layout`](layouts.md) [`StackLayout`](layouts.md#stacklayout) 、 [`Grid`](layouts.md#grid) 或 [`ScrollView`](layouts.md#scrollview) 。<br /><br />[API 文件](xref:Xamarin.Forms.ContentPage) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| 會 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理兩個資訊窗格。 將 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 屬性設定為通常會顯示清單或功能表的頁面。 將 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性設定為頁面，其中會顯示主版頁面中選取的專案。 [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性會控制主要或詳細資料頁面是否為可見。<br /><br />[API 檔](xref:Xamarin.Forms.MasterDetailPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| 會 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 使用以堆疊為基礎的架構來管理其他頁面中的導覽。 在您的應用程式中使用頁面導覽時，應該將首頁的實例傳遞至物件的函式 `NavigationPage` 。<br /><br />[API 檔](xref:Xamarin.Forms.NavigationPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  的 c # 程式碼具有程式[代碼 = 後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)衍生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 類，並允許使用索引標籤在子頁面之間流覽。 將 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 屬性設定為頁面的集合，或將屬性設 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 為數據物件的集合，並將屬性設定為 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以視覺化方式呈現每個物件的。<br /><br />[API 檔](xref:Xamarin.Forms.TabbedPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)衍生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 類，並允許透過手指輕觸在子頁面之間流覽。 將 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 屬性設定為物件的集合 [`ContentPage`](#contentpage) ，或將屬性設 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 為數據物件的集合，並將 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性設定為， [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以視覺化方式呈現每個物件。<br /><br />[API 檔](xref:Xamarin.Forms.CarouselPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)使用控制項範本顯示全螢幕內容，而是的基類 [`ContentPage`](#contentpage) 。<br /><br />[API 檔](xref:Xamarin.Forms.TemplatedPage)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.FormsFormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsAPI 檔](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
