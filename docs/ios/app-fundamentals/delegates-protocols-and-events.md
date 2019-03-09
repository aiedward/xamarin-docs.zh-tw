---
title: 事件、 通訊協定與委派在 Xamarin.iOS 中
description: 本文件說明如何使用事件時，通訊協定，並在 Xamarin.iOS 中委派。 這些基本的概念都廣泛運用 Xamarin.iOS 開發。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: 5ccefdb5e527e67338714896905734c74278d00a
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671870"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>事件、 通訊協定與委派在 Xamarin.iOS 中

Xamarin.iOS 使用控制項，來公開大部分的使用者互動的事件。
Xamarin.iOS 應用程式會使用這些事件在相同的方式如同傳統的.NET 應用程式。 比方說，Xamarin.iOS 標記的 UIButton 類別稱為 TouchUpInside 的事件，並取用此事件，就如同此類別與事件是一樣的.NET 應用程式中。

除了這個.NET 方法時，Xamarin.iOS 會公開可用於更複雜的互動和資料繫結的另一個模型。 這個方法會使用 Apple 所謂的委派和通訊協定。 委派是委派中的概念類似C#，但不需要定義和呼叫一個方法，在 OBJECTIVE-C 中的委派是整個類別符合通訊協定。 通訊協定是在介面類似C#，不同之處在於它的方法可以是選擇性。 比方說，若要填入資料 UITableView，您會建立委派實作的類別，來填入本身呼叫 UITableView 一樣 UITableViewDataSource 通訊協定中定義的方法。

在此文章中，您將了解所有這些主題，讓您奠定堅固的基礎來處理在 Xamarin.iOS 中的回撥案例包括：

- **事件**– UIKit 控制項使用的.NET 事件。
- **通訊協定**– 學習哪些通訊協定，以及其使用方式，並建立範例提供的對應註釋的資料。
- **委派**– 認識 OBJECTIVE-C 委派，藉由擴充 map 為例來處理使用者互動，其中包含註解，然後學習強式和弱式委派，以及何時使用每一種之間的差異。

為了說明通訊協定與委派，我們將建置一個簡單的地圖應用程式，將註解加入至地圖，如下所示：

[![](delegates-protocols-and-events-images/01-map-sml.png "將註解加入至對應的簡單地圖應用程式範例")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "範例註解加入至對應")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

之前處理此應用程式，我們可以開始查看 UIKit 下的.NET 事件。

## <a name="net-events-with-uikit"></a>UIKit.NET 事件

Xamarin.iOS 公開 UIKit 控制項上的.NET 事件。 例如，標記的 UIButton 有 TouchUpInside 事件，它會使用下列程式碼所示，處理像平常一樣在.NET 中， C# lambda 運算式：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

您也可以實作，這與C#2.0 樣式匿名方法與下列類似：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

上述程式碼，會建構好`ViewDidLoad`UIViewController 的方法。 `aButton`變數參考的按鈕，您可以加入 iOS 設計工具中，或是使用程式碼。 下圖顯示 「 iOS 設計工具中已加入的按鈕：

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "在 iOS 設計工具中加入按鈕")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS 也支援連接您的程式碼就會發生與控制項互動的目標動作樣式。 若要建立的目標動作**Hello**按鈕之後，按兩下 iOS 設計工具中。 UIViewController 的程式碼後置檔案就會顯示與開發人員將需要選取要插入的連線方法的位置：

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "UIViewControllers 程式碼後置檔案")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

選取位置之後，新的方法建立並有線接至控制項。 在下列範例中，訊息會寫入至主控台按一下按鈕時：

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "訊息將寫入至主控台，按一下按鈕時")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

如需 iOS 目標動作模式的詳細資訊，請參閱目標動作區段[適用於 iOS 的核心應用程式能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)Apple iOS 開發人員文件庫中。

如需如何使用 Xamarin.iOS 中的 「 iOS 設計工具的詳細資訊，請參閱[iOS 設計工具概觀](~/ios/user-interface/designer/index.md)文件。

## <a name="events"></a>事件

如果您想要攔截從 ui 控制的事件，您會有一些選項： 從使用C#lambda 和委派函式，以使用低層級的 Objective C Api。

以下區段顯示方式，您會擷取 TouchDown 事件 按鈕，視您所需要的控制程度而定。

## <a name="c-style"></a>C#樣式

使用委派的語法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

如果您改為喜歡 lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果您想要有多個按鈕會共用相同的程式碼使用相同的處理常式：

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>監視多個事件的種類

C# UIControlEvent 旗標的事件都有一對一的對應，個別旗標。 當您想要有相同的程式碼處理兩個或多個事件，請使用`UIControl.AddTarget`方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 語法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果您需要使用 OBJECTIVE-C、 連結至特定物件執行個體和叫用特定的選取器等的低層級功能：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

請注意，如果您在繼承的基底類別中實作的執行個體方法，它必須是公用的方法。

## <a name="protocols"></a>通訊協定

通訊協定是 Objective C 語言功能，可提供方法宣告的清單。 它的功能類似的介面，在C#，在 通訊協定可以有選擇性方法的主要差異。 如果採用一種通訊協定類別未實作它們，不會呼叫選擇性方法。 此外，在 OBJECTIVE-C 中的單一類別可實作多個通訊協定，就如同C#類別可以實作多個介面。

Apple 會使用整個 iOS 的通訊協定來定義合約的類別，以採用，同時抽離實作的類別，從呼叫端，因此操作就像C#介面。 使用通訊協定在非委派案例 (例如使用`MKAnnotation`如下所示的範例)，並使用委派 （如本文件中，在 [委派] 區段中稍後所示）。

### <a name="protocols-with-xamarinios"></a>與 Xamarin.ios 的通訊協定

讓我們看看使用 Xamarin.iOS 中的 OBJECTIVE-C 通訊協定的範例。 我們將使用此範例中，`MKAnnotation`通訊協定，也就是組件的`MapKit`framework。 `MKAnnotation` 是一種通訊協定，可讓任何採用它提供註解可加入至對應的相關資訊的物件。 比方說，物件，實作`MKAnnotation`提供註解和與其相關聯的標題的位置。

如此一來，`MKAnnotation`通訊協定用來提供附註解的相關資料。 從所採用之物件中的資料建立本身的註釋的實際檢視`MKAnnotation`通訊協定。 比方說，當使用者點選註解 （如以下螢幕擷取畫面所示） 時所顯示的圖說文字的文字來自`Title`實作通訊協定的類別中的屬性：

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "當使用者點選在註釋的圖說文字的範例文字")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

下一節所述[通訊協定深入探討](#protocols-deep-dive)，Xamarin.iOS 將通訊協定繫結至抽象類別。 針對`MKAnnotation`通訊協定、 繫結C#類別命名為`MKAnnotation`來模擬通訊協定和它的名稱為的子類別`NSObject`，產品 CocoaTouch 根基底類別。 通訊協定需要 getter 和 setter 座標; 的實作不過，標題和副標題是選擇性的。 因此，在`MKAnnotation`類別，`Coordinate`屬性是*抽象*，需要實作並`Title`並`Subtitle`屬性會標示*虛擬*使其選擇性的如下所示：

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

任何類別可以提供註解資料，藉由直接衍生自`MKAnnotation`，只要至少`Coordinate`屬性實作。 例如，以下是範例類別，建構函式會採用座標，並傳回標題字串：

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

透過繫結至通訊協定，任何類別子類別化`MKAnnotation`可以提供將用於此對應建立註解的檢視時的相關資料。 若要新增至對應的註解，只要呼叫`AddAnnotation`方法的`MKMapView`執行個體，如下列程式碼所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

對應變數是的執行個體`MKMapView`，這是代表對應本身的類別。 `MKMapView`會使用`Coordinate`資料衍生自`SampleMapAnnotation`放置在地圖上的註釋檢視的執行個體。

`MKAnnotation`通訊協定提供一組已知的跨實作它的任何物件的功能，而不需要取用者 （在此案例對應表） 不需要知道有關實作詳細資料。 如此一來，可簡化將各種可能的註解加入至對應。

### <a name="protocols-deep-dive"></a>通訊協定深入探討

因為C#介面不支援選擇性方法，Xamarin.iOS 將通訊協定對應至抽象類別。 因此，採用 Objective C 中的通訊協定被透過在 Xamarin.iOS 中衍生自抽象類別，用於繫結至通訊協定，並實作所需的方法。 這些方法會公開為類別中的抽象方法。 選擇性方法，從 通訊協定將會繫結的虛擬方法C#類別。

例如，以下是部分`UITableViewDataSource`通訊協定繫結中的 Xamarin.iOS 做：

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

請注意，此類別是抽象。 Xamarin.iOS 會將類別變成抽象支援通訊協定中的選擇性/所需要的方法。
不過，與 OBJECTIVE-C 通訊協定，不同 (或C#介面)，C#類別不支援多重繼承。 這會影響設計的C#程式碼會使用通訊協定，通常會導致巢狀類別。 更多關於此問題涵蓋稍後在本文件中，在 [委派] 區段中。

 `GetCell(…)` 是抽象的方法，繫結到 OBJECTIVE-C *selector*， `tableView:cellForRowAtIndexPath:`，這是所需的方法的`UITableViewDataSource`通訊協定。 選取器是方法名稱的 Objective C 詞彙。 若要強制執行所需的方法，Xamarin.iOS 將其宣告為抽象。 另一個方法， `NumberOfSections(…)`，繫結至`numberOfSectionsInTableview:`。 這個方法是在通訊協定中，選擇性的因此 Xamarin.iOS 將其宣告為虛擬，進行選擇性覆寫中的C#。

Xamarin.iOS 會負責為您的所有 iOS 繫結。 不過，如果您需要以手動方式從 OBJECTIVE-C 繫結通訊協定，您可以這樣來裝飾類別`ExportAttribute`。 這是 Xamarin.iOS 本身所使用的相同方法。

如需如何在 Xamarin.iOS 中繫結 Objective C 類型的詳細資訊，請參閱文章[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)。

我們還不透過通訊協定，不過。 它們也習慣在 iOS 中做為基礎的 Objective C 的委派，也就是下一節的主題。

## <a name="delegates"></a>委派

iOS 使用 OBJECTIVE-C 委派實作的委派模式，在其中一個物件會傳遞工作到另一個。 執行工作的物件是第一個物件的委派。 物件會告訴它要藉由傳送該訊息之後發生的某些項目執行作業的委派。 在 OBJECTIVE-C 中傳送如下的訊息功能上相當於呼叫方法， C#。 委派會實作這些呼叫中，回應中的方法，因此提供給應用程式的功能。

委派可讓您擴充類別的行為，而不需要建立子類別。 當呼叫傳回，一個類別到另一個重要的動作發生之後，在 iOS 中的應用程式通常會使用委派。 比方說，`MKMapView`類別呼叫其委派，當使用者點選在地圖上的註解讓委派類別的作者有機會在應用程式內回應。 您可以使用這種類型的委派使用方式，稍後在本文中，在範例中使用的範例使用 Xamarin.iOS 的委派。

此時，您可能想知道類別如何決定要在其委派上呼叫的方法。 這是您用來使用通訊協定的另一個位置。 通常，可用來委派的方法是來自他們採用的通訊協定。

### <a name="how-protocols-are-used-with-delegates"></a>如何使用通訊協定與委派

我們會看到稍早如何使用通訊協定來支援新增至對應的註釋。
通訊協定也會用來提供一組已知的類別以呼叫特定事件發生時，像是在使用者點選在地圖上註解後，或在資料表中選取該儲存格後的方法。 實作這些方法的類別稱為類別呼叫它們的委派。

支援委派的類別公開的委派屬性，指派給實作的委派類別的執行這個動作。 您實作委派的方法將取決於特定的委派會採用的通訊協定。 針對`UITableView`實作方法，`UITableViewDelegate`通訊協定，如`UIAccelerometer`方法，您會實作`UIAccelerometerDelegate`、 等等的整個 iOS，您會想要的任何其他類別公開的委派時，才。

`MKMapView`我們在先前的範例所見的類別也有一個稱為委派，它會呼叫它的屬性發生各種事件之後。 委派`MKMapView`別的`MKMapViewDelegate`。
您將使用此不久中之後選取它，回應註解的範例，但第一個讓我們討論強式與弱委派之間的差異。

### <a name="strong-delegates-vs-weak-delegates"></a>強式委派與弱式的委派

我們已經探討了截至目前為止的委派是強式的委派，表示強型別。 Xamarin.iOS 繫結會隨附在 iOS 中的每個委派通訊協定的強型別類別。 不過，iOS 也有弱委派的概念。 而不是子類別化特定的委派繫結到 OBJECTIVE-C 通訊協定的類別，iOS 也可讓您選擇要自己繫結通訊協定方法讓衍生自 NSObject，修飾您的方法，以 exportattribute 標記，任何類別中，然後提供適當的選取器。
當您採用這個方法時，您會將您類別的執行個體指派給 WeakDelegate 屬性而不是委派屬性中。 弱式的委派會提供您彈性地採取不同的繼承階層中向下委派類別。 讓我們看看使用強式與弱委派的 Xamarin.iOS 範例。

### <a name="example-using-a-delegate-with-xamarinios"></a>與 Xamarin.iOS 使用委派的範例

若要執行的程式碼以回應使用者點選在我們的範例註解，我們可以進行子分類`MKMapViewDelegate`並指派至執行個體`MKMapView`的`Delegate`屬性。 `MKMapViewDelegate`通訊協定包含選擇性的方法。
因此，所有方法都是虛擬的繫結至在 Xamarin.iOS 中此通訊協定`MKMapViewDelegate`類別。 當使用者選取註釋，`MKMapView`執行個體將會傳送`mapView:didSelectAnnotationView:`訊息給它的委派。 若要處理這在 Xamarin.iOS 中，我們需要覆寫`DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)`方法在 MKMapViewDelegate 子類別，像這樣：

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

如上所示的 SampleMapDelegate 類別會實作為巢狀類別中包含的控制器`MKMapView`執行個體。 在 OBJECTIVE-C，您通常會看到採用直接在類別內的多個通訊協定的控制站。 不過，因為通訊協定繫結至在 Xamarin.iOS 中的類別，實作強型別的委派的類別是通常會包含做為巢狀類別。

使用委派類別實作之後，您只需要具現化控制器中委派的執行個體，並將它指派給`MKMapView`的`Delegate`屬性如下所示：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

若要使用弱式的委派來完成相同的動作，您需要繫結方法自行在任何類別都衍生自`NSObject`並將它指派給`WeakDelegate`屬性`MKMapView`。 由於`UIViewController`類別最終都會衍生自`NSObject`（例如每個 OBJECTIVE-C 類別中產品 CocoaTouch），我們可以直接實作繫結至方法`mapView:didSelectAnnotationView:`控制器中直接指派至控制器和`MKMapView`的`WeakDelegate`，不需要額外的巢狀類別。 下列程式碼會示範這種方法：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

當執行此程式碼時，應用程式的行為就像執行的強型別的委派版本時。 此程式碼的好處是弱委派不需要建立我們使用強型別的的委派時所建立的額外類別。 不過，這剩型別安全。 如果您選擇了傳遞給選取器中會犯錯`ExportAttribute`，您不了解執行階段之前。

### <a name="events-and-delegates"></a>事件與委派

委派用於在 iOS，類似於.NET 使用事件的方法中的回呼。 若要讓 iOS Api 以及他們使用 OBJECTIVE-C 委派的方式看起來更.NET，Xamarin.iOS 會公開.NET 事件，在許多地方，在 iOS 中使用委派的位置。

例如，先前的實作其中`MKMapViewDelegate`回應選取的註解可能也實作在 Xamarin.iOS 中使用.NET 事件。 在此情況下，會定義事件在`MKMapView`並呼叫`DidSelectAnnotationView`。 您必須`EventArgs`子類別的型別`MKMapViewAnnotationEventsArgs`。 `View`屬性`MKMapViewAnnotationEventsArgs`可讓您的註釋檢視的參考，從使用相同的實作可以繼續這您就必須更早版本，以詳述這裡：

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>總結

本文涵蓋如何在 Xamarin.iOS 中使用事件、 通訊協定與委派。 我們發現 Xamarin.iOS 如何公開一般.NET 樣式控制項的事件。
接下來我們已了解 OBJECTIVE-C 通訊協定，包括它們的不同方式C#介面和 Xamarin.iOS 如何使用它們。 最後，我們會檢查 Xamarin.iOS 觀點的 Objective C 委派。 我們看到如何 Xamarin.iOS 同時支援強式和弱型別的的委派，以及如何繫結至委派方法的.NET 事件。

## <a name="related-links"></a>相關連結

- [通訊協定、 委派和事件 （範例）](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [繫結 OBJECTIVE-C 型別](~/ios/platform/binding-objective-c/index.md)
- [Objective C 程式設計語言](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [設計在 Xcode 4 中的使用者介面](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [適用於 iOS 的核心應用程式能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
