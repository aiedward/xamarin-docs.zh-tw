---
title: Xamarin.Forms 對應
description: 這篇文章說明如何使用 Xamarin.Forms Map 類別在每個平台上使用原生的對應 Api，來提供熟悉對應的使用者體驗。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 5eee9fea7ea0fffe9dc254ac4340ff05d087ce33
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121564"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms 對應

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin.Forms 會在每個平台上使用原生的對應 Api。_

Xamarin.Forms.Maps 會使用原生的對應 Api，每個平台。 這可為使用者提供快速且熟悉的地圖體驗, 但這表示需要一些設定步驟, 以符合每個平臺的 API 需求。
設定後，`Map`控制運作方式就像一般的程式碼中的任何其他 Xamarin.Forms 元素。

地圖控制項用於[MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)範例，如下所示。

 [![MobileCRM 範例中的地圖](map-images/maps-zoom-sml.png "地圖控制項範例")](map-images/maps-zoom.png#lightbox "地圖控制項範例")

地圖功能可以藉由建立進一步強化[對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>對應初始化

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

- **iOS** -在 AppDelegate.cs 檔案`FinishedLaunching`方法。
- **Android** -在 MainActivity.cs 檔案`OnCreate`方法。
- **UWP** -在 MainPage.xaml.cs 檔案`MainPage`建構函式。

新增 NuGet 套件並在每個應用程式內呼叫初始化方法之後, `Xamarin.Forms.Maps`即可在通用 .NET Standard 程式庫專案或共用的專案程式碼中使用 api。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平臺設定

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

如果沒有有效的 API 金鑰, maps 控制項就會在 Android 上顯示為灰色方塊。

> [!NOTE]
> 請注意，為了讓您存取 Google 地圖的 APK，您必須包含 sha-1 指紋和封裝名稱的每個金鑰儲存區 （偵錯和發行），您用來簽署您的 APK。 比方說，如果您使用一部電腦進行偵錯和產生發行 APK 的另一部電腦時，您應該包含 sha-1 憑證指紋，從第一部電腦的偵錯金鑰儲存區和從發行金鑰儲存區的 sha-1 憑證指紋第二部電腦中。 也請務必編輯金鑰的認證，如果應用程式的**封裝名稱**變更。 請參閱[取得 Google Maps API v2 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

您也需要啟用 Android 專案上按一下滑鼠右鍵，然後選取適當的權限**選項 > 建置 > Android 應用程式**和計時下列：

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

這些都是以以下螢幕擷取畫面所示：

![適用於 Android 的必要權限](map-images/android-map-permissions.png "適用於 Android 的必要權限")

這兩個所需的因為應用程式需要下載地圖資料的網路連線。 了解 Android[權限](https://developer.android.com/reference/android/Manifest.permission.html)若要深入了。

此外, Android 9 已從 bootclasspath 移除 Apache HTTP 用戶端程式庫, 因此不適用於以 API 28 或更高版本為目標的應用程式。 您必須將下列這一行新增至`application` **androidmanifest.xml**的節點, 才能繼續在以 API 28 或更高版本為目標的應用程式中使用 Apache HTTP 用戶端:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用通用 Windows 平台上的對應中，您必須產生的授權權杖。 如需詳細資訊，請參閱 <<c0> [ 要求對應的驗證金鑰](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)MSDN 上。

驗證權杖則應該指定在`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法呼叫中，驗證使用 Bing 地圖服務的應用程式。

<a name="Using_Maps" />

## <a name="map-configuration"></a>對應設定

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
map.MapType = MapType.Street;
```

有效`MapType`的值為：

- `Hybrid`
- `Satellite`
- `Street` （預設值）

### <a name="map-region-and-mapspan"></a>對應區域和 MapSpan

上述程式碼片段所示，提供`MapSpan`map 建構函式的執行個體設定的初始檢視 （中心點和縮放層級） 時就會載入對應。 有兩種方式來建立新的`MapSpan`執行個體：

- **MapSpan.FromCenterAndRadius()** -靜態方法，以建立來自`Position`並指定`Distance`。
- **新的 MapSpan （)** -使用的建構函式`Position`和程度的緯度和經度來顯示。

`MoveToRegion` Map 類別上的方法可以再用來變更對應的位置或縮放層級。 若要變更地圖的縮放層級但是不會改變位置，建立新`MapSpan`使用目前的位置，從`VisibleRegion.Center`地圖控制項的屬性。 `Slider`可以用來控制地圖縮放比例, 如下所示 (不過, 直接在地圖控制項中縮放, 目前無法更新滑杆的值):

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![包含縮放地圖](map-images/maps-zoom-sml.png "地圖控制項縮放")](map-images/maps-zoom.png#lightbox "地圖控制項縮放")

此外, [`Map`](xref:Xamarin.Forms.Maps.Map)類別`MoveToLastRegionOnLayoutChange`具有類型`bool`的屬性, 它是由可系結的屬性所支援。 根據預設, 此屬性`true`為, 這表示顯示的對應區域會在配置變更發生時, 從其目前的區域移至其先前設定的區域, 例如裝置旋轉。 當這個屬性設定為`false`時, 顯示的對應區域將會在版面配置變更時保留在中央。 下列範例會示範如何設定此屬性:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-pins"></a>地圖釘選

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

`PinType`可以設定為下列其中一個值, 這可能會影響釘選的呈現方式 (視平臺而定):

- 泛型
- 位置
- SavedPin
- SearchResult

### <a name="map-clicks"></a>對應點擊

`Map`定義繪製對應時所引發的事件。`MapClicked` 伴隨事件的`MapClickedEventArgs`物件具有名為的單一屬性, `Position`其類型`Position`為。 `MapClicked` 當引發事件時, `Position`屬性的值會設定為所按下的地圖位置。

下列程式碼範例顯示`MapClicked`事件的事件處理常式:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此範例中, `OnMapClicked`事件處理常式會輸出代表螺紋對應位置的緯度和經度。

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>在 XAML 中建立對應

您也可以在 XAML 中建立對應, 如下列範例所示:

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
> 需要額外`xmlns`的命名空間定義, 才能參考 Xamarin. Forms 控制項。

在程式`Pins`代碼中, 可以使用的命名參考來`Map`設定和:`MapRegion`

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>使用資料系結將資料填入地圖

[`Map`](xref:Xamarin.Forms.Maps.Map)類別也會公開下列屬性:

- `ItemsSource`–指定要顯示的`IEnumerable`專案集合。
- `ItemTemplate`–指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)要套用至所顯示專案集合中每個專案的。
- `ItemTemplateSelector` – 指定將用來在執行階段為項目選擇 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)。

> [!NOTE]
> 當`ItemTemplate` 與 `ItemTemplateSelector`兩個屬性都已設定時，會優先使用 `ItemTemplate` 屬性。

您可以使用資料系結將其`ItemsSource`屬性`IEnumerable`系結至集合, 以填入資料: [`Map`](xref:Xamarin.Forms.Maps.Map)

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

屬性資料會系結至`Locations`已連接之視圖`ObservableCollection`模型的屬性, 它會傳回`Location`物件的, 這是自訂類型。 `ItemsSource` 每`Location`個物件`Address`都會`Description`定義類型的`string`和屬性, 以及`Position`類型[`Position`](xref:Xamarin.Forms.Maps.Position)的屬性。

`IEnumerable`集合中每個專案的外觀定義方式是將`ItemTemplate`屬性設定[`Pin`](xref:Xamarin.Forms.Maps.Pin)為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , 其中包含資料系結至適當屬性的物件。

下列螢幕擷取畫面顯示[`Map`](xref:Xamarin.Forms.Maps.Map)使用資料系結[`Pin`](xref:Xamarin.Forms.Maps.Pin)顯示集合:

[ ![IOS 和 Android 上具有資料系結圖釘的對應螢幕擷取畫面](map-images/pins-itemssource.png "與資料")]系結的釘選圖(map-images/pins-itemssource-large.png#lightbox "具有資料系結圖釘的對應")

### <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由`IEnumerable` `ItemTemplateSelector`將屬性設定為[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), 您可以在執行時間根據專案值選擇集合中每個專案的外觀:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

下列範例顯示`MapItemTemplateSelector`類別:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

`MapItemTemplateSelector` 類別定義設定為不同資料範本的 `DefaultTemplate` 與 `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 屬性。 當`OnSelectTemplate`專案具有包含`XamarinTemplate`「三藩市」的位址時, 此方法`Pin`會傳回, 當按下時, 會將 "Xamarin" 顯示為標籤。 當專案沒有包含「三藩市」的位址時, 此`OnSelectTemplate`方法`DefaultTemplate`會傳回。

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
- [Maps API](xref:Xamarin.Forms.Maps)
