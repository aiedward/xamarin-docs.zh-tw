---
title: Xamarin.Forms 對應
description: 這篇文章說明如何使用 Xamarin.Forms Map 類別在每個平台上使用原生的對應 Api，來提供熟悉對應的使用者體驗。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: cfa450c977ac9f1d6370f40d27f5d704cc774767
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526672"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms 對應

_Xamarin.Forms 會在每個平台上使用原生的對應 Api。_

Xamarin.Forms.Maps 會使用原生的對應 Api，每個平台。 這對於使用者而言，提供快速、 熟悉對應體驗，但表示一些設定步驟所需遵守每個平台特定的 API 需求。
設定後，`Map`控制運作方式就像一般的程式碼中的任何其他 Xamarin.Forms 元素。

* [對應初始化](#Maps_Initialization)-使用`Map`需要額外的初始化程式碼，在啟動時。
* [平台組態](#Platform_Configuration)-每個平台需要對應至工作的某些設定。
* [使用 C# 中的對應](#Using_Maps)-顯示對應，並固定使用 C#。
* [在 XAML 中使用對應](#Using_Xaml)-顯示具有 XAML 的對應。

地圖控制項用於[MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)範例，如下所示。

 [![MobileCRM 範例中的地圖](map-images/maps-zoom-sml.png "地圖控制項範例")](map-images/maps-zoom.png#lightbox "地圖控制項範例")

地圖功能可以藉由建立進一步強化[對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>對應初始化

將對應新增至 Xamarin.Forms 應用程式時**Xamarin.Forms.Maps**是個別的 NuGet 套件，您應該將它新增至方案中的每個專案。
在 Android 上，這也會相依於當您將新增 Xamarin.Forms.Maps 自動下載 GooglePlayServices (另一個 NuGet)。

每個應用程式專案，安裝 NuGet 套件之後，需要一些初始化程式碼*之後*`Xamarin.Forms.Forms.Init`方法呼叫。 為 iOS 請使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，您必須傳遞相同的參數`Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

適用於通用 Windows 平台 (UWP) 中使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

此呼叫在中新增每個平台的下列檔案：

-  **iOS** -在 AppDelegate.cs 檔案`FinishedLaunching`方法。
-  **Android** -在 MainActivity.cs 檔案`OnCreate`方法。
-  **UWP** -在 MainPage.xaml.cs 檔案`MainPage`建構函式。

當已加入 NuGet 套件，而且初始設定方法呼叫內每個應用程式， `Xamarin.Forms.Maps` Api 可用於一般的.NET Standard 程式庫專案或共用專案的程式碼。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台組態

地圖會顯示之前在某些平台需要額外的設定步驟。

### <a name="ios"></a>iOS

若要存取在 iOS 上的位置服務，您必須在中設定下列索引鍵**Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 當應用程式正在使用中時，請使用位置服務
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – 隨時使用位置服務
- iOS 10 及更早版本
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 當應用程式正在使用中時，請使用位置服務
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – 隨時使用位置服務    

若要支援 iOS 11 和更早版本，您可以包含所有的三個索引鍵： `NSLocationWhenInUseUsageDescription`， `NSLocationAlwaysAndWhenInUseUsageDescription`，和`NSLocationAlwaysUsageDescription`。

這些索引鍵中的 XML 表示法**Info.plist**如下所示。 您應該更新`string`值，以反映您的應用程式如何使用位置資訊：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

**Info.plist**也會在加入項目**來源**檢視，在編輯時**Info.plist**檔案：

![適用於 iOS 8 的 Info.plist](map-images/ios8-map-permissions.png "iOS 8 需要 Info.plist 項目")


### <a name="android"></a>Android

若要使用[Google Maps API v2](https://developers.google.com/maps/documentation/android/)在 Android 上，您必須產生 API 金鑰並將它新增至您的 Android 專案。
遵循上 Xamarin 文件中的指示[取得 Google Maps API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
遵循這些指示之後, 貼上中的 API 金鑰**properties/Androidmanifest.xml**檔案 （檢視原始檔並尋找/更新下列項目）：

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

如果沒有有效的 API 金鑰對應控制項會顯示為灰色方塊在 Android 上。

> [!NOTE]
> 請注意，為了讓您存取 Google 地圖的 APK，您必須包含 sha-1 指紋和封裝名稱的每個金鑰儲存區 （偵錯和發行），您用來簽署您的 APK。 比方說，如果您使用一部電腦進行偵錯和產生發行 APK 的另一部電腦時，您應該包含 sha-1 憑證指紋，從第一部電腦的偵錯金鑰儲存區和從發行金鑰儲存區的 sha-1 憑證指紋第二部電腦中。 也請務必編輯金鑰的認證，如果應用程式的**封裝名稱**變更。 請參閱[取得 Google Maps API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

您也需要啟用 Android 專案上按一下滑鼠右鍵，然後選取適當的權限**選項 > 建置 > Android 應用程式**和計時下列：

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

這些都是以以下螢幕擷取畫面所示：

![適用於 Android 的必要權限](map-images/android-map-permissions.png "適用於 Android 的必要權限")

這兩個所需的因為應用程式需要下載地圖資料的網路連線。 了解 Android[權限](http://developer.android.com/reference/android/Manifest.permission.html)若要深入了。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用通用 Windows 平台上的對應中，您必須產生的授權權杖。 如需詳細資訊，請參閱 <<c0> [ 要求對應的驗證金鑰](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)MSDN 上。

驗證權杖則應該指定在`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法呼叫中，驗證使用 Bing 地圖服務的應用程式。

<a name="Using_Maps" />

## <a name="using-maps"></a>使用 Maps

請參閱[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs)在 MobileCRM 範例中，如需如何使用地圖控制項，在程式碼中的範例。 簡單`MapPage`類別可能類似此-請注意，新`MapSpan`會建立以位置地圖的檢視：

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>對應類型

地圖內容也可以藉由設定變更`MapType`屬性，以顯示規則的街道地圖 （預設值）、 衛星影像或兩者的組合。

```csharp
map.MapType == MapType.Street;
```

有效`MapType`的值為：

-  混合式
-  附屬
-  Street （預設值）


### <a name="map-region-and-mapspan"></a>對應區域和 MapSpan

上述程式碼片段所示，提供`MapSpan`map 建構函式的執行個體設定的初始檢視 （中心點和縮放層級） 時就會載入對應。 `MoveToRegion` Map 類別上的方法可以再用來變更對應的位置或縮放層級。 有兩種方式來建立新的`MapSpan`執行個體：

-  **MapSpan.FromCenterAndRadius()** -靜態方法，以建立來自`Position`並指定`Distance`。
-  **新的 MapSpan （)** -使用的建構函式`Position`和程度的緯度和經度來顯示。


若要變更地圖的縮放層級但是不會改變位置，建立新`MapSpan`使用目前的位置，從`VisibleRegion.Center`地圖控制項的屬性。 A`Slider`可用來控制地圖縮放，像這樣 （不過，縮放直接在地圖控制項中目前無法更新滑桿的值）：

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![包含縮放地圖](map-images/maps-zoom-sml.png "地圖控制項縮放")](map-images/maps-zoom.png#lightbox "地圖控制項縮放")

### <a name="map-pins"></a>對應的 Pin

位置可以在地圖上標示`Pin`物件。

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` 可以設定為下列值，這可能會影響在釘選呈現 （取決於平台） 的方式之一：

-  泛型
-  位置
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

也可以將對應放置 Xaml 的版面配置中，此程式碼片段所示。

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
    x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
            x:Name="MyMap"
            IsShowingUser="true"
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

`MapRegion`並`Pins`中的程式碼使用可設定`MyMap`參考 （或任何對應稱為）。 請注意，額外`xmlns`命名空間定義，才可參考 Xamarin.Forms.Maps 控制項。

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>總結

Xamarin.Forms.Maps 是個別的 NuGet，必須新增至 Xamarin.Forms 方案中的每個專案。 額外的初始化程式碼是必要項，為 iOS、 Android 和 UWP 以及一些設定步驟。

一次設定對應的 API 可以用來呈現地圖釘選標記在短短幾行程式碼使用。 對應可以與進一步增強[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。


## <a name="related-links"></a>相關連結

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
