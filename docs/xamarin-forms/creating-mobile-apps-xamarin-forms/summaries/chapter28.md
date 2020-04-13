---
title: 第28章摘要。 位置與地圖
description: 使用 Xamarin.表單創建行動應用程式:第 28 章摘要。 位置與地圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697083"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第28章摘要。 位置與地圖

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

Xamarin.Forms[`Map`](xref:Xamarin.Forms.Maps.Map)支援 派`View`生自 的元素。 由於使用地圖涉及的特殊平臺要求,它們在單獨的程式集**Xamarin.Forms.maps**中實現,並且涉及不同的命名`Xamarin.Forms.Maps`空間: 。

## <a name="the-geographic-coordinate-system"></a>地理座標系

地理座標系標識像地球這樣的球形(或近球形)物體上的位置。 座標由以角度表示的*緯度*和*經度*組成。

一個叫做「大`equator`圓」的大圓圈位於地球軸概念延伸的兩極之間。

### <a name="parallels-and-latitude"></a>平行與緯度

從地球中心測量赤道北或南的角度標記了稱為*平行*的等緯度線。 這些範圍從赤道的0度到南北兩極的90度。 按照慣例,赤道以北的緯度為正值,赤道以南的緯度為負值。

### <a name="longitude-and-meridians"></a>經度和經絡

從北極到南極的大圓圈是經度相等的線,也稱為*經絡*。 這些是相對於英國格林威治的子午線。 按照慣例,主子午線以東的經度為 0 度到 180 度的正值,而主子午線的西度&ndash;為 0 度到 180 度的負值。

### <a name="the-equirectangular-projection"></a>等矩形投影

地球的任何平面地圖都會帶來扭曲。 如果所有緯度和經度線都是直線,並且緯度和經度角度的相等差異對應於地圖上的相等距離,則結果是*等矩形投影*。 此地圖扭曲了靠近極點的區域,因為它們是水準拉伸的。

### <a name="the-mercator-projection"></a>墨卡托投影

流行的*墨卡托投影*試圖通過垂直拉伸這些區域來補償水準拉伸。 這將導致一個地圖,其中兩極附近的區域看起來比實際面積大得多,但任何局部區域都與實際區域非常接近。

### <a name="map-services-and-tiles"></a>地圖服務和磁貼

地圖服務使用稱為`Web Mercator`的墨卡托投影的變體。 地圖服務根據位置和縮放級別向用戶端提供位圖切片。

## <a name="getting-the-users-location"></a>取得使用者的位置

Xamarin.Forms`Map`類不包括獲取使用者地理位置的設施,但在使用地圖時通常需要這樣做,因此依賴項服務必須處理它。

> [!NOTE]
> Xamarin.Forms 應用程式可以改[`Geolocation`](~/essentials/geolocation.md)用 Xamarin 中包含的類。

### <a name="the-location-tracker-api"></a>位置追蹤器 API

[**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案包含位置跟蹤器 API 的代碼。 該[`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)結構封裝了緯度和經度。 該[`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)介面定義了啟動和暫停位置跟蹤器的兩種方法,以及新位置可用時的事件。

#### <a name="the-ios-location-manager"></a>iOS 位置管理員

iOS`ILocationTracker`的實現是[`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)一個使用[`CLLocationManager`](xref:CoreLocation.CLLocationManager)iOS 的類。

#### <a name="the-android-location-manager"></a>Android 位置管理員

的`ILocationTracker`Android[`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)實現是一個[`LocationManager`](xref:Android.Locations.LocationManager)使用 Android 類的類。

#### <a name="the-uwp-geo-locator"></a>UWP 地理定位器

通用 Windows`ILocationTracker`平台的[`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)實現是一個[`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)使用 UWP 的類。

### <a name="display-the-phones-location"></a>顯示手機的位置

[**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)示例使用位置跟蹤器以文本和等矩形地圖顯示手機的位置。

### <a name="the-required-overhead"></a>所需的花費

**在何處使用**位置跟蹤器時,需要一些開銷。 首先 **,WhereAmI**解決方案中的所有專案都必須引用**Xamarin.FormsBook.Platform 中的**相應項目,並且每個**位置 AmI**專案都必須調用`Toolkit.Init`該方法。

需要一些額外的特定於平台的開銷(以位置許可權的形式)。

#### <a name="location-permission-for-ios"></a>iOS 的位置權限

對於**iOS,info.plist**檔必須包含包含問題文本的專案,要求使用者允許獲取該使用者的位置。

#### <a name="location-permissions-for-android"></a>Android 的位置權限

獲取使用者位置的 Android 應用程式必須在 AndroidManifest.xml 檔中具有ACCESS_FILE_LOCATION許可權。

#### <a name="location-permissions-for-the-uwp"></a>UWP 的位置權限

通用 Windows 平台應用程式必須`location`在包 .appxmanifest 檔中標記設備功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms.地圖

使用`Map`類涉及幾個要求。

### <a name="the-nuget-package"></a>NuGet 套件

必須將**Xamarin.Forms.Map** NuGet 庫添加到應用程式解決方案中。 版本號應與目前安裝的**Xamarin.Forms**包相同。

### <a name="initializing-the-maps-package"></a>初始化地圖包

應用程式項目必須在呼叫`Xamarin.FormsMaps.Init`後 呼`Xamarin.Forms.Forms.Init`叫方法 。

### <a name="enabling-map-services"></a>啟用地圖服務

由於`Map`可以取得使用者的位置,因此應用程式必須以本章前面所述的方式獲得用戶的許可權:

#### <a name="enabling-ios-maps"></a>開啟 iOS 地圖

使用`Map`iOS 應用程式需要在 info.plist 檔中提供兩行。

#### <a name="enabling-android-maps"></a>開啟 Android 地圖

使用 Google 地圖服務需要授權金鑰。 此金鑰插入到**AndroidManifest.xml**檔中。 此外 **,AndroidManifest.xml**檔`manifest`需要 獲取使用者位置時涉及的標記。

#### <a name="enabling-uwp-maps"></a>開啟 UWP 地圖

通用 Windows 平台應用程式需要使用必應地圖的授權密鑰。 此鍵作為參數傳遞給`Xamarin.FormsMaps.Init`方法。 還必須為定位服務啟用應用程式。

### <a name="the-unadorned-map"></a>樸素地圖

[**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)範例由[MapDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)檔案和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)代碼後檔組成,該檔允許導航到各種展示程式。

[基本MapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)檔演示如何[`Map`](xref:Xamarin.Forms.Maps.Map)顯示 視圖。 默認情況下,它顯示羅馬市,但地圖可以由使用者操作。

要關閉水平與垂直捲動,請將[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)屬性設定為`false`。 要關閉縮放,請[`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)設定為`false`。 這些屬性可能不適用於所有平臺。

### <a name="streets-and-terrain"></a>街道和地形

您可以通過設定`Map`[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)[`MapType`](xref:Xamarin.Forms.Maps.MapType)類型 屬性顯示不同類型的地圖,一個帶有三個成員的枚舉:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street),預設值
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)檔展示如何使用單選按鈕來選擇地圖類型。 它利用[`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類和基於[MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)檔的類。

### <a name="map-coordinates"></a>地圖座標

程式可以`Map`[`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)通過 屬性獲取 顯示的當前區域。 此屬性*不*由可綁定屬性支援,並且沒有指示更改時間的通知機制,因此希望監視該屬性的程式可能應該為此使用計時器。

`VisibleRegion`類型[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)為 ,具有四個唯讀屬性的類:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)類型[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)型`double`態 ,指示地圖顯示區域的高度
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)類型`double`,指示地圖顯示區域的寬度
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)類型[`Distance`](xref:Xamarin.Forms.Maps.Distance),表示地圖上可見的最大圓形區域的大小

`Position`和`Distance`都是結構。 `Position`定義透過[`Position`建構函數](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))設定的兩個唯讀屬性:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`旨在通過在公制單位和英語單位之間進行轉換,提供與單位無關的距離。 可以`Distance`通過多種方式建立值:

- 距離(以米為單位)的建構函數[`Distance`](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))靜態方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))靜態方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))靜態方法

該值可從三個屬性獲得:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)類型`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)類型`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)類型`double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)檔包含`Label`多個`MapSpan`用於顯示 資訊的元素。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)代碼背後的檔使用計時器在使用者操作地圖時保持資訊更新。

### <a name="position-extensions"></a>位置延伸

本書的新庫名為[**Xamarin.FormsBook.Toolkit.Map**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含特定於地圖但與平台無關的類型。 類[`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)`ToString`具有`Position`方法和 方法來`Position`計算兩 個值之間的距離。

### <a name="setting-an-initial-location"></a>設定初始位置

可以調用[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan))`Map`方法 以程式設計方式在地圖上設置位置和縮放級別。 參數的型`MapSpan`態為 。 可以使用以下任一`MapSpan`建立物件:

- 具有`Position`的[建構函數,以及緯度和經度跨`MapSpan`度](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance))帶`Position`和半徑

也可以使用方法`MapSpan`[`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double))[`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))或創建來自現有方法的新。

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)檔和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)代碼背後的檔`MoveToRegion`演示如何使用 該方法來顯示懷俄明州。

或者,[`Map`可以將建構函數](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan))與`MapSpan`物件一 起初始化地圖的位置。 [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)檔演示如何在 XAML 中完全執行此操作,以顯示 Xamarin 在舊金山的總部。

### <a name="dynamic-zooming"></a>動態縮放

可以使用動態`Slider`縮放地圖。 [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)檔案和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)代碼背後的檔演示如何根據值更改`Slider`地圖的 半徑。

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)檔和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)代碼背後的文件顯示了一種在 Android 上效果更好的替代方法,但兩種方法在 Windows 平臺上都效果良好。

### <a name="the-phones-location"></a>電話的位置

在[`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)`Map` [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)檔演示:

- 在 iOS 上,一個藍色點指示手機的位置,但您必須手動導航到那裡
- 在 Android 上,將顯示一個圖示,當按下時,將地圖移動到手機的位置
- UWP 與 iOS 類似,但有時會自動導航到位置

**MapDemos**專案嘗試模仿 Android 方法,首先根據[MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)檔案和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)代碼背後的檔案定義基於圖示的按鈕。

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)檔案和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)代碼背後的檔使用此按鈕導航到手機的位置。

### <a name="pins-and-science-museums"></a>針和科學博物館

最後,`Map`類別[`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)定義類型的`IList<Pin>`屬性 。 該[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別定義四個屬性:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)`string`型態 ,必需屬性
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)類型`string`,可選的人類可讀位址
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)類型`Position`,指示圖釘在地圖上的顯示位置
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)類型[`PinType`](xref:Xamarin.Forms.Maps.PinType),未使用的枚舉

**MapDemos**專案包含檔[科學博物館.xml,](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)它列出了美國的[`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)[`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)科學博物館,以及用於對這些數據進行反序列化的類。

[科學博物館Page.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)文件和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)代碼背後的文件顯示引腳為這些科學博物館在地圖。 當用戶點擊一個針腳時,它會顯示博物館的位址和網站。

### <a name="the-distance-between-two-points"></a>兩點之間的距離

該[`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)類包含一[`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)個 方法,該方法簡化了兩個地理位置之間的距離計算。

這用[LocalMuseumPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)檔案與[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)程式碼後檔案,以顯示與使用者位置到博物館的距離:

[![本地博物館頁面的三重螢幕截圖](images/ch28fg28-small.png "到位置的距離")](images/ch28fg28-large.png#lightbox "到位置的距離")

該程式還演示如何根據地圖的位置動態限制引腳數。

## <a name="geocoding-and-back-again"></a>地理編碼與傳回

[**Xamarin.Forms.Maps**](xref:Xamarin.Forms.Maps)程式集還包含一[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)個 類,[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String))其中包含一個將文本位址轉換為零個或多個可能的地理[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position))位置的方法 ,另一種方法則向另一個方向轉換。

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)檔案和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)代碼背後的檔演示了此功能。

## <a name="related-links"></a>相關連結

- [第28章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第28章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.表格地圖](~/xamarin-forms/user-interface/map/index.md)
