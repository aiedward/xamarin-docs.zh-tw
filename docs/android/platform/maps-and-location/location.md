---
title: "位置服務"
description: "本指南介紹 Android 應用程式中的位置感知，並說明如何取得 Google 位置服務 API 中使用 Android 的位置服務 API，以及可用的合成的位置提供者的使用者的位置。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 28cfb4cda27446b914c23a2ce4e8e9f845876107
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="location-services"></a>位置服務

_本指南介紹 Android 應用程式中的位置感知，並說明如何取得 Google 位置服務 API 中使用 Android 的位置服務 API，以及可用的合成的位置提供者的使用者的位置。_

## <a name="location-services-overview"></a>位置服務概觀

Android 存取各種不同的位置技術，例如儲存格塔台位置、 Wi-fi、 和 GPS。 每個位置技術詳細資料會透過抽象*位置提供者*，允許應用程式相同的方式，不論使用的提供者取得的位置。 本指南介紹合成的位置提供者，Google Play 服務，以聰明的方式決定取得根據哪些提供者可以使用，以及如何使用裝置的裝置位置的最佳方式的一部分。 Android 的位置服務 API，並說明如何與系統位置進行服務使用`LocationManager`。 第二個部分的節目表瀏覽 Android 位置服務 API 使用`LocationManager`。
 
為一般的經驗法則，應用程式應該最好使用合成的位置提供者，回到舊 Android 位置服務 API 只在必要時。

## <a name="location-fundamentals"></a>位置的基本概念

在 Android 中，不論您選擇使用位置資料的哪些應用程式開發介面，幾個概念維持不變。本節將介紹位置提供者和位置相關的權限。

### <a name="location-providers"></a>位置提供者

數種技術是在內部用來找出使用者的位置。 類型取決於所使用的硬體*位置提供者*選取之收集資料的作業。 Android 使用三個位置提供者：

-   **GPS 提供者** &ndash; GPS 提供最精確的位置，會使用最佳的能力，和室外可達效果最好。 此提供者使用 GPS 和輔助的 GPS 的組合 ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS))，它會傳回 GPS 行動電話塔台所收集的資料。

-   **網路提供者**&ndash;提供 WiFi 和行動電話通訊使用的資料，包括收集的資料格塔 aGPS 資料的組合。 它會使用比 GPS 提供者的電力較少，但是會傳回位置資料的各種不同的精確度。

-   **被動的提供者**&ndash;使用要求的其他應用程式或服務提供者來產生位置資料的應用程式中的 「 承載 」 選項。 這是較不可靠但省電選項適合不需要工作的常數的位置更新應用程式。

位置提供者不一定可用。 例如，我們可能會想要用於應用程式中，GPS 但 GPS 可能會關閉在設定中，或裝置可能不完全 GPS。 如果找不到特定的提供者，就必須選擇該提供者可能會傳回`null`。

### <a name="location-permissions"></a>位置的權限

定位感知應用程式需要存取裝置的硬體感應器用來接收 GPS、 Wi-fi、 和行動電話通訊的資料。 存取控制是透過應用程式的 Android 資訊清單中的適當權限。
有兩個權限&ndash;根據您的應用程式需求和您所選擇的 API，您會想要允許其中一個：

-   `ACCESS_FINE_LOCATION` &ndash; 允許 GPS 應用程式存取。
    所需的*GPS 提供者*和*被動的提供者*選項 (*被動的提供者需要權限以存取另一個應用程式或服務所收集的 GPS 資料*)。 選擇性的權限*網路提供者*。

-   `ACCESS_COARSE_LOCATION` &ndash; 可讓應用程式存取電話和 Wi-fi 的位置。 所需的*網路提供者*如果`ACCESS_FINE_LOCATION`未設定。

針對 API 21 （Android 5.0 棒棒糖符號） 的版本為目標的應用程式或更新版本中，您可以啟用`ACCESS_FINE_LOCATION`和仍然沒有 GPS 硬體的裝置上執行。 如果您的應用程式需要 GPS 硬體，您應該明確加入`android.hardware.location.gps` `uses-feature` Android 資訊清單的項目。 如需詳細資訊，請參閱 Android[使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)項目參考。

若要設定權限，展開**屬性**資料夾中的**方案板**按兩下**AndroidManifest.xml**。 權限將會列在**必要的使用權限**:

[![Android 資訊清單所需權限設定的螢幕擷取畫面](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

設定這些權限時，會告訴 Android 應用程式需要從使用者的權限，才能存取的位置提供者。 裝置，執行應用程式開發介面層級 22 (Android 5.1) 或更低會要求使用者安裝應用程式每次授與這些權限。 上執行應用程式開發介面裝置層級 23 (Android 6.0) 或更高版本，應用程式應該執行前提出要求的位置提供者的執行階段權限檢查。 

> [!NOTE]
>注意： 設定`ACCESS_FINE_LOCATION`意指粗略和正常位置的資料，資料存取。 您應該永遠不需要設定這兩個權限，只有*最少*您的應用程式運作所需的權限。

這個程式碼片段是如何檢查應用程式具有的權限的範例`ACCESS_FINE_LOCATION`權限：

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

應用程式必須成為可容忍的案例，其中使用者未授與權限 （或已撤銷的權限），並可正常地處理這種情況的方式。 請參閱[權限指南](~/android/app-fundamentals/permissions.md)如需有關實作執行階段權限檢查 Xamarin.Android 中。


## <a name="using-the-fused-location-provider"></a>使用合成的位置提供者

合成的位置提供者會從裝置接收位置更新，因為它有效率地將選取的位置提供者來提供最佳的位置資訊，以節省電池的方式執行階段期間的 Android 應用程式的慣用的方式。 例如，查核種天氣著實周圍的使用者取得讀取與 GPS 的最佳位置。 如果使用者再一一查核室內，其中 GPS 的運作狀況不佳 （如果有的話），合成的位置提供者可能會自動切換至 WiFi，像是室內的運作更好。
 
合成的位置提供者 API 提供各式各樣的其他工具，以確保能夠定位感知應用程式，包括地理圍欄與活動監視。 在本節中，我們將焦點設定的基本概念`LocationClient`、 建立提供者，以及取得使用者的位置。

合成的位置提供者是一部分[Google Play 服務](http://developer.android.com/google/play-services/index.html)。 Google Play 服務封裝必須安裝並正確設定在合成的位置提供者 API 的應用程式，才能執行，而且裝置必須具有 Google 播放服務 APK 安裝。

Xamarin.Android 之前應用程式可以使用合成的位置提供者，它必須加入**Xamarin.GooglePlayServices.Maps**至專案。

### <a name="checking-if-google-play-services-is-installed"></a>檢查是否已安裝 Google Play 服務

Xamarin.Android 會當機，如果它嘗試使用合成的位置提供者未安裝 Google Play 服務時 （或已過期），則執行階段例外狀況就會發生。  如果未安裝 Google Play 服務，然後在應用程式應該改為 Android 上面所討論的位置服務。 如果 Google Play 服務已過期，然後應用程式無法顯示訊息給使用者要求他們更新安裝的 Google Play 服務的版本。

這個程式碼片段是如何 Android 的活動可以透過程式設計方式檢查是否已安裝 Google Play 服務的範例：

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

若要與合成的位置提供者互動，Xamarin.Android 應用程式必須擁有的執行個體`FusedLocationProviderClient`。 這個類別會公開訂閱的位置更新，以及擷取裝置的最後一個已知的位置所需的方法。

`OnCreate`活動的方法是取得參考適合放置`FusedLocationProviderClient`，如下列程式碼片段所示：

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

`FusedLocationProviderClient.GetLastLocationAsync()`方法提供簡單、 非封鎖方式，快速取得裝置的額外負荷最小的程式碼的最後一個已知的位置 Xamarin.Android 應用程式。

這個程式碼片段示範如何使用`GetLastLocationAsync`方法來擷取裝置的位置：

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

### <a name="subscribing-to-location-updates"></a>更新訂閱的位置

Xamarin.Android 應用程式也可以位置更新訂閱者使用合成的位置`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法，此程式碼片段所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
這個方法會採用兩個參數：

-   **`Android.Gms.Location.LocationRequest`** &ndash; A`LocationRequest`物件是 Xamarin.Android 應用程式上合成的位置提供者的運作方式所傳遞的參數。 `LocationRequest`保留資訊這類常見的要求應該可以或應該是重要的精確位置更新。 比方說，重要的位置要求會導致裝置使用 GPS，並因此更多的能力，決定位置時。 此程式碼片段示範如何建立`LocationRequest`高精確度的位置，請檢查大約每隔五分鐘位置更新 （但不比要求之間的兩分鐘的更快）。 合成的位置提供者會使用`LocationRequest`作為要判定裝置位置時嘗試使用哪一個位置提供者的指引：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; 若要接收位置更新，Xamarin.Android 應用程式必須子類別化`LocationProvider`抽象類別。 這個類別會公開合成的位置提供者位置資訊更新的應用程式可能已經叫用兩種方法。 這將以下更詳細討論。

若要通知的位置更新 Xamarin.Android 應用程式，將會叫用合成的位置提供者`LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult`參數會包含更新的位置資訊。

當合成的位置提供者偵測到變更，位置資料的可用性時，它會呼叫`LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)`方法。 如果`LocationAvailability.IsLocationAvailable`屬性會傳回`true`，則可以假定所報告裝置位置結果`OnLocationResult`準確且為最新狀態所需的`LocationRequest`。 如果`IsLocationAvailable`沒有位置的結果將會傳回為 false， `OnLocationResult`。

此程式碼片段是以範例的實作`LocationCallback`物件：

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

Android 的位置服務是較舊的 API 在 Android 上使用位置資訊。 是由硬體感應器收集位置資料，並將其存取的應用程式中的系統服務所收集`LocationManager`類別和`ILocationListener`。

位置服務是最適合用來在沒有安裝的 Google Play 服務的裝置必須執行的應用程式。

位置服務是一種特殊型別的[服務](http://developer.android.com/guide/components/services.html)受到系統管理。 系統服務與裝置硬體互動，而是一律執行。 若要挖掘我們的應用程式中的位置更新，我們將位置更新從訂閱系統使用位置服務`LocationManager`和`RequestLocationUpdates`呼叫。

若要取得使用者的位置使用 Android 位置服務包含數個步驟：

1.  取得參考`LocationManager`服務。
2.  實作`ILocationListener`位置變更時，介面並處理事件。
3.  使用`LocationManager`要求位置更新指定的提供者。 `ILocationListener`從上一個步驟將會用來接收回呼`LocationManager`。
4.  停止應用程式就無法再接收更新的適當位置更新。

### <a name="location-manager"></a>位置管理員

我們可以存取的執行個體的系統位置服務`LocationManager`類別。 `LocationManager` 是特殊類別，讓我們與系統位置服務互動，以及在其上呼叫方法。 應用程式可以取得參考`LocationManager`藉由呼叫`GetSystemService`並傳入服務類型，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 取得參考適合`LocationManager`。
最好保留`LocationManager`當做類別變數，可讓我們在活動的生命週期的各個點上呼叫。

### <a name="request-location-updates-from-the-locationmanager"></a>從 LocationManager 要求位置更新

一旦應用程式有某個參照指向`LocationManager`，它必須告訴`LocationManager`位置資訊的類型所需的而且這些資訊也要更新的頻率。 執行這項操作，藉由呼叫`RequestionLocationUpdates`上`LocationManager`物件，並傳入更新和回呼將接收位置更新一些準則。 此回呼是型別必須實作`ILocationListener`介面 （在本指南稍後將詳細說明）。

`RequestionLocationUpdates`方法會告訴系統位置服務，您的應用程式想要開始接收位置更新。 這個方法可讓您指定的提供者，以及時間和距離臨界值來控制更新頻率。 例如，以下要求位置下的方法更新從 GPS 位置提供者每隔 2000年毫秒，只有當位置變更多個 1 metre 和：

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

應用程式應該只視需要，應用程式才能執行要求的位置更新。 這會保留電池壽命，並建立使用者更好的體驗。

### <a name="responding-to-updates-from-the-locationmanager"></a>回應 LocationManager 更新

一旦應用程式已經要求更新從`LocationManager`，它可以從服務收到資訊，藉由實作[ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)介面。 這個介面會提供四種方法來接聽以服務的位置和位置提供者， `OnLocationChanged`。 系統會呼叫`OnLocationChanged`當使用者的位置變更足以成為位置的變更，根據要求位置更新時所設定的準則。 

下列程式碼將示範中的方法`ILocationListener`介面：

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

為了節省系統資統，應用程式應該取消訂閱位置更新儘速。 `RemoveUpdates`方法會告知`LocationManager`停止將更新傳送至我們的應用程式。  例如，活動可以呼叫`RemoveUpdates`中`OnPause`方法，因此可以節省電力，如果應用程式不需要位置更新其活動不是在螢幕上時：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果您的應用程式需要取得在背景中的位置更新，您會想要建立訂閱的系統位置服務的自訂服務。 請參閱[Backgrounding 與 Android 服務](~/android/app-fundamentals/services/index.md)指南以取得詳細的資訊。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>判斷 LocationManager 的最佳位置提供者

上述應用程式設定 GPS 為位置的提供者。 不過，GPS 可能無法在所有情況下，例如裝置是否室內或沒有 GPS 接收端。 如果這種情況，則結果是`null`傳回提供者。

若要取得您的應用程式運作 GPS 無法使用時，您使用`GetBestProvider`尋求最佳的可用 （支援的裝置和使用者啟用） 的位置提供者，在啟動應用程式的方法。 而不是特定的提供者中傳遞，則可以告知`GetBestProvider`-例如精確度和電源-提供者的需求[`Criteria`物件](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)。 `GetBestProvider` 傳回給定準則的最佳提供者。

下列程式碼會示範如何取得最佳可用的提供者並要求位置更新時使用它：

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
>  如果使用者已經停用所有位置提供者，`GetBestProvider`會傳回`null`。 若要查看此程式碼在實際裝置上的運作方式，請務必啟用 GPS、 Wi-fi、 以及在行動電話通訊網路**Google 設定 > 位置 > 模式**這個螢幕擷取畫面所示：

[![在 Android 手機上的設定位置模式畫面](location-images/location-02.png)](location-images/location-02.png#lightbox)

下列螢幕擷取畫面示範位置應用程式正在執行使用`GetBestProvider`:

[![GetBestProvider 應用程式顯示緯度、 經度和提供者](location-images/location-03.png)](location-images/location-03.png#lightbox)

請注意，`GetBestProvider`不會動態變更提供者。 相反地，它會判斷最佳可用的提供者的活動生命週期當中，一次。 如果提供者狀態變更已設定之後，應用程式會要求額外的程式碼中`ILocationListener`方法&ndash; `OnProviderEnabled`， `OnProviderDisabled`，和`OnStatusChanged`&ndash;來處理每個相關的可能性提供者參數。

## <a name="summary"></a>總結

本指南涵蓋取得使用者的位置使用 Android 的位置服務和合成的位置提供者，從 Google 位置服務 API。


## <a name="related-links"></a>相關連結

- [位置 （範例）](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (sample)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play 服務](http://developer.android.com/google/play-services/index.html)
- [準則類別](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [LocationManager 類別](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [LocationListener 類別](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient 應用程式開發介面](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener 應用程式開發介面](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest 應用程式開發介面](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
