---
title: 第 28 章摘要。 位置與地圖
description: 使用 Xamarin.Forms 建立行動應用程式： 摘要的第 28 章。 位置與地圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bcca7bc7ac56b745945b6d7daef3c4ddd634b486
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059401"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第 28 章摘要。 位置與地圖

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

支援 Xamarin.Forms [ `Map` ](xref:Xamarin.Forms.Maps.Map)項目，衍生自`View`。 參與使用對應的特殊的平台需求，因為它們實作個別的組件中， **Xamarin.Forms.Maps**，並牽涉到不同的命名空間： `Xamarin.Forms.Maps`。

## <a name="the-geographic-coordinate-system"></a>地理的座標系統

地理的座標系統識別球面 （或幾乎球面） 的物件，例如地球上的位置。 同時包含座標*緯度*並*經度*以角度表示。

稱為 「 大圓`equator`兩極透過它的地球軸在概念上擴充之間的中間。

### <a name="parallels-and-latitude"></a>Parallels 和緯度

角度測量北或由從地球標記中央赤道行的相等稱為的緯度*parallels*。 這些範圍從 0 度位於赤道到北部和中南部兩極的 90 度。 依照慣例，赤道北方的經緯度正值，而那些由赤道負數值。

### <a name="longitude-and-meridians"></a>經度和經線

下半部大圓形從北極向南極也稱為是相等的經度，每一行*經線*。 這些是相對於在英國格林威治子午線。 依照慣例，您該以東子午線正值是從 0 度到 180 度，而子午線以西的角度的您該從 0 度的負數值&ndash;180 度。

### <a name="the-equirectangular-projection"></a>等量矩形投影

地球的任何一般對應導入了失真。 如果的緯度與經度的所有行都為直角，而且如果經度和緯度角度等於差異對應到在地圖上距離相等，則結果是*等量矩形投影*。 因為它們會水平縮放，此對應會扭曲更接近兩極的區域。

### <a name="the-mercator-projection"></a>Mercator 投射

熱門*Mercator 投射*嘗試補償也垂直延伸這些區域的水平縮放。 這會導致兩極附近的區域會遠大於真的有需要，但任何區域非常符合實際的區域顯示的對應。

### <a name="map-services-and-tiles"></a>對應的服務和磚

地圖服務會使用稱為 Mercator 投射的變化`Web Mercator`。 地圖服務位置中與縮放層級為基礎的用戶端提供點陣圖的圖格。

## <a name="getting-the-users-location"></a>取得使用者的位置

Xamarin.Forms`Map`類別不包含的程式，以取得使用者的地理位置，但這通常是需要時使用對應，因此相依性服務必須處理它。

> [!NOTE]
> Xamarin.Forms 應用程式可以改為使用[ `Geolocation` ](~/essentials/geolocation.md) Xamarin.Essentials 中包含的類別。

### <a name="the-location-tracker-api"></a>位置追蹤程式 API

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)方案包含位置追蹤器應用程式開發介面的程式碼。 [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)結構封裝緯度和經度。 [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)介面會定義兩種方法可以啟動並暫停位置追蹤程式及使用新的位置時的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理員

IOS 實作`ILocationTracker`已[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)類別，可使用的 ios [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/)。

#### <a name="the-android-location-manager"></a>Android 位置管理員

Android 的實作`ILocationTracker`已[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)類別會使用 Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)類別。

#### <a name="the-uwp-geo-locator"></a>UWP 地理定位程式

通用 Windows 平台實作`ILocationTracker`已[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)類別會使用 UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator)。

### <a name="display-the-phones-location"></a>顯示手機上的位置

[ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)範例會使用位置追蹤程式中文字與等量矩形地圖上顯示手機上的位置。

### <a name="the-required-overhead"></a>所需的額外負荷

一些額外負荷是為了**WhereAmI**使用位置追蹤程式。 首先，在專案的所有**WhereAmI**解決方案必須有對應的專案中參考**Xamarin.FormsBook.Platform**，而且每個**WhereAmI**專案必須呼叫`Toolkit.Init`方法。

需要一些額外的平台專屬負擔，位置的權限，表單中。

#### <a name="location-permission-for-ios"></a>適用於 iOS 的位置權限

適用於 iOS、 **info.plist**檔案必須包含項目包含文字的問題，詢問使用者是否要允許取得該使用者的位置。

#### <a name="location-permissions-for-android"></a>適用於 Android 的位置權限

取得使用者的位置的 android 應用程式必須擁有 ACCESS_FILE_LOCATION 權限，在 AndroidManifest.xml 檔案中。

#### <a name="location-permissions-for-the-uwp"></a>適用於 UWP 的位置權限

通用 Windows 平台應用程式必須具有`location`標示 Package.appxmanifest 檔案中的裝置功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms.Maps

幾項需求包括使用`Map`類別。

### <a name="the-nuget-package"></a>NuGet 套件

**Xamarin.Forms.Maps** NuGet 程式庫必須新增至應用程式方案。 版本號碼應為相同**Xamarin.Forms**目前已安裝的封裝。

### <a name="initializing-the-maps-package"></a>初始化對應封裝

應用程式專案必須呼叫`Xamarin.FormsMaps.Init`方法之後呼叫`Xamarin.Forms.Forms.Init`。

### <a name="enabling-map-services"></a>啟用地圖服務

因為`Map`可以取得使用者的位置、 應用程式必須取得本章稍早所述的方式中的使用者權限：

#### <a name="enabling-ios-maps"></a>啟用 iOS 對應

IOS 應用程式使用`Map`需要在 info.plist 檔案中的兩行。

#### <a name="enabling-android-maps"></a>啟用 Android 對應

提供的授權金鑰，才能使用 Google 地圖服務。 此機碼會插入**AndroidManifest.xml**檔案。 颾魤 ㄛ **AndroidManifest.xml**檔案，而需要`manifest`標記參與取得使用者的位置。

#### <a name="enabling-uwp-maps"></a>讓 UWP 對應

通用 Windows 平台應用程式需要使用 Bing 地圖服務的授權金鑰。 此金鑰會做為引數傳遞`Xamarin.FormsMaps.Init`方法。 應用程式也必須啟用定位服務。

### <a name="the-unadorned-map"></a>未裝飾的對應

[ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)範例組成[MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)檔案並[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)程式碼後置檔案可讓瀏覽至各種示範程式。

[BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)檔案示範如何顯示[ `Map` ](xref:Xamarin.Forms.Maps.Map)檢視。 依預設它會顯示城市的羅馬，但可以由使用者操作對應。

若要停用水平和垂直捲動，請設定[ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)屬性設`false`。 若要停用縮放，請設定[ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)至`false`。 這些屬性可能無法在所有平台上。

### <a name="streets-and-terrain"></a>或街道及地形模型

您可以藉由設定顯示不同類型的地圖`Map`屬性[ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType)型別的[ `MapType` ](xref:Xamarin.Forms.Maps.MapType)，含有三個成員的列舉類型：

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street)預設值
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)檔案示範如何使用選項按鈕選取地圖類型。 它會利用[ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫和類別根據[MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)檔案。

### <a name="map-coordinates"></a>地圖座標

程式可以取得目前的區域`Map`顯示透過[ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion)屬性。 這個屬性就*不*後盾可繫結的屬性，並沒有通知機制，以指出它已變更時，因此想要監視內容的程式應該針對該目的使用計時器。

`VisibleRegion` 屬於類型[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan)，具有四個唯讀屬性的類別：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) 型別 [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) 型別的`double`，表示地圖顯示區域的高度
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) 型別的`double`，表示地圖顯示區域寬度
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) 型別的[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)，表示顯示在對應的最大循環區域的大小

`Position` 和`Distance`是這兩個結構。 `Position` 透過設定的兩個唯讀屬性會定義[`Position`建構函式](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` 被要提供獨立單位的距離計量和英制單位之間轉換。 A`Distance`值可由數種方式：

- [`Distance` 建構函式](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))以公尺為單位的距離
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) 靜態方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) 靜態方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) 靜態方法

這個值是可從三個屬性：

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) 型別 `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) 型別 `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) 型別 `double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)檔案包含數個`Label`顯示的項目`MapSpan`資訊。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)程式碼後置檔案會使用計時器，將更新的使用者管理對應資訊。

### <a name="position-extensions"></a>位置擴充功能

本書名為新的程式庫[ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含對應特定但平台無關的類型。 [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別具有`ToString`方法`Position`，並計算兩個之間距離的方法`Position`值。

### <a name="setting-an-initial-location"></a>設定初始的位置

您可以呼叫[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan))方法`Map`以程式設計方式設定在地圖上的位置中與縮放層級。 引數的型別是`MapSpan`。 您可以建立`MapSpan`物件使用下列其中一項：

- [`MapSpan` 建構函式](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))與`Position`，以及緯度和經度的範圍
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) 使用`Position`和 radius

您也可建立新`MapSpan`從現有的方式[ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double))或是[ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))。

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)檔案並[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)程式碼後置檔案示範如何使用`MoveToRegion`方法，以顯示懷俄明的狀態。

您也可以使用[`Map`建構函式](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan))使用`MapSpan`物件來初始化對應的位置。 [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)檔案示範如何執行這項操作，完全以 XAML 來顯示於舊金山的 Xamarin 的總部。

### <a name="dynamic-zooming"></a>動態縮放

您可以使用`Slider`動態縮放地圖。 [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)檔案並[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)程式碼後置檔案會顯示如何變更在地圖上的 radius`Slider`值。

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)檔案並[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)程式碼後置檔案會顯示在 Android 的效果更好的替代方法，但這兩種方法適用於 Windows平台。

### <a name="the-phones-location"></a>手機上的位置

[ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser)屬性`Map`運作方式有點不同上為每個平台[ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)檔會示範：

- 在 iOS 上，藍點表示手機上的位置，但您必須以手動方式瀏覽那里
- 圖示會顯示在 Android 上，當推入移到手機上的位置對應
- UWP 類似於 iOS，但有時會自動瀏覽到位置

**MapDemos**專案嘗試模擬 Android 的方法是先定義根據圖示型按鈕[MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)檔案和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)程式碼後置檔案。

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)檔案並[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)程式碼後置檔案會使用這個按鈕來瀏覽到手機上的位置。

### <a name="pins-and-science-museums"></a>Pin 和科學博物館一樣

最後，`Map`類別會定義[ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins)型別的屬性`IList<Pin>`。 [ `Pin` ](xref:Xamarin.Forms.Maps.Pin)類別會定義四個屬性：

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) 型別的`string`、 必要的屬性
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) 型別的`string`，選擇性人類看得懂的地址
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) 型別的`Position`，指出其中 pin 會在地圖上顯示
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) 型別的[ `PinType` ](xref:Xamarin.Forms.Maps.PinType)，列舉型別，不會使用

**MapDemos**專案中包含的檔案[ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)，其中會列出美國境內的科學博物館及[ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)和[`Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)進行還原序列化此資料的類別。

[ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)檔案並[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)用於這些科學博物館一樣，在對應中的程式碼後置檔案顯示 pin。 當使用者點選 pin 碼時，它會顯示的位址，而且博物館的網站。

### <a name="the-distance-between-two-points"></a>兩個點之間的距離

[ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類別包含[ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)使用簡化的兩個地理位置之間的距離計算方法。

這用於[LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)檔案並[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)也對博物館顯示之間的距離，從使用者的所在位置的程式碼後置檔案：

[![本機的館藏頁面三個螢幕擷取畫面](images/ch28fg28-small.png "到位置距離")](images/ch28fg28-large.png#lightbox "到位置距離")

程式也會示範如何以動態方式限制對應的位置為基礎的 pin 數目。

## <a name="geocoding-and-back-again"></a>地理編碼來回

[ **Xamarin.Forms.Maps** ](xref:Xamarin.Forms.Maps)組件也包含[ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder)類別[ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String))將轉換的方法零或更多可能的地理位置，和另一種方法的文字位址[ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position))以將轉換成另一個方向。

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)檔案並[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)程式碼後置檔案會示範這項功能。



## <a name="related-links"></a>相關連結

- [第 28 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第 28 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.Forms 對應](~/xamarin-forms/user-interface/map.md)
