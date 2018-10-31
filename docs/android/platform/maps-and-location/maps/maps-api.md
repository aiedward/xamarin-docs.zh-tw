---
title: 使用 Google Maps API，以在您的應用程式
description: 如何在您的 Xamarin.Android 應用程式中實作 Google Maps API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: a89211e4576b8d22b45bb4f675241d47c49270e6
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235034"
---
# <a name="using-the-google-maps-api-in-your-application"></a>應用程式中使用 Google Maps API

使用對應的應用程式很棒，但有時您想要直接在您的應用程式中包含對應的地方。 除了內建對應應用程式，也提供 Google[適用於 Android 的原生對應 API](https://developers.google.com/maps/documentation/android-sdk/intro)。
地圖服務 API 是適用於您想要用來維護更充分掌控對應體驗的情況。 地圖服務 api 進行的事項包括：

-  以程式設計方式變更對應的觀點來看。
-  加入和自訂標記。
-  註釋具有重疊的對應。

現在已被取代 Google Maps Android API v1 與 Google Maps Android API v2 是一部分[Google Play 服務](https://developers.google.com/android/guides/overview)。
Xamarin.Android 應用程式必須符合某些必要的先決條件，才可以使用 Google Maps Android API。


## <a name="google-maps-api-prerequisites"></a>Google Maps API 的必要條件

幾個步驟需要執行時，您可以使用地圖服務 API 中，包括：

-  [取得地圖服務 API 金鑰](#obtain-maps-key)
-  [安裝 Google Play 服務 SDK](#install-gps-sdk)
-  [從 NuGet 安裝 Xamarin.GooglePlayServices.Maps 套件](#install-gpsmaps-nuget)
-  [指定必要的權限](#declare-permissions)
-  [（選擇性） 使用 Google Api 建立模擬器](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />取得 Google Maps API 金鑰

最後一個步驟是取得 Google Maps API 金鑰 （請注意，您無法重複使用舊版 Google Maps v1 API 所提供的 API 金鑰）。 如需有關如何取得和搭配 Xamarin.Android 使用 API 金鑰的資訊，請參閱 <<c0> [ 取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
 

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> 安裝 Google Play 服務 SDK

Google Play 服務是一種技術可讓 Android 應用程式，以利用各種 Google 功能，例如 Google +、 應用程式內計費及對應的 google。 這些功能都可在 Android 裝置上存取為背景服務，其包含在 f [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)。

與 Google Play 服務的 android 應用程式是透過 Google Play 服務的用戶端程式庫互動。 此程式庫包含介面和類別，例如對應個別的服務。 下圖顯示 Android 應用程式和 Google Play 服務之間的關聯性：

![說明 Google Play 商店更新 Google Play Services APK 的圖表](maps-api-images/play-services-diagram.png)

Android 的地圖服務 API 提供 Google Play 服務的一部分。
Xamarin.Android 應用程式可以使用地圖服務 API 之前，必須使用安裝 Google Play 服務 SDK [Android SDK 管理員](~/android/get-started/installation/android-sdk.md)。 下列螢幕擷取畫面顯示 Android SDK Manager 中的 Google Play 服務用戶端可以找到的位置：

![Google Play 服務之下額外項目在 Android SDK 管理員](maps-api-images/image01.png)

> [!NOTE]
> Google Play 服務 APK 已授權的產品，可能不會出現在所有裝置上。 如果未安裝，Google Maps 將無法在裝置上。

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> 從 NuGet 安裝 Xamarin.GooglePlayServices.Maps 套件

[Xamarin.GooglePlayServices.Maps 封裝](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含 Google Play 服務地圖服務 API 的 Xamarin.Android 繫結。
若要新增 Google Play 服務對應的封裝，以滑鼠右鍵按一下**參考**在 [方案總管]，然後按一下您專案的資料夾**管理 NuGet 套件...**:

![方案總管顯示管理 NuGet 套件的內容下功能表項目參考](maps-api-images/image02.png)

這會開啟**NuGet 套件管理員**。 按一下 [**瀏覽**，然後輸入**Xamarin Google Play 服務地圖**搜尋] 欄位中。 選取  **Xamarin.GooglePlayServices.Maps**然後按一下**安裝**。 (如果有先前安裝此封裝，請按一下**更新**。):

[![使用選取的 Xamarin.GooglePlayServices.Maps 套件的 NuGet 套件管理員](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

請注意下列相依性套件也會安裝：

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> 指定必要的權限

應用程式必須找出硬體和權限的需求，才能使用 Google Maps API。  Google Play 服務 SDK，會自動授與某些權限，則不需要明確地將其新增至開發人員**AndroidManfest.XML**:

-  **網路狀態的存取權**&ndash;地圖服務 API 必須要能夠檢查是否網域控制站可以下載地圖底圖。

-  **網際網路存取**&ndash;的網際網路存取僅需要下載地圖底圖，並與 API 存取 Google Play 伺服器通訊。

中，則必須指定下列權限和功能**AndroidManifest.XML** Google Maps Android api:

-  **OpenGL ES v2** &ndash;應用程式必須宣告 OpenGL ES v2 的需求。

-  **Google Maps API 金鑰** &ndash; API 金鑰用來確認應用程式註冊，並獲授權使用 Google Play 服務。 請參閱[取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)如需此金鑰的詳細資訊。
   
- **要求舊版的 Apache HTTP 用戶端** &ndash; Android 9.0 （API 層級 28） 為目標的應用程式，或必須以上所述指定舊版的 Apache HTTP 用戶端是選用的程式庫來使用。 

-  **Google Web 服務的存取權**&ndash;應用程式需要存取 Google 的 web 服務支援 Android 的地圖服務 API 的權限。

-  **Google Play 服務通知的權限**&ndash;必須授與應用程式從 Google Play 服務接收遠端通知的權限。

-  **存取位置提供者**&ndash;這些是選擇性的權限。
   它們可讓`GoogleMap`類別，以在地圖上顯示裝置的位置。


> [!NOTE]
> 非常舊版本的 Google Play SDK 所需的應用程式要求`WRITE_EXTERNAL_STORAGE`權限。 這項需求不再需要使用新的 Xamarin 繫結，Google Play 服務。

下列程式碼片段是必須新增至設定的範例**AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />
    
    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />


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

除了要求權限**AndroidManifest.XML**，應用程式也必須執行執行階段權限檢查作為`ACCESS_COARSE_LOCATION`而`ACCESS_FINE_LOCATION`權限。 請參閱[Xamarin.Android 的權限](~/android/app-fundamentals/permissions.md)指南執行的執行階段權限檢查的詳細資訊。


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />使用 Google Api 建立模擬器

未安裝 Google Play 服務與在實體 Android 裝置，就可以建立模擬器映像進行開發。 如需詳細資訊，請參閱[裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。


## <a name="the-googlemap-class"></a>GoogleMap 類別

一旦滿足必要條件，就可以開始開發應用程式，並使用 Android 的地圖服務 API。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)類別是主要的 Xamarin.Android 應用程式將用來顯示和互動適用於 Android 的 Google Maps API。 此類別具有下列責任：

-  與授權使用 Google web 服務應用程式的 Google Play 服務互動。

-  下載、 快取，並顯示地圖底圖。

-  顯示 UI 控制項，例如移動瀏覽和縮放至使用者。

-  在地圖上繪製標記和幾何形狀。

`GoogleMap`加入至活動中有兩種：

-  **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)是特製的片段做為主機`GoogleMap`物件。 `MapFragment`需要 Android API 層級 12 或更高版本。
   可以使用較舊版本的 Android [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南著重於使用`MapFragment`類別。

-  **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView)是特製化的檢視子類別，其可做為主機`GoogleMap`物件。 這個類別的使用者必須將所有活動生命週期方法，以轉送`MapView`類別。

每個容器會公開`Map`傳回的執行個體的屬性`GoogleMap`。 喜好設定應該提供給[MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)類別，因為它是簡單的 API，可降低開發人員必須以手動方式實作數量未定案程式碼。

### <a name="adding-a-mapfragment-to-an-activity"></a>MapFragment 加入活動

下列螢幕擷取畫面是簡單的範例`MapFragment`:

[![裝置顯示 Google 地圖片段的螢幕擷取畫面](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

類似於其他片段類別，有兩種方式新增`MapFragment`活動：

-   **以宣告方式**-`MapFragment`可以透過 XML 版面配置檔加入活動。 下列 XML 程式碼片段示範如何使用`fragment`項目：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Programmaatically** -`MapFragment`可以以程式設計方式使用具現化[ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance())方法然後新增至活動。 此程式碼片段顯示的最簡單的方式來具現化`MapFragment`物件，並加入活動：
    
    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    您可設定`MapFragment`物件，並傳遞[ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)物件`NewInstance`。 這一節所述[GoogleMap 屬性](#googlemap_object)，稍後會出現在本指南中。

`MapFragment.GetMapAsync`方法用來初始化[ `GoogleMap` ](#googlemap_object)片段所裝載，並取得所裝載的 map 物件的參考`MapFragment`。 這個方法會採用該物件會實作`IOnMapReadyCallback`介面。 

這個介面具有單一方法`IMapReadyCallback.OnMapReady(MapFragment map)`，將會叫用時，它會讓應用程式互動`GoogleMap`物件。 下列程式碼片段示範如何初始化 Android 活動可以`MapFragment`並實作`IOnMapReadyCallback`介面：
```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);
    
        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);
    
        // remainder of code omitted
    }
    
    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>對應類型

有五個不同類型的地圖可從 Google Maps API:

-  **一般**-這是預設的對應型別。 它會顯示道路和重要的自然功能，以及感興趣 （例如建築物和橋接器） 的某些 artifical 點。

-  **附屬**-此地圖顯示附屬攝影。

-  **混合式**-此地圖顯示附屬攝影和 road 對應。

-  **地形**-這主要是示範地理的特性，與一些道路。

-  **無**-此對應不會載入任何圖格，它會轉譯為空的方格。


下圖顯示三種不同類型的地圖，從左到右 （一般、 混合式地形）：

[![三個對應範例螢幕擷取畫面： 一般、 混合和地形模型](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType`屬性用來設定或變更之地圖的類型會顯示。 下列程式碼片段示範如何顯示附屬對應。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />GoogleMap 屬性

`GoogleMap` 定義數個屬性，可以控制的功能和地圖的外觀。 若要設定的初始狀態單向`GoogleMap`是將傳遞[GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)物件建立時`MapFragment`。 下列程式碼片段是一個範例使用`GoogleMapOptions`物件建立時`MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

若要設定的其他方式`GoogleMap`是藉由操作上的屬性[UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings)的 map 物件。 下一步 的程式碼範例示範如何設定`GoogleMap`顯示縮放控制項和羅盤：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>與 GoogleMap 互動

Android 的地圖服務 API 提供的 Api，可讓活動在變更觀點來看，新增標記、 放置自訂重疊，或繪製幾何圖案。 本節將討論如何完成一些在 Xamarin.Android 中的這些工作。

### <a name="changing-the-viewpoint"></a>變更觀點

在畫面上，根據 Mercator 投射，對應都模擬成一般的平面。 地圖檢視，就是*相機*尋找直接關閉此平面上。 變更位置、 縮放、 傾斜，和關係可以控制相機的位置。 [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)類別用來移動觀景窗位置。 `CameraUpdate` 物件不直接執行個體化，改為提供地圖服務 API [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)類別。

一次`CameraUpdate`已建立物件，做為參數傳遞至[GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate))或是[GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate))方法。 `MoveCamera`方法會更新對應時立即`AnimateCamera`方法提供 smooth、 動畫的轉換。

此程式碼片段是如何使用的簡單範例`CameraUpdateFactory`來建立`CameraUpdate`，會遞增地圖的縮放層級，由一個縮放層級：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

提供地圖服務 API [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html)這將會彙總所有觀景窗位置的可能值。 此類別的執行個體可供[CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法會將其傳回`CameraUpdate`物件。 也包含地圖服務 API [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)類別，可提供 fluent API 來建立`CameraPosition`物件。
下列程式碼片段示範建立`CameraUpdate`從`CameraPosition`用來變更觀景窗位置上`GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);
    
    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);
    
    CameraPosition cameraPosition = builder.Build();
    
    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

在先前的程式碼片段中，在地圖上的特定位置由[LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng)類別。 縮放層級會設定為 18，這是由 Google 地圖的縮放的任意量值。 關係是羅盤的從北方順時針測量。 [傾斜] 屬性會控制將檢視角度，並指定垂直 25 度的角度。 下列螢幕擷取畫面顯示`GoogleMap`執行上述程式碼之後：

[![顯示具有傾斜的指定的位置的範例 Google 地圖檢視角度](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>在地圖上繪製

Android 的地圖服務 API 會提供 API 的繪製在地圖上的下列項目：

-  **標記**-這些是用來識別在地圖上的單一位置的特殊圖示。

-  **覆疊**-這是可用來識別集合的位置或區域在地圖上的映像。

-  **線條、 多邊形和圓形**-這些是 Api，可讓活動新增至對應的圖形。


#### <a name="markers"></a>Markers

提供地圖服務 API[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)類別會封裝所有關於在地圖上的單一位置的資料。 預設標記類別會使用 Google 地圖所提供的標準圖示。 它是可以自訂標記的外觀，並回應使用者點選動作。


##### <a name="adding-a-marker"></a>新增標記

若要將標記加入至對應，就必須建立新[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)物件，然後呼叫[AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法`GoogleMap`執行個體。 這個方法會傳回[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)物件。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    map.AddMarker(markerOpt1);
}
```

標記的標題會顯示在*資訊視窗*當使用者點選在標記上。 下列螢幕擷取畫面會顯示此標記的外觀：

[![使用標記和 Vimy Ridge [資訊] 視窗的範例 Google 地圖](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>自訂標記

可自訂圖示，藉由呼叫由標記`MarkerOptions.InvokeIcon`時將標記加入至對應的方法。
這個方法會採用[BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor)物件，包含呈現圖示所需的資料。 [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)類別會提供一些協助程式方法，以簡化建立`BitmapDescriptor`。 下列清單將介紹其中一些方法：

-   `DefaultMarker(float colour)` &ndash; 使用預設 Google 地圖標記，但變更色彩。

-   `FromAsset(string assetName)` &ndash; 使用自訂圖示從 [Assets] 資料夾中指定的檔案。

-   `FromBitmap(Bitmap image)` &ndash; 使用指定的點陣圖為圖示。

-   `FromFile(string fileName)` &ndash; 從位於指定路徑的檔案建立自訂的圖示。

-   `FromResource(int resourceId)` &ndash; 從指定的資源建立自訂的圖示。

下列程式碼片段示範建立青色彩色的預設標記的範例：

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);
    
    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Windows 資訊

*資訊 windows*該快顯 以顯示給使用者的資訊，當他們點選特定的標記是特殊的視窗。 預設值 [資訊] 視窗會顯示的標記項目的內容。 如果尚未指派的標題，則會不出現任何資訊視窗。 只有一個資訊視窗可能會顯示一次。

您可透過實作自訂 [資訊] 視窗[GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)介面。 此介面上有兩個重要的方法：

-  `public View GetInfoWindow(Marker marker)` &ndash; 這個方法會呼叫以取得標記的自訂資訊視窗。 如果它傳回`null`，則會使用預設視窗呈現。 如果這個方法傳回的檢視，該檢視將會放在資訊視窗框架內。

-  `public View GetInfoContents(Marker marker)` &ndash; 將只會呼叫這個方法，如果 GetInfoWindow 傳回`null`。 這個方法可以傳回`null`值，但前提是要使用的資訊視窗內容的預設轉譯。 否則，此方法應傳回的資訊視窗內容的檢視。

[資訊] 視窗是即時檢視-Android 會改為將檢視轉換成靜態的點陣圖，並在影像上顯示的名稱。 這表示，資訊視窗無法回應任何觸控事件或筆勢，也不將它自動自行更新。 若要更新的資訊視窗，就必須呼叫[GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下圖顯示一些部分自訂的資訊視窗的範例。 在左側的映像具有其自訂，而右邊的映像有其視窗的內容和自訂具有圓角的內容：

![墨爾本，包括圖示和母體的範例標記視窗。 右側視窗具有圓角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

不同於標記，識別在地圖上的特定位置，請[GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay)是用來識別的位置或在地圖上的區域集合的映像。

##### <a name="adding-a-groundoverlay"></a>新增 GroundOverlay

加入對應的地面覆疊是類似於將標記加入至對應。 首先， [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions)建立物件。 然後此物件會做為參數傳遞[ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions))方法，它會傳回`GroundOverlay`物件。 此程式碼片段是地上覆疊加入地圖中的範例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

下列螢幕擷取畫面會顯示此重疊在地圖上：

[![範例對應北極熊的重疊映像](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>線條、 圓形和多邊形

有三種可以新增至對應的幾何數據的簡單類型：

-  **聚合線條**-這是一系列連接的直線線段。 它可以將標記在地圖上的路徑，或建立幾何形狀。

-  **圓形**-這會在地圖上繪製圓形。

-  **多邊形**-這是一個封閉的形狀來標示地圖上的區域。


##### <a name="polylines"></a>聚合線條

A[聚合線條](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline)是一份連續`LatLng`物件會指定每個直線線段的頂點。 聚合線條由第一個建立`PolylineOptions`物件，然後將加入的點。 `PolylineOption`物件會傳遞至`GoogleMap`藉由呼叫物件`AddPolyline`方法。

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>圓形

圓形會建立第一個具現化[CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions)物件會在 metres 中指定的中心和圓形的半徑。 藉由呼叫在地圖上繪製圓形[GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))。
下列程式碼片段顯示如何畫一個圓圈：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>多邊形

`Polygon`類似於`Polyline`s，但在不開啟結束。 `Polygon`s 是封閉的循環，而且已填入其內部。
`Polygon`建立以完全相同的方式`Polyline`，除了[GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))叫用方法。

不同於`Polyline`、`Polygon`自我結尾。 多邊形會關閉由`AddPolygon`方法繪製一條線連接的第一個和最後一個點。 下列程式碼片段會建立透過上述程式碼片段中的相同位置的實心矩形`Polyline`範例。

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```


## <a name="responding-to-user-events"></a>回應使用者事件

有三種類型的使用者可能會發生對應的互動：

-  **標記按一下**-使用者按一下標記。

-  **標記拖曳**-使用者長時間-按下 mparger

-  **資訊視窗中按一下** -使用者已按一下資訊視窗上。

每個事件將在下面詳細討論。


### <a name="marker-click-events"></a>按一下 [事件] 標記

`MarkerClicked`在使用者點選標記上時，會引發事件。 此事件會接受`GoogleMap.MarkerClickEventArgs`物件做為參數。 這個類別包含兩個屬性：

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; 這個屬性應該設定為`true`表示事件處理常式已取用的事件。 如果此值設為`false`則預設行為將會發生除了事件處理常式的自訂行為。

-  `Marker` &ndash; 這個屬性是引發標記的參考`MarkerClick`事件。


此程式碼片段舉例示範`MarkerClick`，會在地圖上的新位置來變更觀景窗位置：

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);
    
        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>標記拖曳事件

當使用者想要將標記拖曳時，會引發這個事件。 根據預設，標記不是可拖曳。 標記可以設定為可拖曳 splittunneling`Marker.Draggable`屬性，以`true`或叫用`MarkerOptions.Draggable`方法`true`做為參數。

若要拖曳標記，使用者必須先長時間-按一下標記，然後他們的手指必須保持在地圖上。 當使用者手指拖曳畫面上時，將會移動標記。 當使用者手指上拿起在螢幕中時，標記會留在原處。

下列清單將描述各種事件，就會引發可拖曳的標記：

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; 當使用者第一次將標記時，會引發這個事件。

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; 當被拖曳的標記，則會引發這個事件。

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; 當使用者已完成時，會引發這個事件拖曳標記。

每個`EventArgs`包含單一屬性，以呼叫`P0`也就是參考`Marker`被拖曳的物件。


### <a name="info-window-click-events"></a>資訊視窗中按一下 事件

只有一個 [資訊] 視窗可以顯示一次。 當使用者按一下圖中的資訊視窗中時，將會引發的 map 物件`InfoWindowClick`事件。 下列程式碼片段示範如何連接至事件處理常式：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

請記住，[資訊] 視窗是靜態`View`這會轉譯為影像地圖上。 任何的小工具，例如按鈕、 核取方塊或位於 [資訊] 視窗內的文字檢視會惰性，而且無法回應任何整數類資料的使用者活動。


## <a name="related-links"></a>相關連結

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play 服務](https://developers.google.com/android/guides/overview)
- [Google 對應 Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play 服務 APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [使用程式庫](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element)
