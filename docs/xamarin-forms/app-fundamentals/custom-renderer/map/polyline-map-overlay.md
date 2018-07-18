---
title: 反白顯示地圖上的路由
description: 這篇文章說明如何新增至對應的聚合線條重疊。 聚合線條覆疊是一系列連接的直線線段，通常用來顯示在地圖上的路由，或形成所需的任何圖形。
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998716"
---
# <a name="highlighting-a-route-on-a-map"></a>反白顯示地圖上的路由

_這篇文章說明如何新增至對應的聚合線條重疊。聚合線條覆疊是一系列連接的直線線段，通常用來顯示在地圖上的路由，或形成所需的任何圖形。_

## <a name="overview"></a>總覽

覆疊是在地圖上的多層式的圖形。 覆疊支援會隨著它已縮放比例與對應的繪製圖形內容。 下列螢幕擷取畫面顯示新增至對應的聚合線條重疊的結果：

![](polyline-map-overlay-images/screenshots.png)

當[ `Map` ](xref:Xamarin.Forms.Maps.Map) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`MapRenderer`類別具現化，以依序具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別會具現化原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別會具現化原生`MapControl`。 轉譯程序可以藉由建立自訂轉譯器的實作特定平台對應自訂項目採取善用`Map`每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 自訂對應。
1. [自訂](#Customizing_the_Map)藉由建立自訂轉譯器，對應每個平台上的對應。

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

如需自訂對應，使用自訂轉譯器的資訊，請參閱[自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂的對應

建立的子類別[ `Map` ](xref:Xamarin.Forms.Maps.Map)類別，新增`RouteCoordinates`屬性：

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

`RouteCoordinates`屬性會儲存這些座標會定義要反白顯示的路由集合。

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

這項初始化指定一系列的緯度和經度座標，來定義路由會反白顯示地圖上。 它接著會將地圖的檢視[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，藉由建立變更的位置和地圖的縮放層級[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan)從[ `Position`](xref:Xamarin.Forms.Maps.Position)並[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

自訂轉譯器現在必須新增至每個應用程式專案，以新增至對應的聚合線條重疊。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法來加入聚合線條重疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

這個方法會執行下列設定中，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- `MKMapView.OverlayRenderer`屬性設定為對應的委派。
- 從擷取的緯度和經度座標集合`CustomMap.RouteCoordinates`屬性並儲存成陣列的`CLLocationCoordinate2D`執行個體。
- 聚合線條由呼叫靜態`MKPolyline.FromCoordinates`方法，以指定的緯度與經度的每個點。
- 聚合線條，會藉由呼叫加入至地圖`MKMapView.AddOverlay`方法。

然後，實作`GetOverlayRenderer`方法，以自訂的覆疊轉譯：

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`和`OnMapReady`方法以新增聚合線條重疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

`OnElementChanged`方法會擷取的緯度和經度座標，從集合`CustomMap.RouteCoordinates`屬性並將它們儲存在成員變數。 然後它會呼叫`MapView.GetMapAsync`方法，以取得基礎`GoogleMap`，會繫結至檢視，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素。 一次`GoogleMap`執行個體可供使用，`OnMapReady`方法會叫用，聚合線條由具現化`PolylineOptions`物件，指定的緯度與經度的每個點。 聚合線條然後藉由呼叫加入至地圖`NativeMap.AddPolyline`方法。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法來加入聚合線條重疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

這個方法會執行下列作業，，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- 從擷取的緯度和經度座標集合`CustomMap.RouteCoordinates`屬性和已轉換成`List`的`BasicGeoposition`座標。
- 聚合線條由具現化`MapPolyline`物件。 `MapPolygon`類別用來在地圖上顯示一條線，藉由設定其`Path`屬性設`Geopath`包含線條座標的物件。
- 聚合線條會轉譯在地圖上將它新增至`MapControl.MapElements`集合。

## <a name="summary"></a>總結

這篇文章說明如何將聚合線條重疊影像加入至地圖中，若要顯示在地圖上的路由，或形成所需的任何圖形。


## <a name="related-links"></a>相關連結

- [聚合線條地圖 Ovlerlay （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
