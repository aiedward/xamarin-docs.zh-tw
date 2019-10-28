---
title: Xamarin. 表單檢視
description: '[Xamarin] 是跨平臺行動使用者介面的基礎構件。 本文列出 Xamarin 中包含的視圖。'
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/11/2019
ms.openlocfilehash: bc46d9ee083cca7eacc354846eec6e582070bb9e
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749827"
---
# <a name="xamarinforms-views"></a>Xamarin. 表單檢視

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_[Xamarin] 是跨平臺行動使用者介面的基礎構件。_

檢視是使用者介面物件，例如標籤、 按鈕和滑桿，一般稱為*控制項*或是*widget*其他圖形化的程式設計環境中。 支援的所有衍生自的 Xamarin.Forms 檢視[ `View` ](xref:Xamarin.Forms.View)類別。 它們可以分成數個類別：

## <a name="views-for-presentation"></a>檢視簡報

### <a name="label"></a>ThisAddIn

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)會顯示單行文字字串或文字的多行區塊，其中可能包含常數或變數格式。 將[`Text`](xref:Xamarin.Forms.Label.Text)屬性設為字串，以進行固定格式設定，或將[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)屬性設定為用於變數格式的[`FormattedString`](xref:Xamarin.Forms.FormattedString)物件。<br /><br />[API 檔](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![標籤範例](views-images/Label.png "標籤範例")](views-images/Label-Large.png#lightbox "標籤範例")<br /> 此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)會顯示點陣圖。 點陣圖可以透過 Web 下載、內嵌為一般專案或平臺專案中的資源，或使用 .NET `Stream` 物件建立。<br /><br />[API 檔](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![影像範例](views-images/Image.png "影像範例")](views-images/Image-Large.png#lightbox "影像範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)會顯示以[`Color`](xref:Xamarin.Forms.BoxView.Color)屬性著色的實心矩形。 `BoxView` 的預設大小要求為40x40。 若為其他大小，請指派[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)並[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性。<br /><br />[API 檔](xref:Xamarin.Forms.BoxView) / [指南](~/xamarin-forms/user-interface/boxview.md) / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)、 [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)、 [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)、 [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)和[6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![BoxView 範例](views-images/BoxView.png "BoxView 範例")](views-images/BoxView-Large.png#lightbox "BoxView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)會根據[`Source`](xref:Xamarin.Forms.WebView.Source)屬性設定為[`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource)或[`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource)物件，顯示網頁或 HTML 內容。<br /><br />[API 檔](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Web 視圖範例](views-images/WebView.png "Web 視圖範例")](views-images/WebView-Large.png#lightbox "Web 視圖範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)會在 IOS 和 Android 專案中顯示 OpenGL 圖形。 通用 Windows 平臺不支援。 IOS 和 Android 專案需要**opentk 簡介-1.0**元件或**opentk 簡介**版本1.0.0.0 元件的參考。 `OpenGLView` 在共用的專案中更容易使用;如果用於 .NET Standard 程式庫中，則也會需要相依性服務（如範例程式碼所示）。<br /><br />這是內建于 Xamarin 的圖形設備，但是 Xamarin. Forms 應用程式也可以使用[`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)或[`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md)來呈現圖形。<br /><br />[API 文件](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![OpenGLView 範例](views-images/OpenGLView.png "OpenGLView 範例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) |
|     |     |

### <a name="map"></a>地圖

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)會顯示地圖。 必須安裝**Xamarin. Maps** Nuget 套件。 Android 和通用 Windows 平臺需要對應授權金鑰。<br /><br />[API 檔](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map/index.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Map 範例](views-images/Map.png "Map 範例")](views-images/Map-Large.png#lightbox "Map 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) |
|     |     |

## <a name="views-that-initiate-commands"></a>啟動命令的檢視

### <a name="button"></a>按鈕

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)是一個矩形物件，它會顯示文字，而當按下時，就會引發[`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件。<br /><br />[API 檔](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![按鈕範例](views-images/Button.png "按鈕範例")](views-images/Button-Large.png#lightbox "按鈕範例")<br /> 此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` 是一個矩形物件，它會顯示影像，並在按下時引發 `Clicked` 事件。<br /><br /> [指南](~/xamarin-forms/user-interface/imagebutton.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![ImageButton 範例](views-images/ImageButton.png "ImageButton 範例")](views-images/ImageButton-Large.png#lightbox "ImageButton 範例")<br /> 此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` 是一個容器控制項，可提供可滾動內容的提取重新整理功能。 當觸發重新整理時，會執行 `Command` 屬性所定義的 `ICommand`，而 `IsRefreshing` 屬性則表示控制項的目前狀態。<br /><br /> [指南](~/xamarin-forms/user-interface/refreshview.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![RefreshView 範例](views-images/RefreshView.png "RefreshView 範例")](views-images/RefreshView-Large.png#lightbox "RefreshView Example")<br /> 此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) |
|     |     |

### <a name="searchbar"></a>搜尋列

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)會顯示使用者輸入文字字串的區域，以及指示應用程式執行搜尋的按鈕（或鍵盤按鍵）。 [ [`Text`](xref:Xamarin.Forms.SearchBar.Text) ] 屬性會提供文字的存取權，而 [ [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) ] 事件則表示已按下按鈕。<br /><br />[API 檔](xref:Xamarin.Forms.SearchBar) / [指南](~/xamarin-forms/user-interface/searchbar.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![搜尋列範例](views-images/SearchBar.png "搜尋列範例")](views-images/SearchBar-Large.png#lightbox "搜尋列範例")<br /> 此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視

### <a name="checkbox"></a>核取方塊

|     |     |
| --- | --- |
| `CheckBox` 可讓使用者使用可以檢查或空白的按鈕類型來選取布林值。 `IsChecked` 屬性是 `CheckBox`的狀態，而 `CheckedChanged` 事件則會在狀態變更時引發。<br /><br />API 檔/[指南](~/xamarin-forms/user-interface/checkbox.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![CheckBox 範例](views-images/CheckBox.png "CheckBox 範例")](views-images/CheckBox-Large.png#lightbox "CheckBox 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) |
|     |     |

### <a name="slider"></a>滑桿

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)可讓使用者從以[`Minimum`](xref:Xamarin.Forms.Slider.Minimum)和[`Maximum`](xref:Xamarin.Forms.Slider.Maximum)屬性指定的連續範圍中選取 `double` 值。<br /><br />[API 檔](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![滑杆範例](views-images/Slider.png "滑杆範例")](views-images/Slider-Large.png#lightbox "滑杆範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) |
|     |     |

### <a name="stepper"></a>Stepper

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)可讓使用者從以[`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)、 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)和[`Increment`](xref:Xamarin.Forms.Stepper.Increment)屬性指定的增量值範圍中選取 `double` 值。<br /><br />[API 檔](xref:Xamarin.Forms.Stepper)  / [指南](~/xamarin-forms/user-interface/stepper.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![分檔器範例](views-images/Stepper.png "分檔器範例")](views-images/Stepper-Large.png#lightbox "分檔器範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) |
|     |     |

### <a name="switch"></a>參數

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)採用開啟/關閉開關的形式，讓使用者選取布林值。 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)屬性是參數的狀態，而當狀態變更時，就會引發[`Toggled`](xref:Xamarin.Forms.Switch.Toggled)事件。<br /><br />[API 檔](xref:Xamarin.Forms.Switch) / [指南](~/xamarin-forms/user-interface/switch.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![切換範例](views-images/Switch.png "切換範例")](views-images/Switch-Large.png#lightbox "切換範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)允許使用者選取具有「平臺日期選擇器」的日期。 使用 [ [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) ] 和 [ [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) ] 屬性來設定允許的日期範圍。 [`Date`](xref:Xamarin.Forms.DatePicker.Date)屬性是選取的日期，而當該屬性變更時，就會引發[`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected)事件。<br /><br />[API 檔](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![DatePicker 範例](views-images/DatePicker.png "DatePicker 範例")](views-images/DatePicker-Large.png#lightbox "DatePicker 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)可讓使用者使用平臺時間選擇器來選取時間。 [`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性是選取的時間。 應用程式可以藉由安裝[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件的處理常式，監視 `Time` 屬性中的變更。<br /><br />[API 檔](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![TimePicker 範例](views-images/TimePicker.png "TimePicker 範例")](views-images/TimePicker-Large.png#lightbox "TimePicker 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

這兩個類別衍生自[`InputView`](xref:Xamarin.Forms.InputView)類別，這會定義[`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)屬性。

<a name="entry" />

### <a name="entry"></a>進入

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)可讓使用者輸入和編輯一行文字。 文字會以[`Text`](xref:Xamarin.Forms.Entry.Text)屬性的形式提供，而當文字變更或使用者藉由按下 enter 鍵來指示完成時，就會引發[`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged)和[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件。<br /><br />使用[`Editor`](#editor)來輸入和編輯多行文字。<br /><br />[API 檔](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![專案範例](views-images/Entry.png "專案範例")](views-images/Entry-Large.png#lightbox "專案範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) |
|     |     |

<a name="editor" />

### <a name="editor"></a>編輯器

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)可讓使用者輸入和編輯多行文字。 文字會以[`Text`](xref:Xamarin.Forms.Editor.Text)屬性的形式提供，而當文字變更或使用者表示完成時， [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged)和[`Completed`](xref:Xamarin.Forms.Editor.Completed)事件就會引發。<br /><br />使用[`Entry`](#entry) view 來輸入和編輯單行文字。<br /><br />[API 檔](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![專案範例](views-images/Editor.png "編輯器範例")](views-images/Editor-Large.png#lightbox "編輯器範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) |
|     |     |

## <a name="views-to-indicate-activity"></a>表示活動的檢視

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)使用動畫來顯示應用程式正在進行長時間的活動，而不會提供進度的指示。 [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)屬性控制動畫。<br /><br />如果知道活動的進度，請改用[`ProgressBar`](#progressbar) 。<br /><br />[API 檔](xref:Xamarin.Forms.ActivityIndicator) / [指南](~/xamarin-forms/user-interface/activityindicator.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![ActivityIndicator 範例](views-images/ActivityIndicator.png "ActivityIndicator 範例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>進度列

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)使用動畫來顯示應用程式正在經歷冗長的活動。 將 [ [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) ] 屬性設為0到1之間的值，以表示進度。<br /><br />如果不知道活動的進度，請改用[`ActivityIndicator`](#activityindicator) 。<br /><br />[API 檔](xref:Xamarin.Forms.ProgressBar) / [指南](~/xamarin-forms/user-interface/progressbar.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![ProgressBar 範例](views-images/ProgressBar.png "ProgressBar 範例")](views-images/ProgressBar-Large.png#lightbox "ProgressBar 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合的檢視

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)會顯示資料項目目的可滾動清單。 將 [`ItemsSource`] 屬性設定為物件的集合，並將 [`ItemTemplate`] 屬性設定為描述如何格式化專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。 `CurrentItemChanged` 事件會指出目前顯示的專案已變更，這會以 `CurrentItem` 屬性的形式提供。<br /><br />[指南](~/xamarin-forms/user-interface/carouselview/index.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![CarouselView 範例](views-images/CarouselView.png "CarouselView 範例")](views-images/CarouselView-Large.png#lightbox "CarouselView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)會使用不同的版面配置規格，顯示可選取資料項目的可滾動清單。 它的目的是要提供更具彈性且高效能的替代方案，以[`ListView`](xref:Xamarin.Forms.ListView)。 將 [`ItemsSource`] 屬性設定為物件的集合，並將 [`ItemTemplate`] 屬性設定為描述如何格式化專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。 `SelectionChanged` 事件表示已進行選取，這會以 `SelectedItem` 屬性的形式提供。<br /><br />[指南](~/xamarin-forms/user-interface/collectionview/index.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![CollectionView 範例](views-images/CollectionView.png "CollectionView 範例")](views-images/CollectionView-Large.png#lightbox "CollectionView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)衍生自[`ItemsView`](xref:Xamarin.Forms.ItemsView`1) ，並顯示可選取資料項目的可滾動清單。 將 [ [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) ] 屬性設定為物件的集合，並將 [ [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) ] 屬性設定為描述如何格式化專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件表示已進行選取，這會以[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性的形式提供。<br /><br />[API 檔](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![ListView 範例](views-images/ListView.png "ListView 範例")](views-images/ListView-Large.png#lightbox "ListView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) |
|     |     |

### <a name="picker"></a>Picker

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)會從文字字串清單中顯示選取的專案，並允許在按下時選取該專案。 將 [ [`Items`](xref:Xamarin.Forms.Picker.Items) ] 屬性設為字串清單，或將 [ [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) ] 屬性設定為物件的集合。 選取專案時，就會引發[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件。<br /><br />`Picker` 只會在選取時顯示專案清單。 針對保留在頁面上的可滾動清單，使用[`ListView`](#listView)或[`TableView`](#tableView) 。<br /><br />[API 檔](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![選擇器範例](views-images/Picker.png "選擇器範例")](views-images/Picker-Large.png#lightbox "選擇器範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml)和[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs)置[ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)會以選擇性的標頭和 subheaders 顯示[`Cell`](xref:Xamarin.Forms.Cell)類型的資料列清單。 將[`Root`](xref:Xamarin.Forms.TableView.Root)屬性設定為[`TableRoot`](xref:Xamarin.Forms.TableRoot)類型的物件，並將[`TableSection`](xref:Xamarin.Forms.TableSection)物件加入該 `TableRoot`。 每個 `TableSection` 都是 `Cell` 物件的集合。<br /><br />[API 檔](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![TableView 範例](views-images/TableView.png "TableView 範例")](views-images/TableView-Large.png#lightbox "TableView 範例")<br />此頁面的程式碼  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
