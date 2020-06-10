---
標題：「 Xamarin.Forms 地圖多邊形、多線條和圓形」描述：「本文將說明如何在地圖實例上建立多邊形、折線和圓形 Xamarin.Forms 。」
assetid： CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/10/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-map-polygons-and-polylines"></a>Xamarin.Forms對應多邊形和折線

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

`Polygon`、 `Polyline` 和 `Circle` 元素可讓您反白顯示地圖上的特定區域。 `Polygon`是一個完全封閉的圖形，可以有筆觸和填滿色彩。 `Polyline`是不會完全括住區域的線條。 會反白 `Circle` 顯示地圖的迴圈區域：

「適用[![于 iOS 和 Android](polygons-images/polygon-polyline.png "地圖上的多邊形和折線")](polygons-images/polygon-polyline-large.png#lightbox "地圖上的多邊形和折線") 
 上的地圖多邊形和折線的螢幕擷取畫面」「 [![在 IOS 和 Android 上地圖圓形的螢幕擷取畫面](polygons-images/circle.png "地圖上的圓形")」](polygons-images/circle-large.png#lightbox "地圖上的圓形")

`Polygon`、 `Polyline` 和 `Circle` 類別衍生自 `MapElement` 類別，它會公開下列可系結的屬性：

- `StrokeColor`這是 `Color` 決定線條色彩的物件。
- `StrokeWidth`這是 `float` 決定線條寬度的物件。

`Polygon`類別會定義其他可系結屬性：

- `FillColor`這是 `Color` 決定多邊形背景色彩的物件。

此外， `Polygon` 和 `Polyline` 類別都會定義 `GeoPath` 屬性，這是 [`Position`](xref:Xamarin.Forms.Maps.Position) 指定圖形點的物件清單。

類別會定義下列可系結 `Circle` 屬性：

- `Center`是 [`Position`](xref:Xamarin.Forms.Maps.Position) 定義圓形中心的物件（以緯度和經度為限）。
- `Radius`這是一個 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 物件，可定義以量、公里或英里為單位的圓圈半徑。
- `FillColor`是用 `Color` 來決定圓形周邊內色彩的屬性。

> [!NOTE]
> 如果 `StrokeColor` 未指定屬性，筆劃會預設為黑色。 如果 `FillColor` 未指定屬性，則填滿會預設為透明。 因此，如果未指定任何屬性，則圖形會有黑色外框，沒有填滿。

## <a name="create-a-polygon"></a>建立多邊形

您 `Polygon` 可以將物件具現化，並將它新增至地圖的集合，以將它加入至對應 `MapElements` 。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

`StrokeColor`和 `StrokeWidth` 屬性會指定為自訂多邊形的外框。 `FillColor`屬性值會比對 `StrokeColor` 屬性值，但具有指定的 Alpha 值以使其成為透明，讓基礎地圖可以透過圖形顯示。 `GeoPath`屬性包含 `Position` 定義多邊形點地理座標的物件清單。 `Polygon`物件一旦加入至的集合之後，就會在地圖上呈現 `MapElements` `Map` 。

> [!NOTE]
> `Polygon`是一個完全封閉的圖形。 如果第一個和最後一個點不相符，就會自動連接。

## <a name="create-a-polyline"></a>建立折線

您 `Polyline` 可以將物件具現化，並將它新增至地圖的集合，以將它加入至對應 `MapElements` 。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

`StrokeColor`和 `StrokeWidth` 屬性會指定為自訂行。 `GeoPath`屬性包含定義聚合線條 `Position` 之地理座標的物件清單。 `Polyline`物件一旦加入至的集合之後，就會在地圖上呈現 `MapElements` `Map` 。

## <a name="create-a-circle"></a>建立圓形

您 `Circle` 可以將物件具現化，並將它新增至地圖的集合，以將它加入至對應 `MapElements` 。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
      <maps:Map>
          <maps:Map.MapElements>
              <maps:Circle StrokeColor="#88FF0000"
                           StrokeWidth="8"
                           FillColor="#88FFC0CB">
                  <maps:Circle.Center>
                      <maps:Position>
                          <x:Arguments>
                              <x:Double>37.79752</x:Double>
                              <x:Double>-122.40183</x:Double>
                          </x:Arguments>
                      </maps:Position>
                  </maps:Circle.Center>
                  <maps:Circle.Radius>
                      <maps:Distance>
                          <x:Arguments>
                              <x:Double>250</x:Double>
                          </x:Arguments>
                      </maps:Distance>
                  </maps:Circle.Radius>
              </maps:Circle>             
          </maps:Map.MapElements>
          ...
      </maps:Map>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map();

// Instantiate a Circle
Circle circle = new Circle
{
    Center = new Position(37.79752, -122.40183),
    Radius = new Distance(250),
    StrokeColor = Color.FromHex("#88FF0000"),
    StrokeWidth = 8,
    FillColor = Color.FromHex("#88FFC0CB")
};

// Add the Circle to the map's MapElements collection
map.MapElements.Add(circle);
```

地圖上的位置 `Circle` 是由和屬性的值所決定 `Center` `Radius` 。 `Center`屬性會定義圓形的中心（緯度和經度），而屬性則會 `Radius` 定義圓形的半徑（以量為單位）。 `StrokeColor`和 `StrokeWidth` 屬性會指定為自訂圓形的外框。 `FillColor`屬性值會指定圓形周邊內的色彩。 這兩個色彩值都會指定 Alpha 色板，讓基礎地圖可以透過圓形顯示。 `Circle`物件一旦加入至的集合之後，就會在地圖上轉譯 `MapElements` `Map` 。

> [!NOTE]
> `GeographyUtils`類別具有 `ToCircumferencePositions` 擴充方法，可將 `Circle` 物件（定義 `Center` 和 `Radius` 屬性值）轉換為 `Position` 組成圓形周邊之緯度和經度座標的物件清單。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
