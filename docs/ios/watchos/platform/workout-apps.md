---
title: watchOS 健身應用程式，在 Xamarin 中
description: 本文章涵蓋的增強功能 Apple 對 watchOS 3 以及如何在 Xamarin 中實作它們的健身應用程式。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d755160043191f93247fd09e99f23eb85831fa8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113894"
---
# <a name="watchos-workout-apps-in-xamarin"></a>watchOS 健身應用程式，在 Xamarin 中

_本文章涵蓋的增強功能 Apple 對 watchOS 3 以及如何在 Xamarin 中實作它們的健身應用程式。_


新 watchOS 3 健身相關應用程式也可能在 Apple Watch 上的 在背景執行，並取得 HealthKit 資料的存取權。 其父代 iOS 10 架構應用程式也能夠啟動 watchOS 3 架構應用程式不需要使用者介入。

將會詳細介紹下列主題：

## <a name="about-workout-apps"></a>健身應用程式的相關

適用性和健身應用程式的使用者可以是高度專用，讓針對其健全狀況和符合目標的星期幾的數個小時。 如此一來，他們預期的回應、 簡單易用的應用程式，準確地收集和顯示資料並無縫整合 Apple 健全狀況。

設計良好的適用性或健身應用程式可協助使用者連線到其符合目標活動的圖表。 使用 Apple Watch，適用性和健身應用程式需要立即存取核心比率，卡路里 burn 和活動偵測。

[![](workout-apps-images/workout01.png "適用性和健身應用程式範例")](workout-apps-images/workout01.png#lightbox)

WatchOS 3 的新手_背景執行_提供了相關的應用程式得以在 Apple Watch 上的 在背景執行，並取得 HealthKit 資料的存取權的能力。

本文件將介紹的背景執行的功能，涵蓋健身應用程式生命週期，並說明健身應用程式將如何影響使用者的_活動環形_Apple Watch 上。

## <a name="about-workout-sessions"></a>健身工作階段的相關

每個健身應用程式的核心是_健身工作階段_(`HKWorkoutSession`)，使用者可以啟動和停止。 健身工作階段 API 很容易實作，並提供多項好處是健身應用程式，例如：

- 影片和卡路里燒錄根據活動類型的偵測。
- 自動以使用者的活動環節的貢獻。
- 在 工作階段中，每當使用者喚醒裝置 （無論是藉由引發其手腕或與 Apple Watch 互動） 時自動將顯示應用程式。

## <a name="about-background-running"></a>有關背景執行

如上所述，運用 watchOS 3 健身應用程式可以設定要在背景中執行。 使用背景執行健身應用程式，可以在背景中執行時處理來自 Apple Watch 的感應器資料。 例如，應用程式可以繼續監視使用者的核心比率，即使它不會再顯示在畫面上。

背景執行也會提供能夠呈現給使用者的即時意見反應，在任何時間期間使用了工作階段，例如傳送 haptic 的警示，以通知使用者他們目前的進度。

此外，背景執行，可讓應用程式，以快速地更新其使用者介面，讓他們快速概覽在他們的 Apple Watch 時，使用者會有最新的資料。

若要維持在 Apple Watch 上的高效能，使用背景執行的監看式應用程式應該限制以節省電池的背景工作數量。 如果應用程式使用大量的 CPU，在背景中，它可以取得 watchOS 擱置。

### <a name="enabling-background-running"></a>啟用背景執行

若要啟用背景執行，執行下列作業：

1. 在 **方案總管 中**，連按兩下 監看式延伸模組的小幫手 iPhone 應用程式的`Info.plist`檔案，以開啟它進行編輯。
2. 若要切換**來源**檢視： 

    [![](workout-apps-images/plist01.png "原始碼檢視")](workout-apps-images/plist01.png#lightbox)
3. 加入新的金鑰，稱為`WKBackgroundModes`並設定**型別**到`Array`: 

    [![](workout-apps-images/plist02.png "加入新的金鑰，稱為 WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. 將新的項目新增至的陣列**型別**的`String`值，並針對`workout-processing`: 

    [![](workout-apps-images/plist03.png "String 型別與健身處理值的陣列中加入新項目")](workout-apps-images/plist03.png#lightbox)
5. 將變更儲存到檔案。

## <a name="starting-a-workout-session"></a>啟動了工作階段

有三個主要的步驟，以啟動了工作階段：

[![](workout-apps-images/workout02.png "三個主要步驟，並啟動了工作階段")](workout-apps-images/workout02.png#lightbox)

1. 應用程式必須要求存取 HealthKit 中資料的授權。
2. 建立了組態物件健身所啟動的型別。
3. 建立並啟動了工作階段使用新建立的健身組態。

### <a name="requesting-authorization"></a>要求的授權

應用程式能否存取使用者的 HealthKit 資料之前，它必須要求並接收來自使用者的授權。 視健身應用程式的本質而定，它可能會讓下列類型的要求：

- 若要將資料寫入的授權：
    - 健身
- 若要讀取資料的授權：
    - 燒錄的能源
    - 距離
    - 核心的比率  

應用程式可以要求授權之前，必須設定為存取 HealthKit。

請執行下列動作：

1. 在方案總管中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
2. 捲動到底部，並檢查**啟用 HealthKit**: 

    [![](workout-apps-images/auth01.png "請檢查啟用 HealthKit")](workout-apps-images/auth01.png#lightbox)
3. 將變更儲存到檔案。
4. 依照中的指示[明確的應用程式識別碼和佈建設定檔](~/ios/platform/healthkit.md)並[將應用程式識別碼和佈建的設定檔與您的 Xamarin.iOS 應用程式建立關聯](~/ios/platform/healthkit.md)區段[簡介HealthKit](~/ios/platform/healthkit.md)文章，以正確佈建應用程式。
5. 最後，使用中的指示[程式設計健全狀況套件](~/ios/platform/healthkit.md)並[要求權限從使用者](~/ios/platform/healthkit.md)區段[HealthKit 簡介](~/ios/platform/healthkit.md)文章要求若要存取使用者的 HealthKit 資料存放區的授權。

### <a name="setting-the-workout-configuration"></a>正在設定健身組態

使用健身組態物件建立了工作階段 (`HKWorkoutConfiguration`)，指定了類型 (例如`HKWorkoutActivityType.Running`) 和健身位置 (例如`HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>建立了工作階段代理人 

若要處理健身工作階段期間可能發生的事件，應用程式必須建立了工作階段代理人執行個體。 將新類別加入專案和基底它關閉的`HKWorkoutSessionDelegate`類別。 戶外執行的範例中，它看起來可能如下所示：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

這個類別會建立數個事件，就會引發狀態健身工作階段變更為 (`DidChangeToState`) 且健身工作階段失敗 (`DidFail`)。 

### <a name="creating-a-workout-session"></a>建立了工作階段

使用健身組態和健身工作階段代理人上面建立來建立新的健身工作階段，並開始對使用者的預設 HealthKit 存放區：

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

如果應用程式啟動此健身工作階段，而且使用者會切換回其 watch 錶面，小型的綠色 「 執行 man 」 圖示將會顯示上面所面臨的：

[![](workout-apps-images/workout03.png "所面臨的上方顯示的小綠色執行 man 圖示")](workout-apps-images/workout03.png#lightbox)

如果使用者點選此圖示時，會將他們引導回至應用程式。

## <a name="data-collection-and-control"></a>資料收集和控制項

一旦已設定並啟動了工作階段，應用程式需要收集有關工作階段 （例如使用者的核心頻率） 的資料，並控制工作階段的狀態：

[![](workout-apps-images/workout04.png "資料收集和控制圖表")](workout-apps-images/workout04.png#lightbox)

1. **觀察範例**-應用程式必須擷取 HealthKit 將加以處理並向使用者顯示的資訊。
2. **觀察事件**-應用程式必須回應 HealthKit 或從應用程式的 UI （例如暫停健身的使用者） 所產生的事件。
3. **進入執行狀態**-工作階段已經啟動，但目前正在執行。
4. **輸入已暫停 」 狀態**-使用者已暫停目前的健身工作階段，並可重新啟動它在稍後的日期。 使用者可能會多次單一健身工作階段中執行，且已暫停狀態之間切換。
5. **結束健身階段**-使用者可以在任何時間點結束健身工作階段或它可能會過期，並於它自己時 （例如兩個英里執行） 的計量的健身結束。

最後一個步驟是將健身工作階段的結果儲存到使用者的 HealthKit 資料存放區。

### <a name="observing-healthkit-samples"></a>觀察 HealthKit 範例

應用程式必須開啟_錨點物件查詢_HealthKit 資料的每個點，它所需要的例如核心速率] 或 [作用中的能源燒錄。 針對所觀察每個資料點，更新處理常式必須建立以擷取新的資料，因為它會傳送至應用程式。

從這些資料點時，應用程式可以累積總計 （例如總執行距離），並更新它的使用者介面所需。 此外，應用程式可以通知使用者，當他們已經達到特定目標或例如完成執行中的下一步 英里的成就。

看看下列的範例程式碼：

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

它會建立一個述詞，將它想要取得的資料使用的開始日期`GetPredicateForSamples`方法。 它會建立一組裝置，從使用 HealthKit 資訊提取`GetPredicateForObjectsFromDevices`方法，在此情況下本機 Apple Watch 只 (`HKDevice.LocalDevice`)。 兩個述詞會結合成複合述詞 (`NSCompoundPredicate`) 使用`CreateAndPredicate`方法。

新`HKAnchoredObjectQuery`會建立所需的資料點 (在此情況下`HKQuantityTypeIdentifier.ActiveEnergyBurned`作用中的能源燒錄資料點)，則沒有限制傳回的資料量 (`HKSampleQuery.NoLimit`) 和一個更新處理常式來處理傳回至應用程式的資料是定義從 HealthKit。 

新的資料會傳遞到指定的資料點的應用程式時，會呼叫更新處理常式。 如果會不傳回任何錯誤，應用程式可以安全地讀取資料、 進行任何必要的計算，並更新其所需的 UI。

程式碼執行迴圈的所有範例 (`HKSample`) 中傳回`addedObjects`陣列，並將它們轉換成數量範例 (`HKQuantitySample`)。 然後，它會取得為 joule 範例雙精度浮點數值 (`HKUnit.Joule`) 和累積到作用中的能源燒錄健身的加總，並更新使用者介面。

### <a name="achieved-goal-notification"></a>達成的目標通知

如上方所述，當使用者達到的目標健身應用程式 （例如完成執行中的第一步），它可以傳送 haptic 意見反應給透過 Taptic 引擎使用者。 應用程式應該一併更新其 UI 到目前為止，因為使用者可能會提高其手腕看到系統提示您的意見反應的事件。

若要播放 haptic 意見反應，請使用下列程式碼：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>觀察事件

事件是應用程式可以使用反白顯示 在使用者的健身期間的 特定點的時間戳記。 某些事件會直接由應用程式，並儲存到健身，某些事件將會自動建立 HealthKit。

若要觀察 HealthKit 所建立的事件，應用程式將會覆寫`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple watchOS 3 下加入下列新的事件類型：

- `HKWorkoutEventType.Lap` -對於健身分成等距部分的事件。 比方說，是用來標示一個巡覽時執行的追蹤。
- `HKWorkoutEventType.Marker` -對於感興趣的任意時間點健身內。 例如，達到戶外執行路由上的特定點。

這些新的類型可以建立應用程式中，並儲存在健身供稍後用於建立圖表和統計資料。

若要建立標記事件，執行下列作業：

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

此程式碼會建立一個標記事件的新執行個體 (`HKWorkoutEvent`) 並將它儲存在私用 （稍後將會寫入至健身工作階段） 事件的集合，並通知使用者透過 haptics 的事件。

### <a name="pausing-and-resuming-workouts"></a>暫停和繼續健身

在健身工作階段中的任何時間點，使用者可以暫時暫停健身，並於稍後繼續它。 比方說，它們可能會暫停室內執行取得重要的呼叫，並在呼叫完成之後，繼續執行。

應用程式的 UI 應提供暫停和繼續健身 （藉由呼叫 HealthKit），以便在 Apple Watch 可以節省電源和資料的空間，而使用者已暫停其活動的方式。 此外，應用程式應忽略處於暫停狀態健身工作階段時，就會收到任何新資料點。

HealthKit 會暫停及繼續呼叫產生暫停和繼續事件來回應。 當健身工作階段暫停時，任何新事件或資料會傳送至應用程式由 HealthKit 直到工作階段繼續為止。

若要暫停及繼續健身的工作階段中使用下列程式碼：

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

可以藉由覆寫處理暫停和繼續事件，就會產生從 HealthKit`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>動作事件

也會新增到 watchOS 3，會移動已暫停 (`HKWorkoutEventType.MotionPaused`) 和動作繼續 (`HKWorkoutEventType.MotionResumed`) 事件。 會引發這些事件會自動由 HealthKit 期間執行了使用者啟動和停止移動時。

當應用程式收到移動已暫停的事件時，應該會停止收集資料，直到使用者恢復執行動作，並收到繼續動作的事件。 應用程式的應用程式應該不會暫停健身工作階段，以回應移動已暫停的事件。

> [!IMPORTANT]
> 移動已暫停] 和 [動作恢復事件僅支援 RunningWorkout 活動型別 (`HKWorkoutActivityType.Running`)。

同樣地，處理這些事件，藉由覆寫`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>結束並儲存了工作階段

當使用者完成其健身時，應用程式將需要結束目前的健身工作階段，並將它儲存到 HealthKit 資料庫。 儲存至 HealthKit 健身會自動顯示在健身活動清單。

新增至 iOS 10，這包括健身活動清單，以及使用者的 iPhone 上。 因此即使不在 Apple Watch 附近，會在手機上看到健身。

健身包含能源範例會更新活動應用程式中的 使用者的移動通道，因此第 3 方應用程式現在可以對使用者的每日的移動目標。

若要結束並儲存了工作階段需要下列步驟：

[![](workout-apps-images/workout05.png "結束並儲存了工作階段圖表")](workout-apps-images/workout05.png#lightbox)

1. 首先，應用程式必須結束健身工作階段。
2. 健身工作階段會儲存到 HealthKit。
3. 加入儲存的健身工作階段中的任何範例 （例如燒錄的能源或距離）。

### <a name="ending-the-session"></a>結束工作階段

若要結束健身工作階段，請呼叫`EndWorkoutSession`方法`HKHealthStore`傳入`HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

這會重設裝置感應器，為其正常模式。 完成 HealthKit 結束了，它會接收回呼`DidChangeToState`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>正在儲存工作階段

一旦應用程式已經結束了工作階段，它將需要建立了 (`HKWorkout`) 並將它 （以及事件中） 儲存到 HealthKit 資料存放區 (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

此程式碼會建立為健身所需的能源燒錄總量和距離`HKQuantity`物件。 定義了中繼資料的字典會建立並指定健身的位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

新`HKWorkout`物件會建立具有相同`HKWorkoutActivityType`做為`HKWorkoutSession`，開始和結束日期，事件清單 （正在累積上述各節），燒錄，能源的總距離和中繼資料的字典。 此物件會儲存健康狀態存放區，並處理任何錯誤。  

### <a name="adding-samples"></a>新增範例

當應用程式會將一組範例儲存至健身時，HealthKit 會產生範例與健身本身之間的連線，讓應用程式可以查詢指定了與相關聯的所有樣本日後的 HealthKit。 使用這項資訊，應用程式可以從健身資料產生圖形，並針對健身時間軸繪製。

活動應用程式的移動環形參與應用程式，它必須包括能源範例，其中含有已儲存了。 此外，距離的能源總計必須符合應用程式都關聯已儲存了任何範例的總和。

若要加入已儲存了範例，請執行下列作業：

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

（選擇性） 應用程式可以計算，並建立較小的子集的範例或一個 $5.5 範例 （跨距健身的整個範圍），接著會取得儲存健身相關聯。

## <a name="workouts-and-ios-10"></a>健身與 iOS 10

每個 watchOS 3 健身應用程式具有父 iOS 10 根據的健身應用程式和，新 ios 10，此 iOS 應用程式可以用來啟動健身置於 Apple Watch 健身模式 （無需使用者介入） 並在背景執行模式下執行 watchOS 應用程式 (請參閱[背景執行的相關](#About-Background-Running)上方如需詳細資訊)。

WatchOS 應用程式執行時，它可用於 WatchConnectivity 傳訊和父代的 iOS 應用程式的通訊。

看看此程序的運作方式：

[![](workout-apps-images/workout06.png "iPhone 與 Apple Watch 通訊圖表")](workout-apps-images/workout06.png#lightbox)

1. IPhone app 建立`HKWorkoutConfiguration`物件，並設定健身類型和位置。
2. `HKWorkoutConfiguration`物件傳送應用程式的 Apple Watch 版本，如果尚未執行，它會啟動系統。
3. 傳遞設定中使用健身，watchOS 3 應用程式會啟動新的健身工作階段 (`HKWorkoutSession`)。

> [!IMPORTANT]
> 為了讓父 iPhone 應用程式，Apple Watch 上啟動了，watchOS 3 應用程式必須啟用的背景執行。 請參閱[啟用背景執行](#Enabling-Background-Running)上方以取得詳細資料。

此程序是直接在 watchOS 3 應用程式中啟動了工作階段的程序非常類似。 在 iphone 中，使用下列程式碼：

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

WatchOS 應用程式已安裝的版本，以及 iPhone 版可以先連接到它，以確保此程式碼：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

然後它會建立`HKWorkoutConfiguration`像往常一樣，並使用`StartWatchApp`方法`HKHealthStore`以將它傳送至 Apple Watch 並啟動應用程式與健身工作階段。

位於 watch OS 應用程式中，使用中的下列程式碼和`WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

它會採用`HKWorkoutConfiguration`並建立新`HKWorkoutSession`，並將附加的自訂執行個體`HKWorkoutSessionDelegate`。 健身工作階段會啟動對使用者的 HealthKit 健康狀態存放區。

## <a name="bringing-all-the-pieces-together"></a>結合所有項目

取得所有的這份文件中呈現的資訊，watchOS 3 根據的健身應用程式和其父代 iOS 10 根據的健身應用程式可能包括下列部分：

1. **iOS 10 `ViewController.cs`**  -處理啟動監看式連線工作階段，而且在 Apple Watch 上的了。
2. **watchOS 3 `ExtensionDelegate.cs`**  -處理 watchOS 3 健身應用程式版本。
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -自訂`HKWorkoutSessionDelegate`健身針對處理事件。

> [!IMPORTANT]
> 下列各節所示的程式碼只會包含實作可在 watchOS 3 健身應用程式的新的增強功能所需的組件。 所有支援的程式碼和呈現，並更新 UI 的程式碼就不會包含，但可以輕鬆地建立依照其他 watchOS 文件。<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`健身應用程式的父代 iOS 10 版本中的檔案會包含下列程式碼：

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

`ExtensionDelegate.cs` WatchOS 3 版本健身應用程式中的檔案會包含下列程式碼：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

`OutdoorRunDelegate.cs` WatchOS 3 版本健身應用程式中的檔案會包含下列程式碼：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>最佳作法

使用下列最佳做法設計和實作健身應用程式在 watchOS 3 與 iOS 10 時，Apple 建議：

- 請確定 watchOS 3 健身應用程式是仍運作正常的即使它是無法連線至 iPhone 與 iOS 10 版本的應用程式。
- GPS 時無法使用，因為它是能夠產生沒有 GPS 的距離範例，請使用 HealthKit 距離。
- 允許使用者從 Apple Watch 或 iPhone 啟動了。
- 允許從其他來源 （例如其他第 3 方應用程式） 在其歷程記錄資料檢視中顯示健身應用程式。
- 請確定應用程式不會不顯示刪除健身歷程記錄資料中。

## <a name="summary"></a>總結

這篇文章已涵蓋的增強功能 Apple 對 watchOS 3 以及如何在 Xamarin 中實作它們的健身應用程式。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
