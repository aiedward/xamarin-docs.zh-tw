---
title: 逐步解說：在 Xamarin 中使用觸控
description: 本檔說明如何處理 Xamarin iOS 應用程式中的觸控、討論範例觸控互動、手勢辨識器和自訂手勢辨識器。
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 022602c50386017b178672e20e3e352345feec0b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767198"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>逐步解說：在 Xamarin 中使用觸控

本逐步解說示範如何撰寫程式碼來回應不同種類的觸控事件。 每個範例都包含在另一個畫面中：

- [觸控範例](#Touch_Samples)–如何回應觸控事件。
- [手勢辨識器範例](#Gesture_Recognizer_Samples)–如何使用內建手勢辨識器。
- [自訂手勢辨識器範例](#Custom_Gesture_Recognizer)–如何建立自訂手勢辨識器。

每一節都包含從頭開始撰寫程式碼的指示。
[起始範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)已包含完整的分鏡腳本和功能表畫面：

 [![](ios-touch-walkthrough-images/image3.png "範例包含功能表畫面")](ios-touch-walkthrough-images/image3.png#lightbox)

請依照下列指示將程式碼新增至腳本，並瞭解 iOS 中可用的各種觸控事件種類。 或者，開啟[已完成的範例](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)，以查看所有運作中的專案。

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>觸控範例

在此範例中，我們將示範一些觸控 Api。 請遵循下列步驟來新增執行觸控事件所需的程式碼：

1. 開啟 [專案**Touch_Start**]。 首先執行專案，確定一切都沒問題，並觸及**觸控範例**按鈕。 您應該會看到類似下面的畫面（雖然沒有任何按鈕可以使用）：

    [![](ios-touch-walkthrough-images/image4.png "使用非作用中按鈕執行的範例應用程式")](ios-touch-walkthrough-images/image4.png#lightbox)

1. 編輯檔案**TouchViewController.cs** ，並將下列兩個執行個體變數新增至類別`TouchViewController`：

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```

1. `TouchesBegan`執行方法，如下列程式碼所示：

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    這個方法的運作方式是檢查`UITouch`物件，如果存在，則根據觸控發生的位置執行一些動作：

    - _TouchImage 內部_–在標籤`Touches Began`中顯示文字並變更影像。
    - _內部 DoubleTouchImage_ –如果筆勢是按兩下，則變更所顯示的影像。
    - _內部 DragImage_ –設定旗標，表示已開始觸控。 方法`TouchesMoved`會使用此旗標來判斷是否`DragImage`應該在畫面周圍移動，如我們在下一個步驟中所見。

    上述程式碼只會處理個別的觸控，如果使用者在螢幕上移動手指，仍然沒有任何行為。 若要回應移動，請`TouchesMoved`執行，如下列程式碼所示：

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    這個方法會取得`UITouch`物件，然後檢查以查看觸控發生的位置。 如果在中`TouchImage`發生觸控，則會在畫面上顯示文字觸控已移動的。 

    如果`touchStartedInside`為 true，則我們知道使用者的手指在上`DragImage` ，並將其移動。 當使用者將手指`DragImage`移到螢幕上時，程式碼會隨之移動。

1. 我們需要處理使用者從螢幕中提起手指，或 iOS 取消觸控事件的情況。 為此，我們將會`TouchesEnded`執行`TouchesCancelled` ，如下所示：

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```

    這兩種方法都會將`touchStartedInside`旗標重設為 false。 `TouchesEnded`也會顯示`TouchesEnded`在畫面上。

1. 此時，[Touch 範例] 畫面已完成。 請注意，當您與每個影像互動時，畫面會如何改變，如下列螢幕擷取畫面所示：

    [![](ios-touch-walkthrough-images/image4.png "啟動應用程式畫面")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "使用者拖曳按鈕之後的畫面")](ios-touch-walkthrough-images/image5.png#lightbox)

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>手勢辨識器範例

[上一節](#Touch_Samples)示範如何使用觸控事件，在螢幕上拖曳物件。
在本節中，我們將會清除觸控事件，並示範如何使用下列筆勢辨識器：

- ， `UIPanGestureRecognizer`用於在螢幕周圍拖曳影像。
- 要`UITapGestureRecognizer`對螢幕上的按兩下進行回應的。

如果您執行[起始範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)，並按一下 [**筆勢辨識器範例**] 按鈕，您應該會看到下列畫面：

 [![](ios-touch-walkthrough-images/image6.png "按一下 [手勢辨識器範例] 按鈕會顯示此畫面")](ios-touch-walkthrough-images/image6.png#lightbox)

請遵循下列步驟來執行手勢辨識器：

1. 編輯檔案**GestureViewController.cs** ，並新增下列執行個體變數：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    我們需要這個執行個體變數來追蹤影像先前的位置。
「移動流覽手勢辨識器」 `originalImageFrame`會使用值來計算在螢幕上重繪影像所需的位移。

1. 將下列方法新增至控制器：

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    此程式碼會`UIPanGestureRecognizer`具現化實例，並將它加入至 view。
請注意，我們會以方法`HandleDrag`的形式將目標指派給手勢–在下一個步驟中會提供這個方法。

1. 若要執行 HandleDrag，請將下列程式碼新增至控制器：

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    上述程式碼會先檢查手勢辨識器的狀態，然後在螢幕周圍移動影像。 當此程式碼準備好時，控制器現在可以支援在螢幕上拖曳一個影像。

1. `UITapGestureRecognizer`新增，它會變更顯示在 DoubleTouchImage 中的影像。 將下列方法新增至`GestureViewController`控制器：

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    此程式碼與的程式碼`UIPanGestureRecognizer`非常類似，但不會針對我們`Action`使用的目標使用委派。 

1. 我們需要做的最後一件事是`ViewDidLoad`修改，讓它呼叫剛才新增的方法。 變更 ViewDidLoad，使它類似下列程式碼：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    也請注意，我們會初始化的值`originalImageFrame`。

1. 執行應用程式，並與這兩個影像互動。
下列螢幕擷取畫面是這些互動的其中一個範例：
    
    [![](ios-touch-walkthrough-images/image7.png "這個螢幕擷取畫面顯示拖曳互動")](ios-touch-walkthrough-images/image7.png#lightbox)

<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>自訂手勢辨識器

在本節中，我們將套用先前各節中的概念，以建立自訂手勢辨識器。 自訂手勢辨識器將`UIGestureRecognizer`會子類別，並會在使用者于螢幕上繪製 "V"，然後切換點陣圖時發生。 下列螢幕擷取畫面是此畫面的範例：

 [![](ios-touch-walkthrough-images/image8.png "當使用者在螢幕上繪製 ' V ' 時，應用程式會辨識")](ios-touch-walkthrough-images/image8.png#lightbox)

請遵循下列步驟來建立自訂手勢辨識器：

1. 將新類別新增至名為`CheckmarkGestureRecognizer`的專案，使其看起來類似下列程式碼：

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    當`State`屬性變更`Recognized`為或`Ended`時，會呼叫 Reset 方法。 這是重設自訂手勢辨識器中所設定之任何內部狀態的時間。
現在，類別可以在使用者下一次與應用程式互動時開始，並且準備好重新嘗試識別手勢。

1. 既然我們已定義自訂手勢辨識器（`CheckmarkGestureRecognizer`），請編輯**CustomGestureViewController.cs**檔案，並新增下列兩個執行個體變數：

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. 若要具現化並設定我們的手勢辨識器，請將下列方法新增至控制器：

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. 編輯`ViewDidLoad` ，使其呼叫`WireUpCheckmarkGestureRecognizer`，如下列程式碼片段所示：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. 執行應用程式，並嘗試在畫面上繪製「V」。 您應該會看到影像顯示為 [變更]，如下列螢幕擷取畫面所示：
    
    [![](ios-touch-walkthrough-images/image9.png "已核取按鈕")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "未核取此按鈕")](ios-touch-walkthrough-images/image10.png#lightbox)

上述三節示範了在 iOS 中回應觸控事件的不同方式：使用觸控事件、內建手勢辨識器，或使用自訂手勢辨識器。

## <a name="related-links"></a>相關連結

- [iOS 觸控啟動（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS 觸控最終（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
