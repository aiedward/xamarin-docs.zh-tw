---
title: Xamarin 中的觸控事件和手勢
description: 本檔說明如何在 Xamarin iOS 應用程式中使用觸控事件、多點觸控、筆勢、多個手勢和自訂手勢。
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 9dd06044ea9c4bf28e785932d316b3222c6cd16b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009149"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Xamarin 中的觸控事件和手勢

請務必瞭解 iOS 應用程式中的觸控事件和觸控 Api，因為它們是與裝置的所有實體互動的核心。 所有觸控互動都牽涉到 `UITouch` 物件。 在本文中，我們將瞭解如何使用 `UITouch` 類別及其 Api 來支援觸控。 我們稍後將在我們的知識中擴展，以瞭解如何支援手勢。

## <a name="enabling-touch"></a>啟用觸控

`UIKit` 中的控制項（從 UIControl 子類別化）會相依于他們在 UIKit 中內建手勢的使用者互動，因此不需要啟用觸控。 已啟用此功能。

不過，`UIKit` 中的許多視圖預設不會啟用觸控功能。 有兩種方式可啟用控制項的觸控。 第一種方式是在 iOS 設計工具的屬性 Pad 中檢查 [已啟用使用者互動] 核取方塊，如下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image1.png "Check the User Interaction Enabled checkbox in the Property Pad of the iOS Designer")](touch-in-ios-images/image1.png#lightbox)

我們也可以使用控制器，將 `UIView` 類別上的 `UserInteractionEnabled` 屬性設定為 true。 如果是在程式碼中建立 UI，這就是必要的。

下列程式程式碼是一個範例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>觸控事件

當使用者觸及螢幕、移動手指或移除手指時，會發生三個階段的觸控。 這些方法是在 `UIResponder`中定義，這是 UIView 的基類。 iOS 將覆寫 `UIView` 上的相關聯方法，`UIViewController` 以處理觸控：

- `TouchesBegan` –這是在第一次接觸畫面時呼叫。
- `TouchesMoved` –這會在觸控的位置隨著使用者在螢幕上滑動手指而變更時呼叫。
- `TouchesEnded` 或 `TouchesCancelled` –在使用者的手指從螢幕中提起時，會呼叫 `TouchesEnded`。  如果 iOS 取消觸控，就會呼叫 `TouchesCancelled`，例如，如果使用者將其手指滑掉按鈕來取消按下。

觸控事件會以遞迴方式在 UIViews 的堆疊中往下移動，以檢查觸控事件是否在 view 物件的界限內。 這通常稱為_點擊測試_。 首先會在最上層的 `UIView` 或 `UIViewController` 上呼叫它們，然後在 `UIView` 上呼叫，然後在視圖階層中的下方 `UIViewControllers`。

每次使用者接觸螢幕時，就會建立 `UITouch` 物件。 `UITouch` 物件包含觸控的相關資料，例如觸控發生的時間、發生的位置、觸碰的觸控等等。觸控事件會通過觸控屬性–包含一個或多個觸控的 `NSSet`。 我們可以使用這個屬性來取得觸控的參考，並決定應用程式的回應。

覆寫其中一個觸控事件的類別應該先呼叫基底實作為，然後取得與事件相關聯的 `UITouch` 物件。 若要取得第一個觸控的參考，請呼叫 `AnyObject` 屬性，並將它轉換為 `UITouch`，如下列範例所示：

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

iOS 會自動辨識螢幕上的連續快速操作，並將其全部收集成單一 `UITouch` 物件中的一分。 如此一來，就可以像檢查 `TapCount` 屬性一樣簡單地檢查，如下列程式碼所示：

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

在控制項上預設不會啟用多點觸控功能。 您可以在 iOS 設計工具中啟用多點觸控，如下列螢幕擷取畫面所示：

 [![](touch-in-ios-images/image2.png "Multi-touch enabled in the iOS Designer")](touch-in-ios-images/image2.png#lightbox)

您也可以藉由設定 `MultipleTouchEnabled` 屬性，以程式設計方式設定多點觸控，如下列程式程式碼所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要判斷有多少手指觸及螢幕，請使用 [`UITouch`] 屬性上的 [`Count`] 屬性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>判斷觸控位置

方法 `UITouch.LocationInView` 會傳回 CGPoint 物件，它會在指定的視圖中保存觸控的座標。 此外，我們可以藉由呼叫 `Frame.Contains`方法，來測試該位置是否在控制項內。 下列程式碼片段顯示這種情況的範例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

既然我們已瞭解 iOS 中的觸控事件，讓我們來瞭解手勢辨識器。

## <a name="gesture-recognizers"></a>手勢辨識器

手勢辨識器可大幅簡化並減少程式設計工作，以支援應用程式中的觸控。 iOS 手勢辨識器會將一系列的觸控事件匯總成單一觸控事件。

在下列內建手勢辨識器中，Xamarin 會提供類別 `UIGestureRecognizer` 做為基類：

- *UITapGestureRecognizer* –這適用于一或多個分次。
- *UIPinchGestureRecognizer* –捏合並散佈一手指。
- *UIPanGestureRecognizer* –移動或拖曳。
- *UISwipeGestureRecognizer* –以任何方向刷。
- *UIRotationGestureRecognizer* –以順時針或逆時針的運動旋轉兩個手指。
- *UILongPressGestureRecognizer* –按住，有時稱為長按或按一下。

使用筆勢辨識器的基本模式如下所示：

1. 具現**化手勢辨識器**–先將 `UIGestureRecognizer` 子類別具現化。 已具現化的物件將會由視圖建立關聯，並會在處置此視圖時進行垃圾收集。 您不需要將此視圖建立為類別層級變數。
1. **設定任何手勢設定**-下一個步驟是設定手勢辨識器。 如需可設定以控制 `UIGestureRecognizer` 實例行為的屬性清單，請參閱 Xamarin 的 `UIGestureRecognizer` 及其子類別的檔。
1. 設定**目標**–因為其目標-C 遺產，所以當手勢辨識器與手勢相符時，Xamarin 不會引發事件。  `UIGestureRecognizer` 有一個方法– `AddTarget` –可以接受匿名委派或目標 C 選取器，其中包含筆勢辨識器進行比對時所要執行的程式碼。
1. **啟用手勢辨識器**–就像使用觸控事件一樣，只有在啟用觸控互動時才會辨識手勢。
1. **將手勢辨識器新增至視圖**–最後一個步驟是藉由呼叫 `View.AddGestureRecognizer`，並將手勢辨識器物件傳遞至視圖，以將手勢新增至 view。

如需如何在程式碼中執行的詳細資訊，請參閱[手勢辨識器範例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)。

呼叫手勢的目標時，會將所發生之手勢的參考傳遞給它。 這可讓手勢目標取得所發生之手勢的相關資訊。 可用資訊的範圍取決於所使用的手勢辨識器類型。 如需每個 `UIGestureRecognizer` 子類別之可用資料的相關資訊，請參閱 Xamarin 的檔。

請務必記住，一旦將手勢辨識器新增至視圖之後，視圖（以及其下的任何視圖）就不會收到任何觸控事件。 若要同時允許觸控事件與手勢，`CancelsTouchesInView` 屬性必須設定為 false，如下列程式碼所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每個 `UIGestureRecognizer` 都有 State 屬性，可提供筆勢辨識器狀態的重要資訊。 每次此屬性的值變更時，iOS 會呼叫訂閱者法以提供更新。 如果自訂手勢辨識器永遠不會更新狀態屬性，則永遠不會呼叫「訂閱者」，而呈現手勢辨識器毫無用處。

手勢可以摘要為下列兩種類型的其中一種：

1. *離散*–這些手勢只會在第一次被辨識時引發。
1. *連續*–這些手勢會繼續引發，只要它們被辨識即可。

筆勢辨識器存在下列其中一種狀態：

- *可能*–這是所有手勢辨識器的初始狀態。 這是 State 屬性的預設值。
- *開始*-第一次辨識連續手勢時，狀態會設定為 [已開始]。 這讓訂閱能夠區別手勢辨識開始和變更的時間。
- *已變更*–連續手勢開始但尚未完成之後，只要觸控移動或變更時，狀態就會設定為變更，前提是它仍然在筆勢的預期參數內。
- 已*取消*–如果辨識器來自開始變更，則會設定此狀態，然後觸控會變更，使其不再符合手勢的模式。
- 已*辨識–當*手勢辨識器符合一組觸控時，將會設定狀態，並通知訂閱者筆勢已完成。
- 已*結束*–這是可辨識狀態的別名。
- *Failed* –當手勢辨識器無法再符合它正在接聽的觸控時，狀態會變更為 [失敗]。

[Xamarin] 代表 `UIGestureRecognizerState` 列舉中的這些值。

## <a name="working-with-multiple-gestures"></a>使用多個手勢

根據預設，iOS 不允許同時執行預設手勢。 相反地，每個手勢辨識器都會以不具決定性的順序接收觸控事件。 下列程式碼片段說明了如何讓手勢辨識器同時執行：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

您也可以停用 iOS 中的手勢。 有兩個委派屬性可讓手勢辨識器檢查應用程式的狀態和目前的觸控事件，以決定是否應該辨識手勢。 這兩個事件如下：

1. *ShouldReceiveTouch* –在將觸控事件傳遞至手勢辨識器之前，會呼叫此委派，並讓您有機會檢查觸控，並決定手勢辨識器會處理哪些觸控。
1. *ShouldBegin* –當辨識器嘗試將狀態從可能變更為其他狀態時呼叫。 傳回 false 會強制手勢辨識器的狀態變更為 Failed。

您可以使用強型別 `UIGestureRecognizerDelegate`、弱式委派或透過事件處理常式語法系結來覆寫這些方法，如下列程式碼片段所示：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最後，您可以將手勢辨識器排入佇列，使其只有在其他手勢辨識器失敗時才會成功。 例如，只有在按兩下手勢辨識器失敗時，才會成功執行單一點按手勢辨識器。 下列程式碼片段提供一個範例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>建立自訂手勢

雖然 iOS 提供一些預設手勢辨識器，但在某些情況下可能需要建立自訂手勢辨識器。 建立自訂手勢辨識器包含下列步驟：

1. 子類別 `UIGestureRecognizer`。
1. 覆寫適當的觸控事件方法。
1. 透過基類的 State 屬性，將辨識狀態反升。

在[iOS 中使用觸控](ios-touch-walkthrough.md)逐步解說會涵蓋這種情況的實際範例。
