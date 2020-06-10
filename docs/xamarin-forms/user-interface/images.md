---
title： "中的影像 Xamarin.Forms 描述：" 影像可以透過在平臺間共用 Xamarin.Forms ，也可以特別針對每個平臺載入，也可以下載以供顯示。
assetid： C025AB53-05CC-49BA-9815-75D6DF9E40B7 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：12/04/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="images-in-xamarinforms"></a>中的影像Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_您可以透過在不同平臺之間共用映射 Xamarin.Forms ，也可以特別為每個平臺載入，也可以下載以供顯示。_

映射是應用程式流覽、使用性和商標的重要部分。 Xamarin.Forms應用程式必須能夠跨所有平臺共用映射，但在每個平臺上也可能顯示不同的映射。

圖示和啟動顯示畫面也需要平臺特定影像;這些需要以每個平臺為基礎進行設定。

## <a name="display-images"></a>顯示影像

Xamarin.Forms使用 [`Image`](xref:Xamarin.Forms.Image) view 在頁面上顯示影像。 它有兩個重要的屬性：

- [`Source`](xref:Xamarin.Forms.Image.Source)- [`ImageSource`](xref:Xamarin.Forms.ImageSource) 實例，也就是檔案、Uri 或資源，可設定要顯示的影像。
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect)-如何在其顯示的範圍內調整影像大小（是否要延展、裁剪或黑邊）。

[`ImageSource`](xref:Xamarin.Forms.ImageSource)您可以針對每種影像來源類型使用靜態方法來取得實例：

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))-需要可在每個平臺上解析的檔案名或 filepath。
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))-需要 Uri 物件，例如  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)-需要資源識別碼給內嵌在應用程式或 .NET Standard 程式庫專案中的影像檔案，並具有**組建動作： EmbeddedResource**。
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))-需要提供影像資料的資料流程。

[`Aspect`](xref:Xamarin.Forms.Image.Aspect)屬性會決定如何縮放影像以符合顯示區域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)-將影像完全伸展，完全填滿顯示區域。 這可能會導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-將影像裁剪，使其填滿顯示區域，同時保留外觀（也就是沒有失真）。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Letterboxes 影像（如有必要），使整個影像符合顯示區域，並根據影像是寬或高度，將空白空間加入至上/下或側邊。

您可以從[本機](#local-images)檔案、[內嵌資源](#embedded-images)、[下載](#download-images)或從資料流程載入影像。 此外，您可以在 [`Image`](xref:Xamarin.Forms.Image) 物件中指定字型圖示資料，以顯示字型圖示 `FontImageSource` 。 如需詳細資訊，請參閱字型[指南中](~/xamarin-forms/user-interface/text/fonts.md)的[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="local-images"></a>本機影像

影像檔案可以加入至每個應用程式專案，並從 Xamarin.Forms 共用程式碼參考。 當影像是平台專用的 (例如，在不同平台上使用不同的解析度) 或屬於略微不同的設計時，就需要這種影像散發方法。

若要在所有應用程式中使用單一映射，*必須在每個平臺上使用相同的檔案名*，而且它應該是有效的 Android 資源名稱（例如，只允許小寫字母、數位、底線和句點）。

- **ios** -自 ios 9 起，管理和支援映射的慣用方法是使用**資產目錄映射集**，其中應包含支援應用程式的各種裝置和調整因素所需的所有映射版本。 如需詳細資訊，請參閱[將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -將影像放在**資源/** 圖形目錄中，具有**組建動作： AndroidResource**。 也可以提供影像的高和低 DPI 版本（在適當命名的**資源**子目錄中，例如可**繪製的 lDPI**、可**繪製-hDPI**和可**繪製-xhDPI**）。
- **通用 Windows 平臺（UWP）** -根據預設，映射應該放在應用程式的根目錄中，其**組建動作為： Content**。 或者，映射可以放在不同的目錄中，然後使用平臺特定的來指定。 如需詳細資訊，請參閱[Windows 上的預設影像目錄](~/xamarin-forms/platform/windows/default-image-directory.md)。

> [!IMPORTANT]
> 在 iOS 9 之前，映射通常會放在 [**資源**] 資料夾中，並具有**組建動作： BundleResource**。 不過，Apple 已淘汰在 iOS 應用程式中使用影像的這種方法。 如需詳細資訊，請參閱[影像大小和檔案名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵循這些檔案命名和放置規則，可讓下列 XAML 在所有平臺上載入及顯示影像：

```xaml
<Image Source="waterfront.jpg" />
```

對等的 c # 程式碼如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

下列螢幕擷取畫面顯示在每個平臺上顯示本機影像的結果：

[![顯示本機影像的範例應用程式](images-images/local-sml.png)](images-images/local.png#lightbox)

如需更多彈性 `Device.RuntimePlatform` ，可以使用屬性來為部分或所有平臺選取不同的影像檔案或路徑，如下列程式碼範例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要在所有平臺上使用相同的映射檔案名，此名稱在所有平臺上都必須是有效的。 Android 可繪製資源有命名限制–只允許小寫字母、數位、底線和句點–而且針對跨平臺的相容性，也必須在所有其他平臺上遵循。 範例檔案名**waterfront.png**遵循規則，但無效檔案名的範例包括 "水 front.png"、"WaterFront.png"、"water-front.png" 和 "wåterfront.png"。

### <a name="native-resolutions-retina-and-high-dpi"></a>原生解析度（retina 和高 DPI）

iOS、Android 和 UWP 包含不同映射解析度的支援，其中作業系統會根據裝置的功能，在執行時間選擇適當的映射。 Xamarin.Forms會使用原生平臺的 Api 來載入本機影像，因此，如果檔案正確命名並位於專案中，它就會自動支援替代的解析度。

在 iOS 9 之後管理映射的慣用方式，是將影像拖曳到適當的資產目錄映射集所需的每個解析度。 如需詳細資訊，請參閱[將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前，您可以將映射的 retina 版本放在**Resources**資料夾中，並在 **@2x** **@3x** 副檔名前面加上一個或尾碼（例如， **myimage@2x.png**). 不過，Apple 已淘汰在 iOS 應用程式中使用影像的這種方法。 如需詳細資訊，請參閱[影像大小和檔案名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 替代解析度映射應該放在 Android 專案中的[特殊命名目錄](https://developer.android.com/guide/practices/screens_support.html)中，如下列螢幕擷取畫面所示：

[![Android 多解析度映射位置](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

UWP 影像檔案名稱的[尾碼前面可以加上 `.scale-xxx` 副檔名](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中 `xxx` 是套用到資產的縮放比例，例如**myimage.scale-200.png**。 然後，可以在程式碼或 XAML 中參考影像，而不需要調整修飾詞，例如只**myimage.png**。 平臺會根據顯示器的目前 DPI 來選取最接近的適當資產規模。

### <a name="additional-controls-that-display-images"></a>顯示影像的其他控制項

有些控制項具有顯示影像的屬性，例如：

- [`Button`](xref:Xamarin.Forms.Button)具有 [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) 可設定為要顯示在上之點陣圖影像的屬性 `Button` 。 如需詳細資訊，請參閱搭配[按鈕使用點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。
- [`ImageButton`](xref:Xamarin.Forms.Button)具有 [`Source`](xref:Xamarin.Forms.ImageButton.Source) 屬性，可設定為要顯示在中的影像 `ImageButton` 。 如需詳細資訊，請參閱[設定映射來源](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source)。
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)具有 [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 可設定為從檔案、內嵌資源、URI 或資料流程載入之影像的屬性。
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)具有 [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) 可設定為從檔案、內嵌資源、URI 或資料流程抓取之影像的屬性。
- [`Page`](xref:Xamarin.Forms.Page). 衍生自的任何頁面類型 `Page` 都具有 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 和 [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) 屬性，可以指派檔案、內嵌資源、URI 或資料流程。 在某些情況下（例如當顯示時） [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，如果平臺支援，則會顯示圖示。

  > [!IMPORTANT]
  > 在 iOS 上， [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 無法從資產目錄映射集中的影像填入屬性。 相反地，會 `Page.IconImageSource` 從檔案、內嵌資源、URI 或資料流程載入屬性的圖示影像。

## <a name="embedded-images"></a>內嵌影像

內嵌影像也會隨附于應用程式（例如本機影像），而不是在每個應用程式的檔案結構中擁有影像複本，而是將影像檔內嵌在元件中做為資源。 在每個平臺上使用相同的影像，且特別適合用來建立元件時，建議您使用這種散發映射的方法，因為映射會與程式碼配套。

若要在專案中內嵌影像，請以滑鼠右鍵按一下以加入新專案，然後選取您想要新增的影像。 根據預設，映射會有**組建動作： None**;這必須設定為 [**建立動作： EmbeddedResource**]。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將組建動作設定為內嵌資源](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

您可以在檔案的 [**屬性**] 視窗中，查看和變更 [**建立] 動作**。

在此範例中，資源識別碼是**WorkingWithImages.beach.jpg**。
IDE 已藉由將此專案的**預設命名空間**與檔案名串連，來產生此預設值，並在每個值之間使用句號（.）。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

您也可以在檔案的**Properties** pad 中查看和變更 [**建立] 動作**。
此面板會顯示在程式碼中用來參考資源的**資源識別碼**。 在下面的螢幕擷取畫面中，**資源識別碼**是**WorkingWithImages.beach.jpg**。
IDE 已藉由將此專案的**預設命名空間**與檔案名串連，來產生此預設值，並在每個值之間使用句號（.）。
您可以在**Properties** pad 中編輯此識別碼，但在這些範例中，將會使用**WorkingWithImages.beach.jpg**的值。

[![內嵌資源屬性 pad](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

如果您將內嵌影像放到專案內的資料夾中，資料夾名稱也會在資源識別碼中以句點（.）分隔。 將**beach.jpg**映射移至名為**MyImages**的資料夾，將會產生資源識別碼**WorkingWithImages.MyImages.beach.jpg**

載入內嵌影像的程式碼只會將**資源識別碼**傳遞給方法，如下 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 所示：

```csharp
var embeddedImage = new Image {
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg",
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> 若要支援在通用 Windows 平臺的 [發行] 模式中顯示內嵌影像，必須使用的多載 `ImageSource.FromResource` ，指定要在其中搜尋影像的來源元件。

目前沒有資源識別碼的隱含轉換。 相反地，您必須使用 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 或 `new ResourceImageSource()` 來載入內嵌影像。

下列螢幕擷取畫面顯示在每個平臺上顯示內嵌影像的結果：

[![顯示內嵌影像的範例應用程式](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

因為沒有從到的內建類型轉換器 `string` ，所以 `ResourceImageSource` XAML 無法以原生方式載入這些類型的影像。 相反地，您可以使用 XAML 中指定的**資源識別碼**，撰寫簡單的自訂 XAML 標記延伸來載入影像：

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> 若要支援在通用 Windows 平臺的 [發行] 模式中顯示內嵌影像，必須使用的多載 `ImageSource.FromResource` ，指定要在其中搜尋影像的來源元件。

若要使用此延伸模組 `xmlns` ，請使用專案的正確命名空間和元件值，將自訂加入至 XAML。 接著，您可以使用此語法來設定映射來源： `{local:ImageResource WorkingWithImages.beach.jpg}` 。 完整的 XAML 範例如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>針對內嵌影像進行疑難排解

#### <a name="debug-code"></a>偵錯程式碼

因為有時候難以瞭解為何無法載入特定影像資源，所以可以暫時將下列偵錯工具代碼新增至應用程式，以協助確認資源已正確設定。 它會將內嵌在指定元件中的所有已知資源輸出到**主控台**，以協助偵錯工具資源載入問題。

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>內嵌在其他專案中的影像

根據預設， `ImageSource.FromResource` 方法只會在呼叫方法的程式碼所在的相同元件中尋找影像 `ImageSource.FromResource` 。 使用上述的偵錯工具程式碼，您可以藉由將 `typeof()` 語句變更為 `Type` 每個元件中已知的，來判斷哪些元件包含特定的資源。

不過，要搜尋內嵌影像的來源元件，可以指定為方法的引數 `ImageSource.FromResource` ：

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>下載映射

影像可以自動下載以供顯示，如下列 XAML 所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

對等的 c # 程式碼如下所示：

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

[`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法需要 `Uri` 物件，並傳回 [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) 從讀取的新 `Uri` 。

此外，URI 字串也有隱含的轉換，因此下列範例也可以使用：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

下列螢幕擷取畫面顯示在每個平臺上顯示遠端影像的結果：

[![顯示已下載影像的範例應用程式](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>已下載影像快取

[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)也支援快取下載的影像，並透過下列屬性設定：

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled)-是否啟用快取（ `true` 預設為）。
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)- `TimeSpan` 定義影像儲存在本機的時間長度。

預設會啟用快取，並在本機將映射儲存24小時。 若要停用特定映射的快取，請將映射來源具現化，如下所示：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

若要設定特定的快取期間（例如5天），請將映射來源具現化，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

內建快取可讓您非常輕鬆地支援影像清單之類的案例，您可以在其中設定（或系結）每個資料格中的影像，並讓內建快取在資料格向上滾動到視野時，負責重新載入影像。

## <a name="animated-gifs"></a>動畫 Gif

Xamarin.Forms包含顯示小型動畫 Gif 的支援。 這是藉由將 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性設定為動畫 gif 檔案來完成：

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> 雖然中的動畫 GIF 支援 Xamarin.Forms 包含下載檔案的能力，但它不支援快取或串流動畫 gif。

根據預設，載入動畫 GIF 時不會播放。 這是因為 `IsAnimationPlaying` 控制動畫 GIF 是否現正播放或停止的屬性具有的預設值 `false` 。 類型的這個屬性 `bool` 是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，並已設定樣式。

因此，載入動畫 GIF 時，除非屬性設定為，否則不會播放它 `IsAnimationPlaying` `true` 。 藉由將 `IsAnimationPlaying` 屬性設定為，即可停止播放 `false` 。 請注意，顯示非 GIF 影像來源時，這個屬性沒有作用。

> [!NOTE]
> 在 Android 上，動畫 GIF 支援需要您的應用程式使用快速轉譯器，如果您選擇使用舊版轉譯器，則無法使用。
> 在 UWP 上，動畫 GIF 支援需要最低版本的 Windows 10 年度更新版（版本1607）。

## <a name="icons-and-splash-screens"></a>圖示和啟動顯示畫面

雖然與 [`Image`](xref:Xamarin.Forms.Image) 視圖無關，應用程式圖示和啟動顯示畫面也是專案中影像的重要用法 Xamarin.Forms 。

應用程式的設定圖示和啟動顯示畫面 Xamarin.Forms 是在每個應用程式專案中完成。 這表示會為 iOS、Android 和 UWP 產生正確大小的影像。 這些映射應該根據每個平臺的需求命名和定位。

## <a name="icons"></a>圖示

如需有關建立這些應用程式資源的詳細資訊，請參閱[適用于磚和圖示資產](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)的[iOS 使用影像](~/ios/app-fundamentals/images-icons/index.md)、 [Google 圖示](https://developer.android.com/design/style/iconography.html)和 UWP 指導方針。

此外，您可以在 [`Image`](xref:Xamarin.Forms.Image) 物件中指定字型圖示資料，以顯示字型圖示 `FontImageSource` 。 如需詳細資訊，請參閱字型[指南中](~/xamarin-forms/user-interface/text/fonts.md)的[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="splash-screens"></a>啟動顯示畫面

只有 iOS 和 UWP 應用程式需要啟動顯示畫面（也稱為啟動畫面或預設影像）。

請參閱 Windows 開發人員中心上的[使用影像](~/ios/app-fundamentals/images-icons/index.md)和[啟動](/windows/uwp/launch-resume/splash-screens/)顯示畫面的 iOS 檔。

## <a name="related-links"></a>相關連結

- [WorkingWithImages （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS 使用影像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 圖示](https://developer.android.com/design/style/iconography.html)
- [磚和圖示資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
