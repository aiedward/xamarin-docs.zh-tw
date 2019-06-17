---
title: 第 13 章摘要。 點陣圖
description: 使用 Xamarin.Forms 建立行動應用程式：第 13 章摘要。 點陣圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 737e242e14778f38405845541b2ca30d27c3cf5a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334458"
---
# <a name="summary-of-chapter-13-bitmaps"></a>第 13 章摘要。 點陣圖

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)項目顯示的點陣圖。 所有的 Xamarin.Forms 平台支援的 JPEG、 PNG、 GIF 及 BMP 檔案格式。

在 Xamarin.Forms 中的點陣圖來自於四個位置︰

- 透過所指定 URL 的網頁
- 內嵌做為共用的文件庫中的資源
- 平台應用程式專案中內嵌為資源
- 從任何地方，您可以參考.NET`Stream`物件，包括 `MemoryStream`

共用的文件庫中的點陣圖資源是平台無關，而是平台特定的平台專案中的點陣圖資源。

> [!NOTE] 
> 本書的文字可讓可攜式類別庫，.NET Standard 程式庫已取代的參考。 活頁簿中的所有範例程式碼已經都轉換成使用.NET 標準程式庫。

藉由設定指定的點陣圖[ `Source` ](xref:Xamarin.Forms.Image.Source)屬性`Image`物件的型別[ `ImageSource` ](xref:Xamarin.Forms.ImageSource)，具有三個衍生的抽象類別：

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) 根據在 web 存取的點陣圖`Uri`物件設定為其[ `Uri` ](xref:Xamarin.Forms.UriImageSource.Uri)屬性
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 用於存取儲存在平台應用程式專案中的點陣圖根據 資料夾和檔案的路徑設定為與其[ `File` ](xref:Xamarin.Forms.FileImageSource.File)屬性
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) 載入使用.NET 的點陣圖`Stream`藉由傳回指定的物件`Stream`從`Func`設為其[ `Stream` ](xref:Xamarin.Forms.StreamImageSource.Stream)屬性

或者 （和更常見） 您可以使用下列靜態方法`ImageSource`類別，全部都會傳回`ImageSource`物件：

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) 根據在 web 存取的點陣圖`Uri`物件
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 用於存取點陣圖儲存為 PCL; 的應用程式中的內嵌資源[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))或是[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly))存取另一個來源組件中的點陣圖
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) 從平台應用程式專案中存取的點陣圖
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) 載入點陣圖，根據`Stream`物件

沒有任何類別相當於`Image.FromResource`方法。 `UriImageSource`類別就很有用，如果您要控制快取。 `FileImageSource`類別會在 XAML 中很有用。 `StreamImageSource` 適合用來非同步載入`Stream`物件，而`ImageSource.FromStream`是同步的。

## <a name="platform-independent-bitmaps"></a>平台無關的點陣圖

[ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)專案要載入點陣圖 web 使用`ImageSource.FromUri`。 `Image`元素設定為`Content`屬性`ContentPage`，因此它會受限於頁面的大小。 不論點陣圖的大小，受條件約束`Image`項目會自動縮放以其容器的大小，而點陣圖會顯示在其大小上限內`Image`維持點陣圖的長寬比的項目。 領域`Image`點陣圖可以加上的未來展望[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)。

[ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)範例類似，但只要設定`Source`屬性設為 URL。 轉換由[ `ImageSourceConverter` ](xref:Xamarin.Forms.ImageSourceConverter)類別。

### <a name="fit-and-fill"></a>調整和填滿

您可以控制如何藉由設定自動縮放點陣圖[ `Aspect` ](xref:Xamarin.Forms.Image.Aspect)屬性`Image`的下列成員的其中一個[ `Aspect` ](xref:Xamarin.Forms.Aspect)列舉型別：

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)： 會遵守外觀比例 （預設值）
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)： 填滿區域，不會遵守外觀比例
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)： 填滿區域，但會遵守外觀比例，藉由裁剪點陣圖的一部份來完成

### <a name="embedded-resources"></a>內嵌的資源

您可以將點陣圖檔案加入 PCL 中，或在 PCL 中的資料夾。 不妨**建置動作**的**EmbeddedResource**。 [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)範例示範如何使用`ImageSource.FromResource`載入的檔案。 傳遞給方法的資源名稱是由組件名稱，後面接著一個點，後面接著選擇性的資料夾名稱和一個點，後面接著檔案名稱所組成。

程式設定`VerticalOptions`並`HorizontalOptions`的屬性`Image`來`LayoutOptions.Center`，這可讓`Image`未受限制的項目。 `Image`和點陣圖大小都相同：

- 在 iOS 和 Android 上`Image`是點陣圖的像素大小。 沒有點陣圖的像素與螢幕像素之間的一對一對應。
- 通用 Windows 平台上`Image`是裝置獨立單位點陣圖的像素大小。 在大部分的裝置上每個點陣圖像素會佔用多個螢幕像素。

[ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)範例 puts`Image`垂直`StackLayout`在 XAML 中。 名為標記延伸[ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)可協助您在 XAML 中的內嵌的資源的參考。 這個類別只會從載入資源的組件中的位置，因此它不能放在程式庫。

### <a name="more-on-sizing"></a>更多的調整大小

它通常會以一致的方式在所有平台之間的大小點陣圖。
試驗**StackedBitmap**，您可以設定`WidthRequest`上`Image`項目中垂直`StackLayout`讓大小的一致平台，但您只能減少使用這項技術的大小。

您也可以設定`HeightRequest`製作映像大小一致的平台上，但受條件約束的點陣圖的寬度會限制這項技術的各種用途。 影像的垂直`StackLayout`，`HeightRequest`應予以避免。

最好的方法是以開頭的點陣圖寬度超過的電話寬度，以裝置獨立單位，而且設定`WidthRequest`到所需的寬度，以與裝置無關單位。 這示範於[ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)範例。

[ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)顯示 Lewis Carroll 第 7 章*Alice 的旅程中的夢中仙境*與 John Tenniel 由原始的圖例：

[![Mad 茶合作對象的三個螢幕擷取畫面](images/ch13fg16-small.png "瘋狂 Hatters 茶合作對象書籍的文字")](images/ch13fg16-large.png#lightbox "瘋狂 Hatters 茶合作對象書籍的文字")

### <a name="browsing-and-waiting"></a>瀏覽和等候

[ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)範例可讓使用者瀏覽儲存在 Xamarin 的網站上的原廠映像。 它會使用.NET [ `WebRequest` ](xref:System.Net.WebRequest)類別來下載 JSON 檔案的清單中的點陣圖。

> [!NOTE]
> Xamarin.Forms 程式應該使用[ `HttpClient` ](xref:System.Net.Http.HttpClient)而非[ `WebRequest` ](xref:System.Net.WebRequest)透過網際網路存取的檔案。 

程式會使用[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator)表示的項目即將。 載入每個點陣圖，唯讀[ `IsLoading` ](xref:Xamarin.Forms.Image.IsLoading)屬性`Image`是`true`。 `IsLoading`屬性做為後盾的可繫結的屬性，因此`PropertyChanged`該屬性變更時引發事件。 程式將處理常式附加至這個事件，並使用目前的設定`IsLoaded`來設定[ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)屬性`ActivityIndicator`。

## <a name="streaming-bitmaps"></a>資料流的點陣圖

`ImageSource.FromStream`方法會建立`ImageSource`根據.NET `Stream`。 必須傳遞方法`Func`傳回的物件`Stream`物件。

### <a name="accessing-the-streams"></a>存取資料流

[ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)範例示範如何使用`ImaageSource.FromStream`方法以載入點陣圖儲存為內嵌資源，並在 web 上載入點陣圖。

### <a name="generating-bitmaps-at-run-time"></a>在執行階段產生的點陣圖

所有的 Xamarin.Forms 平台支援的未壓縮的 BMP 檔案格式，可以輕鬆地在程式碼建構，並將儲存在`MemoryStream`。 這項技術可讓以演算法在執行階段建立點陣圖中實作[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)類別**Xamrin.FormsBook.Toolkit**程式庫。

「 不要自己動手 」 [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)範例示範如何使用`BmpMaker`建立點陣圖的漸層停駐的映像。

## <a name="platform-specific-bitmaps"></a>平台專屬的點陣圖

所有的 Xamarin.Forms 平台可讓您將點陣圖儲存在平台應用程式組件。 這些平台點陣圖時擷取 Xamarin.Forms 應用程式，都屬於型別[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)。 您可以使用這些電腦進行：

- [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon)屬性 [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon)屬性 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- [ `Image` ](xref:Xamarin.Forms.Button)屬性 `Button`

平台組件尚未包含的圖示和啟動顯示畫面的點陣圖：

- 在 iOS 專案中，在**資源**資料夾
- 在 Android 專案中的子資料夾中**資源**資料夾
- 在 Windows 專案中，在**資產**資料夾 （雖然 Windows 平台並不會限制該資料夾的點陣圖）

[ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)範例顯示從平台應用程式專案圖示使用的程式碼。

### <a name="bitmap-resolutions"></a>點陣圖的解決方式

所有平台可讓您儲存針對不同的裝置解析度的點陣圖影像的多個版本。 在執行階段，會載入根據裝置解析度螢幕的適當版本。

在 iOS 上，在 檔案名稱的尾碼來區分這些點陣圖：

- 沒有後置詞為 160 DPI 裝置 （裝置的獨立單位 1 個像素） 的
- '@2x' 後的置詞 320 DPI 裝置 （以 DIU 2 個像素）
- '@3x' 後置詞 480 DPI 裝置 （以 DIU 的 3 個像素）

要顯示為一英吋正方形的點陣圖會存在於三個版本：

- MyImage.jpg 160 像素的正方形
- MyImage@2x.jpg 在 320 像素的正方形
- MyImage@3x.jpg 480 像素的正方形

程式會參考 MyImage.jpg，為此點陣圖，但在執行階段根據畫面的解析度會擷取適當的版本。 時未受到約束，點陣圖會永遠會呈現在 160 裝置獨立單位。

若是 Android，點陣圖會儲存在不同的子資料夾的**資源**資料夾：

- 可繪製-ldpi (低 DPI) 為 120 DPI 裝置 （以 DIU 0.75 像素）
- 可繪製-mdpi （中） 為 160 DPI 裝置 （以 DIU 1 個像素）
- 可繪製-hdpi （高） 240 DPI 裝置 （以 DIU 1.5 像素）
- 可繪製-xhdpi （極高） 320 DPI 裝置 （以 DIU 2 個像素）
- 可繪製-xxhdpi （額外會有額外的高） 480 DPI 裝置 （以 DIU 的 3 個像素）
- 可繪製-xxxhdpi （三個額外的起伏） 640 DPI 裝置 （以 DIU 4 個像素）

要轉譯為 1 的平方英吋是點陣圖，點陣圖的各種版本將會有相同名稱但不同的大小，並儲存在這些資料夾中：

- 可繪製-ldpi/MyImage.jpg 120 像素的正方形
- 可繪製-mdpi/MyImage.jpg 160 像素的正方形
- 可繪製-hdpi/MyImage.jpg 240 像素的正方形
- 可繪製-xhdpi/MyImage.jpg 320 像素的正方形
- 可繪製-xxhdpi/MyImage.jpg 480 像素的正方形
- 可繪製-xxxhdpi/MyImage.jpg 640 像素的正方形

點陣圖一律會呈現在 160 裝置無關的單位。 （標準的 Xamarin.Forms 方案範本只包括 hdpi、 xhdpi 和 xxhdpi 資料夾）。

UWP 專案支援點陣圖的命名配置，其中包含的像素為單位，每個裝置獨立單位的縮放比例百分比，例如：

- MyImage.scale-200.jpg 320 像素的正方形

只有某些百分比是有效的。 這本書的範例方案包含使用的映像**縮放 200**後置詞，但目前的 Xamarin.Forms 方案範本，包括**比例 100**，**擴展 125**，**縮放 150**，並**擴展 400**。

當平台專案中，加入點陣圖**建置動作**應該是：

- iOS:**BundleResource**
- Android:**AndroidResource**
- UWP:**Content**

[ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)範例會建立兩個按鈕類似的物件，其中包含`Image`項目`TapGestureRecognizer`安裝。 它可能是物件是一英吋正方形。 `Source`的屬性`Image`會使用設定`OnPlatform`和`On`參考可能位在不同的平台上的檔案名稱的物件。 點陣圖影像包含的數字表示其像素大小，以便您可以看到哪些大小點陣圖會擷取並呈現。

### <a name="toolbars-and-their-icons"></a>工具列和其圖示

其中一個平台專屬點陣圖的主要用途是建構加上的 [Xamarin.Forms] 工具列[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)物件來[ `ToolbarItems` ](xref:Xamarin.Forms.Page.ToolbarItems) 所定義的集合`Page`. `ToobarItem` 衍生自[ `MenuItem` ](xref:Xamarin.Forms.MenuItem)從中繼承某些屬性。

最重要`ToolbarItem`屬性：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) 根據平台可能會出現的文字和 `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 型別的`FileImageSource`可能會顯示根據平台映像和 `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) 型別的[ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder)，具有三個成員，列舉[ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default)， [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary)，以及[ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

數目`Primary`應該限制為三或四個項目。 您應該在包含`Text`設定的所有項目。 針對大部分平台，只能`Primary`項目需要`Icon`但需要 Windows 8.1`Icon`的所有項目。 圖示應該是 32 裝置獨立單位正方形。 `FileImageSource`類型表示它們是特定平台。

`ToolbarItem`引發[ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked)事件，當點選，很像`Button`。 `ToolbarItem` 也支援[ `Command` ](xref:Xamarin.Forms.MenuItem.Command)並[ `CommandParameter` ](xref:Xamarin.Forms.MenuItem.CommandParameter) MVVM 與常用的屬性。 (請參閱[第 18 章、 MVVM](chapter18.md))。

IOS 和 Android 都需要頁面，其中顯示工具列[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)或 巡覽至頁面`NavigationPage`。 [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程式集`MainPage`屬性其`App`類別[`NavigationPage`建構函式](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page))與`ContentPage`引數，並示範如何建構和事件處理常式的工具列。

### <a name="button-images"></a>按鈕的影像

您也可以設定使用平台專屬點陣圖[ `Image` ](xref:Xamarin.Forms.Button.Image)屬性`Button`32 裝置獨立單位正方形，如所示的點陣圖[ **ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)範例。

> [!NOTE]
> 已增強的按鈕上的映像使用。 請參閱[使用的點陣圖按鈕](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相關連結

- [第 13 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第 13 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用影像](~/xamarin-forms/user-interface/images.md)
- [使用的點陣圖按鈕](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
