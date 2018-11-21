---
title: 轉譯器的基底類別和原生控制項
description: 每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。 本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 56df2f7e6b83ddd4a5780506471cbd32a3aced40
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171946"
---
# <a name="renderer-base-classes-and-native-controls"></a>轉譯器的基底類別和原生控制項

_每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。_

除了`MapRenderer`類別，可以在下列命名空間中找到的平台特定轉譯器：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer`類別可在下列命名空間：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>頁面

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[網頁](~/xamarin-forms/user-interface/controls/pages.md)類型：

|頁面|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – 電話) TabletMasterDetailPageRenderer (iOS – 平板電腦) MasterDetailRenderer (Android)、 MasterDetailPageRenderer (Android AppCompat)、 MasterDetailPageRenderer (UWP)|UIViewController (Phone)、 UISplitViewController （平板電腦）|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement （也就是自訂控制項）|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer （iOS 和 Android）、 NavigationPageRenderer (Android AppCompat)、 NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement （也就是自訂控制項）|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer （iOS 和 Android）、 TabbedPageRenderer (Android AppCompat)、 TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Powerpivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>版面配置

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[版面配置](~/xamarin-forms/user-interface/controls/layouts.md)類型：

|配置|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Border|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|檢視|FrameworkElement|

## <a name="views"></a>檢視

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[檢視](~/xamarin-forms/user-interface/controls/views.md)類型：

|檢視|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|進度列||進度列|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer （iOS 和 Android）、 BoxViewRenderer (UWP)|UIView|ViewGroup||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|標記的 UIButton|按鈕|AppCompatButton|按鈕|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|`ImageButton`|ImageButtonRenderer|標記的 UIButton||AppCompatImageButton|按鈕|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|進度列||進度列|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||滑桿|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控制項|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|參數|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>資料格

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[儲存格](~/xamarin-forms/user-interface/controls/cells.md)類型：

|資料格|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|使用 UITextField UITableViewCell|TextView 與 EditText LinearLayout|在文字方塊的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|使用 UISwitch UITableViewCell|參數|使用格線，其中包含 TextBlock 和 ToggleSwitch 的 DataTemplate|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|使用兩個 TextViews LinearLayout|使用包含兩個 Textblock StackPanel 的 DataTemplate|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|使用 UIImage UITableViewCell|使用兩個 TextViews 和 ImageView LinearLayout|使用方格，其中包含影像和兩個 Textblock 的 DataTemplate|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|檢視|以 ContentPresenter 的 DataTemplate|

## <a name="summary"></a>總結

這篇文章已列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。 每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。

## <a name="related-links"></a>相關連結

- [自訂轉譯器 （Xamarin University 影片）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
