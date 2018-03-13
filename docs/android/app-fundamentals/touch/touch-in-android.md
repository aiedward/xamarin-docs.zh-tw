---
title: "在 Android 觸控"
ms.topic: article
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1d9cf345aa971c40f4132cc7970ed1244640da14
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="touch-in-android"></a>在 Android 觸控

更像 iOS、 Android 建立的物件，包含與螢幕的使用者實體互動的相關資料&ndash;`Android.View.MotionEvent`物件。 此物件會保存資料，例如執行什麼動作、 觸控所花費的放置，多少壓力已套用，等等。A`MotionEvent`細分為下列值移入的物件：

-  描述類型的動作，例如初始的觸控螢幕或觸控結束之間移動觸控動作程式碼。

-  一組軸值描述的位置`MotionEvent`和其他移動內容，例如其中觸控正在進行中、 當觸控發生，及使用多少不足的壓力。
   軸值可能不同，視裝置而定，因此上述清單不會描述所有的軸值。


`MotionEvent`物件會傳遞給應用程式中的適當方法。 有三種方式，讓 Xamarin.Android 回應觸控事件的應用程式：

-  *指定事件處理常式`View.Touch`*  -`Android.Views.View`類別具有`EventHandler<View.TouchEventArgs>`哪些應用程式可以指派到的處理常式。 這是一般的.NET 行為。

-  *實作`View.IOnTouchListener`*  -此介面的執行個體可能會指派給使用檢視的檢視物件。 `SetOnListener` 方法。這是功能上相當於指派事件處理常式`View.Touch`事件。 如果有許多不同的檢視可能需要在接觸時一些常見或共用邏輯，它會更有效率的方式建立類別並實作這個方法比指派給每個檢視其自有的事件處理常式。

-  *覆寫`View.OnTouchEvent`*  -Android 子類別中的所有檢視`Android.Views.View`。 接觸到檢視時，會呼叫 Android`OnTouchEvent`並將它傳遞`MotionEvent`物件做為參數。


> [!NOTE]
> 並非所有的 Android 裝置支援觸控式螢幕。 

將下列標記加入至您的資訊清單檔案會造成 Google Play 只顯示您的應用程式啟用觸控式裝置：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>軌跡

筆勢是手繪圖形上的觸控式螢幕。 筆勢可以有一或多個筆劃，每個連絡人與螢幕的不同點所建立的點順序所組成的 stroke。 Android 可以支援許多不同類型的筆勢，從複雜的手勢牽涉到多點觸控簡單 fling 在畫面上。

Android 提供`Android.Gestures`專為管理和手勢回應的命名空間。 在所有筆勢的核心是一種特殊類別，稱為`Android.Gestures.GestureDetector`。 正如其名，這個類別會接聽筆勢與根據事件`MotionEvents`作業系統所提供。

若要實作軌跡偵測器，活動必須具現化`GestureDetector`類別，並提供的執行個體`IOnGestureListener`，如下列程式碼片段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活動也必須實作 OnTouchEvent，並且將 MotionEvent 傳遞給筆勢偵測器。 下列程式碼片段會顯示此動作的範例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

執行個體時`GestureDetector`識別筆勢的感興趣，它會通知活動或應用程式引發事件或透過所提供的回呼`GestureDetector.IOnGestureListener`。
這個介面會提供各種筆勢的六種方法：

-  *OnDown* -點選，就會發生，但未釋放時呼叫。

-  *OnFling* -fling，就會發生與觸發此事件的開始和結束觸控會提供資料時呼叫。

-  *OnLongPress* -長按發生時呼叫。

-  *OnScroll* -捲動事件發生時呼叫。

-  *OnShowPress* -OnDown 發生後，以及移動呼叫或事件尚未執行。

-  *OnSingleTapUp* -點一下發生時呼叫。


在許多情況下應用程式可能只是子集感興趣的筆勢。 在此情況下，應用程式應該擴充 GestureDetector.SimpleOnGestureListener 類別並覆寫對應之事件的方法，他們感興趣。

## <a name="custom-gestures"></a>自訂軌跡

筆勢是使用者與應用程式互動的好方法。 到目前為止，我們已經看到應用程式開發介面的簡單的手勢，即已足夠，但是經過證明可能有點麻煩更複雜的筆勢。 為了協助進行更複雜的筆勢，Android 提供另一組 API，以簡化部分自訂軌跡相關聯的負擔 Android.Gestures 命名空間中。

### <a name="creating-custom-gestures"></a>建立自訂軌跡

自 Android 1.6，Android SDK 會隨附預先安裝模擬器呼叫筆勢產生器上的應用程式。 此應用程式可讓開發人員建立可內嵌於應用程式的預先定義的手勢。 下列螢幕擷取畫面顯示筆勢產生器的範例：

[![螢幕擷取畫面的筆勢產生器與範例的筆勢](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

此應用程式稱為筆勢工具的改良的版本可以找到 Google Play。 筆勢工具非常類似筆勢產生器不同之處在於它可讓您測試之後已建立的筆勢。 這個下一個螢幕擷取畫面顯示筆勢產生器：

[![筆勢工具的螢幕擷取畫面以範例手勢](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

筆勢工具更適用於建立自訂軌跡，因為它可讓它們正在建立要測試手勢，並輕鬆地透過 Google Play 取得。

筆勢工具可讓您建立軌跡繪製在螢幕上，指派名稱。 筆勢建立之後會儲存在您的裝置的 SD 卡上的二進位檔案。 從裝置擷取，然後封裝與資料夾 /Resources/raw 中的應用程式需要此檔案。 這個檔案可以擷取從使用 Android Debug Bridge 模擬器。 下列範例會示範 Galaxy Nexus 檔案複製到應用程式的資源目錄：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦您已擷取的檔案必須是封裝於應用程式內目錄 /Resources/未經處理。 使用此筆勢檔案最簡單的方式是將檔案載入 GestureLibrary，如下列程式碼片段所示：

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自訂軌跡

若要辨識自訂軌跡活動中的，您必須先 Android.Gesture.GestureOverlay 物件加入至其配置。 下列程式碼片段示範如何以程式設計方式加入 GestureOverlayView 活動：

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

下列 XML 程式碼片段示範如何以宣告方式新增 GestureOverlayView:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`有數個會繪製筆勢的程序期間引發的事件。 最有趣的事件是`GesturePeformed`。 當使用者完成繪製其筆勢時，會引發這個事件。

此事件引發時，會要求活動`GestureLibrary`以再試一次，並符合筆勢工具建立具有筆勢的其中一個使用者的筆勢。 `GestureLibrary` 會傳回預測物件清單。

每個預測物件會保存分數與其中一個在筆勢名稱`GestureLibrary`。 較高分數越高，越有可能在預測中名為的筆勢比對使用者繪製的筆勢。
一般而言，分數低於 1.0 會被視為不佳的相符項目。

下列程式碼會顯示比對筆勢的範例：

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

使用此完成，您應該了解如何使用觸控與手勢 Xamarin.Android 應用程式中。 讓我們現在移至逐步解說，並查看所有可用的範例應用程式中的概念。



## <a name="related-links"></a>相關連結

- [Android 觸控開始 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 觸控最終 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
