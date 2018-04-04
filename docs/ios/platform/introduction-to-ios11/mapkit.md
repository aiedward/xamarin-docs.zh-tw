---
title: 在 iOS 11 MapKit 的新功能
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 3b1ac8015a86292f00f26133277ead2f211dca33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="new-features-in-mapkit-on-ios-11"></a>在 iOS 11 MapKit 的新功能

iOS 11 MapKit 來新增下列功能：

- [叢集的註解](#clustering)
- [指南針按鈕](#compass)
- [標尺的檢視](#scale)
- [使用者追蹤按鈕](#user-tracking)

![對應顯示叢集的標記和指南針按鈕](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>自動縮放時群組標記

此範例[MapKit 範例 」 Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)示範如何實作新叢集功能的 iOS 11 註釋。

### <a name="1-create-an-mkpointannotation-subclass"></a>1.建立`MKPointAnnotation`子類別

點註解類別代表地圖上的每個標記。 可以將它們加入使用個別`MapView.AddAnnotation()`或陣列使用`MapView.AddAnnotations()`。

點的註解類別並沒有視覺表示法，只需要表示與標記相關聯的資料 (最重要的是，`Coordinate`屬性為其緯度和經度地圖上的)，以及任何自訂屬性：

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

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2.建立`MKMarkerAnnotationView`單一標記的子類別

標記註解檢視的視覺表示的每個註釋，並使用下列屬性的樣式：

- **MarkerTintColor** – 標記的色彩。
- **GlyphText** – 爦挴齌顯示文字。
- **GlyphImage** – 設定會顯示在標記的影像。
- **DisplayPriority** – 決定疊置順序 （堆疊行為） 的對應是擠滿標記時。 使用其中一種`Required`， `DefaultHigh`，或`DefaultLow`。

若要支援自動的叢集，您也必須設定：

- **ClusteringIdentifier** – 這會控制哪些標記取得叢集在一起。 您可以使用相同的識別項，您所有的標記，或使用不同的識別項來控制它們群組在一起的方式。

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

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3.建立`MKAnnotationView`來代表叢集的標記

雖然代表叢集的標記的註釋檢視_無法_是簡單的映像，使用者應該要提供有關多少標記分組在一起的視覺提示的應用程式。

[範例程式碼](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)使用 CoreGraphics 呈現的叢集，以及每個標記類型的比例的圓形圖形表示法中的標記數目。

您也應該設定：

- **DisplayPriority** – 決定疊置順序 （堆疊行為） 的對應是擠滿標記時。 使用其中一種`Required`， `DefaultHigh`，或`DefaultLow`。
- **CollisionMode** –`Circle`或`Rectangle`。

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

### <a name="4-register-the-view-classes"></a>4.註冊檢視類別

當正在建立地圖的檢視控制項，並加入到檢視，登錄要啟用自動的叢集行為，因為對應放大和縮小的註解檢視類型：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5.呈現地圖 ！

轉譯對應時，將叢集或轉譯的縮放層級根據註解標記。 當縮放層級變更時，標記以動畫顯示出叢集。

![叢集的標記顯示在地圖上的模擬器](mapkit-images/cyclemap-sml.png)

請參閱[對應區段](~/ios/user-interface/controls/ios-maps/index.md)如需有關利用 MapKit 顯示資料。

<a name="compass" />

## <a name="compass-button"></a>指南針按鈕

iOS 11 加入快顯對應超出指南針並呈現檢視中的其他功能。 請參閱[Tandm 範例應用程式](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)的範例。

建立按鈕看起來像羅盤 （包括即時動畫對應方向變更時），並在另一個控制項上呈現。

![在導覽列羅盤的按鈕](mapkit-images/compass-sml.png)

下列程式碼會建立羅盤的按鈕，並呈現在瀏覽列上：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass`屬性可以用來控制地圖檢視內的預設羅盤的可見性。

<a name="scale" />

## <a name="scale-view"></a>標尺的檢視

檢視使用中的其他位置中加入標尺`MKScaleView.FromMapView()`方法來取得標尺檢視，以檢視階層中其他位置新增執行個體。

![在地圖上顯示的小數位數檢視](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale`屬性可以用來控制地圖檢視內的預設羅盤的可見性。

<a name="user-tracking" />

## <a name="user-tracking-button"></a>使用者追蹤按鈕

使用者的 [追蹤] 按鈕會將使用者的目前位置上的地圖置中。 使用`MKUserTrackingButton.FromMapView()`方法來取得按鈕的執行個體、 套用格式設定的變更，檢視階層中其他位置加入。

![在地圖上顯示使用者位置 按鈕](mapkit-images/user-location-sml.png)

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

- [MapKit 範例 'Tandm'](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [什麼是新增在 MapKit (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/237/)
