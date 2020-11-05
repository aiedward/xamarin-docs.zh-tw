---
title: 第28章的摘要。 位置與地圖
description: 建立 Mobile Apps，包含 Xamarin.Forms ：第28章的摘要。 位置與地圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 25b5ad1ef7b1d5d3c545d7977ad735c18affef8e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375105"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第28章的摘要。 位置與地圖

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

Xamarin.Forms 支援 [`Map`](xref:Xamarin.Forms.Maps.Map) 衍生自的元素 `View` 。 由於使用對應的特殊平臺需求，這些需求會在個別的元件中執行 **Xamarin.Forms 。對應** ，並牽涉到不同的命名空間： `Xamarin.Forms.Maps` 。

## <a name="the-geographic-coordinate-system"></a>地理座標系統

地理座標系統會識別球形 (上的位置，或幾乎球形) 物件，例如地球。 座標包含以角度表示的 *緯度* 和 *經度* 。

所謂的很棒圓形 `equator` 是在兩個兩極之間，地球的軸在概念上的擴充。

### <a name="parallels-and-latitude"></a>平行和緯度

從地球中央算起，從地球的上到上赤道的角度，會標示相等緯度的線條，稱為「 *平行* 」。 這些範圍的範圍是從赤道到北和南兩極的90度。 依照慣例，緯度北部的赤道是正值，而這些赤道的南值為負值。

### <a name="longitude-and-meridians"></a>經度和經線

從北極點到南極點的很大圓形是相等經度的行，也稱為 *經線* 。 這些是相對於英國格林威治的主要經線。 依照慣例，質數經線的經度東部是從0度到180度的正值，而質數經線的經度西部是從0度到180度的負數值 &ndash; 。

### <a name="the-equirectangular-projection"></a>Equirectangular 投射

地球的任何平面地圖都會引進扭曲。 如果緯度和經度的所有線條都是直線，而且緯度和經度角度的相等差異與地圖上的相等距離相對應，則結果為 *equirectangular 投射* 。 此對應會扭曲接近兩極的區域，因為它們會水準延展。

### <a name="the-mercator-projection"></a>Mercator 投射

受歡迎的 *Mercator 投射* 也會藉由垂直延展這些區域，嘗試補償水準延展。 這會產生一個地圖，其中兩極附近的區域看起來遠超過真正的範圍，但是任何區域都符合實際的區域。

### <a name="map-services-and-tiles"></a>地圖服務與磚

地圖服務使用稱為的 Mercator 投影變化 `Web Mercator` 。 地圖服務會根據位置和縮放層級，將點陣圖磚傳遞給用戶端。

## <a name="getting-the-users-location"></a>取得使用者的位置

這些 Xamarin.Forms `Map` 類別不包含取得使用者地理位置的功能，但這在使用地圖時通常是很好的做法，因此相依性服務必須處理它。

> [!NOTE]
> Xamarin.Forms 應用程式可以改為使用 [`Geolocation`](~/essentials/geolocation.md) 中包含的類別 Xamarin.Essentials 。

### <a name="the-location-tracker-api"></a>位置追蹤器 API

「 [**Xamarin.Forms 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)方案」包含位置追蹤器 API 的程式碼。 [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)結構會封裝緯度和經度。 [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)介面會定義兩種方法來啟動和暫停位置追蹤器，以及有新位置可用時的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理員

的 iOS 實 `ILocationTracker` 作為 [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) 利用 iOS 的類別 [`CLLocationManager`](xref:CoreLocation.CLLocationManager) 。

#### <a name="the-android-location-manager"></a>Android 位置管理員

的 Android 實作為利用 `ILocationTracker` [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) Android 類別的類別 [`LocationManager`](xref:Android.Locations.LocationManager) 。

#### <a name="the-uwp-geo-locator"></a>UWP 地區定位器

的通用 Windows 平臺實作為 `ILocationTracker` [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) 利用 UWP 的類別 [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator) 。

### <a name="display-the-phones-location"></a>顯示電話的位置

[**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)範例會使用位置追蹤器，以文字和 equirectangular 地圖顯示電話的位置。

### <a name="the-required-overhead"></a>需要的額外負荷

**WhereAmI** 使用位置追蹤程式需要一些額外負荷。 首先， **WhereAmI** 方案中的所有專案都必須參考 **Xamarin.Forms Book** 中對應的專案，而且每個 **WhereAmI** 專案都必須呼叫 `Toolkit.Init` 方法。

需要一些額外的平臺特定額外負荷（以位置許可權的形式）。

#### <a name="location-permission-for-ios"></a>IOS 的 Location 許可權

若是 iOS， **plist** 檔案必須包含包含問題文字的專案，要求使用者允許取得該使用者的位置。

#### <a name="location-permissions-for-android"></a>Android 的位置許可權

取得使用者位置的 Android 應用程式在 AndroidManifest.xml 檔案中必須有 ACCESS_FILE_LOCATION 許可權。

#### <a name="location-permissions-for-the-uwp"></a>UWP 的位置許可權

通用 Windows 平臺的應用程式必須 `location` 在 package.appxmanifest 檔案中標示裝置功能。

## <a name="working-with-no-locxamarinformsmaps"></a>使用 Xamarin.Forms 。地圖

使用類別牽涉到許多需求 `Map` 。

### <a name="the-nuget-package"></a>NuGet 套件

**Xamarin.Forms 。** 必須將 Maps NuGet 程式庫新增至應用程式解決方案。 版本號碼應該與 **Xamarin.Forms** 目前安裝的封裝相同。

### <a name="initializing-the-maps-package"></a>正在初始化 Maps 套件

應用程式專案必須在 `Xamarin.FormsMaps.Init` 呼叫之後呼叫方法 `Xamarin.Forms.Forms.Init` 。

### <a name="enabling-map-services"></a>啟用地圖服務

由於 `Map` 可以取得使用者的位置，因此應用程式必須以本章節稍早所述的方式取得使用者的許可權：

#### <a name="enabling-ios-maps"></a>啟用 iOS 地圖

在 plist 檔案中使用的 iOS 應用程式 `Map` 需要兩行。

#### <a name="enabling-android-maps"></a>啟用 Android 地圖

需要授權金鑰才能使用 Google 地圖服務。 這個金鑰會插入 **AndroidManifest.xml** 檔案中。 此外， **AndroidManifest.xml** 檔案需要 `manifest` 取得使用者位置的相關標記。

#### <a name="enabling-uwp-maps"></a>啟用 UWP 地圖

通用 Windows 平臺的應用程式需要使用 Bing 地圖服務的授權金鑰。 此索引鍵會作為引數傳遞給 `Xamarin.FormsMaps.Init` 方法。 您也必須針對位置服務啟用應用程式。

### <a name="the-unadorned-map"></a>未對應

[**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)範例是由 [MapsDemoHomePage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)檔案和 [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)程式碼後端檔案所組成，可讓您流覽至各種示範程式。

[BasicMapPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)檔案顯示如何顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) 視圖。 依預設，它會顯示羅馬的城市，但使用者可以操作地圖。

若要停用水準和垂直捲動，請將 [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) 屬性設定為 `false` 。 若要停用縮放，請將設定 [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) 為 `false` 。 這些屬性可能無法在所有平臺上運作。

### <a name="streets-and-terrain"></a>街道和地形

您可以藉由設定 `Map` 類型的屬性 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) [`MapType`](xref:Xamarin.Forms.Maps.MapType) ，以及具有三個成員的列舉，來顯示不同類型的對應：

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street)（預設值）
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)檔案顯示如何使用選項按鈕來選取地圖類型。 它會使用 [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) [**Xamarin.Forms Book. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別，以及以 [MapTypeRadioButton .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)檔案為基礎的類別。

### <a name="map-coordinates"></a>地圖座標

程式可透過屬性取得目前顯示的區域 `Map` [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) 。 這個屬性 *不* 是由可系結屬性所支援，而且沒有通知機制可指出其變更時間，所以想要監視屬性的程式應該會針對該目的使用計時器。

`VisibleRegion` 的類型是 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) ，具有四個唯讀屬性的類別：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) 型別為 [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) 型別的 `double` ，表示地圖上顯示區域的高度。
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) 型別的 `double` ，表示地圖上顯示區域的寬度
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) 型別的 [`Distance`](xref:Xamarin.Forms.Maps.Distance) ，表示地圖上可見的最大圓形區域大小

`Position` 和 `Distance` 都是結構。 `Position`定義透過此函式設定的兩個[ `Position` ](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` 的目的是在計量和英文單位之間轉換，以提供單位無關的距離。 您 `Distance` 可以透過數種方式建立一個值：

- [ `Distance` 以量](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))為單位之距離的函式
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) 靜態方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) 靜態方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) 靜態方法

此值可從三個屬性取得：

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) 型別為 `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) 型別為 `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) 型別為 `double`

[MapCoordinatesPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)包含數個 `Label` 用於顯示資訊的元素 `MapSpan` 。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)程式碼後端檔案會使用計時器，在使用者操作對應時保持資訊的更新。

### <a name="position-extensions"></a>位置延伸

此書籍的新程式庫命名為 [**Xamarin.Forms Book。 Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含對應特定但與平臺無關的類型。 [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別具有的 `ToString` 方法 `Position` ，以及用來計算兩個值之間距離的方法 `Position` 。

### <a name="setting-an-initial-location"></a>設定初始位置

您可以呼叫 [ `MoveToRegion` ] (x： Xamarin.Forms 。MoveToRegion (Xamarin.Forms 。MapSpan) # A3 方法 `Map` ，以程式設計方式在地圖上設定位置和縮放層級。 引數的類型為 `MapSpan` 。 您可以 `MapSpan` 使用下列其中一種方式建立物件：

- [函式 `MapSpan` ] (x： Xamarin.Forms 。MapSpan .% 23ctor (Xamarin.Forms 。Map. Position、system.string、system.string) # A3 搭配 a `Position` 、緯度和經度 span
- [ `MapSpan.FromCenterAndRadius` ] (x： Xamarin.Forms 。MapSpan. FromCenterAndRadius (Xamarin.Forms 。Map. Position、 Xamarin.Forms .。地圖。距離) # A3 與 `Position` and 半徑

您也可以 `MapSpan` 使用方法或，從現有的建立新的 [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)) 。

[WyomingPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)檔案和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)程式碼後端檔案會示範如何使用 `MoveToRegion` 方法來顯示懷俄明州的狀態。

您也可以使用 [函式 `Map` ] (x： Xamarin.Forms 。23ctor (的對應 .% Xamarin.Forms 。MapSpan) # A3 與 `MapSpan` 物件，以初始化地圖的位置。 [XamarinHQPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)檔案示範如何在 xaml 中完全完成這項作業，以在三藩市顯示 Xamarin 的總部。

### <a name="dynamic-zooming"></a>動態縮放

您可以使用 `Slider` 來動態縮放地圖。 [RadiusZoomPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)檔案和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)程式碼後端檔案會示範如何根據值變更地圖的半徑 `Slider` 。

[LongitudeZoomPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)檔案和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)程式碼後端檔案會顯示更適合 Android 的替代方法，但是這兩種方法在 Windows 平臺上都沒作用。

### <a name="the-phones-location"></a>電話的位置

的 [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) 屬性在 `Map` 每個平臺上的運作方式稍有不同，因為 [ShowLocationPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) 檔案會示範：

- 在 iOS 上，藍色點表示電話的位置，但您必須在該處手動流覽
- 在 Android 上，當推送將地圖移至電話位置時，會顯示圖示
- UWP 類似于 iOS，但有時會自動流覽至位置

**MapDemos** 專案會嘗試模擬 Android 方法，方法是先定義以 [MyLocationButton .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)檔案為基礎的圖示型按鈕，並 [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)程式碼後端檔案。

[GoToLocationPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)檔案和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)程式碼後置檔案會使用這個按鈕來流覽至電話的位置。

### <a name="pins-and-science-museums"></a>釘選和科學博物館

最後， `Map` 類別會定義 [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) 型別的屬性 `IList<Pin>` 。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義四個屬性：

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) 型別為 `string` 必要屬性的
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) 的型別，也就 `string` 是一種選擇性的人類可閱讀位址
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) 型別的 `Position` ，指出釘選在地圖上顯示的位置
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) 型別為的 [`PinType`](xref:Xamarin.Forms.Maps.PinType) ，未使用的列舉

**MapDemos** 專案包含 [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)的檔案，其中列出美國的科學博物館，以及用來還原 [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) 序列化此資料的類別。

[ScienceMuseumsPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)檔案和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)程式碼後端檔案會在地圖中顯示這些科學博物館的釘選。 當使用者按下 pin 碼時，會顯示該博物館的位址和網站。

### <a name="the-distance-between-two-points"></a>兩點之間的距離

[`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別包含一個 [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) 方法，此方法具有兩個地理位置之間距離的簡化計算。

這會用在 [LocalMuseumsPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) 檔案中，並 [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) 程式碼後置檔案，也會顯示從使用者的位置到博物館的距離：

[![本地博物館頁面的三重螢幕擷取畫面](images/ch28fg28-small.png "距離至位置")](images/ch28fg28-large.png#lightbox "距離至位置")

此程式也會示範如何根據地圖的位置動態限制 pin 數目。

## <a name="geocoding-and-back-again"></a>地理編碼和返回

[**Xamarin.Forms 。Maps**](xref:Xamarin.Forms.Maps)元件也包含類別，其 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) 方法會將文字位址轉換成零個或更多可能的地理位置，而另一個方法 [ `GetAddressesForPositionAsync` ] (x： Xamarin.Forms 。地理編碼器. GetAddressesForPositionAsync (Xamarin.Forms 。Map. Position) # A3，以另一個方向進行轉換。

[GeocoderRoundTrip .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)檔案和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)程式碼後端檔案會示範這種功能。

## <a name="related-links"></a>相關連結

- [第28章的全文文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第28章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.Forms 地圖](~/xamarin-forms/user-interface/map/index.md)
