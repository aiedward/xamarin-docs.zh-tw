---
title： "第13章的摘要。 點陣圖「描述：」建立 Mobile Apps Xamarin.Forms ：第13章的摘要。 點陣圖 "ms. 生產： xamarin ms. 技術： assetid： 5D153857-B6B7-4A14-8FB9-067DE198C2C7 author： davidbritch ms-chap： dabritch ms. date： 07/18/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-13-bitmaps"></a>第13章的摘要。 點陣圖

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) 元素會顯示點陣圖。 所有 Xamarin.Forms 平臺都支援 JPEG、PNG、GIF 和 BMP 檔案格式。

中的點陣圖來自 Xamarin.Forms 四個位置：

- 透過 URL 所指定的網站
- 內嵌為共用程式庫中的資源
- 內嵌為平臺應用程式專案中的資源
- 從可由 .NET 物件參考的任何地方 `Stream` ，包括`MemoryStream`

共用程式庫中的點陣圖資源與平臺無關，而平臺專案中的點陣圖資源則是平臺特定的。

> [!NOTE]
> 本書的文字會參照可移植的類別庫，而這些程式庫已由 .NET Standard 程式庫取代。 本書中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

藉由將的 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性設 `Image` 為類型的物件，即可指定點陣圖 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，其具有三個衍生的抽象類別：

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)針對根據 `Uri` 設定為其屬性之物件的 web 來存取點陣圖 [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)用於存取平臺應用程式專案中儲存的點陣圖（根據資料夾和檔案路徑設定為其 [`File`](xref:Xamarin.Forms.FileImageSource.File) 屬性）
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)若要使用指定的 .NET 物件載入點陣圖，請將 `Stream` `Stream` 從 `Func` 集合傳回至其 [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) 屬性

或者，您也可以使用類別的下列靜態方法 `ImageSource` ，其中全部都會傳回 `ImageSource` 物件：

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))針對以物件為基礎的 web 存取點陣圖 `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)用於存取在應用程式 PCL 中儲存為內嵌資源的點陣圖;[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))或 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) 存取另一個來源元件中的點陣圖
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))用於從平臺應用程式專案存取點陣圖
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))用於載入以物件為基礎的點陣圖 `Stream`

沒有任何類別對等的 `Image.FromResource` 方法。 `UriImageSource`如果您需要控制快取，類別會很有用。 `FileImageSource`類別在 XAML 中很有用。 `StreamImageSource`適用于非同步載入 `Stream` 物件，而 `ImageSource.FromStream` 則是同步的。

## <a name="platform-independent-bitmaps"></a>平臺獨立的點陣圖

[**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)專案會使用，透過 web 載入點陣圖 `ImageSource.FromUri` 。 `Image`元素會設定為的 `Content` 屬性 `ContentPage` ，因此會限制為頁面的大小。 不論點陣圖的大小為何，受條件約束的 `Image` 元素都會延伸到其容器的大小，而點陣圖會顯示在專案內的大小上限， `Image` 同時維持點陣圖的外觀比例。 `Image`點陣圖外的區域可以使用進行著色 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 。

[**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)範例很類似，但只是將 `Source` 屬性設定為 URL。 轉換是由 [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) 類別處理。

### <a name="fit-and-fill"></a>符合並填滿

您可以藉由將的 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 屬性設定 `Image` 為列舉的下列其中一個成員，來控制如何延展點陣圖 [`Aspect`](xref:Xamarin.Forms.Aspect) ：

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)：遵循外觀比例（預設值）
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)：填滿區域，不尊重外觀比例
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)：填滿區域但外觀比例，藉由裁剪部分點陣圖來完成

### <a name="embedded-resources"></a>內嵌資源

您可以將點陣圖檔案新增至 PCL 或 PCL 中的資料夾。 為它提供**EmbeddedResource**的**組建動作**。 [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)範例會示範如何使用 `ImageSource.FromResource` 載入檔案。 傳遞給方法的資源名稱包含元件名稱，後面接著一個點，後面接著選擇性的資料夾名稱和點，然後是檔案名。

程式會將的 `VerticalOptions` 和 `HorizontalOptions` 屬性設定 `Image` 為 `LayoutOptions.Center` ，讓元素不受 `Image` 限制。 `Image`和點陣圖的大小相同：

- 在 iOS 和 Android 上， `Image` 是點陣圖的圖元大小。 點陣圖圖元和螢幕圖元之間有一對一的對應。
- 在通用 Windows 平臺上， `Image` 是以裝置獨立單位表示之點陣圖的圖元大小。 在大部分的裝置上，每個點陣圖圖元都會佔用多個螢幕圖元。

[**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)範例會將放 `Image` 在 XAML 中的垂直 `StackLayout` 。 名為的標記延伸 [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) 有助於在 XAML 中參考內嵌資源。 此類別只會從它所在的元件載入資源，因此不能放在程式庫中。

### <a name="more-on-sizing"></a>調整大小的詳細資訊

通常最好是在所有平臺上一致地調整點陣圖大小。
使用**StackedBitmap**進行實驗，您可以在 `WidthRequest` 垂直的元素上設定， `Image` 使其大小在 `StackLayout` 平臺之間保持一致，但您只能使用這項技術來縮小大小。

您也可以將設定 `HeightRequest` 為讓影像大小在平臺上保持一致，但點陣圖的限制寬度會限制這項技術的多樣性。 若為垂直影像 `StackLayout` ， `HeightRequest` 應避免使用。

最好的方法是以與裝置無關的單位，以比電話寬度更寬的點陣圖開始，並 `WidthRequest` 在與裝置無關的單位中設定為所需的寬度。 這會在[**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)範例中示範。

[**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)會在仙境中顯示 Lewis Carroll 的*Alice 冒險*的第7章，其中包含 John Tenniel 的原始圖例：

[![Mad 茶合作夥伴的三向螢幕擷取畫面](images/ch13fg16-small.png "Mad Hatters 茶合作物件書籍文字")](images/ch13fg16-large.png#lightbox "Mad Hatters 茶合作物件書籍文字")

### <a name="browsing-and-waiting"></a>流覽和等候

[**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)範例可讓使用者流覽儲存在 Xamarin 網站上的股票影像。 它會使用 .NET [`WebRequest`](xref:System.Net.WebRequest) 類別來下載包含點陣圖清單的 JSON 檔案。

> [!NOTE]
> Xamarin.Forms程式應該使用， [`HttpClient`](xref:System.Net.Http.HttpClient) 而不是透過 [`WebRequest`](xref:System.Net.WebRequest) 網際網路存取檔案。

程式 [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 會使用來表示某個專案正在進行中。 載入每個點陣圖時，的唯讀 [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) 屬性 `Image` 是 `true` 。 `IsLoading`屬性是由可系結屬性所支援，因此 `PropertyChanged` 當該屬性變更時，就會引發事件。 程式會將處理常式附加至這個事件，並使用的目前設定 `IsLoaded` 來設定的 [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 屬性 `ActivityIndicator` 。

## <a name="streaming-bitmaps"></a>串流點陣圖

`ImageSource.FromStream`方法會建立以 `ImageSource` .net 為基礎的 `Stream` 。 必須將傳回物件的物件傳遞給方法 `Func` `Stream` 。

### <a name="accessing-the-streams"></a>存取資料流程

[**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)範例會示範如何使用 `ImaageSource.FromStream` 方法來載入儲存為內嵌資源的點陣圖，並在 web 上載入點陣圖。

### <a name="generating-bitmaps-at-run-time"></a>在執行時間產生點陣圖

所有 Xamarin.Forms 平臺都支援未壓縮的 BMP 檔案格式，這可輕鬆地在程式碼中建立，然後儲存在中 `MemoryStream` 。 這項技術可讓以演算法方式在執行時間建立點陣圖，如同在 [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) **Xamrin. FormsBook 工具**庫的類別中所實。

「自行操作」 [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)範例示範 `BmpMaker` 如何使用來建立具有漸層影像的點陣圖。

## <a name="platform-specific-bitmaps"></a>平臺特定的點陣圖

所有 Xamarin.Forms 平臺都允許將點陣圖儲存在平臺應用程式元件中。 由 Xamarin.Forms 應用程式抓取時，這些平臺點陣圖的類型為 [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 。 您可以使用它們來進行下列動作：

- 的 [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 屬性[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- 的 [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 屬性[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- 的 [`Image`](xref:Xamarin.Forms.Button) 屬性`Button`

平臺元件已經包含圖示和啟動顯示畫面的點陣圖：

- 在 iOS 專案的 [**資源**] 資料夾中
- 在 Android 專案的 [ **Resources** ] 資料夾的子資料夾中
- 在 Windows 專案的 [**資產**] 資料夾中（雖然 windows 平臺不會限制點陣圖到該資料夾）

[**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)範例會使用程式碼來顯示平臺應用程式專案中的圖示。

### <a name="bitmap-resolutions"></a>點陣圖解析度

所有平臺都允許針對不同的裝置解析度儲存多個版本的點陣圖影像。 在執行時間，會根據螢幕的裝置解析度載入適當的版本。

在 iOS 上，這些點陣圖會以檔案名的尾碼來區別：

- 160 DPI 裝置沒有尾碼（1圖元到裝置獨立單位）
- @2x320 DPI 裝置的 ' ' 尾碼（DIU 的2圖元）
- @3x480 DPI 裝置的 ' ' 尾碼（DIU 為3圖元）

要顯示為一寸正方形的點陣圖會存在於三個版本中：

- MyImage.jpg 160 圖元正方形
- MyImage@2x.jpg320圖元正方形
- MyImage@3x.jpg480圖元正方形

程式會將此點陣圖稱為 MyImage.jpg，但在執行時間會根據螢幕的解析度來取得適當的版本。 在不受限制的情況下，點陣圖一律會轉譯為160裝置獨立單位。

若是 Android，點陣圖會儲存在**Resources**資料夾的各種子資料夾中：

- 可繪製-適用于 120 DPI 裝置的 lDPI （低 DPI）（0.75 圖元到 DIU）
- 可繪製-適用于 160 DPI 裝置的 mDPI （中）（DIU 的1圖元）
- 可繪製-適用于 240 DPI 裝置的 hDPI （高）（1.5 圖元到 DIU）
- 可繪製-適用于 320 DPI 裝置的 xhDPI （超低）（DIU 的2圖元）
- 可繪製-適用于 480 DPI 裝置的 xxhDPI （額外額外的高）（DIU 的3圖元）
- 可繪製-適用于 640 DPI 裝置的 xxxhDPI （三個額外的高潮）（DIU 為4圖元）

若是要以一平方英寸呈現的點陣圖，點陣圖的各種版本將會有相同的名稱，但大小不同，而且會儲存在這些資料夾中：

- 繪製-lDPI/MyImage.jpg 120 圖元正方形
- 繪製-mDPI/MyImage.jpg 160 圖元正方形
- 繪製-hDPI/MyImage.jpg 240 圖元正方形
- 繪製-xhDPI/MyImage.jpg 320 圖元正方形
- 繪製-xxhDPI/MyImage.jpg 480 圖元正方形
- 繪製-xxxhDPI/MyImage.jpg 640 圖元正方形

點陣圖一律會轉譯為160裝置獨立單位。 （標準 Xamarin.Forms 解決方案範本只包含 hDPI、xhDPI 和 xxhDPI 資料夾）。

UWP 專案支援點陣圖命名配置，其中包含每個裝置獨立單位以圖元為單位的縮放因數，以百分比表示，例如：

- MyImage.scale-200.jpg 320 圖元正方形

只有部分百分比有效。 本書的範例程式僅包含具有延展 **-200**尾碼的映射，但目前的 Xamarin.Forms 解決方案範本包含**調整-100**、**擴充 125**、**擴充-150**和**調整 400**。

將點陣圖新增至平臺專案時，**組建動作**應該是：

- iOS： **BundleResource**
- Android： **AndroidResource**
- UWP：**內容**

[**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)範例會建立兩個類似按鈕的物件，其中包含 `Image` `TapGestureRecognizer` 已安裝的元素。 其目的是讓物件是一英寸的正方形。 的 `Source` 屬性 `Image` 是使用 `OnPlatform` 和 `On` 物件來設定，以參考平臺上可能不同的檔案名。 點陣圖影像包含表示其圖元大小的數位，因此您可以查看要抓取和轉譯的點陣圖大小。

### <a name="toolbars-and-their-icons"></a>工具列及其圖示

平臺特定點陣圖的主要用途之一是 Xamarin.Forms 工具列，它是藉由將 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 物件加入至所定義的集合來建立 [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) `Page` 。 `ToobarItem`衍生自，從中 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 繼承某些屬性。

最重要的 `ToolbarItem` 屬性包括：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)針對可能根據平臺和所顯示的文字`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)`FileImageSource`根據平臺和，可能會出現之影像類型的`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)型別為的 [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder) 列舉，具有三個成員：、 [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) 和 [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary) 。

`Primary`專案數應限制為三或四個。 您應該包含 `Text` 所有專案的設定。 在大部分的平臺中，只有 `Primary` 專案需要， `Icon` 但 Windows 8.1 需要 `Icon` 所有專案的。 圖示應為32裝置獨立單位正方形。 `FileImageSource`類型表示它們是平臺特定的。

按 `ToolbarItem` [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 下時，會引發事件，非常類似 `Button` 。 `ToolbarItem`也支援 [`Command`](xref:Xamarin.Forms.MenuItem.Command) [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 與 MVVM 連接常用的和屬性。 （請參閱第[18 章，MVVM](chapter18.md)）。

IOS 和 Android 都需要顯示工具列的頁面是 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 或流覽至的頁面 `NavigationPage` 。 [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程式會將 `MainPage` 其類別的屬性設定 `App` 為 [構造函式 `NavigationPage` ] （x： Xamarin.Forms 。NavigationPage .% 23ctor （ Xamarin.Forms 。Page）） `ContentPage` ，並示範工具列的結構和事件處理常式。

### <a name="button-images"></a>按鈕影像

您也可以使用平臺特定的點陣圖，將的 [`Image`](xref:Xamarin.Forms.Button.Image) 屬性設定 `Button` 為32裝置獨立單位正方形的點陣圖，如[**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)範例所示。

> [!NOTE]
> 已增強在按鈕上使用影像的功能。 請參閱[使用具有按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相關連結

- [第13章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第13章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用影像](~/xamarin-forms/user-interface/images.md)
- [使用具有按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
