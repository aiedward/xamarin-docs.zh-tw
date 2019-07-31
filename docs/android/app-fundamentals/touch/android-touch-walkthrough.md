---
title: 逐步解說-在 Android 中使用觸控
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: 78878e62a36e3f6dd6ca3c7fcfb6413da4f0e0f9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644128"
---
# <a name="walkthrough---using-touch-in-android"></a>逐步解說-在 Android 中使用觸控

讓我們瞭解如何使用上一節的實用應用程式中的概念。 我們會建立具有四個活動的應用程式。 第一個活動會是功能表或切換開關, 會啟動其他活動以示範各種 Api。 下列螢幕擷取畫面顯示主要活動:

[![[Touch Me] 按鈕的範例螢幕擷取畫面](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一個活動 [Touch Sample] 將示範如何使用事件處理常式來觸及 Views。 手勢辨識器活動將示範如何子類別`Android.View.Views`和處理事件, 以及示範如何處理縮小的手勢。 第三個和最後一個活動 (**自訂手勢**) 將會顯示如何使用自訂手勢。 為了方便追蹤和吸收, 我們會將此逐步解說分解成幾個章節, 而每個章節都著重于其中一個活動。

## <a name="touch-sample-activity"></a>觸控範例活動

-   開啟 [專案**TouchWalkthrough\_** ] [啟動]。 **MainActivity**全都設定為 go &ndash; , 可讓我們在活動中執行觸控行為。 如果您執行應用程式, 並按一下 [ **Touch Sample**], 則應該啟動下列活動:

    [![觸控開始顯示的活動螢幕擷取畫面](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   既然我們已確認活動已啟動, 請開啟檔案**TouchActivity.cs** , 並新增的`Touch`事件`ImageView`處理常式:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   接下來, 將下列方法新增至**TouchActivity.cs**:

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

請注意, 上述程式碼中, 我們`Move`會`Down`將和動作視為相同。 這是因為即使使用者可能不會把手指移開`ImageView`, 它還是可能會改變, 或是使用者所行使的壓力。 這些類型的變更會產生一個`Move`動作。

每次使用者接觸`ImageView`時`Touch` , 就會引發事件, 而我們的處理常式會在螢幕上顯示「**觸控**」訊息, 如下列螢幕擷取畫面所示:

[![開始觸控的活動螢幕擷取畫面](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要使用者觸及`ImageView`, 就會在中`TextView`顯示**觸控開始**。 當使用者不再觸及`ImageView`時, 訊息**觸控結束**將會顯示在中`TextView`, 如下列螢幕擷取畫面所示:

[![觸控端點活動的螢幕擷取畫面](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>手勢辨識器活動

現在可讓您執行手勢辨識器活動。 此活動將示範如何在畫面周圍拖曳視圖, 並說明執行縮小至縮放的一種方式。

-   將新活動新增至名`GestureRecognizer`為的應用程式。
    編輯此活動的程式碼, 使其類似下列程式碼:

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

-   將新的 Android view 新增至專案, 並將其`GestureRecognizerView`命名為。 將下列變數新增至此類別:

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

-   將下列函式新增`GestureRecognizerView`至。 此函式會將`ImageView`新增至我們的活動。 此時, 程式碼仍不會編譯&ndash; , 我們必須建立類別`MyScaleListener` , 協助您在使用者 pinches `ImageView`時, 調整其大小:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   若要在活動上繪製影像, 我們需要覆寫`OnDraw` View 類別的方法, 如下列程式碼片段所示。 此程式碼會將`ImageView`移至`_posX`和所指定的`_posY`位置, 並根據縮放比例調整影像大小:

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

-   接下來, 我們需要在使用者`_scaleFactor` `ImageView`pinches 時更新執行個體變數。 我們會新增名`MyScaleListener`為的類別。 此類別會接聽當使用者 pinches `ImageView`時, 由 Android 所引發的調整事件。
    將下列內部類別加入至`GestureRecognizerView`。 這個類別是`ScaleGesture.SimpleOnScaleGestureListener`。 當您對手勢的子集感興趣時, 這個類別是一個方便的類別, 接聽程式可以子類別:

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

-   下一個我們需要在中`GestureRecognizerView`覆寫的方法是。 `OnTouchEvent` 下列程式碼會列出這個方法的完整執行。 這裡有很多程式碼, 因此讓我們花一分鐘的時間, 看一下這裡會發生什麼事。 這個方法所做的第一件事是, 視&ndash;需要調整圖示, 這`_scaleDetector.OnTouchEvent`是藉由呼叫來處理。 接下來, 我們會試著找出所謂的動作:

    - 如果使用者觸及螢幕, 我們會記錄 X 和 Y 位置, 以及觸及螢幕的第一個指標識別碼。

    - 如果使用者在畫面上移動其觸控, 則我們會找出使用者移動指標的距離。

    - 如果使用者已將其手指向外走, 則會停止追蹤手勢。

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

-   現在執行應用程式, 並啟動手勢辨識器活動。
    當它啟動時, 畫面看起來應該像下面的螢幕擷取畫面:

    [![具有 Android 圖示的手勢辨識器開始畫面](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   現在觸碰圖示, 然後將它拖曳到畫面周圍。 嘗試縮小縮放手勢。 在某些時候, 您的畫面看起來可能像下列螢幕擷取畫面:

    [![畫面周圍的手勢移動圖示](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此時, 您應該為自己提供一位 pat: 您剛在 Android 應用程式中執行縮小至縮放功能! 請快速地中斷, 並使用自訂手勢來繼續進行本逐步&ndash;解說中的第三個和最後一個活動。

## <a name="custom-gesture-activity"></a>自訂手勢活動

本逐步解說中的最後一個畫面會使用自訂手勢。

基於本逐步解說的目的, 筆勢程式庫已經使用手勢工具建立, 並新增至檔案**資源/原始/手勢**中的專案。 在此過程中, 我們將在逐步解說中取得最後一個活動。

-   將名為「**自訂\_手勢\_配置. axml** 」的配置檔案新增至具有下列內容的專案。 專案已經有**Resources**資料夾中的所有影像:

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

-   接下來, 將新的活動加入至專案, `CustomGestureRecognizerActivity.cs`並將其命名為。 將兩個執行個體變數新增至類別, 如下列兩行程式碼所示:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   編輯此活動的方法,讓它類似下列程式碼。`OnCreate` 讓我們花一點時間來說明這段程式碼中會發生什麼事。 我們所做的第一件事`GestureOverlayView`是將具現化, 並將其設定為活動的根視圖。
    我們也會將事件處理常式指派`GesturePerformed`給的`GestureOverlayView`事件。 接下來, 我們會擴充稍早建立的配置檔案, 並將它加入做為的`GestureOverlayView`子視圖。 最後一個步驟是初始化變數`_gestureLibrary` , 並從應用程式資源載入手勢檔案。 如果因為某些原因而無法載入手勢檔案, 此活動不會有太多, 所以會關閉:

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

-   我們需要做的最後一件事就是`GestureOverlayViewOnGesturePerformed`執行方法, 如下列程式碼片段所示。 `GestureOverlayView`當偵測到手勢時, 它會回呼到這個方法。 第一件事是嘗試藉由`IList<Prediction>`呼叫`_gestureLibrary.Recognize()`來取得符合手勢的物件。 我們使用一些 LINQ 來取得`Prediction`筆勢最高分的。

    如果沒有符合的手勢具有足夠的分數, 事件處理常式就會結束, 而不會執行任何動作。 否則, 我們會檢查預測的名稱, 並根據手勢的名稱變更要顯示的影像:

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

-   執行應用程式, 並啟動 [自訂手勢辨識器] 活動。 看起來應該如下列螢幕擷取畫面所示:

    [![具有 [簽入] 影像的螢幕擷取畫面](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    現在在畫面上繪製一個核取記號, 而顯示的點陣圖看起來應該像下一個螢幕擷取畫面所示:

    [![已繪製核取記號, 已辨識核取記號](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    最後, 在畫面上繪製一個塗抹。 此核取方塊應該會變更回其原始影像, 如下列螢幕擷取畫面所示:

    [![螢幕上的塗抹, 顯示原始影像](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

您現在已瞭解如何使用 Xamarin 來整合 Android 應用程式中的觸控和手勢。


## <a name="related-links"></a>相關連結

- [Android Touch 開始 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
