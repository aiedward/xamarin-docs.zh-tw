---
title: "地圖"
description: "iOS 包括 MapKit 架構，可讓您更輕鬆地加入對應至應用程式。 使用對應的套件，iOS 應用程式可以加入互動式支援功能，例如移動瀏覽和縮放、 地圖上顯示使用者位置及分層豐富圖形的對應。 這篇文章深入探討的數個套件對應的功能，顯示如何利用它們地理功能建置到應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3fecf17a4f70e44ca169c825bf0dd34a5127cec8
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="maps"></a>地圖

_iOS 包括 MapKit 架構，可讓您更輕鬆地加入對應至應用程式。使用對應的套件，iOS 應用程式可以加入互動式支援功能，例如移動瀏覽和縮放、 地圖上顯示使用者位置及分層豐富圖形的對應。這篇文章深入探討的數個套件對應的功能，顯示如何利用它們地理功能建置到應用程式_

地圖是所有現代的行動裝置作業系統中的常見功能。 iOS 提供原生透過對應組件架構的對應支援。 使用對應的套件，應用程式可以輕鬆加入豐富、 互動式的對應。 您可以自訂這些對應中有許多種，例如加上註解標記地圖上的位置，以及覆疊圖形的任意形狀。 對應組件即使具有用於顯示裝置的目前位置的內建支援。

## <a name="adding-a-map"></a>將地圖加入

將地圖加入至應用程式藉由加入`MKMapView`檢視階層架構中，執行個體，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` 是`UIView`顯示地圖的子類別。 只要新增對應使用上述程式碼會產生互動式的對應：

 ![](images/00-map.png "範例對應")

## <a name="map-style"></a>地圖類型

 `MKMapView` 支援 3 不同的地圖樣式。 若要套用地圖類型，只要設定`MapType`屬性中的值`MKMapType`列舉型別：
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  下列螢幕擷取畫面顯示可用的不同的地圖樣式：

 ![](images/01-mapstyles.png "這個螢幕擷取畫面顯示可用的不同的地圖樣式")

## <a name="panning-and-zooming"></a>移動和縮放

 `MKMapView` 包含地圖的互動功能的支援，例如：

-  透過捏合手勢縮放
-  移動瀏覽透過取景位置調整筆勢


這些功能可以啟用或停用只需要設定`ZoomEnabled`和`ScrollEnabled`屬性`MKMapView`執行個體，其中的預設值是兩個，則為 true。 例如，若要顯示的靜態對應，只需設定適當的屬性為 false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>使用者位置

使用者互動，除了`MKMapView`也有內建支援來顯示裝置的位置。 它會使用*核心位置*架構。 您可以存取使用者的位置之前，您必須提示使用者。 若要這樣做，建立的執行個體`CLLocationManager`呼叫`RequestWhenInUseAuthorization`。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

請注意，在 iOS 8.0，嘗試呼叫之前的版本`RequestWhenInUseAuthorization`會導致錯誤。 請務必檢查的 iOS 版本之前進行該呼叫，因此如果您想要支援 8 之前的版本。

存取使用者的位置也需要修改**Info.plist**。 以下是應該設定的位置資料相關機碼：

- **NSLocationWhenInUseUsageDescription** - 用於當您在使用者與應用程式互動期間存取使用者位置時。
- **NSLocationAlwaysUsageDescription** - 用於您的應用程式在背景中存取使用者的位置時。

您可以加入這些索引鍵開啟**Info.plist** ，然後選取*來源*編輯器的底部。

一旦您已更新**Info.plist**提示使用者輸入其位置的存取權限，您可以在地圖上顯示使用者的位置，藉由設定`ShowsUserLocation`屬性設定為 true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "允許的位置存取警示")
 
## <a name="annotations"></a>標註

 `MKMapView` 也支援稱為註解，在地圖上的顯示影像。 這些可以是自訂映像或系統定義的各種色彩的 pin。 例如，下列螢幕擷取畫面顯示具有這兩個 pin 的對應和自訂映像：

 ![](images/03-annotations.png "這個螢幕擷取畫面顯示具有這兩個 pin 的對應和自訂映像")

### <a name="adding-an-annotation"></a>加入註解

註解本身會有兩個部分：

-  `MKAnnotation`物件，其中包含模型資料的相關註釋，例如標題和註解的位置。
-  `MKAnnotationView` ，其中包含顯示和 （選擇性） 會顯示當使用者點選註解註標的映像。


對應的套件會使用 iOS 委派模式，將附註加入至地圖，其中`Delegate`屬性`MKMapView`設定的執行個體為`MKMapViewDelegate`。 它是負責傳回的這個委派實作`MKAnnotationView`註釋。

若要加入註解，先註解加入藉由呼叫`AddAnnotations`上`MKMapView`執行個體：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

註解的位置會顯示在地圖上，當`MKMapView`會呼叫它的委派`GetViewForAnnotation`方法來取得`MKAnnotationView`顯示。

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

若要節省記憶體，`MKMapView`可讓您檢視註解的共用以供重複使用，類似於資料表資料格會重複使用的方式。 取得從集區的註釋檢視是藉由呼叫`DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>顯示圖說文字

如先前所述，註解可以選擇性地顯示圖說文字。 若要只顯示圖說文字設定`CanShowCallout`為 true 上`MKAnnotationView`。 註解的標題所示點選註解，當顯示的結果：

 ![](images/04-callout.png "所顯示的註解標題")

### <a name="customizing-the-callout"></a>自訂圖說文字

您也可以將圖說文字自訂顯示左、 右配件檢視，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此程式碼會產生下列圖說文字：

 ![](images/05-callout-accessories.png "範例圖說文字")

若要處理點選右附屬應用程式的使用者，只要實作`CalloutAccessoryControlTapped`方法中的`MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>覆疊

在地圖上的圖層圖形另一個方法使用的影像覆疊。 覆疊支援會隨著與地圖已縮放的繪圖圖形內容。 iOS 提供數種覆疊，包括支援：

-  多邊形-通常用來反白顯示在地圖上的某些區域。
-  聚合線條的顯示路由時，經常看到。
-  圓形-用來反白顯示對應的循環的區域。


此外，可以建立自訂的覆疊來顯示任意的幾何，以更細微的自訂繪圖程式碼。 例如，天氣雷達圖會適合做為自訂的覆疊。

#### <a name="adding-an-overlay"></a>新增覆疊

類似於註釋，加入覆疊牽涉到 2 組件：

-  建立模型物件重疊，並將它加入至`MKMapView`。
-  建立檢視表中的覆疊`MKMapViewDelegate`。


重疊的模型可以是任何`MKShape`子類別。 Xamarin.iOS 包含`MKShape`多邊形、 聚合線條的圓圈，子類別透過`MKPolygon`，`MKPolyline`和`MKCircle`分別類別。

例如，下列程式碼會用於將加入`MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆疊的檢視是`MKOverlayView`所傳回的執行個體`GetViewForOverlay`中`MKMapViewDelegate`。 每個`MKShape`都有對應`MKOverlayView`，知道如何顯示給定的圖案。 如`MKPolygon`沒有`MKPolygonView`。 同樣地，`MKPolyline`對應至`MKPolylineView`，以及`MKCircle`沒有`MKCircleView`。

例如，下列程式碼傳回`MKCircleView`如`MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

在地圖中，如下所示，這會顯示圓形：

 ![](images/06-circle-overlay.png "在地圖上顯示的圓形")

## <a name="local-search"></a>當地搜尋

iOS 包含當地搜尋應用程式開發介面使用對應的套件，可讓非同步搜尋指定的地理區域中感興趣的點。

若要執行當地搜尋，應用程式必須遵循下列步驟：

1.  建立`MKLocalSearchRequest`物件。
1.  建立`MKLocalSearch`物件從`MKLocalSearchRequest`。
1.  呼叫`Start`方法`MKLocalSearch`物件。
1.  擷取`MKLocalSearchResponse`回呼中的物件。


當地搜尋 API 本身未提供使用者介面。 它甚至不需要使用對應。 不過，若要讓實際使用的當地搜尋，應用程式必須提供某種方式來指定搜尋查詢，並顯示結果。 此外，結果將包含位置資料，因為它通常會使得意義上，若要在地圖上顯示它們。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>加入本機搜尋 UI

接受輸入搜尋的一個方法是使用`UISearchBar`，其中所提供`UISearchController`，並將結果顯示資料表中。

下列程式碼加入`UISearchController`（具有的搜尋列屬性） 在`ViewDidLoad`方法`MapViewController`:

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

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

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

`SearchResultsUpdater`做為暫留處理器之間`searchController`的搜尋列中，搜尋結果。 

在此範例中，我們必須先建立中的搜尋方法`SearchResultsViewController`。 若要這樣做，我們必須建立`MKLocalSearch`物件，並使用它核發搜尋`MKLocalSearchRequest`，傳遞至回撥中擷取的結果`Start`方法`MKLocalSearch`物件。 然後會在傳回結果`MKLocalSearchResponse`物件，其中包含的陣列`MKMapItem`物件：

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

然後，在我們`MapViewController`我們將建立的自訂實作`UISearchResultsUpdating`，這指派給`SearchResultsUpdater`屬性我們`searchController`中[加入本機的搜尋 UI](#Adding_a_Local_Search_UI) > 一節：

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

上述實作地圖上加註解從結果中，選取項目如下所示：

 ![](images/08-search-results.png "註解加入至地圖中，從結果中選取項目")
 
> [!IMPORTANT]
> `UISearchController` 在 iOS 8 中實作。 如果您想要支援裝置之前，則您必須使用`UISearchDisplayController`。



## <a name="summary"></a>總結

檢查這篇文章*對應**套件*適用於 iOS 的架構。 首先，它看起來如何`MKMapView`類別可讓應用程式中包含的互動式對應。 然後它會示範如何使用附註和覆疊 map，進一步自訂。 最後，它會檢查本機搜尋功能加入至對應的套件與 iOS 6.1，示範如何使用執行感興趣的點的位置基礎查詢，並將其新增至對應。

## <a name="related-links"></a>相關連結

- [SearchController](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
- [MapDemo （範例）](https://developer.xamarin.com/samples/monotouch/MapDemo)
