---
title: Xamarin.Forms 檢視
description: Xamarin.Forms 檢視是建置組塊的跨平台行動裝置的使用者介面。 這篇文章會列出包含在 Xamarin.Forms 中的檢視。
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/21/2019
ms.openlocfilehash: 5b2e58901d4a850863f68b26ce41e1aa4e8daee4
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/15/2019
ms.locfileid: "61358788"
---
# <a name="xamarinforms-views"></a>Xamarin.Forms 檢視

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/FormsGallery/)

_Xamarin.Forms 檢視是建置組塊的跨平台行動裝置的使用者介面。_

檢視是使用者介面物件，例如標籤、 按鈕和滑桿，一般稱為*控制項*或是*widget*其他圖形化的程式設計環境中。 支援的所有衍生自的 Xamarin.Forms 檢視[ `View` ](xref:Xamarin.Forms.View)類別。 它們可以分為數個類別：

## <a name="views-for-presentation"></a>檢視簡報

### <a name="label"></a>ThisAddIn

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) 單行文字字串或多行的文字區塊，不論是使用常數或變數的格式顯示。 設定[ `Text` ](xref:Xamarin.Forms.Label.Text)屬性設為字串常數的格式，或組[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)屬性設[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)物件變數格式設定。<br /><br />[API 文件](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![標籤範例](views-images/Label.png "標籤範例")](views-images/Label-Large.png#lightbox "標籤範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) 顯示點陣圖。 可以透過 Web、 常見的專案或平台專案中內嵌為資源，或使用.NET 建立下載點陣圖`Stream`物件。<br /><br />[API 文件](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![影像範例](views-images/Image.png "映像範例")](views-images/Image-Large.png#lightbox "影像範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) 顯示實心矩形的色彩來[ `Color` ](xref:Xamarin.Forms.BoxView.Color)屬性。 `BoxView` 已為 40 x 40 的預設大小要求。 如需其他大小，指派[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.BoxView) / [指南](~/xamarin-forms/user-interface/boxview.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)， [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)， [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)， [4](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)， [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)，和[6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![BoxView 範例](views-images/BoxView.png "BoxView 範例")](views-images/BoxView-Large.png#lightbox "BoxView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) 顯示網頁或 HTML 內容，根據是否[ `Source` ](xref:Xamarin.Forms.WebView.Source)屬性設定為[ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource)或是[ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource)物件。<br /><br />[API 文件](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) / [範例 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)和[2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![WebView 範例](views-images/WebView.png "WebView 範例")](views-images/WebView-Large.png#lightbox "WebView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) OpenGL 圖形顯示 iOS 和 Android 專案中。 沒有適用於通用 Windows 平台支援。 IOS 和 Android 專案需要的參考**OpenTK 1.0**組件或有**OpenTK** 1.0.0.0 版的組件。 `OpenGLView` 共用專案; 中使用的工作變得更容易如果使用.NET Standard 程式庫，然後相依性服務也會需要 （如程式碼範例所示）。<br /><br />這是唯一的圖形設施內建於 Xamarin.Forms，但 Xamarin.Forms 應用程式也可以轉譯圖形使用[ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md)， [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)，或[ `UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[API 文件](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![OpenGLView 範例](views-images/OpenGLView.png "OpenGLView 範例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>地圖

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) 顯示地圖。 **Xamarin.Forms.Maps**必須安裝 Nuget 套件。 Android 和通用 Windows 平台都需要一個 map 的授權金鑰。<br /><br />[API 文件](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map.md) / [範例](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![對應範例](views-images/Map.png "對應範例")](views-images/Map-Large.png#lightbox "對應範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>啟動命令的檢視

### <a name="button"></a>按鈕

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) 是一個矩形的物件，顯示的文字，並引發[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)已按下時的事件。<br /><br />[API 文件](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) / [範例](https://developer.xamarin.com/samples/UserInterface/ButtonDemos/) | [![按鈕範例](views-images/Button.png "按鈕範例")](views-images/Button-Large.png#lightbox "按鈕範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` 是矩形物件會顯示映像，這會引發`Clicked`已按下時的事件。<br /><br /> [快速入門](~/xamarin-forms/user-interface/imagebutton.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/) | [![ImageButton 範例](views-images/ImageButton.png "ImageButton 範例")](views-images/ImageButton-Large.png#lightbox "ImageButton 範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) 顯示讓使用者輸入文字字串，並按鈕 （或使用鍵盤按鍵），表示要執行搜尋的應用程式的區域。 [ `Text` ](xref:Xamarin.Forms.SearchBar.Text)屬性可存取該文字，而[ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)事件表示已按下按鈕。<br /><br />[API 文件](xref:Xamarin.Forms.SearchBar) | [![SearchBar 範例](views-images/SearchBar.png "SearchBar 範例")](views-images/SearchBar-Large.png#lightbox "SearchBar 範例")<br /> [此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視

### <a name="slider"></a>滑桿

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) 可讓使用者選取`double`從指定的連續範圍的值[ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum)並[ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) | [![滑桿範例](views-images/Slider.png "滑桿範例")](views-images/Slider-Large.png#lightbox "滑桿範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>步進

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) 可讓使用者選取`double`使用指定的累加值的範圍介於[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)， [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)，並[ `Increment` ](xref:Xamarin.Forms.Stepper.Increment)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.Stepper)  / [指南](~/xamarin-forms/user-interface/stepper.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) | [![步進範例](views-images/Stepper.png "步進範例")](views-images/Stepper-Large.png#lightbox "步進範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>參數

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) 使用 on/off 開關，以允許使用者選取的布林值的格式。 [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled)屬性是交換器的狀態，而[ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled)狀態變更時引發事件。<br /><br />[API 文件](xref:Xamarin.Forms.Switch) | [![切換範例](views-images/Switch.png "切換範例")](views-images/Switch-Large.png#lightbox "切換範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) 可讓使用者與平台的日期選擇器選取日期。 將範圍設定為允許使用的日期[ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate)並[ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate)屬性。 [ `Date` ](xref:Xamarin.Forms.DatePicker.Date)屬性是選取的日期，而[ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected)該屬性變更時引發事件。<br /><br />[API 文件](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![DatePicker 範例](views-images/DatePicker.png "DatePicker 範例")](views-images/DatePicker-Large.png#lightbox "DatePicker 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) 可讓使用者與平台時間選擇器選取的時間。 [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性是在選取的時間。 應用程式可以監視中的變更`Time`屬性所安裝的處理常式[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件。<br /><br />[API 文件](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker) | [![TimePicker 範例](views-images/TimePicker.png "TimePicker 範例")](views-images/TimePicker-Large.png#lightbox "TimePicker 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

這兩個類別衍生自[ `InputView` ](xref:Xamarin.Forms.InputView)類別，定義[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)屬性。

<a name="entry" />

### <a name="entry"></a>進入

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) 可讓使用者輸入和編輯單行文字。 文字可[ `Text` ](xref:Xamarin.Forms.Entry.Text)屬性，而[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)和[ `Completed` ](xref:Xamarin.Forms.Entry.Completed)會引發事件時文字會變更或使用者表示完成藉由點選 enter 鍵。<br /><br />使用[ `Editor` ](#editor)來輸入及編輯多行文字。<br /><br />[API 文件](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![項目範例](views-images/Entry.png "項目範例")](views-images/Entry-Large.png#lightbox "項目範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>編輯器

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) 可讓使用者輸入和編輯多行文字。 文字可[ `Text` ](xref:Xamarin.Forms.Editor.Text)屬性，而[ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged)和[ `Completed` ](xref:Xamarin.Forms.Editor.Completed)會引發事件時文字會變更或使用者表示完成。<br /><br />使用[ `Entry` ](#entry)檢視輸入和編輯單行文字。<br /><br />[API 文件](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![項目範例](views-images/Editor.png "編輯器範例")](views-images/Editor-Large.png#lightbox "編輯器範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>表示活動的檢視

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 使用動畫顯示應用程式參與冗長的活動中，而不需要提供任何指示進行。 [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning)屬性會控制動畫。<br /><br />如果已知活動的進度，請使用[ `ProgressBar` ](#progressbar)改。<br /><br />[API 文件](xref:Xamarin.Forms.ActivityIndicator) | [![ActivityIndicator 範例](views-images/ActivityIndicator.png "ActivityIndicator 範例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>進度列

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 您可以使用動畫來顯示應用程式正在進行長時間的活動。 設定[ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress)屬性值介於 0 和 1，表示進度。<br /><br />如果不知道活動的進度，請使用[ `ActivityIndicator` ](#activityindicator)改。<br /><br />[API 文件](xref:Xamarin.Forms.ProgressBar) | [![ProgressBar 範例](views-images/ProgressBar.png "ProgressBar 範例")](views-images/ProgressBar-Large.png#lightbox "ProgressBar 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合的檢視

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| `CollectionView` 顯示可捲動清單項目可選取的資料，使用不同的版面配置規格。 它旨在提供更多的彈性，以及高效能替代方式，來[ `ListView` ](xref:Xamarin.Forms.ListView)。 設定`ItemsSource`集合的物件和集合的屬性`ItemTemplate`屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述項目為何要格式化的物件。 `SelectionChanged`事件通知，已可進行選取`SelectedItem`屬性。<br /><br />[快速入門](~/xamarin-forms/user-interface/collectionview/index.md) / [範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/) | [![範例 CollectionView](views-images/CollectionView.png "CollectionView 範例")](views-images/CollectionView-Large.png#lightbox "CollectionView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/forms40/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/forms40/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) 衍生自[ `ItemsView` ](xref:Xamarin.Forms.ItemsView`1) ，並顯示可捲動的可選取的資料項目清單。 設定[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性集合的物件和集合[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)物件，描述項目為何若要格式化。 [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件的選取項目發出訊號，可[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) / [範例](https://developer.xamarin.com/samples/WorkingWithListview) | [![ListView 範例](views-images/ListView.png "ListView 範例")](views-images/ListView-Large.png#lightbox "ListView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Picker

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) 顯示選取的項目從清單中的文字字串，並允許時點選檢視時，選取該項目。 設定[ `Items` ](xref:Xamarin.Forms.Picker.Items)屬性的字串清單，或有[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)物件的集合的屬性。 [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)選取項目時，會引發事件。<br /><br />`Picker`只有在已選取時，才會顯示項目清單。 使用[ `ListView` ](#listView)或是[ `TableView` ](#tableView)保留在頁面上的可捲動清單。<br /><br />[API 文件](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![選擇器範例](views-images/Picker.png "選取器範例")](views-images/Picker-Large.png#lightbox "選擇器範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml)使用[程式碼後置](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) 顯示類型的資料列的清單[ `Cell` ](xref:Xamarin.Forms.Cell)選擇性標頭與子標頭。 設定[ `Root` ](xref:Xamarin.Forms.TableView.Root)型別的物件的屬性[ `TableRoot` ](xref:Xamarin.Forms.TableRoot)，並新增[ `TableSection` ](xref:Xamarin.Forms.TableSection)物件， `TableRoot`。 每個`TableSection`的集合`Cell`物件。<br /><br />[API 文件](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) / [範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![TableView 範例](views-images/TableView.png "TableView 範例")](views-images/TableView-Large.png#lightbox "TableView 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
