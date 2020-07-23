---
title: Xamarin 中的事件、通訊協定和委派
description: 本檔說明如何在 Xamarin 中使用事件、通訊協定和委派。 這些基本概念在 Xamarin iOS 開發中很普遍。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2017
ms.openlocfilehash: 1a7d7ec017bb226efb05014dc7ac80160aeaae48
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938331"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Xamarin 中的事件、通訊協定和委派

Xamarin 會使用控制項來公開大部分使用者互動的事件。
就像傳統 .NET 應用程式一樣，Xamarin 應用程式使用這些事件的方式非常類似。 例如，UIButton 類別有一個稱為 TouchUpInside 的事件，並取用此事件，就像這個類別和事件是在 .NET 應用程式中一樣。

除了這個 .NET 方法，Xamarin 會公開另一個可用於更複雜互動和資料系結的模型。 這個方法會使用 Apple 呼叫委派和通訊協定的內容。 委派在概念上類似于 c # 中的委派，但不會定義和呼叫單一方法，但在目標-C 中的委派是符合通訊協定的整個類別。 通訊協定與 c # 中的介面相似，不同之處在于其方法可以是選擇性的。 比方說，若要在 UITableView 中填入資料，您可以建立委派類別來執行 UITableViewDataSource 通訊協定中所定義的方法，而 UITableView 會呼叫它來填入本身。

在本文中，您將瞭解所有這些主題，為您提供堅實的基礎來處理 Xamarin 中的回呼案例，包括：

- **事件**–搭配使用 .net 事件與 UIKit 控制項。
- **通訊協定**–學習哪些通訊協定，以及如何使用它們，以及如何建立可提供地圖注釋資料的範例。
- **委派**–藉由擴充 map 範例來處理包含注釋的使用者互動，以瞭解目標-C 委派，然後學習強式和弱式委派之間的差異，以及使用每個的時機。

為了說明通訊協定和委派，我們將建立一個簡單的地圖應用程式，將批註新增至地圖，如下所示：

[ ![ 簡單對應應用程式的範例，可將批註](delegates-protocols-and-events-images/01-map-sml.png)](delegates-protocols-and-events-images/01-map.png#lightbox)新增至地圖中 
 [ ![ 加入地圖的範例批註](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

在處理此應用程式之前，讓我們先來看一下 UIKit 底下的 .NET 事件。

## <a name="net-events-with-uikit"></a>使用 UIKit 的 .NET 事件

Xamarin 會在 UIKit 控制項上公開 .NET 事件。 例如，UIButton 有一個 TouchUpInside 事件，您可以像平常一樣在 .NET 中處理，如下列使用 c # lambda 運算式的程式碼所示：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

您也可以使用 c # 2.0 樣式的匿名方法來執行這項操作，如下所示：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

上述程式碼會在 UIViewController 的方法中進行有線 `ViewDidLoad` 。 `aButton`變數會參考一個按鈕，您可以在 IOS 設計工具中或透過程式碼來新增。 下圖顯示已在 iOS 設計工具中新增的按鈕：

[![在 iOS 設計工具中新增的按鈕](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png)](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

在將您的程式碼連接到控制項發生的互動時，也支援目標動作樣式。 若要建立**Hello**按鈕的目標動作，請在 iOS 設計工具中按兩下。 將會顯示 UIViewController 的程式碼後置檔案，並要求開發人員選取要插入連接方法的位置：

[![UIViewControllers 程式碼後置檔案](delegates-protocols-and-events-images/03-interface-builder-action-sml.png)](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

選取位置之後，會建立新的方法，並將其連接到控制項。 在下列範例中，當您按一下按鈕時，會將訊息寫入主控台：

[![按一下按鈕時，就會將訊息寫入主控台](delegates-protocols-and-events-images/05-interface-builder-action-sml.png)](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

如需 iOS 目標動作模式的詳細資訊，請參閱 Apple iOS Developer Library 中的[適用于 ios 的核心應用程式職稱](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)的目標動作一節。

如需如何搭配使用 iOS 設計工具與 Xamarin 的詳細資訊，請參閱[IOS 設計工具總覽](~/ios/user-interface/designer/index.md)檔。

## <a name="events"></a>事件

如果您想要攔截 UIControl 的事件，您有一系列選項：從使用 c # lambda，並將函式委派給使用低層級的目標-C Api。

下一節將說明如何根據您所需的控制程度，在按鈕上捕捉 TouchDown 事件。

## <a name="c-style"></a>C # 樣式

使用委派語法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

如果您想要改用 lambda：

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果您想要讓多個按鈕使用相同的處理常式來共用相同的程式碼：

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

## <a name="monitoring-more-than-one-kind-of-event"></a>監視一種以上的事件

UIControlEvent 旗標的 c # 事件與個別旗標具有一對一的對應。 當您想要讓同一段程式碼處理兩個以上的事件時，請使用 `UIControl.AddTarget` 方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 語法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果您需要使用目標-C 的低層級功能，例如連結至特定物件實例，並叫用特定的選取器：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

請注意，如果您在繼承的基類中執行實例方法，它必須是公用方法。

## <a name="protocols"></a>通訊協定

通訊協定是一種以目標為 C 的語言功能，可提供方法宣告的清單。 它的用途類似于 c # 中的介面，主要差異在於通訊協定可以有選擇性的方法。 如果採用通訊協定的類別未執行，則不會呼叫選擇性方法。 此外，在目標中的單一類別也可以實作為 c # 類別來執行多個通訊協定。

Apple 會在 iOS 中使用通訊協定來定義要採用的類別合約，同時從呼叫端抽象化衍生的類別，因此操作方式就像 c # 介面一樣。 在非委派案例（例如 `MKAnnotation` 下圖所示的範例）和委派（如本檔稍後的委派一節中所提供）中，都會使用通訊協定。

### <a name="protocols-with-xamarinios"></a>使用 Xamarin 的通訊協定

讓我們來看一個使用來自 Xamarin 的目標-C 通訊協定的範例。 在此範例中，我們將使用 `MKAnnotation` 通訊協定，這是架構的一部分 `MapKit` 。 `MKAnnotation`是一種通訊協定，可讓任何採用它的物件提供可新增至地圖的注釋相關資訊。 例如，執行的物件 `MKAnnotation` 會提供批註的位置和與其相關聯的標題。

如此一 `MKAnnotation` 來，就會使用通訊協定來提供注釋隨附的相關資料。 批註本身的實際觀點是從採用通訊協定的物件資料建立而來 `MKAnnotation` 。 例如，當使用者按下注釋時出現的注標文字（如下列螢幕擷取畫面所示），來自于 `Title` 執行通訊協定的類別中的屬性：

 [![使用者點擊注釋時的注標範例文字](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

如下一節所述，「[通訊協定深入探討](#protocols-deep-dive)」-「Xamarin」會將通訊協定系結至抽象類別。 針對 `MKAnnotation` 通訊協定，會將系結的 c # 類別命名為 `MKAnnotation` ，以模擬通訊協定的名稱，它是 `NSObject` CocoaTouch 之根基類的子類別。 通訊協定需要針對座標實作為 getter 和 setter;不過，標題和副標題是選擇性的。 因此，在類別中， `MKAnnotation` `Coordinate` 屬性是*抽象*的，需要實作為，而且 `Title` 和 `Subtitle` 屬性會標示為*虛擬*，使其成為選擇性，如下所示：

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

任何類別都可以藉由直接衍生自來提供注釋資料 `MKAnnotation` ，前提是至少 `Coordinate` 會執行屬性。 例如，下列範例類別會接受在此函式中的座標，並傳回標題的字串：

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

透過它所系結的通訊協定，子類別的任何類別都 `MKAnnotation` 可以提供相關資料，當它建立批註的視圖時，將會使用該對應。 若要將批註加入至對應，只要呼叫 `AddAnnotation` 實例的方法即可 `MKMapView` ，如下列程式碼所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

這裡的 map 變數是的實例 `MKMapView` ，它是代表地圖本身的類別。 `MKMapView`會使用 `Coordinate` 衍生自實例的資料 `SampleMapAnnotation` ，將注釋視圖放在地圖上。

`MKAnnotation`通訊協定會在任何執行的物件之間提供一組已知的功能，而不需要取用者（在此案例中為對應）需要知道如何執行的詳細資料。 這可簡化將各種可能的注釋新增至地圖的工作。

### <a name="protocols-deep-dive"></a>通訊協定深入探討

因為 c # 介面不支援選擇性方法，所以 Xamarin 會將通訊協定對應至抽象類別。 因此，在以目標-C 來採用通訊協定的方式，是從系結至通訊協定並執行必要方法的抽象類別衍生而來。 這些方法將會公開為類別中的抽象方法。 來自通訊協定的選擇性方法會系結至 c # 類別的虛擬方法。

例如，以下是在 Xamarin 中系結之 `UITableViewDataSource` 通訊協定的一部分：

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

請注意，類別是抽象的。 在 iOS 中，會將類別設為抽象，以支援通訊協定中的選擇性/必要方法。
不過，不同于目標 C 通訊協定（或 c # 介面），c # 類別不支援多重繼承。 這會影響使用通訊協定之 c # 程式碼的設計，而且通常會導致嵌套的類別。 如需此問題的詳細資訊，請在本文稍後的委派一節中討論。

 `GetCell(…)`是一種抽象方法，系結至目標-C*選取器*， `tableView:cellForRowAtIndexPath:` 這是通訊協定的必要方法 `UITableViewDataSource` 。 選取器是方法名稱的目標-C 詞彙。 為了在必要時強制執行方法，Xamarin 會將它宣告為抽象。 另一個方法是系結 `NumberOfSections(…)` 至 `numberOfSectionsInTableview:` 。 這個方法在通訊協定中是選擇性的，因此 Xamarin 會將它宣告為虛擬，讓它在 c # 中是選擇性覆寫。

Xamarin 會為您處理所有 iOS 系結。 不過，如果您需要以手動方式系結通訊協定，您可以使用裝飾類別來執行此動作 `ExportAttribute` 。 這與 Xamarin 本身所使用的方法相同。

如需如何在 Xamarin 中系結目標-C 類型的詳細資訊，請參閱系結[目標-c 類型一](~/ios/platform/binding-objective-c/index.md)文。

不過，我們還不是使用通訊協定。 它們也會在 iOS 中用來作為目標-C 委派的基礎，也就是下一節的主題。

## <a name="delegates"></a>委派

iOS 會使用目標-C 委派來執行委派模式，其中一個物件會將工作傳遞至另一個。 執行工作的物件是第一個物件的委派。 物件會告知其委派在特定事件發生後傳送訊息來執行工作。 在目標-C 中傳送類似 this 的訊息，在功能上相當於呼叫 c # 中的方法。 委派會執行方法以回應這些呼叫，因此會提供應用程式的功能。

委派可讓您擴充類別的行為，而不需要建立子類別。 IOS 中的應用程式通常會在發生重要動作之後，當一個類別回呼另一個類別時，使用委派。 例如， `MKMapView` 當使用者在地圖上按下注釋時，類別會回呼其委派，讓委派類別的作者有機會在應用程式內回應。 您可以在本文稍後的範例中，逐步解說這種類型的委派使用方式，例如搭配使用委派與 Xamarin。

此時，您可能會想知道類別如何判斷要在其委派上呼叫的方法。 這是您使用通訊協定的另一個地方。 委派的可用方法通常來自其採用的通訊協定。

### <a name="how-protocols-are-used-with-delegates"></a>如何搭配使用通訊協定與委派

我們先前已瞭解如何使用通訊協定來支援將注釋新增至對應。
通訊協定也用來提供一組已知的方法，讓類別在特定事件發生後呼叫，例如在使用者按下地圖上的注釋或選取資料表中的資料格之後。 執行這些方法的類別稱為呼叫它們的類別的委派。

支援委派的類別會藉由公開委派屬性來執行此動作，其中會指派執行委派的類別。 您為委派所執行的方法將取決於特定委派所採用的通訊協定。 針對 `UITableView` 方法，您可以 `UITableViewDelegate` 針對方法執行此通訊協定，然後針對 `UIAccelerometer` `UIAccelerometerDelegate` 您想要公開委派的 iOS 中的任何其他類別，執行這些動作。

`MKMapView`我們在先前的範例中看到的類別也有一個稱為 Delegate 的屬性，它會在發生各種事件之後呼叫。 的委派 `MKMapView` 類型為 `MKMapViewDelegate` 。
在範例中，您很快就會用到它來回應批註，但首先讓我們討論強式和弱式委派之間的差異。

### <a name="strong-delegates-vs-weak-delegates"></a>強式委派與弱式委派的比較

我們到目前為止所討論的委派是強式委派，這表示它們是強型別。 針對 iOS 中的每個委派通訊協定，會隨附一個強型別類別的 Xamarin。 不過，iOS 也具有弱式委派的概念。 除了子類別化系結至特定委派之目標-C 通訊協定的類別，iOS 也可讓您選擇在任何您喜歡的類別中系結通訊協定方法，其衍生自 NSObject，使用 ExportAttribute 裝飾您的方法，然後提供適當的選取器。
當您採用這種方法時，您可以將類別的實例指派給 WeakDelegate 屬性，而不是委派屬性。 弱式委派可讓您彈性地將委派類別細分為不同的繼承階層。 讓我們來看一個同時使用強式和弱式委派的 Xamarin. iOS 範例。

### <a name="example-using-a-delegate-with-xamarinios"></a>搭配使用委派與 Xamarin 的範例

若要執行程式碼來回應使用者在我們的範例中按下注釋，我們可以將實例子類別化 `MKMapViewDelegate` 並指派給 `MKMapView` 的 `Delegate` 屬性。 `MKMapViewDelegate`通訊協定僅包含選擇性方法。
因此，所有方法都是在 Xamarin 類別中系結至此通訊協定的虛擬 `MKMapViewDelegate` 。 當使用者選取注釋時， `MKMapView` 實例會將 `mapView:didSelectAnnotationView:` 訊息傳送至其委派。 若要在 Xamarin 中處理這種情況，我們需要覆寫 `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` MKMapViewDelegate 子類別中的方法，如下所示：

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

如上所示的 SampleMapDelegate 類別會實作為包含實例之控制器中的嵌套類別 `MKMapView` 。 在 [目標-C] 中，您通常會看到控制器直接在類別內採用多個通訊協定。 不過，因為通訊協定系結至 Xamarin 中的類別，所以實強型別委派的類別通常會以嵌套類別的形式包含。

準備好委派類別之後，您只需要在控制器中具現化委派的實例，並將它指派給 `MKMapView` 的屬性，如下 `Delegate` 所示：

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

若要使用弱式委派來完成相同的工作，您必須在衍生自的任何類別中系結方法， `NSObject` 並將它指派給的 `WeakDelegate` 屬性 `MKMapView` 。 因為 `UIViewController` 類別最終衍生自 `NSObject` （例如 CocoaTouch 中的每個目標-C 類別），所以我們可以直接在控制器中實作為系結至的方法， `mapView:didSelectAnnotationView:` 並將控制器指派給 `MKMapView` ，以 `WeakDelegate` 避免需要額外的嵌套類別。 下列程式碼示範此方法：

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

執行此程式碼時，應用程式的行為會與執行強型別委派版本時完全相同。 這段程式碼的優點是，弱式委派不需要建立在我們使用強型別委派時所建立的額外類別。 不過，這就是安全類型的代價。 如果您在傳遞至的選擇器中犯了錯誤 `ExportAttribute` ，在執行時間之前，您將不會發現。

### <a name="events-and-delegates"></a>事件和委派

委派用於 iOS 中的回呼，類似于 .NET 使用事件的方式。 為了讓 iOS Api 和其使用目標-C 委派看起來更像 .NET，Xamarin。 iOS 會在 iOS 中使用委派的許多地方公開 .NET 事件。

例如，您 `MKMapViewDelegate` 也可以使用 .net 事件在 Xamarin 中執行回應所選取注釋的先前執行。 在此情況下，會在中定義事件 `MKMapView` 並呼叫 `DidSelectAnnotationView` 。 它會有 `EventArgs` 類型的子類別 `MKMapViewAnnotationEventsArgs` 。 的 `View` 屬性 `MKMapViewAnnotationEventsArgs` 會提供批註視圖的參考，您可以在其中繼續進行先前所述的相同執行，如下所示：

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

本文涵蓋如何在 Xamarin 中使用事件、通訊協定和委派。 我們看到了 Xamarin 如何公開控制項的一般 .NET 樣式事件。
接下來，我們已瞭解目標-C 通訊協定，包括它們與 c # 介面的不同之處，以及 Xamarin 如何使用它們。 最後，我們從 Xamarin 的觀點來審視目標-C 委派。 我們看到了 Xamarin 如何同時支援強式和弱型別委派，以及如何將 .NET 事件系結至委派方法。

## <a name="related-links"></a>相關連結

- [通訊協定、委派和事件（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [系結目標-C 類型](~/ios/platform/binding-objective-c/index.md)
- [目標-C 程式設計語言](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [在 Xcode 4 中設計使用者介面](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [適用于 iOS 的核心應用程式能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
