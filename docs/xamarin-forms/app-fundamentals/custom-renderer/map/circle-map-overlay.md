---
title: 醒目提示地圖上的循環區域
description: 本文說明如何將循環重疊新增至地圖中，以醒目提示地圖的循環區域。 iOS 和 Android 會提供 API 用於將循環重疊新增至地圖，而重疊在 UWP 上會轉譯為多邊形。
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 551dea5455ffd060d808aa11e8996c5984745fda
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771902"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>醒目提示地圖上的循環區域

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-circle)

_本文說明如何將循環重疊新增至地圖中，以醒目提示地圖的循環區域。_

## <a name="overview"></a>總覽

重疊是地圖上的多層次圖形。 重疊支援繪製隨地圖縮放比例的圖形內容。 下列螢幕擷取畫面顯示將循環重疊新增至地圖的結果：

![](circle-map-overlay-images/screenshots.png)

當 Xamarin.Forms 應用程式轉譯 [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項時，在 iOS 中會先具現化 `MapRenderer` 類別，再由該類別具現化原生 `MKMapView` 控制項。 在 Android 平台上，`MapRenderer` 類別會具現化原生 `MapView` 控制項。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 類別會具現化原生的 `MapControl`。 您可在每個平台上建立適用於 `Map` 的自訂轉譯器，利用轉譯程序實作平台特定的地圖自訂。 執行這項作業的流程如下：

1. [建立](#Creating_the_Custom_Map) Xamarin.Forms 自訂地圖。
1. [使用](#Consuming_the_Custom_Map) Xamarin.Forms 的自訂地圖。
1. 在每個平台上建立地圖自訂轉譯器以[自訂](#Customizing_the_Map)地圖。

> [!NOTE]
> 在使用 [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 前，您必須先加以初始化及設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)

如需使用自訂轉譯器自訂地圖的資訊，請參閱[自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂地圖

建立具有 `Position` 和 `Radius` 屬性的 `CustomCircle` 類別：

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

接著，建立 [`Map`](xref:Xamarin.Forms.Maps.Map) 類別的子類別，這會新增類型 `CustomCircle` 的屬性：

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>使用自訂地圖

您可以在 XAML 頁面執行個體中宣告 `CustomMap` 控制項的執行個體來使用它：

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

或者，在 C# 頁面執行個體中宣告其執行個體，藉以使用 `CustomMap` 控制項：

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

視需要初始化 `CustomMap` 控制項：

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

此初始化會將 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 和 `CustomCircle` 執行個體新增至自訂地圖，並使用 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法定位地圖的檢視，從 [`Position`](xref:Xamarin.Forms.Maps.Position) 和 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 建立 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)，變更地圖的位置和縮放比例。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

您現在必須將自訂轉譯器新增至每個應用程式專案，才能將循環重疊新增至地圖。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增循環重疊：

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

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列組態：

- `MKMapView.OverlayRenderer` 屬性設定為對應的委派。
- 藉由設定會指定圓形中心的靜態 `MKCircle` 物件和圓半徑 (以公尺為單位) 來建立圓形。
- 圓形可透過呼叫 `MKMapView.AddOverlay` 方法新增至地圖。

然後，實作 `GetOverlayRenderer` 方法來自訂重疊轉譯：

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

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 和 `OnMapReady` 方法，以新增循環重疊：

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

若自訂轉譯器已附加到新的 Xamarin.Forms 項目，`OnElementChanged` 方法便會呼叫 `MapView.GetMapAsync` 方法來取得與檢視繫結的基礎 `GoogleMap`。 一旦 `GoogleMap` 執行個體可用，就會叫用 `OnMapReady` 方法，透過具現化會指定圓形中心和圓半徑 (以公尺為單位) 的 `CircleOptions` 物件來建立圓形。 然後，會呼叫 `NativeMap.AddCircle` 方法將圓形新增至地圖。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增循環重疊：

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

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列作業：

- 圓形位置和半徑會從 `CustomMap.Circle` 屬性擷取並傳遞至 `GenerateCircleCoordinates` 方法，該方法會產生圓形周長的緯度和經度座標。 此協助程式方法的程式碼如下所示。
- 圓形周長座標會轉換成 `BasicGeoposition` 座標的 `List`。
- 圓形可透過具現化 `MapPolygon` 物件來建立。 將其 `Path` 屬性設為包含圖形座標的 `Geopath` 物件，使用 `MapPolygon` 類別顯示地圖上的多點圖形。
- 將多邊形新增至 `MapControl.MapElements` 集合以將其轉譯在地圖上。

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

本文說明了如何將循環重疊新增至地圖中，以醒目提示地圖的循環區域。

## <a name="related-links"></a>相關連結

- [Circular Map Ovlerlay (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-circle) (循環地圖重疊 (範例))
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
