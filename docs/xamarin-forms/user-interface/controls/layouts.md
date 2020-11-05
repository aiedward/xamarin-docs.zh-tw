---
title: Xamarin.Forms 佈局
description: Xamarin.Forms 版面配置可用來將使用者介面控制群組合成視覺化結構。 本文列出中包含的版面配置 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d64f1919bebc1988753a7b6d37c8d31fcb6d84e3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373246"
---
# <a name="no-locxamarinforms-layouts"></a>Xamarin.Forms 佈局

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 配置可用來將使用者介面控制項撰寫成視覺化結構。_

[`Layout`](xref:Xamarin.Forms.Layout)中的和 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別 Xamarin.Forms 為 views 的特製化子類型，可作為視圖和其他配置的容器。 `Layout`類別本身衍生自 [`View`](views.md) 。 `Layout`導數通常包含邏輯來設定應用程式中子項目的位置和大小 Xamarin.Forms 。

[![：：：非 loc (Xamarin. Forms) ：：： Layout 類型](layouts-images/layouts-sml.png "：：：非 loc (Xamarin. Forms) ：：： Layout 類型")](layouts-images/layouts.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： Layout 類型")

衍生自的類別 `Layout` 可以分為兩種類別：

## <a name="layouts-with-single-content"></a>具有單一內容的版面配置

這些類別衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 定義 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 和屬性的 [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) ：

| 類型 | 說明 | 外觀 |
| --- | --- | --- |
| `ContentView` | [`ContentView`](xref:Xamarin.Forms.ContentView) 包含使用屬性設定的單一子系 [`Content`](xref:Xamarin.Forms.ContentView.Content) 。 `Content`屬性可以設定為任何 `View` 衍生，包括其他 `Layout` 衍生。 `ContentView` 大部分用來當做結構化元素，並做為的基類 [`Frame`](xref:Xamarin.Forms.Frame) 。<br /><br />[API 檔](xref:Xamarin.Forms.ContentView)  / [指南](~/xamarin-forms/user-interface/layouts/contentview.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![ContentView 範例](layouts-images/ContentView.png "ContentView 範例")](layouts-images/ContentView-Large.png#lightbox "ContentView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
| `Frame` | [`Frame`](xref:Xamarin.Forms.Frame)類別衍生自， [`ContentView`](xref:Xamarin.Forms.ContentView) 並在其子系周圍顯示框線或框架。 `Frame`類別的預設 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 值為20，而且也會定義 [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) 、 [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) 和 [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) 屬性。<br /><br />[API 檔](xref:Xamarin.Forms.Frame)  / [指南](~/xamarin-forms/user-interface/layouts/frame.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![畫面格範例](layouts-images/Frame.png "畫面格範例")](layouts-images/Frame-Large.png#lightbox "畫面格範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
| `ScrollView` | [`ScrollView`](xref:Xamarin.Forms.ScrollView) 能夠滾動其內容。 將 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 屬性設為視圖或配置太大而無法容納在螢幕上。  (的內容 `ScrollView` 通常是 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 ) 設定 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 屬性以指出滾動應為垂直、水準或兩者。<br /><br />[API 檔](xref:Xamarin.Forms.ScrollView)  / [指南](~/xamarin-forms/user-interface/layouts/scrollview.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![ScrollView 範例](layouts-images/ScrollView.png "ScrollView 範例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
| `TemplatedView` | [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 顯示具有控制項範本的內容，而且是的基類 [`ContentView`](xref:Xamarin.Forms.ContentView) 。<br /><br />[API 檔](xref:Xamarin.Forms.TemplatedView)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedView 範例](layouts-images/TemplatedView.png "TemplatedView 範例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 範例") |
| `ContentPresenter` | [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 是樣板化視圖的版面建構管理員，用在中， [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 用以標記要顯示內容的位置。<br /><br />[API 檔](xref:Xamarin.Forms.ContentPresenter)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![ContentPresenter 範例](layouts-images/ContentPresenter.png "ContentPresenter 範例")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter 範例") |
|     |     |     |

## <a name="layouts-with-multiple-children"></a>具有多個子系的版面配置

這些類別衍生自 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ：

| 類型 | 說明 | 外觀 |
| --- | --- | --- |
| `StackLayout` | [`StackLayout`](xref:Xamarin.Forms.StackLayout) 根據屬性，以水準或垂直方式將子項目放置在堆疊中 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 。 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)屬性會控制子系之間的間距，預設值為6。<br /><br />[API 檔](xref:Xamarin.Forms.StackLayout)  / [指南](~/xamarin-forms/user-interface/layouts/stacklayout.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![StackLayout 範例](layouts-images/StackLayout.png "StackLayout 範例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
| `Grid` | [`Grid`](xref:Xamarin.Forms.Grid) 將其子項目置於資料列和資料行的方格中。 子系的位置會以附加的 [屬性](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty) 、、 [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和表示 [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 。<br /><br />[API 檔](xref:Xamarin.Forms.Grid)  / [指南](~/xamarin-forms/user-interface/layouts/grid.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![方格範例](layouts-images/Grid.png "方格範例")](layouts-images/Grid-Large.png#lightbox "方格範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
| `AbsoluteLayout` | [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 將子專案放置在相對於其父系的特定位置。 子系的位置會使用 [附加屬性](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 和來表示 [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 。 適用 `AbsoluteLayout` 于以動畫顯示視圖的位置。<br /><br />[API 檔](xref:Xamarin.Forms.AbsoluteLayout)  / [指南](~/xamarin-forms/user-interface/layouts/absolutelayout.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![AbsoluteLayout 範例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 範例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  的 c # 程式碼具有[程式碼後端](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs)的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) |
| `RelativeLayout` | [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 放置子專案相對於 `RelativeLayout` 本身或其同級的位置。 子系的位置會使用設定為類型和之物件的 [附加屬性](~/xamarin-forms/xaml/attached-properties.md) 來表示 [`Constraint`](xref:Xamarin.Forms.Constraint) [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) 。<br /><br />[API 檔](xref:Xamarin.Forms.RelativeLayout)  / [指南](~/xamarin-forms/user-interface/layouts/relativelayout.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![RelativeLayout 範例](layouts-images/RelativeLayout.png "RelativeLayout 範例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
| `FlexLayout` | [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 是以 [CSS 彈性] 方塊配置 [模組](https://www.w3.org/TR/css-flexbox-1/)為基礎，通常稱為「 _flex 版面_ 配置」或「 _彈性_ 」。 `FlexLayout` 定義六個可系結屬性和五個附加的可系結屬性，允許以許多對齊和方向選項堆疊或包裝子系。<br /><br />[API 檔](xref:Xamarin.Forms.FlexLayout)  / [指南](~/xamarin-forms/user-interface/layouts/flex-layout.md)  / [範例](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![FlexLayout 範例](layouts-images/FlexLayout.png "FlexLayout 範例")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 檔](/dotnet/api/xamarin.forms?view=xamarin-forms)