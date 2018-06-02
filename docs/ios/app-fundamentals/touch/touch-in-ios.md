---
title: 在 iOS 中觸控
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9ed90a9c4ddcd398d834cb8c91553a57e7bd5ad8
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689537"
---
# <a name="touch-in-ios"></a>在 iOS 中觸控

務必了解觸控事件及觸控 Api iOS 應用程式中，因為它們是與裝置的所有實體互動的核心。 觸控的所有互動都涉及`UITouch`物件。 在本文中，我們會了解如何使用`UITouch`類別和其支援觸控的 Api。 更新版本中，我們將會展開以了解如何支援筆勢我們知道。

## <a name="enabling-touch"></a>啟用觸控

中的控制項`UIKit`– 這些子類別化從 ui 控制 – 是根據它們有筆勢 UIKit 內建的使用者互動，因此不需要啟用觸控。 已啟用。

不過，許多中檢視的`UIKit`沒有觸控式預設會啟用。 有兩種方式可啟用觸控在控制項上。 第一種方式是選取啟用的使用者互動的核取方塊在屬性輸入板中的 ios 設計工具中，如下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image1.png "檢查屬性板的 ios 設計工具中的使用者互動啟用核取方塊")](touch-in-ios-images/image1.png#lightbox)

我們也可以使用控制站來設定`UserInteractionEnabled`屬性設定為 true，在`UIView`類別。 如果程式碼中建立 UI，這是必要的。

下列程式碼行是一個範例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>觸控事件

有三個階段因為使用者觸螢幕，移動其指紋，或移除其手指觸控。 這些方法會定義在`UIResponder`，這是 UIView 的基底類別。 iOS 會覆寫相關聯的方法上`UIView`和`UIViewController`處理觸控：

-  `TouchesBegan` – 這第一次接觸到螢幕時呼叫。
-  `TouchesMoved` – 這觸控變更的位置，以使用者的身分滑動其隻手指放在螢幕上的時呼叫。
-  `TouchesEnded` 或`TouchesCancelled`–`TouchesEnded`使用者指會提升超出螢幕時呼叫。  `TouchesCancelled` 取得呼叫 iOS 取消觸控 – 例如，如果使用者的投影片他或她手指遠離取消按下按鈕。


觸控事件旅行遞迴地向下，透過 UIViews，檢查是否在檢視物件的界限內觸控事件的堆疊。 這通常稱為_點擊測試_。 將第一次呼叫上最上層`UIView`或`UIViewController`然後會在呼叫`UIView`和`UIViewControllers`下方檢視階層中。

A`UITouch`物件將會建立每次使用者接觸到螢幕。 `UITouch`物件包含觸控，例如觸控發生時，發生的位置，如果觸控撥動等等的相關資料。觸控事件傳遞修飾屬性 –`NSSet`包含一或多個工作。 我們可以使用這個屬性來取得觸控的參考，並決定應用程式的回應。

覆寫其中一個觸控事件的類別應該先呼叫基底實作，然後取得`UITouch`與事件相關聯的物件。 若要取得第一個觸控的參考，請呼叫`AnyObject`屬性並將它做為轉換`UITouch`做為顯示在下列範例中：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS 會自動識別後續快速接觸到螢幕上，則會收集這些全部都做為點選一次在單一`UITouch`物件。 這可讓檢查點兩下，只要檢查`TapCount`屬性，如下列程式碼所示：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>多點觸控

預設會在控制項上未啟用多點觸控。 可以在 iOS 設計工具中，啟用多點觸控，如下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image2.png "啟用 iOS 設計工具中的多點觸控")](touch-in-ios-images/image2.png#lightbox)

也可以藉由設定，以程式設計方式設定多點觸控`MultipleTouchEnabled`屬性，如下列程式碼所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要判斷多少手指接觸到螢幕，請使用`Count`屬性`UITouch`屬性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>決定觸控位置

此方法`UITouch.LocationInView`傳回 CGPoint 物件，這個物件會保存在給定的檢視觸控的座標。 此外，我們可以測試以查看該位置是否在控制項中，呼叫方法`Frame.Contains`。 下列程式碼片段會顯示此動作的範例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

現在，我們在 iOS 中具有觸控事件的了解，讓我們了解筆勢辨識器。

## <a name="gesture-recognizers"></a>筆勢辨識器

筆勢辨識器可大幅簡化，並減少應用程式中支援觸控的程式設計工作。 iOS 的筆勢辨識器彙總的單一觸控事件的一系列觸控事件。

提供類別，Xamarin.iOS`UIGestureRecognizer`為下列的內建的筆勢辨識器的基底類別：

-  *UITapGestureRecognizer* – 這是針對一個或多個點選。
-  *UIPinchGestureRecognizer* – Pinching 和分配分開手指。
-  *UIPanGestureRecognizer* – 移動或拖曳。
-  *UISwipeGestureRecognizer* – 撥動朝任何方向。
-  *UIRotationGestureRecognizer* – 順時針或逆時針方向移動中的兩指的旋轉。
-  *UILongPressGestureRecognizer* – 按住，有時稱為長時間按或長時間按一下。


若要使用的筆勢辨識器的基本模式如下所示：

1.  **具現化的筆勢辨識器**-首先，具現化`UIGestureRecognizer`子類別。 具現化的物件將會以檢視相關聯，並會進行記憶體回收時，檢視會處置。 您不需要建立此檢視的類別層級變數。
1.  **設定任何的筆勢**– 下一步是設定的筆勢辨識器。 請參閱上的 Xamarin 的文件`UIGestureRecognizer`和子類別的可設定來控制行為的屬性清單`UIGestureRecognizer`執行個體。
1.  **設定目標**– 其 Objective C 的傳承，因為 Xamarin.iOS 不會引發事件的筆勢辨識器符合筆勢時。  `UIGestureRecognizer` 具有方法 – `AddTarget` –，可以接受匿名委派或 OBJECTIVE-C 選取器以程式碼執行時的筆勢辨識器相符項目。
1.  **啟用筆勢辨識器**– 如同與觸控事件筆勢只辨識如果啟用觸控互動。
1.  **筆勢辨識器新增至檢視**– 最後一個步驟是加入到檢視的手勢，藉由呼叫`View.AddGestureRecognizer`，並將其傳遞的筆勢辨識器物件。

請參閱[筆勢辨識器範例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)如需有關如何在程式碼中實作它們。

當呼叫的筆勢目標時，它將會傳遞發生的筆勢的參考。 這可讓筆勢目標來取得有關所發生的筆勢。 提供之資訊的範圍取決於所使用的筆勢辨識器類型。 Xamarin 的文件的每個可用資料的相關資訊，請參閱`UIGestureRecognizer`子類別。

請務必記得，一旦筆勢辨識器加入至檢視，檢視 （和其下的任何檢視） 將不會收到任何觸控事件。 若要允許同時使用筆勢，觸控事件`CancelsTouchesInView`屬性必須設定為 false，如下列程式碼所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每個`UIGestureRecognizer`已提供的筆勢辨識器狀態的重要資訊的狀態屬性。 每次這個屬性的值變更時，iOS 會呼叫提供更新的訂閱方法。 如果自訂的筆勢辨識器永遠不會更新 [狀態] 屬性，「 訂閱者 」 會永遠不會呼叫，轉譯的筆勢辨識器無用。

筆勢可以摘要如下兩種類型之一：

1.  *離散*– 它們會辨識這些筆勢只引發的第一個時間。
1.  *連續*– 繼續引發，只要它們會辨識這些手勢。


筆勢辨識器存在於下列狀態其中之一：

-  *可能*– 這是所有的筆勢辨識器的初始狀態。 這是預設值的狀態屬性。
-  *Began* – 連續的筆勢辨識後第一次，狀態會設為 Began。 這可讓訂閱區分筆勢辨識啟動時，以及變更時。
-  *已變更*– 連續筆勢已經開始，但是尚未完成之後，狀態將設已變更每次觸控式移動或變更時，只要它仍在預期的參數的筆勢。
-  *取消*– 如果辨識器從 Began 去已變更，而且不會再變更的方式與修飾然後容納軌跡的模式，就會設定此狀態。
-  *辨識*– 將設定的狀態，當筆勢辨識器符合一組工作，並會通知 「 訂閱者 」 已完成的筆勢。
-  *結束*– 這是 Recognized 狀態的別名。
-  *失敗*– 當筆勢辨識器不會再比對它正在接聽的狀態會變更為 「 失敗的工作。


Xamarin.iOS 表示這些值`UIGestureRecognizerState`列舉型別。

## <a name="working-with-multiple-gestures"></a>使用多個的筆勢

根據預設，iOS 不允許預設手勢，同時執行。 相反地，每個筆勢辨識器將會收到觸控事件的順序不具決定性。 下列程式碼片段說明如何讓同時執行的筆勢辨識器：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

它也可停用在 iOS 中的筆勢。 有兩種委派屬性可讓您檢查應用程式和目前的觸控事件，請決定要如何和手勢，應該要辨識狀態的筆勢辨識器。 有兩個事件：

1.  *ShouldReceiveTouch* – 筆勢辨識器傳遞到觸控事件，並讓您有機會檢查修飾，並決定哪些工作交由筆勢辨識器之前，會呼叫這個委派。
1.  *ShouldBegin* – 這會辨識器會嘗試從可能的狀態變更為其他狀態時呼叫。 傳回 false，將會強制筆勢辨識器變更為 「 失敗的狀態。


您可以覆寫這些方法可搭配強型別`UIGestureRecognizerDelegate`、 弱式委派或繫結，透過事件處理常式語法，如下列程式碼片段所示：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最後，很可能將排入的筆勢辨識器佇列，如此才會成功如果另一個的筆勢辨識器失敗。 例如，只要點選一下筆勢辨識器只應該會成功連點兩下筆勢辨識器失敗時。 下列程式碼片段提供一個範例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>建立自訂的筆勢

雖然 iOS 提供一些預設的筆勢辨識器，可能需要在某些情況下建立自訂的筆勢辨識器。 建立自訂的筆勢辨識器包括下列步驟：

1.  子類別`UIGestureRecognizer`。
1.  覆寫的適當觸控事件的方法。
1.  反昇辨識透過基底類別的狀態屬性的狀態。


一個實用的範例涵蓋[在 iOS 中使用觸控](ios-touch-walkthrough.md)逐步解說。
