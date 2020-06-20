---
title: 第28章的摘要。 位置與地圖
description: 建立 Mobile Apps Xamarin.Forms ：第28章的摘要。 位置與地圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 301dc65c7909603e117717a993959e3c73fa2d32
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133402"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第28章的摘要。 位置與地圖

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

Xamarin.Forms支援 [`Map`](xref:Xamarin.Forms.Maps.Map) 衍生自的元素 `View` 。 由於使用對應的特殊平臺需求，它們會在不同的元件中執行** Xamarin.Forms 。對應**，且牽涉到不同的命名空間： `Xamarin.Forms.Maps` 。

## <a name="the-geographic-coordinate-system"></a>地理座標系統

地理座標系統會識別球形（或接近球形）物件上的位置，例如地球。 座標包含以角度表示的*緯度*和*經度*。

一個很棒的圓，叫做， `equator` 地球軸在概念上擴充的兩個兩極中間。

### <a name="parallels-and-latitude"></a>平行線與緯度

從地球中心的赤道向北或南部測量的角度，也就是稱為「*平行*」的相等緯度線。 其範圍介於赤道的0度至北和南兩極的90度。 依照慣例，緯度北部的赤道是正值，而這些赤道的南部是負值。

### <a name="longitude-and-meridians"></a>經度和經線

從北極點到南極點的絕佳圓，是一條相等的經度，也稱為*經線*。 這些是相對於英國格林威治的主要經線。 依照慣例，質數經線的經度東部是從0角度到180度的正值，而質數經線的經度西部是從0角度到180度的負數值 &ndash; 。

### <a name="the-equirectangular-projection"></a>Equirectangular: 投射

地球的任何平面地圖都會引進扭曲。 如果緯度和經度的所有行都是直線，而且緯度和經度角度的相等差異對應于地圖上的相等距離，則結果會是*equirectangular: 投射*。 此地圖會扭曲較接近兩極的區域，因為它們會水準伸展。

### <a name="the-mercator-projection"></a>Mercator 投射

受歡迎的*Mercator 投射*也會嘗試以垂直方式延展這些區域，以補償水準延展。 這會導致對應，其中靠近兩極的區域出現遠大於實際的位置，但任何區域都與實際的區域完全一致。

### <a name="map-services-and-tiles"></a>地圖服務和磚

地圖服務會使用一種稱為的 Mercator 投影變化 `Web Mercator` 。 地圖服務會根據位置和縮放層級，將點陣圖磚傳遞給用戶端。

## <a name="getting-the-users-location"></a>取得使用者的位置

這些 Xamarin.Forms `Map` 類別不包含可取得使用者地理位置的功能，但這通常是使用對應時的理想方式，因此相依性服務必須處理它。

> [!NOTE]
> Xamarin.Forms應用程式可以改為使用 [`Geolocation`](~/essentials/geolocation.md) 包含在中的類別 Xamarin.Essentials 。

### <a name="the-location-tracker-api"></a>位置追蹤程式 API

[**FormsBook 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案包含位置追蹤器 API 的程式碼。 [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)結構會封裝緯度和經度。 [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)介面會定義兩種啟動和暫停位置追蹤器的方法，以及可用的新位置的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理員

的 iOS 實作為 `ILocationTracker` [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) 類別，它會使用 ios [`CLLocationManager`](xref:CoreLocation.CLLocationManager) 。

#### <a name="the-android-location-manager"></a>Android 位置管理員

的 Android 實作為 `ILocationTracker` [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) 類別，它會使用 Android [`LocationManager`](xref:Android.Locations.LocationManager) 類別。

#### <a name="the-uwp-geo-locator"></a>UWP 地理定位程式

的通用 Windows 平臺執行 `ILocationTracker` 是 [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) 使用 UWP 的類別 [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator) 。

### <a name="display-the-phones-location"></a>顯示電話的位置

[**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)範例會使用位置追蹤器，在文字和 equirectangular: 地圖上顯示電話的位置。

### <a name="the-required-overhead"></a>所需的額外負荷

**WhereAmI**需要一些額外負荷，才能使用位置追蹤器。 首先， **WhereAmI**方案中的所有專案都必須參考**FormsBook**中的對應專案，而且每個**WhereAmI**專案都必須呼叫 `Toolkit.Init` 方法。

需要一些額外的平臺特定額外負荷（以位置許可權的形式）。

#### <a name="location-permission-for-ios"></a>IOS 的位置許可權

若是 iOS， **plist**檔案必須包含包含問題文字的專案，詢問使用者是否允許取得該使用者的位置。

#### <a name="location-permissions-for-android"></a>Android 的位置許可權

取得使用者位置的 Android 應用程式必須具有 AndroidManifest.xml 檔案中的 ACCESS_FILE_LOCATION 許可權。

#### <a name="location-permissions-for-the-uwp"></a>UWP 的位置許可權

通用 Windows 平臺應用程式必須 `location` 在 package.appxmanifest.xml 檔案中有標記的裝置功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms 。繪

使用類別牽涉到幾項需求 `Map` 。

### <a name="the-nuget-package"></a>NuGet 套件

** Xamarin.Forms 。對應**NuGet 程式庫必須新增至應用程式方案。 版本號碼應該與 **Xamarin.Forms** 目前安裝的套件相同。

### <a name="initializing-the-maps-package"></a>初始化對應套件

呼叫之後，應用程式專案必須呼叫 `Xamarin.FormsMaps.Init` 方法 `Xamarin.Forms.Forms.Init` 。

### <a name="enabling-map-services"></a>啟用地圖服務

因為 `Map` 可以取得使用者的位置，所以應用程式必須以本章稍早所述的方式取得使用者的許可權：

#### <a name="enabling-ios-maps"></a>啟用 iOS 地圖

使用的 iOS 應用程式 `Map` 在 plist 檔案中需要兩行資訊。

#### <a name="enabling-android-maps"></a>啟用 Android 地圖

使用 Google Map 服務需要授權金鑰。 這個金鑰會插入**AndroidManifest.xml**檔案中。 此外， **AndroidManifest.xml**檔案需要 `manifest` 取得使用者位置所需的標記。

#### <a name="enabling-uwp-maps"></a>啟用 UWP 地圖

通用 Windows 平臺應用程式需要使用 Bing 地圖服務的授權金鑰。 這個金鑰會當做引數傳遞給 `Xamarin.FormsMaps.Init` 方法。 應用程式也必須針對位置服務啟用。

### <a name="the-unadorned-map"></a>未對應

[**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)範例是由[MapsDemoHomePage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)檔案和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)程式碼後置檔案所組成，可讓您流覽至各種示範程式。

[BasicMapPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)會顯示如何顯示此 [`Map`](xref:Xamarin.Forms.Maps.Map) 視圖。 根據預設，它會顯示羅馬的城市，但是該地圖可以由使用者操作。

若要停用水準和垂直捲動，請將 [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) 屬性設定為 `false` 。 若要停用縮放，請將設定 [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) 為 `false` 。 這些屬性可能無法在所有平臺上使用。

### <a name="streets-and-terrain"></a>街道和地形

您可以藉由設定 `Map` 類型的屬性 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) [`MapType`](xref:Xamarin.Forms.Maps.MapType) ，以及具有三個成員的列舉，來顯示不同類型的對應：

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street)，預設值
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)會顯示如何使用選項按鈕來選取地圖類型。 它會使用 [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別，以及以[MapTypeRadioButton](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)為基礎的類別。

### <a name="map-coordinates"></a>地圖座標

程式可以透過屬性取得目前顯示的區域 `Map` [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) 。 這個屬性*不*受可系結屬性的支援，而且沒有任何通知機制來指出其何時變更，因此想要監視屬性的程式可能會針對該目的使用計時器。

`VisibleRegion`的類型是 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) ，具有四個唯讀屬性的類別：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)類型為[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)類型的 `double` ，表示地圖的顯示區域高度
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)類型的 `double` ，表示地圖的顯示區域寬度
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)類型的 [`Distance`](xref:Xamarin.Forms.Maps.Distance) ，指出地圖上可見的最大迴圈區域大小

`Position`和 `Distance` 都是結構。 `Position`定義透過此[ `Position` 函數](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))設定的兩個唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`的目的是要在度量與英文單位之間轉換，以提供單位獨立距離。 有 `Distance` 幾種方式可以建立值：

- [ `Distance` 具有距離](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))的函式，以量為單位
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))靜態方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))靜態方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))靜態方法

此值可從三個屬性取得：

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)類型為`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)類型為`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)類型為`double`

[MapCoordinatesPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)包含數個用 `Label` 來顯示資訊的元素 `MapSpan` 。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)程式碼後置檔案會使用計時器，在使用者操作對應時，讓資訊保持更新。

### <a name="position-extensions"></a>位置延伸模組

這本書的新程式庫，名為[**FormsBook。 Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含對應特有但與平臺無關的類型。 [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別具有的 `ToString` 方法 `Position` ，以及用來計算兩個值之間距離的方法 `Position` 。

### <a name="setting-an-initial-location"></a>設定初始位置

您可以呼叫 [ `MoveToRegion` ] （x： Xamarin.Forms 。對應. MoveToRegion （ Xamarin.Forms 。MapSpan））的方法，可透過程式設計 `Map` 方式設定地圖上的位置和縮放層級。 引數的類型為 `MapSpan` 。 您可以使用下列其中一種方式來建立 `MapSpan` 物件：

- [ `MapSpan` 構造函式] （x： Xamarin.Forms 。MapSpan .% 23ctor （ Xamarin.Forms 。Map. Position、system.string、system.string）和 `Position` 、緯度和經度範圍
- [ `MapSpan.FromCenterAndRadius` ] （x： Xamarin.Forms 。對應. MapSpan. FromCenterAndRadius （ Xamarin.Forms 。對應。位置， Xamarin.Forms 。對應. 距離），具有 `Position` 和半徑

您也可以 `MapSpan` 使用方法或，從現有的建立新的 [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)) 。

[WyomingPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)檔案和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)程式碼後置檔案會示範如何使用 `MoveToRegion` 方法來顯示懷俄明州的狀態。

您也可以使用 [構造函式 `Map` ] （x： Xamarin.Forms 。對應. Map .% 23ctor （ Xamarin.Forms 。MapSpan）），具有 `MapSpan` 物件可初始化地圖的位置。 [XamarinHQPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)會示範如何完全在 xaml 中執行這項操作，以在三藩市顯示 Xamarin 的總部。

### <a name="dynamic-zooming"></a>動態縮放

您可以使用 `Slider` 來動態縮放地圖。 [RadiusZoomPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)檔案和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)程式碼後置檔案會顯示如何根據值來變更對應的半徑 `Slider` 。

[LongitudeZoomPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)檔案和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)程式碼後置檔案會顯示在 Android 上效果更佳的替代方法，但這兩種方法都不適用於 Windows 平臺。

### <a name="the-phones-location"></a>電話的位置

的 [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) 屬性 `Map` 在每個平臺上的運作方式稍有不同，因為[ShowLocationPage xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)檔案示範：

- 在 iOS 上，藍色點表示電話的位置，但您必須在該處手動流覽
- 在 Android 上，當推播將地圖移至電話的位置時，會顯示圖示
- UWP 類似于 iOS，但有時會自動導覽至位置

**MapDemos**專案會嘗試模擬 Android 方法，方法是先根據[MyLocationButton](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)檔案和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)程式碼後置檔案來定義以圖示為基礎的按鈕。

[GoToLocationPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)檔案和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)程式碼後置檔案使用此按鈕流覽至電話的位置。

### <a name="pins-and-science-museums"></a>釘選與科學博物館

最後， `Map` 類別會定義 [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) 類型的屬性 `IList<Pin>` 。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義四個屬性：

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)屬於類型的 `string` 必要屬性
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)屬於類型的 `string` 選擇性人可讀取的位址
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)類型的 `Position` ，表示釘選顯示在地圖上的位置
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)類型為的 [`PinType`](xref:Xamarin.Forms.Maps.PinType) 列舉，未使用

**MapDemos**專案包含[ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)的檔案，其中列出美國的科學博物館，以及用來還原 [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) 序列化此資料的和類別。

[ScienceMuseumsPage .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)檔案和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)程式碼後置檔案會在地圖中顯示這些科學博物館的圖釘。 當使用者按下 pin 時，會顯示該博物館的位址和網站。

### <a name="the-distance-between-two-points"></a>兩個點之間的距離

[`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別包含一個 [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) 方法，其簡化了兩個地理位置之間距離的計算。

這會在[LocalMuseumsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)檔案中使用，並[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)程式碼後置檔案，也會顯示從使用者位置到博物館的距離：

[![本機博物館頁面的三向螢幕擷取畫面](images/ch28fg28-small.png "與位置之間的距離")](images/ch28fg28-large.png#lightbox "與位置之間的距離")

此程式也會示範如何根據地圖的位置動態限制釘選數目。

## <a name="geocoding-and-back-again"></a>地理編碼後返回

[** Xamarin.Forms 。Maps**](xref:Xamarin.Forms.Maps)元件也包含一個 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 類別，其 [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) 方法會將文字位址轉換成零個或多個可能的地理位置，以及另一個方法 [ `GetAddressesForPositionAsync` ] （x： Xamarin.Forms 。對應. Geocoder. GetAddressesForPositionAsync （ Xamarin.Forms 。對應. 位置），會以另一個方向轉換。

[GeocoderRoundTrip .xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)檔案和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)程式碼後置檔案會示範這種功能。

## <a name="related-links"></a>相關連結

- [第28章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第28章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.Forms地圖](~/xamarin-forms/user-interface/map/index.md)
