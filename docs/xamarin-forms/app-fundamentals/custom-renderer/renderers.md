---
title: 轉譯器基底類別與原生控制項
description: 每 Xamarin.Forms 個控制項都有每個平臺的轉譯器，可建立原生控制項的實例。 本文列出的轉譯器和原生控制項類別，可執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c543b481091a1b5f1a4efc2935a9c86c276686a
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940417"
---
# <a name="renderer-base-classes-and-native-controls"></a>轉譯器基底類別與原生控制項

_每 Xamarin.Forms 個控制項都有每個平臺的轉譯器，可建立原生控制項的實例。本文列出的轉譯器和原生控制項類別，可執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。_

除了 `MapRenderer` 類別之外，平台特定轉譯器皆可在下列命名空間中找到：

- **iOS** – Xamarin.Forms 。Platform. iOS
- **Android** – Xamarin.Forms 。Platform. Android
- **Android (AppCompat)** – Xamarin.Forms 。AppCompat
- **Android (FastRenderers)**  -  Xamarin.Forms 。FastRenderers
- **通用 Windows 平臺 (UWP)** – Xamarin.Forms 。Platform. UWP

如需快速轉譯器的詳細資訊，請參閱[ Xamarin.Forms 快速](~/xamarin-forms/internals/fast-renderers.md)轉譯器。

`MapRenderer` 類別可在下列命名空間中找到：

- **iOS** – Xamarin.Forms 。對應。 iOS
- **Android** – Xamarin.Forms 。對應。 Android
- **通用 Windows 平臺 (UWP)** – Xamarin.Forms 。對應。 UWP

> [!NOTE]
> 如需建立 Shell 應用程式之自訂轉譯器的詳細資訊，請參閱[ Xamarin.Forms Shell 自訂](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)轉譯器。

## <a name="pages"></a>Pages

下表列出可執行每種頁面類型的轉譯器和原生控制項類別 Xamarin.Forms [](~/xamarin-forms/user-interface/controls/pages.md) ：

|頁面|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage)|PhoneFlyoutPageRenderer (iOS –電話) 、TabletFlyoutPageRenderer (iOS –平板電腦) 、MasterDetailRenderer (Android) 、FlyoutPageRenderer (Android AppCompat) 、FlyoutPageRenderer (UWP) |UIViewController (手機)、UISplitViewController (平板電腦)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (自訂控制項)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS 和 Android)、NavigationPageRenderer (Android AppCompat)、NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (自訂控制項)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS 和 Android)、TabbedPageRenderer (Android AppCompat)、TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (樞紐)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>版面配置

下表列出執行每個配置類型的轉譯器和原生控制項類別 Xamarin.Forms [](~/xamarin-forms/user-interface/controls/layouts.md) ：

|Layout|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|CardView|框線|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|檢視|檢視|FrameworkElement|

## <a name="views"></a>檢視

下表列出執行每個檢視類型的轉譯器和原生控制項類別 Xamarin.Forms [](~/xamarin-forms/user-interface/controls/views.md) ：

|檢視|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS 和 Android)、BoxViewRenderer (UWP)|UIView|ViewGroup||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|按鈕|AppCompatButton|按鈕|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`CheckBox`](xref:Xamarin.Forms.CheckBox)|CheckBoxRenderer|UIButton||AppCompatCheckBox|核取方塊|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse)|EllipseRenderer|CALayer|檢視||橢圓形|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|按鈕|
|[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`Line`](xref:Xamarin.Forms.Shapes.Line)|LineRenderer|CALayer|檢視||線|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MapView||MapControl|
|[`Path`](xref:Xamarin.Forms.Shapes.Path)|PathRenderer|CALayer|檢視||路徑|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`Polygon`](xref:Xamarin.Forms.Shapes.Polygon)|PolygonRenderer|CALayer|檢視||Polygon|
|[`Polyline`](xref:Xamarin.Forms.Shapes.Polyline)|PolylineRenderer|CALayer|檢視||聚合線條|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`RadioButton`](xref:Xamarin.Forms.RadioButton)|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`Rectangle`](xref:Xamarin.Forms.Shapes.Rectangle)|RectangleRenderer|CALayer|檢視||矩形|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||滑桿|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控制|
|[`SwipeView`](xref:Xamarin.Forms.SwipeView)|SwipeViewRenderer|UIView||檢視|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Switch|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS) 、WebViewRenderer (Android 和 UWP) |WkWebView|WebView||WebView|

## <a name="cells"></a>資料格

下表列出執行每個資料格類型的轉譯器和原生控制項類別 Xamarin.Forms [](~/xamarin-forms/user-interface/controls/cells.md) ：

|資料格|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|具有 UITextField 的 UITableViewCell|具有 TextView 和 EditText 的 LinearLayout|具有 TextBox 的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|具有 UISwitch 的 UITableViewCell|Switch|具有 Grid 的 DataTemplate，包含 TextBlock 和 ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|具有兩個 TextView 的 LinearLayout|具有 StackPanel 的 DataTemplate，包含兩個 TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|具有 UIImage 的 UITableViewCell|具有兩個 TextView 和一個 ImageView 的 LinearLayout|具有 Grid 的 DataTemplate，包含一個 Image 和兩個 TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|檢視|具有 ContentPresenter 的 DataTemplate|

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [Xamarin.Forms Shell 自訂轉譯器](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)
