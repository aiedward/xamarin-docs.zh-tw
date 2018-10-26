---
title: Android 中的觸控
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9a6f7f5c82808b6430ba1aa6178a543945ae2634
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104904"
---
# <a name="touch-in-android"></a>Android 中的觸控

更像是 iOS、 Android 建立物件，存放關於使用者的實體與螢幕的互動資料&ndash;`Android.View.MotionEvent`物件。 此物件會保存資料，例如在執行什麼動作、 觸控花了其中放置，多少壓力已套用，以此類推。A`MotionEvent`細分為下列值移入的物件：

-  描述類型的動作，例如初始觸控，也就是在畫面上或在觸控結束之間移動觸控動作程式碼。

-  一組軸值描述的位置`MotionEvent`和其他移動屬性，例如其中觸控正在進行中、 當觸控發生，及使用多少不足的壓力。
   軸值可能是裝置，而有所不同，所以上述清單不會描述所有的軸值。


`MotionEvent`物件會傳遞至應用程式中的適當方法。 有三種方式來回應觸控事件為 Xamarin.Android 應用程式：

-  *將指定的事件處理常式`View.Touch`*  -`Android.Views.View`類別具有`EventHandler<View.TouchEventArgs>`哪些應用程式可以指派至的處理常式。 這是一般的.NET 行為。

-  *實作`View.IOnTouchListener`*  -此介面的執行個體可能會指派給使用檢視的檢視物件。 `SetOnListener` 方法。這是功能上相當於指派的事件處理常式`View.Touch`事件。 如果沒有在接觸時，可能需要許多不同的檢視一些常見或共用邏輯，它更有效率的方式是建立類別並實作這個方法比若要指派給每個檢視自己的事件處理常式。

-  *覆寫`View.OnTouchEvent`*  -Android 子類別中的所有檢視`Android.Views.View`。 當檢視接觸到時，Android 會呼叫`OnTouchEvent`並將它傳遞`MotionEvent`物件做為參數。


> [!NOTE]
> 並非所有的 Android 裝置支援觸控式螢幕。 

將下列標記新增至您的資訊清單檔案會導致 Google Play 只顯示您應用程式會啟用觸控的裝置：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>軌跡

筆勢是手動繪製圖形的觸控式螢幕上。 筆勢可以有一或多個筆劃，組成一連串點的不同畫面的連絡點所建立的每個筆劃。 Android 可以支援許多不同類型的筆勢，從簡單 codingjar 在螢幕上複雜牽涉到多點觸控的筆勢。

Android 提供`Android.Gestures`專為管理與回應筆勢的命名空間。 在所有筆勢的核心是一種特殊類別，稱為`Android.Gestures.GestureDetector`。 如同名稱所暗示，此類別會接聽筆勢和根據事件`MotionEvents`作業系統所提供。

若要實作動作偵測器，活動必須具現化`GestureDetector`類別，並提供的執行個體`IOnGestureListener`，如下列程式碼片段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活動也必須實作 OnTouchEvent，並且將 MotionEvent 傳遞給動作偵測器。 下列程式碼片段會顯示這個範例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

執行個體時`GestureDetector`識別筆勢的感興趣，它會通知的活動或應用程式所引發的事件，或是透過所提供的回呼`GestureDetector.IOnGestureListener`。
這個介面會提供各種手勢的六種方法：

-  *OnDown* -點選，就會發生，但不是會釋放時呼叫。

-  *OnFling* -codingjar，就會發生，並提供資料，觸發事件的開始和結束觸控時呼叫。

-  *OnLongPress* -長按發生時呼叫。

-  *OnScroll* -捲動事件發生時呼叫。

-  *OnShowPress* -呼叫 OnDown 發生之後，進行移動，或事件尚未執行。

-  *OnSingleTapUp* -只要點選一下發生時呼叫。


在許多情況下應用程式可能只會感興趣的筆勢子集。 在此情況下，應用程式應該擴充 GestureDetector.SimpleOnGestureListener 類別並覆寫對應的事件的方法，他們感興趣。

## <a name="custom-gestures"></a>自訂軌跡

筆勢是絕佳的方式，讓使用者與應用程式互動。 我們到目前為止所看到的 Api 的簡單的手勢，即已足夠，但卻可能頗稍微繁重的更複雜的筆勢。 為了協助進行更複雜的筆勢，Android 會提供另一組的 API，讓您更容易一些自訂的筆勢相關聯的負擔 Android.Gestures 命名空間中。

### <a name="creating-custom-gestures"></a>建立自訂軌跡

自 Android 1.6，Android SDK 隨附預先安裝筆勢 builder 在模擬器上的應用程式。 此應用程式可讓開發人員建立預先定義的筆勢，您可以內嵌在應用程式。 下列螢幕擷取畫面顯示手勢產生器的範例：

[![螢幕擷取畫面的手勢產生器範例筆勢](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

此應用程式，稱為筆勢工具的改良的版本可以找到 Google Play。 筆勢工具非常類似筆勢產生器不同之處在於它可讓您在建立之後，測試筆勢。 這個下一步 的螢幕擷取畫面顯示手勢產生器：

[![筆勢工具的螢幕擷取畫面範例筆勢](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

筆勢工具更適用於建立自訂的筆勢，因為它可讓正在建立要測試的筆勢，並輕易地透過 Google Play 取得。

筆勢工具可讓您建立筆勢在螢幕上繪製，並且指派名稱。 建立筆勢之後儲存在您的裝置的 SD 卡上的二進位檔中。 此檔案必須從裝置擷取，然後封裝與資料夾 /Resources/raw 中的應用程式。 這個檔案可以擷取從使用 Android Debug Bridge 的模擬器。 下列範例會示範從 Galaxy Nexus 的檔案複製到應用程式的資源目錄：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦您已擷取的檔案必須與您應用程式內目錄 /Resources/原始一起封裝。 若要使用此筆勢檔案最簡單的方式是將檔案載入 GestureLibrary，如下列程式碼片段所示：

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自訂軌跡

若要辨識的自訂活動中的筆勢，您必須使用 Android.Gesture.GestureOverlay 物件加入至其配置。 下列程式碼片段示範如何以程式設計方式新增 GestureOverlayView 活動：

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

`GestureOverlayView`有數個會繪製筆勢的程序期間引發的事件。 最感興趣的事件是`GesturePeformed`。 當使用者完成繪製其筆勢時，會引發這個事件。

活動會引發這個事件，當要求`GestureLibrary`，試著符合筆勢與筆勢的其中一個使用者所建立之筆勢的工具。 `GestureLibrary` 會傳回預測的物件清單。

每個預測物件保留的分數和中的筆勢的其中一個名稱`GestureLibrary`。 越高的分數，就越可能筆勢在預測中名為比對使用者繪製的筆勢。
一般而言，分數低於 1.0 會被視為不佳的相符項目。

下列程式碼會顯示相符的筆勢的範例：

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

以此方式完成，您應該了解如何在 Xamarin.Android 應用程式中使用觸控和筆勢。 讓我們現在移至逐步解說，並查看所有可用的範例應用程式中的概念。



## <a name="related-links"></a>相關連結

- [Android 觸控開始 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 觸控最終 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
