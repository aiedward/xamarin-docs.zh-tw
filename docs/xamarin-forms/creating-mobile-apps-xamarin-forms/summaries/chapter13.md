---
title: "第 13 章的摘要。 點陣圖"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 74e5e47a481d02fe11be4b770b818d2c88b517f7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-13-bitmaps"></a>第 13 章的摘要。 點陣圖

Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目會顯示點陣圖。 Xamarin.Forms 平台都支援 JPEG、 PNG、 GIF 和 BMP 檔案格式。

Xamarin.Forms 中的點陣圖來自四個位置：

- 將 url 指定 web
- 內嵌為一般的可攜式類別庫中的資源
- 平台應用程式專案中內嵌為資源
- 從.NET 可以參考的任何地方`Stream`物件，包括 `MemoryStream`

平台特定平台專案中的點陣圖資源時，在 PCL 中的點陣圖資源會是平台無關。

設定來指定點陣圖[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/)屬性`Image`物件的型別[ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)，具有三個衍生的抽象類別：

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) 根據在網路上存取點陣圖`Uri`物件設定為其[ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/)屬性
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) 用於存取儲存在平台應用程式專案中的點陣圖根據資料夾和檔案路徑設為其[ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/)屬性
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) 載入點陣圖，使用.NET`Stream`藉由傳回指定物件`Stream`從`Func`設為其[ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/)屬性

或者 （和更常） 您可以使用下列的靜態方法`ImageSource`類別的哪些傳回所有`ImageSource`物件：

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) 根據在網路上存取點陣圖`Uri`物件
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) 用於存取儲存為內嵌資源 PCL 中，應用程式中的點陣圖或[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/)或[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/)存取另一個來源組件中的點陣圖
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) 用於存取點陣圖從平台應用程式專案
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) 載入點陣圖，根據`Stream`物件

沒有的類別對等的`Image.FromResource`方法。 `UriImageSource`類別就很有用，如果您要控制快取。 `FileImageSource`類別會在 XAML 中很有用。 `StreamImageSource` 適用於非同步載入`Stream`物件，而`ImageSource.FromStream`是同步的。

## <a name="platform-independent-bitmaps"></a>平台無關的點陣圖

[ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)專案載入點陣圖透過 web 使用`ImageSource.FromUri`。 `Image`元素設定為`Content`屬性`ContentPage`，所以其會受限為頁面大小。 不論點陣圖的大小，受條件約束`Image`項目會延伸以其容器的大小和大小上限內中會顯示點陣圖`Image`維持點陣圖的長寬比的項目。 區域`Image`超出點陣圖可著色與[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)。

[ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)範例類似，但只會設定`Source`URL 的屬性。 轉換由[ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/)類別。

### <a name="fit-and-fill"></a>調整和填滿

您可以控制如何藉由設定自動縮放點陣圖[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/)屬性`Image`的下列成員的其中一個[ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/)列舉型別：

- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/)： 尊重外觀比例 （預設值）
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/)： 填滿區域中，不會遵守外觀比例
- [`AspectFill`](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect.AspectFill/)： 填滿區域，但尊重外觀比例，達成裁剪點陣圖的一部分

### <a name="embedded-resources"></a>內嵌的資源

您可以將點陣圖檔案加入 PCL 中，或在 PCL 中的資料夾。 提供給它**建置動作**的**EmbeddedResource**。 [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)範例示範如何使用`ImageSource.FromResource`載入檔案。 傳遞給方法的資源名稱是由組件名稱，後面接著句點，再接著選擇性的資料夾名稱和一個點，後面接著檔案名稱所組成。

程式設定`VerticalOptions`和`HorizontalOptions`屬性`Image`至`LayoutOptions.Center`，讓`Image`未受限的項目。 `Image`而點陣圖都是相同的大小：

- IOS 和 Android 上`Image`是點陣圖的像素大小。 沒有點陣圖的像素與畫面像素為單位之間的一對一對應。
- 在 Windows 執行階段平台上`Image`是以與裝置無關的單位點陣圖的像素大小。 在大部份的裝置，每個點陣圖的像素會佔據多個螢幕的像素為單位。

[ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)範例置於`Image`中垂直`StackLayout`在 XAML 中。 名為標記延伸[ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)參考在 XAML 中內嵌的資源可協助。 這個類別只會從載入資源在它的組件所在，因此不能放在文件庫中。

### <a name="more-on-sizing"></a>需調整大小的詳細資訊

它通常會以一致的方式在所有平台之間的大小點陣圖。
試驗**StackedBitmap**，您可以設定`WidthRequest`上`Image`項目中垂直`StackLayout`讓大小一致平台，但您只能減少使用這項技術的大小。

您也可以設定`HeightRequest`讓影像大小一致平台上，但受條件約束的點陣圖的寬度會限制這項技術的多樣化。 在垂直影像`StackLayout`，`HeightRequest`應該予以避免。

最好的方法是要開始使用點陣圖大於電話寬度，以與裝置無關的單位，並設定`WidthRequest`所需的寬度，以與裝置無關的單位。 這示範於[ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)範例。

[ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)顯示 Lewis Carroll 第 7 章*Wonderland Alice 探索*了由 John Tenniel 原始的實例：

[![Mad 茶杯的合作對象的三個螢幕擷取畫面](images/ch13fg16-small.png "Mad Hatters 茶杯的合作對象書籍的文字")](images/ch13fg16-large.png#lightbox "Mad Hatters 茶杯的合作對象書籍的文字")

### <a name="browsing-and-waiting"></a>瀏覽和等候

[ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)範例可讓使用者瀏覽儲存在 Xamarin 網站上的現有映像。 它會使用.NET`WebRequest`類別將下載 JSON 檔案之點陣圖的清單。

程式會使用[ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)表示的項目正在進行的作業。 當載入每個點陣圖，唯讀[ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/)屬性`Image`是`true`。 `IsLoading`屬性所支援可繫結的屬性，因此`PropertyChanged`該屬性變更時引發事件。 程式將處理常式附加至這個事件，並使用目前的設定`IsLoaded`設定[ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)屬性`ActivityIndicator`。

## <a name="streaming-bitmaps"></a>資料流的點陣圖

`ImageSource.FromStream`方法會建立`ImageSource`根據.NET `Stream`。 方法必須傳遞`Func`傳回物件`Stream`物件。

### <a name="accessing-the-streams"></a>存取資料流

[ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)範例示範如何使用`ImaageSource.FromStream`方法以載入點陣圖儲存為內嵌資源，並在 web 上載入點陣圖。

### <a name="generating-bitmaps-at-run-time"></a>在執行階段產生的點陣圖

Xamarin.Forms 平台支援的未壓縮的 BMP 檔案格式，可以輕鬆地在程式碼建構，然後儲存在`MemoryStream`。 這項技術可讓以演算法建立點陣圖，在執行階段，以實作[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)類別**Xamrin.FormsBook.Toolkit**程式庫。

「 執行它自己" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)範例將示範如何使用`BmpMaker`建立點陣圖的漸層的映像。

## <a name="platform-specific-bitmaps"></a>平台專屬的點陣圖

Xamarin.Forms 平台允許將點陣圖儲存在平台應用程式組件。 擷取由 Xamarin.Forms 應用程式，這些平台點陣圖是屬於型別[ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/)。 您可以使用這些電腦進行：

- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/)屬性 [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/)屬性 [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)屬性 `Button`

平台組件尚未包含的圖示和啟動顯示畫面的點陣圖：

- 在 iOS 專案中，在**資源**資料夾
- 在 Android 專案中的子資料夾**資源**資料夾
- 在 Windows 專案中，在**資產**資料夾 （雖然 Windows 平台不限制該資料夾的點陣圖）

[ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)範例會顯示圖示平台的應用程式專案中，使用程式碼。

### <a name="bitmap-resolutions"></a>點陣圖的解決方式

所有平台允許儲存多個版本的點陣圖影像代表不同的裝置解析度。 在執行階段，會載入根據裝置解析度螢幕的正確版本。

在 iOS 上，檔案名稱的後置字元來區分這些點陣圖：

- 160 DPI 裝置 （以裝置獨立單位 1 個像素） 沒有後置詞
- '@2x' 後的置詞 320 DPI 裝置 （以 DIU 2 像素）
- '@3x' 後的置詞 480 DPI 裝置 （以 DIU 3 像素）

要顯示為一英吋方形點陣圖會存在於三個版本：

- MyImage.jpg 正方形 160 像素
- MyImage@2x.jpg 正方形 320 像素
- MyImage@3x.jpg 正方形 480 像素

程式會參考 MyImage.jpg，為此點陣圖，但在執行階段根據螢幕解析度會擷取適當的版本。 未受到約束，當點陣圖會呈現在 160 裝置獨立單位。

適用於 Android 的各種子資料夾中儲存點陣圖**資源**資料夾：

- drawable-ldpi (低 DPI) 120 DPI 裝置 （以 DIU 0.75 像素）
- drawable-mdpi （中） 為 160 DPI 裝置 （以 DIU 1 個像素）
- drawable-hdpi （高） 240 DPI 裝置 （以 DIU 1.5 像素）
- drawable-xhdpi （額外高） 320 DPI 裝置 （以 DIU 2 像素）
- drawable-xxhdpi （額外會有額外的高） 480 DPI 裝置 （以 DIU 3 像素）
- drawable-xxxhdpi （三個額外高點） 640 DPI 裝置 （以 DIU 4 像素）

點陣圖，要呈現在一個平方英吋，點陣圖的各種版本將會有相同名稱但不同的大小，並儲存在這些資料夾：

- drawable-ldpi/MyImage.jpg 正方形 120 像素
- drawable-mdpi/MyImage.jpg 正方形 160 像素
- drawable-hdpi/MyImage.jpg 正方形 240 像素
- drawable-xhdpi/MyImage.jpg 正方形 320 像素
- drawable-xxhdpi/MyImage.jpg 正方形 480 像素
- drawable-xxxhdpi/MyImage.jpg 正方形 640 像素

點陣圖會呈現在 160 裝置獨立單位。 （標準 Xamarin.Forms 方案範本只包括 hdpi、 xhdpi 和 xxhdpi 資料夾）。

Windows 執行階段專案支援點陣圖命名配置所組成的像素為單位，每個裝置獨立單位的縮放比例百分比，例如：

- MyImage.scale-200.jpg 正方形 320 像素

只有某些百分比是有效的。 範例程式，此活頁簿包含使用的映像**標尺 200**尾碼，但目前 Xamarin.Forms 方案範本包含**標尺 100**，**標尺 125**，**標尺 150**，和**標尺 400**。 

加入平台專案中，點陣圖時**建置動作**應該是：

- iOS: **BundleResource**
- Android: **AndroidResource**
- Windows 執行階段：**內容**

[ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)範例會建立組成兩個按鈕類似物件`Image`的項目`TapGestureRecognizer`安裝。 它可能是物件是一英吋正方形。 `Source`屬性`Image`使用設定`OnPlatform`和`On`來參考不同的檔名的平台上的物件。 點陣圖影像包含數字表示其像素大小，讓您可以查看哪些大小點陣圖會擷取並呈現。

### <a name="toolbars-and-their-icons"></a>工具列和其圖示

其中一個平台專屬點陣圖的主要用途是 Xamarin.Forms 工具列來加入建構[ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)物件加入至[ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/) 所定義的集合`Page`. `ToobarItem` 衍生自[ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)從它繼承某些屬性。

最重要`ToolbarItem`屬性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) 根據平台可能會出現的文字和 `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) 型別的`FileImageSource`可能會顯示根據平台映像和 `Order`
- [`Order`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Order/) 型別的[ `ToolbarItemOrder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItemOrder/)，具有三個成員，列舉[ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Default/)， [ `Primary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Primary/)，和[ `Secondary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Secondary/).

數目`Primary`應該限制為三或四個項目。 您應該在包含`Text`設定的所有項目。 針對大部分平台，只`Primary`項目需要`Icon`，但是需要 Windows 8.1`Icon`的所有項目。 圖示應該是 32 裝置獨立單位正方形。 `FileImageSource`類型表示它們是特定平台。

`ToolbarItem`引發[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/)事件時點選，很像`Button`。 `ToolbarItem` 也支援[ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/)和[ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/)與 MVVM 常用的屬性。 (請參閱[第 18 章、 MVVM](chapter18.md))。

IOS 和 Android 需要顯示工具列的網頁是[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)或頁面導覽`NavigationPage`。 [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程式設定`MainPage`屬性其`App`類別[`NavigationPage`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/)與`ContentPage`引數，並示範如何建構和事件處理常式的工具列。

### <a name="button-images"></a>按鈕影像

您也可以使用平台專屬點陣圖設定[ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/)屬性`Button`32 裝置獨立單位正方形，如下所示的點陣圖[ **ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)範例。



## <a name="related-links"></a>相關連結

- [第 13 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第 13 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用影像](~/xamarin-forms/user-interface/images.md)
