---
title: IOS 11 上的 MapKit 新功能
description: 本檔說明 iOS 11 中的新 MapKit 功能：群組標記、羅盤按鈕、調整視圖，以及使用者追蹤按鈕。
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 8ccafec971a97a05dbf16488974d7a8e8e349708
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437331"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>IOS 11 上的 MapKit 新功能

iOS 11 將下列新功能新增至 MapKit：

- [批註群集](#clustering)
- [羅盤按鈕](#compass)
- [調整視圖](#scale)
- [使用者追蹤按鈕](#user-tracking)

![顯示叢集標記和羅盤按鈕的地圖](mapkit-images/cyclemap-heading.png)

<a name="clustering"></a>

## <a name="automatically-grouping-markers-while-zooming"></a>在縮放時自動群組標記

範例 [MapKit 範例 "Tandm"](/samples/xamarin/ios-samples/ios11-mapkitsample) 說明如何執行新的 iOS 11 批註叢集功能。

### <a name="1-create-an-mkpointannotation-subclass"></a>1. 建立子 `MKPointAnnotation` 類別

點注釋類別代表地圖上的每個標記。 您可以使用 `MapView.AddAnnotation()` 或從陣列個別新增它們 `MapView.AddAnnotations()` 。

點注釋類別沒有視覺標記法，因此只需要代表與標記相關聯的資料 (最重要的 `Coordinate` 是其在地圖上的緯度和經度) 的屬性，以及任何自訂屬性：

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. 建立 `MKMarkerAnnotationView` 單一標記的子類別

標記批註視圖是每個批註的視覺標記法，並使用下列屬性來設計樣式：

- **MarkerTintColor** –標記的色彩。
- **GlyphText** –顯示在標記中的文字。
- **GlyphImage** –設定標記中顯示的影像。
- **DisplayPriority** –決定當地圖與標記擁擠時，) z 順序 (堆疊行為。 使用、或的其中一個 `Required` `DefaultHigh` `DefaultLow` 。

若要支援自動群集，您也必須設定：

- **ClusteringIdentifier** –這會控制要將哪些標記聚集在一起。 您可以針對所有標記使用相同的識別碼，或使用不同的識別碼來控制它們群組在一起的方式。

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. 建立 `MKAnnotationView` 以代表標記的群集

雖然代表標記叢集的注釋視圖 _可能_ 是簡單的影像，但使用者預期應用程式會提供視覺提示，告訴您有多少標記已群組在一起。

[範例程式碼](/samples/xamarin/ios-samples/ios11-mapkitsample)會使用 CoreGraphics 來轉譯叢集中的標記數目，以及每個標記類型比例的圓形圖表表示。

您也應該設定：

- **DisplayPriority** –決定當地圖與標記擁擠時，) z 順序 (堆疊行為。 使用、或的其中一個 `Required` `DefaultHigh` `DefaultLow` 。
- **CollisionMode** – `Circle` 或 `Rectangle` 。

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. 註冊 view 類別

當建立地圖視圖控制項並將其加入至視圖時，註冊批註檢視類型可在地圖放大和放大時，啟用自動叢集行為：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. 轉譯地圖！

當地圖呈現時，會根據縮放層級將註解標記叢集或轉譯。 當縮放層級變更時，標記會在叢集內進行動畫。

![在地圖上顯示叢集標記的模擬器](mapkit-images/cyclemap-sml.png)

如需有關使用 MapKit 顯示資料的詳細資訊，請參閱 [Maps 一節](~/ios/user-interface/controls/ios-maps/index.md) 。

<a name="compass"></a>

## <a name="compass-button"></a>羅盤按鈕

iOS 11 新增了從地圖中取出羅盤的功能，並將其呈現在視圖的其他位置。 如需範例，請參閱 [Tandm 範例應用程式](/samples/xamarin/ios-samples/ios11-mapkitsample) 。

建立看起來像羅盤 (的按鈕，包括當地圖方向變更時的實況動畫) ，然後將它轉譯成另一個控制項。

![巡覽列中的羅盤按鈕](mapkit-images/compass-sml.png)

下列程式碼會建立羅盤按鈕，並將它呈現在巡覽列上：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass`屬性可以用來控制地圖視圖內預設羅盤的可見度。

<a name="scale"></a>

## <a name="scale-view"></a>調整視圖

您 `MKScaleView.FromMapView()` 可以使用方法來取得調整視圖的實例，以在視圖階層中的其他位置加入尺規。

![地圖上重迭的調整視圖](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale`屬性可以用來控制地圖視圖內預設羅盤的可見度。

<a name="user-tracking"></a>

## <a name="user-tracking-button"></a>使用者追蹤按鈕

[使用者追蹤] 按鈕會在使用者的目前位置上將地圖置中。 您 `MKUserTrackingButton.FromMapView()` 可以使用方法來取得按鈕的實例、套用格式變更，然後在視圖階層中的其他位置加入。

![地圖上重迭的使用者位置按鈕](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```

## <a name="related-links"></a>相關連結

- [MapKit 範例 ' Tandm '](/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [MapKit (WWDC 的新功能)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/237/)