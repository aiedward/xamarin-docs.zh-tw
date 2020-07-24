---
title: :::no-loc(Xamarin.Forms):::頁面
description: ':::no-loc(Xamarin.Forms):::頁面代表跨平臺行動應用程式畫面。 本文列出包含在中的頁面 :::no-loc(Xamarin.Forms)::: 。'
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: ca7e98e4e955ac45a3813a3c1cde97ab0939853a
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997263"
---
# <a name="no-locxamarinforms-pages"></a>:::no-loc(Xamarin.Forms):::頁面

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_:::no-loc(Xamarin.Forms):::頁面代表跨平臺行動應用程式畫面。_

以下所述的所有頁面類型都衍生自 :::no-loc(Xamarin.Forms)::: [`Page`](xref::::no-loc(Xamarin.Forms):::.Page) 類別。 這些視覺元素會佔用所有或大部分的畫面。 `Page`物件代表 `ViewController` iOS 中的和 `Page` 通用 Windows 平臺中的。 在 Android 上，每個頁面都會佔用像這樣的畫面 `Activity` ，但 :::no-loc(Xamarin.Forms)::: 頁面並*不*是 `Activity` 物件。

[![：：： no-loc （Xamarin）：：：頁面類型](pages-images/pages-sml.png)](pages-images/pages.png#lightbox "：：： no-loc （Xamarin）：：：頁面類型")

## <a name="pages"></a>頁面

:::no-loc(Xamarin.Forms):::支援下列頁面類型：

| 類型 | 描述 | 外觀 |
| --- | --- | --- |
| `ContentPage` | [`ContentPage`](xref::::no-loc(Xamarin.Forms):::.ContentPage)是最簡單且最常見的頁面類型。 將 [`Content`](xref::::no-loc(Xamarin.Forms):::.ContentPage.Content) 屬性設定為單一 [`View`](views.md) 物件，其最常為 [`Layout`](layouts.md) [`StackLayout`](xref::::no-loc(Xamarin.Forms):::.StackLayout) 、 [`Grid`](xref::::no-loc(Xamarin.Forms):::.Grid) 或 [`ScrollView`](xref::::no-loc(Xamarin.Forms):::.ScrollView) 。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.ContentPage) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
| `MasterDetailPage` | 會 [`MasterDetailPage`](xref::::no-loc(Xamarin.Forms):::.MasterDetailPage) 管理兩個資訊窗格。 將 [`Master`](xref::::no-loc(Xamarin.Forms):::.MasterDetailPage.Master) 屬性設定為通常會顯示清單或功能表的頁面。 將 [`Detail`](xref::::no-loc(Xamarin.Forms):::.MasterDetailPage.Detail) 屬性設定為頁面，其中會顯示主版頁面中選取的專案。 [`IsPresented`](xref::::no-loc(Xamarin.Forms):::.MasterDetailPage.IsPresented)屬性會控制主要或詳細資料頁面是否為可見。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.MasterDetailPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) |
| `NavigationPage` | 會 [`NavigationPage`](xref::::no-loc(Xamarin.Forms):::.NavigationPage) 使用以堆疊為基礎的架構來管理其他頁面中的導覽。 在您的應用程式中使用頁面導覽時，應該將首頁的實例傳遞至物件的函式 `NavigationPage` 。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.NavigationPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  的 c # 程式碼具有程式[代碼 = 後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) |
| `TabbedPage` | [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage)衍生自抽象 [`MultiPage`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1) 類，並允許使用索引標籤在子頁面之間流覽。 將 [`Children`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.Children) 屬性設定為頁面的集合，或將屬性設 [`ItemsSource`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.ItemsSource) 為數據物件的集合，並將屬性設定為 [`ItemTemplate`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.ItemTemplate) [`DataTemplate`](xref::::no-loc(Xamarin.Forms):::.DataTemplate) 描述如何以視覺化方式呈現每個物件的。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.TabbedPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
| `CarouselPage` | [`CarouselPage`](xref::::no-loc(Xamarin.Forms):::.CarouselPage)衍生自抽象 [`MultiPage`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1) 類，並允許透過手指輕觸在子頁面之間流覽。 將 [`Children`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.Children) 屬性設定為物件的集合 [`ContentPage`](xref::::no-loc(Xamarin.Forms):::.ContentPage) ，或將屬性設 [`ItemsSource`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.ItemsSource) 為數據物件的集合，並將 [`ItemTemplate`](xref::::no-loc(Xamarin.Forms):::.MultiPage`1.ItemTemplate) 屬性設定為， [`DataTemplate`](xref::::no-loc(Xamarin.Forms):::.DataTemplate) 描述如何以視覺化方式呈現每個物件。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.CarouselPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
| `TemplatedPage` | [`TemplatedPage`](xref::::no-loc(Xamarin.Forms):::.TemplatedPage)使用控制項範本顯示全螢幕內容，而是的基類 [`ContentPage`](xref::::no-loc(Xamarin.Forms):::.ContentPage) 。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.TemplatedPage)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |     |

## <a name="related-links"></a>相關連結

- [:::no-loc(Xamarin.Forms):::FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [:::no-loc(Xamarin.Forms):::範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=:::no-loc(Xamarin.Forms):::)
- [:::no-loc(Xamarin.Forms):::API 檔](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
