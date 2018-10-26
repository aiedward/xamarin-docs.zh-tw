---
title: 觸控事件和在 Xamarin.iOS 中的筆勢
description: 本文件說明如何使用觸控事件、 多點觸控、 手勢、 多個筆勢和在 Xamarin.iOS 應用程式中的自訂軌跡。
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f7160c48e1b1ac85f4aa0173c0eb9f42b8fefca2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114765"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>觸控事件和在 Xamarin.iOS 中的筆勢

務必了解觸控事件和觸控 Api 的 iOS 應用程式，因為它們是與裝置的所有實體互動的核心。 所有的觸控互動涉及`UITouch`物件。 在本文中我們將了解如何使用`UITouch`類別和其 Api，以支援觸控。 稍後，我們將探討我們所知，了解如何支援筆勢。

## <a name="enabling-touch"></a>啟用觸控

中的控制項`UIKit`– 這些子類別化之從 ui 控制 – 是根據它們有筆勢 UIKit 中內建的使用者互動，因此不需要啟用觸控。 已啟用。

不過，許多中檢視的`UIKit`沒有觸控式預設會啟用。 有兩種方式可啟用觸控式控制項上。 第一種方式是核取方塊啟用的使用者互動的 「 iOS 設計工具，[屬性] 面板中，下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image1.png "核取方塊啟用的使用者互動的 「 iOS 設計工具的 [屬性] 面板中")](touch-in-ios-images/image1.png#lightbox)

我們也可以使用一個控制器來設定`UserInteractionEnabled`屬性設為 true，在`UIView`類別。 如果程式碼建立 UI，這是必要的。

下列程式碼是範例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>觸控事件

有三個階段使用者觸控螢幕，將他們的手指，或是移除他們的手指時，發生觸控。 這些方法的定義位於`UIResponder`，這是 UIView 的基底類別。 iOS 會在項目覆寫關聯的方法`UIView`而`UIViewController`處理觸控：

-  `TouchesBegan` – 這第一次接觸到螢幕時呼叫。
-  `TouchesMoved` – 這是在使用者的觸控式變更位置滑動他們的手指在螢幕上時被呼叫的。
-  `TouchesEnded` 或是`TouchesCancelled`–`TouchesEnded`使用者手指在螢幕會被提取時，會呼叫。  `TouchesCancelled` 取得呼叫 iOS 取消觸控 – 比方說，如果，如果使用者的投影片他或她手指離開取消按的按鈕。


向下觸控事件的移動以遞迴方式，透過 UIViews，來檢查的檢視物件的範圍內是否為觸控事件的堆疊。 這通常稱為_點擊測試_。 將第一次呼叫上的最上層`UIView`或是`UIViewController`然後會在呼叫`UIView`和`UIViewControllers`下方檢視階層中。

A`UITouch`物件將會建立每個使用者接觸到螢幕的時間。 `UITouch`物件包含觸控，例如觸控發生時，發生的位置，如果觸控撥動等等的相關資料。觸控事件傳遞修飾屬性 –`NSSet`包含一或多個修飾。 我們可以使用這個屬性來取得的觸控式的參考，並決定應用程式的回應。

覆寫其中一個觸控事件的類別應該先呼叫基底實作，然後取得`UITouch`事件相關聯的物件。 若要取得第一個觸控式的參考，請呼叫`AnyObject`屬性並將它轉換為`UITouch`做為顯示在下列範例中：

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

iOS 會自動識別後續的快速接觸到螢幕上，並會收集這些做為在單一點選`UITouch`物件。 這可讓檢查點兩下，只要檢查`TapCount`屬性，如下列程式碼所示：

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

預設會在控制項上未啟用多點觸控。 「 IOS 設計工具，可以啟用多點觸控，如下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image2.png "啟用 iOS 設計工具中的多點觸控")](touch-in-ios-images/image2.png#lightbox)

您也可透過設定以程式設計方式設定多點觸控`MultipleTouchEnabled`屬性，如下列程式碼所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要判斷多少手指接觸到螢幕，請使用`Count`屬性上的`UITouch`屬性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>判斷觸控位置

此方法`UITouch.LocationInView`傳回 CGPoint 物件，包含指定的檢視中觸控的座標。 此外，我們可以測試以查看該位置是否在控制項中，藉由呼叫方法`Frame.Contains`。 下列程式碼片段會顯示這個範例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

既然我們已經了解觸控事件，在 iOS 中，讓我們深入了解筆勢辨識器。

## <a name="gesture-recognizers"></a>筆勢辨識器

筆勢辨識器可以大幅簡化並減少應用程式中支援觸控的程式設計工作。 iOS 筆勢辨識器來彙總成單一觸控事件一系列的觸控事件。

提供類別，Xamarin.iOS`UIGestureRecognizer`為下列的內建的筆勢辨識器的基底類別：

-  *UITapGestureRecognizer* – 這是一或多個點選。
-  *UIPinchGestureRecognizer* – Pinching 和分配分開手指。
-  *UIPanGestureRecognizer* -移動或拖曳。
-  *UISwipeGestureRecognizer* – 撥動以任何方向。
-  *UIRotationGestureRecognizer* – 順時針或逆時針方向移動中的兩指的旋轉。
-  *UILongPressGestureRecognizer* – 按住不放，有時稱為長按下或長按。


若要使用的筆勢辨識器的基本模式如下所示：

1.  **筆勢辨識器具現化**-首先，具現化`UIGestureRecognizer`子類別。 具現化的物件將會以檢視相關聯，並會進行記憶體回收時的檢視，會處置。 您不需要建立此檢視為類別層級變數。
1.  **設定任何筆勢**– 下一步是設定筆勢辨識器。 請參閱上的 Xamarin 的文件`UIGestureRecognizer`如需可設定來控制行為的屬性的清單及其子類別`UIGestureRecognizer`執行個體。
1.  **設定目標**– 因為其 Objective C 的傳承，Xamarin.iOS 不引發事件的筆勢辨識器符合筆勢時。  `UIGestureRecognizer` 方法 – `AddTarget` – 可以接受匿名委派或 OBJECTIVE-C 選取器，以程式碼執行時的筆勢辨識器相符項目。
1.  **啟用筆勢辨識器**– 就像使用觸控事件時，軌跡只辨識如果啟用觸控式互動。
1.  **筆勢辨識器新增至檢視**– 最後一個步驟是將軌跡新增至檢視中，藉由呼叫`View.AddGestureRecognizer`，並將其傳遞的筆勢辨識器物件。

請參閱[筆勢辨識器範例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)如需有關如何在程式碼中實作它們。

呼叫筆勢的目標時，它會傳遞發生的筆勢的參考。 這可讓筆勢目標以取得發生的筆勢的相關資訊。 所提供的資訊範圍取決於所使用的筆勢辨識器的類型。 Xamarin 的文件的每個可用資料的相關資訊，請參閱`UIGestureRecognizer`子類別。

請務必記住，一旦筆勢辨識器已新增至檢視，檢視 （和其下的任何檢視） 將不會收到任何觸控事件。 若要允許觸控事件，同時運用筆勢`CancelsTouchesInView`屬性必須設為 false，如下列程式碼所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每個`UIGestureRecognizer`已提供狀態相關的重要資訊的筆勢辨識器的狀態屬性。 每次這個屬性的值變更時，iOS 會呼叫訂閱提供更新的方法。 如果自訂的筆勢辨識器永遠不會更新 [狀態] 屬性，「 訂閱者 」 會永遠不會呼叫，轉譯的筆勢辨識器沒有什麼用處。

筆勢可總結為兩種類型之一：

1.  *離散*– 它們會辨識這些筆勢只引發的第一個時間。
1.  *連續*– 這些筆勢繼續引發，因為它們會被辨識。


筆勢辨識器存在於下列狀態其中之一：

-  *可能*– 這是所有的筆勢辨識器的初始狀態。 這是預設值的狀態屬性。
-  *開始*– 在第一次識別持續的筆勢，狀態會設為開始。 這可讓訂閱區分筆勢辨識啟動時與變更時。
-  *已變更*– 連續筆勢開始進行，但尚未完成之後，狀態將設 Changed 每次在觸控移動或變更時，只要它是仍在筆勢的預期的參數內。
-  *取消*– 如果辨識器已開始從已變更，並不會再變更的方式而修飾然後符合筆勢的模式，將會設定此狀態。
-  *辨識*– 筆勢辨識器符合一組的修飾，並將用來告知筆勢已完成的 「 訂閱者 」 時，就會設定的狀態。
-  *結束*– 這是 Recognized 狀態的別名。
-  *失敗*– 當筆勢辨識器可以不再符合與其正在接聽的狀態會變更為 Failed 修飾。


Xamarin.iOS 代表這些值`UIGestureRecognizerState`列舉型別。

## <a name="working-with-multiple-gestures"></a>處理多個筆勢

根據預設，iOS 不允許同時執行的預設筆勢。 相反地，每個筆勢辨識器會在不具決定性的順序收到觸控事件。 下列程式碼片段說明如何讓同時執行的筆勢辨識器：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

它也可停用在 iOS 中的筆勢。 有兩個委派屬性，可讓以檢查狀態的應用程式和目前的觸控事件，可讓決定要如何和筆勢應該要辨識的筆勢辨識器。 兩個事件︰

1.  *ShouldReceiveTouch* – 筆勢辨識器傳遞觸控事件，並讓您有機會檢查修飾，並決定哪些修飾交由筆勢辨識器之前，會呼叫這個委派。
1.  *ShouldBegin* – 這在辨識器會嘗試從可能的狀態變更為某個其他狀態時呼叫。 傳回 false，將會強制筆勢辨識器變更為失敗的狀態。


您可以覆寫這些方法可搭配強型別`UIGestureRecognizerDelegate`、 弱式委派或繫結，透過事件處理常式語法，如下列程式碼片段所示：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最後，就可能筆勢辨識器排入佇列，如此它才會成功另一個的筆勢辨識器就會失敗。 比方說，只要點選一下筆勢辨識器應該只會成功連點兩下筆勢辨識器失敗時。 下列程式碼片段提供一個範例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>建立自訂的筆勢

雖然 iOS 會提供一些預設的筆勢辨識器，可能需要在某些情況下建立自訂的筆勢辨識器。 建立自訂的筆勢辨識器包含下列步驟：

1.  子類別`UIGestureRecognizer`。
1.  覆寫適當的觸控事件的方法。
1.  事件反昇辨識透過基底類別的狀態屬性的狀態。


一個實用的範例涵蓋[在 iOS 中使用觸控](ios-touch-walkthrough.md)逐步解說。
