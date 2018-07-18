---
title: 反白顯示地圖上的循環區域
description: 這篇文章說明如何將循環的覆疊加入至地圖中，反白顯示對應的循環區域。 IOS 和 Android 提供的 Api 新增至對應的循環的重疊，而 UWP 上覆疊會轉譯為多邊形。
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 3064296d4c78a3342fb27afc971c37a029987e5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998553"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>反白顯示地圖上的循環區域

_這篇文章說明如何將循環的覆疊加入至地圖中，反白顯示對應的循環區域。_

## <a name="overview"></a>總覽

覆疊是在地圖上的多層式的圖形。 覆疊支援會隨著它已縮放比例與對應的繪製圖形內容。 下列螢幕擷取畫面顯示新增至對應的循環的重疊的結果：

![](circle-map-overlay-images/screenshots.png)

當[ `Map` ](xref:Xamarin.Forms.Maps.Map) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`MapRenderer`類別具現化，以依序具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別會具現化原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別會具現化原生`MapControl`。 轉譯程序可以藉由建立自訂轉譯器的實作特定平台對應自訂項目採取善用`Map`每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 自訂對應。
1. [自訂](#Customizing_the_Map)藉由建立自訂轉譯器，對應每個平台上的對應。

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)

如需自訂對應，使用自訂轉譯器的資訊，請參閱[自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂的對應

建立`CustomCircle`類別具有`Position`和`Radius`屬性：

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

接著，建立的子類別[ `Map` ](xref:Xamarin.Forms.Maps.Map)類別，新增類型的屬性`CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>使用自訂地圖

取用`CustomMap`藉由宣告它的執行個體，在 XAML 頁面執行個體中的控制項：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

或者，使用`CustomMap`藉由宣告它的執行個體，在 C# page 執行個體中的控制項：

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

初始化`CustomMap`視需要控制：

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

這項初始化加入[ `Pin` ](xref:Xamarin.Forms.Maps.Pin)並`CustomCircle`自訂地圖上，執行個體，並將對應的檢視[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，可變更的位置和縮放藉由建立對應的層級[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan)從[ `Position` ](xref:Xamarin.Forms.Maps.Position)並[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

自訂轉譯器現在必須新增至每個應用程式專案，以新增至對應的循環的重疊。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`新增循環的覆疊的方法：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

這個方法會執行下列設定中，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- `MKMapView.OverlayRenderer`屬性設定為對應的委派。
- 藉由設定靜態建立圓形`MKCircle`物件，指定的圓圈，中心和圓形的半徑，以公尺為單位。
- 圓形，會藉由呼叫加入至地圖`MKMapView.AddOverlay`方法。

然後，實作`GetOverlayRenderer`方法，以自訂的覆疊轉譯：

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`和`OnMapReady`方法以新增循環的覆疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

`OnElementChanged`方法呼叫`MapView.GetMapAsync`方法，以取得基礎`GoogleMap`，會繫結至檢視，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素。 一次`GoogleMap`執行個體可供使用，`OnMapReady`方法會叫用，圓形由具現化`CircleOptions`物件，指定的圓圈，中心和圓形的半徑，以公尺為單位。 圓形接著藉由呼叫加入至地圖`NativeMap.AddCircle`方法。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`新增循環的覆疊的方法：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

這個方法會執行下列作業，，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- 從擷取的圓形位置和 radius`CustomMap.Circle`屬性，傳遞至`GenerateCircleCoordinates`方法，以產生緯度和經度座標的圓形的圓周。 這個 helper 方法的程式碼如下所示。
- 圓形周長座標會轉換成`List`的`BasicGeoposition`座標。
- 圓形由具現化`MapPolygon`物件。 `MapPolygon`類別用來顯示在地圖上的多點圖形，藉由設定其`Path`屬性設`Geopath`物件，其中包含的圖形座標。
- 多邊形呈現在地圖上將它加入至`MapControl.MapElements`集合。


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>總結

這篇文章說明如何將循環的覆疊加入至地圖中，反白顯示對應的循環區域。


## <a name="related-links"></a>相關連結

- [循環對應 Ovlerlay （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
