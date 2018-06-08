---
title: 反白顯示在地圖上的區域
description: 本文說明如何將多邊形重疊影像加入至地圖中，反白顯示在地圖上的區域。 多邊形是封閉的圖形，其內部具有填入資訊。
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: e0ffa1948bb7dd0996dd21793237df550a32aa70
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848326"
---
# <a name="highlighting-a-region-on-a-map"></a>反白顯示在地圖上的區域

_本文說明如何將多邊形重疊影像加入至地圖中，反白顯示在地圖上的區域。多邊形是封閉的圖形，其內部具有填入資訊。_

## <a name="overview"></a>總覽

重疊，在地圖上的多層式的圖形。 覆疊支援會隨著與地圖已縮放的繪圖圖形內容。 下列螢幕擷取畫面顯示新增至地圖多邊形重疊的結果：

![](polygon-map-overlay-images/screenshots.png)

當[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`MapRenderer`類別具現化，這又會具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別具現化的原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別具現化的原生`MapControl`。 轉譯程序可以採取利用平台專屬的對應自訂項目實作所建立的自訂轉譯器`Map`每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 從自訂的地圖。
1. [自訂](#Customizing_the_Map)藉由建立自訂轉譯器對應的每個平台上的對應。

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

自訂地圖使用自訂轉譯器的相關資訊，請參閱[自訂地圖 Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>建立自訂地圖

建立的子類別[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)將新增的類別`ShapeCoordinates`屬性：

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

`ShapeCoordinates`屬性會儲存這些座標會定義要反白顯示區域的集合。

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

這類初始化指定一系列的緯度和經度座標，定義要反白顯示對應的區域。 然後將置於地圖的檢視[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/)方法，以建立變更的位置和地圖的縮放層級[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)從[ `Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)和[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自訂地圖

自訂轉譯器現在必須加入多邊形重疊影像加入至對應的每個應用程式專案。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法來加入多邊形重疊：

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

這個方法會執行下列設定中，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目：

- `MKMapView.OverlayRenderer`屬性設定為對應的委派。
- 緯度和經度座標的集合會從擷取`CustomMap.ShapeCoordinates`屬性並儲存成陣列的`CLLocationCoordinate2D`執行個體。
- 多邊形由呼叫靜態`MKPolygon.FromCoordinates`方法，其指定的緯度與經度的每個點。
- 多邊形，會藉由呼叫加入至地圖`MKMapView.AddOverlay`方法。 這個方法會自動關閉藉由繪製一條線連接的第一個和最後一個點的多邊形。

然後，實作`GetOverlayRenderer`方法，以自訂的覆疊轉譯：

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

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`和`OnMapReady`方法可新增多邊形重疊：

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

`OnElementChanged`方法會擷取的緯度和經度座標，從集合`CustomMap.ShapeCoordinates`屬性並將它們儲存在成員變數中。 然後它會呼叫`MapView.GetMapAsync`方法，取得基礎`GoogleMap`，為繫結至檢視，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目。 一次`GoogleMap`執行個體可供使用，`OnMapReady`方法將會叫用，多邊形由具現化`PolygonOptions`物件，指定的緯度與經度的每個點。 多邊形然後藉由呼叫加入至地圖`NativeMap.AddPolygon`方法。 這個方法會自動關閉藉由繪製一條線連接的第一個和最後一個點的多邊形。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立的自訂轉譯器

建立的子類別`MapRenderer`類別並覆寫其`OnElementChanged`方法來加入多邊形重疊：

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

假設的自訂轉譯器附加至新的 Xamarin.Forms 項目，這個方法會執行下列作業：

- 緯度和經度座標的集合會從擷取`CustomMap.ShapeCoordinates`屬性和已轉換成`List`的`BasicGeoposition`座標。
- 多邊形由具現化`MapPolygon`物件。 `MapPolygon`類別用來在地圖上顯示多點圖形，藉由設定其`Path`屬性`Geopath`物件，其中包含圖形座標。
- 多邊形會轉譯在地圖上將它加入至`MapControl.MapElements`集合。 請注意，多邊形自動關閉藉由繪製一條線連接的第一個和最後一個點。

## <a name="summary"></a>總結

本文說明如何新增至對應，來反白顯示的地圖區域的多邊形重疊。 多邊形是封閉的圖形，其內部具有填入資訊。


## <a name="related-links"></a>相關連結

- [多邊形對應重疊 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [自訂地圖釘選](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
