---
title: 章 28 的摘要。 位置和對應
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 章 28 的摘要。 位置和對應
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c98e0e6611f70d3aea9309472d2287af401ba4ea
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241997"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>章 28 的摘要。 位置和對應

Xamarin.Forms 支援[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)項目，衍生自`View`。 在使用對應的特殊的平台需求，因為它們實作個別的組件， **Xamarin.Forms.Maps**，而且包含不同的命名空間： `Xamarin.Forms.Maps`。

## <a name="the-geographic-coordinate-system"></a>地理座標系統

地理座標系統識別球面 （或幾乎球面） 的物件，例如地球上的位置。 座標組成兩者*緯度*和*經度*表示的角度。

大圓形呼叫`equator`兩極透過此地球軸在概念上擴充之間，參數。

### <a name="parallels-and-latitude"></a>Parallels 和緯度

角度測量往北或由從地球標記的中心赤道行相等的緯度稱為*parallels*。 這些範圍從 0 度位於赤道到在北部和南兩極 90 度。 依照慣例，北方赤道經緯度正值，而且這些由赤道是負數值。

### <a name="longitude-and-meridians"></a>經度和經線

分成兩部分的優越南極延伸從北極圓形呈現也是相等的緯度行*經線*。 這些是相對於格林威治標準，英國子午線。 依照慣例，您該以東子午線正值是從 0 度 180 度、，而您該以西子午線從 0 度負數值&ndash;180 度。

### <a name="the-equirectangular-projection"></a>等量矩形投影

地球的任何一般對應導入了失真。 如果的緯度與經度的所有行都都直，而且如果相等的緯度與經度的角度差異對應到在地圖上距離相等，則結果是*等量矩形投影*。 此對應會扭曲接近兩極的區域，因為它們會水平伸展。

### <a name="the-mercator-projection"></a>Mercator 投射

熱門*Mercator 投射*為了彌補水平延展也垂直縮放這些區域。 這會導致附近兩極區域會遠比它們實際上是的但任何區域非常符合與實際的區域顯示的對應。

### <a name="map-services-and-tiles"></a>服務對應和磚

地圖服務會使用一種稱為 Mercator 投射`Web Mercator`。 地圖服務會將點陣圖磚傳遞到位置中與縮放層級為基礎的用戶端。

## <a name="getting-the-users-location"></a>取得使用者的位置

Xamarin.Forms`Map`類別不包含的程式，以取得使用者的地理位置，但這常常是有需要時使用對應，因此相依服務必須處理它。

### <a name="the-location-tracker-api"></a>位置追蹤程式應用程式開發介面

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)方案包含位置追蹤器應用程式開發介面的程式碼。 [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)結構封裝緯度與經度。 [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)介面會定義兩種方法可以啟動與暫停位置追蹤程式，並使用新的位置時的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理員

IOS 實作`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)類別，可使用的 ios [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/)。

#### <a name="the-android-location-manager"></a>Android 位置管理員

Android 的實作`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)類別，可使用的 Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)類別。

#### <a name="the-windows-runtime-geo-locator"></a>Windows 執行階段地理定位器

Windows 執行階段實作`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)類別，會使用 UWP [ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534)。

### <a name="display-the-phones-location"></a>顯示手機上的位置

[ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)範例會使用位置追蹤程式中的文字和 equirectangular 地圖上顯示手機上的位置。

### <a name="the-required-overhead"></a>所需的額外負荷

一些額外負荷是為了**WhereAmI**使用位置追蹤程式。 第一個專案中的所有**WhereAmI**解決方案必須有對應的專案中參考**Xamarin.FormsBook.Platform**，而且每個**WhereAmI**專案必須呼叫`Toolkit.Init`方法。

需要一些額外的平台專屬負荷，位置的權限形式。

#### <a name="location-permission-for-ios"></a>IOS 的 [位置] 使用權限

對於 iOS， **info.plist**檔案必須包含項目包含文字的問題，詢問使用者允許取得該使用者的位置。

#### <a name="location-permissions-for-android"></a>適用於 Android 的位置權限

Android 應用程式來取得使用者的位置必須 ACCESS_FILE_LOCATION 權限的 AndroidManifest.xml 檔案。

#### <a name="location-permissions-for-the-windows-runtime"></a>Windows 執行階段的 「 位置 」 權限

Windows 或 Windows Phone 的應用程式必須具有`location`標示 Package.appxmanifest 檔案中的裝置功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms.Maps

過程中使用的幾項需求所涉及`Map`類別。

### <a name="the-nuget-package"></a>NuGet 封裝

**Xamarin.Forms.Maps** NuGet 文件庫必須新增至應用程式方案。 版本號碼應與相同**Xamarin.Forms**目前已安裝的封裝。

### <a name="initializing-the-maps-package"></a>初始化對應的封裝

應用程式專案必須呼叫`Xamarin.FormsMaps.Init`方法之後呼叫`Xamarin.Forms.Forms.Init`。

### <a name="enabling-map-services"></a>啟用對應服務

因為`Map`可以取得使用者的位置，應用程式必須取得本章節稍早所述的方式在使用者的權限：

#### <a name="enabling-ios-maps"></a>啟用 iOS 對應

IOS 應用程式使用`Map`需要兩行 info.plist 檔案中的。

#### <a name="enabling-android-maps"></a>啟用 Android 對應

需要使用 Google 地圖服務的授權金鑰。 此機碼中插入**AndroidManifest.xml**檔案。 此外， **AndroidManifest.xml**檔案需要`manifest`標記參與取得使用者的位置。

#### <a name="enabling-windows-runtime-maps"></a>啟用 Windows 執行階段對應

Windows 執行階段應用程式會使用 Bing 地圖服務需要授權金鑰。 此機碼傳遞的引數為`Xamarin.FormsMaps.Init`方法。 位置服務也必須啟用應用程式。

### <a name="the-unadorned-map"></a>未修飾的對應

[ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)範例包含[MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)檔案和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)程式碼後置檔案，可讓瀏覽至不同的示範程式。

[BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)檔案示範如何顯示[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)檢視。 依預設它會顯示羅馬的城市，但使用者可以管理對應。

若要停用水平和垂直捲動，請設定[ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/)屬性`false`。 若要停用縮放，請設定[ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/)至`false`。 這些屬性可能會無法在所有平台上。

### <a name="streets-and-terrain"></a>或街道及地形

您可以藉由設定顯示不同類型的地圖`Map`屬性[ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/)型別的[ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/)，具有三個成員的列舉：

- [`Street`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Street/)預設值
- [`Satellite`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Satellite/)
- [`Hybrid`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Hybrid/)

[MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)檔會示範如何使用選項按鈕以選取地圖類型。 它會使用[ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫和類別取決於[MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)檔案。

### <a name="map-coordinates"></a>地圖座標

程式可以取得目前區域`Map`顯示透過[ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/)屬性。 這個屬性是*不*揭開可繫結的屬性，而且沒有任何通知機制，指出當已變更，因此想要監視之屬性的程式應該針對該用途使用計時器。

`VisibleRegion` 型別[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)，具有四個唯讀屬性的類別：

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) 型別 [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) 型別的`double`，表示地圖的顯示區域的高度
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) 型別的`double`，表示地圖的顯示區域寬度
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) 型別的[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)，指出最大循環區域在地圖上顯示的大小

`Position` 和`Distance`是這兩個結構。 `Position` 定義兩個唯讀屬性，透過設定[`Position`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` 會提供獨立單位的距離度量和英制單位之間進行轉換。 A`Distance`值可由數種方式：

- [`Distance` 建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/)與以公尺為單位的距離
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) 靜態方法
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) 靜態方法
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) 靜態方法

這個值是可從三個屬性：

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) 型別 `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) 型別 `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) 型別 `double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)檔案包含數個`Label`顯示的項目`MapSpan`資訊。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)程式碼後置檔案會使用計時器讓更新的使用者管理對應資訊。

### <a name="position-extensions"></a>位置擴充功能

本書名為的新程式庫[ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含對應特定但平台無關的類型。 [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別具有`ToString`方法`Position`，和一個方法來計算兩個距離`Position`值。

### <a name="setting-an-initial-location"></a>設定初始的位置

您可以呼叫[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/)方法`Map`以程式設計方式設定對應的位置中與縮放層級。 引數是型別`MapSpan`。 您可以建立`MapSpan`物件使用下列任一項：

- [`MapSpan` 建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/)與`Position`，和緯度和經度涵蓋範圍
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) 與`Position`和 radius

您也可建立新`MapSpan`從現有的方式[ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/)或[ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/)。

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)檔案和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)程式碼後置檔案示範如何使用`MoveToRegion`方法來顯示狀態的我們在此將。

或者，您可以使用[`Map`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/)與`MapSpan`物件來初始化對應的位置。 [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)檔會示範如何執行此動作可在 Xamarin 的總部顯示位於舊金山 XAML 中。

### <a name="dynamic-zooming"></a>動態縮放

您可以使用`Slider`動態縮放地圖。 [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)檔案和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)程式碼後置檔案顯示如何變更對應為基礎的 radius`Slider`值。

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)檔案和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)程式碼後置檔案顯示在 Android 的效果更好的替代方法，但這兩種方法也適用於 Windows平台。

### <a name="the-phones-location"></a>手機上的位置

[ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/)屬性`Map`的運作方式稍有不同與三個平台上[ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)檔案示範：

- 在 iOS、 藍色點表示手機上的位置，但您必須手動瀏覽那里
- 在 Android 上，會顯示圖示，當推入移到手機上的位置對應
- UWP iOS 類似，但有時自動瀏覽至位置

**MapDemos**專案嘗試模擬 Android 方法，方式是先定義根據圖示型按鈕[MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)檔案和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)程式碼後置檔案。

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)檔案和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)程式碼後置檔案會使用這個按鈕來瀏覽到手機上的位置。

### <a name="pins-and-science-museums"></a>Pin 與科學博物館

最後，`Map`類別會定義[ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/)型別的屬性`IList<Pin>`。 [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/)類別會定義四個屬性：

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) 型別的`string`、 必要的屬性
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) 型別的`string`，選擇性人類看得懂的地址
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) 型別的`Position`，指出 pin 會顯示在地圖上的位置
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) 型別的[ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/)，列舉型別，不是使用

**MapDemos**專案包含檔案[ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)，其中會列出在美國，科學博物館及[ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)和[`Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)進行還原序列化此資料的類別。

[ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)檔案和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)用於這些科學博物館，對應中的程式碼後置檔案顯示 pin。 當使用者點選 pin 時，它會顯示的位址和博物館的網站。

### <a name="the-distance-between-two-points"></a>兩個點之間的距離

[ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別包含[ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)使用簡化的兩個地理位置之間的距離計算方法。

這用於[LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)檔案和[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)也顯示使用者的位置從博物館距離的程式碼後置檔案：

[![本機博物館頁面的三個螢幕擷取畫面](images/ch28fg28-small.png "到位置距離")](images/ch28fg28-large.png#lightbox "位置的距離")

程式也會示範如何以動態方式限制 pin 根據對應的位置數目。

## <a name="geocoding-and-back-again"></a>地理編碼來回

[ **Xamarin.Forms.Maps** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)組件也包含[ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/)類別[ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/)將轉換的方法零或更多可能的地理位置，而且另一個方法文字位址[ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/)另一方向將轉換。

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)檔案和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)程式碼後置檔案示範此功能。



## <a name="related-links"></a>相關連結

- [章 28 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [章 28 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [地圖控制項](~/xamarin-forms/user-interface/map.md)
