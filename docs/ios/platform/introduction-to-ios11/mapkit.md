---
title: IOS 11 上 MapKit 的新功能
description: 本檔說明 iOS 11 中的新 MapKit 功能：群組標記、羅盤按鈕、尺規視圖和使用者追蹤按鈕。
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 02bd25c4b4e251536dfdabdef109eb659fe3be37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032162"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>IOS 11 上 MapKit 的新功能

iOS 11 將下列新功能新增至 MapKit：

- [批註群集](#clustering)
- [羅盤按鈕](#compass)
- [調整視圖](#scale)
- [[使用者追蹤] 按鈕](#user-tracking)

![顯示叢集標記和羅盤按鈕的地圖](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>在縮放時自動分組標記

範例[MapKit 範例「Tandm](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) 」顯示如何執行新的 iOS 11 注釋叢集功能。

### <a name="1-create-an-mkpointannotation-subclass"></a>1. 建立 `MKPointAnnotation` 子類別

點批註類別代表地圖上的每個標記。 您可以使用 `MapView.AddAnnotation()` 或從使用 `MapView.AddAnnotations()`的陣列來個別新增它們。

點注釋類別沒有視覺標記法，只需要代表與標記相關聯的資料（最重要的是，`Coordinate` 屬性是地圖上的緯度和經度），以及任何自訂屬性：

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

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. 建立單一標記的 `MKMarkerAnnotationView` 子類別

標記批註視圖是每個注釋的視覺標記法，而且是使用屬性（例如）來設計樣式：

- **MarkerTintColor** –標記的色彩。
- **GlyphText** –標記中顯示的文字。
- **GlyphImage** –設定標記中顯示的影像。
- **DisplayPriority** –當對應與標記擁擠時，決定迭置順序（堆疊行為）。 使用其中一個 `Required`、`DefaultHigh`或 `DefaultLow`。

若要支援自動叢集，您也必須設定：

- **ClusteringIdentifier** –這會控制哪些標記會一起叢集化。 您可以針對所有標記使用相同的識別碼，或使用不同的識別碼來控制它們群組在一起的方式。

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

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. 建立 `MKAnnotationView` 來代表標記的群集

雖然代表標記叢集的注釋視圖可以是簡單的影像，但是使用者預期應用_程式會提供_有關已群組在一起的標記的視覺提示。

[範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)會使用 CoreGraphics 來轉譯叢集中的標記數目，以及每個標記類型之比例的圓形圖形標記法。

您也應該設定：

- **DisplayPriority** –當對應與標記擁擠時，決定迭置順序（堆疊行為）。 使用其中一個 `Required`、`DefaultHigh`或 `DefaultLow`。
- **CollisionMode** – `Circle` 或 `Rectangle`。

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

建立地圖視圖控制項並將其加入至視圖時，請註冊批註檢視類型，以啟用自動叢集行為，因為地圖已放大和縮小：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. 呈現地圖！

當對應呈現時，會根據縮放層級來叢集或轉譯註解標記。 當縮放層級變更時，標記會在叢集中建立動畫。

![在地圖上顯示叢集標記的模擬器](mapkit-images/cyclemap-sml.png)

如需使用 MapKit 顯示資料的詳細資訊，請參閱[Maps 一節](~/ios/user-interface/controls/ios-maps/index.md)。

<a name="compass" />

## <a name="compass-button"></a>羅盤按鈕

iOS 11 新增了從地圖中取出羅盤的功能，並將其呈現在視圖中的其他位置。 如需範例，請參閱[Tandm 範例應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)。

建立看起來像羅盤的按鈕（在地圖方向變更時包含即時動畫），並將它呈現在另一個控制項上。

![導覽列中的羅盤按鈕](mapkit-images/compass-sml.png)

下列程式碼會建立羅盤按鈕，並將其呈現在導覽列上：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass` 屬性可以用來控制地圖視圖內預設羅盤的可見度。

<a name="scale" />

## <a name="scale-view"></a>調整視圖

使用 `MKScaleView.FromMapView()` 方法，在視圖的其他位置新增尺規，以取得要在視圖階層中其他位置加入的尺規視圖實例。

![地圖上重迭的縮放視圖](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale` 屬性可以用來控制地圖視圖內預設羅盤的可見度。

<a name="user-tracking" />

## <a name="user-tracking-button"></a>[使用者追蹤] 按鈕

[使用者追蹤] 按鈕會將地圖放在使用者目前的位置上。 使用 `MKUserTrackingButton.FromMapView()` 方法來取得按鈕的實例、套用格式變更，並在視圖階層中的其他位置加入。

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

- [MapKit 範例 ' Tandm '](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [MapKit 的新功能（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/237/)
