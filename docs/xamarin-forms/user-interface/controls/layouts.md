---
title: Xamarin.Forms 版面配置
description: Xamarin.Forms 版面配置用來撰寫使用者介面控制項至視覺化結構。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: e9a4a661e694b5a885f202a36f9a2916c6c339fd
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms 版面配置

_Xamarin.Forms 版面配置用來撰寫使用者介面控制項至視覺化結構。_

[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout)和[ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) Xamarin.Forms 中的類別是特製化的子類型，做為容器的檢視和其他配置檢視。 `Layout`本身的類別衍生自[ `View` ](views.md)。 A`Layout`衍生項目通常包含邏輯，可設定的位置和大小的子項目 Xamarin.Forms 應用程式中。

 [ ![](layouts-images/layouts-sml.png "Xamarin.Forms 版面配置類型")](layouts-images/layouts.png#lightbox "Xamarin.Forms 版面配置類型")

衍生自類別`Layout`可以分為兩個類別：

## <a name="layouts-with-single-content"></a>具有單一內容的配置

這些類別衍生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)，而後者可定義[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)和[ `IsClippedToBounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.IsClippedToBounds/)屬性。

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) 含有已設定的單一子系[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)屬性。 `Content`屬性可以設定為任何`View`衍生項目，包括其他`Layout`衍生項目。 `ContentView` 通常用來結構化項目，並做為基底類別[ `Frame` ](#frame)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) | [![ContentView 範例](layouts-images/ContentView.png "ContentView 範例")](layouts-images/ContentView-Large.png#lightbox "ContentView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)類別衍生自[ `ContentView` ](#contentView)並顯示其子系上的矩形外框。 `Frame` 有了 default [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)值為 20，而且也會定義[ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/)， [ `CornerRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.CornerRadius/)，和[ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) | [![框架範例](layouts-images/Frame.png "框架範例")](layouts-images/Frame-Large.png#lightbox "框架範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) 是可捲動內容。 設定[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/)檢視或配置太大，若要在螢幕上的屬性。 (內容`ScrollView`經常是[ `StackLayout` ](#stackLayout)。)設定[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/)屬性，指出是否捲動應該垂直、 水平或兩者。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) / [指南](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![ScrollView 範例](layouts-images/ScrollView.png "ScrollView 範例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) 顯示使用控制項範本的內容，是基底類別[ `ContentView` ](#contentView)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedView 範例](layouts-images/TemplatedView.png "TemplatedView 範例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 範例") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) 是中所使用的樣板化檢視的配置管理員[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)將標示要呈現的內容出現的位置。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![ContentPresenter 範例](layouts-images/ContentPresenter.png "ContentPresenter 範例")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter 範例") |
|     |     |

## <a name="layouts-with-multiple-children"></a>具有多個子系的版面配置

這些類別衍生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)。

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 水平或垂直根據堆疊中放置子項目[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)屬性。 [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/)屬性管理之子系之間的間距和以及具有預設值是 6。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) / [指南](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![StackLayout 範例](layouts-images/StackLayout.png "StackLayout 範例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) 在資料列和資料行的方格中定位其子項目。 使用表示子系位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/)， [ `Column` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/)， [ `RowSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/)，和[ `ColumnSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) / [指南](~/xamarin-forms/user-interface/layouts/grid.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![格線範例](layouts-images/Grid.png "格線範例")](layouts-images/Grid-Large.png#lightbox "格線範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) 在特定位置，相對於其父放置子項目。 使用表示子系位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/)和[ `LayoutFlags` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/)。 `AbsoluteLayout`適合用來建立動畫的檢視位置。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) / [指南](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![AbsoluteLayout 範例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 範例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) 放置子項目相對於`RelativeLayout`本身或其同層級。 使用表示子系位置[附加屬性](~/xamarin-forms/xaml/attached-properties.md)設定型別的物件[ `Constraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/)和[ `BoundsConstraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/)。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)/ [指南](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![RelativeLayout 範例](layouts-images/RelativeLayout.png "RelativeLayout 範例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/root/Xamarin.Forms/)
