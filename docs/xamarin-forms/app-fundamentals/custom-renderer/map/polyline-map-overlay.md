---
title: 醒目提示地圖上的路線
description: 本文說明如何將聚合線條重疊新增至地圖。 聚合線條重疊是一系列的連接線段，通常用來顯示地圖上的路線，或形成任何需要的圖形。
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 19da0de80819a48259a28b30e003c32923d37378
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650690"
---
# <a name="highlighting-a-route-on-a-map"></a>醒目提示地圖上的路線

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)

_本文說明如何將聚合線條重疊新增至地圖。聚合線條重疊是一系列的連接線段，通常用來顯示地圖上的路線，或形成任何需要的圖形。_

## <a name="overview"></a>總覽

重疊是地圖上的多層次圖形。 重疊支援繪製隨地圖縮放比例的圖形內容。 下列螢幕擷取畫面顯示將聚合線條重疊新增至地圖的結果：

![](polyline-map-overlay-images/screenshots.png)

當 iOS 中的 Xamarin.Forms 應用程式轉譯 [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項時，會先具現化 `MapRenderer` 類別，接著具現化原生的 `MKMapView` 控制項。 在 Android 平台上，`MapRenderer` 類別會具現化原生 `MapView` 控制項。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 類別會具現化原生的 `MapControl`。 您可在每個平台上建立適用於 `Map` 的自訂轉譯器，利用轉譯程序實作平台特定的地圖自訂。 執行這項作業的流程如下：

1. [建立](#Creating_the_Custom_Map) Xamarin.Forms 自訂地圖。
1. [使用](#Consuming_the_Custom_Map) Xamarin.Forms 的自訂地圖。
1. 在每個平台上建立地圖自訂轉譯器以[自訂](#Customizing_the_Map)地圖。

> [!NOTE]
> 您必須先初始化及設定 [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)，才能使用。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

如需使用自訂轉譯器自訂地圖的資訊，請參閱[自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂地圖

建立 [`Map`](xref:Xamarin.Forms.Maps.Map) 類別的子類別，其會新增 `RouteCoordinates` 屬性：

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

`RouteCoordinates` 屬性會儲存座標集合，定義要醒目提示的路線。

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

這項初始化會指定一系列的經緯度座標，定義地圖上要醒目提示的路線。 然後使用 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法定位地圖的檢視，其從 [ `Position`](xref:Xamarin.Forms.Maps.Position) 和 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 建立 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 來變更地圖的位置和縮放層級。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

您現在必須將自訂轉譯器新增至每個應用程式專案，才能將聚合線條重疊新增至地圖。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增聚合線條重疊：

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

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列組態：

- `MKMapView.OverlayRenderer` 屬性設定為對應的委派。
- 經緯度座標集合擷取自 `CustomMap.RouteCoordinates` 屬性，並儲存為 `CLLocationCoordinate2D` 執行個體的陣列。
- 聚合線條是透過呼叫會指定每個點之經緯度的靜態 `MKPolyline.FromCoordinates` 方法來建立。
- 聚合線條是透過呼叫 `MKMapView.AddOverlay` 方法新增至地圖。

然後，實作 `GetOverlayRenderer` 方法來自訂重疊轉譯：

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

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 和 `OnMapReady` 方法，以新增聚合線條重疊：

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

`OnElementChanged` 方法會從 `CustomMap.RouteCoordinates` 屬性擷取經緯度座標集合，並將其儲存在成員變數中。 然後它會呼叫 `MapView.GetMapAsync` 方法，取得繫結至檢視的基礎 `GoogleMap` (前提是自訂轉譯器已附加至新的 Xamarin.Forms 項目)。 一旦有 `GoogleMap` 執行個體可用，就會叫用 `OnMapReady` 方法，其藉由具現化指定每個點之經緯度的 `PolylineOptions` 物件來建立建立聚合線條。 然後呼叫 `NativeMap.AddPolyline` 方法，將聚合線條新增至地圖。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增聚合線條重疊：

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

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列作業：

- 經緯度座標集合擷取自 `CustomMap.RouteCoordinates` 屬性，並轉換為 `BasicGeoposition` 座標的 `List`。
- 聚合線條是透過具現化 `MapPolyline` 物件所建立。 將其 `Path` 屬性設為包含線條座標的 `Geopath` 物件，使用 `MapPolygon` 類別來顯示地圖上的線條。
- 藉由將聚合線條新增至 `MapControl.MapElements` 集合來將其轉譯在地圖上。

## <a name="summary"></a>總結

本文說明如何將聚合線條重疊新增至地圖來顯示地圖上的路線，或形成任何需要的圖形。


## <a name="related-links"></a>相關連結

- [Polyline Map Ovlerlay (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline) (聚合線條地圖重疊 (範例))
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
