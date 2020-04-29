---
title: Xamarin. Forms 地圖控制項
description: 地圖控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗。
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 3861200446ea9c0e368aa251f3e7ec3f992c7152
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517529"
---
# <a name="xamarinforms-map-control"></a>Xamarin. Forms 地圖控制項

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗：

[![地圖控制項（在 iOS 和 Android 上）的螢幕擷取畫面](map-images/map-default.png "地圖控制項")](map-images/map-default-large.png#lightbox "地圖控制項")

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義可控制地圖外觀和行為的下列屬性：

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)，屬於類型`bool`，表示地圖是否顯示使用者目前的位置。
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，屬於類型`IEnumerable`，指定要顯示的`IEnumerable`專案集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)要套用至所顯示專案集合中的每個專案。
- `ItemTemplateSelector`，屬於型[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)別，它會[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)指定將用來在執行時間[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為專案選擇的。
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)，屬於類型`bool`，可決定是否允許地圖進行滾動。
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)，屬於類型`bool`，可決定是否允許地圖縮放。
- `MapElements`，屬於類型`IList<MapElement>`，代表地圖上的專案清單，例如多邊形和折線。
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)，屬於類型[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)，表示地圖的顯示樣式。
- `MoveToLastRegionOnLayoutChange`，屬於類型`bool`，可控制在版面配置變更發生時，所顯示的對應區域是否會從其目前的區域移至先前設定的區域。
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)，屬於類型`IList<Pin>`，代表地圖上的釘選清單。
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)型[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)別為的，會傳回目前顯示的地圖區域。

除了`MapElements`、 `Pins`和`VisibleRegion`屬性以外，這些屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標。

[`Map`](xref:Xamarin.Forms.Maps.Map)類別也會定義在`MapClicked`繪製對應時引發的事件。 伴隨`MapClickedEventArgs`事件的物件具有名為的單一屬性， `Position`其類型[`Position`](xref:Xamarin.Forms.Maps.Position)為。 當事件引發時， `Position`屬性會設定為所按下的地圖位置。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

如需[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)、 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)和`ItemTemplateSelector`屬性的相關資訊，請參閱[顯示 pin 集合](pins.md#display-a-pin-collection)。

## <a name="display-a-map"></a>顯示地圖

將[`Map`](xref:Xamarin.Forms.Maps.Map)其新增至版面配置或頁面，即可加以顯示：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> 需要額外`xmlns`的命名空間定義，才能參考 Xamarin. Forms 控制項。 在上述範例中， `Xamarin.Forms.Maps`命名空間是透過`maps`關鍵字來參考。

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

這個範例會呼叫預設[`Map`](xref:Xamarin.Forms.Maps.Map)的函式，將地圖置中為羅馬：

[![在 iOS 和 Android 上具有預設位置之地圖控制項的螢幕擷取畫面](map-images/map-default.png "具有預設位置的地圖控制項")](map-images/map-default-large.png#lightbox "具有預設位置的地圖控制項")

或者，您[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)也可以將引數傳遞[`Map`](xref:Xamarin.Forms.Maps.Map)至函式，以便在載入時設定地圖的中心點和縮放層級。 如需詳細資訊，請參閱[顯示地圖上的特定位置](#display-a-specific-location-on-a-map)。

## <a name="map-types"></a>對應類型

[`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性可以設定為[`MapType`](xref:Xamarin.Forms.Maps.MapType)列舉成員，以定義地圖的顯示樣式。 `MapType` 列舉會定義下列成員：

- `Street`指定將顯示街道對應。
- `Satellite`指定將顯示包含附屬影像的對應。
- `Hybrid`指定將會顯示與街道和附屬資料結合的對應。

根據預設，如果[`Map`](xref:Xamarin.Forms.Maps.Map)未定義[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性，則會顯示街道對應。 或者，您`MapType`也可以將屬性設定為其中一個[`MapType`](xref:Xamarin.Forms.Maps.MapType)列舉成員：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)當[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為`Street`時，下列螢幕擷取畫面會顯示：

[![在 iOS 和 Android 上具有街道對應類型的地圖控制項螢幕擷取畫面](map-images/maptype-street.png "具有街道 maptype 的地圖控制項")](map-images/maptype-street-large.png#lightbox "具有街道對應類型的地圖控制項")

[`Map`](xref:Xamarin.Forms.Maps.Map)當[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為`Satellite`時，下列螢幕擷取畫面會顯示：

[![在 iOS 和 Android 上具有衛星對應類型的地圖控制項螢幕擷取畫面](map-images/maptype-satellite.png "使用附屬 maptype 的地圖控制項")](map-images/maptype-satellite-large.png#lightbox "具有衛星對應類型的地圖控制項")

[`Map`](xref:Xamarin.Forms.Maps.Map)當[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定為`Hybrid`時，下列螢幕擷取畫面會顯示：

[![在 iOS 和 Android 上具有混合式地圖類型的地圖控制項螢幕擷取畫面](map-images/maptype-hybrid.png "使用混合式 maptype 的地圖控制項")](map-images/maptype-hybrid-large.png#lightbox "具有混合式地圖類型的地圖控制項")

## <a name="display-a-specific-location-on-a-map"></a>顯示地圖上的特定位置

載入對應時要顯示的地圖區域，可以藉由將[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數傳遞至函式來[`Map`](xref:Xamarin.Forms.Maps.Map)設定：

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

這個範例會建立[`Map`](xref:Xamarin.Forms.Maps.Map)物件，以顯示[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件所指定的區域。 `MapSpan`物件會以[`Position`](xref:Xamarin.Forms.Maps.Position)物件所代表的緯度和經度為中心，並橫跨0.01 緯度和0.01 經度度。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。 如需在 XAML 中傳遞引數的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

結果是當地圖顯示時，它會以特定位置為中心，並跨越特定的緯度和經度度：

[![在 iOS 和 Android 上具有指定位置之地圖控制項的螢幕擷取畫面](map-images/map-region.png "具有指定位置的地圖控制項")](map-images/map-region-large.png#lightbox "具有指定位置的地圖控制項")

## <a name="create-a-mapspan-object"></a>建立 MapSpan 物件

有數種方法可以建立[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件。 常見的方法是將必要的引數提供`MapSpan`給此函數。 這些是由[`Position`](xref:Xamarin.Forms.Maps.Position)物件表示的緯度和經度，而`double`代表所跨越之緯度和經度程度的值。 `MapSpan` 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

或者， [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)類別中有三個方法會傳回新`MapSpan`的物件：

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)傳回， `MapSpan`其具有與`LongitudeDegrees`方法的類別實例相同的，以及其`north`和`south`引數所定義的半徑。
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)`MapSpan`傳回由其[`Position`](xref:Xamarin.Forms.Maps.Position)和[`Distance`](xref:Xamarin.Forms.Maps.Distance)引數所定義的。
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)`MapSpan`傳回，其中心與方法的類別實例相同，但半徑乘以其`double`引數。

如需[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)一旦建立之後，即可存取下列屬性來抓取其相關資料：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)，代表[`Position`](xref:Xamarin.Forms.Maps.Position)的地理中心`MapSpan`。
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)，表示所跨越的緯度度數`MapSpan`。
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)，表示所跨越的經度度數`MapSpan`。
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)，表示`MapSpan`半徑。

## <a name="move-the-map"></a>移動地圖

您[`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)可以呼叫方法來變更地圖的位置和縮放層級。 這個方法會接受[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數，以定義要顯示的地圖區域及其縮放層級。

下列程式碼顯示在地圖上移動所顯示區域的範例：

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>縮放地圖

[`Map`](xref:Xamarin.Forms.Maps.Map)可以在不改變其位置的情況下變更的縮放層級。 這可以使用 map UI 來完成，或以程式設計方式呼叫[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法搭配使用[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)目前位置做為[`Position`](xref:Xamarin.Forms.Maps.Position)引數的引數：

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

在此範例中， [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)會使用[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)引數來呼叫方法，以指定地圖的目前位置（透過[`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)屬性）和縮放層級做為緯度和經度的度數。 整體結果是地圖的縮放層級已變更，但其位置不是。 在地圖上執行 zoom 的替代方法是使用[`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)方法來控制縮放因數。

> [!IMPORTANT]
> 縮放地圖（不論是透過地圖 UI 或以程式設計方式）， [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)都需要`true`屬性為。 如需此屬性的詳細資訊，請參閱[停用縮放](#disable-zoom)。

## <a name="customize-map-behavior"></a>自訂地圖行為

您[`Map`](xref:Xamarin.Forms.Maps.Map)可以藉由設定某些屬性，以及處理`MapClicked`事件來自訂的行為。

> [!NOTE]
> 藉由建立地圖自訂轉譯器，可進行其他的對應行為自訂。 如需詳細資訊，請參閱[自訂 Xamarin. 表單對應](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)。

### <a name="disable-scroll"></a>停用 scroll

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義類型[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) `bool`的屬性。 根據預設，此屬性`true`為，表示允許地圖進行滾動。 當這個屬性設定為`false`時，對應將不會滾動。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義類型[`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) `bool`的屬性。 根據預設，此屬性`true`為，表示可以在地圖上執行縮放。 當這個屬性設定為`false`時，就無法縮放地圖。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義類型[`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) `bool`的屬性。 根據預設，此屬性`false`為，表示地圖並未顯示使用者目前的位置。 當這個屬性設定為`true`時，地圖會顯示使用者目前的位置。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義類型`MoveToLastRegionOnLayoutChange` `bool`的屬性。 根據預設，此屬性`true`為，這表示顯示的對應區域會在配置變更發生時，從其目前的區域移至其先前設定的區域，例如裝置旋轉。 當這個屬性設定為`false`時，顯示的對應區域將會在版面配置變更時保留在中央。 下列範例會示範如何設定此屬性：

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

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義在`MapClicked`繪製對應時引發的事件。 伴隨`MapClickedEventArgs`事件的物件具有名為的單一屬性， `Position`其類型[`Position`](xref:Xamarin.Forms.Maps.Position)為。 當事件引發時， `Position`屬性會設定為所按下的地圖位置。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

下列程式碼範例顯示`MapClicked`事件的事件處理常式：

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此範例中， `OnMapClicked`事件處理常式會輸出代表螺紋對應位置的緯度和經度。 事件處理常式可以向`MapClicked`事件註冊，如下所示：

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
- [自訂 Xamarin.Forms 地圖](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
