---
title: Xamarin 中的對應
description: 本檔說明 iOS MapKit 架構，以及它如何與 Xamarin 搭配使用。 它討論如何新增地圖、樣式、平移和縮放、使用使用者位置、新增注釋、使用標注和重迭，以及執行本機搜尋。
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 60bf25d7d88a1772e8b742a336a5faaebdf964fa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290807"
---
# <a name="maps-in-xamarinios"></a>Xamarin 中的對應

Maps 是所有新式行動作業系統中的一項常見功能。 iOS 透過「地圖套件架構」提供原本的對應支援。 透過地圖套件，應用程式可以輕鬆地加入豐富的互動式地圖。 這些對應可以透過各種方式進行自訂，例如新增注釋以標記地圖上的位置，以及覆迭任意圖形的圖形。 地圖套件甚至具有內建支援，可顯示裝置的目前位置。

## <a name="adding-a-map"></a>新增對應

新增對應至應用程式的方式，是將`MKMapView`實例加入至視圖階層，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView`是顯示`UIView`對應的子類別。 只要使用上述程式碼新增對應，就會產生互動式地圖：

![](images/00-map.png "範例對應")

## <a name="map-style"></a>地圖樣式

`MKMapView`支援3種不同的對應樣式。 若要套用地圖樣式，只要將`MapType`屬性設定為`MKMapType`列舉中的值即可：

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

下列螢幕擷取畫面顯示可用的不同地圖樣式：

![](images/01-mapstyles.png "這個螢幕擷取畫面顯示可用的不同地圖樣式")

## <a name="panning-and-zooming"></a>移動流覽和縮放

`MKMapView`包含地圖互動功能的支援，例如：

- 透過縮小手勢縮放
- 透過平移手勢移動


只要設定`ZoomEnabled` `MKMapView`實例的和`ScrollEnabled`屬性，就可以啟用或停用這些功能，其中兩者的預設值都是 true。 例如，若要顯示靜態對應，只要將適當的屬性設為 false 即可：

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>使用者位置

除了使用者互動之外， `MKMapView`也提供內建的支援，以顯示裝置的位置。 它會使用*核心位置*架構來執行此工作。 在您可以存取使用者的位置之前，您必須先提示使用者。 若要這麼做，請建立的`CLLocationManager`實例， `RequestWhenInUseAuthorization`並呼叫。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

請注意，在8.0 之前的 iOS 版本中，嘗試呼叫`RequestWhenInUseAuthorization`將會導致錯誤。 如果您想要支援8之前的版本，請務必先檢查 iOS 版本，再進行該呼叫。

存取使用者的位置也需要修改**plist**。 以下是應該設定的位置資料相關機碼：

- **NSLocationWhenInUseUsageDescription** - 用於當您在使用者與應用程式互動期間存取使用者位置時。
- **NSLocationAlwaysUsageDescription** - 用於您的應用程式在背景中存取使用者的位置時。

您可以開啟 [ **plist** ]，然後選取編輯器底部的 [*來源*] 來新增這些金鑰。

當您更新**plist**並提示使用者取得其位置的存取權限後，您可以將`ShowsUserLocation`屬性設定為 true，以在對應上顯示使用者的位置：

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "允許位置存取警示")

## <a name="annotations"></a>標註

 `MKMapView`也支援在地圖上顯示影像，稱為「批註」。 這些可以是自訂影像或系統定義的各種色彩 pin。 例如，下列螢幕擷取畫面顯示具有 pin 和自訂影像的地圖：

 ![](images/03-annotations.png "此螢幕擷取畫面顯示具有 pin 和自訂影像的對應")

### <a name="adding-an-annotation"></a>加入批註

批註本身有兩個部分：

- `MKAnnotation`物件，包含關於注釋的模型資料，例如批註的標題和位置。
- `MKAnnotationView` ，其中包含要顯示的影像，以及當使用者按下注釋時所顯示的選擇性標注。


對應套件`Delegate` `MKMapView`會使用 iOS 委派模式將注釋加入至對應，其中的屬性會設定`MKMapViewDelegate`為的實例。 這是委派的實作為，負責`MKAnnotationView`傳回批註的。

若要新增批註，請先在`AddAnnotations` `MKMapView`實例上呼叫來加入批註：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

當注釋的位置會在地圖上顯示時， `MKMapView`會呼叫其委派的`GetViewForAnnotation`方法，以取得要顯示`MKAnnotationView`的。

例如，下列程式碼會傳回系統提供`MKPinAnnotationView`的：

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

為了節省記憶體， `MKMapView`允許將注釋視圖集區用於重複使用，類似于重複使用資料表單元格的方式。 從集區取得注釋視圖是透過呼叫來`DequeueReusableAnnotation`完成：

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>顯示標注

如先前所述，批註可以選擇性地顯示標注。 若要`CanShowCallout` `MKAnnotationView`在上顯示一個標注，請直接將設定為 true。 這會導致在按下注釋時顯示批註的標題，如下所示：

 ![](images/04-callout.png "要顯示的注釋標題")

### <a name="customizing-the-callout"></a>自訂標注

注標也可以自訂，以顯示左和右的配件視圖，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此程式碼會產生下列標注：

 ![](images/05-callout-accessories.png "範例標注")

若要處理使用者點擊正確的附件，只要`CalloutAccessoryControlTapped` `MKMapViewDelegate`在中執行方法：

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>覆疊

在地圖上將圖形階層式另一種方式是使用重迭。 重疊支援繪製隨地圖縮放比例的圖形內容。 iOS 提供數種重迭類型的支援，包括：

- 多邊形-通常用來反白顯示地圖上的某些區域。
- 折線-顯示路由時通常會出現。
- 圓形-用來反白顯示地圖的迴圈區域。


此外，您可以建立自訂重迭，以顯示具有細微、自訂繪圖程式碼的任意幾何。 例如，氣象雷達圖是自訂重迭的理想候選。

#### <a name="adding-an-overlay"></a>新增重迭

與注釋類似，新增覆迭牽涉到2個部分：

- 建立重迭的模型物件，並將它新增至`MKMapView` 。
- 在中`MKMapViewDelegate`建立重迭的視圖。


重迭的模型可以是任何`MKShape`子類別。 Xamarin 會分別透過`MKShape` `MKPolygon`、 `MKPolyline`和`MKCircle`類別，包含多邊形、折線和圓形的子類別。

例如，下列程式`MKCircle`代碼是用來加入：

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

重迭的視圖是`MKOverlayView`的實例，由中`MKMapViewDelegate`的所`GetViewForOverlay`傳回。 每`MKShape`個都有`MKOverlayView`對應的，知道如何顯示指定的形狀。 `MKPolygon`有。`MKPolygonView` 同樣地`MKPolyline` ，對應`MKPolylineView`至，而`MKCircle`在中`MKCircleView`則是。

例如，下列程式`MKCircleView` `MKCircle`代碼會傳回的：

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

這會在地圖上顯示一個圓形，如下所示：

 ![](images/06-circle-overlay.png "在地圖上顯示的圓形")

## <a name="local-search"></a>本機搜尋

iOS 包含具有地圖套件的本機搜尋 API，可讓您以非同步方式在指定的地理區域中搜尋感興趣的點。

若要執行本機搜尋，應用程式必須遵循下列步驟：

1. 建立`MKLocalSearchRequest`物件。
1. 從建立`MKLocalSearch`物件。 `MKLocalSearchRequest`
1. 在物件上呼叫`Start`方法 `MKLocalSearch` 。
1. 取得回呼`MKLocalSearchResponse`中的物件。


本機搜尋 API 本身不提供使用者介面。 它甚至不需要使用對應。 不過，若要實際使用本機搜尋，應用程式必須提供一些方法來指定搜尋查詢並顯示結果。 此外，由於結果會包含位置資料，因此在地圖上顯示時通常很合理。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>新增本機搜尋 UI

接受搜尋輸入的其中一種方式是`UISearchBar`使用（ `UISearchController`由提供），並將結果顯示在資料表中。

下列程式碼會`UISearchController` `ViewDidLoad`在的方法`MapViewController`中加入（具有搜尋列屬性）：

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

請注意，您必須負責將搜尋列物件併入使用者介面中。 在此範例中，我們將它指派給導覽列的 TitleView，但是如果您沒有在應用程式中使用流覽控制器，就必須尋找另一個位置來顯示它。

在此程式碼片段中，我們建立了另一個自`searchResultsController`定義視圖控制器––它會顯示搜尋結果，然後我們使用此物件來建立搜尋控制器物件。 我們也建立了新的搜尋更新程式，當使用者與搜尋列互動時，就會變成作用中。 它會接收每個擊鍵搜尋的相關通知，並負責更新 UI。
我們將探討如何執行`searchResultsController`本指南`searchResultsUpdater`稍後的和。

這會導致在地圖上顯示搜尋列，如下所示：

 ![](images/07-searchbar.png "在地圖上顯示的搜尋列")



### <a name="displaying-the-search-results"></a>顯示搜尋結果

若要顯示搜尋結果，我們需要建立自訂視圖控制器;通常是`UITableViewController`。 如上所示， `searchResultsController`會在建立`searchController`時傳遞至的的函式。
下列程式碼是如何建立此自訂視圖控制器的範例：

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

會作為的搜尋列和搜尋`searchController`結果之間的中繼程式。 `SearchResultsUpdater`

在此範例中，我們必須先在中`SearchResultsViewController`建立搜尋方法。 `MKLocalSearch`若要這麼做`MKLocalSearchRequest`，我們必須建立物件，並使用它來發出的搜尋，並在傳遞`MKLocalSearch`給`Start`物件方法的回呼中抓取結果。 然後，結果會在包含物件`MKLocalSearchResponse`陣列的`MKMapItem`物件中傳回：

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

然後， `MapViewController`我們將在我們的中建立自訂`UISearchResultsUpdating`的執行， `SearchResultsUpdater`它會指派給我們`searchController`在[新增本機搜尋 UI](#Adding_a_Local_Search_UI)一節中的屬性：

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

上述的執行方式會在從結果中選取專案時，將批註新增至地圖，如下所示：

 ![](images/08-search-results.png "從結果中選取專案時，新增至地圖的注釋")

> [!IMPORTANT]
> `UISearchController`已在 iOS 8 中執行。 如果您想要支援早于此的裝置，則必須使用`UISearchDisplayController`。



## <a name="summary"></a>總結

本文已檢查適用于 iOS 的*地圖* *套件*架構。 首先，它探討了`MKMapView`類別如何讓互動式對應包含在應用程式中。 然後示範如何使用注釋和重迭進一步自訂地圖。 最後，它會檢查已使用 iOS 6.1 新增至地圖套件的本機搜尋功能，並示範如何使用針對感利率的執行位置查詢，並將其新增至地圖。

## <a name="related-links"></a>相關連結

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
