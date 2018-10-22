---
title: 使用 Google 地圖應用程式開發介面，以在您的應用程式
description: 如何在 Xamarin.Android 應用程式中實作 Google Maps API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: a0e010a8300eb4b4452737e34d2f55a35ab95428
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935135"
---
# <a name="using-the-google-maps-api-in-your-application"></a>應用程式中使用 Google Maps API

使用對應的應用程式很棒，但有時在您想要直接在您的應用程式中包含的對應。 除了內建對應應用程式，也提供 Google[適用於 Android 的原生對應 API](https://developers.google.com/maps/documentation/android/)。
適當的情況下，您要維護更充分掌控對應經驗 Maps API。 與 Maps API 可能會有的項目包括：

-  以程式設計方式變更對應的觀點來看。
-  加入和自訂標記。
-  註釋具有重疊的對應。

不同於目前已被取代的 Google 地圖 Android API v1、 Google 地圖 Android API v2 屬於[Google Play 服務](http://developer.android.com/google/play-services/index.html)。
因此，它是為了符合某些必要的必要條件，很可能在 Xamarin.Android 應用程式中使用 Google 地圖 Android API。


## <a name="google-maps-api-prerequisites"></a>Google 對應 API 必要條件

數個項目必須先設定，然後您可以使用對應應用程式開發介面，包括：

-  安裝 Google Play 服務 SDK
-  使用 Google Api 建立模擬器
-  取得對應的 API 金鑰
-  指定必要的權限



### <a name="install-the-google-play-services-sdk"></a>安裝 Google Play 服務 SDK

Google Play 服務是向 Google，允許利用各種 Google 功能，例如 Google +、-應用程式計費，與對應的 Android 應用程式的技術。 為背景服務包含在中，這些功能都可在 Android 裝置上存取[Google 播放服務 APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)。

與 Google Play 服務的 android 應用程式是透過 Google Play 服務的用戶端程式庫互動。 此程式庫包含介面和類別，像是對應個別的服務。 下圖顯示 Android 應用程式和 Google Play 服務之間的關聯性：

![圖表說明更新 Google 播放服務 APK Google Play 商店](maps-api-images/play-services-diagram.png)

Android Maps API 提供 Google Play 服務的一部分。
Xamarin.Android 應用程式可以使用 Maps API 之前，Google 播放服務 SDK 必須安裝並繫結。 Google 播放服務 SDK 會安裝 Android SDK Manager。 下列螢幕擷取畫面顯示 Android SDK Manager 中的 Google Play 服務用戶端可以找到的位置：

![Google Play 服務之下 Extras Android SDK Manager 中](maps-api-images/image01.png)

> [!NOTE]
> Google Play 服務 APK 是可能不會出現在所有裝置上提供授權的產品。 如果未安裝，然後 Google 地圖不適用於裝置。


#### <a name="binding-google-play-services"></a>繫結 Google Play 服務

安裝 Google Play 服務的用戶端程式庫之後，必須繫結 Xamarin.Android Java 繫結程式庫。 有兩種方式可以完成這項作業：

-  **使用 Google 播放服務對應 NuGet 封裝**-這是最簡單的方式 （僅適用於 Xamarin.Android 4.8 或更新版本）。
   安裝[Xamarin Google 播放服務對應 NuGet](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); 這也會安裝任何 Google Play 服務相依性套件。
   本指南的其餘部分著重在這種方法。

-  **以手動方式將繫結 Google Play 服務的用戶端程式庫**-這是更複雜的方式，是 Xamarin.Android 4.4 或 Xamarin.Android 4.6 Google 播放服務 SDK 將繫結的唯一方式。
   手動繫結 Google Play 服務的用戶端程式庫已超出本文件中，範圍，但若要這樣做的範例，請參閱[地圖與位置示範 v3 範例](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3)Github 上。


#### <a name="adding-the-google-play-services-map-package"></a>新增 Google Play 服務對應套件

若要將 Google 播放服務對應的封裝，以滑鼠右鍵按一下**參考**在 [方案總管]，然後按一下您專案的資料夾**管理 NuGet 封裝...**:

![方案總管顯示管理 NuGet 封裝內容功能表項目底下的參考](maps-api-images/image02.png)

這會開啟**NuGet 套件管理員**。 按一下**瀏覽**輸入**Xamarin Google 播放服務對應**搜尋 欄位中。 選取**Xamarin.GooglePlayServices.Maps**按一下**安裝**。 (如果之前有未安裝此套件，請按一下**更新**。):

[![所選取的 Xamarin.GooglePlayServices.Maps 封裝使用的 NuGet 封裝管理員](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

請注意下列相依性套件也會安裝：

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**



### <a name="create-an-emulator-with-google-apis"></a>使用 Google Api 建立模擬器

雖然不建議，您可設定來支援 Android Maps API 模擬器。 模擬器必須設定為目標 Google 應用程式開發介面層級 17 （Android 4.2.2 節） 或更高版本。 在下列螢幕擷取畫面，模擬器映像已針對 API Level 19: 

![Android 模擬器管理員，以設定 API Level 19 AVD](maps-api-images/image04.png)



### <a name="obtain-a-google-maps-api-key"></a>取得 Google 地圖服務 API 金鑰

最後一個步驟是取得 Google Maps API 金鑰 （請注意，您無法重複使用舊版 Google 地圖 v1 API 金鑰）。 如需如何取得及 Xamarin.Android 搭配使用的 API 金鑰資訊，請參閱[取得 Google 地圖 API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
 


### <a name="specify-the-required-permissions"></a>指定必要的權限

必須指定下列權限**AndroidManifest.XML** Google 地圖 Android api:

-  **存取的網路狀態** &ndash; Maps API 必須能夠檢查是否網域控制站可以下載地圖底圖。

-  **網際網路存取**&ndash;網際網路存取權，才能下載地圖底圖，並與應用程式開發介面存取 Google 播放伺服器通訊。

-  **OpenGL ES v2** &ndash;應用程式必須宣告 OpenGL ES v2 的需求。

-  **Google 地圖服務 API 金鑰** &ndash; API 金鑰用來確認應用程式已註冊且已授權使用 Google Play 服務。 請參閱[取得 Google 地圖服務 API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)如需此金鑰的詳細資訊。

-  **寫入外部儲存體** &ndash; Google 地圖 Android API 將快取至外部儲存體下載圖格。

-  **Google 網頁服務的存取權**&ndash;應用程式必須能夠存取 Google 備份 Android Maps API 的 web 服務的權限。

-  **Google 播放服務通知權限**&ndash;應用程式必須被授與權限從 Google Play 服務接收遠端的通知。

-  **存取位置提供者**&ndash;這些是選擇性的權限。
   它們可讓`GoogleMap`類別，以在地圖上顯示裝置的位置。


下列程式碼片段是必須新增至設定的範例**AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```


## <a name="the-googlemap-class"></a>GoogleMap 類別

已處理的必要條件之後, 就可以開始開發應用程式，並使用 Android Maps API 開始。 [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap)類別是主要 Xamarin.Android 應用程式將用來顯示和互動 Google 地圖，適用於 Android API。 這個類別具有下列責任：

-  與授權與 Google web 服務應用程式的 Google Play 服務互動。

-  下載、 快取，並顯示地圖底圖。

-  顯示 UI 控制項，例如移動瀏覽和縮放至使用者。

-  在地圖中繪製標記和幾何形狀。

`GoogleMap`加入至活動中有兩種：

-  **MapFragment** - [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html)是特製化的片段，可做為主機`GoogleMap`物件。 `MapFragment`需要 12 或更高版本的 Android API 層級。
   可以使用較舊版本的 Android [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html)。

-  **MapView** - [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/)是特殊的檢視子類別可以當做主機`GoogleMap`物件。 這個類別的使用者必須將所有的活動生命週期方法轉送`MapView`類別。

每個容器公開`Map`傳回的執行個體的屬性`GoogleMap`。 喜好設定應該授予[MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html)類別，如它是一個簡單的 API，可降低開發人員必須以手動方式實作量未定案程式碼。


### <a name="adding-a-mapfragment-to-an-activity"></a>MapFragment 加入活動

下列螢幕擷取畫面是非常簡單的範例`MapFragment`:

[![顯示對應片段裝置的螢幕擷取畫面](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

類似於其他片段類別，有兩種方式加入`MapFragment`活動：

-   **以宣告方式**-`MapFragment`可以透過 XML 配置檔案加入活動。 下列 XML 程式碼片段示範如何使用`fragment`項目：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **以程式設計方式控制程式**-`MapFragment`可以新增以程式設計的方式如下所示。

若要以程式設計方式加入`MapFragment`，您的活動必須實作`IOnMapReadyCallback`介面。 因為初始化`GoogleMap`物件可能需要一些時間才能完成，因為應用程式開發介面會與 Google Play 進行通訊，您必須提供回呼，會通知您的應用程式時`GoogleMap`就緒。

首先，新增`IOnMapReadyCallback`至`Activity`類別宣告。
例如: 

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

接下來，在`OnCreate`方法，加入`MapFragment`如下列程式碼範例所示 (`GoogleMapOptions`類別會在本指南稍後說明):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

A`GoogleMap`必須使用取得`GetMapAsync`，結尾的先前的程式碼範例所示&ndash;這會自動初始化對應系統和檢視。 (請注意，這個方法不會使用`await` / `async`語意&ndash; `Async` android 實作行為。)當`GoogleMap`物件已經就緒，Android 呼叫您的應用程式`OnMapReady`方法 (其中的一部分，您必須實作`IOnMapReadyCallback`介面)。 例如: 

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

在上述程式碼範例中，`OnMapReady`回呼初始化`_map`變數建立`GoogleMap`物件。

如何使用此結果中，例如當`OnResume`是呼叫，它可以檢查，看看是否`_map`為非 null。 如果`_map`設`GoogleMap`物件`OnResume`上加入標記並將其相機移至指定的經度和緯度可呼叫的方法。 如需完整的程式碼範例，請參閱[SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)。



### <a name="map-types"></a>將型別對應

有五個不同類型的地圖 Google Maps API 可用：

-  **一般**-這是預設的對應型別。 它會顯示道路和重要自然的功能，以及 （例如建築物和橋接器） 感興趣的部分這二點。

-  **附屬**-這個對應可顯示附屬攝影。

-  **混合式**-這個對應可顯示附屬攝影和 road 對應。

-  **地形**-這主要是會顯示某些道路與地理特性。

-  **無**-此對應不會載入任何磚，它會轉譯為空的方格。


下圖顯示三種不同的地圖，從左到右 （一般的混合，地形）：

[![三個對應範例螢幕擷取畫面： Normal、 混合式和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType`屬性用來設定或變更之地圖的類型會顯示。 下列程式碼片段示範如何顯示附屬對應。

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```


### <a name="googlemap-properties"></a>GoogleMap 屬性

`GoogleMap` 定義數個屬性，可以控制功能，以及地圖的外觀。 其中一種方式設定的初始狀態`GoogleMap`是傳遞[GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html)物件建立時`MapFragment`。 下列程式碼片段是一個範例使用`GoogleMapOptions`物件建立時`MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

若要設定的其他方式`GoogleMap`物件是藉由設定值[UiSettings](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html)地圖物件的屬性。 下一步的程式碼範例示範如何設定`GoogleMap`顯示縮放控制項和羅盤：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```


## <a name="interacting-with-the-map"></a>與地圖互動

Android Maps API 提供了 API 可讓活動在變更觀點來看，新增標記，將自訂的覆疊或繪製幾何圖案。 本節將討論如何完成某些 Xamarin.Android 處理這些工作。

### <a name="changing-the-viewpoint"></a>變更觀點來看

對應會在畫面上，根據 Mercator 投射，和模型化成一般的平面。 地圖檢視，就是*相機*尋找直接關閉此平面上。 變更位置、 縮放、 傾斜，以及關係可以控制相機的位置。 [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate)類別用來移動相機的位置。 `CameraUpdate` 物件不直接執行個體化，改為 Maps API 提供[CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html)類別。

一次`CameraUpdate`已建立物件，做為參數傳遞至[GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera%28com.google.maps.CameraUpdate%29)或[GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera%28com.google.maps.CameraUpdate%29)方法。 `MoveCamera`方法會更新對應時立即`AnimateCamera`方法提供 smooth、 動畫的轉換。

此程式碼片段是簡單的範例使用方式的`CameraUpdateFactory`建立`CameraUpdate`，會以一遞增地圖的縮放層級：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Maps API 提供[CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html)這會相機位置的可能值的所有彙總。 這個類別的執行個體可以提供給[CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法以傳回`CameraUpdate`物件。 Maps API 也包含[CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)提供 fluent 應用程式開發的應用程式開發介面建立類別`CameraPosition`物件。
下列程式碼片段示範建立`CameraUpdate`從`CameraPosition`及變更相機位置上使用該`GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

在先前的程式碼片段中，在地圖上的特定位置由[LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng)類別。 縮放層級設定為 18。 並無影響是順時針方向從 North 羅盤的度量。 傾斜屬性會控制將檢視角度，而且是指定垂直 25 度角度。 下列螢幕擷取畫面顯示`GoogleMap`之後執行上述程式碼：

[![顯示指定的位置與傾斜範例 Google 地圖檢視的角度](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>在地圖上的繪圖

Android Maps API 提供應用程式開發介面的繪製地圖上的下列項目：

-  **標記**-這些是特殊的圖示，可用來識別在地圖上的單一位置。

-  **覆疊**-這是可以用來識別的位置或在地圖上的區域集合的映像。

-  **線條、 多邊形和圓形**-這些是允許活動圖形新增至對應的 Api。


#### <a name="markers"></a>Markers

Maps API 提供[標記](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker)類別會封裝所有地圖上的單一位置的相關資料。 根據預設，它們會使用 Google 地圖所提供的標準圖示。 您可自訂的標記外觀以及回應使用者按鍵動作。


##### <a name="adding-a-marker"></a>加入標記

若要將標記加入至地圖，就必須建立新[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)物件，然後呼叫[AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法`GoogleMap`執行個體。 這個方法會傳回[標記](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker)物件。

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(markerOpt1);
}
```

標記的標題會顯示在*資訊視窗*當使用者點選標記上。 下列螢幕擷取畫面顯示此標記的外觀：

[![範例 Google 地圖標記與 Vimy 立體浮凸 [資訊] 視窗](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>自訂標記

可自訂圖示標記由呼叫`MarkerOptions.InvokeIcon`時加入至地圖標記的方法。
這個方法會採用[BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html)物件，其中包含所需呈現圖示的資料。 [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html)類別會提供一些協助程式方法，以簡化建立`BitmapDescriptor`。 下列清單介紹其中一些方法：

-   `DefaultMarker(float colour)` &ndash; 使用預設 Google 地圖標記，但變更色彩。

-   `FromAsset(string assetName)` &ndash; 從指定的檔案中的 Assets 資料夾中使用自訂的圖示。

-   `FromBitmap(Bitmap image)` &ndash; 使用指定的點陣圖為圖示。

-   `FromFile(string fileName)` &ndash; 從位於指定路徑的檔案中建立自訂的圖示。

-   `FromResource(int resourceId)` &ndash; 從指定的資源建立自訂圖示。

下列程式碼片段示範建立青色色彩的預設標記：

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(markerOpt1);
}
```


#### <a name="info-windows"></a>Windows 資訊

*資訊 windows*該快顯] 以顯示資訊給使用者，當它們點選 [使用特定的標記是特殊的視窗。 根據預設資訊視窗會顯示標記的標題的內容。 如果尚未指派的標題，則沒有 [資訊] 視窗會出現。 只有一個資訊視窗可能會顯示一次。

可自訂 [資訊] 視窗，藉由實作[GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)介面。 此介面上有兩個重要的方法：

-  `public View GetInfoWindow(Marker marker)` &ndash; 取得標記的自訂資訊視窗會呼叫這個方法。 如果它傳回`null`，則會使用預設視窗呈現。 如果此方法傳回的檢視，該檢視就會在資訊視窗框架內。

-  `public View GetInfoContents(Marker marker)` &ndash; 如果 GetInfoWindow 傳回，則只會呼叫這個方法`null`。 這個方法可以傳回`null`如果使用預設呈現的資訊視窗內容的值。 否則，此方法應傳回的資訊視窗內容的檢視。

資訊視窗不是即時檢視-改為 Android 會將檢視轉換為靜態的點陣圖和顯示的映像上。 這表示資訊視窗無法回應任何觸控事件或手勢，也不將它自動自我更新。 若要更新的資訊視窗，就必須呼叫[GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下圖顯示一些自訂的資訊視窗的一些範例。 在左邊的映像具有它的內容來自訂，而右邊的映像的視窗和自訂的內容：

![範例標記為墨爾本，包括圖示和母體擴展的的視窗。 右側視窗具有圓角。](maps-api-images/marker-infowindows.png)


#### <a name="ground-overlays"></a>地面覆疊

不同於標記，找出在地圖上的特定位置， [GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html)是用來識別的位置或在地圖上的區域集合的映像。


##### <a name="adding-a-groundoverlay"></a>加入 GroundOverlay

加入至地圖地面重疊是非常類似於將標記加入至地圖。 首先， [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html)建立物件。 此物件隨後會傳遞為參數，以`GoogleMap.AddGroundOverlay`方法，將會傳回`GroundOverlay`物件。 此程式碼片段是地面重疊影像加入對應的範例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

下列螢幕擷取畫面會顯示在地圖上此重疊：

[![北極熊的重疊映像的範例圖](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>線條、 圓形和多邊形

有三種可以加入至對應的幾何數字的簡單類型：

-  **聚合線條**-這是一系列連接的直線線段。 它可以在地圖上的路徑，或形成所需的任何圖形。

-  **多邊形**-這是封閉的圖形來標示地圖上的區域。

-  **圓形**-這會在地圖上繪製圓形。



##### <a name="polylines"></a>聚合線條

A[聚合線條](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline)是一份連續`LatLng`物件會指定每個直線線段的端點。 聚合線條由第一個建立`PolylineOptions`物件並將點加入它。 `PolylineOption`物件隨後會傳遞至`GoogleMap`藉由呼叫物件`AddPolyline`方法。

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```


##### <a name="polygons"></a>多邊形

`Polygon`非常類似於`Polyline`s，但在不開啟結束。 `Polygon`s 是封閉的迴圈，而且具有填入其內部。
`Polygon`以完全相同的方式建立`Polyline`，除了[GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))叫用方法。

不同於`Polyline`、`Polygon`自行關閉。 當`AddPolygon`呼叫時，自動關閉方法會關閉繪製線條連接的第一個和最後一個點的多邊形。 下列程式碼片段會透過之前的程式碼片段中的相同位置建立實心矩形`Polyline`範例。

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```


##### <a name="circles"></a>圓形

建立圓形的第一個具現化[CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions)將會指定在中間和圓形的半徑 metres 中的物件。 藉由呼叫在地圖上繪製圓形[GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions))。
下列程式碼片段示範如何繪製圓形：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (circleOptions);
```


## <a name="responding-to-events"></a>回應事件

有三種類型的使用者可能會發生對應的互動：

-  **標記按一下**-使用者按一下標記上。

-  **標記拖曳**-使用者已長時間-按一下 mparger 上

-  **資訊視窗中按一下** -使用者已按一下資訊視窗上。

將以下更詳細討論每個事件。


### <a name="marker-click-events"></a>按一下 [事件] 標記

當使用者按一下標記上`MarkerClick`會引發取代事件，以及`GoogleMap.MarkerClickEventArgs`傳入。 這個類別包含兩個屬性：

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; 這個屬性應該設定為`true`表示事件處理常式耗用事件。 如果這個值設定為`false`則預設行為將會發生除了自訂事件處理常式的行為。

-  `P0` &ndash; 這個不良名稱參數為引發標記來參考`MarkerClick`事件。


此程式碼片段顯示的範例`MarkerClick`，會相機的位置變成地圖上的新位置：

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>標記拖曳事件

當使用者想要將標記拖曳時，會引發這個事件。 根據預設，不是可拖曳標記。 標記可以設定為可拖曳藉由設定`Marker.Draggable`屬性`true`或叫用`MarkerOptions.Draggable`方法`true`做為參數。

若要先拖曳標記，使用者必須長時間按一下它，並保留其手指在地圖上。 當使用者拖曳它們在螢幕上的手指時，標記會移動。 當使用者的手指取消關閉螢幕時，標記會留在原處。

下列清單描述可拖曳標記都會引發各種事件：

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; 使用者第一次將標記時，會引發這個事件。

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; 當被拖曳的標記，則會引發這個事件。

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; 會引發這個事件時當使用者完成拖曳標記。

每個`EventArgs`包含單一屬性呼叫`P0`也就是參考`Marker`正在拖曳的物件。


### <a name="info-window-click-events"></a>資訊視窗中按一下 事件

只能有一個 [資訊] 視窗可以顯示一次。 當使用者按一下圖中的資訊視窗中時，將會引發對應物件`InfoWindowClick`事件。 下列程式碼片段示範如何連接至事件處理常式：

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

請記住，資訊視窗會是靜態`View`這會轉譯為影像地圖上。 例如按鈕、 核取方塊或放置於 [資訊] 視窗的文字檢視任何 widget 會惰性，而且無法回應任何其整數類資料的使用者事件。



## <a name="related-links"></a>相關連結

- [Google Play 服務](http://developer.android.com/google/play-services/index.html)
- [Google 對應 Android API v2](https://developers.google.com/maps/documentation/android/)
- [Google Play 服務 APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
