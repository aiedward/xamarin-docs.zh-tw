---
title: 位置服務
description: 本指南介紹 Android 應用程式中的位置感知，並說明如何取得 Google 的位置服務 API 中使用 Android 的位置服務 API，以及可用的積的位置提供者的使用者的位置。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: e3cfc9a345c8ab92b35ad428b550ec42de6312e5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120290"
---
# <a name="location-services"></a>位置服務

_本指南介紹 Android 應用程式中的位置感知，並說明如何取得 Google 的位置服務 API 中使用 Android 的位置服務 API，以及可用的積的位置提供者的使用者的位置。_

## <a name="location-services-overview"></a>位置服務概觀

Android 提供各種不同的位置技術，例如儲存格塔台位置、 Wi-fi 和 GPS 的存取。 每個位置技術的詳細資料會透過抽象化*位置提供者*，讓應用程式取得相同的方式，不論使用的提供者的位置。 本指南介紹積的位置提供者，Google Play 服務，以智慧方式決定若要取得根據哪些提供者可以使用，以及如何使用裝置的裝置的位置，最好的一部分。 Android 的位置服務 API，並說明如何彼此通訊的系統位置服務使用`LocationManager`。 本指南的第二部分探討 Android 位置服務 API 使用`LocationManager`。
 
一般的經驗法則，應用程式應該偏好使用積的位置提供者，切換回舊版 Android 位置服務 API 只在必要時。

## <a name="location-fundamentals"></a>位置的基本概念

在 Android 中，不論您選擇使用位置資料的哪些 API 的幾個概念維持不變。 本節將介紹位置提供者和位置與相關的權限。

### <a name="location-providers"></a>位置提供者

數種技術是在內部用來找出使用者的位置。 類型取決於所使用的硬體*位置提供者*選取收集資料的作業。 Android 會使用三個位置提供者：

-   **GPS 的提供者** &ndash; GPS 提供最精確的位置、 使用的最大的電力，及最適合戶外活動。 此提供者會使用 GPS 以及輔助的 GPS 的組合 ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS))，它會傳回行動電話塔台所收集的 GPS 資料。

-   **網路提供者**&ndash;提供的 WiFi 和行動數據使用的資料，包括收集的資料格 towers aGPS 資料組合。 它使用較少的電量比 GPS 提供者，但會傳回位置資料的各種不同的精確度。

-   **被動的提供者**&ndash;使用由其他應用程式或服務提供者來產生應用程式中的位置資料的 「 承載 」 選項。 這是較不可靠但省電選項適合不需要運作的常數的位置更新的應用程式。

位置提供者不一定可用。 比方說，我們可能會想要使用我們的應用程式的 GPS，但 GPS 可能已關閉，在 [設定]，或裝置可能沒有 GPS 完全。 如果找不到特定的提供者，就必須選擇該提供者可能會傳回`null`。

### <a name="location-permissions"></a>位置的權限

位置感知的應用程式需要存取裝置的硬體感應器接收 GPS、 Wi-fi 及行動數據。 存取控制是透過應用程式的 Android 資訊清單中的適當權限。
有兩個權限&ndash;根據您的應用程式需求和您選擇的 API，您會想要允許其中一個：

-   `ACCESS_FINE_LOCATION` &ndash; 允許 GPS 應用程式存取。
    所需*GPS 提供者*並*被動的提供者*選項 (*被動的提供者需要存取另一個應用程式或服務所收集的 GPS 資料的權限*)。 選擇性的權限*網路提供者*。

-   `ACCESS_COARSE_LOCATION` &ndash; 可讓應用程式存取行動數據和 Wi-fi 的位置。 所需*網路提供者*如果`ACCESS_FINE_LOCATION`未設定。

目標 API 版本為 21 (Android 5.0 Lollipop) 應用程式，或更新版本中，您可以啟用`ACCESS_FINE_LOCATION`和仍然沒有 GPS 硬體的裝置上執行。 如果您的應用程式需要 GPS 硬體，您應該明確新增`android.hardware.location.gps` `uses-feature` Android 資訊清單的項目。 如需詳細資訊，請參閱 Android[會使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)項目參考。

若要設定權限，展開**屬性**中的資料夾**Solution Pad** ，然後按兩下**AndroidManifest.xml**。 權限將會列在**必要的權限**:

[![Android 資訊清單所需權限設定的螢幕擷取畫面](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

設定這些權限，會告知 Android 應用程式必須從使用者的權限，才能存取的位置提供者。 裝置的 API 層級 22 (Android 5.1) 或執行較低會要求這些權限授與應用程式會安裝每次使用者。 執行 API 的裝置層級 23 (Android 6.0) 或更新版本中，應用程式應該執行的執行階段權限檢查，再提出要求的位置提供者。 

> [!NOTE]
>注意： 將`ACCESS_FINE_LOCATION`表示這兩個廣泛且沒問題的位置資料的存取權。 您應該永遠不需要設定這兩個權限，只有*最小*您的應用程式運作所需的權限。

此程式碼片段是如何檢查應用程式有權限的範例`ACCESS_FINE_LOCATION`權限：

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

應用程式必須成為可容忍其中使用者未授與權限 （或已撤銷的權限） 的案例，並有辦法應付這種情況下，依正常程序。 請參閱[權限指南](~/android/app-fundamentals/permissions.md)如需詳細資訊，在執行階段權限的實作會檢查在 Xamarin.Android 中。


## <a name="using-the-fused-location-provider"></a>使用積的位置提供者

積的位置提供者是慣用的 Android 應用程式，以接收來自裝置的位置更新，因為它會有效率地選取在執行階段提供最佳的位置資訊，以電池有效率的方式的位置提供者的方式。 比方說，出外四處戶外的使用者取得讀取含有 GPS 的最佳位置。 如果使用者再一一查核室內，其中 GPS 的運作不佳 （如果有的話），積的位置提供者可能會自動切換至 WiFi 運作更好的室內。
 
積的位置提供者 API 提供各種不同的其他工具，讓定位感知應用程式，包括地理柵欄和活動監視。 在本節中，我們將焦點設定的基本概念`LocationClient`、 建立提供者，以及取得使用者的位置。

積的位置提供者是一部分[Google Play 服務](http://developer.android.com/google/play-services/index.html)。
Google Play 服務套件必須安裝並在運作，積的位置提供者 API 的應用程式中正確設定，裝置必須擁有 Google Play Services APK 安裝。

之前的 Xamarin.Android 應用程式可以使用積的位置提供者，它必須新增**Xamarin.GooglePlayServices.Maps**至專案的封裝。 此外，下列`using`陳述式應該新增至參考的類別，如下所述的任何來源檔案：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>檢查是否已安裝 Google Play 服務

Xamarin.Android 會當機，如果它嘗試時未安裝 Google Play 服務，請使用積的位置提供者 （或過期），則執行階段例外狀況就會發生。  如果未安裝 Google Play 服務，然後應用程式應該改為 Android 上面所討論的位置服務。 Google Play 服務已過期，如果應用程式無法向使用者要求他們更新已安裝的版本的 Google Play 服務顯示訊息。

此程式碼片段是如何 Android 活動可以透過程式設計方式檢查是否已安裝 Google Play 服務的範例：

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

若要與積的位置提供者互動，Xamarin.Android 應用程式必須的執行個體`FusedLocationProviderClient`。 這個類別會公開訂閱位置更新，以及擷取裝置的最後一個已知的位置所需的方法。

`OnCreate`活動的方法是一個適合的位置，來取得參考`FusedLocationProviderClient`，如下列程式碼片段所示：

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

`FusedLocationProviderClient.GetLastLocationAsync()`方法提供簡單、 非封鎖方式，快速取得的裝置有額外負荷最少的程式碼的最後一個已知的位置為 Xamarin.Android 應用程式。

此程式碼片段示範如何使用`GetLastLocationAsync`方法來擷取裝置的位置：

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

Xamarin.Android 應用程式也可以訂閱位置更新從積的位置提供者使用`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法，此程式碼片段所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
這個方法會採用兩個參數：

-   **`Android.Gms.Location.LocationRequest`** &ndash; A`LocationRequest`物件是 Xamarin.Android 應用程式上積的位置提供者的運作方式所傳遞的參數。 `LocationRequest`保留資訊這類方式應該進行頻繁的要求，或應該是重要性的精確位置更新。 比方說，重要的位置要求會導致裝置使用 GPS，並因此更多的能力，判斷位置時。 此程式碼片段示範如何建立`LocationRequest`高精確度的位置，檢查大約每隔五分鐘位置更新 （但不超過兩分鐘的要求之間的更快）。 積的位置提供者會使用`LocationRequest`為位置提供者使用時，嘗試判斷裝置位置的指導方針：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; 若要接收位置更新，Xamarin.Android 應用程式必須子類別化`LocationProvider`抽象類別。 這個類別會公開積的位置提供者位置資訊更新的應用程式可能叫用兩種方法。 這將在下面詳細討論。

若要通知的位置更新的 Xamarin.Android 應用程式，將會叫用積的位置提供者`LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult`參數會包含更新位置資訊。

當積的位置提供者偵測到的位置資料可用性的變更時，它會呼叫`LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)`方法。 如果`LocationAvailability.IsLocationAvailable`屬性會傳回`true`，則可以假設所報告裝置位置結果`OnLocationResult`精確且為最新狀態所需的`LocationRequest`。 如果`IsLocationAvailable`為 false，則不會傳回任何位置結果`OnLocationResult`。

此程式碼片段是範例實作`LocationCallback`物件：

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

Android 的位置服務是在 Android 上使用位置資訊的較舊的 API。 是由硬體感應器收集位置資料，並將其收集的系統服務，即可存取應用程式中`LocationManager`類別和`ILocationListener`。

位置服務是最適合用於必須在沒有安裝的 Google Play 服務的裝置執行的應用程式。

位置服務是一種特殊型別的[服務](http://developer.android.com/guide/components/services.html)系統所管理。 系統服務與裝置硬體互動，而是一律執行。 若要善用我們的應用程式中的位置更新，我們將會訂閱位置更新從系統位置服務使用`LocationManager`和`RequestLocationUpdates`呼叫。

若要取得使用者的位置使用 Android 的位置服務牽涉到幾個步驟：

1.  取得參考`LocationManager`服務。
2.  實作`ILocationListener`位置變更時，介面並處理事件。
3.  使用`LocationManager`要求指定的提供者的位置更新。 `ILocationListener`從上一個步驟將會用來接收回呼從`LocationManager`。
4.  當應用程式適合不會再接收更新，請停止位置更新。

### <a name="location-manager"></a>位置管理員

我們可以存取的執行個體的系統位置服務`LocationManager`類別。 `LocationManager` 是特殊的類別，可讓我們的系統位置服務進行互動，並對它呼叫方法。 應用程式可以取得參考`LocationManager`藉由呼叫`GetSystemService`並傳入服務類型，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 取得參考適合`LocationManager`。
它是個不錯的主意，保留`LocationManager`為類別變數，可讓我們在活動開發週期中各個點呼叫。

### <a name="request-location-updates-from-the-locationmanager"></a>從 LocationManager 要求位置更新

參考應用程式一旦`LocationManager`，它需要向`LocationManager`何種位置資訊的需要，而且該資訊是要更新的頻率。 執行這項操作，藉由呼叫`RequestionLocationUpdates`上`LocationManager`物件，並傳入部分更新和回呼，會收到位置更新的準則。 此回呼是一種類型，必須實作`ILocationListener`（稍後在本指南詳細說明） 的介面。

`RequestionLocationUpdates`方法會告訴系統位置服務，您的應用程式想要開始接收位置更新。 這個方法可讓您指定的提供者，以及時間和距離的臨界值來控制更新頻率。 例如下, 面的要求位置底下的方法更新從 GPS 位置提供者每個 2000年毫秒為單位，並只有位置有所變更時超過 1 metre:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

應用程式應該只視需要正確執行應用程式要求位置更新。 這會保留電池壽命，並建立使用者更好的體驗。

### <a name="responding-to-updates-from-the-locationmanager"></a>更新回應來自 LocationManager

一旦應用程式已要求的更新`LocationManager`，它可以從服務收到資訊，藉由實作[ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)介面。 這個介面會提供四種方法來接聽服務的位置和位置提供者， `OnLocationChanged`。 系統會呼叫`OnLocationChanged`當使用者的位置變更不足以將位置變更，根據要求位置更新時所設定的準則。 

下列程式碼會顯示在方法`ILocationListener`介面：

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

為了節省系統資源，應用程式應該取消訂閱位置更新儘速。 `RemoveUpdates`方法會告訴`LocationManager`停止將更新傳送至我們的應用程式。  例如，活動可以呼叫`RemoveUpdates`在`OnPause`方法使我們能夠節省電力，如果應用程式不需要位置更新其活動不是在螢幕上時：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果您的應用程式需要取得在背景中的位置更新，您會想要建立訂閱的系統位置服務的自訂服務。 請參閱[背景與 Android 服務](~/android/app-fundamentals/services/index.md)指南以取得詳細的資訊。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>判斷 LocationManager 的最佳位置提供者

上述應用程式將設定 GPS 位置提供者。 不過，GPS 可能無法在所有情況下，例如如果裝置是室內，或沒有 GPS 接收器。 如果發生這種情況，結果是`null`傳回提供者。

若要取得您的應用程式運作 GPS 無法使用時，您使用`GetBestProvider`尋求最佳的可用 （支援的裝置和啟用使用者） 的位置提供者中，於應用程式啟動的方法。 在特定的提供者，而您所見`GetBestProvider`-例如正確度和電力-與提供者的需求[`Criteria`物件](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)。 `GetBestProvider` 傳回給定準則的最佳提供者。

下列程式碼示範如何取得最佳可用的提供者並要求位置更新時，請使用它：

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
>  如果使用者已停用所有的位置提供者`GetBestProvider`會傳回`null`。 若要查看此程式碼在實際裝置上的運作方式，一定要啟用 GPS、 Wi-fi 和行動電話通訊網路下的**Google 設定 > 位置 > 模式**這個螢幕擷取畫面所示：

[![在 Android 手機上的設定位置模式螢幕](location-images/location-02.png)](location-images/location-02.png#lightbox)

以下螢幕擷取畫面示範位置的應用程式執行的方式`GetBestProvider`:

[![顯示緯度、 經度和提供者的 GetBestProvider 應用程式](location-images/location-03.png)](location-images/location-03.png#lightbox)

請記住，`GetBestProvider`不會以動態方式變更提供者。 相反地，它會判斷最佳可用的提供者活動生命週期期間，一次。 如果提供者狀態變更時設定它之後，應用程式將會需要額外的程式碼中`ILocationListener`方法&ndash; `OnProviderEnabled`， `OnProviderDisabled`，並`OnStatusChanged`&ndash;來處理每個相關的可能性提供者參數。

## <a name="summary"></a>總結

本指南涵蓋了取得使用者的位置使用 Android 的位置服務和積的位置提供者，從 Google 位置服務 API。


## <a name="related-links"></a>相關連結

- [位置 （範例）](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider （範例）](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play 服務](http://developer.android.com/google/play-services/index.html)
- [準則類別](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [LocationManager 類別](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [LocationListener 類別](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
