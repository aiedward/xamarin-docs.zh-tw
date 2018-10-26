---
title: 逐步解說： 在 Xamarin.iOS 中使用觸控
description: 本文件說明如何處理在 Xamarin.iOS 應用程式，討論範例觸控互動、 筆勢辨識器，以及自訂的筆勢辨識器的觸控。
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: bff4d46ac9d5fe893cbb0a2dfa032e1b9f6daa0e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121551"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>逐步解說： 在 Xamarin.iOS 中使用觸控

本逐步解說示範如何撰寫回應觸控事件的不同種類的程式碼。 每個範例被包含在個別的畫面：

- [Touch 範例](#Touch_Samples)– 如何回應觸控事件。
- [筆勢辨識器範例](#Gesture_Recognizer_Samples)– 如何使用內建的筆勢辨識器。
- [自訂的筆勢辨識器範例](#Custom_Gesture_Recognizer)– 如何建置自訂的筆勢辨識器。

每個區段包含從頭開始撰寫程式碼的指示。
[啟動範例程式碼](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)已經包含完整的分鏡腳本和功能表螢幕：

 [![](ios-touch-walkthrough-images/image3.png "此範例包含礹畫惎")](ios-touch-walkthrough-images/image3.png#lightbox)

請遵循以下指示以將程式碼新增到分鏡腳本，並了解不同類型的可用在 iOS 中觸控事件。 或者，開啟[完成的範例](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)若要查看使用的所有項目。

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>觸控式範例

在此範例中，我們將示範一些觸控 Api。 請遵循下列步驟來新增實作觸控事件所需的程式碼：


1. 開啟專案**Touch_Start**。 執行專案的第一個以確定一切都沒問題，與觸控**Touch 範例** 按鈕。 （雖然任何按鈕都可以），您會看到類似下面的畫面：
    
    [![](ios-touch-walkthrough-images/image4.png "範例應用程式執行與非工作按鈕")](ios-touch-walkthrough-images/image4.png#lightbox)


1. 編輯檔案**TouchViewController.cs**並將下列兩個執行個體變數新增至類別`TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. 實作`TouchesBegan`方法，如下列程式碼所示：

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
    
    這個方法的運作方式是檢查`UITouch`物件，以及如果它存在於執行基礎觸控發生某些動作：

    * _內 TouchImage_ – 顯示文字`Touches Began`標籤和變更映像中。
    * _內 DoubleTouchImage_ – 變更點選手勢時所顯示的影像。
    * _內 DragImage_ – 設定旗標，指出已啟動的觸控。 方法`TouchesMoved`會使用這個旗標來判斷如果`DragImage`應在螢幕上移動，因為我們應該會看到下一個步驟中。

    上述程式碼只處理個別的修飾，不仍有任何行為如果使用者於螢幕上移動他們的手指。 若要回應移動，實作`TouchesMoved`如下列程式碼所示：

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

    這個方法會取得`UITouch`物件，然後檢查以查看發生觸控的位置。 在觸控發生`TouchImage`，然後將修飾移動螢幕所顯示的文字。 

    如果`touchStartedInside`為 true，則我們知道，使用者對他們的手指`DragImage`並於四處移動它。 程式碼會移動`DragImage`當使用者移動其手指在螢幕上的。

1. 我們需要處理的情況，當使用者拿起他或她的手指在螢幕或 iOS 取消觸控事件。 為此，我們會實作`TouchesEnded`和`TouchesCancelled`，如下所示：

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
    
    這兩種方法將會重設`touchStartedInside`旗標設為 false。 `TouchesEnded` 也會顯示`TouchesEnded`在螢幕上。

1. 此時 Touch 範例畫面已完成。 請注意如何畫面變更當您互動與每個映像，如下列螢幕擷取畫面所示：
        
    [![](ios-touch-walkthrough-images/image4.png "起始應用程式畫面")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "之後使用者拖曳一個按鈕的螢幕")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>筆勢辨識器範例

[上一節](#Touch_Samples)示範如何將物件拖曳至在螢幕上使用觸控事件。
這一節我們將會去除觸控事件，並示範如何使用下列的筆勢辨識器：

-  `UIPanGestureRecognizer`拖曳影像在螢幕上的。
-  `UITapGestureRecognizer`螢幕上的雙精度浮點點選回應。

如果您執行[啟動程式碼範例](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)，然後按一下**筆勢辨識器範例** 按鈕，您應該會看到下列畫面：

 [![](ios-touch-walkthrough-images/image6.png "按一下 [筆勢辨識器範例] 按鈕以顯示這個畫面")](ios-touch-walkthrough-images/image6.png#lightbox)

請遵循下列步驟來實作筆勢辨識器：


1. 編輯檔案**GestureViewController.cs**並加入下列的執行個體變數：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    我們需要這個執行個體變數，以追蹤的映像先前的位置。
移動瀏覽筆勢辨識器將會使用`originalImageFrame`值來計算重繪螢幕上的映像所需的位移。

1. 將下列方法加入至控制器：

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

    此程式碼會具現化`UIPanGestureRecognizer`執行個體，並將它加入至檢視。
請注意我們將目標指派給方法的表單中的筆勢`HandleDrag`-下一個步驟中，提供這個方法。

1. 若要實作 HandleDrag，請在控制器中加入下列程式碼：

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

    上述程式碼會先檢查 筆勢辨識器的狀態，然後將 在螢幕上的映像。 使用此程式碼就緒之後，控制器現在可以支援拖曳一個映像，在螢幕上。


1. 新增`UITapGestureRecognizer`，會變更 DoubleTouchImage 中所顯示的影像。 將下列方法加入`GestureViewController`控制器：

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

    此程式碼是非常類似的程式碼`UIPanGestureRecognizer`但不使用委派的目標，我們會使用`Action`。 

1. 我們要做的最後一件事就是修改`ViewDidLoad`，因此它會呼叫我們剛新增的方法。 變更 ViewDidLoad，使它類似下列程式碼：

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

    也請注意我們初始化值的`originalImageFrame`。


1. 執行應用程式，並與兩個映像互動。
下列螢幕擷取畫面是這些互動的其中一個範例：
    
    [![](ios-touch-walkthrough-images/image7.png "此螢幕擷取畫面顯示拖放互動")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>自訂的筆勢辨識器

在本節中，我們將會套用的概念來建置自訂的筆勢辨識器上一節中。 自訂的筆勢辨識器將子類別`UIGestureRecognizer`，並會在畫面上，當使用者繪製"V"辨識然後切換點陣圖。 下列螢幕擷取畫面是這個畫面的範例：

 [![](ios-touch-walkthrough-images/image8.png "當使用者在螢幕上繪製 'V' 時，會辨識應用程式")](ios-touch-walkthrough-images/image8.png#lightbox)

請遵循下列步驟來建立自訂的筆勢辨識器：


1. 將新類別加入專案，命名為`CheckmarkGestureRecognizer`，並使它看起來像下列程式碼：

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

    重設方法時，會呼叫`State`屬性變更為其中一個`Recognized`或`Ended`。 這是重設任何在自訂的筆勢辨識器中設定的內部狀態的時間。
現在類別可以重頭開始下一次使用者所互動的應用程式，並準備好重新嘗試辨識的筆勢。



1. 既然我們已經定義了自訂的筆勢辨識器 (`CheckmarkGestureRecognizer`) 編輯**CustomGestureViewController.cs**檔案，並新增下列兩個執行個體變數：

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. 若要具現化，並設定我們的筆勢辨識器，請至控制器中新增下列方法：

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

1. 編輯`ViewDidLoad`使其呼叫`WireUpCheckmarkGestureRecognizer`，如下列程式碼片段所示：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. 執行應用程式，並嘗試在螢幕上畫"V"。 您應該會看到映像所顯示的變更，如下列螢幕擷取畫面所示：
    
    [![](ios-touch-walkthrough-images/image9.png "[檢查] 按鈕")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "取消核取的按鈕")](ios-touch-walkthrough-images/image10.png#lightbox)



上述三個區段示範回應觸控事件在 iOS 中的不同方式： 使用觸控事件，內建的筆勢辨識器，或使用自訂的筆勢辨識器。



## <a name="related-links"></a>相關連結

- [iOS 觸控開始 （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS 觸控最終 （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
