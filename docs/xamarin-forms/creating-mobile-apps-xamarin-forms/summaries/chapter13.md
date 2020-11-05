---
title: 第13章的摘要。 點陣圖
description: 使用：第 Xamarin.Forms 13 章的摘要建立 Mobile Apps。 點陣圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 011ef7c73e756c938589124676fa3d9c14a3b556
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374834"
---
# <a name="summary-of-chapter-13-bitmaps"></a>第13章的摘要。 點陣圖

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) 元素會顯示點陣圖。 所有 Xamarin.Forms 平臺都支援 JPEG、PNG、GIF 和 BMP 檔案格式。

中的點陣圖 Xamarin.Forms 來自四個位置：

- 透過 URL 指定的網路
- 內嵌為共用程式庫中的資源
- 內嵌為平臺應用程式專案中的資源
- 從任何可由 .NET 物件參考的位置 `Stream` ，包括 `MemoryStream`

共用程式庫中的點陣圖資源與平臺無關，而平臺專案中的點陣圖資源則是平臺特定的。

> [!NOTE]
> 書籍的文字會參考便攜類別庫，而這些類別庫已由 .NET Standard 程式庫取代。 書籍中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

點陣圖是藉由將的 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性設定 `Image` 為類型的物件 [`ImageSource`](xref:Xamarin.Forms.ImageSource) （具有三個衍生物件的抽象類別）來指定：

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)根據 `Uri` 設定為其屬性的物件，在 web 上存取點陣圖 [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 存取儲存在平臺應用程式專案中的點陣圖（根據資料夾和檔案路徑設定為其 [`File`](xref:Xamarin.Forms.FileImageSource.File) 屬性）
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) 使用指定的 .NET 物件載入點陣圖， `Stream` 方法是 `Stream` 將從集合中傳回的 `Func` 設定為其 [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) 屬性。

或者 (和更常見的) 您可以使用類別的下列靜態方法 `ImageSource` ，而這些方法全都會傳回 `ImageSource` 物件：

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))以物件為基礎，透過 web 存取點陣圖 `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 存取儲存為應用程式 PCL 中內嵌資源的點陣圖; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) 或 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) 存取另一個來源元件中的點陣圖
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) 從平臺應用程式專案存取點陣圖
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))用於根據物件載入點陣圖 `Stream`

沒有方法的對等類別 `Image.FromResource` 。 `UriImageSource`如果您需要控制快取，這個類別會很有用。 `FileImageSource`類別在 XAML 中很有用。 `StreamImageSource` 適用于非同步載入 `Stream` 物件，而 `ImageSource.FromStream` 是同步。

## <a name="platform-independent-bitmaps"></a>與平臺無關的點陣圖

[**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)專案會使用在 web 上載入點陣圖 `ImageSource.FromUri` 。 專案 `Image` 會設定為的 `Content` 屬性 `ContentPage` ，因此會限制為頁面的大小。 無論點陣圖的大小為何，受條件約束的 `Image` 元素都會延伸至其容器的大小，而點陣圖會以其在元素內的大小上限顯示， `Image` 同時維持點陣圖的外觀比例。 `Image`點陣圖以外的區域可以用來著色 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 。

[**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)範例很類似，但只是將 `Source` 屬性設定為 URL。 轉換是由 [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) 類別處理。

### <a name="fit-and-fill"></a>填滿並填滿

您可以藉由將 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 的屬性設定 `Image` 為列舉的下列其中一個成員，來控制如何伸展點陣圖 [`Aspect`](xref:Xamarin.Forms.Aspect) ：

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)：遵循外觀比例 (預設) 
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)：填滿區域，不遵守外觀比例
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)：填滿區域但遵循外觀比例，藉由裁剪點陣圖的一部分完成

### <a name="embedded-resources"></a>內嵌資源

您可以將點陣圖檔案新增至 PCL 或 PCL 中的資料夾。 提供它一個 **EmbeddedResource** 的 **組建動作** 。 [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)範例會示範如何使用 `ImageSource.FromResource` 載入檔案。 傳遞給方法的資源名稱包含元件名稱，後面接著點，後面接著選用的資料夾名稱和點，後面接著檔案名。

程式會將的 `VerticalOptions` 和 `HorizontalOptions` 屬性設定 `Image` 為 `LayoutOptions.Center` ，讓元素不受 `Image` 限制。 `Image`和點陣圖大小相同：

- 在 iOS 和 Android 上， `Image` 是點陣圖的圖元大小。 點陣圖圖元與螢幕圖元之間有一對一的對應。
- 在通用 Windows 平臺上， `Image` 是與裝置無關的單位中點陣圖的圖元大小。 在大部分的裝置上，每個點陣圖圖元都會佔用多個螢幕圖元。

[**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)範例會 `Image` 在 XAML 中以垂直方式放置 `StackLayout` 。 名為的標記延伸  [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) 有助於參考 XAML 中的內嵌資源。 此類別只會從它所在的元件載入資源，因此不能放在程式庫中。

### <a name="more-on-sizing"></a>調整規模的詳細資訊

通常最好是在所有平臺之間一致地調整點陣圖大小。
使用 **StackedBitmap** 進行實驗，您可以在專案中將設定為垂直的， `WidthRequest` 讓大小在各 `Image` `StackLayout` 平臺之間保持一致，但您只能使用這項技術來縮減大小。

您也可以設定， `HeightRequest` 讓影像大小在平臺上保持一致，但是點陣圖的限制寬度將會限制這項技術的多樣性。 若為垂直影像 `StackLayout` ， `HeightRequest` 應避免使用。

最好的方法是以與裝置無關的單位來比手機寬度更寬的點陣圖開始，並 `WidthRequest` 在裝置獨立單位中設定為所需的寬度。 [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)範例會示範這一點。

此 [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) 會顯示 Lewis Carroll 的 *Alice 冒險在仙境中* 的第7章，並顯示 John Tenniel 的原始圖解：

[![Mad 茶合作物件的三重螢幕擷取畫面](images/ch13fg16-small.png "Mad Hatters 茶方書籍文字")](images/ch13fg16-large.png#lightbox "Mad Hatters 茶方書籍文字")

### <a name="browsing-and-waiting"></a>流覽及等待

[**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)範例可讓使用者流覽儲存在 Xamarin 網站上的股票影像。 它會使用 .NET [`WebRequest`](xref:System.Net.WebRequest) 類別來下載具有點陣圖清單的 JSON 檔案。

> [!NOTE]
> Xamarin.Forms 程式應使用， [`HttpClient`](xref:System.Net.Http.HttpClient) 而不是透過 [`WebRequest`](xref:System.Net.WebRequest) 網際網路存取檔案。

程式 [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 會使用來表示發生問題。 當每個點陣圖載入時， [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) 的唯讀屬性 `Image` 為 `true` 。 `IsLoading`屬性是由可系結屬性所支援，因此 `PropertyChanged` 當該屬性變更時，就會引發事件。 程式會將處理常式附加至這個事件，並使用的目前設定 `IsLoaded` 來設定的 [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 屬性 `ActivityIndicator` 。

## <a name="streaming-bitmaps"></a>串流點陣圖

`ImageSource.FromStream`方法會 `ImageSource` 根據 .net 建立 `Stream` 。 方法必須傳遞傳回 `Func` 物件的物件 `Stream` 。

### <a name="accessing-the-streams"></a>存取資料流程

[**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)範例會示範如何使用 `ImaageSource.FromStream` 方法載入儲存為內嵌資源的點陣圖，並在 web 上載入點陣圖。

### <a name="generating-bitmaps-at-run-time"></a>在執行時間產生點陣圖

所有 Xamarin.Forms 平臺都支援未壓縮的 BMP 檔案格式，此格式很容易在程式碼中建立，然後儲存在中 `MemoryStream` 。 這項技術可讓演算法在執行時間建立點陣圖，如同在 [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) **Xamrin. FormsBook 工具** 庫的類別中所執行。

「自行操作」 [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) 範例示範 `BmpMaker` 如何使用來建立具有漸層影像的點陣圖。

## <a name="platform-specific-bitmaps"></a>平臺特定點陣圖

所有平臺都可 Xamarin.Forms 讓您在平臺應用程式元件中儲存點陣圖。 當 Xamarin.Forms 應用程式抓取時，這些平臺點陣圖的類型為 [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 。 您可以使用它們來進行下列動作：

- 的 [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 屬性 [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- 的 [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 屬性 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- 的 [`Image`](xref:Xamarin.Forms.Button) 屬性 `Button`

平臺元件已包含圖示和啟動顯示畫面的點陣圖：

- 在 iOS 專案的 [ **資源** ] 資料夾中
- 在 Android 專案中，在 [ **資源** ] 資料夾的子資料夾中
- 在 Windows 專案的 [ **資產** ] 資料夾中 (雖然 windows 平臺不會限制該資料夾的點陣圖) 

[**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)範例會使用程式碼，顯示平臺應用程式專案中的圖示。

### <a name="bitmap-resolutions"></a>點陣圖解析度

所有平臺都可以針對不同的裝置解析度儲存多個版本的點陣圖影像。 在執行時間，會根據螢幕的裝置解析度載入適當的版本。

在 iOS 上，這些點陣圖會以檔案名的尾碼來區分：

- 160 DPI 裝置沒有任何尾碼 (1 圖元給裝置獨立單位) 
- @2x320 DPI 裝置 (2 圖元到 DIU) 的 ' ' 尾碼
- @3x480 DPI 裝置 (3 圖元到 DIU) 的 ' ' 尾碼

要以一英寸的正方形顯示的點陣圖存在於三個版本中：

- MyImage.jpg 160 圖元正方形
- MyImage@2x.jpg 320圖元正方形
- MyImage@3x.jpg 480圖元正方形

程式會將這個點陣圖稱為 MyImage.jpg，但會在執行時間根據畫面的解析度抓取適當的版本。 未受限制時，點陣圖一律會以160裝置獨立的單位轉譯。

針對 Android，點陣圖會儲存在 **Resources** 資料夾的不同子資料夾中：

- 適用于 120 DPI 裝置的可繪製 lDPI (低 DPI)  (0.75 圖元到 DIU) 
- 適用于 160 DPI 裝置的可繪製 mDPI (中)  (1 圖元到 DIU) 
- 適用于 240 DPI 裝置的可繪製 hDPI (高)  (1.5 圖元到 DIU) 
- 320 DPI 裝置的可繪製 xhDPI (額外的高)  (2 圖元到 DIU) 
- 適用于 480 DPI 裝置的可繪製 xxhDPI (額外的高)  (3 圖元到 DIU) 
- 可繪製-xxxhDPI (三個額外的高) 640 DPI 裝置 (4 圖元到 DIU) 

若為要在一種正方形中轉譯的點陣圖，不同版本的點陣圖將會有相同的名稱但大小不同，並儲存在這些資料夾中：

- 繪製-lDPI/MyImage.jpg 120 圖元正方形
- 繪製-mDPI/MyImage.jpg 160 圖元正方形
- 繪製-hDPI/MyImage.jpg 240 圖元正方形
- 繪製-xhDPI/MyImage.jpg 320 圖元正方形
- 繪製-xxhDPI/MyImage.jpg 480 圖元正方形
- 繪製-xxxhDPI/MyImage.jpg 640 圖元正方形

點陣圖一律會以160裝置獨立單位呈現。  (標準 Xamarin.Forms 解決方案範本只包含 hDPI、xhDPI 和 xxhDPI 資料夾。 ) 

UWP 專案支援點陣圖命名配置，此配置包含每個裝置獨立單位的縮放比例（以圖元為單位），例如：

- MyImage.scale-200.jpg 320 圖元正方形

只有部分百分比有效。 這本書的範例程式僅包含具有延展 **200** 尾碼的映射，但目前的 Xamarin.Forms 解決方案範本包含 **調整-100** 、 **調整-125** 、級別 **150** 和 **調整規模的 400** 。

將點陣圖新增至平臺專案時， **組建動作** 應該是：

- iOS： **套件套件**
- Android： **AndroidResource**
- UWP： **內容**

[**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)範例會建立兩個類似按鈕的物件，其中包含 `Image` `TapGestureRecognizer` 已安裝的元素。 其目的是要讓物件成為一英寸的正方形。 的 `Source` 屬性 `Image` 會使用 `OnPlatform` 和 `On` 物件來設定，以參考平臺上可能不同的檔案名。 點陣圖影像包含表示其圖元大小的數位，因此您可以查看抓取和轉譯的大小點陣圖。

### <a name="toolbars-and-their-icons"></a>工具列及其圖示

平臺特定點陣圖的其中一個主要用途是 Xamarin.Forms 工具列，它是藉由將 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 物件加入至所定義的 [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) 集合來建立 `Page` 。 `ToobarItem` 衍生自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 其繼承某些屬性的。

最重要的 `ToolbarItem` 屬性如下：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) 可能根據平臺而出現的文字 `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)`FileImageSource`可能根據平臺而出現的影像類型`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) 型別為的 [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder) 列舉，具有三個成員、 [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) 、 [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) 和 [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary) 。

`Primary`專案數應限制為三個或四個。 您應該包含 `Text` 所有專案的設定。 針對大部分的平臺，只有 `Primary` 專案需要， `Icon` 但 Windows 8.1 需要 `Icon` 所有專案的。 圖示應為32裝置獨立單位的正方形。 `FileImageSource`型別指出它們是平臺特定的。

`ToolbarItem` [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 當出現時，就會引發事件，很類似 `Button` 。 `ToolbarItem` 也支援 [`Command`](xref:Xamarin.Forms.MenuItem.Command) [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 與 MVVM 連接常用的和屬性。  (請參閱第 [18 章，MVVM](chapter18.md)) 。

IOS 和 Android 都要求顯示工具列的頁面必須是 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 或導覽至的頁面 `NavigationPage` 。 [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程式會將 `MainPage` 其類別的屬性設定 `App` 為 [ (x：的 [函式 `NavigationPage` ] Xamarin.Forms 。NavigationPage。% 23ctor (Xamarin.Forms 。頁面) # A3 與 `ContentPage` 引數，並示範工具列的結構和事件處理常式。

### <a name="button-images"></a>按鈕影像

您也可以使用平臺特定點陣圖，將的 [`Image`](xref:Xamarin.Forms.Button.Image) 屬性設定 `Button` 為32裝置獨立單位平方的點陣圖（如 [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) 範例所示）。

> [!NOTE]
> 已增強在按鈕上使用影像的功能。 請參閱 [使用具有按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相關連結

- [第13章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第13章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用影像](~/xamarin-forms/user-interface/images.md)
- [使用具有按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
