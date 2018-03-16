---
title: "Xamarin.Forms 檢視"
description: "Xamarin.Forms 檢視是跨平台行動裝置的使用者介面的建置組塊。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ef4de2d544f3bcfb661b29dd90de738ae0442373
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="xamarinforms-views"></a>Xamarin.Forms 檢視

_Xamarin.Forms 檢視是跨平台行動裝置的使用者介面的建置組塊。_

檢視是使用者介面物件，例如標籤、 按鈕和滑桿，一般稱為*控制項*或*widget*其他圖形化的程式設計環境中。 Xamarin.Forms 都是衍生自支援的檢視[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別。 它們可以分為數個類別：

## <a name="views-for-presentation"></a>要呈現的檢視

### <a name="label"></a>ThisAddIn

|     |     |
| --- | --- |
| [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 顯示單行文字字串或多行文字區塊，不論是透過常數或變數的格式。 設定[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性設為字串常數的格式，或組[ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/)屬性[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)物件變數格式設定。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) / [指南](~/xamarin-forms/user-interface/text/label.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![標籤範例](views-images/Label.png "標籤範例")](views-images/Label-Large.png#lightbox "標籤範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 顯示點陣圖。 可以透過內嵌為資源通用專案或平台專案中建立或使用.NET Web 下載點陣圖`Stream`物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) / [指南](~/xamarin-forms/user-interface/images.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![影像範例](views-images/Image.png "影像範例")](views-images/Image-Large.png#lightbox "影像範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) 顯示的色彩來實心矩形[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)屬性。 `BoxView` 已為 40 x 40 的預設大小要求。 對於其他的大小，指派[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) / [指南](~/xamarin-forms/user-interface/boxview.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)， [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)， [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)， [4](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)， [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)，和[6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![BoxView 範例](views-images/BoxView.png "BoxView 範例")](views-images/BoxView-Large.png#lightbox "BoxView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>網頁檢視

|     |     |
| --- | --- |
| [`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) 顯示網頁或 HTML 內容，根據是否[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)屬性設定為[ `UriWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UrlWebViewSource/)或[ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/)物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) / [指南](~/xamarin-forms/user-interface/webview.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)和[2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![WebView 範例](views-images/WebView.png "WebView 範例")](views-images/WebView-Large.png#lightbox "WebView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/) OpenGL 圖形顯示 iOS 和 Android 專案中。 沒有任何支援的通用 Windows 平台。 IOS 和 Android 專案需要的參考**OpenTK 1.0**組件或**OpenTK** 1.0.0.0 版組件。 `OpenGLView` 更輕鬆地使用共用專案;如果使用 PCL 或.NET 標準文件庫中，然後相依性服務也會需要 （如範例程式碼所示）。<br /><br />這是透過 Xamarin.Forms，內建的唯一圖形設備但 Xamarin.Forms 應用程式也可以轉譯圖形使用[ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md)， [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)，或[ `UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/)<br /><br /> | [![OpenGLView 範例](views-images/OpenGLView.png "OpenGLView 範例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>對應

|     |     |
| --- | --- |
| [`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) 會顯示的對應。 **Xamarin.Forms.Maps**必須安裝 Nuget 套件。 Android 和通用 Windows 平台都需要對應的授權金鑰。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) / [指南](~/xamarin-forms/user-interface/map.md) / [範例](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![對應範例](views-images/Map.png "對應範例")](views-images/Map-Large.png#lightbox "對應範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>啟動命令的檢視

### <a name="button"></a>按鈕

|     |     |
| --- | --- |
| [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 是一個矩形的物件，顯示的文字，並引發[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/)時已按下的事件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) | [![按鈕範例](views-images/Button.png "按鈕範例")](views-images/Button-Large.png#lightbox "按鈕範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 顯示讓使用者輸入文字字串，和一個按鈕 （或鍵盤按鍵），表示要執行的搜尋應用程式的區域。 [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.Text/)屬性可存取該文字，而[ `SearchButtonPressed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)事件表示已按下按鈕。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) | [![SearchBar 範例](views-images/SearchBar.png "SearchBar 範例")](views-images/SearchBar-Large.png#lightbox "SearchBar 範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視 

### <a name="slider"></a>滑桿

|     |     |
| --- | --- |
| [`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) 可讓使用者選取`double`值從指定的連續範圍[ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/)和[ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) | [![滑桿範例](views-images/Slider.png "滑桿範例")](views-images/Slider-Large.png#lightbox "滑桿範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Stepper

|     |     |
| --- | --- |
| [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) 可讓使用者選取`double`值所指定的增量值範圍從[ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Minimum/)， [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Maximum/)，和[ `Increment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) | [![Stepper 範例](views-images/Stepper.png "Stepper 範例")](views-images/Stepper-Large.png#lightbox "Stepper 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>參數 

|     |     |
| --- | --- |
| [`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) 形式 on/off 開關，以允許使用者選取的布林值。 [ `IsToggled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/)屬性是交換器的狀態，而[ `Toggled` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/)狀態變更時引發事件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) | [![切換範例](views-images/Switch.png "切換範例")](views-images/Stepper-Large.png#lightbox "切換範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) 可讓使用者與平台日期選擇器選取的日期。 將範圍設定為允許的日期與[ `MinimumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/)和[ `MaximumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/)屬性。 [ `Date` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/)屬性是選取的日期和[ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/)該屬性變更時引發事件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) / [指南](~/xamarin-forms/user-interface/datepicker.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![日期選擇器範例](views-images/DatePicker.png "日期選擇器範例")](views-images/DatePicker-Large.png#lightbox "日期選擇器範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) 可讓使用者與平台時間選擇器選取的時間。 [ `Time` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/)屬性是選取的時間。 應用程式可以在中監視變更`Time`所安裝的處理常式屬性[ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/)事件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) | [![Timeupdown 範例](views-images/TimePicker.png "Timeupdown 範例")](views-images/TimePicker-Large.png#lightbox "Timeupdown 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

這兩個類別衍生自[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)類別，定義[ `Keyboard` ](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/)屬性。

<a name="entry" />

### <a name="entry"></a>進入

|     |     |
| --- | --- |
| [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 可讓使用者輸入和編輯單行文字。 文字可做為[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/)屬性，而[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)和[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)引發事件時文字會變更或使用者表示完成藉由點選 enter 鍵。<br /><br />使用[ `Editor` ](#editor)來輸入及編輯多行文字。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) / [指南](~/xamarin-forms/user-interface/text/entry.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![項目範例](views-images/Entry.png "項目範例")](views-images/Entry-Large.png#lightbox "項目範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>編輯器

|     |     |
| --- | --- |
| [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) 可讓使用者輸入和編輯多行文字。 文字可做為[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Editor.Text/)屬性，而[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/)和[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/)引發事件時文字會變更或使用者表示完成。<br /><br />使用[ `Entry` ](#entry)檢視輸入和編輯單行文字。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) / [指南](~/xamarin-forms/user-interface/text/editor.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![項目範例](views-images/Editor.png "編輯器範例")](views-images/Editor-Large.png#lightbox "編輯器範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>若要表示的活動檢視

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) 使用動畫顯示應用程式正在進行長時間的活動中，而不需給予進度的任何指示。 [ `IsRunning` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)屬性會控制動畫。<br /><br />如果已知活動的進度，請使用[ `ProgressBar` ](#progressbar)改為。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) | [![ActivityIndicator 範例](views-images/ActivityIndicator.png "ActivityIndicator 範例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>進度列

|     |     |
| --- | --- |
| [`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) 使用動畫顯示應用程式進行長時間的活動。 設定[ `Progress` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ProgressBar.Progress/)值介於 0 和 1，指出進度的屬性。<br /><br />如果不知道在活動的進度，使用[ `ActivityIndicator` ](#activityindicator)改為。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) | [![進度列範例](views-images/ProgressBar.png "ProgressBar 範例")](views-images/ProgressBar-Large.png#lightbox "進度列範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合檢視

### <a name="picker"></a>選擇器

|     |     |
| --- | --- |
| [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) 顯示選取的項目從清單的文字字串，並可讓您選取的項目時，點選檢視。 設定[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)屬性清單的字串，或[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性物件的集合。 [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)選取項目時引發事件。<br /><br />`Picker`僅當選取時顯示的項目清單。 使用[ `ListView` ](#listView)或[ `TableView` ](#tableView)頁上的可捲動清單。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) / [指南](~/xamarin-forms/user-interface/picker/index.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![選擇器範例](views-images/Picker.png "選擇器範例")](views-images/Picker-Large.png#lightbox "選擇器範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml)與[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 衍生自[ `ItemsView[Cell]` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) ，並顯示可捲動的清單，可選取項目。 設定[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)屬性集合的物件和集合[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)屬性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)物件描述項目如何若要格式化。 [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)事件的選取項目發出訊號，可做為[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) / [指南](~/xamarin-forms/user-interface/listview/index.md) / [範例](https://developer.xamarin.com/samples/WorkingWithListview) | [![ListView 範例](views-images/ListView.png "ListView 範例")](views-images/ListView-Large.png#lightbox "ListView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) 顯示類型的資料列的清單[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) subheaders 與選擇性標頭。 設定[ `Root` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/)屬性型別的物件[ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)，並加入[ `TableSection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/)物件的`TableRoot`。 每個`TableSection`是一組`Cell`物件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) / [指南](~/xamarin-forms/user-interface/tableview.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![TableView 範例](views-images/TableView.png "TableView 範例")](views-images/TableView-Large.png#lightbox "TableView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/root/Xamarin.Forms/)
