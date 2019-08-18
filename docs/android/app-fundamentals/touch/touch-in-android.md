---
title: Android 中的觸控
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 274c441e0507f100697fc153a9f748de1bce4cf3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526077"
---
# <a name="touch-in-android"></a>Android 中的觸控

Android 和 iOS 非常類似, 它會建立一個物件, 其中保存使用者與螢幕&ndash; `Android.View.MotionEvent`上的實體互動相關資料。 這個物件會保存資料, 例如執行的動作、觸控發生的位置、套用的壓力等等。`MotionEvent`物件會將移動分解成下列值:

- 描述動作類型的動作代碼, 例如初始觸控、在螢幕上移動的觸控, 或觸控結束。

- 一組軸值, 描述`MotionEvent`和其他移動屬性的位置, 例如觸控的發生位置、觸控發生的時間, 以及使用了多少壓力。
   軸值可能會因裝置而有所不同, 因此上一個清單不會描述所有的軸值。


`MotionEvent`物件將會傳遞至應用程式中的適當方法。 有三種方式可讓 Xamarin Android 應用程式回應觸控事件:

- *將事件處理常式指派`View.Touch`給*- `Android.Views.View`類別具有`EventHandler<View.TouchEventArgs>`可將處理常式指派給的應用程式。 這是典型的 .NET 行為。

- *執行`View.IOnTouchListener`* 此介面的實例可能會使用 view 指派給 view 物件。 `SetOnListener`方法.這在功能上相當於將事件處理常式指派`View.Touch`給事件。 如果有許多不同的視圖在觸及時可能需要的常見或共用邏輯, 建立類別並執行這個方法會比指派每個 view 自己的事件處理常式更有效率。

- *覆`View.OnTouchEvent`寫*-Android 子類別`Android.Views.View`中的所有 views。 觸及視圖時, Android 會呼叫`OnTouchEvent` , 並將`MotionEvent`物件當做參數傳給它。


> [!NOTE]
> 並非所有 Android 裝置都支援觸控畫面。 

將下列標記新增至您的資訊清單檔, 會導致 Google Play 只會將您的應用程式顯示到已啟用觸控功能的裝置上:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>軌跡

筆勢是觸控式螢幕上的手繪形狀。 筆勢可以有一或多個筆劃, 而每個筆劃則是由與螢幕不同的連絡人點所建立的點序列所組成。 Android 可以支援許多不同類型的手勢, 從螢幕上的簡單 codingjar 到牽涉到多點觸控的複雜手勢。

Android 提供專門`Android.Gestures`用來管理和回應手勢的命名空間。 所有手勢的核心都是稱為`Android.Gestures.GestureDetector`的特殊類別。 正如其名, 這個類別會根據作業系統所`MotionEvents`提供的手勢和事件進行接聽。

若要執行手勢偵測器, 活動必須具現`GestureDetector`化類別, 並提供的`IOnGestureListener`實例, 如下列程式碼片段所示:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活動也必須執行 OnTouchEvent, 並將 MotionEvent 傳遞至手勢偵測器。 下列程式碼片段顯示這種情況的範例:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

當的實例`GestureDetector`識別感關注的手勢時, 它會藉由引發事件或透過所`GestureDetector.IOnGestureListener`提供的回呼, 來通知活動或應用程式。
此介面針對各種手勢提供六種方法:

- *OnDown* -當點一下出現但未釋放時呼叫。

- *OnFling* -在 codingjar 發生時呼叫, 並在觸發事件的開始和結束觸控上提供資料。

- *OnLongPress* -當長按下出現時呼叫。

- *OnScroll* -發生 scroll 事件時呼叫。

- *OnShowPress* -在 OnDown 發生後呼叫, 而且尚未執行 move 或 up 事件。

- *OnSingleTapUp* -在進行單鍵時呼叫。


在許多情況下, 應用程式可能只對手勢的子集感興趣。 在此情況下, 應用程式應該擴充類別 GestureDetector. SimpleOnGestureListener, 並覆寫對應至其感興趣之事件的方法。

## <a name="custom-gestures"></a>自訂手勢

手勢是讓使用者與應用程式互動的絕佳方式。 到目前為止, 我們所見到的 Api 對簡單手勢已經足夠, 但可能會對更複雜的手勢證明有點繁重。 為了協助進行更複雜的手勢, Android 提供了另一組在 Android 中的 API。手勢命名空間, 可減輕與自訂手勢相關聯的一些負擔。

### <a name="creating-custom-gestures"></a>建立自訂手勢

從 Android 1.6 開始, Android SDK 隨附在模擬器上預先安裝的應用程式, 稱為手勢 Builder。 此應用程式可讓開發人員建立可內嵌在應用程式中的預先定義手勢。 下列螢幕擷取畫面顯示手勢產生器的範例:

[![具有範例手勢的手勢產生器螢幕擷取畫面](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

您可以 Google Play 找到此應用程式的改良版本, 稱為手勢工具。 手勢工具非常類似手勢產生器, 不同之處在于它可讓您在建立筆勢之後進行測試。 下一個螢幕擷取畫面顯示手勢產生器:

[![筆勢工具的螢幕擷取畫面與範例手勢](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

手勢工具在建立自訂手勢時更有用, 因為它可讓手勢在建立時進行測試, 並可透過 Google Play 輕鬆取得。

手勢工具可讓您在螢幕上繪製並指派名稱, 藉以建立手勢。 在建立手勢之後, 它們會儲存在您裝置的 SD 記憶卡上的二進位檔案中。 此檔案必須從裝置中取出, 然後以應用程式封裝在/Resources/raw. 資料夾中 您可以使用 Android Debug Bridge 從模擬器抓取此檔案。 下列範例顯示如何將檔案從 Galaxy 結點複製到應用程式的資原始目錄:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦您取出檔案, 它就必須在目錄/Resources/raw. 中與您的應用程式一起封裝 使用此手勢檔案最簡單的方式, 就是將檔案載入至 GestureLibrary, 如下列程式碼片段所示:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自訂手勢

若要辨識活動中的自訂手勢, 它必須將 GestureOverlay 物件新增至其版面配置。 下列程式碼片段顯示如何以程式設計方式將 GestureOverlayView 新增至活動:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

下列 XML 程式碼片段顯示如何以宣告方式新增 GestureOverlayView:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`有數個事件, 會在繪製手勢的過程中引發。 最有趣的事件是`GesturePerformed`。 當使用者完成其手勢繪製時, 就會引發此事件。

當引發這個事件時, 活動會要求`GestureLibrary`嘗試並比對使用者與手勢工具所建立的其中一項手勢。 `GestureLibrary`將傳回預測物件的清單。

每個預測物件都包含其中一個手勢`GestureLibrary`的分數和名稱。 分數越高, 預測中所命名的手勢就越可能符合使用者所繪製的手勢。
一般來說, 低於1.0 的分數會視為不相符的專案。

下列程式碼顯示比對手勢的範例:

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

完成這項作業之後, 您應該瞭解如何在 Xamarin Android 應用程式中使用觸控和手勢。 現在讓我們繼續進行逐步解說, 並查看實用範例應用程式中的所有概念。



## <a name="related-links"></a>相關連結

- [Android Touch 開始 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
