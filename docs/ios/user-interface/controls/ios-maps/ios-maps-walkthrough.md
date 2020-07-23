---
title: Xamarin 中的注釋和重迭
description: 本文提供逐步解說，示範如何使用地圖套件的注釋和重迭功能。 它會示範如何將對應新增至應用程式，以在 Xamarin 演變2013會議的位置顯示注釋和重迭。
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 59ad6a11eecf629fc2a815e21a29493f4a1a1397
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932219"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Xamarin 中的注釋和重迭

我們將在本逐步解說中建立的應用程式如下所示：

 [![範例 MapKit 應用程式](ios-maps-walkthrough-images/00-map-overlay.png)](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)

您可以在[Maps 逐步解說範例](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)中找到完整的程式碼。

首先，我們要建立新的**IOS 空白專案**，並為它提供相關的名稱。 我們一開始會先將程式碼新增至我們的視圖控制器以顯示 MapView，然後再為我們的 MapDelegate 建立新的類別，以及自訂注釋。 請遵循下列步驟來進行這項作業：

## <a name="viewcontroller"></a>ViewController

1. 將下列命名空間新增至 `ViewController` ：

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. 將 `MKMapView` 執行個體變數連同實例一起加入至類別 `MapDelegate` 。 我們很快就會建立 `MapDelegate` ：

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. 在控制器的 `LoadView` 方法中，新增 `MKMapView` ，並將它設定為 `View` 控制器的屬性：

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    接下來，我們會新增程式碼，以初始化 ' ViewDidLoad ' ' 方法中的對應。

1. 在 [加入程式碼] 中 `ViewDidLoad` ，若要設定地圖類型，請顯示使用者位置，並允許縮放和移動：

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;

    ```

1. 接下來，新增程式碼以置中地圖，並將其設定為區域：

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;

    ```

1. 建立的新實例 `MapDelegate` ，並將它指派給的 `Delegate` `MKMapView` 。 同樣地，我們很快就會執行 `MapDelegate` ：

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;
    ```

1. 從 iOS 8 開始，您應該向使用者要求授權以使用其位置，因此讓我們將其新增至我們的範例。 首先，定義 `CLLocationManager` 類別層級的變數：

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. 在 `ViewDidLoad` 方法中，我們想要檢查執行應用程式的裝置是否使用 iOS 8，如果是，我們會在應用程式使用中時要求授權：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
        locationManager.RequestWhenInUseAuthorization ();
    }
    ```

1. 最後，我們需要編輯**plist**檔案，以通知使用者要求其位置的原因。 在**plist**的 [**來源**] 功能表中，新增下列機碼：

    `NSLocationWhenInUseUsageDescription`

    和字串：

    `Maps Walkthrough Docs Sample`.

## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs –自訂批註的類別

1. 我們會將自訂類別用於名為的注釋 `ConferenceAnnotation` 。 將下列類別新增至專案：

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

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-新增注釋和重迭

1. 備妥之後， `ConferenceAnnotation` 我們可以將它新增至地圖。 回到的 `ViewDidLoad` 方法 `ViewController` ，在地圖的中心座標處新增注釋：

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter));
    ```

1. 我們也想要有一個飯店的重迭。 新增下列 `MKPolygon` 程式碼，以使用所提供之飯店的座標來建立，並藉由呼叫將其新增至地圖 `AddOverlay` ：

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

這會完成中的程式碼 `ViewDidLoad` 。 現在，我們需要實 `MapDelegate` 作為類別來分別處理批註和重迭視圖的建立。

## <a name="mapdelegate"></a>MapDelegate

1. 建立一個名為 `MapDelegate` 的類別，它會繼承自 `MKMapViewDelegate` ，並包含 `annotationId` 要做為批註重複使用識別碼的變數：

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```

    這裡只有一個批註，因此重複使用的程式碼並不是絕對必要的，但最好是將它納入。

1. `GetViewForAnnotation` `ConferenceAnnotation` 使用本逐步解說中包含的**conference.png**影像，執行方法以傳回的視圖：

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

1. 當使用者按下注釋時，我們想要顯示顯示奧斯丁城市的影像。 將下列變數新增至影像的， `MapDelegate` 並將它顯示為：

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. 接下來，若要在按下注釋時顯示影像，請依照下列方式執行 `DidSelectAnnotation` 方法：

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

1. 若要在使用者藉由點擊地圖上的任何位置來取消選擇批註時隱藏影像，請依照下列方式執行 `DidDeselectAnnotationView` 方法：

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

    我們現在已備妥批註的程式碼。 剩下的就是將程式碼新增至， `MapDelegate` 以建立飯店重迭的視圖。

1. 將下列的實作為新增 `GetViewForOverlay` 至 `MapDelegate` ：

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

執行應用程式。 我們現在有一個互動式地圖，其中包含自訂注釋和重迭！ 按一下注釋，即會顯示 [奧斯丁] 的影像，如下所示：

 [![按一下注釋，即會顯示 [奧斯丁] 的影像](ios-maps-walkthrough-images/01-map-image.png)](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>總結

在本文中，我們探討了如何將批註新增至地圖，以及如何為指定的多邊形加入重迭。 我們也示範了如何將觸控支援新增至注釋，以在地圖上建立影像的動畫。

## <a name="related-links"></a>相關連結

- [Maps 逐步解說範例](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)
- [地圖示范範例](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
