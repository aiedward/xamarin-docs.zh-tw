---
title: 反白顯示地圖上的循環的區域
description: 本文說明如何將循環重疊影像加入至地圖中，反白顯示對應的循環區域。
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 23f36bfbdc4638bb8f35dd2a55124a1438e1d441
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="highlighting-a-circular-area-on-a-map"></a>反白顯示地圖上的循環的區域

_本文說明如何將循環重疊影像加入至地圖中，反白顯示對應的循環區域。_

## <a name="overview"></a>總覽

重疊，在地圖上的多層式的圖形。 覆疊支援會隨著與地圖已縮放的繪圖圖形內容。 下列螢幕擷取畫面顯示循環重疊影像加入對應的結果：

![](circle-map-overlay-images/screenshots.png)

當[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`MapRenderer`類別具現化，這又會具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別具現化的原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別具現化的原生`MapControl`。 轉譯程序可以採取利用平台專屬的對應自訂項目實作所建立的自訂轉譯器`Map`每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 從自訂的地圖。
1. [自訂](#Customizing_the_Map)藉由建立自訂轉譯器對應的每個平台上的對應。

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/") 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)

自訂地圖使用自訂轉譯器的相關資訊，請參閱[自訂地圖 Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂地圖

建立`CustomCircle`類別具有`Position`和`Radius`屬性：

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

接著，建立的子類別[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)類別，將型別的屬性`CustomCircle`:

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

或者，使用`CustomMap`藉由宣告它的執行個體，在 C# 頁面執行個體中的控制項：

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

這類初始化加入[ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/)和`CustomCircle`至自訂對應中，執行個體，並具有地圖的檢視[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/)方法，這個方法會變更的位置和縮放藉由建立對應的層級[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)從[ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)和[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

自訂轉譯器現在必須加入將循環重疊影像加入至對應的每個應用程式專案。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法，將循環重疊：

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

這個方法會執行下列設定中，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目：

- `MKMapView.OverlayRenderer`屬性設定為對應的委派。
- 藉由設定靜態建立圓形`MKCircle`物件，以公尺為單位指定圓形的中心和圓形的半徑。
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

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`和`OnMapReady`方法，將循環重疊：

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

`OnElementChanged`方法呼叫`MapView.GetMapAsync`方法，取得基礎`GoogleMap`，為繫結至檢視，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目。 一次`GoogleMap`執行個體可供使用，`OnMapReady`方法將會叫用，圓形由具現化`CircleOptions`物件，以公尺為單位指定圓形的中心和圓形的半徑。 圓形然後藉由呼叫加入至地圖`NativeMap.AddCircle`方法。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立的自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法，將循環重疊：

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

假設的自訂轉譯器附加至新的 Xamarin.Forms 項目，這個方法會執行下列作業：

- 從擷取圓形位置與 radius`CustomMap.Circle`屬性，並傳遞給`GenerateCircleCoordinates`方法，會產生緯度和經度座標圓形的圓周。 這個 helper 方法的程式碼如下所示。
- 圓形周邊座標會轉換成`List`的`BasicGeoposition`座標。
- 由具現化 circle`MapPolygon`物件。 `MapPolygon`類別用來在地圖上顯示多點圖形，藉由設定其`Path`屬性`Geopath`物件，其中包含圖形座標。
- 多邊形會轉譯在地圖上將它加入至`MapControl.MapElements`集合。


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

本文說明如何將循環重疊影像加入至地圖中，反白顯示對應的循環區域。


## <a name="related-links"></a>相關連結

- [循環圖 Ovlerlay （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
