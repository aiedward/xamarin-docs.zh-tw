---
title: 註釋及重疊在 Xamarin.iOS 中
description: 本文提供逐步的逐步解說，示範如何使用 Map Kit 的註釋及重疊的功能。 它示範如何將地圖加入 Xamarin 發展 2013年會議的位置顯示註釋及重疊的應用程式。
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 445661513b0cf79df99d54ed0bb4b0261dd75c2a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105427"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>註釋及重疊在 Xamarin.iOS 中

我們將在本逐步解說中建立的應用程式如下所示：

 [![](ios-maps-walkthrough-images/00-map-overlay.png "範例 MapKit 應用程式")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
您可以找到完整的程式碼中[對應的逐步解說範例](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)。

讓我們開始建立新**iOS 空專案**，並提供相關的名稱。 我們一開始先將程式碼新增至我們的檢視控制器來顯示 MapView，並接著會建立新的類別我們 MapDelegate，和自訂附註。 請遵循下列步驟來進行這項作業：

## <a name="viewcontroller"></a>ViewController


1. 新增下列命名空間，以`ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. 新增`MKMapView`執行個體類別的變數，連同`MapDelegate`執行個體。 我們將建立`MapDelegate`短時間內：

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. 在控制器的`LoadView`方法，加入`MKMapView`並將它設定為`View`控制器的屬性：

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    接下來，我們要在其中加入程式碼以初始化中的對應 'ViewDidLoad' 方法。

1. 在 `ViewDidLoad`加入程式碼來設定地圖類型、 顯示使用者位置，並允許縮放和移動瀏覽：

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. 接下來，加入程式碼將地圖置中，並設定它的區域：

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. 建立的新執行個體`MapDelegate`並將它指派給`Delegate`的`MKMapView`。 同樣地，我們將 implcodeent`MapDelegate`短時間內：

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. 從 iOS 8，您應該會要求授權從您的使用者使用其位置，讓我們將此加入我們的範例。 首先，定義`CLLocationManager`類別層級變數：

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. 在 `ViewDidLoad`方法中，我們想要檢查是否執行應用程式的裝置使用 iOS 8，以及它是否使用中應用程式時，我們會要求授權：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. 最後，我們需要編輯**Info.plist**檔案，以建議的原因，要求其位置的使用者。 在 **來源**功能表**Info.plist**，加入下列機碼：
    
    `NSLocationWhenInUseUsageDescription` 
    
    和字串： 

    `Maps Walkthrough Docs Sample`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs – 自訂附註的類別


1. 我們要呼叫的註解中使用的自訂類別`ConferenceAnnotation`。 將下列類別新增至專案：

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-新增註釋及重疊

1. 使用`ConferenceAnnotation`就地我們可以將其新增至對應。 回到`ViewDidLoad`方法的`ViewController`，在地圖的中心座標新增註解：

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. 我們也想要有重疊的旅館。 新增下列程式碼，以建立`MKPolygon`旅館提供，如使用的座標，並將它新增到對應中，呼叫`AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
如此即完成中的程式碼`ViewDidLoad`。 現在我們要實作我們`MapDelegate`類別來處理建立註解，並分別覆疊檢視。


## <a name="mapdelegate"></a>MapDelegate

1. 建立一個名為`MapDelegate`繼承自`MKMapViewDelegate`，並包含`annotationId`變數，以便做為重複使用識別項使用的註解：

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    我們只需要一個註釋此處以便重複使用程式碼不是絕對必要，但是最好將它加入。

1. 實作`GetViewForAnnotation`方法，以傳回一份檢視的`ConferenceAnnotation`使用**conference.png**映像隨附於此逐步解說：

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. 當使用者點選註解時，我們想要顯示影像顯示奧斯丁的縣 （市）。 新增下列變數以`MapDelegate`映像和要顯示它的檢視：

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. 接下來，若要顯示影像，當點選註解時，實作`DidSelectAnnotation`方法，如下所示：

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. 若要隱藏影像，當使用者取消註解選取點選在地圖上的其他地方時，實作`DidSelectAnnotationView`方法，如下所示：

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    我們現在會有註解的程式碼的位置。 剩下的就是將程式碼加入`MapDelegate`建立旅館覆疊的檢視。

1. 新增下列實作`GetViewForOverlay`至`MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

執行應用程式。 我們現在有互動式地圖與自訂註釋和覆疊 ！ 點選 註解和奧斯丁的影像隨即顯示，如下所示：

 [![](ios-maps-walkthrough-images/01-map-image.png "點選 註解，並顯示奧斯丁的映像")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>總結

這篇文章中我們探討了如何將註解加入至地圖，以及如何新增指定的多邊形的覆疊。 我們也會示範如何將觸控支援新增至要建立映像的動畫在地圖的註釋。


## <a name="related-links"></a>相關連結

- [地圖逐步解說範例](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)
- [示範的對應範例](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
