---
title: 在 Xamarin.Forms 中的影像
description: 圖像可以跨平台與 Xamarin.Forms 共用、 可以載入特別針對每個平台，或它們可以供下載顯示。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: f6815b54867b47bb32ede41470712dac65b6d410
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53062171"
---
# <a name="images-in-xamarinforms"></a>在 Xamarin.Forms 中的影像

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)

_影像可以使用 Xamarin.Forms 進行跨平台共用、可以針對每個平台特別載入，或它們可以下載供顯示。_

影像是應用程式瀏覽、可用性和商標的一個重要部分。 Xamarin.Forms 應用程式必須能夠跨所有平台共用影像，但也可能會在每個平台上顯示不同的影像。

特定平台影像也是圖示和啟動顯示畫面所必須的。這些必須以每個平台為基礎進行設定。

## <a name="displaying-images"></a>顯示影像

使用 Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) 檢視來顯示網頁上的影像。 它有兩個重要屬性：

- [`Source`](xref:Xamarin.Forms.Image.Source) - [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) 執行個體，設定要顯示之影像的檔案、Uri 或資源。
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) - 如何使用影像邊界調整影像的大小 (伸展、裁剪或上下黑邊)。

[`ImageSource`](xref:Xamarin.Forms.ImageSource) 執行個體可以使用每種影像類型來源的靜態方法來取得：

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -需要檔案名稱或可解析每個平台上的檔案路徑。
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -例如，需要的 Uri 物件。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) - 需要內嵌在應用程式或 .NET Standard 程式庫專案之影像檔的資源識別元 (具有**建置動作：EmbeddedResource**)。
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -需要提供影像資料的資料流。

[ `Aspect` ](xref:Xamarin.Forms.Image.Aspect)屬性會決定如何將影像會縮放以符合顯示區域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) - 會自動縮放以完全填滿顯示區域。 這可能導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，同時保留長寬滿顯示區域 (亦即。 不失真)。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -上下黑邊 （如有必要） 的映像讓整個影像放入顯示區域，具有空白空間加入至頂端/底端或側邊，取決於是否該影像是寬或高。

可從[本機檔案](#Local_Images)、[內嵌資源](#embedded-images)或透過 [下載](#Downloading_Images)方式載入影像。

## <a name="local-images"></a>本機影像

影像檔可以加入至每個應用程式專案，並從 Xamarin.Forms 共用程式碼參考。 映像不是平台特定，例如使用不同的解析度不同的平台或稍有不同的設計上需要這種散發映像方法。

若要跨所有應用程式使用單一影像，必須在每個平台上使用相同的檔案名稱，而且它應該是有效的 Android 資源名稱 (亦即，只允許小寫字母、數字、底線與句點)。

- 從 iOS 9 開始，管理及支援影像的慣用方式是使用**資產目錄影像集**，其中應包含支援各種裝置和縮放因數所需的所有影像版本。 如需詳細資訊，請參閱 [新增影像至資產目錄影像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -將影像放在**資源/可繪製資源**目錄，內含**建置動作：AndroidResource**。 也可以提供高和低 DPI 版本的影像 (在適當命名的**資源**子目錄，例如**可繪製資源 ldpi**、**可繪製資源 hdpi** 與**可繪製資源 xhdpi**)。
- **通用 Windows 平台 (UWP)** - 使用**建置動作：內容**將影像放在應用程式的根目錄。

> [!IMPORTANT]
> 在 iOS 9 之前，影像通常是使用**建置動作：BundleResource**放在**資源**資料夾中。 Apple 已不再使用此方法來處理 iOS 應用程式中的影像。 如需詳細資訊，請參閱 [影像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵守這些檔案命名和放置規則可讓下列 XAML 在所有平台上載入並顯示影像：

```xaml
<Image Source="waterfront.jpg" />
```

對等的 C# 程式碼如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

下列螢幕擷取畫面顯示在每個平台上顯示本機影像的結果：

[![本機影像來源](images-images/local-sml.png "顯示本機影像的範例應用程式\"")](images-images/local.png#lightbox "顯示本機影像的範例應用程式")

更多的彈性`Device.RuntimePlatform`屬性可以用來選取不同的圖像檔案或路徑的部分或所有平台，此程式碼範例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要跨所有平台使用相同的影像檔案名稱，該名稱在所有平台上都必須是有效的。 Android 可繪製資源有命名限制 (僅允許小寫字母、數字、底線與句點)，而且針對跨平台相容性，也必須在所有其他平台上依照此規則。 範例檔案名稱 **waterfront.png** 依照此規則，但無效檔案名稱的範例包括 "water front.png"、"WaterFront.png"、"water-front.png" 和 "wåterfront.png"。

### <a name="native-resolutions-retina-and-high-dpi"></a>原生解析度 （Retina 和高 DPI）

iOS、Android 與 UWP 包括針對不同影像解析度的支援，其中作業系統會在執行階段根據裝置的功能選擇適當的影像。 Xamarin.Forms 會針對載入本機影響使用原生平台的 API，因此若，若檔案正確命名並位於專案中，它會自動支援替代的解析度。

從 iOS 9 開始，管理影像的慣用方法是將每個解析度所需要的影像拖曳到適當的資產類別目錄影像集。 如需詳細資訊，請參閱 [新增影像至資產目錄影像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前，視網膜版本的影像可放在**資源**資料夾 - 具有兩倍與三倍解析度的檔案其檔案名稱後方接著 **@2x** 或 **@3x** (例如 **myimage@2x.png**). 不過，Apple 已不再使用此方法來處理 iOS 應用程式中的影像。 如需詳細資訊，請參閱 [影像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 的替代解析度影像應該放在 Android 專案中的[蓄意命名目錄](http://developer.android.com/guide/practices/screens_support.html)中，如下列螢幕擷取畫面所示：

[![Android 的多解析度影像位置](images-images/xs-highdpisolution-sml.png "Android 的多解析度影像位置")](images-images/xs-highdpisolution.png#lightbox "Android 的多解析度影像位置")

UWP 影像檔案名稱[可以附加 `.scale-xxx` 在副檔名前面](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中 `xxx` 是套用到資產的縮放百分比，例如 **myimage.scale 200.png**。 影像可以在程式碼或 XAML 中參考，而不需要使用縮放修飾詞，例如只使用 **myimage.png**。 平台將會根據顯示器的目前 DPI 選取最接近的適當資產比例。

### <a name="additional-controls-that-display-images"></a>顯示影像的其他控制項

有些控制項具有屬性的顯示圖像，例如：

- [`Page`](xref:Xamarin.Forms.Page) -任何頁面上，衍生自類型`Page`已經[ `Icon` ](xref:Xamarin.Forms.Page.Icon)並[ `BackgroundImage` ](xref:Xamarin.Forms.Page.BackgroundImage)可指派的本機檔案參考的屬性。 在某些情況下，例如當[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)顯示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，如果平台支援，則會顯示圖示。

  > [!IMPORTANT]
  > 在 iOS 上，無法從資產類別目錄影像集中的影像填入 [`Page.Icon`](xref:Xamarin.Forms.Page.Icon) 屬性。 相反地，從 iOS 專案中的**資源**資料夾載入 `Page.Icon` 屬性的圖示影像。

- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) -具有[ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon)可以設定為本機檔案參考的屬性。
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) - 具有可設定為從本機檔案、內嵌資源或 URI 擷取之影像的屬性 [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) 屬性。

## <a name="embedded-images"></a>內嵌圖像

內嵌的圖像也隨附在應用程式中 (例如本機影像)，但影像檔案是內嵌在組件中做為資源，而不是在每個應用程式的檔案結構中都有影像的複本。 此方法散發映像的每個平台上使用相同的映像時，建議使用，特別適合用來建立元件，因為映像隨附的程式碼。

若要將影像內嵌在專案中，以滑鼠右鍵按一下要加入新項目，然後選取您想要新增映像/秒。 根據預設會將映像**建置動作： 無**; 這必須設為**建置動作： EmbeddedResource**。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](images-images/vs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**可以檢視和變更**屬性**視窗中的檔案。

在此範例是資源識別碼**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**此專案的檔案名稱中，使用每個值之間的句號 （.）。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](images-images/xs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**也可以檢視和變更**屬性**填補的檔案。
此面板會顯示**資源識別碼**用來參考程式碼中的資源。 在下面的螢幕擷取畫面**資源識別碼**是**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**此專案的檔案名稱中，使用每個值之間的句號 （.）。
此識別碼可以在中編輯**屬性**平台，但如需這些範例值**WorkingWithImages.beach.jpg**將使用。

![](images-images/xs-embeddedproperties.png "EmbeddedResource Properties Pad")

-----

如果您在專案中將內嵌的影像放入資料夾，資料夾名稱會也隔開句號 （.） 中的資源 id。 移動**beach.jpg**映像到名為的資料夾**MyImages**可能會導致資源識別碼**WorkingWithImages.MyImages.beach.jpg**

若要將內嵌的影像載入程式碼只會將傳遞**資源識別碼**要[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*)方法，如下所示：

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> 若要支援在通用 Windows 平台上的發行模式中顯示內嵌的影像，就必須使用 `ImageSource.FromResource` 的多載來指定要在其中搜尋影像的來源組件。

目前沒有任何隱含的轉換，資源識別元。 相反地，您必須使用[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*)或`new ResourceImageSource()`載入內嵌的影像。

下列螢幕擷取畫面顯示在每個平台上顯示內嵌影像的結果：

[![ResourceImageSource](images-images/resource-sml.png "顯示內嵌影像的範例應用程式")](images-images/resource.png#lightbox "顯示內嵌影像的範例應用程式")

### <a name="using-xaml"></a>使用 XAML

因為沒有任何從 `string` 到 `ResourceImageSource` 的內建型別轉換器，這些類型的影像法以原生方式由 XAML 載入。 相反地，撰寫簡單的自訂 XAML 標記延伸，將使用的映像**資源識別碼**XAML 中指定：

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
> 若要支援在通用 Windows 平台上的發行模式中顯示內嵌的影像，就必須使用 `ImageSource.FromResource` 的多載來指定要在其中搜尋影像的來源組件。

若要使用此擴充功能，請使用專案的正確命名空間和組件值，將自訂 `xmlns` 新增到 XAML 中。 接著，可以使用此下列語法來設定影像來源： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 範例如下所示：

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

### <a name="troubleshooting-embedded-images"></a>針對內嵌影像進行疑難排解

#### <a name="debugging-code"></a>偵錯程式碼

因為有時很難了解為什麼未載入特定的影像資源，可以暫時將下列偵錯程式碼新增到應用程式，以協助確認是否已正確設定資源。 它會輸出指定組件中內嵌的所有已知的資源到<span class="UIItem">主控台</span>以協助針對資源載入問題進行偵錯。

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

根據預設，`ImageSource.FromResource` 方法只會尋找與呼叫 `ImageSource.FromResource` 方法相同之程式碼的組件中的影像。 使用上面的偵錯程式碼，可以判斷哪些組件包含特定資源，方式是將 `typeof()` 陳述式變更為組件中存在的 `Type`。

不過，可以將要在其中搜尋內嵌影像的來源組件指定為 `ImageSource.FromResource` 方法的引數：

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="downloading-images"></a>下載影像

您可以自動下載要顯示的影像，如下列 XAML 所示：

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

對等的 C# 程式碼如下所示：

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

[`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法需要 `Uri` 物件，而且會傳回從 `Uri` 讀取的新[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)。

因此也適用於下列的範例，也會產生 URI 字串的隱含轉換：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

下列螢幕擷取畫面顯示在每個平台上顯示遠端影像的結果：

[![下載影像來源](images-images/download-sml.png "顯示已下載之影像的範例應用程式")](images-images/download.png#lightbox "顯示已下載之影像的範例應用程式")

### <a name="downloaded-image-caching"></a>下載的影像快取

[`UriImageSource`](xref:Xamarin.Forms.UriImageSource) 也支援快取下載的影像，這是透過下列屬性所設定：

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -是否啟用快取 (`true`預設情況下)。
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A`TimeSpan`定義多久映像會儲存在本機。

快取預設為啟用，而且會將影像儲存在本機 24 小時。 若要停用特定影像的快取，請具現化映影像來源，如下所示：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

若要設定特定快取期間 (例如，5 天)，請具現化影像來源，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

內建的快取可以很容易支援如影像的捲動清單的案例，在此案例中，您可以在每個呼叫中設定 (或繫結) 影像，並讓內建快取負責在儲存格捲動回檢視中時重新載入影像。

## <a name="icons-and-splash-screens"></a>圖示和啟動顯示畫面

雖然無關[ `Image` ](xref:Xamarin.Forms.Image)還有一個重要用途的 Xamarin.Forms 專案中的映像檢視中，應用程式圖示和啟動顯示畫面。

設定圖示和啟動顯示畫面的 Xamarin.Forms 應用程式是在每個應用程式專案來完成。 這表示正在產生適用於 iOS、Android 和 UWP 的正確大小影像。 這些影像應該根據每個平台的需求進行命名及放置。

## <a name="icons"></a>圖示

請參閱[iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)， [Google 遙控器](http://developer.android.com/design/style/iconography.html)，並[指導方針 圖格和圖示的資產](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)如需有關建立這些應用程式資源。

## <a name="splash-screens"></a>啟動顯示畫面

只有 iOS 和 UWP 應用程式需要啟動顯示畫面 （也稱為啟動畫面或預設映像）。

請參閱 Windows 開發人員中心上的文件[iOS 處理影像](~/ios/app-fundamentals/images-icons/index.md)與[啟動顯示畫面](/windows/uwp/launch-resume/splash-screens/)。

## <a name="summary"></a>總結

Xamarin.Forms 提供各種不同的方式在跨平台應用程式中包含影像，允許跨平台使用相同的影像或允許指定平台特定影像。 系統也會自動快取已下載的影像，進而自動化常見的程式碼撰寫案例。

應用程式圖示和啟動顯示畫面的映像已設定，並設定與非 Xamarin.Forms 應用程式-遵循用於特定平台應用程式的相同指導方針。

## <a name="related-links"></a>相關連結

- [WorkingWithImages （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 的遙控器](http://developer.android.com/design/style/iconography.html)
- [圖格和圖示的資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
