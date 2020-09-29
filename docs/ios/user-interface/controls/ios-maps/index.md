---
title: Xamarin 中的 Maps
description: 本檔說明 iOS MapKit 架構，以及如何搭配使用它與 Xamarin。 它會討論如何新增地圖、樣式、平移和縮放、使用使用者位置、新增批註、使用標注和重迭，以及執行本機搜尋。
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 78c5d639ef75891c037529f270bfb36f776a12e7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436648"
---
# <a name="maps-in-xamarinios"></a>Xamarin 中的 Maps

Maps 是所有新式行動作業系統中的常見功能。 iOS 透過地圖套件架構以原生方式提供地圖支援。 使用地圖套件，應用程式可以輕鬆地加入豐富的互動式地圖。 您可以透過各種方式自訂這些對應，例如新增批註以標示地圖上的位置，以及覆迭任意圖形的圖形。 地圖套件甚至有內建的支援，可顯示裝置目前的位置。

## <a name="adding-a-map"></a>新增對應

將對應加入至 view 階層，即可完成將對應加入至應用程式 `MKMapView` ，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView` 是 `UIView` 顯示地圖的子類別。 只要使用上面的程式碼加入對應，就會產生互動式地圖：

![範例地圖](images/00-map.png)

## <a name="map-style"></a>地圖樣式

`MKMapView` 支援三種不同的地圖樣式。 若要套用地圖樣式，只要將 `MapType` 屬性設為列舉中的值即可 `MKMapType` ：

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

下列螢幕擷取畫面顯示可用的不同地圖樣式：

![此螢幕擷取畫面顯示可用的不同地圖樣式](images/01-mapstyles.png)

## <a name="panning-and-zooming"></a>移動流覽和縮放

`MKMapView` 包含地圖互動功能的支援，例如：

- 透過縮小手勢縮放
- 透過平移手勢移動

只要設定 `ZoomEnabled` 實例的和 `ScrollEnabled` 屬性 `MKMapView` （這兩者的預設值都是 true），即可啟用或停用這些功能。 例如，若要顯示靜態地圖，只要將適當的屬性設定為 false 即可：

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>使用者位置

除了使用者互動之外， `MKMapView` 也有內建的支援，可顯示裝置的位置。 它會使用 *核心位置* 架構來執行此工作。 在您可以存取使用者的位置之前，您必須先提示使用者。 若要這樣做，請建立的實例， `CLLocationManager` 並呼叫 `RequestWhenInUseAuthorization` 。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

請注意，在8.0 之前的 iOS 版本中，嘗試呼叫 `RequestWhenInUseAuthorization` 將會導致錯誤。 如果您想要支援8之前的版本，請務必先檢查 iOS 的版本，再進行呼叫。

存取使用者的位置也需要修改 **plist**。 以下是應該設定的位置資料相關機碼：

- **NSLocationWhenInUseUsageDescription** - 用於當您在使用者與應用程式互動期間存取使用者位置時。
- **NSLocationAlwaysUsageDescription** - 用於您的應用程式在背景中存取使用者的位置時。

您可以藉由開啟 **plist** 並選取編輯器底部的 [ *來源* ]，來新增這些金鑰。

當您更新 **plist** 並提示使用者有權存取其位置時，您可以將屬性設定為 true，以在地圖上顯示使用者的位置 `ShowsUserLocation` ：

```csharp
map.ShowsUserLocation = true;
```

 ![允許位置存取警示](images/02-location-alert.png)

## <a name="annotations"></a>註解

 `MKMapView` 也支援在地圖上顯示影像（稱為批註）。 這些可以是自訂影像或各種色彩的系統定義 pin。 例如，下列螢幕擷取畫面顯示具有 pin 和自訂影像的地圖：

 ![此螢幕擷取畫面顯示具有 pin 和自訂影像的地圖](images/03-annotations.png)

### <a name="adding-an-annotation"></a>新增批註

批註本身有兩個部分：

- `MKAnnotation`物件，其中包含批註的模型資料，例如注釋的標題和位置。
- `MKAnnotationView`，其中包含要顯示的影像，以及當使用者按下注釋時顯示的選擇性標注。

地圖套件會使用 iOS 委派模式將批註加入至對應，其中的 `Delegate` 屬性 `MKMapView` 會設定為的實例 `MKMapViewDelegate` 。 這是此委派的實作為，負責傳回批註的 `MKAnnotationView` 。

若要加入批註，首先會 `AddAnnotations` 在實例上呼叫來加入批註 `MKMapView` ：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

當批註的位置在地圖上變成可見時， `MKMapView` 會呼叫其委派的 `GetViewForAnnotation` 方法來取得 `MKAnnotationView` 顯示。

例如，下列程式碼會傳回系統提供的 `MKPinAnnotationView` ：

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>重複使用批註

為了節省記憶體， `MKMapView` 允許將批註視圖合併以供重複使用，類似于使用資料表單元格的方式。 從集區取得批註視圖是透過呼叫來完成 `DequeueReusableAnnotation` ：

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>顯示標注

如先前所述，批註可以選擇性地顯示標注。 若要顯示注標，在上，只會將設定 `CanShowCallout` 為 true `MKAnnotationView` 。 這會導致在按下注釋時顯示批註的標題，如下所示：

 ![要顯示的附注標題](images/04-callout.png)

### <a name="customizing-the-callout"></a>自訂標注

您也可以自訂注標以顯示左邊和右邊的配件視圖，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

這段程式碼會產生下列標注：

 ![範例標注](images/05-callout-accessories.png)

若要處理使用者按下正確的配件，只要 `CalloutAccessoryControlTapped` 在中執行方法即可 `MKMapViewDelegate` ：

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>重疊

在地圖上分層圖形的另一種方式是使用覆迭。 重疊支援繪製隨地圖縮放比例的圖形內容。 iOS 提供數種重迭類型的支援，包括：

- 多邊形-通常用來反白顯示地圖上的某些區域。
- 折線-顯示路線時通常會看到。
- 圓形-用來反白顯示地圖的圓形區域。

此外，您也可以建立自訂的重迭，以使用精細的自訂繪圖程式碼來顯示任意幾何。 例如，氣象雷達圖是自訂覆迭的好候選。

#### <a name="adding-an-overlay"></a>新增覆迭

類似于注釋，新增覆迭牽涉到兩個部分：

- 建立覆迭的模型物件，並將它加入至  `MKMapView` 。
- 在中建立覆蓋的視圖  `MKMapViewDelegate` 。

覆迭的模型可以是任何子 `MKShape` 類別。 Xamarin 包含 `MKShape` 多邊形、折線和圓形的子類別， `MKPolygon` 分別透過、 `MKPolyline` 和 `MKCircle` 類別。

例如，下列程式碼會用來新增 `MKCircle` ：

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆迭的視圖是 `MKOverlayView` 中由所傳回的實例 `GetViewForOverlay` `MKMapViewDelegate` 。 每個都 `MKShape` 有一個對應 `MKOverlayView` 的，它知道如何顯示指定的圖形。 `MKPolygon`有 `MKPolygonView` 。 同樣地， `MKPolyline` 會對應至 `MKPolylineView` ，而且 `MKCircle` 會有 `MKCircleView` 。

例如，下列程式碼會傳回的 `MKCircleView` `MKCircle` ：

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

這會顯示地圖上的圓形，如下所示：

 ![顯示在地圖上的圓形](images/06-circle-overlay.png)

## <a name="local-search"></a>本機搜尋

iOS 包含具有地圖套件的本機搜尋 API，可讓您在指定的地理區域中，以非同步方式搜尋感興趣的點。

若要執行本機搜尋，應用程式必須遵循下列步驟：

1. 建立  `MKLocalSearchRequest` 物件。
1. 從建立  `MKLocalSearch` 物件  `MKLocalSearchRequest` 。
1. 呼叫  `Start` 物件上的方法  `MKLocalSearch` 。
1. `MKLocalSearchResponse`在回呼中取出物件。

本機搜尋 API 本身不提供任何使用者介面。 甚至不需要使用對應。 不過，若要實際使用本機搜尋，應用程式必須提供某種方式來指定搜尋查詢並顯示結果。 此外，由於結果將會包含位置資料，因此在地圖上顯示它們通常是合理的。

<a name="Adding_a_Local_Search_UI"></a>

### <a name="adding-a-local-search-ui"></a>新增本機搜尋 UI

接受搜尋輸入的其中一種方式是使用 `UISearchBar` 提供的， `UISearchController` 並將結果顯示在資料表中。

下列程式碼會 `UISearchController` 在的方法中新增具有搜尋列屬性) 的 (`ViewDidLoad` `MapViewController` ：

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;

//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

請注意，您必須負責將搜尋列物件併入使用者介面。 在此範例中，我們將它指派給導覽列的 TitleView，但是如果您未在應用程式中使用流覽控制器，就必須尋找另一個位置來顯示它。

在此程式碼片段中，我們建立了另一個自訂視圖控制器– `searchResultsController` 會顯示搜尋結果，然後使用此物件來建立搜尋控制器物件。 我們也建立了新的搜尋更新程式，當使用者與搜尋列互動時，就會變成作用中。 它會接收有關每個按鍵搜尋的通知，並負責更新 UI。
我們將在 `searchResultsController` 本指南中討論如何執行和 `searchResultsUpdater` 稍後的。

這會導致在地圖上顯示搜尋列，如下所示：

 ![在地圖上顯示的搜尋列](images/07-searchbar.png)

### <a name="displaying-the-search-results"></a>顯示搜尋結果

若要顯示搜尋結果，我們必須建立自訂視圖控制器;通常為 `UITableViewController` 。 如上所示，在 `searchResultsController` 建立時，會傳遞給的函式 `searchController` 。
下列程式碼是如何建立這個自訂視圖控制器的範例：

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });

    }
}
```

### <a name="updating-the-search-results"></a>更新搜尋結果

會 `SearchResultsUpdater` 作為 `searchController` 搜尋列和搜尋結果之間的中繼程式。

在此範例中，我們必須先在中建立搜尋方法 `SearchResultsViewController` 。 若要這樣做，我們必須建立一個 `MKLocalSearch` 物件，並使用它來發出搜尋 `MKLocalSearchRequest` ，然後在傳遞給物件方法的回呼中取出結果 `Start` `MKLocalSearch` 。 然後，會在 `MKLocalSearchResponse` 包含物件陣列的物件中傳回結果 `MKMapItem` ：

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });

}
```

然後，在我們 `MapViewController` 的中，我們將建立的自訂執行 `UISearchResultsUpdating` ，它會指派給 `SearchResultsUpdater` 我們 `searchController` 在 [ [新增本機搜尋 UI](#Adding_a_Local_Search_UI) ] 區段中的屬性：

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

上述的執行會在從結果中選取專案時，將注釋加入至地圖，如下所示：

 ![從結果中選取專案時，新增至地圖的注釋](images/08-search-results.png)

> [!IMPORTANT]
> `UISearchController` 在 iOS 8 中實作為。 如果您想要支援早于此的裝置，您必須使用 `UISearchDisplayController` 。

## <a name="summary"></a>摘要

本文探討適用于 iOS 的*地圖**套件*架構。 首先，它探討了類別如何 `MKMapView` 允許互動式地圖包含在應用程式中。 接著示範如何使用批註和覆迭，進一步自訂對應。 最後，它會檢查已使用 iOS 6.1 新增至地圖套件的本機搜尋功能，並示範如何針對感興趣的點使用以位置為基礎的查詢，並將其新增至地圖。

## <a name="related-links"></a>相關連結

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (範例) ](/samples/xamarin/ios-samples/mapdemo)