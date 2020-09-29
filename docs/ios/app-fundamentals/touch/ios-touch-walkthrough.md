---
title: 逐步解說：在 Xamarin 中使用觸控
description: 本檔說明如何處理 Xamarin iOS 應用程式中的觸控、討論範例觸控互動、手勢辨識器和自訂手勢辨識器。
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 3d296c36febfb5671c816372aa97661494179b83
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433907"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>逐步解說：在 Xamarin 中使用觸控

本逐步解說示範如何撰寫程式碼，以回應不同種類的觸控事件。 每個範例都包含在不同的畫面中：

- [觸控範例](#Touch_Samples) –如何回應觸控事件。
- [手勢辨識器範例](#Gesture_Recognizer_Samples) –如何使用內建的手勢辨識器。
- [自訂手勢辨識器範例](#Custom_Gesture_Recognizer) –如何建立自訂手勢辨識器。

每一節都包含從頭撰寫程式碼的指示。

請遵循下列指示，將程式碼新增至腳本，並瞭解 iOS 中可用的不同觸控事件種類。 或者，開啟 [完成的範例](/samples/xamarin/ios-samples/applicationfundamentals-touch-final) 以查看一切運作正常。

<a name="Touch_Samples"></a>

## <a name="touch-samples"></a>觸控範例

在此範例中，我們將示範一些觸控 Api。 請遵循下列步驟來新增執行觸控事件所需的程式碼：

1. 開啟專案 **Touch_Start**。 首先，執行專案以確定一切都正常，並接觸 [ **觸控範例** ] 按鈕。 您應該會看到類似下列 (的畫面，不過沒有任何按鈕可運作) ：

    [![使用非工作按鈕執行的範例應用程式](ios-touch-walkthrough-images/image4.png)](ios-touch-walkthrough-images/image4.png#lightbox)

1. 編輯檔案 **TouchViewController.cs** ，並將下列兩個執行個體變數新增至類別 `TouchViewController` ：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```

1. 執行 `TouchesBegan` 方法，如下列程式碼所示：

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

    這種方法的運作方式是檢查 `UITouch` 物件，如果存在，則會根據觸控發生的位置來執行某些動作：

    - _TouchImage 內_ –在 `Touches Began` 標籤中顯示文字並變更影像。
    - _內部 DoubleTouchImage_ –如果手勢是點兩下，請變更顯示的影像。
    - _在 DragImage 內_ –設定旗標，指出已開始觸控。 方法 `TouchesMoved` 會使用此旗標來判斷是否 `DragImage` 應該在畫面周圍移動，如我們在下一個步驟中所見。

    上述程式碼只會處理個別的觸控，如果使用者在螢幕上移動手指，仍不會有任何行為。 若要回應移動，請依照 `TouchesMoved` 下列程式碼所示執行：

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

    這個方法會取得 `UITouch` 物件，然後檢查以查看觸控發生的位置。 如果發生觸控，則 `TouchImage` 會在畫面上顯示已移動的文字。

    如果 `touchStartedInside` 是 true，則我們知道使用者有手指， `DragImage` 並且正在移動它。 `DragImage`當使用者在螢幕上移動手指時，程式碼將會移動。

1. 當使用者將手指移到螢幕上，或 iOS 取消觸控事件時，我們需要處理這種情況。 為此，我們將會執行 `TouchesEnded` ， `TouchesCancelled` 如下所示：

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

    這兩種方法都會將旗標重設 `touchStartedInside` 為 false。 `TouchesEnded` 也會顯示 `TouchesEnded` 在螢幕上。

1. 此時，[觸控範例] 畫面已完成。 請注意，當您與每個影像互動時，畫面會如何改變，如下列螢幕擷取畫面所示：

    [![啟動應用程式畫面](ios-touch-walkthrough-images/image4.png)](ios-touch-walkthrough-images/image4.png#lightbox)

    [![使用者拖曳按鈕之後的畫面](ios-touch-walkthrough-images/image5.png)](ios-touch-walkthrough-images/image5.png#lightbox)

<a name="Gesture_Recognizer_Samples"></a>

## <a name="gesture-recognizer-samples"></a>手勢辨識器範例

[上一節](#Touch_Samples)示範如何使用觸控事件將物件拖曳到螢幕周圍。
在本節中，我們將消除觸控事件，並示範如何使用下列手勢辨識器：

- ， `UIPanGestureRecognizer` 用於在螢幕周圍拖曳影像。
- `UITapGestureRecognizer`要回應按兩下畫面上的。

遵循下列步驟來執行手勢辨識器：

1. 編輯 **GestureViewController.cs** 檔案，並新增下列執行個體變數：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    我們需要此執行個體變數來追蹤影像的先前位置。
平移手勢辨識器會使用此 `originalImageFrame` 值來計算在螢幕上重新繪製影像所需的位移。

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

    此程式碼會具現化 `UIPanGestureRecognizer` 實例，並將它加入至 view。
請注意，我們會以方法的形式將目標指派給手勢 `HandleDrag` –此方法會在下一個步驟中提供。

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

    上述程式碼會先檢查手勢辨識器的狀態，然後在畫面周圍移動影像。 使用此程式碼時，控制器現在可支援在螢幕上拖曳一個影像。

1. 新增 `UITapGestureRecognizer` ，它會變更要在 DoubleTouchImage 中顯示的影像。 將下列方法新增至 `GestureViewController` 控制器：

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

    這段程式碼與的程式碼非常類似， `UIPanGestureRecognizer` 但不會針對我們使用的目標使用委派 `Action` 。

1. 我們需要做的最後一件事是修改， `ViewDidLoad` 讓它呼叫我們剛剛加入的方法。 變更 ViewDidLoad，使它類似下列程式碼：

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

    也請注意，我們會初始化的值 `originalImageFrame` 。

1. 執行應用程式，並與兩個影像互動。
下列螢幕擷取畫面是這些互動的其中一個範例：

    [![此螢幕擷取畫面顯示拖曳互動](ios-touch-walkthrough-images/image7.png)](ios-touch-walkthrough-images/image7.png#lightbox)

<a name="Custom_Gesture_Recognizer"></a>

## <a name="custom-gesture-recognizer"></a>自訂手勢辨識器

在本節中，我們將套用先前章節的概念，以建立自訂的手勢辨識器。 自訂手勢辨識器將會子類別 `UIGestureRecognizer` ，並且會辨識使用者在畫面上繪製 "V"，然後切換點陣圖。 下列螢幕擷取畫面是此畫面的範例：

 [![當使用者在畫面上繪製 ' V ' 時，應用程式會辨識](ios-touch-walkthrough-images/image8.png)](ios-touch-walkthrough-images/image8.png#lightbox)

遵循下列步驟來建立自訂手勢辨識器：

1. 將新類別新增至名為的專案 `CheckmarkGestureRecognizer` ，並使其看起來類似下列程式碼：

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

    當 `State` 屬性變更為或時，會呼叫 Reset 方法 `Recognized` `Ended` 。 這是重設自訂手勢辨識器中任何內部狀態集的時間。
現在，當使用者與應用程式互動時，類別可以開始重新整理，並準備好重新嘗試辨識手勢。

1. 既然我們已定義自訂的手勢辨識器 (`CheckmarkGestureRecognizer`) 編輯 **CustomGestureViewController.cs** 檔案，並新增下列兩個執行個體變數：

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. 若要具現化並設定手勢辨識器，請將下列方法新增至控制器：

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

1. 進行編輯 `ViewDidLoad` ，使其呼叫 `WireUpCheckmarkGestureRecognizer` ，如下列程式碼片段所示：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. 執行應用程式，然後嘗試在畫面上繪製「V」。 您應該會看到顯示的影像變更，如下列螢幕擷取畫面所示：

    [![已核取按鈕](ios-touch-walkthrough-images/image9.png)](ios-touch-walkthrough-images/image9.png#lightbox)

    [![按鈕未核取](ios-touch-walkthrough-images/image10.png)](ios-touch-walkthrough-images/image10.png#lightbox)

上述三個章節示範了不同的方式來回應 iOS 中的觸控事件：使用觸控事件、內建手勢辨識器，或使用自訂手勢辨識器。

## <a name="related-links"></a>相關連結

- [iOS 觸控最終 (範例) ](/samples/xamarin/ios-samples/applicationfundamentals-touch-final)