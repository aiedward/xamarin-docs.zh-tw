---
title: Xamarin.Forms 地圖控制項
description: 地圖控制項是顯示和標注地圖的跨平臺視圖。 它使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗。
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3029bfeb8179e4bf6a7b9103b22719c19fb38920
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375456"
---
# <a name="no-locxamarinforms-map-control"></a>Xamarin.Forms 地圖控制項

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是顯示和標注地圖的跨平臺視圖。 它使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗：

[![螢幕擷取畫面： iOS 和 Android 上的地圖控制項](map-images/map-default.png "地圖控制項")](map-images/map-default-large.png#lightbox "地圖控制項")

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義可控制地圖外觀和行為的下列屬性：

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)型別為的 `bool` ，表示地圖是否顯示使用者目前的位置。
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，類型為 `IEnumerable` ，指定 `IEnumerable` 要顯示之專案的集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，類型為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，指定要套用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 至所顯示專案集合中的每個專案。
- `ItemTemplateSelector`，類型為 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ，指定 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 將用來 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在執行時間為專案選擇的。
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)型別為的， `bool` 可決定是否允許地圖滾動。
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)型別為的 `bool` ，可決定是否允許地圖縮放。
- `MapElements`型別為的， `IList<MapElement>` 表示地圖上的元素清單，例如多邊形和多線條。
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)型別為的 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) ，表示地圖的顯示樣式。
- `MoveToLastRegionOnLayoutChange`型別為的 `bool` ，可控制當版面配置變更時，顯示的地圖區域是否會從其目前區域移至先前設定的區域。
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)型別為的， `IList<Pin>` 代表地圖上的釘選清單。
- `TrafficEnabled`型別為的 `bool` ，表示流量資料是否在地圖上重迭。
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)型別為的，會傳回 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 目前顯示的地圖區域。

這些屬性（除了 `MapElements` 、和屬性之外） `Pins` `VisibleRegion` 都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標。

[`Map`](xref:Xamarin.Forms.Maps.Map)類別也會定義在 `MapClicked` 對應時引發的事件。 `MapClickedEventArgs`事件隨附的物件具有名為的單一屬性 `Position` ，類型為 [`Position`](xref:Xamarin.Forms.Maps.Position) 。 當事件被引發時， `Position` 屬性會設定為已點擊的對應位置。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱 [地圖位置和距離](position-distance.md)。

如需 [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) 、和屬性的相關資訊 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) ，請 `ItemTemplateSelector` 參閱 [顯示 pin 集合](pins.md#display-a-pin-collection)。

## <a name="display-a-map"></a>顯示地圖

您 [`Map`](xref:Xamarin.Forms.Maps.Map) 可以藉由將它新增至版面配置或頁面來顯示。

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> 需要額外的 `xmlns` 命名空間定義才能參考 Xamarin.Forms 。地圖控制項。 在先前的範例中， `Xamarin.Forms.Maps` 會透過關鍵詞參考命名空間 `maps` 。

對等的 C# 程式碼為：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

這個範例會呼叫預設的函式 [`Map`](xref:Xamarin.Forms.Maps.Map) ，將地圖置中為羅馬：

[![螢幕擷取畫面： iOS 和 Android 上的地圖控制項與預設位置](map-images/map-default.png "具有預設位置的地圖控制項")](map-images/map-default-large.png#lightbox "具有預設位置的地圖控制項")

或者，您 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 可以將引數傳遞至函式， [`Map`](xref:Xamarin.Forms.Maps.Map) 以在載入時設定地圖的中心點和縮放層級。 如需詳細資訊，請參閱 [在地圖上顯示特定位置](#display-a-specific-location-on-a-map)。

## <a name="map-types"></a>對應類型

[`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性可以設定為 [`MapType`](xref:Xamarin.Forms.Maps.MapType) 列舉成員，以定義地圖的顯示樣式。 `MapType` 列舉會定義下列成員：

- `Street` 指定將顯示街道地圖。
- `Satellite` 指定將顯示包含衛星影像的地圖。
- `Hybrid` 指定將顯示結合街道和附屬資料的對應。

依預設， [`Map`](xref:Xamarin.Forms.Maps.Map) 如果屬性未定義，將會顯示街道地圖 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 。 或者，您 `MapType` 可以將屬性設定為其中一個 [`MapType`](xref:Xamarin.Forms.Maps.MapType) 列舉成員：

```xaml
<maps:Map MapType="Satellite" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

下列螢幕擷取畫面顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) 當 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 屬性設定為時的 `Street` ：

[![IOS 和 Android 上的地圖控制項與街道地圖類型的螢幕擷取畫面](map-images/maptype-street.png "地圖控制項與街道 maptype")](map-images/maptype-street-large.png#lightbox "地圖控制項與街道地圖類型")

下列螢幕擷取畫面顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) 當 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 屬性設定為時的 `Satellite` ：

[![IOS 和 Android 上的地圖控制項與附屬地圖類型的螢幕擷取畫面](map-images/maptype-satellite.png "使用附屬 maptype 對應控制項")](map-images/maptype-satellite-large.png#lightbox "使用附屬對應類型的地圖控制項")

下列螢幕擷取畫面顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) 當 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 屬性設定為時的 `Hybrid` ：

[![螢幕擷取畫面： iOS 和 Android 上具有混合式地圖類型的地圖控制項](map-images/maptype-hybrid.png "使用混合式 maptype 的地圖控制項")](map-images/maptype-hybrid-large.png#lightbox "使用混合式地圖類型的地圖控制項")

## <a name="display-a-specific-location-on-a-map"></a>在地圖上顯示特定位置

載入地圖時要顯示的地圖區域，可以藉由將引數傳遞至函式來設定 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) [`Map`](xref:Xamarin.Forms.Maps.Map) ：

```xaml
<maps:Map>
    <x:Arguments>
        <maps:MapSpan>
            <x:Arguments>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>36.9628066</x:Double>
                        <x:Double>-122.0194722</x:Double>
                    </x:Arguments>
                </maps:Position>
                <x:Double>0.01</x:Double>
                <x:Double>0.01</x:Double>
            </x:Arguments>
        </maps:MapSpan>
    </x:Arguments>
</maps:Map>
```

對等的 C# 程式碼為：

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

這個範例會建立一個 [`Map`](xref:Xamarin.Forms.Maps.Map) 物件，此物件會顯示物件所指定的區域 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 。 `MapSpan`物件會以物件所代表的緯度和經度為中心 [`Position`](xref:Xamarin.Forms.Maps.Position) ，並橫跨0.01 緯度和0.01 經度度數。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱 [地圖位置和距離](position-distance.md)。 如需在 XAML 中傳遞引數的詳細資訊，請參閱 [在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

結果是當地圖顯示時，它會在特定位置上置中，並跨越特定數量的緯度和經度度數：

[![螢幕擷取畫面：在 iOS 和 Android 上具有指定位置的地圖控制項](map-images/map-region.png "具有指定位置的地圖控制項")](map-images/map-region-large.png#lightbox "具有指定位置的地圖控制項")

## <a name="create-a-mapspan-object"></a>建立 MapSpan 物件

有幾種方法可以建立 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 物件。 常見的方法是提供函數的必要引數 `MapSpan` 。 這些是由物件所代表的緯度和經度 [`Position`](xref:Xamarin.Forms.Maps.Position) ，以及 `double` 代表所跨越之緯度和經度度數的值 `MapSpan` 。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱 [地圖位置和距離](position-distance.md)。

或者，類別中有三種方法會傳回 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 新的 `MapSpan` 物件：

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) 傳回 `MapSpan` 與 `LongitudeDegrees` 方法的類別實例相同的，以及其 `north` 和引數所定義的半徑 `south` 。
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) 傳回 `MapSpan` 其 [`Position`](xref:Xamarin.Forms.Maps.Position) 和引數所定義的 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 。
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) 傳回 `MapSpan` 與方法的類別實例相同的中心，但使用半徑乘以其 `double` 引數。

如需結構的詳細資訊 [`Distance`](xref:Xamarin.Forms.Maps.Distance) ，請參閱 [地圖位置和距離](position-distance.md)。

一旦 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 建立之後，就可以存取下列屬性來取得其相關資料：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)，代表的 [`Position`](xref:Xamarin.Forms.Maps.Position) 地理中心 `MapSpan` 。
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)，代表所跨越的緯度度數 `MapSpan` 。
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)，代表所跨越的經度度數 `MapSpan` 。
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)，代表 `MapSpan` 半徑。

## <a name="move-the-map"></a>移動地圖

您 [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 可以呼叫方法來變更地圖的位置和縮放層級。 這個方法會接受 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 引數，此引數會定義要顯示之地圖的區域，以及其縮放層級。

下列程式碼顯示在地圖上移動顯示區域的範例：

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>縮放地圖

您可以變更的縮放層級， [`Map`](xref:Xamarin.Forms.Maps.Map) 而不需要變更其位置。 您可以使用 map UI 來完成這項作業，或透過以程式設計方式呼叫 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法與 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 使用目前位置作為引數的引數來完成這項作業 [`Position`](xref:Xamarin.Forms.Maps.Position) ：

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

在此範例中， [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 會使用 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 引數來呼叫方法，此引數會透過屬性指定地圖的目前位置， [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) 並以緯度和經度的角度來指定縮放層級。 整體結果是地圖的縮放層級已變更，但其位置不是。 在地圖上執行縮放的替代方法是使用 [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) 方法來控制縮放因數。

> [!IMPORTANT]
> 縮放地圖（不論是透過地圖 UI 或以程式設計方式） [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) 都需要屬性為 `true` 。 如需此屬性的詳細資訊，請參閱 [停用縮放](#disable-zoom)。

## <a name="customize-map-behavior"></a>自訂對應行為

您 [`Map`](xref:Xamarin.Forms.Maps.Map) 可以藉由設定某些屬性，以及藉由處理事件來自訂的行為 `MapClicked` 。

> [!NOTE]
> 您可以藉由建立地圖自訂轉譯器來達成其他的對應行為自訂。 如需詳細資訊，請參閱 [自訂 Xamarin.Forms 對應](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)。

### <a name="show-traffic-data"></a>顯示流量資料

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `TrafficEnabled` 型別的屬性 `bool` 。 根據預設，這個屬性是 `false` ，表示不會在地圖上覆迭流量資料。 當這個屬性設定為時 `true` ，會將流量資料重迭在地圖上。 下列範例將示範如何設定這個屬性：

```xaml
<maps:Map TrafficEnabled="true" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    TrafficEnabled = true
};
```

### <a name="disable-scroll"></a>停用捲軸

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) 型別的屬性 `bool` 。 依預設，這個屬性是 `true` ，表示允許地圖滾動。 當這個屬性設定為時 `false` ，地圖不會滾動。 下列範例將示範如何設定這個屬性：

```xaml
<maps:Map HasScrollEnabled="false" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>停用縮放

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) 型別的屬性 `bool` 。 依預設，這個屬性是 `true` ，表示可以在地圖上執行縮放。 當這個屬性設定為時 `false` ，就無法縮放地圖。 下列範例將示範如何設定這個屬性：

```xaml
<maps:Map HasZoomEnabled="false" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>顯示使用者的位置

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) 型別的屬性 `bool` 。 依預設，這個屬性是 `false` ，表示地圖不會顯示使用者的目前位置。 當這個屬性設定為時 `true` ，地圖會顯示使用者目前的位置。 下列範例將示範如何設定這個屬性：

```xaml
<maps:Map IsShowingUser="true" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> 在 iOS、Android 和通用 Windows 平臺上，存取使用者的位置需要將位置許可權授與應用程式。 如需詳細資訊，請參閱 [平臺](setup.md#platform-configuration)設定。

### <a name="maintain-map-region-on-layout-change"></a>維護版面配置變更的地圖區域

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `MoveToLastRegionOnLayoutChange` 型別的屬性 `bool` 。 根據預設，這個屬性是 `true` ，表示顯示的地圖區域會在版面配置變更時（例如裝置旋轉），從其目前區域移至其先前設定的區域。 當這個屬性設定為時 `false` ，顯示的地圖區域會在版面配置變更時維持在中心。 下列範例將示範如何設定這個屬性：

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>地圖點擊

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義在 `MapClicked` 對應時引發的事件。 `MapClickedEventArgs`事件隨附的物件具有名為的單一屬性 `Position` ，類型為 [`Position`](xref:Xamarin.Forms.Maps.Position) 。 當事件被引發時， `Position` 屬性會設定為已點擊的對應位置。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱 [地圖位置和距離](position-distance.md)。

下列程式碼範例顯示事件的事件處理常式 `MapClicked` ：

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此範例中， `OnMapClicked` 事件處理常式會輸出代表點擊地圖位置的緯度和經度。 事件處理常式可以使用事件來註冊，如下所示 `MapClicked` ：

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>相關連結

- [Maps 範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [地圖位置和距離](position-distance.md)
- [自訂 Xamarin.Forms 對應](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)