---
title: 第13章摘要。 點陣圖
description: 使用 Xamarin.表單創建行動應用程式:第 13 章摘要。 點陣圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292406"
---
# <a name="summary-of-chapter-13-bitmaps"></a>第13章摘要。 點陣圖

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

Xamarin.Forms[`Image`](xref:Xamarin.Forms.Image)元素顯示點陣圖。 所有 Xamarin.Forms 平臺都支援 JPEG、PNG、GIF 和 BMP 檔案格式。

Xamarin.表單中的位圖來自四個位置:

- 網址指定的 Web 上
- 為資源嵌入分享庫中
- 為資源嵌入到平台應用程式項目中
- 從可以引用的任何地方,包括`Stream``MemoryStream`

共用庫中的位圖資源與平台無關,而平臺專案中的位圖資源特定於平臺。

> [!NOTE]
> 本書的文本引用了便攜式類庫,這些庫已被 .NET 標準庫替換。 書中的所有範例代碼已轉換為使用 .NET 標準庫。

點陣圖是透過設定的[`Source`](xref:Xamarin.Forms.Image.Source)`Image`屬性 設定[`ImageSource`](xref:Xamarin.Forms.ImageSource)為類型 物件(具有三個導數的抽象類別)來指定的:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)基於`Uri`物件集到[`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)其 屬性的 Web 上存取點陣圖
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)用於存取基於資料夾與檔案路徑設定為[`File`](xref:Xamarin.Forms.FileImageSource.File)其 屬性的平台應用程式項目中儲存的點陣圖
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)用於使用`Stream`.NET 物件載入點陣圖,`Stream`該`Func`物件來從集[`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)傳回屬性而指定

或者(更常見)可以使用`ImageSource`類的以下靜態方法,所有這些方法都`ImageSource`返回 物件:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))用於基於`Uri`物件在 Web 上存取點陣圖
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)用於存取存儲在應用程式 PCL 中作為嵌入式資源的點陣圖;[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))或[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly))存取另一個來源程式集中的點陣圖
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))從平台應用程式項目存取點陣圖
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))以基於物件載入`Stream`點陣圖

沒有類別等效的方法`Image.FromResource`。 如果需要`UriImageSource`控制緩存,則類很有用。 該`FileImageSource`類在 XAML 中很有用。 `StreamImageSource`可用於物件的異步載入,而`Stream``ImageSource.FromStream`是同步的。

## <a name="platform-independent-bitmaps"></a>與平台無關的點陣圖

[**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)專案`ImageSource.FromUri`使用 在 Web 上載入點陣圖。 元素`Image`設置為`Content``ContentPage`的屬性 ,因此它受限制為頁面的大小。 無論位圖的大小如何,約束`Image`元素都會拉伸到其容器的大小,並且位圖`Image`在 保持位貼圖的縱橫比的同時以其最大大小顯示在元素內。 `Image`位圖以外的區域可以使用[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)著色。

[**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)範例類似,但只需`Source`將 屬性設置到網址即可。 轉換由[`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter)類處理。

### <a name="fit-and-fill"></a>適合與填充

通過將`Image`的 屬性[`Aspect`](xref:Xamarin.Forms.Aspect)設定為 枚舉的[`Aspect`](xref:Xamarin.Forms.Image.Aspect)以下成員之 一,可以控制位圖的拉伸方式:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): 尊重縱橫比(預設)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): 填充面積,不尊重縱橫比
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill):填充面積,但尊重縱橫比,通過裁剪位圖的一部分來完成

### <a name="embedded-resources"></a>嵌入式資源

您可以將點陣圖檔加入 PCL 或 PCL 中的資料夾。 給它一個**嵌入式資源的****建構操作**。 [**資源點映射代碼**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)範例展示如何`ImageSource.FromResource`使用 來載入檔。 傳遞給方法的資源名稱由程式集名稱(後跟點)組成,後跟可選資料夾名稱和點,後跟檔名。

程式設定`VerticalOptions``HorizontalOptions`的`Image``LayoutOptions.Center`和`Image`屬性, 使 元素不受限制。 與`Image`點陣圖的大小相同:

- 在 iOS`Image`和 Android 上, 是位圖的圖元大小。 位圖圖元和螢幕圖元之間存在一對一映射。
- 在通用 Windows`Image`平臺上 ,是位圖的圖元大小(以設備無關的單位)。 在大多數設備上,每個位圖圖元都佔用多個螢幕圖元。

[**堆疊點圖**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)範例在 XAML`Image``StackLayout`中將 的垂直中放入 。 名為標記[`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)擴展有助於引用 XAML 中的嵌入資源。 此類僅從其所在的程式集載入資源,因此無法將其放置在庫中。

### <a name="more-on-sizing"></a>有關大小調整的更多

通常,在所有平臺中一致地調整位圖大小是可取的。
試驗**StackedBitmap**,您`WidthRequest``Image``StackLayout`可以在垂直 元素上設置 ,以使大小在平台之間一致,但只能使用此技術減小大小。

您還可以設置`HeightRequest`以使圖像大小在平臺上保持一致,但位圖的限制寬度將限制此技術的多功能性。 對於垂直`StackLayout`影像,`HeightRequest`應避免使用。

最佳方法是從與設備無關的單位比電話寬度寬的位圖開始,並在與設備無關的單元`WidthRequest`中設置為所需的寬度。 這在[**設備 IndBitMapSize 範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)中演示。

[**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)展出了劉易斯·卡羅爾的 *《愛麗絲夢遊仙境》* 第7章,以及約翰·特尼爾的原始插圖:

[![瘋狂茶黨的三重截圖](images/ch13fg16-small.png "瘋帽子茶黨書文字")](images/ch13fg16-large.png#lightbox "瘋帽子茶黨書文字")

### <a name="browsing-and-waiting"></a>瀏覽並等待

[**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)範例允許使用者流覽儲存在 Xamarin 網站上的庫存圖像。 它使用 .NET[`WebRequest`](xref:System.Net.WebRequest)類下載包含點陣清單的 JSON 檔。

> [!NOTE]
> Xamarin.Forms 程式[`HttpClient`](xref:System.Net.Http.HttpClient)應該[`WebRequest`](xref:System.Net.WebRequest)使用, 而不是在網路上存取檔。

程式使用[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 當每個點陣圖載入時,的[`IsLoading`](xref:Xamarin.Forms.Image.IsLoading)`Image`唯讀屬性為`true`。 該`IsLoading`屬性由可綁定屬性支援,因此當該`PropertyChanged`屬性 更改時將觸發事件。 程式將處理程式附加到此事件,並使用的`IsLoaded`當前設置來[`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)設置 的`ActivityIndicator`屬性。

## <a name="streaming-bitmaps"></a>流式處理點陣圖

該方法`ImageSource.FromStream`建立`ImageSource`基於`Stream`.NET 的 。 方法必須傳遞返回`Func``Stream`物件的物件。

### <a name="accessing-the-streams"></a>存取流

[**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)範例示範如何`ImaageSource.FromStream`使用 方法載入儲存為嵌入資源的點陣圖,以及如何在 Web 上載入點陣圖。

### <a name="generating-bitmaps-at-run-time"></a>在執行時產生點陣圖

所有 Xamarin.Forms 平台都支援未壓縮的 BMP 檔案格式,該格式易於在代碼中建構`MemoryStream`,然後存儲在中。 此技術允許在運行時以演演演算法方式創建位圖[`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs),如在**Xamrin.FormsBook.Toolkit**庫中的類中實現的那樣。

"自己動手[**"DiyGradientBitmap 範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)示範`BmpMaker`使用使用 使用 漸變圖像創建點陣圖。

## <a name="platform-specific-bitmaps"></a>特定於平台的點陣圖

所有 Xamarin.Forms 平臺都允許在平台應用程式程式集中儲存位圖。 當 Xamarin.Forms 應用程式檢索時,這些平台點陣[`FileImageSource`](xref:Xamarin.Forms.FileImageSource)圖的類型為 。 您可以使用它們進行:

- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)的財產[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)的財產[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- [`Image`](xref:Xamarin.Forms.Button)的財產`Button`

平台程式集已包含圖示與初始螢幕的點陣圖:

- 在 iOS 專案中,在 **「資源」** 資料夾中
- 在 Android 專案中,在 **「資源」** 資料夾的子資料夾中
- 在 Windows 專案中,在 **「資源」** 資料夾中(儘管 Windows 平台不將位映射限制到該資料夾)

[**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)示例使用代碼來顯示平臺應用程式專案中的圖示。

### <a name="bitmap-resolutions"></a>點陣圖解析度

所有平臺都允許存儲多個版本的位圖圖像,以用於不同的設備解析度。 在運行時,根據屏幕的設備解析度載入正確的版本。

在 iOS 上,這些位圖通過檔名上的後綴進行區分:

- 160 個 DPI 裝置沒有後置字串(與裝置無關的單元為 1 像素)
- '@2x' 320 DPI 裝置的後綴(DIU 為 2 像素)
- '@3x' 480 DPI 裝置的後綴(DIU 為 3 像素)

以顯示為一英吋的正方形的點陣圖會存在於三個版本中:

- MyImage.jpg 在 160 像素的正方形
- MyImage@2x.jpg在 320 像素的正方形
- MyImage@3x.jpg在 480 像素的正方形

程式會將此位圖稱為 MyImage.jpg,但根據螢幕的解析度在運行時檢索正確的版本。 當不受約束時,位圖將始終呈現在 160 個與設備無關的單位。

對於 Android,位元圖儲存在 **「資源」** 資料夾的各個子資料夾中:

- 可繪製 ldpi(低 DPI)適用於 120 個 DPI 裝置(DU 為 0.75 像素)
- 可繪製 mdpi(中等)適用於 160 個 DPI 裝置(1 像素到 DIU)
- 可繪製 hdpi(高)適用於 240 個 DPI 裝置(DIU 為 1.5 像素)
- 可繪製-xhdpi(超高)320 DPI 裝置(2 像素到 DIU)
- 可繪製-xxhdpi(超高)適用於 480 個 DPI 裝置(DIU 為 3 圖元)
- 可繪製-xxxhdpi(3 個額外高點),適用於 640 個 DPI 裝置(DIU 為 4 圖元)

對於打算以一平方英寸渲染的位圖,位圖的各種版本將具有相同的名稱,但大小不同,並存儲在這些資料夾中:

- 可繪製-ldpi/MyImage.jpg,在120圖元的正方形
- 可繪製的 mdpi/MyImage.jpg 在 160 像素的正方形
- 可繪製 hdpi/MyImage.jpg,在 240 像素的正方形
- 可繪製-xhdpi/MyImage.jpg 在 320 像素的正方形
- 可繪製-xxhdpi/MyImage.jpg,在480圖元的正方形
- 可繪製-xxxhdpi/MyImage.jpg 在 640 像素的正方形

位圖始終以 160 個與設備無關的單位呈現。 (標準 Xamarin.Forms 解決方案範本僅包括 hdpi、xhdpi 和 xxhdpi 資料夾。

UWP 專案支援位圖命名方案,該方案由每個獨立於裝置的單位(以百分比為單位)的縮放因數組成,例如:

- MyImage.scale-200.jpg,在320圖元的正方形

只有某些百分比有效。 本書的範例程式僅包括具有**Scale-200**後綴的影像,但當前的 Xamarin.Forms 解決方案範本包括**scale-100,****比例-125、****縮放-150**和**scale-400。**

向平台專案新增位圖時,**產生操作**應為:

- iOS:**捆綁資源**
- 安卓系統:**安卓資源**
- UWP:**內容**

[**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)範例建立兩個類似於按鈕的物件`Image`,`TapGestureRecognizer`由已安裝的元素組成。 物件為一英寸正方形。 的屬性`Source``Image`設定為`OnPlatform``On`使用和物件來引用平臺上可能不同的檔名。 圖圖像包括指示其圖元大小的數位,因此您可以看到檢索和渲染的大小位圖。

### <a name="toolbars-and-their-icons"></a>工具列與圖示

特定於平臺的位圖的主要用途之一是 Xamarin.Forms 工具列,該工具列是通過將物件[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)添加到[`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems)定義的 集合`Page`而構建的。 `ToobarItem`派生自[`MenuItem`](xref:Xamarin.Forms.MenuItem)它繼承某些屬性。

最重要的`ToolbarItem`屬性是:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)對於可能根據平臺和`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)影像的類型`FileImageSource`,可能根據平台和`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)類型[`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder),具有三個成員[`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default)的 枚[`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)舉[`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary), 和 。

項目數量`Primary`應限於三個或四個。 應包含所有項目的`Text`設置。 對於大多數平臺,`Primary`只有專案需要`Icon`, 但 Windows`Icon`8.1 需要所有專案。 圖示應為 32 個與設備無關的單位正方形。 類型`FileImageSource`指示它們是特定於平臺的。

點擊`ToolbarItem`時觸[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)發 事件,很`Button`像 。 `ToolbarItem`還支援[`Command`](xref:Xamarin.Forms.MenuItem.Command)和[`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)屬性通常用於與 MVVM 有關。 (參見[第 18 章 MVVM](chapter18.md))。

iOS 和 Android 都要求顯示工具列[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)的頁面是 導航`NavigationPage`到 的 頁面。 [**工具列Demo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)`MainPage`程式`App``ContentPage`使用 參數將類的屬性設置給[`NavigationPage`建構函數](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)),並展示工具列的建構和事件處理程式。

### <a name="button-images"></a>按鈕影像

您還可以使用特定於平臺的位圖將 的[`Image`](xref:Xamarin.Forms.Button.Image)`Button`屬性 設置為 32 個與設備無關的單位平方的位圖,如[**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)範例所示。

> [!NOTE]
> 按鈕上圖像的使用得到了增強。 請參考[使用帶按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相關連結

- [第13章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第13章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用影像](~/xamarin-forms/user-interface/images.md)
- [使用帶按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
