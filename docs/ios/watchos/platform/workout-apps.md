---
title: 健身應用程式
description: 本文件涵蓋的增強功能蘋果對健身 watchOS 3，以及如何將它們實作中的 Xamarin 應用程式。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 96eb2eaca15ed0bccbb4c5cdb6a855fc7e0e3bb1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="workout-apps"></a>健身應用程式

_本文件涵蓋的增強功能蘋果對健身 watchOS 3，以及如何將它們實作中的 Xamarin 應用程式。_


新增至 watchOS 3，健身相關的應用程式具有 Apple Watch 在背景中執行，並取得 HealthKit 資料的存取權的能力。 其父 iOS 10 基礎應用程式也能夠啟動 watchOS 3 基礎應用程式不需要使用者介入。

將會詳細介紹下列主題：

## <a name="about-workout-apps"></a>關於健身應用程式

適用性和健身應用程式的使用者可以是高專用，讓其健全狀況和符合目標朝向一天的數個小時。 如此一來，他們期望能有效回應，方便使用精確地收集和顯示資料，並與 Apple 健全狀況緊密整合的應用程式。

設計良好的適用性或健身應用程式可協助使用者連線到其適用性的目標活動的圖表。 使用 Apple Watch，適用性和健身應用程式需要立即存取核心比率卡路里 burn 和活動偵測。

[![](workout-apps-images/workout01.png "適用性和健身應用程式範例")](workout-apps-images/workout01.png#lightbox)

新手 watchOS 3，_背景執行_提供健身相關的應用程式在 Apple Watch 背景中執行，並取得 HealthKit 資料的存取權的能力。

本文件將介紹背景執行的功能、 涵蓋健身應用程式生命週期，並顯示健身應用程式將如何影響使用者的_活動環形_Apple Watch 上。

## <a name="about-workout-sessions"></a>健身工作階段的相關

每個健身應用程式的核心是_健身工作階段_(`HKWorkoutSession`)，使用者可以啟動和停止。 健身工作階段應用程式開發介面很容易實作，並提供數個好處是健身應用程式，例如：

- 影片和卡路里燒錄根據活動類型的偵測。
- 使用者的活動環形自動比重。
- 在 工作階段，應用程式會自動顯示每當使用者喚醒裝置 （無論是透過引發其腕帶或與其互動 Apple Watch）。

## <a name="about-background-running"></a>有關背景執行

如前所述，與 watchOS 3 健身應用程式可以設定在背景中執行。 使用背景執行健身應用程式，可以在背景中執行時處理來自 Apple Watch 的感應器資料。 例如，應用程式能夠繼續監視使用者的核心比率，即使它不會再顯示在畫面上。

背景執行也提供了可向使用者顯示即時的意見反應，隨時在使用中健身工作階段，例如傳送 haptic 警示來通知使用者他們目前的進度。

此外，背景執行，可讓快速更新其使用者介面，讓使用者有最新的資料，當它們快速瀏覽其 Apple Watch 應用程式。

若要維持在 Apple Watch 上的高效能，使用背景執行的監看式應用程式應該限制以節省電池的背景工作數量。 如果應用程式使用過多的 CPU，在背景中，它可以取得 watchOS 擱置。

### <a name="enabling-background-running"></a>啟用執行的背景

若要啟用背景執行，執行下列作業：

1. 在**方案總管] 中**，連按兩下 [監看式擴充功能的附屬 iPhone 應用程式的`Info.plist`檔案，以開啟它進行編輯。
2. 切換至**來源**檢視： 

    [![](workout-apps-images/plist01.png "原始碼檢視")](workout-apps-images/plist01.png#lightbox)
3. 加入新的金鑰呼叫`WKBackgroundModes`並設定**類型**至`Array`: 

    [![](workout-apps-images/plist02.png "加入新的金鑰呼叫 WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. 具有陣列中加入新項目**類型**的`String`以及值`workout-processing`: 

    [![](workout-apps-images/plist03.png "將新的項目加入至字串類型和值的健身處理陣列")](workout-apps-images/plist03.png#lightbox)
5. 將變更儲存到檔案。

## <a name="starting-a-workout-session"></a>啟動健身工作階段

有三個啟動健身工作階段的主要步驟：

[![](workout-apps-images/workout02.png "若要啟動健身工作階段的三個主要步驟")](workout-apps-images/workout02.png#lightbox)

1. 應用程式必須要求授權存取 HealthKit 中的資料。
2. 正在啟動健身類型建立健身組態物件。
3. 建立並開始使用新建立的健身組態健身工作階段。

### <a name="requesting-authorization"></a>要求的授權

應用程式可以存取使用者的 HealthKit 資料之前，必須要求和接收來自使用者的授權。 根據健身應用程式的本質，它可能會導致下列類型的要求：

- 要寫入資料的授權：
    - 健身
- 讀取資料的授權：
    - 能源燒錄
    - 距離
    - 核心比率  

應用程式可以要求授權之前，它必須設定為存取 HealthKit。

請執行下列動作：

1. 在方案總管中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
2. 捲動到底部，並檢查**啟用 HealthKit**: 

    [![](workout-apps-images/auth01.png "核取啟用 HealthKit")](workout-apps-images/auth01.png#lightbox)
3. 將變更儲存到檔案。
4. 請依照下列中的指示[明確的應用程式識別碼和佈建設定檔](~/ios/platform/healthkit.md)和[相關聯的應用程式識別碼和佈建的設定檔與 Xamarin.iOS 應用程式](~/ios/platform/healthkit.md)區段[簡介HealthKit](~/ios/platform/healthkit.md)正確佈建應用程式的發行項。
5. 最後，使用中的指示[程式設計健全狀況套件](~/ios/platform/healthkit.md)和[要求權限從 使用者](~/ios/platform/healthkit.md)區段[簡介 HealthKit](~/ios/platform/healthkit.md)要求文件若要存取使用者的 HealthKit 資料存放區的授權。

### <a name="setting-the-workout-configuration"></a>設定健身組態

健身工作階段會建立使用健身組態物件 (`HKWorkoutConfiguration`) 指定健身型別 (例如`HKWorkoutActivityType.Running`) 和健身位置 (例如`HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>建立健身工作階段代理人 

若要處理可以健身工作階段期間發生的事件，應用程式必須建立健身工作階段代理人執行個體。 在專案中加入新的類別和基底它關閉的`HKWorkoutSessionDelegate`類別。 室外執行的範例中，它看起來可能如下所示：

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

這個類別會建立數個事件，就會引發為健身工作階段變更的狀態 (`DidChangeToState`) 如果健身工作階段失敗 (`DidFail`)。 

### <a name="creating-a-workout-session"></a>健身工作階段的建立

使用健身組態和健身工作階段代理人建立上方來建立新的健身工作階段並開始針對使用者的預設 HealthKit 存放區：

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

如果應用程式啟動此健身工作階段，使用者切換回其 watch 錶面微小的綠色 「 執行中攔截 」 圖示將會顯示圖示上方：

[![](workout-apps-images/workout03.png "表面上方顯示的小綠色執行 man 圖示")](workout-apps-images/workout03.png#lightbox)

如果在使用者點選此圖示，它們將會進入回應用程式。

## <a name="data-collection-and-control"></a>資料收集和控制

一旦已經設定和啟動健身工作階段，應用程式需要收集 （例如使用者的核心速率） 工作階段的相關資料，並控制工作階段的狀態：

[![](workout-apps-images/workout04.png "資料收集和控制圖表")](workout-apps-images/workout04.png#lightbox)

1. **觀察範例**-應用程式必須擷取 HealthKit 將作用時，並向使用者顯示的資訊。
2. **觀察事件**-應用程式必須回應 HealthKit 或從應用程式的 UI （例如，暫停健身使用者） 所產生的事件。
3. **進入執行狀態**-工作階段已啟動，且目前正在執行。
4. **輸入已暫停 」 狀態**-使用者已暫停目前健身工作階段，並可以重新啟動它較晚的日期。 使用者可能會多次單一健身工作階段中執行，且已暫停狀態之間切換。
5. **結束健身階段**-使用者可以在任何時間點結束健身工作階段或它可能會過期且結束於它自己是否 （例如兩個英哩就已經執行） 的計量付費的健身。

最後一個步驟是將健身工作階段的結果儲存到使用者的 HealthKit 資料存放區。

### <a name="observing-healthkit-samples"></a>觀察 HealthKit 範例

應用程式必須開啟_錨點物件查詢_每一個 HealthKit 資料點，它感興趣，例如核心速率或使用中的能源燒錄。 每個資料點所觀察到，更新處理常式必須建立新的資料傳送至應用程式擷取。

這些資料點，從應用程式可以累積總計 （例如總執行距離），並更新它的使用者介面所需。 此外，應用程式可以通知使用者他們已達到特定目標或成就，例如正在執行中的下一個英哩就已經完成時。

看看下列範例程式碼：

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

它會建立一個述詞，設定要取得的資料使用的開始日期`GetPredicateForSamples`方法。 它會建立一組裝置 HealthKit 從提取資訊使用`GetPredicateForObjectsFromDevices`方法，在此情況下本機 Apple Watch 只 (`HKDevice.LocalDevice`)。 兩個述詞會結合為複合述詞 (`NSCompoundPredicate`) 使用`CreateAndPredicate`方法。

新`HKAnchoredObjectQuery`建立所需的資料點 (在此情況下`HKQuantityTypeIdentifier.ActiveEnergyBurned`Active 能源燒錄資料點)，則沒有限制傳回的資料量 (`HKSampleQuery.NoLimit`) 並且更新處理常式定義為處理資料是傳回至應用程式從 HealthKit。 

更新處理常式會呼叫任何新的資料傳送到指定的資料點的應用程式的時間。 如果不傳回任何錯誤，則應用程式可以安全無虞地讀取資料、 進行所需的計算並更新其所需的 UI。

程式碼進行的所有範例迴圈 (`HKSample`) 中傳回`addedObjects`陣列，並將它們轉換至數量樣本 (`HKQuantitySample`)。 然後它會取得為 joule 範例雙精度浮點數值 (`HKUnit.Joule`) 並加以累積到作用中的能源的健身燒錄的累加更新使用者介面。

### <a name="achieved-goal-notification"></a>達到的目標的通知

如同上面所述，當使用者達到目標健身應用程式 （例如，完成後執行中的第一英哩就已經），它可以傳送意見反應 haptic 使用者可透過 Taptic 引擎。 應用程式應該一併更新它的 UI 此時，因為使用者可能會引發其腕帶看到提示您的意見反應的事件。

若要播放 haptic 意見反應，請使用下列程式碼：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>觀察事件

事件是應用程式可以使用以使用者的健身期間醒目提示的特定點的時間戳記。 某些事件會直接建立應用程式，並儲存到健身，某些事件將會自動建立 HealthKit。

若要觀察 HealthKit 所建立的事件，應用程式將會覆寫`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

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

- `HKWorkoutEventType.Lap` -對於健身分成等距部分的事件。 例如，對於標示一圈住追蹤記錄時執行。
- `HKWorkoutEventType.Marker` -適用於感興趣的任意點健身內。 例如，達到室外執行路由上的特定點。

這些新的類型可以建立應用程式中，並儲存在健身稍後可用來建立圖表和統計資料。

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

此程式碼建立的標記事件的新執行個體 (`HKWorkoutEvent`) 並將其儲存至私用 （稍後將會寫入至健身工作階段） 事件的集合，並通知使用者透過 haptics 的事件。

### <a name="pausing-and-resuming-workouts"></a>暫停和繼續健身

在健身工作階段中的任何時間點，使用者可以暫時暫停健身，並在稍後繼續。 例如，他們可能會暫停室內執行重要的呼叫並在呼叫完成之後繼續執行。

應用程式的 UI 應提供暫停和繼續健身 （藉由呼叫 HealthKit），以便 Apple Watch 可以節省電源和資料的空間，而使用者已暫停其活動的方式。 此外，應用程式應忽略健身工作階段處於暫停狀態時，就會收到任何新資料點。

暫停及繼續執行呼叫產生暫停和繼續事件會回應 HealthKit。 當健身工作階段暫停時，任何新事件或資料將會傳送至應用程式 HealthKit 直到工作階段繼續為止。

若要暫停及繼續健身工作階段中使用下列程式碼：

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

可以藉由覆寫處理將從 HealthKit 產生的暫停和繼續事件`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

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

### <a name="motion-events"></a>影片事件

也會新增至 watchOS 3，會移動已暫停 (`HKWorkoutEventType.MotionPaused`) 和影片繼續 (`HKWorkoutEventType.MotionResumed`) 事件。 這些事件會自動 HealthKit 期間執行健身時引發使用者啟動和停止移動。

當應用程式收到移動已暫停的事件時，它應該先停止收集資料，直到使用者繼續移動，且收到影片會繼續執行事件。 應用程式的應用程式應該不會暫停健身工作階段，以回應移動已暫停的事件。

> [!IMPORTANT]
> 移動已暫停] 和 [影片恢復事件僅針對 RunningWorkout 活動類型支援 (`HKWorkoutActivityType.Running`)。

同樣地，這些事件可以藉由覆寫處理`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

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

## <a name="ending-and-saving-the-workout-session"></a>結束並儲存健身工作階段

當使用者完成其健身時，應用程式將需要結束目前的健身工作階段，並將它儲存到 HealthKit 資料庫。 儲存至 HealthKit 健身會自動顯示健身活動清單中。

新增 ios 10，這包括健身活動清單，以及使用者的 iPhone 上。 因此即使 Apple Watch 不在附近，健身將會出現在電話上。

包含能源樣本的健身會更新使用者的移動環形活動應用程式中，因此第 3 個合作對象應用程式現在可以參與使用者的每日的移動目標。

若要結束並儲存健身工作階段需要下列步驟：

[![](workout-apps-images/workout05.png "結束並儲存健身工作階段的圖表")](workout-apps-images/workout05.png#lightbox)

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

這會為其標準模式來重設裝置感應器。 當 HealthKit 完成結束健身時，它會接收回呼`DidChangeToState`方法`HKWorkoutSessionDelegate`:

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

### <a name="saving-the-session"></a>儲存工作階段

一旦應用程式已經結束健身工作階段，它必須建立健身 (`HKWorkout`) 並將它儲存 （以及事件） HealthKit 資料存放區 (`HKHealthStore`):

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

此程式碼會建立為健身需要燒錄的能源總數和距離`HKQuantity`物件。 建立定義健身中繼資料的字典，並指定健身的位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

新`HKWorkout`物件建立具有相同`HKWorkoutActivityType`為`HKWorkoutSession`，開始和結束日期，事件的清單 （正在累積從上面的章節），燒錄的能源總計距離和中繼資料的字典。 此物件會儲存到健全狀況存放區，以及任何錯誤處理。  

### <a name="adding-samples"></a>新增範例

當應用程式會將一組範例儲存至健身時，HealthKit 會產生範例與健身本身之間的連線，讓應用程式可以在日後給定健身相關聯的所有範例查詢 HealthKit。 使用這項資訊，應用程式可從健身資料產生圖形，並繪製其針對健身時間軸。

活動應用程式移動鈴聲參與應用程式，它必須包含已儲存健身能源樣本。 此外，距離和能源的總計必須符合應用程式會儲存健身關聯的任何範例的總和。

要加入儲存健身範例，請執行下列作業：

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

（選擇性） 應用程式可以計算並建立較小的範例或一個百萬範例 （跨距健身的整個範圍），接著會取得相關聯儲存健身子集。

## <a name="workouts-and-ios-10"></a>健身和 iOS 10

每個 watchOS 3 健身應用程式具有父 iOS 10 根據的健身應用程式和，新 ios 10，此 iOS 應用程式可以用來啟動健身置於 Apple Watch 健身模式 （無需使用者介入） 並在背景執行模式下執行 watchOS 應用程式 (請參閱[背景執行的相關](#About-Background-Running)上方如需詳細資訊)。

WatchOS 應用程式執行時，它可以使用 WatchConnectivity 傳訊和與父 iOS 應用程式的通訊。

看看此程序的運作方式：

[![](workout-apps-images/workout06.png "iPhone 和 Apple Watch 通訊圖表")](workout-apps-images/workout06.png#lightbox)

1. IPhone app 建立`HKWorkoutConfiguration`物件，並設定健身類型和位置。
2. `HKWorkoutConfiguration`物件傳送 Apple Watch 新版應用程式，以及如果未執行，其啟動系統。
3. 傳遞設定中使用健身，watchOS 3 應用程式會啟動新的健身工作階段 (`HKWorkoutSession`)。

> [!IMPORTANT]
> 為了讓父 iPhone 應用程式，在 Apple Watch 上啟動健身，watchOS 3 應用程式必須有背景執行啟用。 請參閱[啟用背景執行](#Enabling-Background-Running)上方如需詳細資訊。

此程序會直接啟動健身工作階段 watchOS 3 應用程式中的程序非常類似。 在 iPhone 中使用下列程式碼：

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

此程式碼，以確保 watchOS 應用程式已安裝的版本，和 iPhone 版本可以先連線到它：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

然後它會建立`HKWorkoutConfiguration`像往常一樣，並使用`StartWatchApp`方法`HKHealthStore`將它傳送至 Apple Watch 並啟動應用程式和健身工作階段。

監看式 OS 應用程式時，在使用中的下列程式碼和`WKExtensionDelegate`:

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

它會採用`HKWorkoutConfiguration`並建立新`HKWorkoutSession`並附加執行個體的自訂`HKWorkoutSessionDelegate`。 健身工作階段會對使用者的 HealthKit Health Store 中啟動。

## <a name="bringing-all-the-pieces-together"></a>將所有片段結合在一起

花費在所有的這份文件中呈現的資訊，請 watchOS 3 根據的健身應用程式和其父 iOS 10 根據的健身應用程式可能包括下列部分：

1. **iOS 10 `ViewController.cs`**  -處理監看式連線工作階段和健身 Apple Watch 上的啟動。
2. **watchOS 3 `ExtensionDelegate.cs`**  -處理 watchOS 3 版本的健身應用程式。
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -自訂`HKWorkoutSessionDelegate`健身針對處理事件。

> [!IMPORTANT]
> 下列各節中所顯示的程式碼只會包含實作健身 watchOS 3 中的應用程式提供的新的增強功能所需的組件。 所有支援的程式碼和呈現，並更新 UI 的程式碼就不會包含，但可以輕鬆地建立的其他 watchOS 文件。<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`健身應用程式的父 iOS 10 版中的檔案會包含下列程式碼：

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

`ExtensionDelegate.cs`健身應用程式的 watchOS 3 版中的檔案會包含下列程式碼：

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

`OutdoorRunDelegate.cs`健身應用程式的 watchOS 3 版中的檔案會包含下列程式碼：

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

Apple 建議使用下列最佳作法設計和實作 watchOS 3 和 iOS 10 健身應用程式時：

- 請確定 watchOS 3 健身應用程式是仍運作正常的即使它是無法連線至 iPhone 和 iOS 10 版本的應用程式。
- GPS 時無法使用，因為它是能夠產生不含 GPS 距離範例，請使用 HealthKit 距離。
- 允許使用者從 Apple Watch 或 iPhone 啟動健身。
- 允許從其他來源 （例如其他第 3 個合作對象應用程式） 其歷程記錄資料檢視中顯示健身應用程式。
- 確認應用程式就無法顯示刪除健身歷程記錄資料中。

## <a name="summary"></a>總結

這篇文章已涵蓋增強功能蘋果對健身 watchOS 3，以及如何將它們實作中的 Xamarin 應用程式。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
