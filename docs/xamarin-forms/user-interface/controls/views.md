---
title: Xamarin.Forms視圖
description: Xamarin.Formsviews 是跨平臺行動使用者介面的基礎構件。 本文列出所包含的視圖 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd503ce9fd04d80fc0f791131f67f6f1a86ae84a
ms.sourcegitcommit: cd0c0999b53e825b60471bfbfd4144cfcd783587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225623"
---
# <a name="xamarinforms-views"></a>Xamarin.Forms視圖

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Formsviews 是跨平臺行動使用者介面的基礎構件。_

Views 是使用者介面物件，例如標籤、按鈕和滑杆，在其他圖形化程式設計環境中通常稱為*控制項*或*小*工具。 支援的視圖 Xamarin.Forms 全都衍生自 [`View`](xref:Xamarin.Forms.View) 類別。 它們可以分成數個類別：

## <a name="views-for-presentation"></a>簡報的檢視

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)顯示以屬性著色的實心矩形 [`Color`](xref:Xamarin.Forms.BoxView.Color) 。 `BoxView`的預設大小要求為40x40。 針對其他大小，請指派 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性。<br /><br />[API 檔](xref:Xamarin.Forms.BoxView)  / [指南](~/xamarin-forms/user-interface/boxview.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)、 [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)、 [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)、 [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)和[6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![BoxView 範例](views-images/BoxView.png "BoxView 範例")](views-images/BoxView-Large.png#lightbox "BoxView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="ellipse"></a>橢圓形

|     |    |
| --- | ---|
| [`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse)顯示大小為 x 的橢圓形或 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 圓形 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 。 若要在橢圓形內繪製，請將其 [`Fill`](xref:Xamarin.Forms.Shapes.Shape.Fill) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為橢圓形提供外框，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 `Color` 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Ellipse)  / [指南](~/xamarin-forms/user-interface/shapes/ellipse.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![橢圓形範例](views-images/Ellipse.png "橢圓形範例")](views-images/Ellipse-Large.png#lightbox "橢圓形範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EllipseDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EllipseDemoPage.xaml) |
|     |     |

### <a name="expander"></a>展開工具

|     |     |
| --- | --- |
| [`Expander`](xref:Xamarin.Forms.Expander)提供可擴充的容器來裝載任何內容，並由標頭和內容所組成。 將 `Header` 屬性設定為， [`View`](xref:Xamarin.Forms.View) 將會顯示為標頭，並將屬性設為，以便在按下點 `Content` [`View`](xref:Xamarin.Forms.View) 展開標頭時顯示。<br /><br />[API 檔](xref:Xamarin.Forms.Expander)  / [指南](~/xamarin-forms/user-interface/expander.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) | [![展開器範例](views-images/Expander.png "展開器範例")](views-images/Expander-Large.png#lightbox "展開器範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>標籤

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)使用常數或變數格式，顯示單行文字字串或多行文字的區塊。 將 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性設定為常數格式的字串，或將屬性設定為 [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) [`FormattedString`](xref:Xamarin.Forms.FormattedString) 變數格式的物件。<br /><br />[API 檔](xref:Xamarin.Forms.Label)  / [指南](~/xamarin-forms/user-interface/text/label.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![標籤範例](views-images/Label.png "標籤範例")](views-images/Label-Large.png#lightbox "標籤範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="line"></a>折線圖

|     |     |
| --- | --- |
| [`Line`](xref:Xamarin.Forms.Shapes.Line)顯示從起點到結束點的線條。 起始點是由 [`X1`](xref:Xamarin.Forms.Shapes.Line.X1) 和 [`Y1`](xref:Xamarin.Forms.Shapes.Line.Y1) 屬性工作表示，而端點則是由 [`X2`](xref:Xamarin.Forms.Shapes.Line.X2) 和屬性工作表示 [`Y2`](xref:Xamarin.Forms.Shapes.Line.Y2) 。 若要將線條色彩，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Line)  / [指南](~/xamarin-forms/user-interface/shapes/line.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![行範例](views-images/Line.png "行範例")](views-images/Line-Large.png#lightbox "行範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LineDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LineDemoPage.xaml) |
|     |     |

### <a name="image"></a>映像

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)顯示點陣圖。 點陣圖可以透過 Web 下載、內嵌為一般專案或平臺專案中的資源，或使用 .NET `Stream` 物件建立。<br /><br />[API 檔](xref:Xamarin.Forms.Image)  / [指南](~/xamarin-forms/user-interface/images.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![影像範例](views-images/Image.png "影像範例")](views-images/Image-Large.png#lightbox "影像範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>對應

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)顯示地圖。 ** Xamarin.Forms 。** 必須安裝 Maps NuGet 套件。 Android 和通用 Windows 平臺需要對應授權金鑰。<br /><br />[API 檔](xref:Xamarin.Forms.Maps.Map)  / [指南](~/xamarin-forms/user-interface/map/index.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Map 範例](views-images/Map.png "Map 範例")](views-images/Map-Large.png#lightbox "Map 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)播放影片或音訊。 根據 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 屬性設定為或，您可以從 URL 或本機檔案播放媒體 [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 。<br /><br />[API 檔](xref:Xamarin.Forms.MediaElement)  / [指南](~/xamarin-forms/user-interface/mediaelement.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) | [![MediaElement 範例](views-images/MediaElement.png "MediaElement 範例")](views-images/MediaElement-Large.png#lightbox "MediaElement 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)在 iOS 和 Android 專案中顯示 OpenGL 圖形。 通用 Windows 平臺不支援。 IOS 和 Android 專案需要**opentk 簡介-1.0**元件或**opentk 簡介**版本1.0.0.0 元件的參考。 `OpenGLView`在共用的專案中更容易使用;如果用於 .NET Standard 程式庫中，則也會需要相依性服務， (如範例程式碼) 所示。<br /><br />這是內建的唯一圖形設備 Xamarin.Forms ，但 Xamarin.Forms 應用程式也可以使用、或來轉譯圖形 [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md) 。<br /><br />[API 檔](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![OpenGLView 範例](views-images/OpenGLView.png "OpenGLView 範例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) |
|     |     |

### <a name="path"></a>路徑

|     |     |
| --- | --- |
| [`Path`](xref:Xamarin.Forms.Shapes.Path)顯示曲線和複雜圖形。 [`Data`](xref:Xamarin.Forms.Shapes.Path.Data)屬性會指定要繪製的形狀。 若要將圖形色彩，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Path)  / [指南](~/xamarin-forms/user-interface/shapes/path.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![路徑範例](views-images/Path.png "路徑範例")](views-images/Path-Large.png#lightbox "路徑範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PathDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PathDemoPage.xaml) |
|     |     |

### <a name="polygon"></a>多邊形

|     |     |
| --- | --- |
| [`Polygon`](xref:Xamarin.Forms.Shapes.Polygon)顯示多邊形。 [`Points`](xref:Xamarin.Forms.Shapes.Polygon.Points)屬性會指定多邊形的頂點，而 [`FillRule`](xref:Xamarin.Forms.Shapes.Polygon.FillRule) 屬性會指定多邊形內部填滿的決定方式。 若要在多邊形內繪製，請將其 [`Fill`](xref:Xamarin.Forms.Shapes.Shape.Fill) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為多邊形提供外框輪廓，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 `Color` 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Polygon)  / [指南](~/xamarin-forms/user-interface/shapes/polygon.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![多邊形範例](views-images/Polygon.png "多邊形範例")](views-images/Polygon-Large.png#lightbox "多邊形範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PolygonDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PolygonDemoPage.xaml) |
|     |     |

### <a name="polyline"></a>聚合線條

|     |     |
| --- | --- |
| [`Polyline`](xref:Xamarin.Forms.Shapes.Polyline)顯示一系列連接的直線。 [`Points`](xref:Xamarin.Forms.Shapes.Polygon.Points)屬性會指定聚合線條的頂點，而 [`FillRule`](xref:Xamarin.Forms.Shapes.Polygon.FillRule) 屬性則會指定如何決定整個折線的內部填滿。 若要在折線內繪製，請將其 [`Fill`](xref:Xamarin.Forms.Shapes.Shape.Fill) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為折線提供外框，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 `Color` 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Polyline)  / [指南](~/xamarin-forms/user-interface/shapes/polyline.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![聚合線條範例](views-images/Polyline.png "聚合線條範例")](views-images/Polyline-Large.png#lightbox "聚合線條範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PolylineDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PolylineDemoPage.xaml) |
|     |     |

### <a name="rectangle"></a>矩形

|     |     |
| --- | --- |
| [`Rectangle`](xref:Xamarin.Forms.Shapes.Rectangle)顯示矩形或正方形。 若要在矩形內繪製，請將其 [`Fill`](xref:Xamarin.Forms.Shapes.Shape.Fill) 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為矩形提供外框輪廓，請將其 [`Stroke`](xref:Xamarin.Forms.Shapes.Shape.Stroke) 屬性設為 `Color` 。<br /><br />[API 檔](xref:Xamarin.Forms.Shapes.Rectangle)  / [指南](~/xamarin-forms/user-interface/shapes/rectangle.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos) | [![矩形範例](views-images/Rectangle.png "矩形範例")](views-images/Rectangle-Large.png#lightbox "矩形範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RectangleDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RectangleDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)根據 [`Source`](xref:Xamarin.Forms.WebView.Source) 屬性是否設定為 [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) 或物件，顯示網頁或 HTML 內容 [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) 。<br /><br />[API 檔](xref:Xamarin.Forms.WebView)  / [指南](~/xamarin-forms/user-interface/webview.md)  / [範例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Web 視圖範例](views-images/WebView.png "Web 視圖範例")](views-images/WebView-Large.png#lightbox "Web 視圖範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>起始命令的檢視

### <a name="button"></a>按鈕

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)是顯示文字的矩形物件， [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 當按下時，會引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.Button)  / [指南](~/xamarin-forms/user-interface/button.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![按鈕範例](views-images/Button.png "按鈕範例")](views-images/Button-Large.png#lightbox "按鈕範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| [`ImageButton`](xref:Xamarin.Forms.ImageButton)是顯示影像的矩形物件，它會 `Clicked` 在按下時引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.ImageButton)  / [指南](~/xamarin-forms/user-interface/imagebutton.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![ImageButton 範例](views-images/ImageButton.png "ImageButton 範例")](views-images/ImageButton-Large.png#lightbox "ImageButton 範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| [`RadioButton`](xref:Xamarin.Forms.RadioButton)允許從集合中選取一個選項，並 `CheckedChanged` 在選取發生時引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.RadioButton)  / [指南](~/xamarin-forms/user-interface/radiobutton.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) | [![選項按鈕範例](views-images/RadioButton.png "選項按鈕範例")](views-images/RadioButton-Large.png#lightbox "選項按鈕範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| [`RefreshView`](xref:Xamarin.Forms.RefreshView)是一個容器控制項，可提供可滾動內容的提取重新整理功能。 `ICommand`當觸發重新整理 `Command` 時，會執行屬性所定義的，而屬性則會 `IsRefreshing` 指出控制項的目前狀態。<br /><br />[API 檔](xref:Xamarin.Forms.RefreshView)  / [指南](~/xamarin-forms/user-interface/refreshview.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![RefreshView 範例](views-images/RefreshView.png "RefreshView 範例")](views-images/RefreshView-Large.png#lightbox "RefreshView 範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) |
|     |     |

### <a name="searchbar"></a>搜尋列

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)顯示一個區域，讓使用者輸入文字字串，以及一個按鈕 (或鍵盤按鍵，) 以指示應用程式執行搜尋。 [`Text`](xref:Xamarin.Forms.InputView.Text)屬性可讓您存取文字，而事件則 [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) 表示已按下按鈕。<br /><br />[API 檔](xref:Xamarin.Forms.SearchBar)  / [指南](~/xamarin-forms/user-interface/searchbar.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![搜尋列範例](views-images/SearchBar.png "搜尋列範例")](views-images/SearchBar-Large.png#lightbox "搜尋列範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| [`SwipeView`](xref:Xamarin.Forms.SwipeView)是包裝內容專案的容器控制項，可提供滑動手勢所顯示的操作功能表項目。 每個功能表項目都是以表示 `SwipeItem` ，其屬性會在 `Command` 按下 `ICommand` 專案時執行。<br /><br />[API 檔](xref:Xamarin.Forms.SwipeView)  / [指南](~/xamarin-forms/user-interface/swipeview.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![SwipeView 範例](views-images/SwipeView.png "SwipeView 範例")](views-images/SwipeView-Large.png#lightbox "SwipeView 範例")<br /> [此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視

### <a name="checkbox"></a>核取方塊

|     |     |
| --- | --- |
| [`CheckBox`](xref:Xamarin.Forms.CheckBox)允許使用者使用可以檢查或空白的按鈕類型來選取布林值。 `IsChecked`屬性是的狀態 `CheckBox` ，而且 `CheckedChanged` 當狀態變更時，就會引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.CheckBox)  / [指南](~/xamarin-forms/user-interface/checkbox.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![CheckBox 範例](views-images/CheckBox.png "CheckBox 範例")](views-images/CheckBox-Large.png#lightbox "CheckBox 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)允許使用者 `double` 從使用和屬性指定的連續範圍中選取值 [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 。<br /><br />[API 檔](xref:Xamarin.Forms.Slider)  / [指南](~/xamarin-forms/user-interface/slider.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![滑杆範例](views-images/Slider.png "滑杆範例")](views-images/Slider-Large.png#lightbox "滑杆範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Stepper

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)允許使用者 `double` 從以 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 、 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 和屬性指定的累加值範圍中選取值 [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 。<br /><br />[API 檔](xref:Xamarin.Forms.Stepper)   / [指南](~/xamarin-forms/user-interface/stepper.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![分檔器範例](views-images/Stepper.png "分檔器範例")](views-images/Stepper-Large.png#lightbox "分檔器範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>參數

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)採用開啟/關閉參數的形式，讓使用者選取布林值。 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)屬性是參數的狀態，而 [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 當狀態變更時，就會引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.Switch)  / [指南](~/xamarin-forms/user-interface/switch.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![切換範例](views-images/Switch.png "切換範例")](views-images/Switch-Large.png#lightbox "切換範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)允許使用者選取具有「平臺日期選擇器」的日期。 使用和屬性來設定允許的日期 [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 範圍 [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 。 [`Date`](xref:Xamarin.Forms.DatePicker.Date)屬性是選取的日期， [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) 當該屬性變更時，就會引發事件。<br /><br />[API 檔](xref:Xamarin.Forms.DatePicker)  / [指南](~/xamarin-forms/user-interface/datepicker.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![DatePicker 範例](views-images/DatePicker.png "DatePicker 範例")](views-images/DatePicker-Large.png#lightbox "DatePicker 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)可讓使用者使用平臺時間選擇器來選取時間。 [`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性為選取的時間。 應用程式可以藉由安裝事件的處理常式來監視屬性中的變更 `Time` [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 。<br /><br />[API 檔](xref:Xamarin.Forms.TimePicker)  / [指南](~/xamarin-forms/user-interface/timepicker.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![TimePicker 範例](views-images/TimePicker.png "TimePicker 範例")](views-images/TimePicker-Large.png#lightbox "TimePicker 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

這兩個類別衍生自 [`InputView`](xref:Xamarin.Forms.InputView) 定義屬性的類別 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 。

### <a name="entry"></a>進入

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)允許使用者輸入和編輯單行文字。 文字會以屬性的形式提供 [`Text`](xref:Xamarin.Forms.InputView.Text) ，而 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) [`Completed`](xref:Xamarin.Forms.Entry.Completed) 當文字變更或使用者藉由按下 enter 鍵來指示完成時，就會引發和事件。<br /><br />使用 [`Editor`](#editor) 來輸入及編輯多行文字。<br /><br />[API 檔](xref:Xamarin.Forms.Entry)  / [指南](~/xamarin-forms/user-interface/text/entry.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![專案範例](views-images/Entry.png "專案範例")](views-images/Entry-Large.png#lightbox "專案範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>編輯器

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)允許使用者輸入和編輯多行文字。 文字可當做 [`Text`](xref:Xamarin.Forms.InputView.Text) 屬性使用，而 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) [`Completed`](xref:Xamarin.Forms.Editor.Completed) 當文字變更或使用者指示完成時，會引發和事件。<br /><br />使用 [`Entry`](#entry) view 來輸入和編輯單行文字。<br /><br />[API 檔](xref:Xamarin.Forms.Editor)  / [指南](~/xamarin-forms/user-interface/text/editor.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![專案範例](views-images/Editor.png "編輯器範例")](views-images/Editor-Large.png#lightbox "編輯器範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>表示活動的檢視

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)使用動畫顯示應用程式正在進行長時間的活動，而不會提供進度的指示。 [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)屬性會控制動畫。<br /><br />如果已知道活動的進度，請改用 [`ProgressBar`](#progressbar) 。<br /><br />[API 檔](xref:Xamarin.Forms.ActivityIndicator)  / [指南](~/xamarin-forms/user-interface/activityindicator.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![ActivityIndicator 範例](views-images/ActivityIndicator.png "ActivityIndicator 範例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)使用動畫來顯示應用程式正在經歷冗長的活動。 將 [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) 屬性設定為0到1之間的值，以表示進度。<br /><br />如果活動的進度未知，請改用 [`ActivityIndicator`](#activityindicator) 。<br /><br />[API 檔](xref:Xamarin.Forms.ProgressBar)  / [指南](~/xamarin-forms/user-interface/progressbar.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![ProgressBar 範例](views-images/ProgressBar.png "ProgressBar 範例")](views-images/ProgressBar-Large.png#lightbox "ProgressBar 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合的檢視

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)顯示資料項目目的可滾動清單。 將 `ItemsSource` 屬性設定為物件的集合，並將屬性設定 `ItemTemplate` 為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何格式化專案的物件。 `CurrentItemChanged`事件表示目前顯示的專案已變更，這是以屬性的形式提供 `CurrentItem` 。<br /><br />[指南](~/xamarin-forms/user-interface/carouselview/index.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![CarouselView 範例](views-images/CarouselView.png "CarouselView 範例")](views-images/CarouselView-Large.png#lightbox "CarouselView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)使用不同的版面配置規格，顯示可選取資料項目的可滾動清單。 它的目的是要提供更具彈性且更高效能的替代方案 [`ListView`](xref:Xamarin.Forms.ListView) 。 將 `ItemsSource` 屬性設定為物件的集合，並將屬性設定 `ItemTemplate` 為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何格式化專案的物件。 `SelectionChanged`事件會指示已進行選取，這會以屬性的形式提供 `SelectedItem` 。<br /><br />[指南](~/xamarin-forms/user-interface/collectionview/index.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![CollectionView 範例](views-images/CollectionView.png "CollectionView 範例")](views-images/CollectionView-Large.png#lightbox "CollectionView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| [`IndicatorView`](xref:Xamarin.Forms.IndicatorView)顯示表示中專案數的指標 `CarouselView` 。 將 `CarouselView.IndicatorView` 屬性設定為 `IndicatorView` 物件，以顯示的指標 `CarouselView` 。<br /><br />[API 檔](xref:Xamarin.Forms.IndicatorView)  / [指南](~/xamarin-forms/user-interface/indicatorview.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) | [![IndicatorView 範例](views-images/IndicatorView.png "IndicatorView 範例")](views-images/IndicatorView-Large.png#lightbox "IndicatorView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)衍生自 [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) 並顯示可選取資料項目的可滾動清單。 將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為物件的集合，並將屬性設定 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何格式化專案的物件。 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件會指示已進行選取，這會以屬性的形式提供 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 。<br /><br />[API 檔](xref:Xamarin.Forms.ListView)  / [指南](~/xamarin-forms/user-interface/listview/index.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![ListView 範例](views-images/ListView.png "ListView 範例")](views-images/ListView-Large.png#lightbox "ListView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Picker

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)從文字字串清單中顯示選取的專案，並允許在按下視圖時選取該專案。 將 [`Items`](xref:Xamarin.Forms.Picker.Items) 屬性設為字串清單，或將屬性設定為 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 物件的集合。 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)選取專案時，就會引發事件。<br /><br />`Picker`只有在選取時，才會顯示專案清單。 [`ListView`](#listview) [`TableView`](#tableview) 針對保留在頁面上的可滾動清單，使用或。<br /><br />[API 檔](xref:Xamarin.Forms.Picker)  / [指南](~/xamarin-forms/user-interface/picker/index.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![選擇器範例](views-images/Picker.png "選擇器範例")](views-images/Picker-Large.png#lightbox "選擇器範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs)  /  的 c # 程式碼具有[程式碼後](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs)置的[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)[`Cell`](xref:Xamarin.Forms.Cell)以選擇性標頭和 subheaders 顯示類型的資料列清單。 將 [`Root`](xref:Xamarin.Forms.TableView.Root) 屬性設定為類型的物件 [`TableRoot`](xref:Xamarin.Forms.TableRoot) ，並將物件加入 [`TableSection`](xref:Xamarin.Forms.TableSection) 至其中 `TableRoot` 。 每個 `TableSection` 都是物件的集合 `Cell` 。<br /><br />[API 檔](xref:Xamarin.Forms.TableView)  / [指南](~/xamarin-forms/user-interface/tableview.md)  / [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![TableView 範例](views-images/TableView.png "TableView 範例")](views-images/TableView-Large.png#lightbox "TableView 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.FormsFormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsAPI 檔](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
