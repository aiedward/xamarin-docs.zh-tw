---
title: 中的影像 Xamarin.Forms
description: 您可以使用跨平臺共用映射 Xamarin.Forms ，也可以特別為每個平臺載入，也可以下載這些映射以供顯示。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2ea65a646add3f42ee88e5cd884e80131b9964bf
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373831"
---
# <a name="images-in-no-locxamarinforms"></a>中的影像 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithimages)

_您可以使用跨平臺共用映射 Xamarin.Forms ，也可以特別為每個平臺載入，也可以下載這些映射以供顯示。_

影像是應用程式流覽、可用性和商標的重要部分。 Xamarin.Forms 應用程式必須能夠在所有平臺之間共用影像，但也可能會在每個平臺上顯示不同的影像。

圖示和啟動顯示畫面也需要平臺特定映射;這些需要以每個平臺為基礎進行設定。

## <a name="display-images"></a>顯示影像

Xamarin.Forms 使用 [`Image`](xref:Xamarin.Forms.Image) 視圖在頁面上顯示影像。 它有幾個重要的屬性：

- [`Source`](xref:Xamarin.Forms.Image.Source) - [`ImageSource`](xref:Xamarin.Forms.ImageSource) 實例，也就是檔案、Uri 或資源，它會設定要顯示的影像。
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -如何在顯示的範圍內調整影像大小， (是否要延展、裁剪或黑邊) 。

[`ImageSource`](xref:Xamarin.Forms.ImageSource) 您可以針對每個影像來源類型使用靜態方法來取得實例：

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -需要可在每個平臺上解析的檔案名或 filepath。
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -需要 Uri 物件，例如  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -需要資源識別碼以內嵌在應用程式或 .NET Standard 程式庫專案中的影像檔案，並具有 **組建動作： EmbeddedResource** 。
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -需要提供影像資料的資料流程。

[`Aspect`](xref:Xamarin.Forms.Image.Aspect)屬性會決定如何調整影像以符合顯示區域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -將影像完全填滿，完全填滿顯示區域。 這可能會導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，讓它填滿顯示區域，同時保留外觀 (也就是沒有扭曲) 。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -如有必要，請 Letterboxes 影像 () 如此一來，整個影像就會放入顯示區域中，並根據影像的寬度或高度，將空格新增至頂端/底部或側邊。

您可以從 [本機](#local-images)檔案、 [內嵌資源](#embedded-images) [、從](#download-images)資料流程載入或載入映射。 此外，您可以藉 [`Image`](xref:Xamarin.Forms.Image) 由在物件中指定字型圖示資料，來顯示字型圖示 `FontImageSource` 。 如需詳細資訊，請參閱字型[指南中](~/xamarin-forms/user-interface/text/fonts.md)的[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="local-images"></a>本機影像

影像檔案可以加入至每個應用程式專案，並從 Xamarin.Forms 共用程式碼參考。 當影像是平台專用的 (例如，在不同平台上使用不同的解析度) 或屬於略微不同的設計時，就需要這種影像散發方法。

若要在所有應用程式上使用單一映射，您 *必須在每個平臺上使用相同的檔案名* ，其應為有效的 Android 資源名稱 (亦即，) 只允許小寫字母、數位、底線和期間。

- **ios** -自 ios 9 起，管理及支援映射的慣用方式是使用 **資產目錄映射集** ，其中應該包含支援各種裝置的所有映射版本，以及應用程式的規模調整因素。 如需詳細資訊，請參閱 [將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- 使用 **組建動作： AndroidResource** 的 **資源/繪製** 目錄中的 **Android** 預留位置影像。 您也可以在適當命名的 **資源** 子目錄（例如可 **繪製 lDPI** 、可 **繪製 hDPI** 和可 **繪製的 xhDPI** ) ）中 (提供映射的高和低 DPI 版本。
- **通用 Windows 平臺 (UWP)** -根據預設，映射應該放在應用程式的根目錄中，並包含 **組建動作： Content** 。 或者，您可以將影像放在不同的目錄中，然後以平臺特定的方式指定。 如需詳細資訊，請參閱 [Windows 上的預設映射目錄](~/xamarin-forms/platform/windows/default-image-directory.md)。

> [!IMPORTANT]
> 在 iOS 9 之前，映射通常會放在 [ **資源** ] 資料夾中，並具有 **組建動作：套件套件** 。 不過，Apple 已淘汰此方法來處理 iOS 應用程式中的影像。 如需詳細資訊，請參閱 [影像大小和檔案名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵循這些規則來進行檔案命名和放置，可讓下列 XAML 在所有平臺上載入和顯示影像：

```xaml
<Image Source="waterfront.jpg" />
```

對等的 c # 程式碼如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

下列螢幕擷取畫面顯示每個平臺上顯示本機影像的結果：

[![顯示本機影像的範例應用程式](images-images/local-sml.png)](images-images/local.png#lightbox)

為了提供更大的彈性， `Device.RuntimePlatform` 屬性可用來針對部分或所有平臺選取不同的影像檔案或路徑，如下列程式碼範例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要在所有平臺上使用相同的映射檔案名，此名稱在所有平臺上都必須是有效的。 Android 可繪製資源具有命名限制，只允許小寫字母、數位、底線和句號，而針對跨平臺相容性，也必須在所有其他平臺上遵循。 範例檔案名 **waterfront.png** 遵循規則，但無效檔案名的範例包括 "水 front.png"、"WaterFront.png"、"water-front.png" 和 "wåterfront.png"。

### <a name="native-resolutions-retina-and-high-dpi"></a>原生解析度 (retina 和高 DPI) 

iOS、Android 和 UWP 包含不同映射解析度的支援，其中作業系統會根據裝置的功能，在執行時間選擇適當的映射。 Xamarin.Forms 使用原生平臺的 Api 來載入本機映射，因此，如果檔案已正確命名且位於專案中，它會自動支援替代解析度。

在 iOS 9 之後管理映射的慣用方式是將影像拖曳至適當的資產目錄映射集所需的每個解析度。 如需詳細資訊，請參閱 [將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前，您可以將映射的 retina 版本放在 **Resources** 資料夾中，在副檔名前面加上一個或後置詞，然後 **@2x** **@3x** 在副檔名 (例如。 **myimage@2x.png** ). 不過，Apple 已淘汰此方法來處理 iOS 應用程式中的影像。 如需詳細資訊，請參閱 [影像大小和檔案名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 替代解析度映射應放置在 Android 專案中的 [特殊命名目錄](https://developer.android.com/guide/practices/screens_support.html) 中，如下列螢幕擷取畫面所示：

[![Android 多重解析度映射位置](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

UWP 影像檔案名稱 [的尾碼可以是 `.scale-xxx` 副檔名之前](/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中 `xxx` 是套用至資產的縮放比例，例如 **myimage.scale-200.png** 。 然後，您可以在程式碼或 XAML 中參考影像，而不需要縮放修飾詞，例如只是 **myimage.png** 。 平臺會根據顯示器的目前 DPI 來選取最接近的適當資產規模。

### <a name="additional-controls-that-display-images"></a>顯示影像的其他控制項

有些控制項具有顯示影像的屬性，例如：

- [`Button`](xref:Xamarin.Forms.Button) 具有 [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) 屬性，可以設定為要顯示在上的點陣圖影像 `Button` 。 如需詳細資訊，請參閱 [使用具有按鈕的點陣圖](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。
- [`ImageButton`](xref:Xamarin.Forms.Button) 具有 [`Source`](xref:Xamarin.Forms.ImageButton.Source) 屬性，可以設定為要顯示在中的影像 `ImageButton` 。 如需詳細資訊，請參閱 [設定影像來源](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source)。
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 的 [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 屬性可以設定為從檔案、內嵌資源、URI 或串流載入的影像。
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) 具有 [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) 可設定為從檔案、內嵌資源、URI 或串流抓取之影像的屬性。
- [`Page`](xref:Xamarin.Forms.Page). 任何衍生自的頁面類型 `Page` 都有 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 和 [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) 屬性，可以指派檔案、內嵌資源、URI 或串流。 在某些情況下（例如，當 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 顯示時） [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，如果平臺支援，則會顯示圖示。

  > [!IMPORTANT]
  > 在 iOS 上， [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 無法從資產目錄映射集中的映射填入屬性。 相反地，會 `Page.IconImageSource` 從檔案、內嵌資源、URI 或資料流程載入屬性的圖示影像。

## <a name="embedded-images"></a>內嵌影像

內嵌影像也會隨附于應用程式 (例如本機) 影像），而不是在每個應用程式的檔案結構中都有一份影像複本，而是將影像檔內嵌在元件中做為資源。 當您在每個平臺上使用相同的映射時，建議使用這種方式來發佈影像，而且特別適合用來建立元件，因為映射與程式碼配套。

若要在專案中內嵌映射，請以滑鼠右鍵按一下來加入新專案，然後選取您想要新增的映射。 根據預設，映射會有 **組建動作： None** ;這必須設定為 **組建動作： EmbeddedResource** 。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將組建動作設定為內嵌資源](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

您可以在檔案的 [ **屬性** ] 視窗中，查看並變更 **組建動作** 。

在此範例中，資源識別碼是 **WorkingWithImages.beach.jpg** 。
IDE 已產生此預設值，方法是將這個專案的 **預設命名空間** 與檔案名串連在一起，使用句點 (。 ) 每個值之間。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![設定組建動作： EmbeddedResource](images-images/xs-buildaction.png)

您也可以在檔案的 **屬性** 面板中，查看並變更 **組建動作** 。
此 pad 會顯示用來在程式碼中參考資源的 **資源識別碼** 。 在以下螢幕擷取畫面中， **資源識別碼** 是 **WorkingWithImages.beach.jpg** 。
IDE 已產生此預設值，方法是將這個專案的 **預設命名空間** 與檔案名串連在一起，使用句點 (。 ) 每個值之間。
此識別碼可以在 **Properties** pad 中編輯，但在這些範例中，將會使用 **WorkingWithImages.beach.jpg** 的值。

[![內嵌資源屬性面板](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

如果您將內嵌影像放入專案中的資料夾，則資料夾名稱也會以句點分隔 (，) 在資源識別碼中。 將 **beach.jpg** 映射移至名為 **MyImages** 的資料夾，會導致資源識別碼 **WorkingWithImages.MyImages.beach.jpg**

載入內嵌影像的程式碼只會將 **資源識別碼** 傳遞給 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 方法，如下所示：

```csharp
Image embeddedImage = new Image
{
    Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(MyClass).GetTypeInfo().Assembly)
};
```

> [!NOTE]
> 若要支援在通用 Windows 平臺上以發行模式顯示內嵌影像，必須使用的多載 `ImageSource.FromResource` ，以指定要在其中搜尋影像的來源元件。

目前沒有資源識別碼的隱含轉換。 相反地，您必須使用 [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 或 `new ResourceImageSource()` 來載入內嵌的影像。

下列螢幕擷取畫面顯示在每個平臺上顯示內嵌影像的結果：

[![顯示內嵌影像的範例應用程式](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

因為沒有的內建類型轉換器 `string` `ResourceImageSource` ，所以這些類型的影像無法由 XAML 以原生方式載入。 相反地，您可以撰寫簡單的自訂 XAML 標記延伸，以使用 XAML 中指定的 **資源識別碼** 載入影像：

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
> 若要支援在通用 Windows 平臺上以發行模式顯示內嵌影像，必須使用的多載 `ImageSource.FromResource` ，以指定要在其中搜尋影像的來源元件。

若要使用此擴充功能 `xmlns` ，請使用專案的正確命名空間和元件值，將自訂新增至 XAML。 然後，您可以使用下列語法來設定映射來源： `{local:ImageResource WorkingWithImages.beach.jpg}` 。 完整的 XAML 範例如下所示：

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

因為有時很難瞭解為什麼無法載入特定映射資源，所以可以暫時將下列偵錯工具代碼新增至應用程式，以協助確認資源已正確設定。 它會將內嵌在指定元件中的所有已知資源輸出到 **主控台** ，以協助偵測資源載入問題。

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(MyClass).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>內嵌在其他專案中的影像

根據預設，此 `ImageSource.FromResource` 方法只會在與呼叫方法的程式碼相同的元件中尋找影像 `ImageSource.FromResource` 。 使用上述的偵錯工具代碼，您可以藉由將 `typeof()` 語句變更為 `Type` 每個元件中已知的語句，來判斷哪些元件包含特定的資源。

不過，要搜尋的內嵌影像來源元件可以指定為方法的引數 `ImageSource.FromResource` ：

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>下載映射

影像可以自動下載以供顯示，如下列 XAML 所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://aka.ms/campus.jpg" />
    <Label Text="campus.jpg gets downloaded from microsoft.com" />
  </StackLayout>
</ContentPage>
```

對等的 c # 程式碼如下所示：

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://aka.ms/campus.jpg")
     ) };
```

[`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法需要 `Uri` 物件，並傳回 [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) 從讀取的新 `Uri` 。

URI 字串也有隱含轉換，因此下列範例也可以運作：

```csharp
webImage.Source = "https://aka.ms/campus.jpg";
```

下列螢幕擷取畫面顯示每個平臺上顯示遠端影像的結果：

[![顯示已下載影像的範例應用程式](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>下載的影像快取

[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)也支援快取下載的影像，並透過下列屬性進行設定：

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -預設 (是否啟用快取 `true`) 。
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) - `TimeSpan` 定義將映射儲存在本機的時間長度。

預設會啟用快取，並在本機將映射儲存24小時。 若要停用特定映射的快取，請將映射來源具現化，如下所示：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("https://server.com/image") };
```

若要設定特定的快取期間 (例如，5天) 將映射來源具現化，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://aka.ms/campus.jpg"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

內建快取可讓您輕鬆地支援像是滾動影像清單之類的案例，您可以在其中設定 (或系結) 每個資料格中的影像，並讓內建的快取在資料格滾動回查看時，負責重新載入影像。

## <a name="animated-gifs"></a>動畫 Gif

Xamarin.Forms 包含顯示小型動畫 Gif 的支援。 這是藉由將 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性設定為動畫 GIF 檔案來完成的：

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> 雖然中的動畫 GIF 支援 Xamarin.Forms 包括下載檔案的功能，但它不支援快取或串流處理的 gif。

根據預設，當有動畫 GIF 載入時，將不會播放。 這是因為 `IsAnimationPlaying` 屬性（property）會控制動畫 GIF 是否現正播放或停止，其預設值為 `false` 。 這個屬性的型別 `bool` 是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

因此，當動畫 GIF 載入時，將不會播放它，直到 `IsAnimationPlaying` 屬性設定為為止 `true` 。 然後可以將 `IsAnimationPlaying` 屬性設定為，以停止播放 `false` 。 請注意，在顯示非 GIF 影像來源時，這個屬性不會有任何作用。

> [!NOTE]
> 在 Android 上，動畫 GIF 支援要求您的應用程式使用快速轉譯器，如果您選擇使用舊版轉譯器，將無法運作。
> 在 UWP 上，動畫 GIF 支援需要最低版本的 Windows 10 年度更新版 (1607 版) 。

## <a name="icons-and-splash-screens"></a>圖示和啟動顯示畫面

雖然與 [`Image`](xref:Xamarin.Forms.Image) 視圖無關，但應用程式圖示和啟動顯示畫面也是專案中影像的重要用途 Xamarin.Forms 。

設定應用程式的圖示和啟動顯示畫面 Xamarin.Forms 是在每個應用程式專案中完成。 這表示會為 iOS、Android 和 UWP 產生正確大小的影像。 這些映射應該根據每個平臺的需求命名及找出。

## <a name="icons"></a>圖示

如需有關建立這些應用程式資源的詳細資訊，請參閱使用影像、 [Google 圖示](https://developer.android.com/design/style/iconography.html)和[UWP 指導方針來](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)建立這些應用程式資源的[iOS](~/ios/app-fundamentals/images-icons/index.md)。

此外，您可以藉 [`Image`](xref:Xamarin.Forms.Image) 由在物件中指定字型圖示資料，來顯示字型圖示 `FontImageSource` 。 如需詳細資訊，請參閱字型[指南中](~/xamarin-forms/user-interface/text/fonts.md)的[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="splash-screens"></a>啟動顯示畫面

只有 iOS 和 UWP 應用程式需要啟動顯示畫面 (也稱為啟動畫面或預設影像) 。

請參閱 Windows 開發人員中心上 [使用影像](~/ios/app-fundamentals/images-icons/index.md) 和 [啟動](/windows/uwp/launch-resume/splash-screens/) 顯示畫面的 iOS 檔。

## <a name="related-links"></a>相關連結

- [WorkingWithImages (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [使用影像的 iOS](~/ios/app-fundamentals/images-icons/index.md)
- [Android 圖示](https://developer.android.com/design/style/iconography.html)
- [磚和圖示資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)