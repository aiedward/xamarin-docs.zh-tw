---
title: Xamarin 中的事件、通訊協定和委派
description: 本檔說明如何在 Xamarin 中使用事件、通訊協定與委派。 這些基本概念在 Xamarin iOS 開發中是普遍的。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2017
ms.openlocfilehash: b4c23792cca0bbaabeeaac38b2756490f1485605
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602927"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Xamarin 中的事件、通訊協定和委派

Xamarin 使用控制項來公開大部分使用者互動的事件。
Xamarin iOS 應用程式使用這些事件的方式，與傳統 .NET 應用程式的方式大致相同。 例如，UIButton 類別有一個稱為 TouchUpInside 的事件，它會使用這個事件，就像這個類別和事件是在 .NET 應用程式中一樣。

除了此 .NET 方法，Xamarin 也會公開可用於更複雜互動和資料系結的另一個模型。 此方法會使用 Apple 呼叫委派和通訊協定的內容。 委派的概念類似于 c # 中的委派，但不是定義和呼叫單一方法，而是在目標 C 中的委派是符合通訊協定的整個類別。 通訊協定類似于 c # 中的介面，不同之處在于它的方法可以是選擇性的。 比方說，若要在 Ios uitableview 範例中填入資料，您會建立一個委派類別，其會在 Ios uitableview 範例會呼叫來填入本身的 UITableViewDataSource 通訊協定中，執行這些方法。

在本文中，您將瞭解所有這些主題，提供您穩固的基礎來處理 Xamarin 中的回呼案例，包括：

- **事件** –使用具有 UIKit 控制項的 .net 事件。
- **通訊協定** –學習什麼是通訊協定以及它們的使用方式，以及建立提供地圖注釋資料的範例。
- **委派** –藉由擴充 map 範例以處理包含批註的使用者互動來學習目標 C 委派，然後瞭解強式和弱式委派之間的差異，以及使用這些委派的時機。

為了說明通訊協定和委派，我們將建立簡單的地圖應用程式，以將注釋新增至地圖，如下所示：

[ ![ 簡單的地圖應用程式範例，可將批註](delegates-protocols-and-events-images/01-map-sml.png)](delegates-protocols-and-events-images/01-map.png#lightbox)加入至地圖，並將 
 [ ![ 範例注釋新增至](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)地圖

在處理此應用程式之前，讓我們先查看 UIKit 底下的 .NET 事件。

## <a name="net-events-with-uikit"></a>使用 UIKit 的 .NET 事件

Xamarin 會在 UIKit 控制項上公開 .NET 事件。 例如，UIButton 有一個 TouchUpInside 事件，您可以像平常在 .NET 中處理一樣，如下列使用 c # lambda 運算式的程式碼所示：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

您也可以使用 c # 2.0 樣式匿名方法（如下所示）來執行這項操作：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

上述程式碼會在 UIViewController 的方法中連接 `ViewDidLoad` 。 `aButton`變數會參考按鈕，您可以在 Xcode 介面產生器或程式碼中加入該按鈕。 

Xamarin 也支援將您的程式碼連接到與控制項發生互動的目標動作樣式。 

如需有關 iOS 目標動作模式的詳細資訊，請參閱 Apple iOS Developer Library 中 [適用于 ios 的核心應用程式](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Target-Action/Target-Action.html#//apple_ref/doc/uid/TP40010810-CH12) 專長認證的 Target-Action 一節。

如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。

## <a name="events"></a>事件

如果您想要攔截 UIControl 中的事件，您有一系列的選項：從使用 c # lambda 和委派函式，到使用低層級的目標 C Api。

下一節將說明如何根據您需要的控制程度，在按鈕上捕捉 TouchDown 事件。

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

UIControlEvent 旗標的 c # 事件與個別旗標有一對一的對應。 當您想要讓同一段程式碼處理兩個或多個事件時，請使用 `UIControl.AddTarget` 方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 語法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果您需要使用目標-C 的低層級功能，例如與特定物件實例連結，並叫用特定的選取器：

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

通訊協定是一種可提供方法聲明清單的目標 C 語言功能。 它的用途與 c # 中的介面類別似，主要的差別在於通訊協定可以有選擇性方法。 如果採用通訊協定的類別沒有執行，則不會呼叫選擇性方法。 此外，目標 C 的單一類別可以實作為多個通訊協定，就像 c # 類別可以執行多個介面一樣。

Apple 會使用整個 iOS 的通訊協定來定義要採用之類別的合約，同時從呼叫端抽離實類別，進而像 c # 介面一樣運作。 在非委派案例中，會使用通訊協定 (例如 `MKAnnotation`) 下圖所示的範例，以及本檔稍後的委派 (（如本文稍後所示），) 的委派區段中。

### <a name="protocols-with-xamarinios"></a>使用 Xamarin 的通訊協定

讓我們看看使用來自 Xamarin 的目標 C 通訊協定的範例。 在此範例中，我們將使用此 `MKAnnotation` 通訊協定，這是架構的一部分 `MapKit` 。 `MKAnnotation` 是一種通訊協定，可讓任何採用它的物件提供可新增至地圖的批註相關資訊。 例如，執行的物件 `MKAnnotation` 會提供注釋的位置和與其相關聯的標題。

如此一 `MKAnnotation` 來，通訊協定可用來提供注釋隨附的相關資料。 批註本身的實際觀點是根據採用通訊協定之物件中的資料所建立 `MKAnnotation` 。 例如，當使用者按下注釋時顯示的注標文字 (如下列螢幕擷取畫面所示) 來自 `Title` 可執行通訊協定之類別中的屬性：

 [![當使用者按注釋時，標注的範例文字](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

如同下一節中的 [通訊協定深入探討](#protocols-deep-dive)，Xamarin 會將通訊協定系結至抽象類別。 針對 `MKAnnotation` 通訊協定，系結的 c # 類別會命名 `MKAnnotation` 為模仿通訊協定的名稱，而這是 `NSObject` CocoaTouch 的根基類的子類別。 通訊協定需要針對座標實作為 getter 和 setter;不過，標題和子標題是選擇性的。 因此，在類別中， `MKAnnotation` `Coordinate` 屬性是 *抽象* 的，需要實作為，而且 `Title` 和 `Subtitle` 屬性標示為 *虛擬*，使其成為選擇性，如下所示：

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

任何類別都可以藉由直接衍生自來提供注釋資料 `MKAnnotation` ，只要至少 `Coordinate` 執行了屬性即可。 例如，以下範例類別會採用函式中的座標，並傳回標題的字串：

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

透過其所系結的通訊協定，子類別 `MKAnnotation` 可提供相關資料的任何類別，當它建立批註的視圖時，將會使用對應的資料。 若要將批註加入至對應，只要呼叫 `AddAnnotation` 實例的方法 `MKMapView` ，如下列程式碼所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

此處的對應變數是的實例 `MKMapView` ，這是代表地圖本身的類別。 `MKMapView`將使用 `Coordinate` 衍生自實例的資料，將 `SampleMapAnnotation` 批註視圖置於地圖上。

`MKAnnotation`此通訊協定提供一組已知的功能，可供任何執行它的物件使用，在此情況下，不需要取用者 (對應) 需要瞭解執行詳細資料。 這可簡化將各種可能的注釋新增至地圖。

### <a name="protocols-deep-dive"></a>通訊協定深入探討

因為 c # 介面不支援選擇性方法，所以 Xamarin 會將通訊協定對應至抽象類別。 因此，在目標 C 中採用通訊協定是在 Xamarin 中完成，方法是從系結至通訊協定並執行必要方法的抽象類別衍生。 這些方法將公開為類別中的抽象方法。 來自通訊協定的選擇性方法會系結至 c # 類別的虛擬方法。

例如，以下是在 Xamarin 中系結的 `UITableViewDataSource` 通訊協定部分：

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

請注意，此類別是抽象的。 Xamarin 可讓類別抽象化，以支援通訊協定中的選擇性/必要方法。
不過，不同于目標 C 通訊協定， (或 c # 介面) ，c # 類別不支援多重繼承。 這會影響使用通訊協定的 c # 程式碼設計，而且通常會導致嵌套類別。 本檔稍後的「委派」一節中會討論此問題的詳細資訊。

 `GetCell(…)` 是一種抽象方法，系結至目標 C *選取器*， `tableView:cellForRowAtIndexPath:` 它是通訊協定的必要方法 `UITableViewDataSource` 。 選取器是方法名稱的目標 C 詞彙。 為了在必要時強制執行此方法，Xamarin 會將它宣告為抽象。 另一個方法會系結 `NumberOfSections(…)` 至 `numberOfSectionsInTableview:` 。 此方法在通訊協定中是選擇性的，因此，Xamarin 會將它宣告為虛擬，讓它可以選擇在 c # 中覆寫。

Xamarin 會為您處理所有 iOS 系結。 但是，如果您需要以手動方式從目標 C 系結通訊協定，您可以使用來裝飾類別 `ExportAttribute` 。 這與 Xamarin 本身所使用的方法相同。

如需如何在 Xamarin 中系結目標 C 類型的詳細資訊，請參閱系結 [目標-c 類型一](~/ios/platform/binding-objective-c/index.md)文。

不過，我們還沒有透過通訊協定。 它們也會在 iOS 中用來作為目標 C 委派的基礎，也就是下一節的主題。

## <a name="delegates"></a>委派

iOS 使用目標 C 委派來實行委派模式，其中一個物件會將工作傳遞至另一個物件。 執行工作的物件是第一個物件的委派。 物件會在發生特定事情之後傳送訊息，告知其委派執行工作。 在目標 C 中傳送這類訊息的功能相當於在 c # 中呼叫方法。 委派會實作為回應這些呼叫的方法，並提供應用程式的功能。

委派可讓您擴充類別的行為，而不需要建立子類別。 IOS 中的應用程式通常會在某個類別回呼至另一個類別，並在發生重要動作之後，使用委派。 例如， `MKMapView` 當使用者在地圖上按下批註時，類別會回呼至其委派，讓委派類別的作者有機會在應用程式內回應。 您可以在本文稍後的範例中，使用這類委派使用方式的範例，例如搭配使用具有 Xamarin 的委派。

到目前為止，您可能會想知道類別如何判斷要在其委派上呼叫的方法。 這是您使用通訊協定的另一個位置。 通常，委派的可用方法來自所採用的通訊協定。

### <a name="how-protocols-are-used-with-delegates"></a>如何搭配使用通訊協定與委派

我們稍早看到了如何使用通訊協定來支援將批註新增至地圖。
通訊協定也用來提供一組已知的方法，讓類別在特定事件發生之後呼叫，例如在使用者按下地圖上的注釋或選取資料表中的資料格之後。 執行這些方法的類別稱為呼叫它們的類別委派。

支援委派的類別會藉由公開委派屬性來執行此動作，而該屬性會將執行委派的類別指派給該屬性。 您為委派所實行的方法將取決於特定委派採用的通訊協定。 針對 `UITableView` 方法，您可以針對要為 `UITableViewDelegate` `UIAccelerometer` `UIAccelerometerDelegate` 其公開委派的任何其他 iOS 類別，執行該方法的通訊協定，依此類推。

`MKMapView`我們在先前的範例中看到的類別也有一個稱為 Delegate 的屬性，它會在發生各種事件之後呼叫。 的委派 `MKMapView` 是型別 `MKMapViewDelegate` 。
您稍後會在範例中使用這項功能，以便在批註被選取後回應，但首先讓我們討論強式和弱式委派之間的差異。

### <a name="strong-delegates-vs-weak-delegates"></a>強式委派和弱式委派

我們目前為止所探討的委派是強式委派，這表示它們是強型別。 針對 iOS 中的每個委派通訊協定，各有一個強型別類別隨附的 Xamarin 系結。 不過，iOS 也有弱式委派的概念。 IOS 也可讓您選擇在任何您喜歡的類別（衍生自 NSObject）中自行系結通訊協定方法、使用 ExportAttribute 裝飾您的方法，然後提供適當的選取器，而不是子類別化系結至特定委派之目標 C 通訊協定的類別。
當您採用這種方法時，您可以將類別的實例指派給 WeakDelegate 屬性，而不是委派屬性。 弱式委派可讓您彈性地將委派類別帶到不同的繼承階層。 讓我們看看使用強式和弱式委派的 Xamarin. iOS 範例。

### <a name="example-using-a-delegate-with-xamarinios"></a>範例：搭配使用委派與 Xamarin

若要執行程式碼以回應在我們的範例中使用注釋的使用者，我們可以將實例子類別化 `MKMapViewDelegate` 並指派給 `MKMapView` 的 `Delegate` 屬性。 `MKMapViewDelegate`通訊協定只包含選擇性方法。
因此，所有方法都是在 Xamarin 類別中系結至此通訊協定的虛擬 `MKMapViewDelegate` 。 當使用者選取批註時， `MKMapView` 實例會將 `mapView:didSelectAnnotationView:` 訊息傳送到其委派。 若要在 Xamarin 中處理這種情況，我們需要覆寫 `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` MKMapViewDelegate 子類別中的方法，如下所示：

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

上方顯示的 SampleMapDelegate 類別會實作為包含實例之控制器中的嵌套類別 `MKMapView` 。 在目標 C 中，您通常會看到控制器直接採用類別內的多個通訊協定。 不過，由於通訊協定會系結至 Xamarin 中的類別，因此執行強型別委派的類別通常會包含為嵌套類別。

有了委派類別的實，您只需要在控制器中具現化委派的實例，並將它指派給的 `MKMapView` `Delegate` 屬性，如下所示：

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

若要使用弱式委派來完成相同的工作，您必須在衍生自的任何類別中自行系結方法， `NSObject` 並將它指派給的 `WeakDelegate` 屬性 `MKMapView` 。 由於 `UIViewController` 類別最終衍生自 `NSObject` (，就像 CocoaTouch) 中的每個目標 C 類別一樣，我們可以直接在控制器中執行系結至的方法， `mapView:didSelectAnnotationView:` 並將控制器指派給 `MKMapView` ，以 `WeakDelegate` 避免需要額外的嵌套類別。 下列程式碼示範此方法：

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

執行此程式碼時，應用程式的行為會與執行強型別委派版本的行為完全相同。 這段程式碼的優點是弱式委派不需要建立使用強型別委派時所建立的額外類別。 不過，這會犧牲型別安全。 如果您在傳遞給的選取器中犯錯誤 `ExportAttribute` ，則在執行時間之前將不會發現。

### <a name="events-and-delegates"></a>事件和委派

委派可用於 iOS 中的回呼，類似于 .NET 使用事件的方式。 為了讓 iOS Api 與其使用目標 C 委派的方式看起來更像 .NET，Xamarin 會在 iOS 中使用委派的許多地方公開 .NET 事件。

例如，在先前的執行中，您 `MKMapViewDelegate` 也可以使用 .net 事件在 Xamarin 中實作為所選取批註的回應。 在此情況下，會在中定義事件 `MKMapView` 並呼叫 `DidSelectAnnotationView` 。 它會有一個 `EventArgs` 類型的子類別 `MKMapViewAnnotationEventsArgs` 。 的 `View` 屬性 `MKMapViewAnnotationEventsArgs` 會提供批註視圖的參考，您可以在其中繼續進行先前所述的相同執行，如下所示：

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

## <a name="summary"></a>摘要

本文涵蓋如何在 Xamarin 中使用事件、通訊協定與委派。 我們看到了 Xamarin 如何公開控制項的一般 .NET 樣式事件。
接下來，我們瞭解到目標 C 通訊協定，包括它們與 c # 介面的不同之處，以及 Xamarin 如何使用它們。 最後，我們從 Xamarin 的觀點來檢驗目標 C 委派。 我們看到了 Xamarin 如何支援強式和弱型別的委派，以及如何系結 .NET 事件以委派方法。

## <a name="related-links"></a>相關連結

- [通訊協定、委派和事件 (範例) ](/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [系結目標-C 類型](~/ios/platform/binding-objective-c/index.md)
- [目標 C 程式設計語言](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [在 Xcode 4 中設計使用者介面](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [適用于 iOS 的核心應用程式能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)