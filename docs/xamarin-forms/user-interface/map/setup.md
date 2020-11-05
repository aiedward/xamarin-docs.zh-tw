---
title: Xamarin.Forms 對應初始化和設定
description: Xamarin.Forms。需要 maps NuGet 套件，才能在應用程式中使用地圖功能。 此外，存取使用者的位置，需要將位置許可權授與應用程式。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b84336f836c3fa421537daf7e43de01e7be20b01
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371283"
---
# <a name="no-locxamarinforms-map-initialization-and-configuration"></a>Xamarin.Forms 對應初始化和設定

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項會在每個平臺上使用原生地圖控制項。 這可為使用者提供快速且熟悉的地圖體驗，但表示需要一些設定步驟才能遵守每個平臺的 API 需求。

## <a name="map-initialization"></a>對應初始化

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是由提供的[ Xamarin.Forms 。對應](https://www.nuget.org/packages/Xamarin.Forms.Maps/)NuGet 套件，應新增至方案中的每個專案。

安裝之後[ Xamarin.Forms 。Map](https://www.nuget.org/packages/Xamarin.Forms.Maps/) NuGet 套件，必須在每個平臺專案中初始化。

在 iOS 上，這應該會在 **AppDelegate.cs** 中執行，方法是在 `Xamarin.FormsMaps.Init` 方法 *之後* 叫用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，在方法之後叫用方法，就會在 **MainActivity.cs** 中進行這項 `Xamarin.FormsMaps.Init` *after* `Xamarin.Forms.Forms.Init` 操作：

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

在通用 Windows 平臺 (UWP) 上，這應該會在 **MainPage.xaml.cs** 中叫用方法，方法是從函式叫用 `Xamarin.FormsMaps.Init` 方法 `MainPage` ：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

如需 UWP 所需驗證權杖的詳細資訊，請參閱 [通用 Windows 平臺](#universal-windows-platform)。

一旦新增 NuGet 套件，並在每個應用程式內呼叫初始化方法， `Xamarin.Forms.Maps` 即可在共用程式碼專案中使用 api。

## <a name="platform-configuration"></a>平臺設定

Android 上需要進行其他設定，而在地圖將會顯示通用 Windows 平臺 (UWP) 。 此外，在 iOS、Android 及 UWP 上，存取使用者的位置，需要將位置許可權授與應用程式。

### <a name="ios"></a>iOS

在 iOS 上顯示地圖並與其互動，不需要任何額外的設定。 不過，若要存取位置服務，您必須在 **Info. plist** 中設定下列索引鍵：

- iOS 11 和更新版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –用於在應用程式使用中時使用位置服務
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nslocationalwaysandwheninuseusagedescription) –適用于隨時使用位置服務
- iOS 10 及更早版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –用於在應用程式使用中時使用位置服務
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) –適用于隨時使用位置服務    

若要支援 iOS 11 和更早版本，您可以包含三個金鑰： `NSLocationWhenInUseUsageDescription` 、 `NSLocationAlwaysAndWhenInUseUsageDescription` 和 `NSLocationAlwaysUsageDescription` 。

這些索引鍵在 **plist** 中的 XML 表示如下所示。 您應更新這些 `string` 值，以反映您的應用程式如何使用位置資訊：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

編輯 **info. plist** 檔案時，也可以在 **來源** 視圖中加入 **plist** 專案：

![適用于 iOS 8 的 plist](setup-images/ios8-map-permissions.png "iOS 8 必要資訊。 plist 專案")

當應用程式嘗試存取使用者的位置、要求存取權時，就會出現提示：

[![IOS 上位置許可權要求的螢幕擷取畫面](setup-images/permission-ios.png "iOS 許可權要求")](setup-images/permission-ios-large.png#lightbox "iOS 許可權要求")

### <a name="android"></a>Android

在 Android 上顯示地圖和與之互動的設定程式如下：

1. 取得 Google Maps API 金鑰，並將它新增至資訊清單。
1. 在資訊清單中指定 Google Play 服務版本號碼。
1. 在資訊清單中指定 Apache HTTP 舊版程式庫的需求。
1. 參數在資訊清單中指定 WRITE_EXTERNAL_STORAGE 許可權。
1. 參數指定資訊清單中的位置許可權。
1. 參數要求類別中的執行時間位置許可權 `MainActivity` 。

如需正確設定之資訊清單檔的範例，請參閱範例應用程式中的 [AndroidManifest.xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) 。

#### <a name="get-a-google-maps-api-key"></a>取得 Google Maps API 金鑰

若要在 Android 上使用 [Google MAPS API](https://developers.google.com/maps/documentation/android/) ，您必須產生 API 金鑰。 若要這樣做，請依照 [取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)中的指示進行。

一旦取得 API 金鑰，就必須將其新增至 `<application>` **屬性/AndroidManifest.xml** 檔案的元素內：

```xml
<application ...>
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

這會將 API 金鑰內嵌到資訊清單中。 如果沒有有效的 API 金鑰， [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項將會顯示空白方格。

> [!NOTE]
> `com.google.android.geo.API_KEY` 是 API 金鑰的建議中繼資料名稱。 為了回溯相容性，您 `com.google.android.maps.v2.API_KEY` 可以使用中繼資料名稱，但只允許對 Android MAPS API v2 進行驗證。

若要讓您的 APK 存取 Google Maps，您必須針對每個金鑰儲存區加入 SHA-1 指紋和套件名稱 (debug 和 release) 用來簽署您的 APK。 例如，如果您使用一部電腦進行 debug，而另一部電腦用來產生 release APK，您應該在第一部電腦的 debug 金鑰儲存區中包含 SHA-1 憑證指紋，並在第二部電腦的版本金鑰儲存區中加入 SHA-1 憑證指紋。 如果應用程式的 **套件名稱** 有所變更，也請記得編輯金鑰認證。 請參閱 [取得 Google MAPS API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

#### <a name="specify-the-google-play-services-version-number"></a>指定 Google Play 服務版本號碼

在AndroidManifest.xml的元素內新增下列宣告 `<application>` ： **AndroidManifest.xml**

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

這會將用來編譯應用程式的 Google Play 服務版本內嵌到資訊清單中。

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>指定 Apache HTTP 舊版程式庫的需求

如果您的 Xamarin.Forms 應用程式是以 API 28 或更高版本為目標，您必須在AndroidManifest.xml的元素內新增下列宣告 `<application>` ： **AndroidManifest.xml**

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

這會告訴應用程式使用已從 Android 9 中移除的 Apache Http 用戶端程式庫 `bootclasspath` 。

#### <a name="specify-the-write_external_storage-permission"></a>指定 WRITE_EXTERNAL_STORAGE 許可權

如果您的應用程式是以 API 22 或更低版本為目標，則可能需要將 `WRITE_EXTERNAL_STORAGE` 許可權新增至資訊清單，以做為專案的子系 `<manifest>` ：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

如果您的應用程式以 API 23 或更新版本為目標，則不需要此項。

#### <a name="specify-location-permissions"></a>指定位置許可權

如果您的應用程式需要存取使用者的位置，您必須將 `ACCESS_COARSE_LOCATION` 或 `ACCESS_FINE_LOCATION` 許可權新增至資訊清單 (或) 的子系作為元素的子系，以要求許可權 `<manifest>` ：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

此 `ACCESS_COARSE_LOCATION` 許可權可讓 API 使用 WiFi 或行動資料（或兩者）來判斷裝置的位置。 這些 `ACCESS_FINE_LOCATION` 許可權可讓 API 使用全球定位系統 (GPS) 、WiFi 或行動資料來判斷盡可能精確的位置。

或者，您可以使用資訊清單編輯器來新增下列許可權，以啟用這些許可權：

- `AccessCoarseLocation`
- `AccessFineLocation`

這些會顯示在下列螢幕擷取畫面中：

![Android 的必要許可權](setup-images/android-map-permissions.png "Android 的必要許可權")

#### <a name="request-runtime-location-permissions"></a>要求執行時間位置許可權

如果您的應用程式是以 API 23 或更新版本為目標，且需要存取使用者的位置，則必須檢查它是否有執行時間所需的許可權，如果沒有，請要求它。 執行下列工作即可達成這點：

1. 在 `MainActivity` 類別中，新增下欄欄位：

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. 在 `MainActivity` 類別中，新增下列覆 `OnStart` 寫：

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    如果應用程式是以 API 23 或更高版本為目標，此程式碼會執行許可權的執行時間許可權檢查 `AccessFineLocation` 。 如果未授與許可權，則會呼叫方法來提出許可權要求 `RequestPermissions` 。

1. 在 `MainActivity` 類別中，新增下列覆 `OnRequestPermissionsResult` 寫：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    此覆寫會處理許可權要求的結果。

這段程式碼的整體效果是，當應用程式要求使用者的位置時，會顯示下列對話方塊來要求許可權：

[![Android 上位置許可權要求的螢幕擷取畫面](setup-images/permission-android.png "Android 許可權要求")](setup-images/permission-android-large.png#lightbox "Android 許可權要求")

### <a name="universal-windows-platform"></a>通用 Windows 平台

在 UWP 上，您的應用程式必須經過驗證，才能顯示地圖並使用地圖服務。 若要驗證您的應用程式，您必須指定對應的驗證金鑰。 如需詳細資訊，請參閱 [要求對應驗證金鑰](/windows/uwp/maps-and-location/authentication-key)。 然後，您應該在方法呼叫中指定驗證權杖 `FormsMaps.Init("AUTHORIZATION_TOKEN")` ，以使用 Bing 地圖服務來驗證應用程式。

> [!NOTE]
> 在 UWP 上，若要使用地理編碼之類的地圖服務，您也必須將 `MapService.ServiceToken` 屬性設定為驗證金鑰值。 您可以使用下列這行程式碼來完成這項作業： `Windows.Services.Maps.MapService.ServiceToken = "INSERT_AUTH_TOKEN_HERE";` 。

此外，如果您的應用程式需要存取使用者的位置，您必須啟用套件資訊清單中的位置功能。 執行下列工作即可達成這點：

1. 在 \[ **方案總管** \] 中按兩下 **package.appxmanifest** ，然後選取 \[ **功能** \] 索引標籤。
1. 在 **\[功能\]** 清單中，選取 **\[位置\]** 的方塊。 這會將 `location` 裝置功能新增至套件資訊清單檔案。

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>發行組建

UWP 版本組建使用 .NET 原生編譯，將應用程式直接編譯成機器碼。 不過，這是 [`Map`](xref:Xamarin.Forms.Maps.Map) 因為 UWP 上的控制項轉譯器可能會從可執行檔連結。 您可以使用 App.xaml.cs 中方法的 UWP 特定多載來修正此 `Forms.Init` 問題 **App.xaml.cs** ：

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

此程式碼會將類別所在的元件傳遞 `Xamarin.Forms.Maps.UWP.MapRenderer` 給 `Forms.Init` 方法。 這可確保元件不會由 .NET 原生編譯器連結到可執行檔。

> [!IMPORTANT]
> 若未執行此動作，就會導致控制項在執行 [`Map`](xref:Xamarin.Forms.Maps.Map) 發行組建時不會出現。

## <a name="related-links"></a>相關連結

- [Maps 範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms.地圖釘](~/xamarin-forms/user-interface/map/pins.md)選。
- [地圖 API](xref:Xamarin.Forms.Maps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)