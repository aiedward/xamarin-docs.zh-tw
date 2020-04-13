---
title: 轉譯器基底類別與原生控制項
description: 每個 Xamarin.Forms 控制項都具有每個平台的轉譯器，這些轉譯器可建立原生控制項的執行個體。 本文列出可實作每個 Xamarin.Forms 頁面、配置、檢視和資料格的轉譯器和原生控制項類別。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: f4c2663e4e4e298975fc1e230750ec562547b0da
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77636025"
---
# <a name="renderer-base-classes-and-native-controls"></a>轉譯器基底類別與原生控制項

_每個 Xamarin.Forms 控制件都有一個伴隨的呈現器,用於創建本機控制項的實例的每個平臺。本文列出了實現每個 Xamarin.Forms 頁面、佈局、檢視和單元格的呈現器和本機控制項類。_

除了 `MapRenderer` 類別之外，平台特定轉譯器皆可在下列命名空間中找到：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer` 類別可在下列命名空間中找到：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

> [!NOTE]
> 如需針對 Shell 應用程式建立自訂轉譯器的相關資訊，請參閱 [Xamarin.Forms Shell 自訂轉譯器](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)。

## <a name="pages"></a>頁面

下表列出可實作每個 Xamarin.Forms [頁面](~/xamarin-forms/user-interface/controls/pages.md)類型的轉譯器和原生控制項類別：

|頁面|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – 手機)、TabletMasterDetailPageRenderer (iOS – 平板電腦)、MasterDetailRenderer (Android)、MasterDetailPageRenderer (Android AppCompat)、MasterDetailPageRenderer (UWP)|UIViewController (手機)、UISplitViewController (平板電腦)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (自訂控制項)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS 和 Android)、NavigationPageRenderer (Android AppCompat)、NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (自訂控制項)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS 和 Android)、TabbedPageRenderer (Android AppCompat)、TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (樞紐)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>版面配置

下表列出可實作每個 Xamarin.Forms [配置](~/xamarin-forms/user-interface/controls/layouts.md)類型的轉譯器和原生控制項類別：

|配置|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|框線|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|檢視|FrameworkElement|

## <a name="views"></a>檢視

下表列出可實作每個 Xamarin.Forms [檢視](~/xamarin-forms/user-interface/controls/views.md)類型的轉譯器和原生控制項類別：

|檢視|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|進度列||進度列|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS 和 Android)、BoxViewRenderer (UWP)|UIView|ViewGroup||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|按鈕|AppCompatButton|按鈕|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|旋轉木馬檢視成像器|UICollectionView||RecyclerView|清單檢視基礎|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|清單檢視基礎|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||映像|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|按鈕|
|`IndicatorView`|指標檢視成像器|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|媒體元素渲染器|UIView||視訊檢視|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|進度列||進度列|
|`RefreshView`|重新載圖成像器|UIView||輕掃刷新佈局|重新整理容器|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||滑桿|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控制|
|`SwipeView`|輕地圖成像器|UIView||檢視|輕掃控制|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Switch|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (安卓和 UWP)|WkWebView|WebView||WebView|

## <a name="cells"></a>資料格

下表列出可實作每個 Xamarin.Forms [資料格](~/xamarin-forms/user-interface/controls/cells.md)類型的轉譯器和原生控制項類別：

|資料格|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|具有 UITextField 的 UITableViewCell|具有 TextView 和 EditText 的 LinearLayout|具有 TextBox 的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|具有 UISwitch 的 UITableViewCell|Switch|具有 Grid 的 DataTemplate，包含 TextBlock 和 ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|具有兩個 TextView 的 LinearLayout|具有 StackPanel 的 DataTemplate，包含兩個 TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|具有 UIImage 的 UITableViewCell|具有兩個 TextView 和一個 ImageView 的 LinearLayout|具有 Grid 的 DataTemplate，包含一個 Image 和兩個 TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|檢視|具有 ContentPresenter 的 DataTemplate|

## <a name="summary"></a>總結

本文列出可實作每個 Xamarin.Forms 頁面、配置、檢視和資料格的轉譯器和原生控制項類別。 每個 Xamarin.Forms 控制項都具有每個平台的轉譯器，這些轉譯器可建立原生控制項的執行個體。
