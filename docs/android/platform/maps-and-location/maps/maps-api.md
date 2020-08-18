---
title: 在您的應用程式中使用 Google Maps API
description: 如何在您的 Xamarin Android 應用程式中執行 Google Maps API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 2779019fff1354b18d285775daf224ffc1402fd4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571359"
---
# <a name="using-the-google-maps-api-in-your-application"></a>在您的應用程式中使用 Google Maps API

使用 Maps 應用程式很好，但有時候您會想要在應用程式中直接包含對應。 除了內建的 maps 應用程式之外，Google 也提供 [適用于 Android 的原生對應 API](https://developers.google.com/maps/documentation/android-sdk/intro)。
Maps API 適用于您想要更充分掌控對應體驗的案例。 Maps API 可能的事項包括：

- 以程式設計方式變更地圖的觀點。
- 新增和自訂標記。
- 標注具有重迭的地圖。

不同于現已淘汰的 Google Maps Android API v1，Google Maps Android API v2 是 [Google Play Services](https://developers.google.com/android/guides/overview)的一部分。
Xamarin. Android 應用程式必須符合某些必要的必要條件，才能使用 Google Maps Android API。

## <a name="google-maps-api-prerequisites"></a>Google Maps API 必要條件

您必須採取幾個步驟，才能使用 Maps API，包括：

- [取得地圖服務 API 金鑰](#obtain-maps-key)
- [安裝 Google Play Services SDK](#install-gps-sdk)
- [從 NuGet 安裝 >xamarin.googleplayservices.base Maps 套件](#install-gpsmaps-nuget)
- [指定必要的許可權](#declare-permissions)
- [（選擇性）使用 Google Api 建立模擬器](#create-emulator-with-google-api)

### <a name="obtain-a-google-maps-api-key"></a><a name="obtain-maps-key"></a>取得 Google Maps API 金鑰

第一個步驟是取得 Google Maps API 金鑰 (請注意，您無法重複使用舊版 Google Maps v1 API) 的 API 金鑰。 如需如何取得及使用適用于 Xamarin 的 API 金鑰的詳細資訊，請參閱 [取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

### <a name="install-the-google-play-services-sdk"></a><a name="install-gps-sdk"></a> 安裝 Google Play Services SDK

Google Play Services 是 Google 提供的技術，可讓 Android 應用程式利用各種 Google 功能，例如 Google +、應用程式內結帳和 Maps。 這些功能可在 Android 裝置上以背景服務的形式存取，這些裝置包含在 [GOOGLE PLAY SERVICES APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)中。

Android 應用程式會透過 Google Play Services 用戶端程式庫與 Google Play Services 互動。 此程式庫包含個別服務（例如地圖）的介面和類別。 下圖顯示 Android 應用程式與 Google Play Services 之間的關聯性：

![說明更新 Google Play Services APK Google Play 商店的圖表](maps-api-images/play-services-diagram.png)

Android Maps API 是 Google Play Services 的一部分來提供。
在 Xamarin Android 應用程式可以使用 Maps API 之前，您必須使用 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md)來安裝 Google Play Services SDK。 下列螢幕擷取畫面顯示 Android SDK 管理員可以在何處找到 Google Play services 用戶端：

![Google Play Services 會出現在 Android SDK 管理員的 [額外專案] 下](maps-api-images/image01.png)

> [!NOTE]
> Google Play services APK 是可能不會出現在所有裝置上的授權產品。 如果未安裝，Google Maps 將無法在裝置上運作。

### <a name="install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget"></a> 從 NuGet 安裝 >xamarin.googleplayservices.base Maps 套件

[>xamarin.googleplayservices.base maps 套件](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含適用于 GOOGLE PLAY SERVICES Maps API 的 Xamarin. Android 系結。
若要加入 Google Play Services 對應套件，請以滑鼠右鍵按一下方案總管中專案的 [ **參考** ] 資料夾，然後按一下 [ **管理 NuGet 套件 ...**]：

![顯示 [參考] 下的 [管理 NuGet 套件] 內容功能表項目方案總管](maps-api-images/image02.png)

這會開啟 **NuGet 封裝管理員**。 按一下 **[流覽]** ，然後在 [搜尋] 欄位中輸入 **Xamarin Google Play Services Maps** 。 選取 [ **>xamarin.googleplayservices.base** ]，然後按一下 [ **安裝**]。  (如果先前已安裝此套件，請按一下 [ **更新**]。 ) ：

[![已選取 >xamarin.googleplayservices.base 套件的 NuGet 封裝管理員](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

請注意，也會安裝下列相依性套件：

- **>xamarin.googleplayservices.base 基底**
- **>xamarin.googleplayservices.base**
- **>xamarin.googleplayservices.base**

### <a name="specify-the-required-permissions"></a><a name="declare-permissions"></a> 指定必要的許可權

應用程式必須識別硬體和許可權需求，才能使用 Google Maps API。  Google Play Services SDK 會自動授與某些許可權，因此開發人員不需要明確地將它們新增至 **AndroidManfest.XML**：

- **存取網路狀態** &ndash; Maps API 必須能夠檢查是否可以下載地圖底圖。

- **網際網路存取** &ndash; 需要網際網路存取，才能下載地圖底圖，並與 Google Play 伺服器進行通訊以進行 API 存取。

您必須在 Google Maps Android API 的 **AndroidManifest.XML** 中指定下列許可權和功能：

- **OPENGL ES v2** &ndash; 應用程式必須宣告 OpenGL ES v2 的需求。

- **Google MAPS API 金鑰** &ndash; API 金鑰是用來確認應用程式已註冊，且已獲授權可使用 Google Play Services。 如需此金鑰的詳細資訊，請參閱 [取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) 。

- **要求舊版 APACHE HTTP 用戶端** &ndash; 以 Android 9.0 (API 層級 28) 或以上版本為目標的應用程式，必須指定舊版 Apache HTTP 用戶端是要使用的選擇性程式庫。

- **存取 Google Web 服務** &ndash; 應用程式需要存取 Google web 服務的許可權，以存取 Android Maps API。

- **Google Play Services 通知** &ndash; 的許可權必須授與應用程式從 Google Play Services 接收遠端通知的許可權。

- **存取位置提供者** &ndash; 這些是選擇性的許可權。
   它們可讓 `GoogleMap` 類別在地圖上顯示裝置的位置。

此外，Android 9 已從 bootclasspath 移除 Apache HTTP 用戶端程式庫，因此以 API 28 或更高版本為目標的應用程式無法使用該程式庫。 您必須將下列程式程式碼新增至 `application` **AndroidManifest.xml** 檔案的節點，才能在以 API 28 或更高版本為目標的應用程式中繼續使用 Apache HTTP 用戶端：

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> 舊版 Google Play SDK 需要應用程式來要求 `WRITE_EXTERNAL_STORAGE` 許可權。 Google Play Services 最近的 Xamarin 系結不再需要這項需求。

下列程式碼片段是必須新增至 **AndroidManifest.XML**的設定範例：

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
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

除了要求 **AndroidManifest.XML**許可權之外，應用程式也必須針對和許可權執行執行時間許可權檢查  `ACCESS_COARSE_LOCATION` `ACCESS_FINE_LOCATION` 。 如需執行執行時間許可權檢查的詳細資訊，請參閱「 [Xamarin Android 許可權](~/android/app-fundamentals/permissions.md) 指南」。

### <a name="create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api"></a>使用 Google Api 建立模擬器

如果未安裝具有 Google Play 服務的實體 Android 裝置，則可以建立用於開發的模擬器映射。 如需詳細資訊，請參閱 [裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。

## <a name="the-googlemap-class"></a>GoogleMap 類別

滿足必要條件之後，就可以開始開發應用程式，並使用 Android Maps API。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)類別是 Xamarin android 應用程式將用來顯示 Google Maps for android 並與其互動的主要 API。 此類別具有下列責任：

- 與 Google Play 服務互動，以授權應用程式使用 Google web 服務。

- 下載、快取及顯示地圖底圖。

- 將 UI 控制項（例如平移和縮放）顯示給使用者。

- 在地圖上繪製標記和幾何形狀。

`GoogleMap`會以下列兩種方式之一新增至活動：

- **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) 是做為物件主機的特製化片段 `GoogleMap` 。 `MapFragment`需要 ANDROID API 層級12或更高版本。
   較舊版本的 Android 可以使用 [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南將著重于使用 `MapFragment` 類別。

- **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) 是特製化的 View 子類別，可作為物件的主機 `GoogleMap` 。 此類別的使用者必須將所有的活動生命週期方法轉寄給 `MapView` 類別。

每個容器都會公開一個 `Map` 屬性，以傳回的實例 `GoogleMap` 。 應將喜好設定提供給 [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) 類別，因為它是較簡單的 API，可減少開發人員必須手動執行的未定案程式碼數量。

### <a name="adding-a-mapfragment-to-an-activity"></a>將 MapFragment 新增至活動

下列螢幕擷取畫面是簡單的範例 `MapFragment` ：

[![顯示 Google 地圖片段的裝置螢幕擷取畫面](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

類似于其他片段類別，有兩種方式可將加入 `MapFragment` 至活動：

- **以宣告方式** - `MapFragment` 可透過活動的 XML 配置檔加入。 下列 XML 程式碼片段顯示如何使用元素的範例 `fragment` ：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **以** 程式設計方式- `MapFragment` 可以使用方法以程式設計方式具現化 [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) ，然後再新增至活動。 此程式碼片段顯示將物件具現化 `MapFragment` 並加入至活動的最簡單方式：

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    您可以藉 `MapFragment` 由將 [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) 物件傳遞給來設定物件 `NewInstance` 。 這將在本指南稍後的 [GoogleMap 屬性](#googlemap_object) 一節中討論。

`MapFragment.GetMapAsync`方法是用來初始化由片段裝載的，並取得所裝載之 [`GoogleMap`](#googlemap_object) map 物件的參考 `MapFragment` 。 這個方法會採用可執行介面的物件 `IOnMapReadyCallback` 。

這個介面具有單一方法， `IMapReadyCallback.OnMapReady(MapFragment map)` 當應用程式可以與物件互動時，就會叫用此方法 `GoogleMap` 。 下列程式碼片段說明 Android 活動如何初始化 `MapFragment` 和執行 `IOnMapReadyCallback` 介面：

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

Google Maps API 提供五種不同的對應類型：

- **Normal** -這是預設的對應類型。 它會顯示道路和重要的自然功能，以及一些感興趣的點 (例如建築物和橋接器) 。

- **附屬** -此地圖顯示衛星攝影。

- **混合** 式-此地圖顯示衛星攝影和藍圖。

- **地形** -這主要是以一些道路來說明 topographical 的功能。

- **無** ：此對應不會載入任何圖格，而是會轉譯為空的方格。

下圖顯示三種不同類型的地圖，從左至右 (標準、混合式地形) ：

[![三個地圖範例螢幕擷取畫面：標準、混合式和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType`屬性是用來設定或變更要顯示的地圖類型。 下列程式碼片段說明如何顯示附屬地圖。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="googlemap-properties"></a><a name="googlemap_object"></a>GoogleMap 屬性

`GoogleMap` 定義數個屬性，可控制地圖的功能和外觀。 設定初始狀態的其中一種方式 `GoogleMap` 是在建立時傳遞 [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) 物件 `MapFragment` 。 下列程式碼片段是在建立時使用物件的其中一個範例 `GoogleMapOptions` `MapFragment` ：

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

設定的另一種方式 `GoogleMap` 是在 map 物件的 [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) 上操作屬性。 下一個程式碼範例顯示如何設定 `GoogleMap` ，以顯示縮放控制項和羅盤：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>與 GoogleMap 互動

Android Maps API 提供的 Api 可讓活動變更視點、新增標記、放置自訂覆迭或繪製幾何形狀。 本節將討論如何在 Xamarin 中完成這些工作。

### <a name="changing-the-viewpoint"></a>變更視點

地圖會根據 Mercator 投射，模型化為螢幕上的平面平面。 地圖視圖是指在此平面上直接查看的 *相機* 。 您可以藉由變更位置、縮放、傾斜和方位來控制相機的位置。 [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)類別可用來移動相機位置。 `CameraUpdate` 物件不會直接具現化，而 Maps API 會提供 [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) 類別。

一旦 `CameraUpdate` 建立物件之後，就會將它當作參數傳遞至 [GoogleMap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) 或 [GoogleMap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) 方法。 方法會在 `MoveCamera` `AnimateCamera` 方法提供順暢、動畫的轉換時，立即更新對應。

此程式碼片段是一個簡單的範例，說明如何使用 `CameraUpdateFactory` 建立 `CameraUpdate` ，將地圖的縮放層級遞增一個縮放層級：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Maps API 提供的 [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) 會匯總相機位置的所有可能值。 此類別的實例可提供給將會傳回物件的[CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法。 `CameraUpdate` Maps API 也包含可提供流暢 API 來建立物件的[CameraPosition Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)類別。 `CameraPosition`
下列程式碼片段顯示從建立的範例 `CameraUpdate` `CameraPosition` ，並使用它來變更上的相機位置 `GoogleMap` ：

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

在先前的程式碼片段中，地圖上的特定位置會以 [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) 類別表示。 縮放層級會設定為18，這是 Google Maps 所使用之縮放的任意量值。 「方向」是指順向北的羅盤測量。 [傾斜] 屬性會控制視圖角度，並從垂直指定25度的角度。 下列螢幕擷取畫面顯示 `GoogleMap` 執行上述程式碼之後的：

[![範例 Google 地圖顯示具有傾斜視圖角度的指定位置](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>在地圖上繪圖

Android Maps API 提供 API 在地圖上繪製下列專案：

- **標記** -這些是用來識別地圖上單一位置的特殊圖示。

- 覆**迭-這**是可用來識別地圖上位置或區域集合的影像。

- **線條、多邊形和圓形** -這些都是允許活動將圖形新增至地圖的 api。

#### <a name="markers"></a>標記

Maps API 提供 [標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) 類別，可封裝地圖上單一位置的所有相關資料。 標記類別預設會使用 Google Maps 提供的標準圖示。 您可以自訂標記的外觀，並回應使用者按下的動作。

##### <a name="adding-a-marker"></a>新增標記

若要將標記加入至地圖，必須建立新的 [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) 物件，然後在實例上呼叫 [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) 方法 `GoogleMap` 。 這個方法會傳回 [標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) 物件。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

當使用者按標記時，標記的標題將會顯示在 *資訊視窗* 中。 下列螢幕擷取畫面顯示此標記的樣子：

[![範例 Google 地圖與標記，以及用於 Vimy 凸緣的資訊視窗](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>自訂標記

將 `MarkerOptions.InvokeIcon` 標記新增至地圖時，可以藉由呼叫方法來自訂標記所使用的圖示。
這個方法會採用 [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) 物件，其中包含呈現圖示所需的資料。 [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)類別提供一些 helper 方法來簡化的建立 `BitmapDescriptor` 。 下列清單介紹其中一些方法：

- `DefaultMarker(float colour)`&ndash;使用預設的 Google Maps 標記，但變更色彩。

- `FromAsset(string assetName)`&ndash;在 [資產] 資料夾中，使用來自指定檔案的自訂圖示。

- `FromBitmap(Bitmap image)`&ndash;使用指定的點陣圖作為圖示。

- `FromFile(string fileName)`&ndash;從位於指定路徑的檔案建立自訂圖示。

- `FromResource(int resourceId)`&ndash;從指定的資源建立自訂圖示。

下列程式碼片段顯示建立青色顏色預設標記的範例：

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

#### <a name="info-windows"></a>資訊視窗

[*資訊] 視窗*是特殊的視窗，可在使用者按一下特定標記時，向使用者顯示資訊。 [資訊] 視窗預設會顯示標記標題的內容。 如果尚未指派標題，則不會顯示任何資訊視窗。 一次只會顯示一個資訊視窗。

您可以藉由執行 [GoogleMap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) 介面來自訂資訊視窗。 這個介面有兩個重要的方法：

- `public View GetInfoWindow(Marker marker)`&ndash;呼叫這個方法以取得標記的自訂資訊視窗。 如果傳回 `null` ，則會使用預設視窗轉譯。 如果這個方法傳回視圖，則該視圖會放置在資訊視窗框架內。

- `public View GetInfoContents(Marker marker)`&ndash;只有在 GetInfoWindow 傳回時，才會呼叫這個方法 `null` 。 `null`如果要使用資訊視窗內容的預設轉譯，則這個方法會傳回值。 否則，此方法應該會傳回包含資訊視窗內容的視圖。

資訊視窗不是即時視圖，而是 Android 會將視圖轉換成靜態點陣圖，並顯示在影像上。 這表示資訊視窗無法回應任何觸控事件或手勢，也不會自動自行更新。 若要更新資訊視窗，必須呼叫 [GoogleMap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) 方法。

下圖顯示一些自訂資訊視窗的一些範例。 左邊的影像會自訂其內容，而右邊的影像會以圓角自訂其視窗和內容：

![適用于墨爾本的範例標記視窗，包括圖示和人口。 右邊的視窗有圓角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

不同于在地圖上識別特定位置的標記， [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) 是用來識別位置集合或地圖上某個區域的影像。

##### <a name="adding-a-groundoverlay"></a>新增 GroundOverlay

將地面重迭新增至地圖類似于將標記新增至地圖。 首先，會建立 [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) 物件。 然後，這個物件會以參數的形式傳遞給 [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) 方法，以傳回 `GroundOverlay` 物件。 此程式碼片段是將地面重迭新增至地圖的範例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

下列螢幕擷取畫面顯示地圖上的這項覆蓋：

[![具有極座標重迭影像的範例地圖](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>線條、圓形和多邊形

有三種可新增至地圖的簡單類型幾何圖：

- 聚合**線條-這**是一連串連接的線段。 它可以標示地圖上的路徑，或建立幾何形狀。

- **圓形** -這會在地圖上繪製圓形。

- **多邊形** -這是標示地圖上區域的封閉圖形。

##### <a name="polylines"></a>折線

聚合線條是連續物件的 [清單，](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) `LatLng` 可指定每個線段的頂點。 建立聚合線條的方式是先建立 `PolylineOptions` 物件，並在其中加入點。 `PolylineOption`然後藉由呼叫方法，將物件傳遞給 `GoogleMap` 物件 `AddPolyline` 。

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

圓形的建立方式是先具現化 [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) 物件，此物件將會指定 metres 中圓形的中心和半徑。 藉由呼叫 [GoogleMap AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))，在地圖上繪製圓形。
下列程式碼片段顯示如何繪製圓形：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>多邊形

`Polygon`與 `Polyline` s 類似，不過它們並未開啟。 `Polygon`s 是封閉的迴圈，其內部已填滿。
`Polygon`會以與相同的相同方式來建立 `Polyline` ，但所叫用的 [GoogleMap AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) 方法除外。

不同于 `Polyline` ， `Polygon` 會自行關閉。 藉 `AddPolygon` 由繪製連接第一個和最後一個點的線條，方法會關閉多邊形。 下列程式碼片段會在與範例中先前的程式碼片段相同的區域上建立一個實心矩形 `Polyline` 。

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

有三種類型的互動：使用者可能具有地圖：

- **標記按一下** -使用者按一下標記。

- **標記拖曳** -使用者長按 mparger

- **資訊視窗點擊** -使用者已按一下資訊視窗。

以下將更詳細地討論每個事件。

### <a name="marker-click-events"></a>標記點擊事件

`MarkerClicked`當使用者按了標記時，就會引發事件。 此事件會接受 `GoogleMap.MarkerClickEventArgs` 物件做為參數。 此類別包含兩個屬性：

- `GoogleMap.MarkerClickEventArgs.Handled`&ndash;這個屬性應該設定為， `true` 以表示事件處理常式已使用事件。 如果設定為， `false` 則除了事件處理常式的自訂行為之外，也會發生預設行為。

- `Marker`&ndash;這個屬性是引發事件之標記的參考 `MarkerClick` 。

此程式碼片段顯示的範例 `MarkerClick` 會將相機位置變更為地圖上的新位置：

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

當使用者希望拖曳標記時，會引發此事件。 根據預設，標記無法拖曳。 您可以藉由將 `Marker.Draggable` 屬性設定為，或藉由以參數的方式 `true` `MarkerOptions.Draggable` 叫用方法，將標記設定為可拖曳 `true` 。

若要拖曳標記，使用者必須先在標記上按一下滑鼠右鍵，然後其手指必須留在地圖上。 當使用者的手指在螢幕上拖曳時，標記就會移動。 當使用者的手指離開螢幕時，標記就會保留在原處。

下列清單說明可拖曳標記將引發的各種事件：

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash;當使用者第一次拖曳標記時，就會引發此事件。

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`拖曳 &ndash;   標記時，會引發此事件。

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash;當使用者完成拖曳標記時，就會引發此事件。

每一個都 `EventArgs` 包含名為的單一屬性 `P0` ，它是要拖曳之物件的參考 `Marker` 。

### <a name="info-window-click-events"></a>資訊視窗點擊事件

一次只能顯示一個資訊視窗。 當使用者按一下地圖中的 [資訊] 視窗時，地圖物件將會引發 `InfoWindowClick` 事件。 下列程式碼片段顯示如何將處理常式連接到事件：

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

回想一下，資訊視窗是在 `View` 地圖上呈現為影像的靜態。 任何 widget （例如按鈕、核取方塊或放在資訊視窗內的文字流覽）都會惰性，而且無法回應其任何整數的使用者事件。

## <a name="related-links"></a>相關連結

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play 服務](https://developers.google.com/android/guides/overview)
- [Google Maps Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [使用程式庫](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [使用-功能](https://developer.android.com/guide/topics/manifest/uses-feature-element)
