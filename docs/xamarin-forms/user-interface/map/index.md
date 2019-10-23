---
title: Xamarin. 表單對應初始化和設定
description: 本文說明如何使用 Xamarin Map 類別，在每個平臺上使用原生地圖 Api，為使用者提供熟悉的地圖體驗。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697385"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Xamarin. 表單對應初始化和設定

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin 會在每個平臺上使用原生地圖 Api。_

[Xamarin] 會在每個平臺上使用原生地圖 Api。 這可為使用者提供快速且熟悉的地圖體驗，但這表示需要一些設定步驟，以符合每個平臺的 API 需求。
一旦設定之後，`Map` 控制項的運作方式就如同通用程式碼中的任何其他 Xamarin. Forms 元素一樣。

地圖控制項已用於[MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)範例中，如下所示。

 [![MobileCRM 範例中的對應](map-images/maps-zoom-sml.png "地圖控制項範例")](map-images/maps-zoom.png#lightbox "地圖控制項範例")

藉由建立[地圖自訂](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)轉譯器，可進一步增強對應功能。

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>對應初始化

將對應新增至 Xamarin **. forms 應用程式時，您**應該新增至方案中的每個專案的個別 NuGet 套件。
在 Android 上，這也會相依于 GooglePlayServices （另一個 NuGet），而當您新增 Xamarin 時，會自動下載此項。

安裝 NuGet 套件之後，在 `Xamarin.Forms.Forms.Init` 方法呼叫*之後*，每個應用程式專案中都需要某些初始化程式碼。 若是 iOS，請使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，您必須傳遞與 `Forms.Init` 相同的參數：

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

針對通用 Windows 平臺（UWP），請使用下列程式碼：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

在每個平臺的下列檔案中新增此呼叫：

- **iOS** -AppDelegate.cs 檔案，在 `FinishedLaunching` 方法中。
- **Android** -MainActivity.cs 檔案，在 `OnCreate` 方法中。
- **UWP** -MainPage.xaml.cs 檔案，位於 `MainPage` 的函式中。

一旦新增 NuGet 套件，並在每個應用程式內呼叫初始化方法，就可以在通用 .NET Standard 程式庫專案或共用的專案程式碼中使用 `Xamarin.Forms.Maps` Api。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平臺設定

在某些平臺上，您必須先進行其他設定步驟，才會顯示對應。

### <a name="ios"></a>iOS

若要存取 iOS 上的位置服務，您必須在**Info. plist**中設定下列機碼：

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –適用于使用應用程式時的位置服務
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) –隨時使用位置服務
- iOS 10 和更早版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –適用于使用應用程式時的位置服務
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) –隨時使用位置服務    

若要支援 iOS 11 和更早版本，您可以包含三個金鑰： `NSLocationWhenInUseUsageDescription`、`NSLocationAlwaysAndWhenInUseUsageDescription` 和 `NSLocationAlwaysUsageDescription`。

**Plist**中這些索引鍵的 XML 表示如下所示。 您應該更新 `string` 值，以反映應用程式使用位置資訊的方式：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

編輯**plist**檔案時，也可以在**來源**視圖中新增**plist**專案：

![適用于 iOS 8 的 plist](map-images/ios8-map-permissions.png "iOS 8 必要資訊。 plist 專案")

### <a name="android"></a>Android

若要在 Android 上使用[Google MAPS API v2](https://developers.google.com/maps/documentation/android/) ，您必須產生 API 金鑰，並將它新增至您的 Android 專案。
請依照 Xamarin 檔中的指示[取得 Google MAPS API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
遵循這些指示之後，請在**Properties/androidmanifest.xml**檔案中貼上 API 金鑰（view source，並尋找/更新下列元素）：

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

如果沒有有效的 API 金鑰，maps 控制項就會在 Android 上顯示為灰色方塊。

> [!NOTE]
> 請注意，為了讓您的 APK 存取 Google Maps，您必須針對用來簽署 APK 的每個金鑰儲存區（debug 和 release）包含 SHA-1 指紋和套件名稱。 例如，如果您使用一部電腦進行 debug，而另一部電腦用於產生發行 APK，您應該包含第一部電腦的 debug 金鑰儲存區中的 SHA-1 憑證指紋，以及來自的版本金鑰儲存區的 SHA-1 憑證指紋第二部電腦。 也請記得在應用程式的**套件名稱**變更時編輯金鑰認證。 請參閱[取得 Google MAPS API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

您也需要以滑鼠右鍵按一下 Android 專案，然後選取 [**選項] > 組建 > Android 應用程式**，以啟用適當的許可權，然後滴答下列各項：

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

下列螢幕擷取畫面顯示其中部分：

![Android 的必要許可權](map-images/android-map-permissions.png "Android 的必要許可權")

最後兩個是必要的，因為應用程式需要網路連線才能下載地圖資料。 若要深入瞭解，請參閱 Android[許可權](https://developer.android.com/reference/android/Manifest.permission.html)。

此外，Android 9 已從 bootclasspath 移除 Apache HTTP 用戶端程式庫，因此不適用於以 API 28 或更高版本為目標的應用程式。 您必須將下列這一行新增至**androidmanifest.xml**檔案的 `application` 節點，才能繼續在以 API 28 或更高版本為目標的應用程式中使用 Apache HTTP 用戶端：

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要在通用 Windows 平臺上使用對應，您必須產生授權權杖。 如需詳細資訊，請參閱 MSDN 上的[要求地圖服務驗證金鑰](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)。

接著，您應該在 `FormsMaps.Init("AUTHORIZATION_TOKEN")` 方法呼叫中指定驗證權杖，以使用 Bing 地圖服務來驗證應用程式。

<a name="Using_Maps" />

## <a name="map-configuration"></a>對應設定

如需如何在程式碼中使用地圖控制項的範例，請參閱 MobileCRM 範例中的[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) 。 簡單的 `MapPage` 類別看起來可能像這樣-請注意，會建立新的 `MapSpan` 來定位地圖的觀點：

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

您也可以藉由設定 `MapType` 屬性來變更地圖內容，以顯示一般街道地圖（預設值）、衛星圖像或兩者的組合。

```csharp
map.MapType = MapType.Street;
```

有效的 `MapType` 值為：

- `Hybrid`
- `Satellite`
- `Street` （預設值）

### <a name="map-region-and-mapspan"></a>對應區域和 MapSpan

如上述程式碼片段所示，將 `MapSpan` 實例提供給對應的函式，會在載入時設定地圖的初始視圖（中心點和縮放層級）。 有兩種方式可以建立新的 `MapSpan` 實例：

- **MapSpan. FromCenterAndRadius （）** -從 `Position` 建立範圍並指定 `Distance` 的靜態方法。
- **新的 MapSpan （）** -使用 `Position` 的函式，以及要顯示的緯度和經度度數。

然後，可以使用 map 類別上的 `MoveToRegion` 方法來變更地圖的位置或縮放層級。 若要在不改變位置的情況下變更地圖的縮放層級，請使用地圖控制項的 [`VisibleRegion.Center`] 屬性中的目前位置來建立新的 `MapSpan`。 @No__t_0 可用來控制地圖縮放比例，如下所示（不過，直接在地圖控制項中縮放，目前無法更新滑杆的值）：

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![具有 zoom 的地圖](map-images/maps-zoom-sml.png "地圖控制項縮放")](map-images/maps-zoom.png#lightbox "地圖控制項縮放")

此外， [`Map`](xref:Xamarin.Forms.Maps.Map)類別具有類型 `bool` 的 `MoveToLastRegionOnLayoutChange` 屬性，其受可系結屬性支援。 根據預設，此屬性為 `true`，這表示顯示的對應區域會在配置變更發生時，從其目前的區域移至先前設定的區域，例如裝置旋轉時。 當這個屬性設定為 [`false`] 時，顯示的對應區域會在版面配置變更發生時保留在中央。 下列範例會示範如何設定此屬性：

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>對應點擊

`Map` 定義在繪製對應時所引發的 `MapClicked` 事件。 隨附 `MapClicked` 事件的 `MapClickedEventArgs` 物件具有名為 `Position` 的單一屬性，其類型為 `Position`。 當引發事件時，`Position` 屬性的值會設定為已按下的地圖位置。

下列程式碼範例顯示 `MapClicked` 事件的事件處理常式：

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此範例中，`OnMapClicked` 事件處理常式會輸出代表螺紋地圖位置的緯度和經度。

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>在 XAML 中建立對應

您也可以在 XAML 中建立對應，如下列範例所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> 需要額外的 `xmlns` 命名空間定義，才能參考 Xamarin. Forms 控制項。 在上述範例中，會透過 `maps` 關鍵字來參考 `Xamarin.Forms.Maps` 命名空間。

您可以在程式碼中使用 `Map` 的命名參考來設定 `MapRegion`：

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin]。[地圖釘](~/xamarin-forms/user-interface/map/pins.md)選。
- [Maps API](xref:Xamarin.Forms.Maps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
