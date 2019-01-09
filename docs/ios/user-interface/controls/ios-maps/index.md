---
title: 在 Xamarin.iOS 中的對應
description: 本文件說明 iOS MapKit 架構以及如何搭配 Xamarin.iOS。 它討論如何將地圖，樣式，移動瀏覽和縮放、 使用使用者位置、 加入註解、 使用圖說文字和覆疊，並執行本機搜尋。
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: c61b5a8bd99afda5e8fbeea44e3362574fa7feea
ms.sourcegitcommit: b18ceed35aa94999d13faf4a3e3177c0b9fc33b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084577"
---
# <a name="maps-in-xamarinios"></a>在 Xamarin.iOS 中的對應

對應是在所有現代的行動作業系統中的常見功能。 iOS 提供原生透過 Map Kit 架構的對應支援。 Map Kit 與應用程式可以輕鬆地加入豐富的互動式地圖。 在各種不同的方式，例如加入註解標記在地圖上的位置，以及在覆疊的任意形狀的圖形中，您可以自訂這些對應。 Map Kit 甚至還有用於顯示裝置的目前位置的內建支援。

## <a name="adding-a-map"></a>新增地圖

將地圖加入至應用程式藉由加入`MKMapView`檢視階層中，執行個體，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` 是`UIView`會顯示對應的子類別。 只要加上地圖使用上述程式碼會產生互動式地圖：

 ![](images/00-map.png "範例對應")

## <a name="map-style"></a>地圖樣式

 `MKMapView` 支援對應的 3 個不同的樣式。 若要套用地圖樣式，請設定`MapType`屬性設為從值`MKMapType`列舉型別：
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  下列螢幕擷取畫面顯示不同的地圖樣式可用：

 ![](images/01-mapstyles.png "此螢幕擷取畫面顯示有不同的地圖樣式")

## <a name="panning-and-zooming"></a>移動瀏覽和縮放

 `MKMapView` 包含地圖互動功能的支援，例如：

-  透過捏合手勢縮放
-  透過移動瀏覽軌跡移動瀏覽


這些功能可以啟用或停用，只`ZoomEnabled`並`ScrollEnabled`的屬性`MKMapView`執行個體，其中的預設值是兩個，則為 true。 比方說，若要顯示靜態地圖，只要設定適當的屬性為 false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>使用者位置

除了使用者互動，`MKMapView`也有內建支援顯示裝置的位置。 作法是使用*Core 位置*framework。 您可以存取使用者的位置之前，您必須提示使用者。 若要這樣做，建立的執行個體`CLLocationManager`並呼叫`RequestWhenInUseAuthorization`。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

請注意，在 iOS 8.0，嘗試呼叫之前的版本`RequestWhenInUseAuthorization`會導致錯誤。 請務必檢查的 iOS 版本，再進行呼叫，如果您想要支援 8 之前的版本。

存取使用者的位置也需要修改**Info.plist**。 以下是應該設定的位置資料相關機碼：

- **NSLocationWhenInUseUsageDescription** - 用於當您在使用者與應用程式互動期間存取使用者位置時。
- **NSLocationAlwaysUsageDescription** - 用於您的應用程式在背景中存取使用者的位置時。

您可以將這些索引鍵，開啟**Info.plist** ，然後選取*來源*編輯器的底部。

一旦您已更新**Info.plist**提示使用者輸入其位置的存取權限，您可以在地圖上顯示使用者的位置，藉由設定`ShowsUserLocation`屬性設為 true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "允許的位置存取警示")
 
## <a name="annotations"></a>標註

 `MKMapView` 也支援稱為 註解，在地圖上顯示影像。 這些可以是自訂映像或系統定義的 pin，各種色彩。 例如，下列螢幕擷取畫面顯示具有這兩個 pin 的對應和自訂映像：

 ![](images/03-annotations.png "此螢幕擷取畫面顯示具有這兩個 pin 的對應和自訂映像")

### <a name="adding-an-annotation"></a>新增註解

註解本身有兩個部分：

-  `MKAnnotation`物件，其中包含模型資料的相關註釋，例如標題和註解的位置。
-  `MKAnnotationView` ，其中包含要顯示和 （選擇性） 圖說文字會顯示當使用者點選註釋的映像。


Map Kit 使用 iOS 委派模式，來將附註加入至地圖，其中`Delegate`的屬性`MKMapView`設定為執行個體`MKMapViewDelegate`。 這是負責傳回的這個委派實作`MKAnnotationView`註釋。

若要加入註解，首先會加入註釋藉由呼叫`AddAnnotations`上`MKMapView`執行個體：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

在圖中可以看到的附註的位置時`MKMapView`會呼叫它的委派`GetViewForAnnotation`方法來取得`MKAnnotationView`來顯示。

例如，下列程式碼會傳回系統提供`MKPinAnnotationView`:

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

### <a name="reusing-annotations"></a>重複使用的註解

若要節省記憶體，`MKMapView`允許重複使用，便可重複使用表格儲存格的方式類似的共用中的註釋檢視。 取得註釋檢視從集區是藉由呼叫`DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>顯示的圖說文字

如先前所述，註解可以選擇性地顯示圖說文字。 若要只顯示一個圖說文字`CanShowCallout`設為 true，在`MKAnnotationView`。 這會導致註解的標題時，顯示點選註解時，所示：

 ![](images/04-callout.png "所顯示的註解標題")

### <a name="customizing-the-callout"></a>自訂圖說文字

圖說文字，也可以自訂向左和右配件檢視中，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此程式碼會產生下列的註標：

 ![](images/05-callout-accessories.png "範例圖說文字")

若要處理的點選正確的附屬應用程式的使用者，只要實作`CalloutAccessoryControlTapped`方法中的`MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>覆疊

在地圖上的圖層圖形的另一種方式使用重疊。 重疊支援繪製隨地圖縮放比例的圖形內容。 iOS 提供數種覆疊，包括支援：

-  多邊形-通常用來反白顯示地圖上的某些區域。
-  聚合線條-經常會看到顯示路由時。
-  圓形-用來反白顯示對應的循環區域。


此外，可以建立自訂覆疊顯示任意幾何與細微且可自訂的繪圖程式碼。 比方說，天氣雷達圖會是自訂的覆疊的良好候選項目。

#### <a name="adding-an-overlay"></a>新增覆疊

類似於註釋，新增覆疊包含 2 個部分：

-  建立用於覆疊模型物件，並將它新增至`MKMapView`。
-  建立檢視表中的覆疊`MKMapViewDelegate`。


重疊的模型可以是任何`MKShape`子類別。 包含 Xamarin.iOS`MKShape`子類別的多邊形，聚合線條和圓形，透過`MKPolygon`，`MKPolyline`和`MKCircle`分別為類別。

比方說，下列程式碼用來新增`MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆疊的檢視是`MKOverlayView`所傳回的執行個體`GetViewForOverlay`在`MKMapViewDelegate`。 每個`MKShape`都有對應`MKOverlayView`，知道如何顯示指定的圖形。 針對`MKPolygon`沒有`MKPolygonView`。 同樣地，`MKPolyline`對應至`MKPolylineView`，並針對`MKCircle`沒有`MKCircleView`。

例如，下列程式碼會傳回`MKCircleView`針對`MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

這會顯示圓形地圖上所示：

 ![](images/06-circle-overlay.png "地圖上顯示圓形")

## <a name="local-search"></a>當地搜尋

iOS 會包含當地搜尋 API 與 Map Kit，可讓非同步搜尋指定的地理區域中感興趣的點。

若要執行的本機搜尋，應用程式必須遵循下列步驟：

1.  建立`MKLocalSearchRequest`物件。
1.  建立`MKLocalSearch`物件從`MKLocalSearchRequest`。
1.  呼叫`Start`方法`MKLocalSearch`物件。
1.  擷取`MKLocalSearchResponse`於回呼中的物件。


當地搜尋 API 本身未提供使用者介面。 它甚至不需要使用對應。 不過，若要讓實際使用的當地搜尋，應用程式必須提供某種方式來指定搜尋查詢，並顯示結果。 此外，因為結果會包含位置資料，它通常會使得在地圖上顯示它們的意義。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>新增本機搜尋 UI

若要接受搜尋輸入的一個方法是使用`UISearchBar`，其中所提供`UISearchController`，並將結果顯示資料表中。

下列程式碼加入`UISearchController`（其中的搜尋列屬性） 中`ViewDidLoad`方法`MapViewController`:

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

請注意，您負責併入使用者介面中的搜尋列物件。 在此範例中，我們將它指派給 TitleView 的導覽列中，但是如果您未在您的應用程式中使用瀏覽控制器必須尋找另一個用來顯示它的地方。

在此程式碼片段中，我們建立另一個自訂檢視控制器 – `searchResultsController` – 顯示搜尋結果，並接著我們使用此物件來建立我們的搜尋控制站物件。 此外，我們也會建立新的搜尋更新程式，而成為作用中使用者互動的搜尋列時。 它會接收通知有關與每個按鍵輸入的搜尋，而且會負責更新 UI。
我們將探討如何實作這兩`searchResultsController`而`searchResultsUpdater`本指南稍後的。

這會產生如下所示，在地圖上顯示 [搜尋] 列中：

 ![](images/07-searchbar.png "在地圖上顯示的搜尋列")
 


### <a name="displaying-the-search-results"></a>顯示搜尋結果

若要顯示搜尋結果，我們需要建立自訂的檢視控制器;通常`UITableViewController`。 如上所示，`searchResultsController`傳遞至建構函式的`searchController`它正在建立時。
下列程式碼是如何建立此自訂的檢視控制器的範例：

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

### <a name="updating-the-search-results"></a>正在更新搜尋結果

`SearchResultsUpdater`做為中繼程序之間`searchController`的搜尋列中，搜尋結果。 

在此範例中，我們必須先建立中的搜尋方法`SearchResultsViewController`。 若要這樣做，我們必須建立`MKLocalSearch`物件，並使用它來發出搜尋`MKLocalSearchRequest`，在回呼中傳遞給擷取的結果`Start`方法`MKLocalSearch`物件。 然後會傳回結果`MKLocalSearchResponse`物件的陣列`MKMapItem`物件：

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

然後，在我們`MapViewController`我們將建立的自訂實作`UISearchResultsUpdating`，為指派給`SearchResultsUpdater`屬性我們`searchController`中[加入本機的搜尋 UI](#Adding_a_Local_Search_UI)區段：

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

上述實作地圖上加註解時，從結果選取項目，如下所示：

 ![](images/08-search-results.png "註解加入至地圖中，從結果中選取項目")
 
> [!IMPORTANT]
> `UISearchController` 在 iOS 8 中實作。 如果您想要支援裝置之前，則您必須使用`UISearchDisplayController`。



## <a name="summary"></a>總結

檢查這篇文章*地圖* *Kit*適用於 iOS 的架構。 首先，它會探討`MKMapView`類別可讓應用程式中要包含的互動式地圖。 然後它會示範如何使用註釋及重疊的對應，進一步自訂。 最後，它會檢查本機搜尋功能新增至對應套件使用 iOS 6.1，示範如何使用執行觀光點位置為基礎的查詢，並將其新增至對應。

## <a name="related-links"></a>相關連結

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo （範例）](https://developer.xamarin.com/samples/monotouch/MapDemo)
