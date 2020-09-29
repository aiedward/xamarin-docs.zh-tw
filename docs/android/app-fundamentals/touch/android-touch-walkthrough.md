---
title: 逐步解說-在 Android 中使用觸控
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: b7db20c2d51e96de8fcadaf5d50627132946177c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455283"
---
# <a name="walkthrough---using-touch-in-android"></a>逐步解說-在 Android 中使用觸控

讓我們看看如何使用上一節的實用應用程式概念。 我們將建立具有四個活動的應用程式。 第一個活動會是會啟動其他活動以示範各種 Api 的功能表或切換開關。 下列螢幕擷取畫面顯示主要活動：

[![具有 Touch Me 按鈕的範例螢幕擷取畫面](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一個活動觸控範例將示範如何使用事件處理常式來觸及視圖。 手勢辨識器活動將示範如何將事件子類別 `Android.View.Views` 和處理，以及示範如何處理縮小的手勢。 第三個和最後一個活動（ **自訂手勢**）將會顯示如何使用自訂筆勢。 為了讓您更容易追蹤及吸收，我們會將此逐步解說分成幾個部分，每個章節都著重于其中一個活動。

## <a name="touch-sample-activity"></a>觸控範例活動

- 開啟專案 **TouchWalkthrough \_ 開始**。 **>mainactivity**全部都設定為 go， &ndash; 由我們負責在活動中執行觸控行為。 如果您執行應用程式並按一下 [ **觸控範例**]，則應該會啟動下列活動：

  [![螢幕擷取畫面：觸控開始顯示的活動](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

- 既然我們已確認活動啟動，請開啟 [檔案 **TouchActivity.cs** ]，並為下列事件新增處理常式 `Touch` `ImageView` ：

  ```csharp
  _touchMeImageView.Touch += TouchMeImageViewOnTouch;
  ```

- 接下來，將下列方法新增至 **TouchActivity.cs**：

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

請注意，在上述程式碼中，我們會將 `Move` 和 `Down` 動作視為相同。 這是因為即使使用者無法將手指 `ImageView` 移開，也可能會四處移動，或使用者行使的壓力可能會變更。 這些變更類型會產生 `Move` 動作。

每次使用者接觸時 `ImageView` ，就會 `Touch` 引發事件，而我們的處理常式會在螢幕上顯示訊息 **觸控開始** ，如下列螢幕擷取畫面所示：

[![具有觸控開始的活動螢幕擷取畫面](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要使用者觸及 `ImageView` ，就會在中顯示 **觸控開始** `TextView` 。 當使用者不再接觸時 `ImageView` ，訊息 **觸控端** 會顯示在中 `TextView` ，如下列螢幕擷取畫面所示：

[![具有觸控端點的活動螢幕擷取畫面](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)

## <a name="gesture-recognizer-activity"></a>手勢辨識器活動

現在可讓您執行手勢辨識器活動。 此活動將示範如何在螢幕上拖曳視圖，並說明一種執行縮小至縮放的方式。

- 將新的活動加入至名為的應用程式 `GestureRecognizer` 。
  編輯此活動的程式碼，使其類似于下列程式碼：

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

- 將新的 Android 視圖新增至專案，並將其命名為 `GestureRecognizerView` 。 將下列變數新增至此類別：

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

- 將下列的函式加入至 `GestureRecognizerView` 。 這個函式會將新增 `ImageView` 至我們的活動。 此時，程式碼仍不會編譯， &ndash; 因此我們必須建立可協助您在 `MyScaleListener` 使用者 pinches 時調整大小的類別 `ImageView` ：

  ```csharp
  public GestureRecognizerView(Context context): base(context, null, 0)
  {
      _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
      _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
      _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
  }
  ```

- 若要在我們的活動上繪製影像，我們需要覆寫 `OnDraw` View 類別的方法，如下列程式碼片段所示。 此程式碼會將移 `ImageView` 至所指定的位置 `_posX` ，以及 `_posY` 根據縮放比例調整影像的大小：

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

- 接下來，我們需要在 `_scaleFactor` 使用者 pinches 時更新執行個體變數 `ImageView` 。 我們會新增一個名為的類別 `MyScaleListener` 。 當使用者 pinches 時，此類別會接聽將由 Android 引發的調整事件 `ImageView` 。
  將下列內部類別加入至 `GestureRecognizerView` 。 此類別為 `ScaleGesture.SimpleOnScaleGestureListener` 。 當您對手勢的子集感興趣時，此類別是一個便利的類別，可讓接聽程式子類別：

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

- 下一個我們需要在中覆寫的方法 `GestureRecognizerView` 是 `OnTouchEvent` 。 下列程式碼會列出這個方法的完整實作為。 這裡有很多程式碼，讓我們花一分鐘的時間看看這裡的內容。 這個方法所做的第一件事，就是在需要的情況下，藉 &ndash; 由呼叫來調整圖示 `_scaleDetector.OnTouchEvent` 。 接下來，我們會試著找出呼叫這個方法的動作：

  - 如果使用者觸及螢幕，則會記錄 X 和 Y 位置，以及接觸螢幕的第一個指標的識別碼。

  - 如果使用者在螢幕上移動了觸控，則會找出使用者移動指標的程度。

  - 如果使用者已從畫面中抬起，則會停止追蹤手勢。

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

- 現在，執行應用程式，並啟動「手勢辨識器」活動。
  啟動畫面時，畫面看起來應該像下面的螢幕擷取畫面：

  [![具有 Android 圖示的手勢辨識器開始畫面](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

- 現在請觸碰圖示，並將它拖曳到螢幕周圍。 嘗試縮小至縮放手勢。 在某個時間點，您的畫面看起來會像下列螢幕擷取畫面：

  [![畫面周圍的手勢移動圖示](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此時您應該為自己提供 pat：您已在 Android 應用程式中執行過縮小至縮放功能！ 請使用自訂手勢，快速中斷並讓我們繼續進行本逐步解說中的第三個和最後一個活動 &ndash; 。

## <a name="custom-gesture-activity"></a>自訂手勢活動

本逐步解說中的最後一個畫面將使用自訂筆勢。

基於本逐步解說的目的，已使用手勢工具建立手勢程式庫，並在檔案 **資源/原始/手勢**中新增至專案。 在這種情況下，我們會在逐步解說中取得最後的活動。

- 將名為 **custom \_ 手勢 \_ layout** 的版面配置檔案新增至專案，並以下列內容 .axml 至專案。 專案中已有 [ **Resources** ] 資料夾中的所有影像：

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

- 接下來，將新的活動加入至專案，並將其命名為 `CustomGestureRecognizerActivity.cs` 。 將兩個執行個體變數新增至類別，如下列兩行程式碼所示：

  ```csharp
  private GestureLibrary _gestureLibrary;
  private ImageView _imageView;
  ```

- 編輯 `OnCreate` 此活動的方法，使其類似于下列程式碼。 讓我們花一分鐘的時間來說明這段程式碼的進展。 我們所做的第一件事是具現化， `GestureOverlayView` 並將它設定為活動的根視圖。
  我們也會將事件處理常式指派給的 `GesturePerformed` 事件 `GestureOverlayView` 。 接下來，我們會擴充稍早建立的版面配置檔案，並將它加入做為的子視圖 `GestureOverlayView` 。 最後一個步驟是初始化變數 `_gestureLibrary` ，並從應用程式資源載入筆勢檔案。 如果因為某些原因而無法載入手勢檔案，則此活動不會有太多，因此它會關閉：

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

- 我們需要做的最後一件事，就是執行 `GestureOverlayViewOnGesturePerformed` 下列程式碼片段所示的方法。 當 `GestureOverlayView` 偵測到手勢時，它會回呼到這個方法。 首先我們會藉由呼叫來取得 `IList<Prediction>` 與手勢相符的物件 `_gestureLibrary.Recognize()` 。 我們使用了一些 LINQ 來取得 `Prediction` 筆勢最高分的。

  如果沒有任何相符的手勢具有夠大的分數，事件處理常式就會結束，而不會執行任何動作。 否則，我們會檢查預測的名稱，並根據手勢的名稱變更要顯示的影像：

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

- 執行應用程式，並啟動「自訂手勢辨識器」活動。 它看起來應該類似下列螢幕擷取畫面：

  [![具有 [檢查我] 影像的螢幕擷取畫面](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

  現在在畫面上繪製一個核取記號，而顯示的點陣圖看起來應該像下一個螢幕擷取畫面所示：

  [![已繪製核取記號，可辨識核取記號](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

  最後，在畫面上繪製一個塗抹。 此核取方塊應該會變更回其原始影像，如下列螢幕擷取畫面所示：

  [![螢幕上的塗抹，會顯示原始影像](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

您現在已瞭解如何使用 Xamarin 在 Android 應用程式中整合觸控和手勢。

## <a name="related-links"></a>相關連結

- [Android Touch 開始 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)