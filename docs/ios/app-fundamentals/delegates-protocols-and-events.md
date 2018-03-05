---
title: "事件、 通訊協定與委派"
description: "這篇文章會提供用來接收回撥，並填入資料的使用者介面控制項的索引鍵的 iOS 技術。 這些技術是事件、 通訊協定，以及委派。 這篇文章解釋每一種是，以及每個從 C# 的使用方式。 它會示範如何 Xamarin.iOS 使用 iOS 控制項來公開熟悉.NET 事件，以及如何 Xamarin.iOS Objective C 的概念，例如通訊協定與委派提供支援 （OBJECTIVE-C 委派不應該與 C# 委派混淆）。 本文也會提供顯示通訊協定的使用方式 – 同時做為基礎的 OBJECTIVE-C 委派和非委派案例中的範例。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5df7c2bbc7be1089795c94b6f639bd4556b49366
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="events-protocols-and-delegates"></a>事件、 通訊協定與委派

_這篇文章會提供用來接收回撥，並填入資料的使用者介面控制項的索引鍵的 iOS 技術。這些技術是事件、 通訊協定，以及委派。這篇文章解釋每一種是，以及每個從 C# 的使用方式。它會示範如何 Xamarin.iOS 使用 iOS 控制項來公開熟悉.NET 事件，以及如何 Xamarin.iOS Objective C 的概念，例如通訊協定與委派提供支援 （OBJECTIVE-C 委派不應該與 C# 委派混淆）。本文也會提供顯示通訊協定的使用方式 – 同時做為基礎的 OBJECTIVE-C 委派和非委派案例中的範例。_

Xamarin.iOS 使用控制項來公開大部分的使用者互動的事件。
傳統的.NET 應用程式一樣，Xamarin.iOS 應用程式會使用這些事件中的相同方式進行。 比方說，Xamarin.iOS UIButton 類別已呼叫 TouchUpInside 的事件，並取用此事件，就如同此類別與事件是在.NET 應用程式。

此.NET 方法時，除了 Xamarin.iOS 公開另一個模型，可用於更複雜的互動和資料繫結。 這個方法會使用 Apple 呼叫的項目委派和通訊協定。 委派是在 C# 中，委派的概念類似，但而不是定義和呼叫的單一方法，Objective C 中的委派是一種通訊協定符合整個類別。 通訊協定是類似於 C# 中的介面，不同之處在於它的方法可以是選擇性。 比方說，若要填入資料 UITableView，您會建立委派類別可實作 UITableView 會呼叫以填入本身 UITableViewDataSource 通訊協定中定義的方法。

在此文章中，您將了解所有這些主題，提供您穩固的基礎處理回呼 Xamarin.iOS，案例包括：

-  **事件**– UIKit 控制項使用的.NET 事件。
-  **通訊協定**– 了解哪些通訊協定和使用方式，並建立範例提供對應的註解的資料。
-  **委派**– 學習需 OBJECTIVE-C 委派延伸來處理使用者互動，其中包含註解，對應的範例，然後學習強式和弱式委派，以及何時使用每一種之間的差異。


為了說明通訊協定與委派，我們會建置一個簡單的對應應用程式，將註解加入至地圖，如下所示：

 [ ![](delegates-protocols-and-events-images/01-map.png "將註解加入至對應的簡單的對應應用程式的範例")](delegates-protocols-and-events-images/01-map.png) [ ![ ](delegates-protocols-and-events-images/04-annotation-with-callout.png "範例註解加入至地圖")](delegates-protocols-and-events-images/04-annotation-with-callout.png)

之前解決這個應用程式，讓我們開始吧藉由查看下 UIKit.NET 事件。

 <a name=".NET_Events_with_UIKit" />


## <a name="net-events-with-uikit"></a>UIKit.NET 事件

Xamarin.iOS 公開 UIKit 控制項的.NET 事件。 例如，UIButton 具有 TouchUpInside 事件，它處理像平常一樣在.NET 中，使用 C# lambda 運算式的下列程式碼所示：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

您也可以實作這與 C# 2.0 樣式匿名方法與下列類似：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

上述程式碼固定 UIViewContoller ViewDidLoad 方法中。 AButton 變數參考一個按鈕，您可以加入 iOS 設計工具中，或以程式碼。 下圖顯示此按鈕將它加入 iOS 設計工具中，取自範例本文章中：

 [ ![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "在 iOS 設計工具中加入按鈕")](delegates-protocols-and-events-images/02-interface-builder-outlet.png)

Xamarin.iOS 也支援連接您的程式碼就會發生與控制項互動的目標動作樣式。 若要建立 Hello 按鈕目標動作，按兩下 iOS 設計工具中。 將顯示 UIViewController 的程式碼後置檔案，並將要求開發人員選取要插入的連線方法的位置：

 [ ![](delegates-protocols-and-events-images/03-interface-builder-action.png "UIViewControllers 程式碼後置檔案")](delegates-protocols-and-events-images/03-interface-builder-action.png)

系統會選取位置之後，新的方法建立並有線向上至控制項。 在下列範例中，訊息會寫入至主控台按一下按鈕時：

 [ ![](delegates-protocols-and-events-images/05-interface-builder-action.png "訊息將寫入至主控台，按一下按鈕時")](delegates-protocols-and-events-images/05-interface-builder-action.png)

如需 iOS 目標動作模式的詳細資訊，請參閱目標動作的 「[核心適用於 iOS 的應用程式能力](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)「 Apple iOS 開發人員文件庫中。

如需如何使用 Xamarin.iOS iOS 設計工具的詳細資訊，請參閱 「 [iOS 設計工具概觀](~/ios/user-interface/designer/index.md)」 文件。

 <a name="Events" />


## <a name="events"></a>事件

如果您想要攔截 ui 控制的事件，您有許多選項： 從使用 C# lambda 與使用低層級的 Objective C 應用程式開發介面的委派函數。

下節將說明如何，您會擷取 TouchDown 事件上的按鈕，視您所需要的控制程度而定。

 <a name="C#_Style" />


## <a name="c-style"></a>C# 樣式

使用委派的語法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

如果您改為讓 lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果您想要有多個按鈕會使用相同的處理常式來共用相同的程式碼：

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

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>監視多個事件類型

C# 事件 UIControlEvent 旗標都有一對一的對應，以個別旗標。 當您想要有相同的程式碼片段處理兩個或多個事件，請使用`UIControl.AddTarget`方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 語法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果您要使用的 Objective C，連結至特定物件執行個體和叫用特定的選取器等低層級功能：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

請注意，如果您繼承的基底類別中實作的執行個體方法，它必須是公用的方法。

 <a name="Protocols" />


## <a name="protocols"></a>通訊協定

通訊協定是提供的方法宣告清單 Objective C 語言功能。 它可以做類似的用途，在 C# 中的主要差異在於介面的通訊協定可以有選擇性的方法。 選擇性方法不會呼叫如果採用一種通訊協定的類別未實作它們。 此外，Objective C 中的單一類別可以實作多個通訊協定，如同 C# 類別可以實作多個介面。

Apple 會使用整個 iOS 的通訊協定來定義類別，以採用抽離從呼叫端，因此操作就像 C# 介面實作的類別時的合約。 通訊協定會用非委派案例中 (例如與`MKAnnotation`顯示下一個範例)，和使用委派 （如稍後所呈現本文件中，在 [委派] 區段中）。

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>通訊協定與 Xamarin.ios

讓我們看看使用 Xamarin.iOS Objective C 的通訊協定的範例。 我們將使用此範例中，`MKAnnotation`通訊協定，也就是組件的`MapKit`架構。 `MKAnnotation` 是一種通訊協定，可讓任何採用提供註解可以加入至對應的相關資訊的物件。 例如，實作`MKAnnotation`提供註解和與其相關聯的標題的位置。

如此一來，`MKAnnotation`通訊協定用來提供附註解的相關資料。 會採用物件中的資料建置本身的註釋的實際檢視`MKAnnotation`通訊協定。 例如，當使用者點選註解 （如以下螢幕擷取畫面所示） 時所顯示的圖說文字的文字來自`Title`實作通訊協定的類別中的屬性：

 [ ![](delegates-protocols-and-events-images/04-annotation-with-callout.png "當使用者點選註解圖說文字的範例文字")](delegates-protocols-and-events-images/04-annotation-with-callout.png)

下一節，深入了解通訊協定中, 所述 Xamarin.iOS 將通訊協定繫結到抽象類別。 如`MKAnnotation`通訊協定，名為繫結的 C# 類別`MKAnnotation`模仿通訊協定，和它的名稱為的子類別`NSObject`，CocoaTouch 根基底類別。 通訊協定需要 getter 和 setter 必須實作的座標。不過，標題和副標題是選擇性的。 因此，在`MKAnnotation`類別`Coordinate`屬性是*抽象*，便會實作要求和`Title`和`Subtitle`屬性會標示*虛擬*使其選擇性的如下所示：

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

任何類別可以提供註解資料，只衍生自`MKAnnotation`，只要至少`Coordinate`屬性實作。 例如，以下是在建構函式會採用座標，並傳回標題字串的範例類別：

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

透過繫結至通訊協定，任何類別的子類別`MKAnnotation`可以提供建立註解的檢視時將使用此對應的相關資料。 若要新增至對應的註解，只要呼叫`AddAnnotation`方法`MKMapView`執行個體，如下列程式碼所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

此處的對應變數是的執行個體`MKMapView`，這是代表對應本身的類別。 `MKMapView`將使用`Coordinate`資料衍生自`SampleMapAnnotation`放置在地圖上的註釋檢視的執行個體。

`MKAnnotation`通訊協定提供一組已知的功能整個實作它的任何物件，取用者 (在此情況下 map) 不需要了解實作詳細資料。 這可簡化將各種可能的註解加入至地圖。

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>通訊協定深入探討

C# 介面不支援選擇性方法，因為 Xamarin.iOS 會對應至抽象類別的通訊協定。 因此，採用 Objective C 中的通訊協定被透過在 Xamarin.iOS 衍生自抽象類別繫結至通訊協定，並實作需要的方法。 這些方法會公開為類別中的抽象方法。 選擇性方法，從通訊協定會繫結的 C# 類別虛擬方法。

例如，以下是部份`UITableViewDataSource`通訊協定繫結中的 Xamarin.iOS 做：

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

請注意此類別是抽象的。 Xamarin.iOS 會將類別變成抽象支援通訊協定中的選擇性/所需的方法。
不過，不同於 Objective C 的通訊協定 （或 C# 介面），C# 類別不支援多重繼承。 這會影響設計的 C# 程式碼，會使用通訊協定，通常會導致巢狀類別。 更多關於此問題涵蓋本文件稍後，在 [委派] 區段中。

 `GetCell(…)` 是抽象方法，繫結至 Objective C*選取器*， `tableView:cellForRowAtIndexPath:`，這是必要的方法`UITableViewDataSource`通訊協定。 選取器是方法名稱的 OBJECTIVE-C 詞彙。 若要強制所需的方法，Xamarin.iOS 宣告為抽象。 另一個方法， `NumberOfSections(…)`，繫結至`numberOfSectionsInTableview:`。 這是方法選擇性通訊協定，因此 Xamarin.iOS 會將其宣告為 virtual，讓選擇性覆寫在 C#。

Xamarin.iOS 會負責為您的所有 iOS 繫結。 不過，如果您需要以手動方式從 OBJECTIVE-C 繫結通訊協定，您可以藉由裝飾類別`ExportAttribute`。 這是使用 Xamarin.iOS 本身的相同方法。

如需如何將 Objective C 類型 Xamarin.iOS 中繫結的詳細資訊，請參閱文章[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)。

我們還不透過通訊協定，不過。 這些是也用於在 iOS 中做為基礎 Objective C 的委派，也就是下一節的主題。

 <a name="Delegates" />


## <a name="delegates"></a>委派

iOS 會以實作委派模式，在其中一個物件會傳遞工作至另一個使用 Objective C 的委派。 執行工作的物件是第一個物件的委派。 物件會告訴它要執行的作業所傳送訊息之後某些項目，就可能發生的委派。 Objective C 中傳送類似這樣的訊息是功能上等同於 C# 中呼叫的方法。 委派實作的方法，以回應這些呼叫，因此提供給應用程式的功能。

委派可讓您擴充類別的行為，而不需要建立子類別。 一個類別進行呼叫時傳回至另一個重要的動作發生之後，在 iOS 中的應用程式通常會使用委派。 例如，`MKMapView`類別呼叫它的委派，當使用者點選註解在對應上，讓委派類別的作者有機會在應用程式內回應。 您可以透過這種類型的委派本文稍後，在範例中使用的使用方式範例 Xamarin.iOS 的委派。

此時，您可能想知道類別如何決定其委派上呼叫的方法。 這是另一個位置，您可以使用通訊協定。 通常，適用於委派方法來自於它們所採用的通訊協定。

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>通訊協定與委派的使用方式

我們了解如何先前使用通訊協定來支援加入至對應的註釋。
通訊協定也可用來提供一組已知的特定事件發生時，像是在使用者點選在地圖上註解後，或選取表格中的資料格之後要呼叫的類別的方法。 實作這些方法的類別是所謂的類別呼叫它們的委派。

支援委派的類別公開的委派屬性，指派給實作委派類別的執行這個動作。 您實作委派方法將取決於特定的委派會採用的通訊協定。 如`UITableView`方法，實作`UITableViewDelegate`通訊協定，如`UIAccelerometer`方法，您也會實作`UIAccelerometerDelegate`等等的整個 iOS，您會想要的任何其他類別公開的委派。

`MKMapView`我們在先前的範例，了解的類別也具有稱為委派，它會呼叫它的屬性就會發生各種事件之後。 委派`MKMapView`的型別`MKMapViewDelegate`。
您將使用這很快中回應之後已選取，註解的範例，但第一個讓我們來討論強式和弱式委派之間的差異。

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>強式委派 vs。弱式的委派

我們已經討論過為止委派是強式委派，這表示強型別。 Xamarin.iOS 繫結會隨附在 iOS 中每個委派通訊協定的強型別類別。 但是，iOS 也具有弱式委派的概念。 而不是子類別化特定的委派繫結至 OBJECTIVE-C 通訊協定的類別，iOS 也可讓您選擇自己繫結通訊協定方法中的任何類別要衍生自 NSObject，而將您的方法，以 exportattribute 標記的型，然後提供適當的選取器。
這種方法時，您會將您類別的執行個體指派給 WeakDelegate 屬性而不是委派屬性中。 弱式委派，可讓您彈性地使用不同的繼承階層中向您類別的委派。 讓我們看看 Xamarin.iOS 範例，其使用強式和弱式委派。

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>使用委派與 Xamarin.iOS 範例

若要執行程式碼以回應使用者點選在本例中的註解，我們可以子類別化為`MKMapViewDelegate`並指派至執行個體`MKMapView`的`Delegate`屬性。 `MKMapViewDelegate`通訊協定僅包含選擇性方法。
因此，所有方法都是虛擬的繫結至這個通訊協定中 Xamarin.iOS`MKMapViewDelegate`類別。 當使用者選取註解，`MKMapView`執行個體將會傳送`mapView:didSelectAnnotationView:`訊息給它的委派。 若要處理這 Xamarin.iOS 中，我們需要覆寫`DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)`像這樣的 MKMapViewDelegate 子類別中的方法：

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

如上所示的 SampleMapDelegate 類別會實作為巢狀類別中包含的控制器`MKMapView`執行個體。 Objective C，在您通常會看到採用直接在類別內的多個通訊協定的控制站。 不過，因為通訊協定繫結至 Xamarin.iOS 中的類別，類別可實作強類型的委派則通常是以巢狀類別。

與就地委派類別實作，您只需要具現化控制器中委派的執行個體，並將它指派給`MKMapView`的`Delegate`屬性如下所示：

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

若要達成相同目的使用弱式委派，您需要繫結方法自行在任何衍生自的類別中`NSObject`並將它指派給`WeakDelegate`屬性`MKMapView`。 因為`UIViewController`類別最終都會衍生自`NSObject`（例如每個 OBJECTIVE-C 類別 CocoaTouch 中），我們只是實作方法，可以繫結至`mapView:didSelectAnnotationView:`控制器中直接指派至控制器和`MKMapView`的`WeakDelegate`，避免不需要額外的巢狀類別。 下列程式碼會示範這種方法：

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

當執行此程式碼時，應用程式的行為完全一樣時執行的強型別的的委派版本。 這個程式碼的優點是不安全的委派不需要使用強類型的委派時所建立的額外類別建立。 不過，這樣的代價型別安全。 如果您選擇了傳來的選取器中容易出錯`ExportAttribute`，您可能不到執行階段才找出。

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>事件和委派

委派可以用在.NET 中使用事件的方式類似於 iOS 中的回呼。 若要讓 iOS Api 以及使用 Objective C 的委派的方式起來更.NET、 Xamarin.iOS 公開.NET 事件在許多地方，委派可以用在 iOS 中。

例如，較早實作其中`MKMapViewDelegate`對選取的註解可能也會實作 Xamarin.iOS 中使用.NET 事件。 在此情況下，會定義事件在`MKMapView`和呼叫`DidSelectAnnotationView`。 就會`EventArgs`子類別的型別`MKMapViewAnnotationEventsArgs`。 `View`屬性`MKMapViewAnnotationEventsArgs`會提供您的註釋檢視的參考，從具有相同的實作無法繼續的您就必須更早版本，以圖解這裡：

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

 <a name="Summary" />


## <a name="summary"></a>總結

本文涵蓋如何使用事件、 通訊協定，以及委派 Xamarin.iOS 中。 我們了解如何 Xamarin.iOS 公開控制項的標準.NET 樣式事件。
接下來我們了解 OBJECTIVE-C 通訊協定，包括的方式不同於 C# 介面以及 Xamarin.iOS 如何使用它們。 最後，我們會檢查以 Xamarin.iOS 觀點 OBJECTIVE-C 委派。 我們了解如何 Xamarin.iOS 同時支援強和弱委派型別，以及如何將.NET 事件委派的方法繫結。


## <a name="related-links"></a>相關連結

- [通訊協定、 委派和事件 （範例）](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [繫結 Objective C 的型別](~/ios/platform/binding-objective-c/index.md)
- [Objective C 程式設計語言](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [在 Xcode 4 中設計使用者介面](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [適用於 iOS 的核心應用程式能力](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
