---
title: 安卓系統上的定位服務
description: 本指南介紹了 Android 應用程式中的位置感知,並說明瞭如何使用 Android 定位服務 API 以及 Google 定位服務 API 提供的融合位置提供商獲取使用者的位置。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 0fc74ae2307ffd14f8c52515c93993a51455997a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80805960"
---
# <a name="location-services-on-android"></a>安卓系統上的定位服務

_本指南介紹了 Android 應用程式中的位置感知,並說明瞭如何使用 Android 定位服務 API 以及 Google 定位服務 API 提供的融合位置提供商獲取使用者的位置。_

Android 提供各種位置技術(如基塔位置、Wi-Fi 和 GPS)的訪問。 每個位置技術的詳細資訊通過*位置提供程式*進行抽象,允許應用程式以相同的方式獲取位置,而不管所使用的提供程式如何。 本指南介紹了融合位置供應商,這是 Google Play 服務的一部分,它根據可用的供應商以及設備的使用方式,智慧地確定獲取設備位置的最佳方式。 Android 位置服務 API 並演示如何使用`LocationManager`與 系統位置服務 通訊。 本指南的第二部分使用 探索 Android`LocationManager`定位服務 API。

作為一般經驗法則,應用程式應更喜歡使用融合位置提供程式,僅在必要時返回較舊的 Android 位置服務 API。

## <a name="location-fundamentals"></a>位置基礎知識

在 Android 中,無論您選擇使用位置數據所需的 API 是什麼,幾個概念都保持不變。 本節介紹位置提供程式和位置相關許可權。

### <a name="location-providers"></a>位置提供者

內部使用多種技術來精確定位使用者的位置。 使用的硬體取決於為收集數據的工作選擇*的位置提供程式*的類型。 安卓使用三個位置提供者:

- **GPS 提供者**&ndash;GPS 提供最準確的位置,使用最多的電源,並在戶外工作最佳。 該提供者使用GPS和輔助[GPS(GPS)](https://en.wikipedia.org/wiki/Assisted_GPS)的組合,返回由蜂巢藏的收集的GPS資料。

- **網路供應商**&ndash;提供WiFi和蜂巢資料的組合,包括基站收集的GPS資料。 它使用的功率比 GPS 提供者少,但返回位置數據的準確性各不相同。

- **被動提供程式**&ndash;使用其他應用程式或服務請求的提供程式在應用程式中生成位置數據。 這是一個不太可靠但省電的選項,非常適合不需要恆定的位置更新才能工作的應用程式。

位置提供程式並不總是可用的。 例如,我們可能希望將 GPS 用於我們的應用,但 GPS 可能在「設定」中關閉,或者設備可能根本不具有 GPS。 如果指定提供者無法使用,則選擇該提供程式可能會傳`null`回 。

### <a name="location-permissions"></a>位置權限

位置感知應用程式需要存取設備的硬體感測器來接收 GPS、Wi-Fi 和蜂巢式行動資料。 訪問通過應用程式的 Android 清單中的相應許可權進行控制。
&ndash;根據應用程式的要求和選擇的 API,可以使用兩個權限,您需要允許一個權限:

- `ACCESS_FINE_LOCATION`&ndash;允許應用程式訪問 GPS。
    *GPS 提供者*和*被動提供者*選項(*被動供應商需要許可權才能存取由其他應用程式或服務收集的 GPS 資料*)。 *網路提供者*的選擇權限 。

- `ACCESS_COARSE_LOCATION`&ndash;允許應用程式存取蜂巢移動和Wi-Fi位置。 如果未設置 *,則網路供應商*`ACCESS_FINE_LOCATION`需要。

對於針對 API 版本 21(Android 5.0 棒棒糖`ACCESS_FINE_LOCATION`)或更高版本的 應用,您可以在沒有 GPS 硬體的設備上啟用並且仍然運行。 如果你的應用需要 GPS 硬體,則應顯式`android.hardware.location.gps``uses-feature`向 Android 清單添加元素。 有關詳細資訊,請參閱 Android[使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)元素引用。

要設定權限,請展開**解決方案墊**中的 **「屬性**」資料夾,然後按**下 AndroidManifest.xml**。 權限將欄在 **「必需權限**」

[![Android 清單必須權限設定的螢幕截圖](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

設置這些許可權之一告訴 Android 應用程式需要使用者的許可才能訪問位置提供程式。 運行 API 級別 22(Android 5.1)或更低等級的設備將要求使用者在每次安裝應用時授予這些許可權。 在運行 API 級別 23 (Android 6.0) 或更高等級的設備上,應用應在發出位置提供程式的請求之前執行執行時許可權檢查。 

> [!NOTE]
>注意:設置`ACCESS_FINE_LOCATION`意味著訪問粗位置和精細位置數據。 您不必同時設置這兩個許可權,只需設置應用所需的*最小*許可權即可工作。

此程式段是檢查應用程式是否具有`ACCESS_FINE_LOCATION`權限的範例:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

應用必須容忍使用者不會授予許可權(或已吊銷許可權)的情況,並具有優雅地處理這種情況的方法。 有關在 Xamarin.Android 中實現執行時權限檢查的詳細資訊,請參考[權限指南](~/android/app-fundamentals/permissions.md)。

## <a name="using-the-fused-location-provider"></a>使用融合位置提供者

融合位置提供者是 Android 應用程式從設備接收位置更新的首選方式,因為它將在執行時有效地選擇位置提供者,以節能的方式提供最佳位置資訊。 例如,在戶外走動的使用者使用 GPS 獲得最佳位置讀數。 如果用戶隨後在室內行走,GPS 工作不佳(如果有的話),融合位置提供者可能會自動切換到 WiFi,這在室內效果更好。

融合位置提供者 API 提供了各種其他工具,用於增強位置感知應用程式,包括地理圍欄和活動監視。 在本節中,我們將重點介紹設置`LocationClient`、建立提供程式和獲取使用者位置的基礎知識。

融合的位置提供商是[谷歌Play服務的](https://developer.android.com/google/play-services/index.html)一部分。
Google Play 服務包必須在應用程式中正確安裝和配置,以便融合位置提供者 API 正常工作,並且設備必須安裝 Google Play 服務 APK。

在 Xamarin.Android 應用程式可以使用融合位置提供程式之前,它必須向專案添加**Xamarin.GooglePlayServices.位置**包。 此外,應將以下`using`語句添加到引用下面描述的類的任何來源檔中:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>檢查是否安裝 Google 播放服務

如果 Xamarin.Android 嘗試在未安裝(或過期)時使用融合位置供應商,則會出現運行時異常,則 Xamarin.Android 將崩潰。  如果未安裝 Google Play 服務,則應用程式應回退到上面討論的 Android 定位服務。 如果 Google Play 服務已過期,則應用可能會向使用者顯示一條消息,要求他們更新已安裝版本的 Google Play 服務。

此程式碼段是 Android 活動如何以程式設計方式檢查是否安裝 Google Play 服務的範例:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);

        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>融合提供者客戶端

要與融合位置提供程式進行互動,Xamarin.Android 應用程式必須具有`FusedLocationProviderClient`的實例。 此類公開訂閱位置更新和檢索設備最後一個已知位置的必要方法。

活動`OnCreate`方法是取得 引用的合適`FusedLocationProviderClient`位置, 如以下代碼段所示:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>取得最後一個已知位置

該方法`FusedLocationProviderClient.GetLastLocationAsync()`為 Xamarin.Android 應用程式提供了一種簡單、非阻塞的方法,以最小的編碼開銷快速獲取設備的最後已知位置。

此程式碼段展示如何`GetLastLocationAsync`使用 方法檢索裝置的位置:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>訂閱位置更新

Xamarin.Android 應用程式還可以使用`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法 從融合位置提供程式訂閱位置更新,如以下代碼段所示:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

此方法採用兩個參數:

- **`Android.Gms.Location.LocationRequest`**&ndash;對`LocationRequest`像是 Xamarin.Android 應用程式如何傳遞有關融合位置提供程式應如何工作的參數。 這些`LocationRequest`保留資訊,例如應頻繁發出請求的頻率或準確位置更新的重要性。 例如,在確定位置時,重要的位置請求將導致設備使用 GPS,從而增加電源。 此程式碼段演示`LocationRequest`如何 為位置創建高精度,大約每五分鐘檢查一次位置更新(但請求之間不超過兩分鐘)。 在嘗試確定裝置位置時,融合`LocationRequest`位置提供程式將使用 作為位置提供程式的指南:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`**&ndash;為了接收位置更新,Xamarin.Android 應用程式必須`LocationProvider`對抽象類進行數類。 此類公開了兩種方法,這些方法可能由融合位置提供程式調用,以使用位置資訊更新應用。 下面將更詳細地討論這一問題。

要將位置更新通知 Xamarin.Android 應用程式,融合位置提供程式將呼`LocationCallBack.OnLocationResult(LocationResult result)`叫 。 參數`Android.Gms.Location.LocationResult`將包含更新位置資訊。

當融合位置提供程式檢測到位置資料可用性的變化時,它將調用`LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`方法 。 如果`LocationAvailability.IsLocationAvailable`屬性`true`返回 ,則可以假`OnLocationResult`定所 報告的設備`LocationRequest`位置結果與 要求一樣準確且最新。 如果`IsLocationAvailable`為 false,則`OnLocationResult`不會返回 位置結果。

此程式碼是物件的範例`LocationCallback`:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>使用 Android 定位服務 API

Android 定位服務是使用 Android 上的位置資訊的舊 API。 位置數據由硬體感測器收集,並由系統服務收集,系統服務在應用程式中使用`LocationManager`類`ILocationListener`和 訪問。

定位服務最適合必須在未安裝 Google Play 服務的設備上運行的應用程式。

定位服務是系統管理的[特殊型態的服務](https://developer.android.com/guide/components/services.html)。 系統服務與設備硬體交互,並且始終運行。 要利用應用程式中的位置更新,我們將使用和`LocationManager``RequestLocationUpdates`調用訂閱系統位置服務的位置更新。

要使用 Android 定位服務取得使用者的位置,需要執行以下幾個步驟:

1. 獲取對`LocationManager`服務的引用。
2. 實現介面`ILocationListener`並在位置更改時處理事件。
3. 使用`LocationManager`請求指定提供程式的位置更新。 上`ILocationListener`一步中的將用於接收`LocationManager`來自的回調。
4. 當應用程式不再適合接收更新時,停止位置更新。

### <a name="location-manager"></a>位置管理員

我們可以使用`LocationManager`類的實例訪問系統位置服務。 `LocationManager`是一個特殊的類,允許我們與系統位置服務進行交互,並在其上調用方法。 應用程式可以通過調`GetSystemService`用 和傳遞服務`LocationManager`類型來 獲取對 的引用,如下所示:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`是獲取`LocationManager`參考的好地方。
最好將`LocationManager`保留 為類變數,以便我們可以在活動生命週期中的各個點調用它。

### <a name="request-location-updates-from-the-locationmanager"></a>從位置管理員要求位置更新

一旦應用程式具有 對`LocationManager`的引用,它需要`LocationManager`告訴 需要哪些類型的位置資訊,以及該資訊的更新頻率。 為此,調用`RequestLocationUpdates``LocationManager`物件,並傳入一些更新條件和將接收位置更新的回調。 此回調是必須實現`ILocationListener`介面的類型(本指南稍後將詳細介紹)。

該方法`RequestLocationUpdates`告訴系統位置服務,您的應用程式希望開始接收位置更新。 此方法允許您指定提供程式以及時間和距離閾值以控制更新頻率。 例如,下面的方法每 2000 毫秒請求 GPS 位置提供程式的位置更新,並且僅當位置更改超過 1 公尺時:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

應用程式應僅按要求請求位置更新,以便應用程式執行良好。 這樣可以延長電池壽命,並為用戶創造更好的體驗。

### <a name="responding-to-updates-from-the-locationmanager"></a>回應位置管理員的更新

一旦應用程式要求了 更新`LocationManager`, 它[`ILocationListener`](xref:Android.Locations.ILocationListener)可以通過實現 介面從服務接收資訊。 此介面提供四種方法來偵聽位置服務與位置提供者`OnLocationChanged`。 當使用者的位置發生足夠`OnLocationChanged`變化,以符合請求位置更新時設置的條件更改時,系統將調用。 

以下代碼顯示了介面中`ILocationListener`的方法:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>取消訂閱位置管理員更新

為了節省系統資源,應用程式應儘快取消訂閱位置更新。 該方法`RemoveUpdates`告訴`LocationManager`停止向應用程式發送更新。  例如,如果應用程式不需要位置更新,`RemoveUpdates`而`OnPause`其 活動不在螢幕上,則 Activity 可能會調用 方法,以便在應用程式不需要位置更新時,我們能夠節省功耗:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果應用程式需要在後台獲取位置更新,則需要創建訂閱系統定位服務的自定義服務。 有關詳細資訊,請參閱[Android 服務背景](~/android/app-fundamentals/services/index.md)指南。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>決定位置管理員的最佳位置提供者

上述應用程式將 GPS 設置為位置提供程式。 但是,GPS 可能並非在所有情況下都可用,例如設備在室內或沒有 GPS 接收器。 如果是這種情況,則結果是`null`提供程式返回。

要使應用在 GPS 不可用時正常工作,`GetBestProvider`請使用該方法在應用程式啟動時請求最佳可用(支援設備且啟用使用者)位置提供程式。 您可以用`GetBestProvider`[`Criteria`物件](xref:Android.Locations.Criteria)告訴提供程式的要求(如準確性和功率)而不是傳入特定提供程式。 `GetBestProvider`返回給定條件的最佳提供程式。

以下代碼展示如何取得最佳可用提供者,並在請求位置更新時使用它:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
> 如果使用者已關閉所有位置提供者,`GetBestProvider`則傳`null`回 。 要查看此代碼在真實設備上的工作方式,請確保在 Google 設定下啟用 GPS、Wi-Fi 和蜂巢網路 **>位置>模式**,如以下螢幕截圖所示:
>
> [![Android 手機上的設定位置模式螢幕](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> 下面的螢幕擷取使用執行`GetBestProvider`位置應用程式:
>
> [![GetBestProvider 應用顯示緯度、經度和提供者](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> 請記住,`GetBestProvider`它不會動態更改提供程式。 相反,它在活動生命週期中確定一次最佳可用提供程式。 如果提供程式狀態在設置後發生`ILocationListener`更改,則應用程式將需要&ndash;`OnProviderEnabled`方法中的其他`OnProviderDisabled`代碼`OnStatusChanged`&ndash;,並處理與提供程式交換機相關的所有可能性。

## <a name="summary"></a>總結

本指南涵蓋使用 Android 定位服務和從 Google 定位服務 API 中融合的位置提供商獲取使用者位置。

## <a name="related-links"></a>相關連結

- [位置(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [融合定位提供者 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play 服務](https://developer.android.com/google/play-services/index.html)
- [標準類](xref:Android.Locations.Criteria)
- [位置管理員類別](xref:Android.Locations.LocationManager)
- [位置偵聽類](xref:Android.Locations.ILocationListener)
- [位置客戶端 API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [位置偵聽器 API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [位置要求 API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
