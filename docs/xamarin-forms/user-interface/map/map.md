---
title: Xamarin. Forms 地圖控制項
description: 地圖控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗。
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912708"
---
# <a name="xamarinforms-map-control"></a>Xamarin. Forms 地圖控制項

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗：

[![地圖控制項（在 iOS 和 Android 上）的螢幕擷取畫面](map-images/map-default.png "地圖控制項")](map-images/map-default-large.png#lightbox "地圖控制項")

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義可控制地圖外觀和行為的下列屬性：

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)，屬於 `bool`類型，表示地圖是否顯示使用者目前的位置。
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，屬於 `IEnumerable`類型，指定要顯示 `IEnumerable` 專案的集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型，它會指定要套用至所顯示專案集合中每個專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector`，屬於[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)類型，它會指定在執行時間用來選擇專案[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)，屬於 `bool`類型，可決定是否允許地圖進行滾動。
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)，屬於 `bool`類型，可決定是否允許地圖縮放。
- `MapElements`，屬於 `IList<MapElement>`類型，代表地圖上的專案清單，例如多邊形和折線。
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)，屬於[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)類型，表示地圖的顯示樣式。
- `MoveToLastRegionOnLayoutChange`，屬於 `bool`類型，可控制當版面配置變更時，所顯示的對應區域是否會從其目前區域移至其先前設定的區域。
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)，屬於 `IList<Pin>`類型，代表地圖上的釘選清單。
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)，屬於[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)類型，會傳回地圖目前顯示的區域。

除了 `MapElements`、`Pins`和 `VisibleRegion` 屬性以外，這些屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標。

[`Map`](xref:Xamarin.Forms.Maps.Map)類別也會定義在繪製對應時所引發的 `MapClicked` 事件。 事件隨附的 `MapClickedEventArgs` 物件具有名為 `Position`的單一屬性，其類型為[`Position`](xref:Xamarin.Forms.Maps.Position)。 引發事件時，`Position` 屬性會設定為已按下的地圖位置。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

如需[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)、 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)和 `ItemTemplateSelector` 屬性的相關資訊，請參閱[顯示 pin 集合](pins.md#display-a-pin-collection)。

## <a name="display-a-map"></a>顯示地圖

藉由將[`Map`](xref:Xamarin.Forms.Maps.Map)新增至版面配置或頁面，即可加以顯示：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> 需要額外的 `xmlns` 命名空間定義，才能參考 Xamarin. Forms 控制項。 在上述範例中，會透過 `maps` 關鍵字來參考 `Xamarin.Forms.Maps` 命名空間。

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

這個範例會呼叫預設的[`Map`](xref:Xamarin.Forms.Maps.Map)的函式，以將地圖置中為羅馬：

[![在 iOS 和 Android 上具有預設位置之地圖控制項的螢幕擷取畫面](map-images/map-default.png "具有預設位置的地圖控制項")](map-images/map-default-large.png#lightbox "具有預設位置的地圖控制項")

或者，您也可以將[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數傳遞至[`Map`](xref:Xamarin.Forms.Maps.Map)的函式，以在載入時設定地圖的中心點和縮放層級。 如需詳細資訊，請參閱[顯示地圖上的特定位置](#display-a-specific-location-on-a-map)。

## <a name="map-types"></a>對應類型

[`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性可以設定為[`MapType`](xref:Xamarin.Forms.Maps.MapType)列舉成員，以定義地圖的顯示樣式。 `MapType` 列舉會定義下列成員：

- `Street` 指定將顯示街道地圖。
- `Satellite` 指定將顯示包含附屬影像的對應。
- `Hybrid` 指定將會顯示與街道和附屬資料結合的對應。

根據預設，如果[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性未定義， [`Map`](xref:Xamarin.Forms.Maps.Map)將會顯示街道對應。 或者，`MapType` 屬性可以設定為其中一個[`MapType`](xref:Xamarin.Forms.Maps.MapType)列舉成員：

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

下列螢幕擷取畫面顯示[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為 `Street`時的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上具有街道對應類型的地圖控制項螢幕擷取畫面](map-images/maptype-street.png "具有街道 maptype 的地圖控制項")](map-images/maptype-street-large.png#lightbox "具有街道對應類型的地圖控制項")

下列螢幕擷取畫面顯示[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為 `Satellite`時的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上具有衛星對應類型的地圖控制項螢幕擷取畫面](map-images/maptype-satellite.png "使用附屬 maptype 的地圖控制項")](map-images/maptype-satellite-large.png#lightbox "具有衛星對應類型的地圖控制項")

下列螢幕擷取畫面顯示[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為 `Hybrid`時的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上具有混合式地圖類型的地圖控制項螢幕擷取畫面](map-images/maptype-hybrid.png "使用混合式 maptype 的地圖控制項")](map-images/maptype-hybrid-large.png#lightbox "具有混合式地圖類型的地圖控制項")

## <a name="display-a-specific-location-on-a-map"></a>顯示地圖上的特定位置

載入對應時要顯示的地圖區域，可以藉由將[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數傳遞給[`Map`](xref:Xamarin.Forms.Maps.Map)的「函式」來設定：

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

這個範例會建立[`Map`](xref:Xamarin.Forms.Maps.Map)物件，以顯示[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件所指定的區域。 `MapSpan` 物件會以[`Position`](xref:Xamarin.Forms.Maps.Position)物件所表示的緯度和經度為中心，並橫跨0.01 緯度和0.01 經度度。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。 如需在 XAML 中傳遞引數的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

結果是當地圖顯示時，它會以特定位置為中心，並跨越特定的緯度和經度度：

[![在 iOS 和 Android 上具有指定位置之地圖控制項的螢幕擷取畫面](map-images/map-region.png "具有指定位置的地圖控制項")](map-images/map-region-large.png#lightbox "具有指定位置的地圖控制項")

## <a name="create-a-mapspan-object"></a>建立 MapSpan 物件

建立[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件的方法有許多種。 常見的方法是將必要的引數提供給 `MapSpan` 的函式。 這些是由[`Position`](xref:Xamarin.Forms.Maps.Position)物件表示的緯度和經度，而 `double` 值代表 `MapSpan`所跨越的緯度和經度度數。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

或者， [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)類別中有三個方法會傳回新的 `MapSpan` 物件：

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)會傳回與方法的類別實例具有相同 `LongitudeDegrees` 的 `MapSpan`，以及其 `north` 和 `south` 引數所定義的半徑。
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)會傳回其[`Position`](xref:Xamarin.Forms.Maps.Position)和[`Distance`](xref:Xamarin.Forms.Maps.Distance)引數所定義的 `MapSpan`。
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)會傳回與方法的類別實例相同中心的 `MapSpan`，但半徑乘以其 `double` 引數。

如需[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

建立[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)之後，即可存取下列屬性來抓取其相關資料：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)，代表 `MapSpan`地理中心的[`Position`](xref:Xamarin.Forms.Maps.Position) 。
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)，代表 `MapSpan`所跨越的緯度度數。
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)，代表 `MapSpan`所跨越的經度度數。
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)，代表 `MapSpan` 的半徑。

## <a name="move-the-map"></a>移動地圖

您可以呼叫[`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，以變更地圖的位置和縮放層級。 這個方法會接受[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數，以定義要顯示的地圖區域及其縮放層級。

下列程式碼顯示在地圖上移動所顯示區域的範例：

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>縮放地圖

您可以變更[`Map`](xref:Xamarin.Forms.Maps.Map)的縮放層級，而不需要改變其位置。 您可以使用 map UI 來完成這項作業，或以程式設計方式呼叫[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法與使用目前位置做為[`Position`](xref:Xamarin.Forms.Maps.Position)引數的[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數：

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

在此範例中，會使用[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數來呼叫[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，以指定地圖的目前位置（透過[`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)屬性），並以緯度和經度的度數做為縮放層級。 整體結果是地圖的縮放層級已變更，但其位置不是。 在地圖上執行 zoom 的替代方法是使用[`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)方法來控制縮放因數。

> [!IMPORTANT]
> 縮放地圖（不論是透過地圖 UI 還是以程式設計方式），都需要 `true`[`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)屬性。 如需此屬性的詳細資訊，請參閱[停用縮放](#disable-zoom)。

## <a name="customize-map-behavior"></a>自訂地圖行為

您可以藉由設定部分屬性和處理 `MapClicked` 事件的方式來自訂[`Map`](xref:Xamarin.Forms.Maps.Map)的行為。

> [!NOTE]
> 藉由建立地圖自訂轉譯器，即可 customizatin 額外的對應行為。 如需詳細資訊，請參閱[自訂 Xamarin. 表單對應](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

### <a name="disable-scroll"></a>停用 scroll

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `bool`類型的[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)屬性。 根據預設，此屬性為 `true`，表示允許該地圖進行滾動。 當這個屬性設定為 `false`時，對應將不會滾動。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `bool`類型的[`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)屬性。 根據預設，此屬性為 `true`，表示可以在地圖上執行縮放。 當這個屬性設定為 `false`時，就無法縮放地圖。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `bool`類型的[`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)屬性。 根據預設，此屬性為 `false`，表示地圖並未顯示使用者目前的位置。 當此屬性設定為 [`true`] 時，地圖會顯示使用者目前的位置。 下列範例會示範如何設定此屬性：

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
> 在 iOS、Android 和通用 Windows 平臺上，存取使用者的位置必須已授與應用程式的「位置」許可權。 如需詳細資訊，請參閱[Platform configuration](setup.md#platform-configuration)。

### <a name="maintain-map-region-on-layout-change"></a>維護版面配置變更的地圖區域

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義 `bool`類型的 `MoveToLastRegionOnLayoutChange` 屬性。 根據預設，此屬性為 `true`，這表示顯示的對應區域會在配置變更發生時，從其目前的區域移至先前設定的區域，例如裝置旋轉時。 當這個屬性設定為 [`false`] 時，顯示的對應區域會在版面配置變更發生時保留在中央。 下列範例會示範如何設定此屬性：

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

### <a name="map-clicks"></a>對應點擊

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義在繪製對應時所引發的 `MapClicked` 事件。 事件隨附的 `MapClickedEventArgs` 物件具有名為 `Position`的單一屬性，其類型為[`Position`](xref:Xamarin.Forms.Maps.Position)。 引發事件時，`Position` 屬性會設定為已按下的地圖位置。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

下列程式碼範例顯示 `MapClicked` 事件的事件處理常式：

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此範例中，`OnMapClicked` 事件處理常式會輸出代表螺紋地圖位置的緯度和經度。 事件處理常式可以向 `MapClicked` 事件註冊，如下所示：

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

對等的 C# 程式碼為：

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [地圖位置和距離](position-distance.md)
- [自訂 Xamarin. 表單地圖](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
