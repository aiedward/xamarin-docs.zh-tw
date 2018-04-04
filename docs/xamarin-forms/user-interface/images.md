---
title: 影像
description: 可以跨平台與 Xamarin.Forms 共用映像、 可以載入特別針對每個平台，或它們可以顯示為下載。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: ddbcb74d34f09c7bb60891148bd50b36bc5094c3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="images"></a>影像

_可以跨平台與 Xamarin.Forms 共用映像、 可以載入特別針對每個平台，或它們可以顯示為下載。_

影像是應用程式瀏覽、 可用性和品牌的一個重要部分。 Xamarin.Forms 應用程式必須能夠跨所有平台上共用映像，但也可能會在每個平台上顯示不同的影像。

特定平台映像也是必要的圖示和啟動顯示畫面。這些需要設定每個平台為基礎。

本文將討論下列主題：

- [ **本機的映像**](#Local_Images) -顯示映像隨附於應用程式，包括解決 iOS Retina、 Android 或 UWP 高 DPI 版本的映像等原生解析度。
- [ **內嵌影像**](#Embedded_Images) -顯示影像內嵌為組件資源。
- [ **下載映像**](#Downloading_Images) -下載，並顯示影像。
- [ **圖示和啟動顯示畫面**](#Icons_and_splashscreens) -平台專屬圖示和啟動映像。

## <a name="displaying-images"></a>顯示影像

Xamarin.Forms 使用[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)檢視頁面上顯示影像。 它有兩個重要屬性：

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) - [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)執行個體，檔案、 Uri 或資源，設定要顯示的影像。
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -如何調整大小和顯示 （不論縮放、 裁剪或 letterbox） 內的範圍內的映像。

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) 您可以使用靜態方法的每個影像來源類型取得執行個體：

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -需要檔名或可以解決每個平台的檔案路徑。
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -需要 Uri 物件，例如。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -需要內嵌至應用程式或 PCL 中，映像檔案的資源識別元**建置動作： EmbeddedResource**。
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -需要提供影像資料的資料流。

[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/)屬性會決定如何將縮放影像以符合顯示區域：

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -伸展影像以完全並完全填滿顯示區域。 這可能會導致正在失真的映像。
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -裁剪影像，使它填滿顯示區域，同時保留外觀 (ie。 不失真)。
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -Letterboxes 映像 （如有必要） 使整個影像納入 [顯示] 區域中，具有空白空間上/下或兩側取決於是否加入影像是寬度或高度。

可以載入影像，從[本機檔案](#Local_Images_in_Xaml)、[內嵌資源](#embedded_images)，或[下載](#Downloading_Images)。

<a name="Local_Images" />

## <a name="local-images"></a>本機的映像

映像檔可以加入至每個應用程式專案，並從 Xamarin.Forms 共用程式碼參考。 若要使用的單一映像跨所有應用程式，*相同的檔案名稱必須使用每個平台*，且應該是有效的 Android 資源名稱 (ie。 允許小寫字母、 數字、 底線和句號)。

- **iOS** -慣用的方式來管理和支援映像，因為 iOS 9 是要使用**資產目錄映像集**，其中應包含的所有支援各種裝置和縮放因數所需的映像的版本應用程式。 如需詳細資訊，請參閱[加入影像資產目錄映像設定](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -將放在映像**資源/drawable**目錄用於**建置動作： AndroidResource**。 您也可以提供高和低 DPI 映像的版本 (在適當命名**資源**子目錄，例如**drawable ldpi**， **drawable hdpi**，和**drawable xhdpi**)。
- **Windows Phone** -將映像放在與應用程式的根目錄**建置動作： 內容**。
- **通用 Windows 平台 (UWP)** -將映像放在與應用程式的根目錄**建置動作： 內容**。

> [!IMPORTANT]
> 在 iOS 9 之前, 映像通常被放入**資源**資料夾**建置動作： BundleResource**。 不過，這個方法的使用中 iOS 應用程式的映像已由 Apple 被取代。 如需詳細資訊，請參閱[映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵守進行檔案命名和放置這些規則可讓下列 XAML 載入並顯示在所有平台上的映像：

```xaml
<Image Source="waterfront.jpg" />
```

對等的 C# 程式碼如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

下列螢幕擷取畫面顯示結果的每個平台上顯示本機映像：

[![本機的 ImageSource](images-images/local-sml.png "範例應用程式顯示本機影像")](images-images/local.png#lightbox "範例應用程式顯示本機映像")

更有彈性`Device.RuntimePlatform`屬性可以用來選取不同的影像檔案或路徑的部分或所有平台，這個程式碼範例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 所有平台使用相同的映像檔名的名稱必須是在所有平台上有效。 Android drawables 已命名限制 – 僅小寫字母、 數字、 底線和句號允許 – 和跨平台相容性這必須遵循所有其他的平台上太。 範例名**waterfront.png**如下所示的規則，但是無效的檔名的範例包括 「 water front.png"，"WaterFront.png"，"上限-front.png"和"wåterfront.png"。

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>原生解析度 （Retina 和高 DPI）

iOS、 Android、 Windows Phone 和 UWP 包括支援針對不同的影像解析度，其中作業系統會選擇適當的映像，在執行階段根據裝置的功能。 Xamarin.Forms 使用原生的平台應用程式開發介面，用於載入本機映像，所以若正確命名並在專案檔案會自動支援替代的解決方式。

自 iOS 9 管理映像的較佳的方式是拖曳至適當的資產目錄映像集所需的每種解析度的影像。 如需詳細資訊，請參閱[加入影像資產目錄映像設定](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

IOS 9 之前, 的映像的視網膜版本可以架設在**資源**資料夾-二及第三次以解析**@2x**或**@3x**上的檔案名稱副檔名 （例如之前的尾碼 **myimage@2x.png**). 不過，這個方法的使用中 iOS 應用程式的映像已由 Apple 被取代。 如需詳細資訊，請參閱[映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 的替代解析度的影像應該放在[特別名為目錄](http://developer.android.com/guide/practices/screens_support.html)在 Android 專案中，如下列螢幕擷取畫面所示：

[![Android 的多個高解析度影像位置](images-images/xs-highdpisolution-sml.png "Android 多重解析度影像位置")](images-images/xs-highdpisolution.png#lightbox "Android 多重解析度影像位置")

UWP 和 Windows Phone 映像檔案名稱[可以後置字元為`.scale-xxx`副檔名前](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中`xxx`百分比的縮放比例資產，例如套用**myimage.scale 200.png**. 然後可以參考映像程式碼或 XAML 沒有小數位數修飾詞，例如只**myimage.png**。 平台將會選取最接近的適當的資產縮放比例根據目前的顯示器的 DPI。

### <a name="additional-controls-that-display-images"></a>顯示影像的其他控制項

有些控制項其屬性可顯示影像，例如：

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -任何頁面上的型別衍生自`Page`具有[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)和[ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/)屬性，可指派的本機檔案參考。 在某些情況下，例如當[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)顯示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，如果平台支援，將會顯示圖示。

  > [!IMPORTANT]
  > 在 iOS、 [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)無法從映像資產目錄影像集中填入屬性。 相反地，載入圖示的影像`Page.Icon`屬性從**資源**iOS 專案中的資料夾。

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -具有[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/)可以設定為本機檔案參考的屬性。
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -具有[ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/)屬性，可以將映像從本機檔案、 內嵌的資源或擷取 URI。

<a name="embedded_images" />

## <a name="embedded-images"></a>內嵌影像

內嵌的影像也隨附的應用程式 （例如本機影像） 但檔案內嵌為資源組件中而不需要在每個應用程式的檔案結構的映像中的映像複本。 映像隨附的程式碼時，特別適合用來建立元件，此方法將映像。

若要將影像內嵌在專案中，以滑鼠右鍵按一下要加入新項目，然後選取您想要加入映像/s。 根據預設映像已**建置動作： 無**; 這必須設為**建置動作： EmbeddedResource**。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**可以檢視和變更**屬性**檔案的視窗。

在此範例中的資源識別碼是**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**這個專案檔名，使用每個值之間的句號 （.）。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "設定建置動作： EmbeddedResource")

**建置動作**也可以檢視和變更**屬性**填補的檔案。
顯示這個板**資源識別碼**，用來參考程式碼中的資源。 在下列螢幕擷取畫面**資源識別碼**是**WorkingWithImages.beach.jpg**。
IDE 已產生此預設值，藉由串連**預設命名空間**這個專案檔名，使用每個值之間的句號 （.）。
此識別碼可以在中編輯**屬性**板，但這些範例值**WorkingWithImages.beach.jpg**將使用。

![](images-images/xs-embeddedproperties.png "EmbeddedResource 屬性填補")

-----

如果您在專案中將內嵌的影像放入資料夾，資料夾名稱是也分隔句號 （.） 中的資源 id。 移動**beach.jpg**映像到資料夾，稱為**MyImages**資源識別碼將會產生**WorkingWithImages.MyImages.beach.jpg**

載入內嵌的影像的程式碼直接傳**資源識別碼**至[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)方法如下所示：

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg") };
```

目前沒有資源識別元隱含轉換。 相反地，您必須使用[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)或`new ResourceImageSource()`載入內嵌的影像。

下列螢幕擷取畫面會顯示每個平台上顯示內嵌的影像的結果：

[![ResourceImageSource](images-images/resource-sml.png "範例應用程式顯示內嵌的影像")](images-images/resource.png#lightbox "範例顯示內嵌的影像的應用程式")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>使用 XAML

因為沒有任何內建型別轉換器`string`至`ResourceImageSource`，這些類型的映像無法原生載入 xaml。 相反地，寫入簡單的自訂 XAML 標記延伸使用的映像載入**資源識別碼**XAML 中指定：

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
   var imageSource = ImageSource.FromResource(Source);

   return imageSource;
 }
}
```

若要使用這項擴充功能加入自訂`xmlns`成 XAML，請使用正確的命名空間和組件值專案。 影像來源可以設定使用此語法： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 範例如下所示：

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

### <a name="troubleshooting-embedded-images"></a>疑難排解 內嵌的影像

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>偵錯程式碼

因為有時候很難了解為什麼未載入特定的影像資源，所以下列偵錯程式碼可以暫時加入應用程式以協助確認已正確設定的資源。 它會輸出指定的組件中內嵌的所有已知的資源<span class="UIItem">主控台</span>協助偵錯載入問題的資源。

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

#### <a name="images-embedded-in-other-projects-dont-appear"></a>不會出現在其他專案中內嵌影像

`Image.FromResource` 只會尋找程式碼呼叫相同的組件中的映像`FromResource`。 使用偵錯程式碼，您可以判斷哪些組件包含特定的資源，藉由變更`typeof()`陳述式來`Type`已知為每個組件。

<a name="Downloading_Images" />

## <a name="downloading-images"></a>下載映像

下列 XAML 中所示，映像，可以自動下載適用於顯示：

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

下列範例也能運作，因此，也是產生 URI 字串的隱含轉換：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

下列螢幕擷取畫面顯示結果的每個平台上顯示的遠端影像：

[![下載 ImageSource](images-images/download-sml.png "範例應用程式顯示下載的映像")](images-images/download.png#lightbox "範例應用程式顯示下載的映像")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>下載的映像快取

A [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)也支援 快取的下載映像，透過下列屬性：

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -是否啟用快取 (`true`依預設)。
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A`TimeSpan`多久映像會儲存在本機定義。

快取預設為啟用，並將儲存在本機的 24 小時的映像。 若要停用特定的映像快取，具現化映像來源，如下所示：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

若要設定特定的快取期間 （例如，5 天） 具現化映像來源，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

內建快取，可以很容易支援狀況，像是每個資料格中捲動的映像，您可以設定 （或繫結） 映像的清單，並讓內建的快取處理的資料格回捲動檢視時，重新載入影像。

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>圖示和啟動顯示畫面

雖然無關[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)檢視、 應用程式圖示和啟動顯示畫面也是一個重要用途 Xamarin.Forms 專案中的映像。

設定圖示和啟動顯示畫面 Xamarin.Forms 應用程式會在每個應用程式的專案中完成。 這表示正在產生正確大小的 iOS、 Android 和 UWP 映像。 這些映像應該進行命名，並位於根據每個平台需求。

## <a name="icons"></a>圖示

請參閱[iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)， [Google 遙控器](http://developer.android.com/design/style/iconography.html)，和[並排顯示和圖示資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)如需建立這些應用程式資源的詳細資訊。

## <a name="splashscreens"></a>啟動顯示畫面

僅限 iOS 和 UWP 應用程式需要啟動顯示畫面 （也稱為啟動螢幕或預設映像）。

請參閱文件[iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)和[啟動顯示畫面](/windows/uwp/launch-resume/splash-screens/)Windows 開發人員中心。

## <a name="summary"></a>總結

Xamarin.Forms 提供數種不同的跨平台應用程式，允許跨平台使用相同的映像，或指定的平台專屬映像中包含影像。 下載的影像也會自動快取，自動化程式碼撰寫的常見案例。

應用程式圖示和啟動顯示畫面影像會安裝並設定與非 Xamarin.Forms 應用程式-請遵循相同的指引，用於特定平台應用程式。


## <a name="related-links"></a>相關連結

- [WorkingWithImages （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS 處理映像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 的遙控器](http://developer.android.com/design/style/iconography.html)
- [並排顯示和圖示資產的指導方針](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
