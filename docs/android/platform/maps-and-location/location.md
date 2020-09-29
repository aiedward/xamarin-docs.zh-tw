---
title: Android 上的位置服務
description: 本指南介紹 Android 應用程式中的位置感知，並說明如何使用 Android 位置服務 API 取得使用者的位置，以及使用 Google 位置服務 API 提供的融合位置提供者。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 37bfed9a2b49be2d04f58dab70d3185418f0f67d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457467"
---
# <a name="location-services-on-android"></a>Android 上的位置服務

_本指南介紹 Android 應用程式中的位置感知，並說明如何使用 Android 位置服務 API 取得使用者的位置，以及使用 Google 位置服務 API 提供的融合位置提供者。_

Android 可讓您存取各種不同的位置技術，例如資料格位置、Wi-fi 和 GPS。 每個位置技術的詳細資料都是透過 *位置提供者*進行抽象化，讓應用程式可以使用相同的方式取得位置，而不論使用的是哪一個提供者。 本指南介紹融合的位置提供者，這是 Google Play Services 的一部分，可根據提供的提供者和裝置的使用方式，以智慧方式決定取得裝置位置的最佳方式。 Android 位置服務 API，並說明如何使用來與系統位置服務通訊 `LocationManager` 。 本指南的第二個部分會使用來探索 Android 定位服務 API `LocationManager` 。

一般的經驗法則是，應用程式應該偏好使用融合的位置提供者，只在必要時才回復較舊的 Android 位置服務 API。

## <a name="location-fundamentals"></a>位置基礎

在 Android 中，不論您選擇何種 API 來處理位置資料，有幾個概念仍維持不變。 本節介紹位置提供者和位置相關許可權。

### <a name="location-providers"></a>位置提供者

在內部使用數種技術來指出使用者的位置。 使用的硬體取決於為收集資料所選取的 *位置提供者* 類型。 Android 使用三個位置提供者：

- **GPS 提供者** &ndash; GPS 提供最準確的位置、使用最強大的功能，且最適合室外。 此提供者會使用 GPS 和輔助 GPS ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)) 的組合，以傳回行動電話塔所收集的 gps 資料。

- **網路提供者** &ndash; 提供 WiFi 和 Cellular data 的組合，包括資料格 aGPS 所收集的資料。 它使用的電源比 GPS 提供者少，但會傳回不同精確度的位置資料。

- **被動式提供者** &ndash; 使用其他應用程式或服務所要求的提供者在應用程式中產生位置資料的「可參與」選項。 這是較不可靠但省電的選項，適用于不需要常數位置更新即可運作的應用程式。

位置提供者不一定可以使用。 例如，我們可能會想要將 GPS 用於我們的應用程式，但 GPS 可能會在設定中關閉，或者裝置可能完全沒有 GPS。 如果無法使用特定提供者，則選擇該提供者可能會傳回 `null` 。

### <a name="location-permissions"></a>位置許可權

定位感知的應用程式需要存取裝置的硬體感應器，才能接收 GPS、Wi-fi 和行動資料。 存取權是透過應用程式的 Android 資訊清單中適當的許可權來控制。
&ndash;根據您的應用程式需求和您選擇的 API 而定，有兩個可用的許可權，您會想要允許一個：

- `ACCESS_FINE_LOCATION`&ndash;允許應用程式存取 GPS。
    *Gps 提供者*和*被動式提供*者選項的必要項 (*被動提供者需要存取另一個應用程式或服務) 所收集之 GPS 資料的許可權*。 *網路提供者*的選擇性許可權。

- `ACCESS_COARSE_LOCATION`&ndash;允許應用程式存取行動電話通訊和 wi-fi 位置。 如果未設定，則為 *網路提供者* 所需 `ACCESS_FINE_LOCATION` 。

針對以 API 第21版 (Android 5.0 棒) 或更高版本為目標的應用程式，您可以啟用 `ACCESS_FINE_LOCATION` ，仍可在沒有 GPS 硬體的裝置上執行。 如果您的應用程式需要 GPS 硬體，您應該明確地將 `android.hardware.location.gps` `uses-feature` 元素新增至 Android 資訊清單。 如需詳細資訊，請參閱 Android [使用-功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) 元素參考。

若要設定許可權，請展開**Solution Pad**中的 [**屬性**] 資料夾，然後按兩下 [ **AndroidManifest.xml**]。 許可權會列在 [ **必要許可權**] 底下：

[![Android 資訊清單所需許可權設定的螢幕擷取畫面](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

設定這些許可權的其中之一，會告知 Android 您的應用程式需要使用者的許可權，才能存取位置提供者。 執行 API 層級 22 (Android 5.1) 或更低版本的裝置，會要求使用者在每次安裝應用程式時授與這些許可權。 在執行 API 層級 23 (Android 6.0) 或更高版本的裝置上，應用程式應該在提出位置提供者要求之前，先執行執行時間許可權檢查。 

> [!NOTE]
>注意：設定 `ACCESS_FINE_LOCATION` 意指對粗略和微調位置資料的存取。 您永遠都不需要設定這兩個許可權，只有您的應用程式所需的 *最基本* 許可權才能運作。

此程式碼片段是如何檢查應用程式是否有許可權的範例 `ACCESS_FINE_LOCATION` ：

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

應用程式必須能夠容忍使用者不會授與許可權 (或已撤銷許可權) 的情況，並有方法可正常處理該情況。 如需在 Xamarin 中實施執行時間許可權檢查的詳細資訊，請參閱 [許可權指南](~/android/app-fundamentals/permissions.md) 。

## <a name="using-the-fused-location-provider"></a>使用融合的位置提供者

預先融合的位置提供者是 Android 應用程式接收來自裝置之位置更新的慣用方式，因為它會在執行時間有效率地選取位置提供者，以電池效率的方式提供最佳的位置資訊。 例如，使用者在戶外進行流覽時，會取得最佳的位置以 GPS 閱讀。 如果使用者接著走到室內，其中 GPS 的運作效能不佳 (如果在所有) ，則融合的位置提供者可能會自動切換至 WiFi，以更好的室內運作。

融合的位置提供者 API 提供了各種不同的工具，可讓定位感知的應用程式（包括地理柵欄和活動監視）。 在本節中，我們將著重于設定 `LocationClient` 、建立提供者以及取得使用者位置的基本概念。

融合的位置提供者是 [Google Play Services](https://developer.android.com/google/play-services/index.html)的一部分。
您必須在應用程式中正確安裝並設定 Google Play Services 套件，才能讓融合的位置提供者 API 運作，而且裝置必須安裝 Google Play Services APK。

在 Xamarin Android 應用程式可以使用已融合的位置提供者之前，必須先將 **>xamarin.googleplayservices.base** 新增至專案。 此外，下列 `using` 語句應該加入至任何參考下列類別的原始程式檔：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>正在檢查是否已安裝 Google Play Services

如果 Google Play Services 未安裝 (或已) 過期，則 Xamarin 會在嘗試使用融合的位置提供者時損毀，否則會發生執行時間例外狀況。  如果未安裝 Google Play Services，則應用程式應該切換回上述的 Android 位置服務。 如果 Google Play Services 已過期，則應用程式可能會向使用者顯示訊息，要求他們更新已安裝的 Google Play Services 版本。

此程式碼片段是 Android 活動如何以程式設計方式檢查是否已安裝 Google Play Services 的範例：

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

若要與融合的位置提供者互動，Xamarin. Android 應用程式必須具有的實例 `FusedLocationProviderClient` 。 此類別會公開訂閱位置更新的必要方法，以及取得裝置的最後已知位置。

`OnCreate`活動的方法是取得參考的適當位置 `FusedLocationProviderClient` ，如下列程式碼片段所示：

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

`FusedLocationProviderClient.GetLastLocationAsync()`方法提供簡單、未封鎖的方式，讓 Xamarin. Android 應用程式以最少量的程式碼額外負荷快速取得裝置的最後一個已知位置。

此程式碼片段說明如何使用 `GetLastLocationAsync` 方法來取出裝置的位置：

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

Xamarin. Android 應用程式也可以使用方法，從融合的位置提供者訂閱位置更新 `FusedLocationProviderClient.RequestLocationUpdatesAsync`  ，如下列程式碼片段所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

這個方法會採用兩個參數：

- **`Android.Gms.Location.LocationRequest`**&ndash; `LocationRequest` 物件是 Xamarin. Android 應用程式如何傳遞有關融合位置提供者應如何運作的參數。 `LocationRequest`保存資訊，例如應該如何提出要求，或正確位置更新的重要性。 例如，重要的位置要求會導致裝置使用 GPS，因此在決定位置時，會造成更多的威力。 此程式碼片段會示範如何建立 `LocationRequest` 具有高精確度的位置、大約每五分鐘檢查一次位置更新 (，但在要求) 之間的時間不會超過兩分鐘。 融合的位置提供者將會使用做為指導方針，以在 `LocationRequest` 嘗試判斷裝置位置時使用哪個位置提供者：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`** 為了 &ndash; 接收位置更新，Xamarin. Android 應用程式必須將抽象類別設為子類別 `LocationProvider` 。 此類別會公開兩個方法，這些方法可能由融合的位置提供者叫用，以使用位置資訊來更新應用程式。 以下將詳細討論這項資訊。

為了通知 Xamarin. Android 應用程式的位置更新，已融合的位置提供者將會叫用 `LocationCallBack.OnLocationResult(LocationResult result)` 。 `Android.Gms.Location.LocationResult`參數將包含更新位置資訊。

當融合的位置提供者偵測到位置資料的可用性有變更時，就會呼叫 `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` 方法。 如果 `LocationAvailability.IsLocationAvailable` 屬性傳回 `true` ，則可以假設所報告的裝置位置結果，其為 `OnLocationResult` 所需的正確和最新狀態 `LocationRequest` 。 如果 `IsLocationAvailable` 為 false，則不會傳回任何位置結果 `OnLocationResult` 。

此程式碼片段是物件的範例執行 `LocationCallback` ：

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

Android 位置服務是在 Android 上使用位置資訊的較舊 API。 位置資料是由硬體感應器收集並由系統服務所收集，而系統服務則是在應用程式中使用類別和來進行存取 `LocationManager` `ILocationListener` 。

位置服務最適合必須在未安裝 Google Play Services 的裝置上執行的應用程式。

位置服務是由系統所管理的特殊 [服務](https://developer.android.com/guide/components/services.html) 類型。 系統服務會與裝置硬體互動，而且一律在執行中。 若要在我們的應用程式中使用位置更新，我們將使用和呼叫來訂閱系統位置服務的位置更新 `LocationManager` `RequestLocationUpdates` 。

若要使用 Android 位置服務取得使用者的位置，您需要執行幾個步驟：

1. 取得服務的參考 `LocationManager` 。
2. `ILocationListener`當位置變更時，請執行介面並處理事件。
3. 使用 `LocationManager` 來要求指定提供者的位置更新。 `ILocationListener`上一個步驟中的會用來接收來自的回呼 `LocationManager` 。
4. 當應用程式不再適合用來接收更新時，停止位置更新。

### <a name="location-manager"></a>位置管理員

我們可以使用類別的實例來存取系統位置服務 `LocationManager` 。 `LocationManager` 是一個特殊類別，可讓我們與系統位置服務互動，並在其上呼叫方法。 應用程式可以藉 `LocationManager` 由呼叫 `GetSystemService` 和傳遞服務類型來取得的參考，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 是取得參考的絕佳位置 `LocationManager` 。
最好將 `LocationManager` 作為類別變數，讓我們可以在活動生命週期的各個點上呼叫它。

### <a name="request-location-updates-from-the-locationmanager"></a>從 LocationManager 要求位置更新

一旦應用程式具有的參考 `LocationManager` ，它就必須知道 `LocationManager` 需要何種類型的位置資訊，以及更新該資訊的頻率。 若要這麼做 `RequestLocationUpdates` `LocationManager` ，請在物件上呼叫，並傳入更新的某些條件，以及接收位置更新的回呼。 此回呼是必須執行介面的類型， `ILocationListener` (本指南稍後的詳細說明) 。

`RequestLocationUpdates`方法會告訴系統位置服務您的應用程式想要開始接收位置更新。 這個方法可讓您指定提供者，以及用來控制更新頻率的時間和距離閾值。 例如，下列方法會每隔2000毫秒要求來自 GPS 位置提供者的位置更新，而且只有在位置變更超過1米時：

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

應用程式應該只要求應用程式正常執行所需的位置更新。 這會保留電池壽命，並為使用者創造更好的體驗。

### <a name="responding-to-updates-from-the-locationmanager"></a>回應 LocationManager 的更新

當應用程式從要求更新之後 `LocationManager` ，就可以藉由執行介面從服務接收資訊 [`ILocationListener`](xref:Android.Locations.ILocationListener) 。 此介面提供四種方法來接聽位置服務和位置提供者 `OnLocationChanged` 。 `OnLocationChanged`當使用者的位置有足夠的變更，可根據要求位置更新時所設定的準則來限定位置變更時，系統將會呼叫。 

下列程式碼顯示介面中的方法  `ILocationListener` ：

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

### <a name="unsubscribing-to-locationmanager-updates"></a>取消訂閱以 LocationManager 更新

為了節省系統資源，應用程式應該儘快取消位置更新的訂閱。 `RemoveUpdates`方法會指示 `LocationManager` 停止將更新傳送至我們的應用程式。  例如，活動可能會 `RemoveUpdates` 在方法中呼叫，  `OnPause` 如此一來，當應用程式不需要更新位置，而其活動不在畫面上時，就可以節省電源：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果您的應用程式在背景中需要取得位置更新，您會想要建立訂閱系統位置服務的自訂服務。 如需詳細資訊，請參閱 [背景處理 With Android Services](~/android/app-fundamentals/services/index.md) 指南。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>判斷 LocationManager 的最佳位置提供者

上述應用程式會將 GPS 設定為位置提供者。 不過，GPS 可能無法在所有情況下使用，例如裝置為室內或沒有 GPS 接收器。 如果是這種情況，則結果會傳回 `null` 給提供者。

若要讓您的應用程式在 GPS 無法使用時正常運作，您可以使用 `GetBestProvider` 方法在應用程式啟動時，要求支援裝置的最佳 (裝置支援和使用者啟用的) 位置提供者。 您可以告知 `GetBestProvider` 提供者的需求，例如[ `Criteria` 物件](xref:Android.Locations.Criteria)的精確度和電源，而不是傳入特定的提供者。 `GetBestProvider` 傳回指定準則的最佳提供者。

下列程式碼示範如何取得最佳的可用提供者，並在要求位置更新時使用它：

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
> 如果使用者已停用所有位置提供者， `GetBestProvider` 則會傳回 `null` 。 若要查看此程式碼在真實裝置上的運作方式，請務必在 [Google 設定] 下啟用 GPS、Wi-fi 和行動電話通訊網路， **> 位置 > 模式** ，如下列螢幕擷取畫面所示：
>
> [![Android 手機上的設定位置模式畫面](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> 下列螢幕擷取畫面示範使用執行的位置應用程式 `GetBestProvider` ：
>
> [![顯示緯度、經度和提供者的 GetBestProvider 應用程式](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> 請記住，不 `GetBestProvider` 會動態變更提供者。 相反地，它會決定在活動生命週期內最適合使用的提供者一次。 如果提供者狀態在設定之後變更，應用程式將需要方法中的額外程式碼 `ILocationListener` ， &ndash; `OnProviderEnabled` `OnProviderDisabled` 以及處理與 `OnStatusChanged` &ndash; 提供者參數相關的所有可能性。

## <a name="summary"></a>摘要

本指南說明如何使用來自 Google 位置服務 API 的 Android 位置服務和融合的位置提供者，來取得使用者的位置。

## <a name="related-links"></a>相關連結

- [位置 (範例) ](/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (範例) ](/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play 服務](https://developer.android.com/google/play-services/index.html)
- [準則類別](xref:Android.Locations.Criteria)
- [LocationManager 類別](xref:Android.Locations.LocationManager)
- [LocationListener 類別](xref:Android.Locations.ILocationListener)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)