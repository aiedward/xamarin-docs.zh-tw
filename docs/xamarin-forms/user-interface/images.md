---
title: 在 Xamarin.Forms 中的映像
description: 映像可以跨平台與 Xamarin.Forms 共用、 可以載入特別針對每個平台，或它們可以下載供顯示。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: ea7ec0d297e144966f8ff7545e6b12160f66c9b0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935536"
---
# <a name="images-in-xamarinforms"></a>在 Xamarin.Forms 中的映像

_映像可以跨平台與 Xamarin.Forms 共用、 可以載入特別針對每個平台，或它們可以下載供顯示。_

影像是應用程式瀏覽、 可用性和商標的一個重要部分。 Xamarin.Forms 應用程式必須能夠跨所有平台上共用映像，但也可能會在每個平台上顯示不同的映像。

特定平台映像也是必要的圖示和啟動顯示畫面。這些必須設定每個平台。

本文將討論下列主題：

- [ **本機映像**](#Local_Images) -顯示映像隨附於應用程式，包括解析原生的解決方法，如 iOS Retina、 Android 或 UWP 高 DPI 的版本映像。
- [ **內嵌影像**](#Embedded_Images) -顯示影像內嵌為組件資源。
- [ **下載映像**](#Downloading_Images) -下載，並顯示影像。
- [ **圖示和啟動顯示畫面**](#Icons_and_splashscreens) -平台專屬圖示和啟動影像。

## <a name="displaying-images"></a>顯示影像

使用 Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)檢視來顯示網頁上的映像。 它有兩個重要屬性：

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) -An [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)執行個體、 檔案、 Uri 或資源，設定要顯示的影像。
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -如何調整大小的影像，它顯示 （是否要自動縮放、 裁剪或 letterbox） 內的範圍內。

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) 您可以使用靜態方法，每種類型的映像來源取得執行個體：

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -需要檔案名稱或可解析每個平台上的檔案路徑。
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -例如，需要的 Uri 物件。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -需要內嵌在應用程式或.NET Standard 程式庫專案，為影像檔的資源識別元**建置動作： EmbeddedResource**。
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -需要提供影像資料的資料流。

[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/)屬性會決定如何將影像會縮放以符合顯示區域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -會自動縮放以完全並完全填滿顯示區域的映像。 這可能導致正在失真的映像。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，同時保留長寬滿顯示區域 (亦即。 不失真)。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -上下黑邊 （如有必要） 的映像，讓整個影像放入顯示區域，具有空白空間加入至頂端/底端或側邊，取決於是否映像已寬或高。

可從載入映像[本機檔案](#Local_Images_in_Xaml)，則[內嵌資源](#embedded_images)，或[下載](#Downloading_Images)。

<a name="Local_Images" />

## <a name="local-images"></a>本機映像

映像檔可以加入至每個應用程式專案，並從 Xamarin.Forms 共用程式碼參考。 跨所有應用程式，使用單一映像*必須在每個平台上使用相同的檔案名稱*，而且它應該是有效的 Android 資源名稱 (亦即。 允許小寫字母、 數字、 底線和句點)。

- **iOS** -慣用的方式來管理和支援映像，因為 iOS 9 是使用**資產目錄映像集**，其中應包含的所有支援各種裝置和縮放因數所需的映像版本應用程式。 如需詳細資訊，請參閱 <<c0> [ 新增至資產目錄映像設定的映像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -將放在映像**資源/drawable**目錄，內含**建置動作： AndroidResource**。 也可以提供的映像的高和低 DPI 版本 (在適當命名**資源**子目錄，例如**drawable ldpi**， **drawable hdpi**，和**drawable xhdpi**)。
- **通用 Windows 平台 (UWP)** -使用應用程式的根目錄中放置影像**建置動作： 內容**。

> [!IMPORTANT]
> 在 iOS 9 之前, 的映像通常被放入**資源**資料夾中的，使用**建置動作： BundleResource**。 不過，apple 已被取代的 iOS 應用程式中的映像使用此方法。 如需詳細資訊，請參閱 <<c0> [ 映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵守檔案命名和放置這些規則可讓下列 XAML，載入並顯示在所有平台上的映像：

```xaml
<Image Source="waterfront.jpg" />
```

對等的 C# 程式碼如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

下列螢幕擷取畫面顯示的結果，顯示每個平台上的本機映像：

[![本機的 ImageSource](images-images/local-sml.png "範例應用程式顯示本機映像")](images-images/local.png#lightbox "範例顯示本機映像的應用程式")

更多的彈性`Device.RuntimePlatform`屬性可以用來選取不同的映像檔案或路徑的部分或所有平台，此程式碼範例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 跨所有平台使用相同的映像檔名的名稱必須是有效的所有平台。 Android 可繪製資源來有命名限制 – 僅小寫字母、 數字、 底線和句點允許 – 和跨平台相容性這必須遵循所有其他平台上過。 範例檔名**waterfront.png**如下所示的規則，但不是正確的檔案名稱的範例包括"water front.png"，"WaterFront.png"，"water-front.png"和"wåterfront.png 」。

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>原生解析度 （Retina 和高 DPI）

iOS、 Android 和 UWP 包括支援針對不同的影像解析度，其中作業系統會選擇適當的映像，在執行階段根據裝置的功能。 Xamarin.Forms 會針對載入本機映像，因此它會自動支援替代的解決方法，若檔案正確命名並位於專案中使用原生的平台 Api。

因為 iOS 9 管理映像的慣用的方法是將拖曳至適當的資產目錄的影像集所需的每種解析度的映像。 如需詳細資訊，請參閱 <<c0> [ 新增至資產目錄映像設定的映像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前 retina 版本的映像可放在**資源**資料夾-二及第三次使用解析**@2x**或是**@3x**上的檔案名稱副檔名 （例如前面的尾碼。 **myimage@2x.png**). 不過，apple 已被取代的 iOS 應用程式中的映像使用此方法。 如需詳細資訊，請參閱 <<c0> [ 映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 的替代解析度影像應該置於[蓄意名為目錄](http://developer.android.com/guide/practices/screens_support.html)在 Android 專案中，如下列螢幕擷取畫面所示：

[![Android 的多個高解析度影像位置](images-images/xs-highdpisolution-sml.png "Android 多個高解析度的影像位置")](images-images/xs-highdpisolution.png#lightbox "Android 多個高解析度的影像位置")

UWP 映像檔名[可以加`.scale-xxx`副檔名前面](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中`xxx`是套用到資產，例如縮放比例的百分比**myimage.scale 200.png**。 映像參考中的程式碼或 XAML 未擴展修飾詞，例如只**myimage.png**。 平台將會選取最接近的適當的資產規模，取決於顯示的目前 DPI。

### <a name="additional-controls-that-display-images"></a>顯示影像的其他控制項

有些控制項具有屬性的顯示影像，例如：

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -任何頁面上，衍生自類型`Page`已經[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)並[ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/)可指派的本機檔案參考的屬性。 在某些情況下，例如當[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)顯示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，如果平台支援，則會顯示圖示。

  > [!IMPORTANT]
  > 在 iOS 上， [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)無法填入屬性，從映像中的資產目錄的影像集。 相反地，載入圖示的映像`Page.Icon`屬性從**資源**iOS 專案中的資料夾。

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -具有[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/)可以設定為本機檔案參考的屬性。
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -具有[ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/)從本機檔案、 內嵌的資源或在 URI 中擷取可以設定為映像的屬性。

<a name="embedded_images" />

## <a name="embedded-images"></a>內嵌影像

內嵌的影像也隨附的應用程式 （例如本機映像），但檔案內嵌為資源組件中而不需要在每個應用程式檔案結構中的映像的映像的複本。 此散發映像的方式特別適合用來建立元件，因為映像隨附的程式碼。

若要將影像內嵌在專案中，以滑鼠右鍵按一下要加入新項目，然後選取您想要新增映像/秒。 根據預設會將映像**建置動作： 無**; 這必須設為**建置動作： EmbeddedResource**。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**可以檢視和變更**屬性**視窗中的檔案。

在此範例是資源識別碼**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**此專案的檔案名稱中，使用每個值之間的句號 （.）。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**也可以檢視和變更**屬性**填補的檔案。
此面板會顯示**資源識別碼**用來參考程式碼中的資源。 在下面的螢幕擷取畫面**資源識別碼**是**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**此專案的檔案名稱中，使用每個值之間的句號 （.）。
此識別碼可以在中編輯**屬性**平台，但如需這些範例值**WorkingWithImages.beach.jpg**將使用。

![](images-images/xs-embeddedproperties.png "EmbeddedResource Properties Pad")

-----

如果您在專案中將內嵌的影像放入資料夾，資料夾名稱會也隔開句號 （.） 中的資源 id。 移動**beach.jpg**映像到名為的資料夾**MyImages**可能會導致資源識別碼**WorkingWithImages.MyImages.beach.jpg**

若要將內嵌的影像載入程式碼只會將傳遞**資源識別碼**要[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)方法，如下所示：

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> 若要支援在通用 Windows 平台上的 [發行] 模式中顯示內嵌的影像，就必須使用的多載`ImageSource.FromResource`，指定要在其中搜尋映像的來源組件。

目前沒有任何隱含的轉換，資源識別元。 相反地，您必須使用[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)或`new ResourceImageSource()`載入內嵌的影像。

下列螢幕擷取畫面顯示每個平台上顯示內嵌的影像的結果：

[![ResourceImageSource](images-images/resource-sml.png "範例應用程式顯示內嵌的影像")](images-images/resource.png#lightbox "範例應用程式顯示內嵌的影像")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>使用 XAML

因為沒有任何內建的型別轉換子，從`string`至`ResourceImageSource`，這些類型的映像無法以原生方式載入的 XAML。 相反地，撰寫簡單的自訂 XAML 標記延伸，將使用的映像**資源識別碼**XAML 中指定：

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
> 若要支援在通用 Windows 平台上的 [發行] 模式中顯示內嵌的影像，就必須使用的多載`ImageSource.FromResource`，指定要在其中搜尋映像的來源組件。

若要使用此擴充功能加入自訂`xmlns`到 XAML 中，使用正確的命名空間和組件值的專案。 影像來源可以設定使用此語法： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 範例如下所示：

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

### <a name="troubleshooting-embedded-images"></a>疑難排解的內嵌的影像

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>偵錯程式碼

它有時很難了解為什麼未載入特定的映像資源，因為下列偵錯程式碼可以暫時新增的應用程式，以協助確認已正確設定的資源。 它會輸出到指定的組件中內嵌的所有已知的資源<span class="UIItem">主控台</span>協助偵錯載入問題的資源。

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

#### <a name="images-embedded-in-other-projects"></a>內嵌在其他專案中的映像

根據預設，`ImageSource.FromResource`方法只會尋找與程式碼呼叫相同的組件中的映像`ImageSource.FromResource`方法。 使用上方的偵錯程式碼，可以判斷哪些組件包含特定的資源，藉由變更`typeof()`陳述式來`Type`已知為每個組件中。

不過，指定要搜尋的內嵌影像的來源組件，做為引數`ImageSource.FromResource`方法：

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

<a name="Downloading_Images" />

## <a name="downloading-images"></a>下載映像

下列 XAML 所示，映像，您可以自動下載的顯示：

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

[ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/)方法需要`Uri`物件，並傳回新[ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)自讀取`Uri`。

因此也適用於下列的範例，也會產生 URI 字串的隱含轉換：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

下列螢幕擷取畫面顯示結果的每個平台上顯示的遠端的映像：

[![下載 ImageSource](images-images/download-sml.png "範例應用程式顯示下載的映像")](images-images/download.png#lightbox "範例顯示下載的映像的應用程式")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>下載的映像快取

A [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)也支援快取的下載映像，透過下列屬性：

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -是否啟用快取 (`true`預設情況下)。
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A`TimeSpan`定義多久映像會儲存在本機。

快取預設為啟用，並將儲存在本機的 24 小時內的映像。 若要停用特定的映像快取，請具現化映像來源，如下所示：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

若要設定特定快取期間 （例如，5 天） 具現化映像來源，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

內建的快取，可以很容易支援案例，例如每個儲存格捲動的映像，您可以設定 （或繫結） 映像的清單，並讓內建的快取負責重新載入映像，當儲存格捲動至檢視。

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>圖示和啟動顯示畫面

雖然無關[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)檢視、 應用程式圖示和啟動顯示畫面也是一個重要用途的 Xamarin.Forms 專案中的映像。

設定圖示和啟動顯示畫面的 Xamarin.Forms 應用程式是在每個應用程式專案來完成。 這表示正在產生正確調整適用於 iOS、 Android 和 UWP 映像。 這些映像應進行命名，並位於根據每個平台的需求。

## <a name="icons"></a>圖示

請參閱[iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)， [Google 遙控器](http://developer.android.com/design/style/iconography.html)，並[指導方針 圖格和圖示的資產](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)如需有關建立這些應用程式資源。

## <a name="splashscreens"></a>啟動顯示畫面

只有 iOS 和 UWP 應用程式需要啟動顯示畫面 （也稱為啟動畫面或預設映像）。

請參閱文件[iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)並[啟動顯示畫面](/windows/uwp/launch-resume/splash-screens/)Windows 開發人員中心上。

## <a name="summary"></a>總結

Xamarin.Forms 提供各種不同的方式，跨平台應用程式，允許跨平台使用相同的映像，或指定的特定平台映像中包含映像。 已下載的映像也會自動快取，自動化常見的程式碼撰寫案例。

應用程式圖示和啟動顯示畫面的映像已設定，並設定與非 Xamarin.Forms 應用程式-遵循用於特定平台應用程式的相同指導方針。

## <a name="related-links"></a>相關連結

- [WorkingWithImages （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 的遙控器](http://developer.android.com/design/style/iconography.html)
- [圖格和圖示的資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
