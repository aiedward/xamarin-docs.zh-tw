---
title: Xamarin.Forms 版面配置
description: Xamarin.Forms 版面配置用來撰寫使用者介面控制項至視覺化結構。 本文章列出包含在 Xamarin.Forms 中的配置。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 3c525178b5ed1e62774d032c0d317d43cd3f94c1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61377321"
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms 版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/FormsGallery/)

_Xamarin.Forms 版面配置用來撰寫使用者介面控制項至視覺化結構。_

[ `Layout` ](xref:Xamarin.Forms.Layout)並[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)在 Xamarin.Forms 中的類別是特製化的檢視，做為檢視和其他版面配置容器的子類型。 `Layout`類別本身衍生自[ `View` ](views.md)。 A`Layout`衍生項目通常包含邏輯，可在 Xamarin.Forms 應用程式中設定的位置和大小的子項目。

[![Xamarin.Forms 版面配置類型](layouts-images/layouts-sml.png "Xamarin.Forms 版面配置類型")](layouts-images/layouts.png#lightbox "Xamarin.Forms 版面配置類型")

衍生自類別`Layout`可以分為兩類：

## <a name="layouts-with-single-content"></a>使用單一內容的配置

這些類別衍生自[ `Layout` ](xref:Xamarin.Forms.Layout)，其定義[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)並[ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds)屬性。

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) 包含使用所設定的單一子系[ `Content` ](xref:Xamarin.Forms.ContentView.Content)屬性。 `Content`屬性可以設定為任何`View`衍生項目，包括其他`Layout`衍生項目。 `ContentView` 通常用來結構化的項目，並做為基底類別[ `Frame` ](#frame)。<br /><br />[API 文件](xref:Xamarin.Forms.ContentView) | [![ContentView 範例](layouts-images/ContentView.png "ContentView 範例")](layouts-images/ContentView-Large.png#lightbox "ContentView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| [ `Frame` ](xref:Xamarin.Forms.Frame)類別衍生自[ `ContentView` ](#contentView)並顯示其子系矩形外的框。 `Frame` 預設值[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)值為 20，而且也會定義[ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor)， [ `CornerRadius` ](xref:Xamarin.Forms.Frame.CornerRadius)，以及[ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.Frame) | [![畫面格範例](layouts-images/Frame.png "畫面格範例")](layouts-images/Frame-Large.png#lightbox "框架範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) 是可捲動內容。 設定[ `Content` ](xref:Xamarin.Forms.ScrollView.Content)屬性，以檢視或太大的版面配置以符合螢幕大小。 (內容`ScrollView`是經常[ `StackLayout` ](#stackLayout)。)設定[ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation)屬性，指出是否捲動應該是垂直、 水平或兩者。<br /><br />[API 文件](xref:Xamarin.Forms.ScrollView) / [指南](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![ScrollView 範例](layouts-images/ScrollView.png "ScrollView 範例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 顯示使用控制項範本的內容，並是基底類別[ `ContentView` ](#contentView)。<br /><br />[API 文件](xref:Xamarin.Forms.TemplatedView) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedView 範例](layouts-images/TemplatedView.png "TemplatedView 範例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 範例") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 是使用中的樣板化檢視的版面配置管理員[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)將標示要呈現的內容出現的位置。<br /><br />[API 文件](xref:Xamarin.Forms.ContentPresenter) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![ContentPresenter 範例](layouts-images/ContentPresenter.png "ContentPresenter 範例")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter 範例") |
|     |     |

## <a name="layouts-with-multiple-children"></a>具有多個子系的版面配置

這些類別衍生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)。

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) 將子元素放在水平或垂直根據一個堆疊[ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)屬性。 [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing)屬性會控管之子系之間的間距和其預設值為 6。<br /><br />[API 文件](xref:Xamarin.Forms.StackLayout) / [指南](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![StackLayout 範例](layouts-images/StackLayout.png "StackLayout 範例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) 放置其子項目格線中的資料列和資料行。 使用表示子系的位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty)， [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty)， [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty)，並[ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty)。<br /><br />[API 文件](xref:Xamarin.Forms.Grid) / [指南](~/xamarin-forms/user-interface/layouts/grid.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![格線範例](layouts-images/Grid.png "格線範例")](layouts-images/Grid-Large.png#lightbox "格線範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 放置相對於其父代的特定位置的子項目。 使用表示子系的位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)並[ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)。 `AbsoluteLayout`適合用來以動畫顯示的檢視位置。<br /><br />[API 文件](xref:Xamarin.Forms.AbsoluteLayout) / [指南](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![AbsoluteLayout 範例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 範例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 放置相對於的子項目`RelativeLayout`本身或其同層級項目。 使用表示子系的位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md)並設為類型的物件[ `Constraint` ](xref:Xamarin.Forms.Constraint)並[ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint)。<br /><br />[API 文件](xref:Xamarin.Forms.RelativeLayout) / [指南](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![範例 RelativeLayout](layouts-images/RelativeLayout.png "RelativeLayout 範例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 會根據 CSS[彈性的方塊版面配置模組](http://www.w3.org/TR/css-flexbox-1/)，稱為_flex 配置_或_彈性方塊_。 `FlexLayout` 定義六個可繫結的屬性和五個附加的可繫結屬性，讓堆疊或許多的對齊方式和方向選項以包裝的子系。<br /><br />[API 文件](xref:Xamarin.Forms.FlexLayout) / [指南](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/) | [![FlexLayout 範例](layouts-images/FlexLayout.png "FlexLayout 範例")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
