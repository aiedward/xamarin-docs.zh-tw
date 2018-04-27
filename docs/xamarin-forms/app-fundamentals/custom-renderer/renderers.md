---
title: 轉譯器的基底類別和原生控制項
description: Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 513b9b3738b9481444cdad10daa9b11a8441c9dd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>轉譯器的基底類別和原生控制項

_Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。_

除了`MapRenderer`類別，可以在下列命名空間中找到平台特定轉譯器：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer`類別位於下列命名空間：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>頁面

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[頁面](~/xamarin-forms/user-interface/controls/pages.md)類型：

|頁面|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|檢視||FrameworkElement|
|[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)|PhoneMasterDetailRenderer (iOS – 電話) TabletMasterDetailPageRenderer (iOS – 平板電腦)，MasterDetailRenderer (Android)、 MasterDetailPageRenderer (Android AppCompat)、 MasterDetailPageRenderer (UWP)|UIViewController (Phone)，UISplitViewController （平板電腦）|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement （自訂控制項）|
|[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)|NavigationRenderer （iOS 和 Android）、 NavigationPageRenderer (Android AppCompat)、 NavigationPageRenderer (UWP)|UIToolbar|檢視|檢視|FrameworkElement （自訂控制項）|
|[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)|TabbedRenderer （iOS 和 Android）、 TabbedPageRenderer (Android AppCompat)、 TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)|PageRenderer|UIViewController|檢視||FrameworkElement|
|[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>版面配置

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[配置](~/xamarin-forms/user-interface/controls/layouts.md)類型：

|配置|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Frame`](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)|FrameRenderer|UIView|檢視|Border|
|[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)|ViewRenderer|UIView|檢視|FrameworkElement|
|[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)|ViewRenderer|UIView|檢視|FrameworkElement|

## <a name="views"></a>檢視

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[檢視](~/xamarin-forms/user-interface/controls/views.md)類型：

|檢視|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)|ActivityIndicatorRenderer|UIActivityIndicator|進度列||進度列|
|[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)|BoxRenderer （iOS 和 Android）、 BoxViewRenderer (UWP)|UIView|檢視||矩形|
|[`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)|ButtonRenderer|UIButton|按鈕|AppCompatButton|按鈕|
|[`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/)|CarouselViewRenderer|UIScrollView|RecyclerView||FlipView|
|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)|ImageRenderer|UIImageView|ImageView||Image|
|[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)|ProgressBarRenderer|UIProgressView|進度列||進度列|
|[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)|SliderRenderer|UISlider|SeekBar||滑桿|
|[`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|StepperRenderer|UIStepper|線性||控制項|
|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|SwitchRenderer|UISwitch|參數|SwitchCompat|ToggleSwitch|
|[`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)|WebViewRenderer|UIWebView|網頁檢視||網頁檢視|

## <a name="cells"></a>資料格

下表列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms[儲存格](~/xamarin-forms/user-interface/controls/cells.md)類型：

|資料格|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/)|EntryCellRenderer|與 UITextField UITableViewCell|TextView 與 EditText 線性|在文字方塊的 DataTemplate|
|[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/)|SwitchCellRenderer|與 UISwitch UITableViewCell|參數|以方格，其中包含 TextBlock 和 ToggleSwitch DataTemplate|
|[`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/)|TextCellRenderer|UITableViewCell|與兩個 TextViews 線性|與包含兩個 Textblock StackPanel DataTemplate|
|[`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)|ImageCellRenderer|與 UIImage UITableViewCell|與兩個 TextViews ImageView 線性|以方格包含的映像和兩個 Textblock DataTemplate|
|[`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|檢視|DataTemplate ContentPresenter 與|

## <a name="summary"></a>總結

這篇文章已列出轉譯器，而且原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。 Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。

## <a name="related-links"></a>相關連結

- [自訂轉譯器 （Xamarin 大學影片）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
