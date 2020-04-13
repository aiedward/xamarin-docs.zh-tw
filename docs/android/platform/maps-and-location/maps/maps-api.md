---
title: 在應用程式中使用 Google 地圖 API
description: 如何在 Xamarin.Android 應用程式中實現 Google 地圖 API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027156"
---
# <a name="using-the-google-maps-api-in-your-application"></a>在應用程式中使用 Google 地圖 API

使用 Map 應用程式很棒,但有時您希望將地圖直接包含在應用程式中。 除了內置地圖應用程式,谷歌還提供[一個本機映射API的Android。](https://developers.google.com/maps/documentation/android-sdk/intro)
地圖 API 適用於您希望對映射體驗保持更多控制的情況。 地圖 API 可能實現::

- 以程式設計方式更改地圖的視點。
- 添加和自定義標記。
- 使用疊加對地圖進行加號。

與現在被棄用的谷歌地圖Android API v1不同,谷歌地圖Android API v2是[谷歌Play服務的](https://developers.google.com/android/guides/overview)一部分。
Xamarin.Android 應用程式必須滿足一些強制性的先決條件,然後才能使用 Google 地圖 Android API。

## <a name="google-maps-api-prerequisites"></a>谷歌地圖 API 先決條件

在使用地圖 API 之前,需要執行幾個步驟,包括:

- [取得地圖 API 金鑰](#obtain-maps-key)
- [安裝谷歌播放服務 SDK](#install-gps-sdk)
- [安裝 Xamarin.GooglePlay 服務.來自 NuGet 的地圖套件](#install-gpsmaps-nuget)
- [指定需要的權限](#declare-permissions)
- [使用 Google API 建立模擬程式](#create-emulator-with-google-api)

### <a name="obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />取得谷歌地圖 API 金鑰

第一步是獲取 Google 地圖 API 金鑰(請注意,您不能重用舊版 Google 地圖 v1 API 中的 API 金鑰)。 有關如何取得與使用 Xamarin.Android 的 API 金鑰的資訊,請參閱[獲取 Google 地圖 API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

### <a name="install-the-google-play-services-sdk"></a><a name="install-gps-sdk" />安裝谷歌播放服務 SDK

谷歌Play服務是谷歌的一項技術,它允許Android應用程式利用谷歌的各種功能,如谷歌+,應用內計費和地圖。 這些功能可在Android設備上作為後台服務訪問,包含在[谷歌Play服務APK。](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)

Android應用程式通過谷歌Play服務用戶端庫與谷歌Play服務互動。 此庫包含各個服務(如地圖)的介面和類。 下圖顯示了 Android 應用程式和 Google Play 服務之間的關係:

![圖說明谷歌Play商店更新谷歌播放服務APK](maps-api-images/play-services-diagram.png)

Android 地圖 API 是作為 Google Play 服務的一部分提供的。
在 Xamarin.Android 應用程式可以使用地圖 API 之前,必須使用[Android SDK 管理器](~/android/get-started/installation/android-sdk.md)安裝 Google Play 服務 SDK。 以下螢幕截圖顯示了在 Android SDK 管理員中可以找到 Google Play 服務用戶端的位置:

![谷歌播放服務顯示在 Android SDK 管理器中的「附加」下](maps-api-images/image01.png)

> [!NOTE]
> Google Play 服務 APK 是一種許可產品,可能並不存在於所有設備上。 如果未安裝,則 Google 地圖將不適用於設備。

### <a name="install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" />安裝 Xamarin.GooglePlay 服務.來自 NuGet 的地圖套件

[Xamarin.GooglePlayServices.地圖包](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含 Google Play 服務地圖 API 的 Xamarin.Android 綁定。
要新增 Google Play 服務地圖包,請右鍵單擊解決方案資源管理員中的專案的**參考**資料夾,然後按下「**管理 NuGet 包...":**

![解決方案資源管理員在參考項目下顯示管理 NuGet 套件上下文選單項](maps-api-images/image02.png)

這會開啟**NuGet 套件管理員**。 點選 **「瀏覽」** 並在搜尋欄位中輸入**Xamarin Google Play 服務地圖**。 選擇**Xamarin.GooglePlay服務.地圖**,然後單擊 **「安裝**」。 (如果以前已安裝此包,請按下 **「更新**」:

[![NuGet 套件管理員與 Xamarin.GooglePlay 服務.地圖套件選擇](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

請注意,還安裝了以下依賴項包:

- **Xamarin.GooglePlay服務.基地**
- **Xamarin.GooglePlay服務**
- **Xamarin.GooglePlay服務.任務**

### <a name="specify-the-required-permissions"></a><a name="declare-permissions" />指定需要的權限

應用必須識別硬體和許可權要求才能使用 Google 地圖 API。  某些權限由 Google Play 服務 SDK 自動授予,開發人員無需將其顯式新增到**AndroidManfest.XML**:

- **存取網路狀態**&ndash;地圖 API 必須能夠檢查是否可以下載地圖磁貼。

- **互聯網接入**&ndash;是下載地圖磁貼並與 Google Play 伺服器進行 API 訪問所必需的。

以下權限和功能必須在 Android 地圖 Android API 的**Android 清單.XML**中指定:

- **OpenGL ES v2**&ndash;應用程式必須聲明 OpenGL ES v2 的要求。

- **谷歌地圖 API 金鑰**&ndash;API 金鑰用於確認應用程式已註冊並授權使用 Google Play 服務。 有關此密鑰的詳細資訊[,請參閱取 Google 地圖 API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

- **請求**&ndash;面向 Android 9.0(API 級別 28)或以上的舊 Apache HTTP 客戶端應用必須指定舊 Apache HTTP 用戶端是要使用的可選庫。

- **訪問基於谷歌的基於 Web 的服務**&ndash;應用程式需要許可權才能訪問支援 Android 地圖 API 的 Google 網路服務。

- **Google 播放服務通知**&ndash;的許可權 應用程式必須被授予接收來自 Google Play 服務的遠端通知的許可權。

- **訪問位置提供程式**&ndash;這些是可選許可權。
   它們將允許類在`GoogleMap`地圖上顯示設備的位置。

此外,Android 9 從引導類路徑中刪除了 Apache HTTP 用戶端庫,因此它不適用於針對 API 28 或更高版本的應用程式。 以下行必須添加到`application`**AndroidManifest.xml**檔案的節點中,才能在針對 API 28 或更高值的應用程式中繼續使用 Apache HTTP 用戶端:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Google Play SDK 的最新版本需要套`WRITE_EXTERNAL_STORAGE`用才能要求授權 。 對於 Google Play 服務最近的 Xamarin 綁定,不再需要此要求。

以下代碼段是必須添加到**AndroidManifest.XML**的設定範例:

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

除了請求**AndroidManifest.XML**的許可權外,應用還必須`ACCESS_COARSE_LOCATION``ACCESS_FINE_LOCATION`對和許可權執行運行時許可權檢查。 有關執行運行時許可權檢查的詳細資訊,請參閱[Xamarin.Android 許可權](~/android/app-fundamentals/permissions.md)指南。

### <a name="create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />使用 Google API 建立模擬器

如果未安裝帶有 Google Play 服務的物理 Android 設備,則可以創建用於開發模擬器的圖像。 有關詳細資訊,請參閱[裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。

## <a name="the-googlemap-class"></a>谷歌地圖類

一旦先決條件得到滿足,是時候開始開發應用程式並使用 Android 地圖 API 了。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)類是 Xamarin.Android 應用程式用於顯示 Android 的 Google 地圖並與其互動的主要 API。 此類具有以下職責:

- 與 Google Play 服務互動,授權使用 Google 網路服務進行應用程式應用。

- 下載、緩存和顯示地圖磁貼。

- 向用戶顯示 UI 控制件,如平移和縮放。

- 在地圖上繪製標記和幾何形狀。

以`GoogleMap`以下兩種方式之一新增到活動:

- **Map片段**- [Map片段](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)是充當物件`GoogleMap`宿主的 專用片段。 要求`MapFragment`Android API 級別 12 或更高。
   較舊版本的 Android 可以使用[支援映射碎片](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南將重點介紹使用該`MapFragment`類。

- **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView)是一個專用的視圖子類,它`GoogleMap`可以充當 物件的主機。 此類的使用者必須將所有活動生命週期方法轉寄到`MapView`類別 。

每個容器都公開返回`Map`實例的屬性`GoogleMap`。 應優先考慮[MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)類,因為它是一種更簡單的 API,可減少開發人員必須手動實現的樣板代碼量。

### <a name="adding-a-mapfragment-to-an-activity"></a>新增地圖片段

以下螢幕截圖是一個簡單的`MapFragment`範例:

[![顯示 Google 地圖片段的裝置螢幕擷取](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

與其他片段類類似,有兩種方法可以向活動添加`MapFragment`。

- **宣告性**`MapFragment`─ 可以透過作用的 XML 佈局檔加入 。 以下 XML 代碼段顯示如何使用`fragment`此元素的範例:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **以程式設計方式**`MapFragment`─可以使用方法以程式設計方式實例[`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance())化 ,然後添加到活動。 此程式碼段顯示了實體化`MapFragment`物件並加入到作用中的最簡單方法:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    可以通過[`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)將 物件`NewInstance`傳遞`MapFragment`給 來 配置物件。 本指南稍後將顯示的[GoogleMap 屬性](#googlemap_object)部分將討論此點。

該方法`MapFragment.GetMapAsync`用於初始化片段承載[`GoogleMap`](#googlemap_object)的 ,並取得對 由 承載的映`MapFragment`射物件的參考 。 此方法採用實現`IOnMapReadyCallback`介面的物件。

此介面具有單個方法,`IMapReadyCallback.OnMapReady(MapFragment map)`當應用可能`GoogleMap`與 物件交互時,將調用該方法。 以下代碼段顯示了 Android 活動如何`MapFragment`初始化`IOnMapReadyCallback`並實現介面:

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

### <a name="map-types"></a>地圖類型

Google 地圖 API 有五種不同類型的地圖:

- **正常**- 這是預設地圖類型。 它顯示了道路和重要的自然特徵,以及一些人工景點(如建築物和橋樑)。

- **衛星**- 這張地圖顯示了衛星攝影。

- **混合**- 此地圖顯示衛星攝影和路線圖。

- **地形**- 這主要顯示一些道路的地形特徵。

- **無**- 此地圖不載入任何切片,它呈現為空格格。

下圖顯示了三種不同類型的地圖,從左到右(普通、混合、地形):

[![三個地圖範例螢幕截圖:普通圖、混合圖和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

該`GoogleMap.MapType`屬性用於設置或更改顯示的地圖類型。 以下代碼段演示如何顯示衛星地圖。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="googlemap-properties"></a><a name="googlemap_object" />谷歌地圖屬性

`GoogleMap`定義幾個可以控制地圖的功能和外觀的屬性。 配置 初始狀態的一種`GoogleMap`方法 是在`MapFragment`創建 時 傳遞[GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)物件。 以下代碼段是在創建 時`GoogleMapOptions`使用 物件的範例之`MapFragment`一。

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

配置的另一`GoogleMap`種方法是通過操作地圖物件的[UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings)上的屬性。 下一個程式碼範例展示如何`GoogleMap`設定 以顯示縮放控制項和指南針:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>與 Google 地圖互動

Android 地圖 API 提供 API,允許活動更改視點、添加標記、放置自定義疊加或繪製幾何形狀。 本節將討論如何在 Xamarin.Android 中完成其中一些任務。

### <a name="changing-the-viewpoint"></a>變更檢視點

地圖根據墨卡托投影在螢幕上建模為平面。 地圖視圖是*照相機*在這張平面上直視的視圖。 可以通過更改位置、縮放、傾斜和軸承來控制攝像機的位置。 [「相機更新](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)」類用於移動攝像機位置。 `CameraUpdate`物件不會直接實例化,而是地圖 API 提供[CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)類。

創建`CameraUpdate`物件後,它將作為參數傳遞給[GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate))或[GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate))方法。 該方法`MoveCamera`會立即更新地圖`AnimateCamera`, 而該方法提供平滑的動畫過渡。

此程式碼是如何使用`CameraUpdateFactory``CameraUpdate`建立將地圖縮放等級提高一個縮放等級的的簡單範例:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

地圖 API 提供[一](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html)個 Camera 定位,它將聚合攝像機位置的所有可能值。 此實體可以提供相機[更新工廠.NewCamera定位](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法,該方法將傳回物件`CameraUpdate`。 地圖 API 還包括`CameraPosition`用於創建 物件的流暢 API 的[Camera 定位.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)類。
以下代碼片段顯示從 建立的範例`CameraUpdate``CameraPosition`, 並使用 該範例變更 的影像`GoogleMap`位置:

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

在前面的代碼段中,地圖上的特定位置由[LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng)類表示。 縮放級別設置為 18,這是 Google 地圖使用的任意縮放度量。 軸承是從北順時針順時針測量的。 "傾斜"屬性控制視角,並指定垂直角度 25 度。 以下螢幕截圖顯示了執行上述`GoogleMap`代碼後:

[![顯示有傾斜檢視角的指定位置的範例](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>在地圖上繪圖

Android 地圖 API 提供用於在地圖上繪製以下專案的 API:

- **標記**- 這些是用於標識地圖上單個位置的特殊圖示。

- **疊加**- 這是可用於標識地圖上位置或區域的集合的圖像。

- **線、多邊形和圓**─ 這些是允許活動向地圖添加形狀的 API。

#### <a name="markers"></a>標記

地圖 API 提供[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)類,該類封裝了地圖上單個位置的所有數據。 默認情況下,標記類使用 Google 地圖提供的標準圖示。 可以自定義標記的外觀並回應用戶按一下。

##### <a name="adding-a-marker"></a>新增標籤

要向地圖添加標記,必須創建新[的標記選項](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)物件,然後`GoogleMap`在實例上調用[AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法。 此方法將返回[標記](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)物件。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

當使用者點擊標記時,標記的標題會顯示在*資訊視窗中*。 以下螢幕截圖顯示了此標記的外觀:

[![範例 Google 地圖,帶有 Vimy Ridge 的標記和資訊視窗](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>自訂標記

在將標記添加到地圖時,`MarkerOptions.InvokeIcon`可以通過調用 方法自定義標記使用的圖示。
此方法採用一個[Bitmap 描述器](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor)物件,其中包含呈現圖示所需的數據。 [Bitmap 描述器工廠](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)類別提供一些協助程式方法來簡化`BitmapDescriptor`建立的 。 下面的清單介紹了以下一些方法:

- `DefaultMarker(float colour)`&ndash;使用預設的 Google 地圖標記,但更改顏色。

- `FromAsset(string assetName)`&ndash;使用"資源"資料夾中指定檔中的自定義圖示。

- `FromBitmap(Bitmap image)`&ndash;使用指定的點陣圖作為圖示。

- `FromFile(string fileName)`&ndash;從指定路徑上的文件創建自訂圖示。

- `FromResource(int resourceId)`&ndash;從指定資源創建自定義圖示。

以下代碼片段顯示了建立青色彩色預設標記的範例:

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

*資訊視窗*是特殊的視窗,在使用者點擊特定標記時彈出以向使用者顯示資訊。 預設情況下,資訊視窗將顯示標記標題的內容。 如果未分配標題,則不會顯示任何資訊視窗。 一次只能顯示一個資訊視窗。

可以通過實現[GoogleMap.IInfoWindowAdapter 界面](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)來自定義資訊視窗。 此介面上有兩種重要方法:

- `public View GetInfoWindow(Marker marker)`&ndash;調用此方法是為了獲取標記的自定義資訊視窗。 如果返回`null`,則將使用默認窗口呈現。 如果此方法返回檢視,則該視圖將放置在資訊視窗框架中。

- `public View GetInfoContents(Marker marker)`&ndash;僅當 GetInfoWindow`null`返回 時,才會調用此方法。 如果要使用資訊視窗內容`null`的預設呈現,此方法可以返回值。 否則,此方法應返回包含資訊窗口內容的視圖。

資訊視窗不是即時檢視 - 相反,Android 會將視圖轉換為靜態位圖,並在圖像上顯示該視圖。 這意味著資訊視窗無法回應任何觸摸事件或手勢,也不會自動更新自身。 要更新資訊視窗,必須調用[GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下圖顯示了一些自定義資訊視窗的一些範例。 左方的影像已自訂其內容,而右側的影像具有具有帶圓角的視窗和內容:

![墨爾本的示例標記視窗,包括圖示和填充。 右側視窗具有圓角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>接地覆寫

與識別地圖上特定位置的標記不同,[地面覆蓋](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay)是用於標識地圖上位置或區域的集合的圖像。

##### <a name="adding-a-groundoverlay"></a>新增接地覆寫

向地圖添加地面疊加類似於向地圖添加標記。 首先,創建[「地基覆蓋選項」](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions)物件。 然後,此對象作為參數傳遞[`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions))給 方法,該方法將返回`GroundOverlay`物件。 此程式碼段是向地圖加入地疊加的範例:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

以下螢幕截圖顯示地圖上的此疊加:

[![帶有北極熊疊加影像的範例地圖](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>線條、圓形和多邊形

有三種簡單的幾何圖形類型可以新增到地圖中:

- **折線**- 這是一系列連接的線段。 它可以在地圖上標記路徑或創建幾何形狀。

- **圓圈**- 這將在地圖上繪製一個圓圈。

- **多邊形**─ 這是用於標記地圖上區域的封閉形狀。

##### <a name="polylines"></a>折線

[折線](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline)是指定每個線段`LatLng`頂 點的連續物件的清單。 通過首先創建`PolylineOptions`物件並將點添加到該物件來創建折線。 然後`PolylineOption`,通過調`AddPolyline`用`GoogleMap`方法將 物件傳遞給物件。

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

通過首先實例化[CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions)物件創建圓圈,該物件將指定圓的中心和半徑(以米為單位)。 該圓圈通過調用[GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))在地圖上繪製。
以下程式碼段展示如何繪製圓圈:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>多邊形

`Polygon`s 類似`Polyline`於 s,但它們不是開放式的。 `Polygon`s 是一個閉環,並填充其內部。
`Polygon`s的創建方式與`Polyline`相同 ,但調用的[GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))方法除外。

與`Polyline`不同`Polygon`, 是自我關閉。 通過繪製連接第一個點和最後一`AddPolygon`個點的線,該方法將關閉面。 以下代碼段將在與`Polyline`示例中的上一個代碼段相同的區域上創建一個實體矩形。

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

使用者與地圖的互動類型有三種:

- **標記按下**- 使用者按下標記。

- **標記拖曳**─ 使用者長時間按下滑鼠

- **資訊視窗按下**─使用者已按下資訊視窗。

下面將更詳細地討論其中每一個事件。

### <a name="marker-click-events"></a>標記按下事件

當用戶`MarkerClicked`點擊標記時,將引發該事件。 此事件接受物件`GoogleMap.MarkerClickEventArgs`作為參數。 此包含兩個屬性:

- `GoogleMap.MarkerClickEventArgs.Handled`&ndash;此屬性應設置為`true`指示事件處理程式已使用該事件。 如果設置為,`false`則除了事件處理程式的自定義行為外,還將發生默認行為。

- `Marker`&ndash;此屬性是引發事件的標記的`MarkerClick`引用。

此程式碼段顯示`MarkerClick`將 攝像機位置更改為地圖上的新位置的範例:

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

### <a name="marker-drag-events"></a>標記拖動事件

當使用者希望拖動標記時引發此事件。 默認情況下,標記不可拖動。 使用屬性`Marker.Draggable`設定為`true`或將`MarkerOptions.Draggable`方法作為參數呼叫,`true`可以將標記設定為可拖動。

要拖動標記,用戶必須首先長按標記,然後他們的手指必須保留在地圖上。 當使用者的手指在螢幕上拖動時,標記將移動。 當使用者的手指從螢幕上抬起時,標記將保持原位。

下面的清單描述了將針對可拖動標記引發的各種事件:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash;當使用者首次拖動標記時引發此事件。

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`&ndash;當拖動標記時引發此事件。

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash;當使用者完成拖動標記後,將引發此事件。

每個`EventArgs`包含一個稱為`P0`「 要拖動的物件`Marker`」的 單個屬性。

### <a name="info-window-click-events"></a>資訊視窗 點擊事件

一次只能顯示一個資訊視窗。 當使用者按一下地圖中的資訊視窗時,地圖物件將引發`InfoWindowClick`事件 。 以下程式碼段展示如何將處理程式連線到事件:

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

回想一下,資訊視窗是靜態`View`視窗,在地圖上呈現為圖像。 放置在資訊視窗中的任何小部件(如按鈕、複選框或文本視圖)都將是惰性的,無法回應其任何集成使用者事件。

## <a name="related-links"></a>相關連結

- [簡單對應映射](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play 服務](https://developers.google.com/android/guides/overview)
- [谷歌地圖Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [谷歌播放服務APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [取得 Google 地圖 API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [使用庫](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element)
