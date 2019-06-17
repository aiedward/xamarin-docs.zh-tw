---
title: IOS 11 上 MapKit 中新功能
description: 本文件說明 iOS 11 中的新 MapKit 功能： 群組標記、 羅盤的按鈕、 [縮放] 檢視中和使用者的 [追蹤] 按鈕。
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3c1b29a4aba03ffe8a3131625ef29cf64766bb6c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342324"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>IOS 11 上 MapKit 中新功能

iOS 11 MapKit 中加入下列新功能：

- [叢集的註解](#clustering)
- [羅盤的按鈕](#compass)
- [刻度檢視](#scale)
- [使用者 [追蹤] 按鈕](#user-tracking)

![對應顯示叢集的標記和羅盤按鈕](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>自動群組標記時縮放

此範例[MapKit 範例 」 Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)示範如何實作新叢集功能的 iOS 11 註釋。

### <a name="1-create-an-mkpointannotation-subclass"></a>1.建立`MKPointAnnotation`子類別

點註解類別代表地圖上的每個標記。 它們可以加入使用個別`MapView.AddAnnotation()`或從陣列使用`MapView.AddAnnotations()`。

點的註解類別並沒有視覺表示法，他們只需要代表與標記相關聯的資料 (最重要的是，`Coordinate`也就是其緯度和經度，在地圖上的屬性)，以及任何自訂屬性：

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

標記註釋檢視每個註釋的視覺表示法，並使用下列屬性的樣式：

- **MarkerTintColor** – 標記的色彩。
- **GlyphText** – 在標記中顯示文字。
- **GlyphImage** – 設定會顯示在標記的影像。
- **DisplayPriority** – 決定疊置順序 （堆疊行為） 對應時太過擁擠時標記。 使用其中一種`Required`， `DefaultHigh`，或`DefaultLow`。

若要支援自動的叢集，您也必須設定：

- **ClusteringIdentifier** – 這會控制哪些標記取得叢集在一起。 您可以使用相同的識別項，針對您所有的標記，或使用不同的識別項來控制它們會群組在一起的方式。

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

雖然表示標記的叢集中的註釋檢視_無法_是簡單的映像，使用者期望應用程式提供視覺提示，多少標記分組在一起。

[範例程式碼](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)使用 CoreGraphics 呈現的叢集，以及每個標記類型的比例的圓形圖形表示法中的標記數目。

您也應該設定：

- **DisplayPriority** – 決定疊置順序 （堆疊行為） 對應時太過擁擠時標記。 使用其中一種`Required`， `DefaultHigh`，或`DefaultLow`。
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

當正在建立地圖的檢視控制項，並加入至檢視，註冊若要啟用自動的叢集行為，因為對應縮放的註解檢視類型：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5.呈現地圖 ！

轉譯的對應時，將叢集化或轉譯根據縮放層級註解標記。 當縮放層級變更時，標記以動畫顯示流入和流出叢集。

![顯示叢集的標記在地圖上的模擬器](mapkit-images/cyclemap-sml.png)

請參閱[對應區段](~/ios/user-interface/controls/ios-maps/index.md)如需有關使用 MapKit 顯示資料。

<a name="compass" />

## <a name="compass-button"></a>羅盤的按鈕

iOS 11 加入快顯縮小圖羅盤和其他轉譯檢視中的功能。 請參閱[Tandm 範例應用程式](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)的範例。

建立按鈕看起來像羅盤 （包括即時動畫對應方向變更時），並在另一個控制項上呈現。

![在導覽列中的羅盤按鈕](mapkit-images/compass-sml.png)

下列程式碼會建立羅盤的按鈕，並瀏覽列上呈現：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass`屬性可以用來控制地圖檢視內的預設羅盤的可見性。

<a name="scale" />

## <a name="scale-view"></a>刻度檢視

檢視使用中的其他位置新增 scale`MKScaleView.FromMapView()`方法取得檢視階層中其他地方加入刻度檢視的執行個體。

![重疊在地圖上的刻度檢視](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale`屬性可以用來控制地圖檢視內的預設羅盤的可見性。

<a name="user-tracking" />

## <a name="user-tracking-button"></a>使用者 [追蹤] 按鈕

使用者的 [追蹤] 按鈕會將使用者的目前位置上的地圖置中。 使用`MKUserTrackingButton.FromMapView()`來取得按鈕的執行個體、 套用格式設定的變更，以及加入其他地方檢視階層中的方法。

![重疊在地圖上的使用者位置 按鈕](mapkit-images/user-location-sml.png)

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
