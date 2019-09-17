---
title: 在您的應用程式中使用 Google Maps API
description: 如何在您的 Xamarin Android 應用程式中執行 Google Maps API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: a640e1d6accdfa9184a29127bf4b3c7eeefe9b64
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761835"
---
# <a name="using-the-google-maps-api-in-your-application"></a>在您的應用程式中使用 Google Maps API

使用 Maps 應用程式很棒，但有時您想要直接在應用程式中包含對應。 除了內建 maps 應用程式以外，Google 也提供[適用于 Android 的原生對應 API](https://developers.google.com/maps/documentation/android-sdk/intro)。
Maps API 適用于您想要更充分掌控對應體驗的情況。 Maps API 可能的事項包括：

- 以程式設計方式變更地圖的觀點。
- 加入和自訂標記。
- 使用重迭標注地圖。

不同于現已淘汰的 Google Maps Android API v1，Google Maps Android API v2 是[Google Play Services](https://developers.google.com/android/guides/overview)的一部分。
Xamarin Android 應用程式必須符合一些必要的必要條件，才能使用 Google Maps Android API。

## <a name="google-maps-api-prerequisites"></a>Google Maps API 必要條件

您必須先執行幾個步驟，才能使用 Maps API，包括：

- [取得 Maps API 金鑰](#obtain-maps-key)
- [安裝 Google Play Services SDK](#install-gps-sdk)
- [從 NuGet 安裝 GooglePlayServices. Maps 套件](#install-gpsmaps-nuget)
- [指定所需的許可權](#declare-permissions)
- [（選擇性）使用 Google Api 建立模擬器](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />取得 Google Maps API 金鑰

第一個步驟是取得 Google Maps API 金鑰（請注意，您無法從舊版 Google Maps v1 API 重複使用 API 金鑰）。 如需如何透過 Xamarin 取得和使用 API 金鑰的詳細資訊，請參閱[取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" />安裝 Google Play Services SDK

Google Play Services 是 Google 的一項技術，可讓 Android 應用程式利用各種 Google 功能，例如 Google +、應用程式內計費和地圖。 這些功能可在 Android 裝置上以背景服務的形式存取，其包含在[GOOGLE PLAY SERVICES APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)中。

Android 應用程式會透過 Google Play Services 用戶端程式庫與 Google Play Services 互動。 此程式庫包含個別服務的介面和類別，例如 Maps。 下圖顯示 Android 應用程式和 Google Play Services 之間的關聯性：

![說明 Google Play 商店更新 Google Play Services APK 的圖表](maps-api-images/play-services-diagram.png)

Android Maps API 是 Google Play Services 的一部分提供。
在 Xamarin Android 應用程式可以使用 Maps API 之前，必須先使用[Android SDK 管理員](~/android/get-started/installation/android-sdk.md)來安裝 Google Play Services SDK。 下列螢幕擷取畫面顯示 Android SDK Manager 中可找到 Google Play 服務用戶端的位置：

![Google Play Services 會出現在 [Android SDK 管理員] 的 [額外專案] 底下](maps-api-images/image01.png)

> [!NOTE]
> Google Play services APK 是一種授權產品，可能不會出現在所有裝置上。 如果未安裝，則 Google Maps 將無法在裝置上使用。

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" />從 NuGet 安裝 GooglePlayServices. Maps 套件

[GooglePlayServices 封裝](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含適用于 GOOGLE PLAY SERVICES Maps API 的 Xamarin. Android 系結。
若要加入 Google Play Services 對應套件，請在方案總管中以滑鼠右鍵按一下專案的 [**參考**] 資料夾，然後按一下 [**管理 NuGet 套件 ...** ]：

![顯示 [參考] 底下的 [管理 NuGet 套件] 內容功能表項目的方案總管](maps-api-images/image02.png)

這會開啟 [ **NuGet 套件管理員**]。 按一下 **[流覽]** ，然後在 [搜尋] 欄位中輸入**Xamarin Google Play Services 對應**。 選取 [ **GooglePlayServices** ]，然後按一下 [**安裝**]。 （如果先前已安裝此套件，請按一下 [**更新**]）：

[![已選取 GooglePlayServices 的 NuGet 套件管理員套件](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

請注意，也會安裝下列相依性套件：

- **Xamarin.GooglePlayServices.Base**
- **Xamarin.GooglePlayServices.Basement**
- **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" />指定所需的許可權

應用程式必須識別硬體和許可權需求，才能使用 Google Maps API。  某些許可權會由 Google Play Services SDK 自動授與，而開發人員不需要明確地將其新增至**AndroidManfest**：

- **存取網路狀態**&ndash; Maps API 必須能夠檢查是否可以下載地圖底圖。

- **網際網路存取**&ndash;若要下載地圖底圖並與 Google Play 伺服器通訊以進行 API 存取，則需要網際網路存取。

您必須在 Google Maps Android API 的**androidmanifest.xml**中指定下列許可權和功能：

- **OPENGL ES v2**&ndash;應用程式必須宣告 OpenGL ES v2 的需求。

- **Google MAPS API 金鑰**&ndash; API 金鑰是用來確認應用程式已註冊，並已獲授權使用 Google Play Services。 如需此金鑰的詳細資訊，請參閱[取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

- **要求舊版 APACHE HTTP 用戶端**&ndash;以 Android 9.0 （API 層級28）或更新版本為目標的應用程式，必須指定舊版 Apache HTTP 用戶端是選用的程式庫來使用。

- **存取 Google Web 服務**&ndash;應用程式需要有許可權，才能存取 Google 的 web 服務，以傳回 Android Maps API。

- **Google Play Services 通知的許可權**&ndash;應用程式必須被授與從 Google Play Services 接收遠端通知的許可權。

- **存取位置提供者**&ndash;這些是選擇性的許可權。
   它們可讓`GoogleMap`類別顯示裝置在地圖上的位置。

此外，Android 9 已從 bootclasspath 移除 Apache HTTP 用戶端程式庫，因此不適用於以 API 28 或更高版本為目標的應用程式。 您必須將下列這一行新增至`application` **androidmanifest.xml**的節點，才能繼續在以 API 28 或更高版本為目標的應用程式中使用 Apache HTTP 用戶端：

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> 非常舊的 Google Play SDK 版本需要應用程式來要求`WRITE_EXTERNAL_STORAGE`許可權。 Google Play Services 的最新 Xamarin 系結已不再需要這項需求。

下列程式碼片段是必須新增至**androidmanifest.xml**的設定範例：

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

除了要求**androidmanifest.xml**許可權，應用程式也必須針對`ACCESS_COARSE_LOCATION`和`ACCESS_FINE_LOCATION`許可權執行執行時間許可權檢查。 如需執行執行時間許可權檢查的詳細資訊，請參閱 < [Xamarin 許可權](~/android/app-fundamentals/permissions.md)指南。

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />使用 Google Api 建立模擬器

如果未安裝具有 Google Play 服務的實體 Android 裝置，則可以建立用於開發的模擬器映射。 如需詳細資訊，請參閱[Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)。

## <a name="the-googlemap-class"></a>GoogleMap 類別

滿足必要條件之後，就可以開始開發應用程式，並使用 Android Maps API。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)類別是 Xamarin android 應用程式將用來顯示及與適用于 Android 的 Google Maps 互動的主要 API。 此類別具有下列責任：

- 與 Google Play 服務互動，以向 Google web 服務授權應用程式。

- 下載、快取和顯示地圖底圖。

- 向使用者顯示 UI 控制項，例如 pan 和 zoom。

- 在地圖上繪製標記和幾何形狀。

`GoogleMap`會以下列兩種方式的其中一種新增至活動：

- **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)是專門做為`GoogleMap`物件主機的特定片段。 `MapFragment`需要 Android API 層級12或更高版本。
   較舊版本的 Android 可以使用[SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南將著重于使用`MapFragment`類別。

- **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView)是特殊的 View 子類別，可作為`GoogleMap`物件的主機。 此類別的使用者必須將所有活動生命週期方法轉送至`MapView`類別。

這些容器中的每一個`Map`都會公開一個會傳回實例`GoogleMap`的屬性。 喜好設定應該提供給[MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)類別，因為它是較簡單的 API，可減少開發人員必須手動執行的程式碼數量。

### <a name="adding-a-mapfragment-to-an-activity"></a>將 MapFragment 新增至活動

下列螢幕擷取畫面是簡單`MapFragment`的範例：

[![顯示 Google 地圖片段的裝置螢幕擷取畫面](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

類似于其他片段類別，有兩種方式可將加入`MapFragment`至活動：

- **以**宣告方式`MapFragment` -可以透過活動的 XML 配置檔案來新增。 下列 XML 程式碼片段顯示如何使用`fragment`元素的範例：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **以**程式設計`MapFragment`方式-可以使用[`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance())方法以程式設計方式具現化，然後加入至活動。 此程式碼片段顯示具現化`MapFragment`物件並新增至活動的最簡單方式：

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    您可以藉由將`MapFragment` [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)物件傳遞至`NewInstance`來設定物件。 這會在本指南稍後顯示的[GoogleMap 屬性](#googlemap_object)一節中討論。

方法是用來初始化由片段[`GoogleMap`](#googlemap_object)主控的，並取得所裝載之 map 物件`MapFragment`的參考。 `MapFragment.GetMapAsync` 這個方法會採用可執行`IOnMapReadyCallback`介面的物件。

這個介面具有單一方法， `IMapReadyCallback.OnMapReady(MapFragment map)`當應用程式可以`GoogleMap`與物件互動時，將會叫用它。 下列程式碼片段說明 Android 活動如何初始化`MapFragment`並`IOnMapReadyCallback`執行介面：

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

Google Maps API 提供五種不同類型的對應：

- **Normal** -這是預設的地圖類型。 它會顯示道路和重要自然功能，以及一些需要注意的點（例如大樓和橋接器）。

- **衛星**-此地圖會顯示附屬攝影。

- **混合**式-此地圖會顯示附屬攝影和道路地圖。

- **地形**-這主要會顯示一些道路的 topographical 功能。

- **無**-此對應不會載入任何磚，而是會呈現為空白方格。

下圖顯示三種不同類型的對應，從左至右（標準、混合式、地形）：

[![三個地圖範例螢幕擷取畫面：標準、混合式和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType`屬性可用來設定或變更要顯示的地圖類型。 下列程式碼片段顯示如何顯示衛星地圖。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a><a name="googlemap_object" />GoogleMap 屬性

`GoogleMap`定義數個可控制對應功能和外觀的屬性。 設定初始狀態的`GoogleMap`其中一種方式是在建立時傳遞[GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) `MapFragment`物件。 下列程式碼片段是在`GoogleMapOptions` `MapFragment`建立時使用物件的其中一個範例：

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

設定的另一`GoogleMap`種方式是在 map 物件的[UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings)上操作屬性。 下一個程式碼範例顯示如何設定`GoogleMap` ，以顯示縮放控制項和羅盤：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>與 GoogleMap 互動

Android Maps API 提供的 Api 可讓活動變更觀點、新增標記、放置自訂重迭或繪製幾何圖案。 本節將討論如何在 Xamarin 中完成這些工作。

### <a name="changing-the-viewpoint"></a>變更觀點

地圖會根據 Mercator 投影，以平面方式模型化在螢幕上。 [地圖] 視圖是指*攝影機*在此平面上直接向下看的。 您可以藉由變更位置、縮放、傾斜和軸承來控制相機的位置。 [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)類別是用來移動相機位置。 `CameraUpdate`物件不會直接具現化，而是 Maps API 會提供[CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)類別。

一旦建立物件之後，就會將它當做參數傳遞至 [GoogleMap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) 或[GoogleMap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate))方法。`CameraUpdate` 方法會立即更新對應，而方法`AnimateCamera`則提供平滑的動畫轉換。 `MoveCamera`

此程式碼片段是一個簡單的範例，說明如何使用`CameraUpdateFactory` `CameraUpdate`建立，將地圖的縮放比例增加一個縮放層級：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Maps API 提供[CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) ，其會匯總相機位置的所有可能值。 這個類別的實例可以提供給會`CameraUpdate`傳回物件的[CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法。 Maps API 也包含[CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)類別，可提供建立`CameraPosition`物件的 Fluent API。
下列程式碼片段顯示`CameraUpdate` `CameraPosition`從建立的範例，並使用它來變更上`GoogleMap`的相機位置：

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

在先前的程式碼片段中，地圖上的特定位置會以[LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng)類別表示。 縮放層級設定為18，這是 Google Maps 所使用的任意縮放量值。 軸承是從北順時針方向的羅盤測量。 [傾斜] 屬性會控制視圖角度，並指定從垂直角度的25度。 下列螢幕擷取畫面顯示執行`GoogleMap`上述程式碼之後的：

[![顯示具有傾斜角度之指定位置的 Google 地圖範例](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>在地圖上繪製

Android 地圖服務 API 提供 API，可在地圖上繪製下列專案：

- **標記**-這些是用來識別地圖上單一位置的特殊圖示。

- 重**迭-這**是可以用來識別地圖上的位置或區域集合的影像。

- **線條、多邊形和圓形**-這些是允許活動將圖形新增至地圖的 api。

#### <a name="markers"></a>Markers

Maps API 提供一個[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)類別，它會封裝地圖上單一位置的所有相關資料。 根據預設，標記類別會使用 Google Maps 所提供的標準圖示。 您可以自訂標記的外觀，以及回應使用者按下的動作。

##### <a name="adding-a-marker"></a>加入標記

若要將標記加入至對應，必須建立新的[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)物件，然後在`GoogleMap`實例上呼叫[AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法。 這個方法會傳回[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)物件。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

當使用者點擊標記時，標記的標題將會顯示在 [*資訊] 視窗*中。 下列螢幕擷取畫面顯示此標記看起來的樣子：

[![使用標記的範例 Google 地圖和 Vimy 凸緣的資訊視窗](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>自訂標記

將標記新增至地圖時，可以藉由呼叫`MarkerOptions.InvokeIcon`方法，自訂標記所使用的圖示。
這個方法會採用[BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor)物件，其中包含呈現圖示所需的資料。 [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)類別會提供一些 helper 方法，以簡化的建立`BitmapDescriptor`。 下列清單會介紹其中一些方法：

- `DefaultMarker(float colour)`&ndash;使用預設的 [Google Maps] 標記，但變更色彩。

- `FromAsset(string assetName)`&ndash;使用 [資產] 資料夾中指定檔案的自訂圖示。

- `FromBitmap(Bitmap image)`&ndash;使用指定的點陣圖作為圖示。

- `FromFile(string fileName)`&ndash;在指定路徑的檔案中建立自訂圖示。

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

[*資訊] 視窗*是特殊的視窗，會在使用者按一下特定標記時，快顯視窗顯示資訊。 根據預設，[資訊] 視窗會顯示標記標題的內容。 如果尚未指派標題，則不會顯示任何資訊視窗。 一次只會顯示一個資訊視窗。

您可以藉由執行[GoogleMap IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)介面來自訂 [資訊] 視窗。 此介面上有兩個重要的方法：

- `public View GetInfoWindow(Marker marker)`&ndash;呼叫這個方法以取得標記的自訂資訊視窗。 如果傳回，則會使用預設的視窗轉譯。 `null` 如果這個方法傳回視圖，則該視圖會放在資訊視窗框架內。

- `public View GetInfoContents(Marker marker)`只有在 GetInfoWindow 傳回時，才會呼叫這個方法。 `null` &ndash; 如果要使用資訊視窗`null`內容的預設呈現，這個方法會傳回值。 否則，這個方法應該會傳回具有 [資訊] 視窗內容的 View。

[資訊] 視窗不是即時觀看-相反地，Android 會將此視圖轉換成靜態點陣圖，並顯示在影像上。 這表示資訊視窗無法回應任何觸控事件或筆勢，也不會自動自行更新。 若要更新資訊視窗，必須呼叫[GoogleMap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下圖顯示一些自訂資訊視窗的範例。 左邊的影像已自訂其內容，而右邊的影像具有以圓角自訂的視窗和內容：

![墨爾本的範例標記視窗，包括圖示和填入。 右視窗具有圓角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

不同于識別地圖上特定位置的標記， [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay)是用來識別位置集合或地圖上某個區域的影像。

##### <a name="adding-a-groundoverlay"></a>新增 GroundOverlay

將地面重迭新增至地圖，類似于將標記新增至地圖。 首先，會建立[GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions)物件。 然後，這個物件會當做參數傳遞給[`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions))方法，這會`GroundOverlay`傳回物件。 此程式碼片段是將地面重迭新增至地圖的範例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

下列螢幕擷取畫面顯示地圖上的此重迭：

[![具有極座標之商店影像的範例對應](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>線條、圓形和多邊形

有三種簡單類型的幾何圖形可新增至地圖：

- **折線**-這是一系列連接的線段。 它可以標示地圖上的路徑，或建立幾何形狀。

- **Circle** -這會在地圖上繪製一個圓圈。

- **多邊形**-這是在地圖上標示區域的封閉圖形。

##### <a name="polylines"></a>折線

「[折線](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline)」是連續`LatLng`的物件清單，可指定每個線段的頂點。 建立一條折線的方式是先`PolylineOptions`建立物件，並將點加入其中。 然後藉由呼叫`GoogleMap` `AddPolyline`方法，將物件傳遞至物件。`PolylineOption`

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>圓圈

建立圓形的方式是先具現化[CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions)物件，它會在 metres 中指定圓形的中心和半徑。 藉由呼叫[GoogleMap AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))，在地圖上繪製圓形。
下列程式碼片段顯示如何繪製圓形：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>多邊形

`Polygon`與類似`Polyline`，但它們並未開啟。 `Polygon`s 是封閉式迴圈，其內部已填滿。
`Polygon`會以與相同的方式建立`Polyline`，但叫用的[GoogleMap AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))方法除外。

不同于`Polyline` `Polygon` ，會自我關閉。 `AddPolygon`方法會藉由繪製連接第一個和最後一個點的線條來關閉多邊形。 下列程式碼片段會在與`Polyline`範例中的先前程式碼片段相同的區域上建立實心矩形。

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

使用者可能有對應的互動類型有三種：

- **標記按一下**-使用者按一下標記。

- **標記拖曳**-使用者長時間按下 mparger

- **資訊視窗中按一下** -使用者已按一下資訊視窗上。

下面將更詳細地討論每個事件。

### <a name="marker-click-events"></a>標記按一下事件

當`MarkerClicked`使用者點擊標記時，就會引發事件。 這個事件會接受`GoogleMap.MarkerClickEventArgs`物件做為參數。 此類別包含兩個屬性：

- `GoogleMap.MarkerClickEventArgs.Handled`這個屬性應該設定為`true` ，以表示事件處理常式已耗用事件。 &ndash; 如果這個設定為， `false`則除了事件處理常式的自訂行為之外，也會發生預設行為。

- `Marker`此屬性是`MarkerClick`引發事件之標記的參考。 &ndash;

此程式碼片段顯示的範例`MarkerClick`會將相機位置變更為地圖上的新位置：

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

當使用者希望拖曳標記時，就會引發這個事件。 根據預設，標記無法拖曳。 藉由將`Marker.Draggable`屬性設定為， `true`或`MarkerOptions.Draggable`叫用具有`true`做為參數的方法，即可將標記設定為可拖曳。

若要拖曳標記，使用者必須先長時間按一下標記，然後其手指必須留在地圖上。 當使用者的手指拖曳至螢幕上時，標記將會移動。 當使用者的手指在螢幕上脫落時，標記就會保留在原處。

下列清單說明可拖曳標記會引發的各種事件：

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash;當使用者第一次拖曳標記時，就會引發這個事件。

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`&ndash;這個事件會在拖曳標記時引發。

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash;當使用者完成拖曳標記時，就會引發這個事件。

中的每一個都`P0` `Marker`包含名為的單一屬性，這是所要拖曳之物件的參考。 `EventArgs`

### <a name="info-window-click-events"></a>資訊視窗點擊事件

一次只能顯示一個資訊視窗。 當使用者按一下地圖中的資訊視窗時，map 物件將會引發`InfoWindowClick`事件。 下列程式碼片段顯示如何將處理常式與事件連接：

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

回想一下，資訊視窗是一個靜態`View` ，它會轉譯為地圖上的影像。 放在資訊視窗內的任何 widget （例如按鈕、核取方塊或文字視圖）都會惰性，而且無法回應其任何整數使用者事件。

## <a name="related-links"></a>相關連結

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Google Maps Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element)
