---
title: ''
description: 每 Xamarin.Forms 個控制項都有一個適用于每個平臺的轉譯器，可建立原生控制項的實例。 本文列出的轉譯器和原生控制項類別會執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a56f05f8ff4eb8ece43a9f4f38a669cfdc85c4be
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135183"
---
# <a name="renderer-base-classes-and-native-controls"></a>轉譯器基底類別與原生控制項

_每 Xamarin.Forms 個控制項都有一個適用于每個平臺的轉譯器，可建立原生控制項的實例。本文列出的轉譯器和原生控制項類別會執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。_

除了 `MapRenderer` 類別之外，平台特定轉譯器皆可在下列命名空間中找到：

- **iOS** – Xamarin.Forms 。平臺 iOS
- **Android** – Xamarin.Forms 。Platform. Android
- **Android （AppCompat）** – Xamarin.Forms 。AppCompat
- **通用 Windows 平臺（UWP）** – Xamarin.Forms 。平臺. UWP

`MapRenderer` 類別可在下列命名空間中找到：

- **iOS** – Xamarin.Forms 。對應。 iOS
- **Android** – Xamarin.Forms 。對應。 Android
- **通用 Windows 平臺（UWP）** – Xamarin.Forms 。對應. UWP

> [!NOTE]
> 如需建立 Shell 應用程式之自訂轉譯器的詳細資訊，請參閱[ Xamarin.Forms Shell 自訂](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)轉譯器。

## <a name="pages"></a>頁面

下表列出執行每個頁面類型的轉譯器和原生控制項類別 Xamarin.Forms [Page](~/xamarin-forms/user-interface/controls/pages.md) ：

|頁面|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – 手機)、TabletMasterDetailPageRenderer (iOS – 平板電腦)、MasterDetailRenderer (Android)、MasterDetailPageRenderer (Android AppCompat)、MasterDetailPageRenderer (UWP)|UIViewController (手機)、UISplitViewController (平板電腦)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (自訂控制項)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS 和 Android)、NavigationPageRenderer (Android AppCompat)、NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (自訂控制項)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS 和 Android)、TabbedPageRenderer (Android AppCompat)、TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (樞紐)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>版面配置

下表列出的轉譯器和原生控制項類別會執行每種 Xamarin.Forms [版面](~/xamarin-forms/user-interface/controls/layouts.md)配置類型：

|Layout|轉譯器|iOS|Android|UWP|
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

下表列出的轉譯器和原生控制項類別會執行每種 Xamarin.Forms [視圖](~/xamarin-forms/user-interface/controls/views.md)類型：

|檢視|轉譯器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|進度列||進度列|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS 和 Android)、BoxViewRenderer (UWP)|UIView|ViewGroup||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|按鈕|AppCompatButton|按鈕|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|核取方塊|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||映像|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|按鈕|
|`IndicatorView`|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|`RadioButton`|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|進度列||進度列|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||滑桿|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控制|
|`SwipeView`|SwipeViewRenderer|UIView||檢視|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|參數|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer （iOS）、WebViewRenderer （Android 和 UWP）|WkWebView|WebView||WebView|

> [!NOTE]
> `Expander`控制項是使用進行的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，具有動畫。 因此，它不會有任何平臺轉譯器。

## <a name="cells"></a>資料格

下表列出的轉譯器和原生控制項類別會執行每個資料 Xamarin.Forms [格](~/xamarin-forms/user-interface/controls/cells.md)類型：

|資料格|轉譯器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|具有 UITextField 的 UITableViewCell|具有 TextView 和 EditText 的 LinearLayout|具有 TextBox 的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|具有 UISwitch 的 UITableViewCell|參數|具有 Grid 的 DataTemplate，包含 TextBlock 和 ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|具有兩個 TextView 的 LinearLayout|具有 StackPanel 的 DataTemplate，包含兩個 TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|具有 UIImage 的 UITableViewCell|具有兩個 TextView 和一個 ImageView 的 LinearLayout|具有 Grid 的 DataTemplate，包含一個 Image 和兩個 TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|檢視|具有 ContentPresenter 的 DataTemplate|

## <a name="summary"></a>摘要

本文列出的轉譯器和原生控制項類別會執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。 每 Xamarin.Forms 個控制項都有一個適用于每個平臺的轉譯器，可建立原生控制項的實例。
