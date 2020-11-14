---
title: Xamarin.Forms 頁面
description: Xamarin.Forms 頁面代表跨平臺行動應用程式畫面。 本文列出中包含的頁面 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed59483a1be5537917ca8c53fcbbfbd9040eded8
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590359"
---
# <a name="no-locxamarinforms-pages"></a>Xamarin.Forms 頁面

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Forms 頁面代表跨平臺行動應用程式畫面。_

以下所述的所有頁面類型都是衍生自 Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) 類別。 這些視覺元素會佔據全部或大部分的畫面。 `Page`物件表示 `ViewController` iOS 中的，而 `Page` 通用 Windows 平臺中的。 在 Android 上，每個頁面都會佔用像這樣的畫面 `Activity` ，但 Xamarin.Forms 頁面並 *不* 是 `Activity` 物件。

[![：：：非 loc (Xamarin. Forms) ：：：頁面類型](pages-images/pages-sml.png)](pages-images/pages.png#lightbox "：：：非 loc (Xamarin. Forms) ：：：頁面類型")

## <a name="pages"></a>頁面

Xamarin.Forms 支援下列頁面類型：

| 類型 | 描述 | 外觀 |
| --- | --- | --- |
| `ContentPage` | [`ContentPage`](xref:Xamarin.Forms.ContentPage) 是最簡單且最常見的頁面類型。 將 [`Content`](xref:Xamarin.Forms.ContentPage.Content) 屬性設定為單一 [`View`](views.md) 物件，最常為 [`Layout`](layouts.md) [`StackLayout`](xref:Xamarin.Forms.StackLayout) 、 [`Grid`](xref:Xamarin.Forms.Grid) 或 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。<br /><br />[API 檔](xref:Xamarin.Forms.ContentPage) | [![ContentPage 範例](pages-images/ContentPage.png "ContentPage 範例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
| `MasterDetailPage` | 會 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理兩個資訊窗格。 將 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 屬性設定為通常會顯示清單或功能表的頁面。 將 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性設定為顯示主版頁面所選取專案的頁面。 [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性會控制主要或詳細資料頁面是否可見。<br /><br />[API 檔](xref:Xamarin.Forms.MasterDetailPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [範例](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 範例](pages-images/MasterDetailPage.png "MasterDetailPage 範例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  的 c # 程式碼具有[程式碼後端](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) |
| `NavigationPage` | [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)使用堆疊架構來管理其他頁面之間的導覽。 在您的應用程式中使用頁面流覽時，首頁的實例應該傳遞至物件的函式 `NavigationPage` 。<br /><br />[API 檔](xref:Xamarin.Forms.NavigationPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [範例 1](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 範例](pages-images/NavigationPage.png "NavigationPage 範例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  的 c # 程式碼具有[程式碼後端](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs)的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) |
| `TabbedPage` | [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 衍生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 類，並允許使用索引標籤在子頁面之間進行導覽。 將 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 屬性（property）設定為頁面的集合，或將屬性（property）設定為數據物件的集合，並將屬性（property）設定為，以 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以視覺化方式呈現每個物件。<br /><br />[API 檔](xref:Xamarin.Forms.TabbedPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [範例 1](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 範例](pages-images/TabbedPage.png "TabbedPage 範例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
| `CarouselPage` | [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 衍生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 類，並允許透過手指輕觸的子頁面之間進行導覽。 將 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 屬性（property）設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件的集合，或將屬性（property）設定為數據物件的集合，並將屬性（property）設定為，以 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以視覺化方式呈現每個物件。<br /><br />[API 檔](xref:Xamarin.Forms.CarouselPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [範例 1](/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 範例](pages-images/CarouselPage.png "CarouselPage 範例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
| `TemplatedPage` | [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) 以控制項範本顯示全螢幕內容，而且是的基類 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。<br /><br />[API 檔](xref:Xamarin.Forms.TemplatedPage)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedPage 範例](pages-images/TemplatedPage.png "TemplatedPage 範例")](pages-images/TemplatedPage.png "TemplatedPage 範例") |
|     |     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 檔](/dotnet/api/xamarin.forms?view=xamarin-forms)
