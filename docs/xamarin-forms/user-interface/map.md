---
title: 對應
description: Xamarin.Forms 會在每個平台上使用原生對應應用程式開發介面。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: f78b16a99d8bc828e26bb6aecdb67d4ba07e18d4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="map"></a>對應

_Xamarin.Forms 會在每個平台上使用原生對應應用程式開發介面。_

Xamarin.Forms.Maps 會在每個平台上使用原生的對應應用程式開發介面。 這對於使用者，提供快速且熟悉的對應體驗，但表示一些設定步驟需要依照每個平台特定應用程式開發介面的需求。
一旦設定，`Map`控制運作方式就像在一般的程式碼中任何其他 Xamarin.Forms 項目。

* [對應初始化](#Maps_Initialization)-使用`Map`需要額外的初始化程式碼啟動時。
* [平台組態](#Platform_Configuration)-每個平台必須對應到工作進行一些設定。
* [在 C# 中使用 Map](#Using_Maps) -顯示對應，並釘選使用 C#。
* [在 XAML 中使用對應](#Using_Xaml)-顯示具有 XAML 的對應。

地圖控制項用於[MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)範例，如下所示。

 [![MobileCRM 範例中的對應](map-images/maps-zoom-sml.png "地圖控制項範例")](map-images/maps-zoom.png#lightbox "地圖控制項範例")

地圖功能可以藉由建立進一步增強[對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>對應初始化

將地圖加入至 Xamarin.Forms 應用程式時**Xamarin.Forms.Maps**是個別的 NuGet 封裝，您應該將它加入至方案中每個專案。
在 Android 上，這也會相依於 GooglePlayServices (另一個 NuGet) 當您將加入 Xamarin.Forms.Maps 自動下載。

安裝 NuGet 封裝之後, 一些初始化程式碼必須在每個應用程式專案中，*之後*`Xamarin.Forms.Forms.Init`方法呼叫。 適用於 iOS 使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，您必須傳遞相同的參數做為`Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Windows 執行階段 (WinRT) 和通用 Windows 平台 (UWP) 使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

在每個平台的下列檔案中新增此呼叫：

-  **iOS** -d 檔，請在`FinishedLaunching`方法。
-  **Android** -Weatherapp 檔，請在`OnCreate`方法。
-  **WinRT 及 UWP** -MainPage.xaml.cs 檔，請在`MainPage`建構函式。

已加入 NuGet 套件和初始設定方法呼叫內每個應用程式中，一旦`Xamarin.Forms.Maps`Api 可用於通用的 PCL 或共用的專案程式碼。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台組態

地圖會顯示之前某些平台上需要額外的設定步驟。

### <a name="ios"></a>iOS

在 iOS 7 地圖控制項 < 只運作 >，因此長時間作為`FormsMaps.Init()`在進行呼叫。

針對 iOS 8 的兩個索引鍵必須加入至**Info.plist**檔案： [ `NSLocationAlwaysUsageDescription` ](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18)和[ `NSLocationWhenInUseUsageDescription` ](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26)。 如下所示的 XML 表示法，-，您應該更新`string`值，以反映您的應用程式如何使用位置資訊：

```xml
<key>NSLocationAlwaysUsageDescription</key>
    <string>Can we use your location</string>
<key>NSLocationWhenInUseUsageDescription</key>
    <string>We are using your location</string>
```

**Info.plist**也會在加入項目**來源**編輯時檢視**Info.plist**檔案：

![適用於 iOS 8 Info.plist](map-images/ios8-map-permissions.png "iOS 8 需要 Info.plist 項目")


### <a name="android"></a>Android

若要使用[Google Maps API v2](https://developers.google.com/maps/documentation/android/)在 Android 上，您必須產生 API 金鑰並將它加入至您的 Android 專案。
依照上 Xamarin 文件中的指示[取得 Google Maps API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
遵循這些指示之後, 貼上 API 金鑰，在**Properties/AndroidManifest.xml**檔案 （檢視原始檔及尋找更新下列項目）：

```xml
<meta-data android:name="com.google.android.maps.v2.API_KEY"
            android:value="AbCdEfGhIjKlMnOpQrStUvWValueGoesHere" />
```

沒有有效的 API 金鑰地圖控制項顯示為灰色方塊在 Android 上。

> [!NOTE]
> 請記得要產生另一個索引鍵使用的金鑰存放區檔案，用來簽署發行任何的版本應用程式上傳至 Google Play 存放區。 索引鍵產生的程式開發和偵錯將無法運作，從 Google Play 下載應用程式就會中斷地圖顯示。 也請記得要重新產生鍵，如果應用程式的**封裝名稱**變更。

您也必須啟用適當的權限的 Android 專案上按一下滑鼠右鍵，然後選取**選項 > 建置 > Android 應用程式**和計時下列：

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

其中一些會顯示在下面的螢幕擷取畫面：

![Android 的必要權限](map-images/android-map-permissions.png "適用於 Android 的必要權限")

最後兩個所需的因為應用程式需要下載對應資料的網路連線。 了解 Android[權限](http://developer.android.com/reference/android/Manifest.permission.html)如需詳細資訊。

### <a name="windows-runtime-and-universal-windows-platform"></a>Windows 執行階段和通用 Windows 平台

若要在 Windows 執行階段和通用 Windows 平台上使用 map 中，您必須產生的授權權杖。 如需詳細資訊，請參閱[要求對應的驗證金鑰](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)MSDN 上。

驗證權杖則應該指定在`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法呼叫中，驗證與 Bing 地圖服務的應用程式。

<a name="Using_Maps" />

## <a name="using-maps"></a>使用對應

請參閱[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) MobileCRM 範例如需如何使用地圖控制項，程式碼中的範例中。 簡單`MapPage`類別可能類似此-請注意，新`MapSpan`是用來放置地圖的檢視：

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

也可以藉由設定改變地圖內容`MapType`屬性，以顯示規則的街道地圖 （預設值）、 附屬圖像或兩者的組合。

```csharp
map.MapType == MapType.Street;
```

有效`MapType`的值為：

-  混合式
-  附屬項目
-  街道 （預設值）


### <a name="map-region-and-mapspan"></a>地圖區域和 MapSpan

上述程式碼片段所示，提供`MapSpan`map 建構函式的執行個體設定的初始檢視 （中心點和縮放層級） 的載入時將它的對應。 `MoveToRegion` Map 類別上的方法可以再用來變更對應的位置或縮放層級。 有兩種方式來建立新的`MapSpan`執行個體：

-  **MapSpan.FromCenterAndRadius()** -靜態方法建立的範圍從`Position`並指定`Distance`。
-  **新 MapSpan （)** -使用建構函式`Position`和顯示經緯度度。


若要變更地圖的縮放層級，而不會變更位置，建立新`MapSpan`使用目前的位置，從`VisibleRegion.Center`地圖控制項的屬性。 A`Slider`無法用來控制地圖縮放如下 （不過縮放直接在地圖控制項中目前無法更新滑桿的值）：

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![與縮放地圖](map-images/maps-zoom-sml.png "地圖控制項縮放")](map-images/maps-zoom.png#lightbox "地圖控制項縮放")

### <a name="map-pins"></a>對應的 Pin

位置可以在對應圖上標示`Pin`物件。

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

 `PinType` 可以設定下列值，這可能會影響 （視平台） 的 pin 呈現的方式的其中一個：

-  泛型
-  位置
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

也可以將地圖放置 Xaml 版面配置中，此程式碼片段所示。

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

`MapRegion`和`Pins`可以在程式碼中使用設定`MyMap`參考 （或任何對應名為）。 請注意，額外`xmlns`命名空間定義才可參考 Xamarin.Forms.Maps 控制項。

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>總結

Xamarin.Forms.Maps 是個別的 NuGet 必須新增至 Xamarin.Forms 方案中每個專案。 額外的初始化程式碼是必要項，為 iOS、 Android、 WinRT，和 UWP 也一些設定步驟。

一次設定對應的 API 可用來呈現釘選標記在短短幾行程式碼對應。 地圖可以進一步加強與[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。


## <a name="related-links"></a>相關連結

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [對應的自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
