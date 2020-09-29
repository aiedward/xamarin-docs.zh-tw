---
title: 在 Xamarin 中 watchOS 測驗應用程式
description: 本文涵蓋 Apple 針對 watchOS 3 中的測驗應用程式所進行的增強功能，以及如何在 Xamarin 中加以執行。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 826d8c982e9976cb4147c0ed77a55a4b1d18f879
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432414"
---
# <a name="watchos-workout-apps-in-xamarin"></a>在 Xamarin 中 watchOS 測驗應用程式

_本文涵蓋 Apple 針對 watchOS 3 中的測驗應用程式所進行的增強功能，以及如何在 Xamarin 中加以執行。_

WatchOS 3 的新功能，測驗相關的應用程式能夠在 Apple Watch 的背景中執行，並取得 HealthKit 資料的存取權。 其上層 iOS 10 應用程式也能夠在不需要使用者介入的情況下啟動 watchOS 3 型應用程式。

將會詳細介紹下列主題：

## <a name="about-workout-apps"></a>關於測驗應用程式

健身和測驗應用程式的使用者可高度專用，專數小時的時間，以達到其健康情況和健身目標。 因此，他們預期會有回應式、容易使用的應用程式，可精確地收集和顯示資料，並與 Apple Health 緊密整合。

設計完善的健身或測驗應用程式，可協助使用者建立活動的圖表，以達成其健身目標。 使用 Apple Watch，健身和測驗應用程式可以立即存取核心費率、calorie 燒錄和活動偵測。

[![健身和測驗應用程式範例](workout-apps-images/workout01.png)](workout-apps-images/workout01.png#lightbox)

WatchOS 3 的新功能， _背景_ 執行可讓健身相關的應用程式能夠在 Apple Watch 的背景中執行，並取得 HealthKit 資料的存取權。

本檔將介紹背景執行功能、涵蓋測驗應用程式生命週期，並顯示測驗應用程式如何參與 Apple Watch 的使用者 _活動環形_ 。

## <a name="about-workout-sessions"></a>關於測驗課程

每個測驗應用程式的核心都是一個 _測驗會話_ ， (`HKWorkoutSession` 使用者可以啟動和停止的) 。 您可以輕鬆地實行測驗會話 API，並為測驗應用程式提供數個優點，例如：

- 以活動類型為基礎的動作和 calorie 燒錄偵測。
- 自動參與使用者的活動環。
- 在會話中，每當使用者喚醒裝置時，就會自動顯示應用程式 (藉由提高手腕或與 Apple Watch) 互動。

## <a name="about-background-running"></a>關於背景執行

如上所述，使用 watchOS 3 時，可以將測驗應用程式設定為在背景中執行。 使用背景執行測驗應用程式，可以在背景中執行時，處理來自 Apple Watch 感應器的資料。 例如，應用程式可以繼續監視使用者的核心速率，即使它不再顯示在畫面上也一樣。

背景執行也可讓您在使用中的測驗會話期間，隨時向使用者呈現即時意見反應，例如傳送 haptic 警示以通知使用者其目前的進度。

此外，執行中的背景可讓應用程式快速更新其消費者介面，讓使用者在快速概覽其 Apple Watch 時擁有最新的資料。

為了維持 Apple Watch 的高效能，使用背景執行的 Watch 應用程式應該限制背景工作的數量，以節省電池。 如果應用程式在背景中使用過多的 CPU，則 watchOS 可能會遭到擱置。

### <a name="enabling-background-running"></a>正在啟用背景執行

若要啟用背景執行，請執行下列動作：

1. 在 **方案總管**中，按兩下 Watch 延伸模組的隨附 iPhone 應用程式檔案， `Info.plist` 將其開啟以供編輯。
2. 切換至 **來源** view： 

    [![來源視圖](workout-apps-images/plist01.png)](workout-apps-images/plist01.png#lightbox)
3. 加入名為的新機碼 `WKBackgroundModes` ，並將 **類型** 設定為 `Array` ： 

    [![加入名為 WKBackgroundModes 的新機碼](workout-apps-images/plist02.png)](workout-apps-images/plist02.png#lightbox)
4. 將新專案加入至 **類型** 為的陣列 `String` ，並將值設為 `workout-processing` ： 

    [![將新的專案加入至陣列，其中包含字串的類型和測驗處理的值](workout-apps-images/plist03.png)](workout-apps-images/plist03.png#lightbox)
5. 將變更儲存至檔案。

## <a name="starting-a-workout-session"></a>開始測驗課程

開始測驗課程有三個主要步驟：

[![開始測驗課程的三個主要步驟](workout-apps-images/workout02.png)](workout-apps-images/workout02.png#lightbox)

1. 應用程式必須要求授權才能存取 HealthKit 中的資料。
2. 針對正在啟動的測驗類型建立測驗設定物件。
3. 使用新建立的測驗設定，建立並啟動測驗會話。

### <a name="requesting-authorization"></a>要求授權

在應用程式可以存取使用者的 HealthKit 資料之前，必須先向使用者要求並接收授權。 視測驗應用程式的本質而定，它可能會產生下列類型的要求：

- 寫入資料的授權：
  - 訓練
- 讀取資料的授權：
  - 能源燒錄
  - 距離
  - 心率  

在應用程式可以要求授權之前，必須先將其設定為存取 HealthKit。

執行下列動作：

1. 在方案總管**** 中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
2. 滾動至底部並勾選 [ **啟用 HealthKit**： 

    [![檢查啟用 HealthKit](workout-apps-images/auth01.png)](workout-apps-images/auth01.png#lightbox)
3. 將變更儲存至檔案。
4. 遵循明確的應用程式 [識別碼和布建設定檔](~/ios/platform/healthkit.md) 中的指示，並將 [應用程式識別碼和布建設定檔與](~/ios/platform/healthkit.md) HealthKit 文章 [簡介](~/ios/platform/healthkit.md) 中的 Xamarin 應用程式區段建立關聯，以正確布建應用程式。
5. 最後，請使用程式[設計健康情況套件](~/ios/platform/healthkit.md)中的指示，並向[HealthKit](~/ios/platform/healthkit.md)文章的使用者章節要求[許可權](~/ios/platform/healthkit.md)，以要求授權存取使用者的 HealthKit 資料存放區。

### <a name="setting-the-workout-configuration"></a>設定測驗設定

測驗會話是使用測驗設定物件來建立 (`HKWorkoutConfiguration`) 指定健身類型 (例如 `HKWorkoutActivityType.Running`) 和測驗位置 (例如 `HKWorkoutSessionLocationType.Outdoor`) ：

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>建立測驗會話委派 

若要處理在測驗會話期間可能發生的事件，應用程式將需要建立測驗會話委派實例。 將新類別新增至專案，並以類別作為基礎 `HKWorkoutSessionDelegate` 。 在室外執行的範例中，它看起來可能如下所示：

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

此類別會建立數個事件，這些事件會隨著測驗會話的狀態變更 (`DidChangeToState`) ，以及如果測驗會話失敗 (`DidFail`) 。 

### <a name="creating-a-workout-session"></a>建立測驗會話

使用上面建立的測驗設定和測驗會話委派來建立新的測驗會話，並根據使用者的預設 HealthKit 存放區來啟動它：

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

如果應用程式啟動此測驗會話，而使用者切換回其觀賞臉部，則臉部上方會顯示一個小綠色的「執行中」圖示：

[![顯示在臉部上方的小綠色執行中 man 圖示](workout-apps-images/workout03.png)](workout-apps-images/workout03.png#lightbox)

如果使用者按下此圖示，則會將他們重新導向至應用程式。

## <a name="data-collection-and-control"></a>資料收集和控制

設定並啟動測驗會話之後，應用程式就必須收集會話的相關資料 (例如使用者的核心費率) 和控制會話的狀態：

[![資料收集和控制圖表](workout-apps-images/workout04.png)](workout-apps-images/workout04.png#lightbox)

1. **觀察範例** -應用程式必須從 HealthKit 中取出會對使用者採取動作和顯示的資訊。
2. **觀察事件** -應用程式必須回應 HealthKit 或應用程式 (UI 所產生的事件，例如使用者暫停測驗) 。
3. **進入執行狀態** -會話已啟動且目前正在執行。
4. **進入暫停狀態** -使用者已暫停目前的測驗會話，並可在日後重新開機。 使用者可以在單一測驗會話中多次切換正在執行和暫停狀態。
5. **結束測驗會話** -使用者可以在任何時間點結束測驗會話，或如果它是計量式測驗 (例如兩英里的執行) ，則會在任何時間點結束測驗會話或終止。

最後一個步驟是將測驗會話的結果儲存至使用者的 HealthKit 資料存放區。

### <a name="observing-healthkit-samples"></a>觀察 HealthKit 範例

應用程式必須針對每個感興趣的 HealthKit 資料點開啟 _錨點物件查詢_ ，例如，心率或主動式能源燒錄。 針對觀察到的每個資料點，必須建立更新處理常式，以便在將新的資料傳送至應用程式時加以捕捉。

從這些資料點，應用程式可以累積總計 (例如總執行距離) ，並視需要更新它的消費者介面。 此外，應用程式可以在達到特定目標或成就（例如完成下一英里的執行）時通知使用者。

請參閱下列範例程式碼：

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

它會建立述詞，以設定它想要取得資料以使用方法的開始日期 `GetPredicateForSamples` 。 它會建立一組裝置，從使用方法提取 HealthKit 資訊 `GetPredicateForObjectsFromDevices` ，在此情況下，本機 Apple Watch 只 (`HKDevice.LocalDevice`) 。 這兩個述詞會合並為複合述詞， (`NSCompoundPredicate` 使用 `CreateAndPredicate` 方法) 。

`HKAnchoredObjectQuery`系統會為所需的資料點建立新的 (在此案例中 `HKQuantityTypeIdentifier.ActiveEnergyBurned` ，針對使用中的能源燒錄資料點) ， () 傳回的資料量沒有限制， `HKSampleQuery.NoLimit` 而且會定義更新處理常式來處理從 HealthKit 傳回至應用程式的資料。 

每當有新資料傳遞給指定資料點的應用程式時，就會呼叫更新處理常式。 如果未傳回任何錯誤，應用程式可以安全地讀取資料，進行任何必要的計算，並視需要更新其 UI。

程式碼會在陣列中傳回 () 的所有樣本上進行迴圈 `HKSample` `addedObjects` ，並將它們轉換成 Quantity 範例 (`HKQuantitySample`) 。 然後，它會取得樣本的雙精度浮點數作為 joule (`HKUnit.Joule`) ，並將它累積至適用于測驗的主動式能源累積，並更新消費者介面。

### <a name="achieved-goal-notification"></a>達成的目標通知

如上所述，當使用者達到測驗應用程式的目標 (例如完成執行) 的第一英里時，它可以透過 Taptic Engine 將 haptic 回饋傳送給使用者。 應用程式此時也應該更新其 UI，因為使用者可能會提高手腕以查看提示意見反應的事件。

若要播放 haptic 意見反應，請使用下列程式碼：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>觀察事件

事件是應用程式在使用者的測驗期間，可用來反白顯示特定點的時間戳記。 有些事件會直接由應用程式建立並儲存至測驗中，而某些事件將會由 HealthKit 自動建立。

為了觀察 HealthKit 所建立的事件，應用程式會覆寫的 `DidGenerateEvent` 方法 `HKWorkoutSessionDelegate` ：

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

Apple 已在 watchOS 3 中新增下列新的事件種類：

- `HKWorkoutEventType.Lap` -適用于將測驗細分為相等距離部分的事件。 例如，在執行時，將一個膝上的周圍標記。
- `HKWorkoutEventType.Marker` -適用于測驗內的任意興趣點。 例如，到達室外執行路線上的特定點。

這些新的類型可以由應用程式建立並儲存在測驗中，以便稍後用於建立圖形和統計資料。

若要建立標記事件，請執行下列動作：

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

此程式碼會建立新的標記事件實例 (`HKWorkoutEvent`) ，並將它儲存至事件的私用集合 (稍後將會寫入至測驗會話) ，並透過 haptics 通知使用者事件。

### <a name="pausing-and-resuming-workouts"></a>暫停和繼續 Workouts

在測驗會話中的任何時間點，使用者都可以暫時暫停測驗，稍後再繼續。 例如，他們可能會暫停室內執行以進行重要的呼叫，並在呼叫完成之後繼續執行。

應用程式的 UI 應該會藉由呼叫 HealthKit) 來提供暫停和繼續測驗 (的方法，讓 Apple Watch 可以在使用者暫停其活動時省下電源和資料空間。 此外，當測驗會話處於暫停狀態時，應用程式應該會忽略任何可能收到的新資料點。

HealthKit 將會藉由產生暫停和繼續事件，來回應暫停和繼續呼叫。 當測驗會話暫停時，HealthKit 將不會有任何新的事件或資料傳送至應用程式，直到會話繼續為止。

使用下列程式碼來暫停和繼續測驗會話：

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

您可以藉由覆寫的方法，來處理將從 HealthKit 產生的暫停和繼續事件 `DidGenerateEvent` `HKWorkoutSessionDelegate` ：

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

也是 watchOS 3 的新手， (`HKWorkoutEventType.MotionPaused`) 和移動 () 事件中的動作暫停 `HKWorkoutEventType.MotionResumed` 。 當使用者開始和停止移動時，HealthKit 會在執行中的測驗期間自動引發這些事件。

當應用程式收到動作暫停事件時，它應該會停止收集資料，直到使用者繼續移動並收到移動繼續事件為止。 應用程式不應暫停測驗會話以回應動作暫停事件。

> [!IMPORTANT]
> 只有 RunningWorkout 活動類型 () 支援動作暫停和移動繼續事件 `HKWorkoutActivityType.Running` 。

同樣地，您可以藉由覆寫的方法來處理這些事件 `DidGenerateEvent` `HKWorkoutSessionDelegate` ：

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

## <a name="ending-and-saving-the-workout-session"></a>結束和儲存測驗會話

當使用者完成測驗之後，應用程式將需要結束目前的測驗會話，並將其儲存至 HealthKit 資料庫。 儲存至 HealthKit 的 Workouts 會自動顯示在 [測驗活動] 清單中。

最新的 iOS 10，這也包含使用者 iPhone 上的測驗活動清單清單。 因此，即使 Apple Watch 不在附近，仍會在手機上顯示測驗。

包含能源範例的 Workouts 會更新使用者在活動應用程式中的移動響鈴，讓協力廠商應用程式現在可以參與使用者的每日移動目標。

以下是結束和儲存測驗會話的必要步驟：

[![結束和儲存測驗會話圖表](workout-apps-images/workout05.png)](workout-apps-images/workout05.png#lightbox)

1. 首先，應用程式將需要結束測驗會話。
2. 測驗會話會儲存至 HealthKit。
3. 將任何範例 (例如能源燒錄或距離) 至已儲存的測驗課程。

### <a name="ending-the-session"></a>結束會話

若要結束測驗會話，請呼叫 `EndWorkoutSession` 傳入的方法 `HKHealthStore` `HKWorkoutSession` ：

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

這會將裝置感應器重設為正常模式。 當 HealthKit 完成測驗結束時，它會收到的方法的回呼 `DidChangeToState` `HKWorkoutSessionDelegate` ：

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

### <a name="saving-the-session"></a>正在儲存會話

一旦應用程式結束測驗會話之後，就必須建立測驗 (`HKWorkout`) 並將其儲存 (以及) HealthKit 資料存放區 () 的事件中 `HKHealthStore` ：

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

這段程式碼會建立要求的總能源，以及將測驗作為物件的距離 `HKQuantity` 。 會建立定義測驗的元資料字典，並指定測驗的位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

新 `HKWorkout` 物件的建立方式與 `HKWorkoutActivityType` `HKWorkoutSession` 、開始和結束日期相同， (從上述各節累積的事件清單) 、能源燒錄、總距離和元資料字典。 這個物件會儲存至健康情況存放區，並處理任何錯誤。  

### <a name="adding-samples"></a>加入範例

當應用程式將一組範例儲存至測驗時，HealthKit 會產生範例和測驗本身之間的連線，讓應用程式可以在日後針對所有與特定測驗相關聯的範例，查詢 HealthKit。 使用此資訊，應用程式可以從測驗資料產生圖形，並將其繪製于測驗時間軸上。

若要讓應用程式參與活動應用程式的行動電話，它必須包含已儲存測驗的能源範例。 此外，距離和能源的總計必須符合應用程式與儲存的測驗相關聯之任何樣本的總和。

若要將範例新增至已儲存的測驗，請執行下列動作：

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

（選擇性）應用程式可以計算並建立較小的樣本子集或一個百萬位元樣本 (橫跨測驗) 的整個範圍，然後與已儲存的測驗相關聯。

## <a name="workouts-and-ios-10"></a>Workouts 和 iOS 10

每個 watchOS 3 測驗應用程式都有以 iOS 10 為基礎的上層測驗應用程式，以及 iOS 10 的新功能，此 iOS 應用程式可用來開始將 Apple Watch 放在測驗模式 (不需要使用者介入的測驗) 並在背景執行模式中執行 watchOS 應用程式 (如需詳細) 資料，請參閱上面執行的 [背景](#about-background-running) 。

當 watchOS 應用程式正在執行時，它可以使用 WatchConnectivity 來進行訊息和與父 iOS 應用程式的通訊。

請看看這個程式的運作方式：

[![iPhone 和 Apple Watch 通訊圖表](workout-apps-images/workout06.png)](workout-apps-images/workout06.png#lightbox)

1. IPhone 應用程式會建立 `HKWorkoutConfiguration` 物件，並設定測驗類型和位置。
2. `HKWorkoutConfiguration`物件會傳送 Apple Watch 的應用程式版本，如果尚未執行，系統就會將它啟動。
3. WatchOS 3 應用程式會使用通過的測驗設定來啟動新的測驗會話 (`HKWorkoutSession`) 。

> [!IMPORTANT]
> 為了讓上層 iPhone 應用程式在 Apple Watch 上啟動測驗，watchOS 3 應用程式必須已啟用背景執行。 如需詳細資料，請參閱啟用上述的 [背景](#enabling-background-running) 內容。

此程式與直接在 watchOS 3 應用程式中啟動測驗會話的程式非常類似。 在 iPhone 上，使用下列程式碼：

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

這段程式碼可確保已安裝應用程式的 watchOS 版本，且 iPhone 版本可先連接到該版本：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

然後，它會 `HKWorkoutConfiguration` 如往常般建立，並使用的 `StartWatchApp` 方法將 `HKHealthStore` 它傳送至 Apple Watch，然後啟動應用程式和測驗會話。

在監看式操作系統應用程式上，使用下列程式碼 `WKExtensionDelegate` ：

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

它會取得 `HKWorkoutConfiguration` 並建立新的 `HKWorkoutSession` ，並附加自訂的實例 `HKWorkoutSessionDelegate` 。 測驗會話是針對使用者的 HealthKit Health Store 啟動的。

## <a name="bringing-all-the-pieces-together"></a>將所有元件彙集在一起

取得本檔中顯示的所有資訊，以 watchOS 3 為基礎的測驗應用程式及其以上層 iOS 10 為基礎的測驗應用程式可能包含下列部分：

1. **iOS 10 `ViewController.cs` **-處理 Apple Watch 的監看式連線會話開始和測驗。
2. **watchOS 3 `ExtensionDelegate.cs` **-處理測驗應用程式的 watchOS 3 版本。
3. **watchOS 3 `OutdoorRunDelegate.cs` **- `HKWorkoutSessionDelegate` 用於處理測驗事件的自訂。

> [!IMPORTANT]
> 下列各節所顯示的程式碼僅包含在 watchOS 3 中執行適用于健身應用程式之新增強功能所需的元件。 不包含所有支援的程式碼和要呈現和更新 UI 的程式碼，但可透過遵循我們其他的 watchOS 檔來輕鬆建立。<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`應用程式的上層 iOS 10 版本中的檔案包含下列程式碼：

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

`ExtensionDelegate.cs`WatchOS 3 版測驗應用程式中的檔案包含下列程式碼：

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

`OutdoorRunDelegate.cs`WatchOS 3 版測驗應用程式中的檔案包含下列程式碼：

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

## <a name="best-practices"></a>最佳做法

Apple 建議在 watchOS 3 和 iOS 10 中設計和實行測驗應用程式時使用下列最佳作法：

- 確定 watchOS 3 測驗應用程式即使無法連線到 iPhone 和 iOS 10 版本的應用程式，仍可正常運作。
- 當 GPS 無法使用時，請使用 HealthKit 距離，因為它可以產生沒有 GPS 的距離樣本。
- 允許使用者從 Apple Watch 或 iPhone 啟動測驗。
- 允許應用程式顯示來自其他來源的 workouts， (例如，其他協力廠商應用程式) 在其歷程記錄資料檢視中。
- 確定應用程式不會在歷程記錄資料中顯示已刪除的 workouts。

## <a name="summary"></a>摘要

本文涵蓋了 Apple 對 watchOS 3 中的測驗應用程式所做的增強功能，以及如何在 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [HealthKit 簡介](~/ios/platform/healthkit.md)