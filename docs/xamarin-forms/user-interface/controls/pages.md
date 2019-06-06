---
title: Xamarin.Forms 頁面
description: Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 這篇文章會列出包含在 Xamarin.Forms 中的頁面。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 42cbc6f50da34cbbc594dfef1dec49619202c191
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741074"
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms 頁面

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/FormsGallery/)

_Xamarin.Forms 頁面代表跨平台行動應用程式畫面。_

如下所述的所有頁面類型都衍生自 Xamarin.Forms [ `Page` ](xref:Xamarin.Forms.Page)類別。 這些視覺項目所佔用的所有或大部分的螢幕。 A`Page`物件代表`ViewController`在 iOS 和`Page`在通用 Windows 平台。 在 Android 上，每一頁會佔用所示的畫面`Activity`，但 Xamarin.Forms 頁面*不*`Activity`物件。

[ ![](pages-images/pages-sml.png "Xamarin.Forms 頁面型別")](pages-images/pages.png#lightbox "Xamarin.Forms 頁面類型")

## <a name="pages"></a>頁面

Xamarin.Forms 可支援下列的頁面類型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) 是最簡單且最常見的類型 頁面。 設定[ `Content` ](xref:Xamarin.Forms.ContentPage.Content)屬性，以單一[ `View` ](views.md)物件，這是最常[ `Layout` ](layouts.md)例如[ `StackLayout`](layouts.md#stackLayout)， [ `Grid` ](layouts.md#grid)，或[ `ScrollView` ](layouts.md#scrollView)。<br /><br />[API 文件](xref:Xamarin.Forms.ContentPage) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| A [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)管理兩個窗格的資訊。 設定[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)屬性頁面，通常顯示的清單或功能表。 設定[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)畫面，顯示選取的項目，從主版頁面的屬性。 [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性會管理 master] 或 [詳細資料頁面是否可見。<br /><br />[API 文件](xref:Xamarin.Forms.MasterDetailPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)管理使用以堆疊為主的架構的其他頁面之間巡覽。 首頁上的執行個體中使用時頁面瀏覽您的應用程式，應傳遞至建構函式的`NavigationPage`物件。<br /><br />[API 文件](xref:Xamarin.Forms.NavigationPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)， [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)，以及[3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml)使用[程式碼後置 =](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 衍生自抽象[ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1)類別，並允許在子系之間的巡覽頁面使用索引標籤。 設定[ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children)屬性頁面或設定集合[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)屬性的資料物件集合和[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性，以[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述每個物件的視覺化方式表示的方式。<br /><br />[API 文件](xref:Xamarin.Forms.TabbedPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 衍生自抽象[ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1)類別，並可讓瀏覽子逐頁手指撥動。 設定[ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children)屬性的集合[ `ContentPage` ](#contentPage)物件或一組[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)資料物件集合的屬性，[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述每個物件的視覺化方式表示的方式。<br /><br />[API 文件](xref:Xamarin.Forms.CarouselPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) 顯示使用控制項範本的全螢幕內容，並是基底類別[ `ContentPage` ](#contentPage)。<br /><br />[API 文件](xref:Xamarin.Forms.TemplatedPage) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
