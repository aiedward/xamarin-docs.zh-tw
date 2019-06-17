---
title: 醒目提示地圖上的區域
description: 本文說明如何將多邊形重疊新增至地圖，以醒目提示地圖上的區域。 多邊形是封閉圖形，且其內部已填滿。
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 01db31a797efc4b383f3bda3fbcf3bb91e0d38e1
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926046"
---
# <a name="highlighting-a-region-on-a-map"></a>醒目提示地圖上的區域

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/Map/Polygon/)

_本文說明了如何將多邊形重疊新增至地圖，以醒目提示地圖上的區域。多邊形是封閉圖形，且其內部已填滿。_

## <a name="overview"></a>總覽

重疊是地圖上的多層次圖形。 重疊支援繪製隨地圖縮放比例的圖形內容。 下列螢幕擷取畫面顯示將多邊形重疊新增至地圖的結果：

![](polygon-map-overlay-images/screenshots.png)

當 Xamarin.Forms 應用程式轉譯 [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項時，在 iOS 中會先具現化 `MapRenderer` 類別，再由該類別具現化原生 `MKMapView` 控制項。 在 Android 平台上，`MapRenderer` 類別會具現化原生 `MapView` 控制項。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 類別會具現化原生的 `MapControl`。 您可在每個平台上建立適用於 `Map` 的自訂轉譯器，利用轉譯程序實作平台特定的地圖自訂。 執行這項作業的流程如下：

1. [建立](#Creating_the_Custom_Map) Xamarin.Forms 自訂地圖。
1. [使用](#Consuming_the_Custom_Map) Xamarin.Forms 的自訂地圖。
1. 在每個平台上建立地圖自訂轉譯器以[自訂](#Customizing_the_Map)地圖。

> [!NOTE]
> 您必須先初始化及設定 [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)，才能使用。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

如需使用自訂轉譯器自訂地圖的資訊，請參閱[自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂地圖

建立 [`Map`](xref:Xamarin.Forms.Maps.Map) 類別的子類別，這會新增 `ShapeCoordinates` 屬性：

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

`ShapeCoordinates` 屬性會儲存座標集合，定義要醒目提示的區域。

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

這項初始化會指定一連串的經緯度座標，定義地圖上要醒目提示的區域。 然後使用 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法定位地圖的檢視，從 [ `Position`](xref:Xamarin.Forms.Maps.Position) 和 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 建立 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)，變更地圖的位置和縮放比例。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

您現在必須將自訂轉譯器新增至每個應用程式專案，才能將多邊形重疊新增至地圖。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增多邊形重疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列組態：

- `MKMapView.OverlayRenderer` 屬性設定為對應的委派。
- 經緯度座標集合擷取自 `CustomMap.ShapeCoordinates` 屬性，並儲存為 `CLLocationCoordinate2D` 執行個體的陣列。
- 多邊形是透過呼叫靜態 `MKPolygon.FromCoordinates` 方法，指定每個點的經緯度所建立。
- 多邊形是透過呼叫 `MKMapView.AddOverlay` 方法新增至地圖。 這個方法會透過繪製一條連接第一個點和最後一個點的線條來自動封閉多邊形。

然後，實作 `GetOverlayRenderer` 方法來自訂重疊轉譯：

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 和 `OnMapReady` 方法，以新增多邊形重疊：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

`OnElementChanged` 方法會從 `CustomMap.ShapeCoordinates` 屬性擷取經緯度座標集合，並將其儲存在成員變數中。 然後它會呼叫 `MapView.GetMapAsync` 方法，取得繫結至檢視的基礎 `GoogleMap` (前提是自訂轉譯器已附加至新的 Xamarin.Forms 項目)。 一旦有 `GoogleMap` 執行個體可用，就會叫用 `OnMapReady` 方法，其藉由具現化指定每個點之經緯度的 `PolygonOptions` 物件來建立多邊形。 然後呼叫 `NativeMap.AddPolygon` 方法，將多邊形新增至地圖。 這個方法會透過繪製一條連接第一個點和最後一個點的線條來自動封閉多邊形。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上建立自訂轉譯器

建立 `MapRenderer` 類別的子類別並覆寫其 `OnElementChanged` 方法，以新增多邊形重疊：

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

若自訂轉譯器已附加於新的 Xamarin.Forms 項目，則此方法會執行下列作業：

- 經緯度座標集合擷取自 `CustomMap.ShapeCoordinates` 屬性，並轉換為 `BasicGeoposition` 座標的 `List`。
- 多邊形是透過具現化 `MapPolygon` 物件所建立。 將其 `Path` 屬性設為包含圖形座標的 `Geopath` 物件，使用 `MapPolygon` 類別顯示地圖上的多點圖形。
- 將多邊形新增至 `MapControl.MapElements` 集合，轉譯在地圖上。 請注意，多邊形會透過繪製一條連接第一個點和最後一個點的線條來自動封閉。

## <a name="summary"></a>總結

本文說明了如何將多邊形重疊新增至地圖，以醒目提示地圖的區域。 多邊形是封閉圖形，且其內部已填滿。


## <a name="related-links"></a>相關連結

- [Polygon Map Overlay (Samples)](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/Map/Polygon/) (多邊形地圖重疊 (範例))
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
