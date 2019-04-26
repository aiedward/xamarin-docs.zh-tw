---
title: 逐步解說-在 Android 中使用觸控
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: c4192f22ebd0ad1cde27745f5439c2d18a268ed3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012431"
---
# <a name="walkthrough---using-touch-in-android"></a>逐步解說-在 Android 中使用觸控

讓我們了解如何使用上一節中可用的應用程式的概念。 我們將建立應用程式，使用下列四個活動。 功能表或切換表單，將會啟動其他活動，以示範各種 Api，將會第一個活動。 下列螢幕擷取畫面顯示的主要活動：

[![範例螢幕擷取畫面與觸控我按鈕](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一個活動，也就是觸控式的範例中，將示範如何使用事件處理常式，如碰觸的檢視。 筆勢辨識器活動將示範如何以子類別`Android.View.Views`和處理事件，以及示範如何處理捏合手勢。 第三個和最後一個活動，**自訂筆勢**，將示範如何使用自訂的筆勢。 若要讓事情容易遵循，並吸收，我們將分成本逐步解說幾節中，將焦點放在其中一個活動的每個部分。

## <a name="touch-sample-activity"></a>觸控範例活動

-   開啟專案**TouchWalkthrough\_啟動**。 **MainActivity**已就緒，可移&ndash;則由我們實作活動中的觸控行為。 如果您執行應用程式，然後按一下**Touch 範例**，應該啟動的下列活動：

    [![活動的使用觸控開始顯示螢幕擷取畫面](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   既然我們已確認活動啟動，請開啟檔案**TouchActivity.cs**並加入的處理常式`Touch`事件的`ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   接下來，新增下列方法加入**TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

請注意，在上述程式碼對待`Move`和`Down`視為相同的動作。 這是因為即使使用者可能不提起他們的手指`ImageView`，它可能會移動，或可能會變更使用者所施加的壓力。 這些類型的變更將會產生`Move`動作。

每次使用者修飾`ImageView`，則`Touch`就會引發事件，並會顯示訊息，我們的處理常式**觸控開始**在畫面上，如下列螢幕擷取畫面所示：

[![活動的使用觸控開始螢幕擷取畫面](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要在使用者觸碰`ImageView`， **Touch 開始**將會顯示在`TextView`。 當使用者不再觸碰`ImageView`，訊息**Touch 結束**將會顯示在`TextView`，如下列螢幕擷取畫面所示：

[![活動的螢幕擷取畫面，與觸控結束](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>筆勢辨識器活動

現在可讓實作筆勢辨識器的活動。 此活動將示範如何將檢視在螢幕上的拖曳，並說明實作縮小-放大一種方式。

-   將新的活動加入至呼叫的應用程式`GestureRecognizer`。
    編輯此活動的程式碼，使它類似下列程式碼：

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   加入新的 Android 專案中，檢視並將它命名`GestureRecognizerView`。 這個類別中加入下列變數：

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   新增下列建構函式來`GestureRecognizerView`。 這個建構函式會將新增`ImageView`至我們的活動。 此時程式碼仍將不會編譯&ndash;我們要建立類別`MyScaleListener`，將幫助您調整大小`ImageView`使用者當 pinches 它：

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   若要在我們的活動上繪製影像，我們需要覆寫`OnDraw`檢視類別，如下列程式碼片段所示的方法。 此程式碼會移動`ImageView`所指定的位置`_posX`和`_posY`也做為調整大小的縮放比例根據映像：

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   接下來我們需要更新的執行個體變數`_scaleFactor`使用者身分 pinches `ImageView`。 我們會新增一個叫做類別`MyScaleListener`。 這個類別會接聽擴展事件，當使用者 pinches 會引發由 Android `ImageView`。
    新增下列內部類別`GestureRecognizerView`。 這個類別是`ScaleGesture.SimpleOnScaleGestureListener`。 這個類別是接聽程式可以子類別，當您感興趣的筆勢子集的方便類別：

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

-   我們要覆寫中的下一個方法`GestureRecognizerView`是`OnTouchEvent`。 下列程式碼會列出這個方法的完整實作。 有很多程式碼，因此就讓我們花點時間，並尋找以下情形。 這個方法會執行第一件事是調整圖示，視&ndash;這由呼叫`_scaleDetector.OnTouchEvent`。 接下來，我們試著找出哪些動作會呼叫這個方法：

    - 如果使用者接觸到的畫面，我們會記錄的 X 和 Y 位置和接觸到螢幕的第一個指標的識別碼。

    - 如果使用者在螢幕上移動其觸控，我們找出使用者移動滑鼠指標的幅度。

    - 如果使用者具有提高他的手指在螢幕，我們將會停止追蹤的筆勢。

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   現在，執行應用程式，並啟動筆勢辨識器的活動。
    啟動時的畫面應該看起來像下列螢幕擷取畫面：

    [![筆勢辨識器 Android 圖示與啟動畫面](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   現在觸圖示，並將它拖曳畫面。 請嘗試縮小-放大手勢。 在某個時間點您的畫面可能看起來像下列螢幕擷取畫面：

    [![在螢幕上的手勢移動圖示](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此時您應該提供您自己 pat 背面： 您只在 Android 應用程式中實作捏合以縮放 ！ 快速休息一下，讓 移至本逐步解說的第三個也是最後一個活動&ndash;使用自訂的筆勢。

## <a name="custom-gesture-activity"></a>自訂動作活動

在此逐步解說中的最後一個畫面將會使用自訂的筆勢。

基於本逐步解說的目的，手勢 library 已使用筆勢工具建立並新增至檔案中的專案**資源/原始/筆勢**。 使用此位元的例行性的方式，可讓開始本逐步解說中的最後一個活動。

-   新增名為的版面配置檔案**自訂\_筆勢\_layout.axml**到專案中下列內容。 這個專案已經有的所有映像**資源**資料夾：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   接下來將新的活動加入至專案並將它命名`CustomGestureRecognizerActivity.cs`。 類別中加入兩個執行個體變數，如下列兩行程式碼中：

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   編輯`OnCreate`方法，這個活動中，讓它類似下列的程式碼。 可讓花點時間說明情形在此程式碼。 我們所做的第一件事是具現化`GestureOverlayView`並將其設定為 [根] 檢視的活動。
    我們也將指定的事件處理常式`GesturePerformed`事件的`GestureOverlayView`。 接下來我們擴大稍早建立的配置檔案，並將它加入為的子檢視`GestureOverlayView`。 最後一個步驟是將變數初始化`_gestureLibrary`，並從應用程式資源載入筆勢檔案。 如果基於某些原因無法載入筆勢檔案，不多可以執行這項活動，因此很關機：

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   我們要實作此方法的最後一件事`GestureOverlayViewOnGesturePerformed`如下列程式碼片段所示。 當`GestureOverlayView`偵測到筆勢，它會呼叫回至這個方法。 我們嘗試取得第一件事`IList<Prediction>`物件，而且藉由呼叫符合筆勢`_gestureLibrary.Recognize()`。 我們使用的 LINQ 來取得`Prediction`具有最高分數的筆勢。

    如果是沒有相符的軌跡具有高，因此足夠的分數，則事件處理常式會結束而不執行任何動作。 否則我們會檢查預測的名稱，並變更所顯示的影像根據筆勢的名稱：

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   執行應用程式，並啟動自訂筆勢辨識器的活動。 它看起來應該類似下列螢幕擷取畫面：

    [![螢幕擷取畫面與檢查我映像](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    現在在畫面上，繪製核取記號，並顯示點陣圖應該看起來像下一步 的螢幕擷取畫面所示：

    [![繪製核取記號，核取記號會辨識](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    最後，在螢幕上繪製徒手畫。 核取方塊應該變更回其原始映像，如下列螢幕擷取畫面所示：

    [![會顯示在畫面上，原始的映像的 scribble](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

您現在有了解如何整合觸控和筆勢使用 Xamarin.Android 的 Android 應用程式中。


## <a name="related-links"></a>相關連結

- [Android 觸控開始 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 觸控最終 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
