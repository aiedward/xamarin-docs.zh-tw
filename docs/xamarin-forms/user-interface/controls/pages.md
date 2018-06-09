---
title: Xamarin.Forms 頁面
description: Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 本文列出隨附 Xamarin.Forms 中的頁面。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: f4b8d895c6c9c7f47b8af8e56ad60a5a453e2d56
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243154"
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms 頁面

_Xamarin.Forms 頁面代表跨平台行動應用程式畫面。_

以下說明的所有頁面類型都衍生自 Xamarin.Forms [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)類別。 這些視覺項目所佔用的所有或大部分的螢幕。 A`Page`物件代表`ViewController`在 iOS 和`Page`在通用 Windows 平台。 在 Android 上，每一頁會佔用像畫面`Activity`，但 Xamarin.Forms 頁會使用*不*`Activity`物件。

[ ![](pages-images/pages-sml.png "Xamarin.Forms 頁面類型")](pages-images/pages.png#lightbox "Xamarin.Forms 頁面類型")

## <a name="pages"></a>頁面

Xamarin.Forms 可支援下列的頁面類型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 是最簡單且最常見類型。 設定[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentPage.Content/)屬性至單一[ `View` ](views.md)物件，它是最常[ `Layout` ](layouts.md)例如[ `StackLayout`](layouts.md#stackLayout)， [ `Grid` ](layouts.md#grid)，或[ `ScrollView` ](layouts.md#scrollView)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)管理兩個窗格的資訊。 設定[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)通常顯示清單或功能表頁面的屬性。 設定[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)畫面，顯示選取的項目，從主版頁面的屬性。 [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)屬性控制是否會顯示與 master 或詳細資料頁面。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)管理使用堆疊為基礎的架構的其他頁面之間巡覽。 在首頁上的執行個體時使用網頁瀏覽應用程式中，應該傳遞給建構函式`NavigationPage`物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)， [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)，和[3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml)與[程式碼後置 =](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 衍生自抽象[ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)類別，並允許在子系之間瀏覽頁面使用索引標籤。 設定[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/)屬性集合的頁面或組[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/)資料物件的集合的屬性和[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/)屬性為[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)描述如何以視覺方式表示每個物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) 衍生自抽象[ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)類別，並允許在子系之間瀏覽頁面透過手指撥動。 設定[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/)屬性集合的[ `ContentPage` ](#contentPage)物件或一組[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/)資料物件的集合的屬性，[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/)屬性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)描述如何以視覺方式表示每個物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) 顯示全螢幕控制項範本的內容，是基底類別[ `ContentPage` ](#contentPage)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/root/Xamarin.Forms/)
