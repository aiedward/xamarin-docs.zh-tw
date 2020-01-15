---
title: Xamarin. 表單頁面
description: '[Xamarin] 代表跨平臺行動應用程式畫面。 本文列出包含在 Xamarin 中的頁面。'
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 101b7aa014064e07de69e8c6b12f0ad1000ae0dd
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955744"
---
# <a name="xamarinforms-pages"></a>Xamarin. 表單頁面

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_[Xamarin] 代表跨平臺行動應用程式畫面。_

以下所述的所有頁面類型都衍生自[`Page`](xref:Xamarin.Forms.Page)類別的 Xamarin。 這些視覺元素會佔用所有或大部分的畫面。 `Page` 物件代表 iOS 中的 `ViewController` 和通用 Windows 平臺中的 `Page`。 在 Android 上，每個頁面都會佔用畫面，例如 `Activity`，但是 Xamarin. 表單頁面並*不*是 `Activity` 物件。

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>頁面

Xamarin 支援下列頁面類型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)是最簡單且最常見的頁面類型。 將 [ [`Content`](xref:Xamarin.Forms.ContentPage.Content) ] 屬性設定為單一[`View`](views.md)物件，這通常是[`StackLayout`](layouts.md#stackLayout)、 [`Grid`](layouts.md#grid)或[`ScrollView`](layouts.md#scrollView)等[`Layout`](layouts.md) 。<br /><br />[API 文件](xref:Xamarin.Forms.ContentPage) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)會管理兩個資訊窗格。 將 [ [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) ] 屬性設定為通常會顯示清單或功能表的頁面。 將 [ [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) ] 屬性設定為頁面，其中顯示主版頁面中選取的專案。 [ [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) ] 屬性會控制是否顯示主版或詳細資料頁面。<br /><br />[API 檔](xref:Xamarin.Forms.MasterDetailPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />此頁面的程式碼 / 包含[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)使用以堆疊為基礎的架構來管理其他頁面間的導覽。 在您的應用程式中使用頁面導覽時，首頁的實例應該傳遞至 `NavigationPage` 物件的函式。<br /><br />[API 檔](xref:Xamarin.Forms.NavigationPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml)和[程式碼 = 後置 ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)衍生自抽象[`MultiPage`](xref:Xamarin.Forms.MultiPage`1)類別，並允許使用索引標籤在子頁面之間流覽。 將 [ [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) ] 屬性設定為頁面的集合，或將 [ [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) ] 屬性設為數據物件的集合，並將 [ [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) ] 屬性設定為描述如何以視覺化方式呈現每個物件的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。<br /><br />[API 檔](xref:Xamarin.Forms.TabbedPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)衍生自抽象[`MultiPage`](xref:Xamarin.Forms.MultiPage`1)類別，並允許透過手指輕觸在子頁面之間流覽。 將 [ [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) ] 屬性設為[`ContentPage`](#contentPage)物件的集合，或將[`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)屬性設定為數據物件的集合，並將[`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性設為描述如何以視覺化方式呈現每個物件的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。<br /><br />[API 檔](xref:Xamarin.Forms.CarouselPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)會以控制項範本顯示全螢幕內容，而是[`ContentPage`](#contentPage)的基類。<br /><br />[API 檔](xref:Xamarin.Forms.TemplatedPage) / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
