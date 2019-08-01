---
title: Android 上的位置服務
description: 本指南介紹 Android 應用程式中的位置感知, 並說明如何使用 Android 位置服務 API 來取得使用者的位置, 以及搭配 Google 位置服務 API 提供的融合式位置提供者。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: b44bb52dc69aae1d3d058a1eae7c3be13ec5dc53
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643344"
---
# <a name="location-services-on-android"></a>Android 上的位置服務

_本指南介紹 Android 應用程式中的位置感知, 並說明如何使用 Android 位置服務 API 來取得使用者的位置, 以及搭配 Google 位置服務 API 提供的融合式位置提供者。_

Android 可讓您存取各種不同的位置技術, 例如儲存格塔式位置、Wi-fi 和 GPS。 每個位置技術的詳細資料都是透過*位置提供者*來抽象化, 讓應用程式以相同的方式取得位置, 而不論所使用的提供者為何。 本指南介紹「融合的位置提供者」, 這是 Google Play Services 的一部分, 可根據可用的提供者和裝置的使用方式, 以智慧方式判斷取得裝置位置的最佳方法。 Android 位置服務 API, 並顯示如何使用`LocationManager`來與系統位置服務通訊。 本指南的第二個部分會使用`LocationManager`探索 ANDROID 定位服務 API。

根據一般經驗法則, 應用程式應該偏好使用已融合的位置提供者, 只有在必要時才回復較舊的 Android 位置服務 API。

## <a name="location-fundamentals"></a>位置基本概念

在 Android 中, 不論您選擇使用哪個 API 來處理位置資料, 有幾個概念保持不變。 本節介紹位置提供者和與位置相關的許可權。

### <a name="location-providers"></a>位置提供者

在內部使用數種技術來找出使用者的位置。 使用的硬體取決於為收集資料所選取的*位置提供者*類型。 Android 使用三個位置提供者:

-   **GPS 提供者**&ndash; GPS 提供最精確的位置、使用最多的功能, 而且在戶外的效果最佳。 此提供者會使用 GPS 和輔助 GPS ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)) 的組合, 這會傳回行動電話塔所收集的 GPS 資料。

-   **網路提供者**&ndash;提供 WiFi 和行動電話資料的組合, 包括資料格塔所收集的 aGPS 資料。 其使用的功能比 GPS 提供者少, 但會傳回不同精確度的位置資料。

-   **被動提供者**&ndash;使用其他應用程式或服務所要求的提供者, 在應用程式中產生位置資料的「可攜帶」選項。 這是較不可靠但省電選項, 適用于不需要常數位置更新才能正常執行的應用程式。

位置提供者不一定可供使用。 例如, 我們可能會想要針對我們的應用程式使用 GPS, 但 GPS 可能會在 [設定] 中關閉, 或者裝置可能完全沒有 GPS。 如果特定提供者無法使用, 則選擇該提供者可能`null`會傳回。

### <a name="location-permissions"></a>位置許可權

有位置感知的應用程式需要存取裝置的硬體感應器, 才能接收 GPS、Wi-fi 和行動資料。 存取權是透過應用程式的 Android 資訊清單中的適當許可權來控制。
視您的應用程式&ndash;需求和您選擇的 API 而定, 有兩個可用的許可權, 您會想要允許一個:

-   `ACCESS_FINE_LOCATION`&ndash;允許應用程式存取 GPS。
    *GPS 提供者*和*被動提供者*選項的必要項 (*被動提供者需要許可權才能存取其他應用程式或服務所收集的 GPS 資料*)。 *網路提供者*的選擇性許可權。

-   `ACCESS_COARSE_LOCATION`&ndash;允許應用程式存取行動電話和 wi-fi 位置。 如果`ACCESS_FINE_LOCATION`未設定, 則為*網路提供者*所需。

針對以 API 第21版 (Android 5.0 棒) 或更高版本為目標的`ACCESS_FINE_LOCATION`應用程式, 您可以啟用並繼續在沒有 GPS 硬體的裝置上執行。 如果您的應用程式需要 GPS 硬體, 您應該明確`android.hardware.location.gps`地將`uses-feature`元素新增至 Android 資訊清單。 如需詳細資訊, 請參閱 Android[使用-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)元素參考。

若要設定許可權, 請展開  **Solution Pad**中的 **屬性** 資料夾, 然後按兩下  **androidmanifest.xml**。 許可權會列在 [**必要許可權**] 底下:

[![Android 資訊清單所需許可權設定的螢幕擷取畫面](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

設定其中任一許可權會告訴 Android, 您的應用程式需要使用者的許可權, 才能存取位置提供者。 執行 API 層級 22 (Android 5.1) 或更低版本的裝置, 會要求使用者在每次安裝應用程式時授與這些許可權。 在執行 API 層級 23 (Android 6.0) 或更高版本的裝置上, 應用程式應該在提出位置提供者的要求之前, 先執行執行時間許可權檢查。 

> [!NOTE]
>注意:設定`ACCESS_FINE_LOCATION`意指對粗略和精確位置資料的存取。 您永遠都不需要設定這兩個許可權, 只有應用程式需要的*最低*許可權才能正常執行。

此程式碼片段是如何檢查應用程式是否有`ACCESS_FINE_LOCATION`許可權許可權的範例:

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

應用程式必須能夠容忍使用者不會授與許可權 (或已撤銷許可權) 的情況, 並且能夠以適當的方式處理這種情況。 如需在 Xamarin. Android 中執行執行時間許可權檢查的詳細資訊, 請參閱[許可權指南](~/android/app-fundamentals/permissions.md)。


## <a name="using-the-fused-location-provider"></a>使用融合的位置提供者

「融合位置提供者」是 Android 應用程式從裝置接收位置更新的慣用方式, 因為它會在執行時間有效率地選取位置提供者, 以符合電池效率的方式提供最佳的位置資訊。 例如, 使用者在戶外流覽時, 會取得使用 GPS 閱讀的最佳位置。 如果使用者接著走到室內, 其中 GPS 的運作效果不佳 (如果有的話), 則已融合的位置提供者可能會自動切換至 WiFi, 其運作效果較佳。
 
「融合的位置提供者 API」提供各種其他工具來強化位置感知應用程式, 包括地理柵欄和活動監視。 在本節中, 我們將著重于設定`LocationClient`、建立提供者, 以及取得使用者位置的基本概念。

「融合位置提供者」是[Google Play Services](https://developer.android.com/google/play-services/index.html)的一部分。
Google Play Services 套件必須在應用程式中正確安裝並設定, 才能讓融合的位置提供者 API 運作, 而且裝置必須已安裝 Google Play Services APK。

在 Xamarin Android 應用程式可以使用已融合的位置提供者之前, 它必須先將**GooglePlayServices**套件新增至專案。 此外, 下列`using`語句應新增至任何參考下列類別的原始程式檔:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>檢查是否已安裝 Google Play Services

如果 Google Play Services 未安裝 (或已過期), 則 Xamarin 會在嘗試使用已融合的位置提供者時損毀, 然後會發生執行時間例外狀況。  如果未安裝 Google Play Services, 應用程式應該會回到上面討論的 Android 位置服務。 如果 Google Play Services 已過期, 應用程式可能會向使用者顯示訊息, 要求他們更新已安裝的 Google Play Services 版本。

此程式碼片段是 Android 活動如何以程式設計方式檢查是否已安裝 Google Play Services 的範例:

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

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

若要與融合位置提供者互動, Xamarin. Android 應用程式必須具有的`FusedLocationProviderClient`實例。 此類別會公開訂閱位置更新的必要方法, 以及取得裝置的最後一個已知位置。

活動`OnCreate`的方法是取得參考`FusedLocationProviderClient`的適當位置, 如下列程式碼片段所示:

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

### <a name="getting-the-last-known-location"></a>取得最後一個已知的位置

`FusedLocationProviderClient.GetLastLocationAsync()`方法為 Xamarin Android 應用程式提供簡單且非封鎖的方式, 以最少的編碼額外負荷來快速取得裝置的最後一個已知位置。

此程式碼片段顯示如何使用`GetLastLocationAsync`方法來抓取裝置的位置:

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

Xamarin Android 應用程式也可以使用`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法, 從已融合的位置提供者訂閱位置更新, 如下列程式碼片段所示:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

這個方法會採用兩個參數:

-   **`Android.Gms.Location.LocationRequest`** &ndash; 物件是XamarinAndroid應用程式如何傳遞「融合位置提供者」應該`LocationRequest`如何使用的參數。 會`LocationRequest`保留資訊, 例如要求的執行頻率, 或正確位置更新的重要性。 例如, 在判斷位置時, 重要的位置要求將會導致裝置使用 GPS, 因而更強大。 此程式碼片段示範如何`LocationRequest`針對具有高精確度的位置建立, 並每隔五分鐘檢查一次位置更新 (但不會在要求之間的兩分鐘內)。 已融合的位置提供者會`LocationRequest`使用, 做為嘗試判斷裝置位置時所要使用之位置提供者的指引:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

-   **`Android.Gms.Location.LocationCallback`** 為了接收位置更新, Xamarin. Android 應用程式必須`LocationProvider`子類別化抽象類別。 &ndash; 此類別公開了兩種方法, 可供融合位置提供者用來以位置資訊更新應用程式。 下面將更詳細地討論這一點。

若要通知 Xamarin Android 應用程式的位置更新, 已融合的位置提供者將會`LocationCallBack.OnLocationResult(LocationResult result)`叫用。 `Android.Gms.Location.LocationResult`參數將包含更新位置資訊。

當融合的位置提供者偵測到位置資料的可用性變更時, 它會呼叫`LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`方法。 `LocationAvailability.IsLocationAvailable`如果`OnLocationResult` `LocationRequest`屬性傳回,則可以假設所報告的裝置位置結果是正確的,並且是所需的`true`最新狀態。 如果`IsLocationAvailable`為 false, 則不會`OnLocationResult`傳回任何位置結果。

此程式碼片段是`LocationCallback`物件的範例執行:

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

## <a name="using-the-android-location-service-api"></a>使用 Android 位置服務 API

Android 位置服務是舊版的 API, 可在 Android 上使用位置資訊。 位置資料是由硬體感應器收集, 並由系統服務所收集, 其會在具有`LocationManager`類別`ILocationListener`和的應用程式中存取。

「位置」服務最適合必須在未安裝 Google Play Services 的裝置上執行的應用程式。

「位置服務」是系統所管理的特殊[服務](https://developer.android.com/guide/components/services.html)類型。 系統服務會與裝置硬體互動, 而且一律會執行。 若要在我們的應用程式中切入位置更新, 我們將使用`LocationManager` `RequestLocationUpdates`和呼叫來訂閱系統位置服務的位置更新。

若要使用 Android 位置服務來取得使用者的位置, 需要執行幾個步驟:

1.  取得`LocationManager`服務的參考。
2.  `ILocationListener`執行介面, 並在位置變更時處理事件。
3.  `LocationManager`使用來要求指定提供者的位置更新。 上`ILocationListener`一個步驟中的會用來接收來自的`LocationManager`回呼。
4.  當應用程式不再適合接收更新時, 停止位置更新。

### <a name="location-manager"></a>位置管理員

我們可以使用`LocationManager`類別的實例來存取系統位置服務。 `LocationManager`是特殊類別, 可讓我們與系統位置服務互動, 並對其呼叫方法。 應用程式可以藉由呼叫`LocationManager` `GetSystemService`並傳入服務類型來取得的參考, 如下所示:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`是取得參考`LocationManager`的絕佳位置。
最好將保留`LocationManager`為類別變數, 讓我們可以在活動生命週期的不同點呼叫它。

### <a name="request-location-updates-from-the-locationmanager"></a>從 LocationManager 要求位置更新

一旦應用程式具有的`LocationManager`參考, 它就必須`LocationManager`告訴您所需的位置資訊類型, 以及該資訊的更新頻率。 若要這麼做`RequestLocationUpdates` , 請`LocationManager`在物件上呼叫, 並傳入一些更新準則, 以及接收位置更新的回呼。 此回呼是必須實作為`ILocationListener`介面的型別 (本指南稍後會詳細說明)。

`RequestLocationUpdates`方法會告訴系統位置服務, 您的應用程式會想要開始接收位置更新。 這個方法可讓您指定提供者, 以及用來控制更新頻率的時間和距離閾值。 例如, 下列方法會要求來自 GPS 位置提供者的位置更新每2000毫秒, 而且只有在位置變更超過1米時:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

應用程式應該只要求應用程式正常執行所需的位置更新。 這會保留電池壽命, 並為使用者建立更好的體驗。

### <a name="responding-to-updates-from-the-locationmanager"></a>回應來自 LocationManager 的更新

應用程式從`LocationManager`要求更新之後, 就可以藉由[`ILocationListener`](xref:Android.Locations.ILocationListener)執行介面, 從服務接收資訊。 這個介面提供四種接聽位置服務和位置提供者`OnLocationChanged`的方法。 當使用者的位置`OnLocationChanged`變更時, 系統將會呼叫, 以便根據要求位置更新時所設定的準則, 以符合位置的變更。 

下列程式碼顯示`ILocationListener`介面中的方法:

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

### <a name="unsubscribing-to-locationmanager-updates"></a>取消訂閱 LocationManager 更新

為了節省系統資源, 應用程式應該儘快取消訂閱位置更新。 方法會指示停止`LocationManager`將更新傳送至我們的應用程式。 `RemoveUpdates`  例如, 活動可能會`RemoveUpdates` `OnPause`在方法中呼叫, 以便在應用程式的活動不在畫面上時, 可以節省電源:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果您的應用程式需要在背景中取得位置更新, 您會想要建立訂閱系統位置服務的自訂服務。 如需詳細資訊, 請參閱[使用 Android 服務的背景處理](~/android/app-fundamentals/services/index.md)指南。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>判斷 LocationManager 的最佳位置提供者

上述應用程式會將 GPS 設定為位置提供者。 不過, GPS 可能無法在所有情況下使用, 例如裝置為室內或沒有 GPS 接收器。 如果是這種情況, 則結果會`null`傳回提供者。

若要讓您的應用程式在 GPS 無法使用時能夠運作, `GetBestProvider`您可以使用方法, 在應用程式啟動時要求最佳可用 (裝置支援和使用者啟用) 位置提供者。 您可以不傳遞特定的提供者, 而是`GetBestProvider`告訴提供者的需求, 例如精確度和[ `Criteria`具有物件](xref:Android.Locations.Criteria)的電源。 `GetBestProvider`傳回指定準則的最佳提供者。

下列程式碼顯示如何取得最佳的可用提供者, 並在要求位置更新時使用:

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
>  如果使用者已停用所有位置提供者`GetBestProvider` , `null`則會傳回。 若要查看此程式碼如何在實際裝置上運作, 請務必啟用 Google 設定 下的 GPS、Wi-fi 和行動網路, **> 位置 > 模式**, 如下列螢幕擷取畫面所示:

[![Android 手機上的設定位置模式畫面](location-images/location-02.png)](location-images/location-02.png#lightbox)

下列螢幕擷取畫面示範使用`GetBestProvider`執行的位置應用程式:

[![顯示緯度、經度和提供者的 GetBestProvider 應用程式](location-images/location-03.png)](location-images/location-03.png#lightbox)

請記住, `GetBestProvider`不會動態變更提供者。 相反地, 它會在活動生命週期期間決定最適合的提供者一次。 如果提供者狀態在設定之後變更, 應用程式`ILocationListener`將需要`OnProviderDisabled`方法`OnStatusChanged` &ndash; `OnProviderEnabled` &ndash;中的額外程式碼, 以及處理與提供者切換。

## <a name="summary"></a>總結

本指南涵蓋從 Google 位置服務 API 使用 Android 定位服務和融合位置提供者來取得使用者的位置。

## <a name="related-links"></a>相關連結

- [位置 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Criteria 類別](xref:Android.Locations.Criteria)
- [LocationManager 類別](xref:Android.Locations.LocationManager)
- [LocationListener 類別](xref:Android.Locations.ILocationListener)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
