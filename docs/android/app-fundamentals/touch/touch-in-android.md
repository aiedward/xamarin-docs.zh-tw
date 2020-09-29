---
title: Android 中的觸控
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 154c33944e48f1409b52f14c297befa960b84f39
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455127"
---
# <a name="touch-in-android"></a>Android 中的觸控

就像 iOS 一樣，Android 會建立物件，以保存使用者實體與螢幕互動的相關資料 &ndash; `Android.View.MotionEvent` 。 此物件包含資料，例如執行的動作、觸控發生的位置、已套用的壓力等等。物件會將 `MotionEvent` 移動細分為下列值：

- 描述動作類型的動作程式碼，例如初始觸控、在螢幕上移動觸控或觸控結束。

- 一組軸值，描述 `MotionEvent` 和其他移動屬性的位置，例如觸控發生的位置、觸控發生的時間，以及使用了多少壓力。
   軸值可能會因裝置而有所不同，因此上一個清單不會描述所有軸值。

`MotionEvent`物件將會傳遞至應用程式中的適當方法。 有三種方式可讓 Xamarin. Android 應用程式回應觸控事件：

- *將事件處理常式指派 `View.Touch` 給*- `Android.Views.View` 類別具有 `EventHandler<View.TouchEventArgs>` 可指派處理常式的目標應用程式。 這是一般的 .NET 行為。

- *執行 `View.IOnTouchListener` *-這個介面的實例可使用 View 指派給 view 物件。 `SetOnListener` 方法。這在功能上相當於將事件處理常式指派給 `View.Touch` 事件。 如果有許多不同的視圖在接觸時可能需要的常見或共用邏輯，建立類別並執行此方法的方式，會比指派每一個 view 本身的事件處理常式更有效率。

- *覆 `View.OnTouchEvent` 寫*-Android 子類別中的所有觀點 `Android.Views.View` 。 當您接觸到視圖時，Android 會呼叫， `OnTouchEvent` 並將 `MotionEvent` 物件做為參數傳遞給它。

> [!NOTE]
> 並非所有 Android 裝置都支援觸控式螢幕。 

將下列標記新增至您的資訊清單檔，會導致 Google Play 只顯示您的應用程式到已啟用觸控的裝置：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>軌跡

手勢是觸控式螢幕上的手繪圖形。 手勢可以有一或多個筆劃，每個筆劃都是由與螢幕不同的點點所建立的一連串點所組成。 Android 可支援許多不同類型的手勢，從畫面上的簡單 codingjar 到牽涉到多點觸控的複雜手勢。

Android 提供 `Android.Gestures` 專門用來管理和回應筆勢的命名空間。 所有手勢的核心都是稱為的特殊類別 `Android.Gestures.GestureDetector` 。 顧名思義，這個類別會根據作業系統所提供的手勢來接聽手勢和事件 `MotionEvents` 。

若要執行筆勢偵測器，活動必須具現化 `GestureDetector` 類別並提供的實例 `IOnGestureListener` ，如下列程式碼片段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活動也必須執行 OnTouchEvent，並將 MotionEvent 傳遞給手勢偵測器。 下列程式碼片段顯示此範例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

當的實例 `GestureDetector` 識別感興趣的手勢時，它會藉由引發事件或透過提供的回呼來通知活動或應用程式 `GestureDetector.IOnGestureListener` 。
此介面針對各種手勢提供六種方法：

- *OnDown* -當點一下出現但未釋放時呼叫。

- *OnFling* -在 codingjar 發生時呼叫，並在觸發事件的開始和結束觸控上提供資料。

- *OnLongPress* -在長按下發生時呼叫。

- *OnScroll* -發生滾動事件時呼叫。

- *OnShowPress* -在 OnDown 發生之後呼叫，且尚未執行 move 或 up 事件。

- *OnSingleTapUp* -在點一下發生時呼叫。

在許多情況下，應用程式可能只對部分手勢感興趣。 在此情況下，應用程式應擴充 GestureDetector 類別，並覆寫對應至其感興趣之事件的方法。

## <a name="custom-gestures"></a>自訂筆勢

手勢是讓使用者與應用程式互動的絕佳方式。 到目前為止，我們所見到的 Api 對簡單的手勢而言就已足夠，但對於更複雜的手勢而言，可能會有很大的麻煩。 為了協助進行更複雜的手勢，Android 會在 Android. 手勢命名空間中提供另一組 API，可減輕一些與自訂手勢相關聯的負擔。

### <a name="creating-custom-gestures"></a>建立自訂手勢

自 Android 1.6 起，Android SDK 隨附預先安裝在模擬器上的應用程式，稱為手勢 Builder。 此應用程式可讓開發人員建立可內嵌在應用程式中的預先定義筆勢。 下列螢幕擷取畫面顯示手勢 Builder 的範例：

[![具有範例手勢的手勢產生器螢幕擷取畫面](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

您可以 Google Play 中找到此應用程式的改良版本，稱為「手勢」工具。 軌跡工具與手勢產生器非常類似，不同之處在于它可讓您在建立筆勢之後進行測試。 下一個螢幕擷取畫面顯示手勢產生器：

[![具有範例手勢之手勢工具的螢幕擷取畫面](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

軌跡工具會更適合用來建立自訂筆勢，因為它可讓手勢在建立時進行測試，而且可透過 Google Play 輕鬆取得。

手勢工具可讓您在螢幕上繪製並指派名稱，藉以建立手勢。 建立手勢之後，它們會儲存在裝置 SD 記憶卡上的二進位檔案中。 您必須從裝置中取出此檔案，然後使用該資料夾中的應用程式封裝/Resources/raw。 您可以使用 Android Debug Bridge 從模擬器中取出這個檔案。 下列範例示範如何將檔案從 Galaxy 結點複製到應用程式的資原始目錄：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦您已取出檔案，它就必須與您的應用程式一起封裝在目錄中/Resources/raw。 使用這個軌跡檔案最簡單的方式，就是將檔案載入 GestureLibrary，如下列程式碼片段所示：

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自訂手勢

若要辨識活動中的自訂手勢，必須將 GestureOverlay 物件新增至其配置。 下列程式碼片段顯示如何以程式設計方式將 GestureOverlayView 加入至活動：

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

下列 XML 程式碼片段顯示如何以宣告方式加入 GestureOverlayView：

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`有數個事件，會在繪製手勢的過程中引發。 最有趣的事件是 `GesturePerformed` 。 當使用者完成繪製其手勢時，就會引發此事件。

當引發這個事件時，活動會要求嘗試 `GestureLibrary` ，並比對使用者與 [手勢] 工具所建立的其中一個手勢的手勢。 `GestureLibrary` 會傳回預測物件的清單。

每個預測物件都會在中保存其中一個手勢的分數和名稱 `GestureLibrary` 。 分數愈高，在預測中命名的手勢就越可能符合使用者所繪製的手勢。
一般來說，低於1.0 的分數會視為不相符。

下列程式碼顯示與手勢相符的範例：

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

完成這項操作之後，您應該瞭解如何在 Xamarin Android 應用程式中使用觸控和手勢。 現在讓我們繼續進行逐步解說，並查看實用範例應用程式中的所有概念。

## <a name="related-links"></a>相關連結

- [Android Touch 開始 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)