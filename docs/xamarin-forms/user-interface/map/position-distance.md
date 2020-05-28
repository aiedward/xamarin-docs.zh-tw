---
標題： Xamarin.Forms [地圖位置和距離] 描述： ' Xamarin.Forms 。Maps 命名空間包含位置結構，通常會在定位地圖和其釘選時使用，以及可選擇性地在定位對應時使用的距離結構。
assetid： ms. 技術： author： ms. 作者： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-map-position-and-distance"></a>Xamarin.Forms地圖位置和距離

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間包含的 [`Position`](xref:Xamarin.Forms.Maps.Position) 結構，通常會在定位地圖和其釘選時使用，以及 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 可選擇性地在定位對應時使用的結構。

## <a name="position"></a>位置

[`Position`](xref:Xamarin.Forms.Maps.Position)結構會封裝儲存為緯度和經度值的位置。 這個結構會定義兩個唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)，屬於類型 `double` ，表示以十進位為單位的位置緯度。
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，屬於類型 `double` ，表示以十進位為單位之位置的經度。

[`Position`](xref:Xamarin.Forms.Maps.Position)物件是使用「函式」所建立 `Position` ，其需要指定為值的緯度和經度引數 `double` ：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

建立物件時 `Position` ，緯度值會在-90.0 和90.0 之間壓制，而經度值將會在-180.0 和180.0 之間壓制。

> [!NOTE]
> `GeographyUtils`類別的 `ToRadians` 擴充方法會將 `double` 值從角度轉換成弧度，以及將 `ToDegrees` `double` 值從弧度轉換成度數的擴充方法。

## <a name="distance"></a>Distance

[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構會封裝儲存為值的距離 `double` ，代表以量為單位的距離。 此結構會定義三個唯讀屬性：

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，屬於類型 `double` ，表示所跨越的距離（以公里為限） `Distance` 。
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，屬於類型 `double` ，代表跨越的量（以量為單位） `Distance` 。
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，屬於類型 `double` ，表示所跨越的距離（以英里為單位） `Distance` 。

[`Distance`](xref:Xamarin.Forms.Maps.Distance)物件可以使用此函式來建立，而此函式 `Distance` 需要指定為的計量引數 `double` ：

```csharp
Distance distance = new Distance(1450.5);
```

或者，您 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 可以使用 [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) 、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) 、 [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) 和 `BetweenPositions` factory 方法來建立物件：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
