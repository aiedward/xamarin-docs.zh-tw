---
title: 在 Xamarin 中 watchOS 健身應用程式
description: 本文涵蓋 Apple 對 watchOS 3 中的健身應用程式所做的增強，以及如何在 Xamarin 中執行。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c4fc1607667dd6201c28c4d00a2938760e429f0f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938979"
---
# <a name="watchos-workout-apps-in-xamarin"></a>在 Xamarin 中 watchOS 健身應用程式

_本文涵蓋 Apple 對 watchOS 3 中的健身應用程式所做的增強，以及如何在 Xamarin 中執行。_

WatchOS 3 的新功能：健身相關應用程式可在 Apple Watch 的背景中執行，並取得 HealthKit 資料的存取權。 其父系 iOS 10 型應用程式也能夠啟動 watchOS 3 型應用程式，而不需要使用者介入。

將會詳細介紹下列主題：

## <a name="about-workout-apps"></a>關於健身應用程式

健身和健身應用程式的使用者可以高度專用，專幾個小時的健康狀態和健身目標。 因此，他們預期有回應、容易使用的應用程式，可正確地收集和顯示資料，並與 Apple Health 緊密整合。

設計良好的健身或健身應用程式可協助使用者建立活動的圖表，以達到其健身目標。 藉由使用 Apple Watch，健身和健身應用程式就能立即存取核心費率、calorie 燒錄和活動偵測。

[![健身和健身應用程式範例](workout-apps-images/workout01.png)](workout-apps-images/workout01.png#lightbox)

WatchOS 3 的新功能：_背景_執行可讓健身相關應用程式在 Apple Watch 的背景中執行，並取得 HealthKit 資料的存取權。

本檔將介紹背景執行的功能、涵蓋健身應用程式生命週期，以及示範健身應用程式如何在 Apple Watch 上參與使用者的_活動環_。

## <a name="about-workout-sessions"></a>關於健身課程

每個測驗應用程式的核心是_Workout Session_ `HKWorkoutSession` 使用者可以啟動和停止的健身會話（）。 健身會話 API 很容易實行，並為健身應用程式提供數個優點，例如：

- 根據活動類型的動作和 calorie 燒錄偵測。
- 使用者活動環形的自動投稿。
- 在會話中，每當使用者喚醒裝置時（藉由提高手腕或與 Apple Watch 互動），就會自動顯示應用程式。

## <a name="about-background-running"></a>關於背景執行

如上所述，使用 watchOS 3，可以將健身應用程式設定為在背景中執行。 使用執行健身應用程式的背景，可以在背景中執行時，處理來自 Apple Watch 感應器的資料。 例如，應用程式可以繼續監視使用者的核心速率，即使它不會再顯示在螢幕上也一樣。

執行的背景也可讓您在使用中的健身會話期間隨時向使用者呈現即時回饋，例如傳送 haptic 警示，以通知使用者目前的進度。

此外，背景執行可讓應用程式快速更新其使用者介面，讓使用者在快速概覽其 Apple Watch 時擁有最新的資料。

為了維持 Apple Watch 的高效能，使用背景執行的監看式應用程式應該限制背景工作量以節省電池。 如果應用程式在背景中使用過多的 CPU，則 watchOS 會將它暫停。

### <a name="enabling-background-running"></a>正在啟用背景執行

若要啟用背景，請執行下列動作：

1. 在 [**方案總管**中，按兩下 [監看式] 延伸模組的隨附 iPhone 應用程式檔案， `Info.plist` 將其開啟以供編輯。
2. 切換至 [**來源**] 視圖： 

    [![來源視圖](workout-apps-images/plist01.png)](workout-apps-images/plist01.png#lightbox)
3. 新增名為的新金鑰 `WKBackgroundModes` ，並將**類型**設定為 `Array` ： 

    [![新增名為 WKBackgroundModes 的金鑰](workout-apps-images/plist02.png)](workout-apps-images/plist02.png#lightbox)
4. 將新專案加入**至型別為的陣列** `String` ，並將值設為 `workout-processing` ： 

    [![將新專案新增至具有字串類型的陣列和健身處理的值](workout-apps-images/plist03.png)](workout-apps-images/plist03.png#lightbox)
5. 將變更儲存至檔案。

## <a name="starting-a-workout-session"></a>啟動健身會話

啟動健身會話有三個主要步驟：

[![啟動健身會話的三個主要步驟](workout-apps-images/workout02.png)](workout-apps-images/workout02.png#lightbox)

1. 應用程式必須要求授權，才能存取 HealthKit 中的資料。
2. 為正在啟動的健身類型建立健身設定物件。
3. 使用新建立的健身設定來建立並啟動健身會話。

### <a name="requesting-authorization"></a>要求授權

在應用程式可以存取使用者的 HealthKit 資料之前，必須先向使用者要求並接收授權。 視健身應用程式的本質而定，它可能會產生下列類型的要求：

- 寫入資料的授權：
  - Workouts
- 讀取資料的授權：
  - 燒錄的能源
  - 距離
  - 核心速率  

在應用程式可以要求授權之前，必須先將它設定為存取 HealthKit。

執行下列動作：

1. 在方案總管**** 中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
2. 向下流覽並核取 [**啟用 HealthKit**]： 

    [![勾選 [啟用 HealthKit]](workout-apps-images/auth01.png)](workout-apps-images/auth01.png#lightbox)
3. 將變更儲存至檔案。
4. 請遵循[明確應用程式識別碼和布建設定檔](~/ios/platform/healthkit.md)中的指示，並將[應用程式識別碼和布建設定檔與您](~/ios/platform/healthkit.md)的[HealthKit 文章簡介](~/ios/platform/healthkit.md)中的 Xamarin 應用程式章節建立關聯，以正確布建應用程式。
5. 最後，請使用程式[設計健全狀況套件](~/ios/platform/healthkit.md)中的指示，並向[HealthKit 簡介](~/ios/platform/healthkit.md)文章的[使用者區段要求許可權](~/ios/platform/healthkit.md)，以要求授權存取使用者的 HealthKit 資料存放區。

### <a name="setting-the-workout-configuration"></a>設定健身設定

健身會話是使用健身設定物件（）所建立 `HKWorkoutConfiguration` ，可指定健身類型（例如 `HKWorkoutActivityType.Running` ）和健身位置（例如 `HKWorkoutSessionLocationType.Outdoor` ）：

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>建立健身會話委派 

若要處理在健身會話期間可能發生的事件，應用程式將需要建立健身會話委派實例。 將新類別新增至專案，並以類別為基礎 `HKWorkoutSessionDelegate` 。 針對戶外執行的範例，它看起來可能如下所示：

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

此類別會建立數個事件，以在健身會話的狀態變更時引發（ `DidChangeToState` ），而且如果健身會話失敗（ `DidFail` ）。 

### <a name="creating-a-workout-session"></a>建立健身會話

使用上面建立的健身設定和健身會話委派來建立新的健身會話，並針對使用者的預設 HealthKit 存放區啟動它：

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

如果應用程式啟動此健身會話，而使用者切換回其監看面，表面上會顯示一個小綠色的「執行中」圖示：

[![表面上顯示的小綠色執行中的男士圖示](workout-apps-images/workout03.png)](workout-apps-images/workout03.png#lightbox)

如果使用者按下此圖示，將會被帶回應用程式。

## <a name="data-collection-and-control"></a>資料收集與控制

設定並啟動測驗會話之後，應用程式將需要收集會話的相關資料（例如使用者的核心速率），並控制會話的狀態：

[![資料收集與控制圖表](workout-apps-images/workout04.png)](workout-apps-images/workout04.png#lightbox)

1. **觀察樣本**-應用程式將需要從 HealthKit 取得將會對使用者採取動作並向其顯示的資訊。
2. **觀察事件**-應用程式必須回應 HealthKit 或應用程式 UI （例如，暫停測驗的使用者）所產生的事件。
3. **進入執行狀態**-會話已啟動且目前正在執行。
4. **進入暫停狀態**-使用者已暫停目前的健身會話，並可在日後重新開機。 使用者可以在單一健身會話中多次切換執行中與已暫停狀態。
5. **結束健身會話**-使用者可以在任何時間點結束健身會話，或如果它是計量付費的健身（例如兩英里執行），則可能會到期並自行結束。

最後一個步驟是將健身會話的結果儲存到使用者的 HealthKit 資料存放區。

### <a name="observing-healthkit-samples"></a>觀察 HealthKit 範例

應用程式必須為每個感興趣的 HealthKit 資料點開啟_錨點物件查詢_，例如，心率或活躍的能源燒錄。 針對觀察到的每個資料點，將需要建立更新處理常式，以在傳送至應用程式時捕捉新資料。

應用程式可以從這些資料點累積總計（例如總執行距離），並視需要更新其使用者介面。 此外，應用程式可以在達到特定目標或成就（例如完成下一英里的執行）時，通知使用者。

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

它會建立述詞，以設定它想要使用方法取得資料的開始日期 `GetPredicateForSamples` 。 它會建立一組裝置，以使用方法從提取 HealthKit 資訊 `GetPredicateForObjectsFromDevices` ，在此案例中為僅限本機 Apple Watch （ `HKDevice.LocalDevice` ）。 使用方法，將兩個述詞結合成複合述詞（ `NSCompoundPredicate` ） `CreateAndPredicate` 。

`HKAnchoredObjectQuery`系統會針對所需的資料點（在此案例中 `HKQuantityTypeIdentifier.ActiveEnergyBurned` 為使用中的能源燒錄資料點）建立新的，並不會限制傳回的資料量（ `HKSampleQuery.NoLimit` ），而且會定義更新處理常式來處理從 HealthKit 傳回到應用程式的資料。 

每當有新的資料傳遞給指定資料點的應用程式時，就會呼叫更新處理常式。 如果未傳回任何錯誤，應用程式可以安全地讀取資料、進行任何必要的計算，並視需要更新其 UI。

程式碼會在陣列中傳回的所有樣本（）上迴圈 `HKSample` `addedObjects` ，並將它們轉換成數量範例（ `HKQuantitySample` ）。 然後，它會以 joule （）的形式取得樣本的雙精度浮點數 `HKUnit.Joule` ，並將其累積為適用于健身的總使用中能源，並更新使用者介面。

### <a name="achieved-goal-notification"></a>達到的目標通知

如上所述，當使用者在健身應用程式中達成目標時（例如，完成第一次執行），它可以透過 Taptic Engine 傳送 haptic 的意見反應給使用者。 應用程式此時也應該更新它的 UI，因為使用者可能會提高手腕，以查看提示意見反應的事件。

若要播放 haptic 的意見反應，請使用下列程式碼：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>觀察事件

事件是一種時間戳記，應用程式可以用來在使用者的健身期間反白顯示特定點。 有些事件會直接由應用程式建立並儲存到健身中，而某些事件會由 HealthKit 自動建立。

為了觀察 HealthKit 所建立的事件，應用程式將會覆寫的 `DidGenerateEvent` 方法 `HKWorkoutSessionDelegate` ：

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

Apple 已在 watchOS 3 中新增下列新事件種類：

- `HKWorkoutEventType.Lap`-適用于將健身細分為相等距離部分的事件。 例如，在執行時，將一個膝上標示為在一個軌道周圍。
- `HKWorkoutEventType.Marker`-適用于健身內的任意點。 例如，到達室外執行路由的特定點。

這些新類型可以由應用程式建立並儲存在健身中，以供稍後用來建立圖表和統計資料。

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

此程式碼會建立標記事件的新實例（ `HKWorkoutEvent` ），並將它儲存至事件的私用集合（稍後會寫入至健身會話），並透過 haptics 通知使用者事件。

### <a name="pausing-and-resuming-workouts"></a>暫停與繼續 Workouts

在健身會話中的任何時間點，使用者都可以暫時暫停測驗並于稍後繼續。 例如，他們可能會暫停室內執行，以進行一項重要的呼叫，並在呼叫完成後繼續執行。

應用程式的 UI 應該提供一種方法來暫停和繼續測驗（藉由呼叫 HealthKit），如此一來，Apple Watch 可以在使用者暫停其活動時，同時保留電源和資料空間。 此外，當健身會話處於暫停狀態時，應用程式應該忽略任何可能收到的新資料點。

HealthKit 會藉由產生暫停和繼續事件來回應暫停和繼續呼叫。 當健身會話暫停時，在會話繼續之前，HealthKit 不會傳送任何新的事件或資料到應用程式。

使用下列程式碼來暫停和繼續健身會話：

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

也是 watchOS 3 的新手，也就是動作已暫停（ `HKWorkoutEventType.MotionPaused` ）和動作繼續（ `HKWorkoutEventType.MotionResumed` ）事件。 當使用者開始並停止移動時，HealthKit 會在執行中的測驗期間自動引發這些事件。

當應用程式收到「動作已暫停」事件時，它應該會停止收集資料，直到使用者繼續移動並收到「動作繼續」事件為止。 應用程式不應暫停健身會話，以回應動作暫停事件。

> [!IMPORTANT]
> 只有 RunningWorkout 活動類型（）支援動作已暫停和動作繼續事件 `HKWorkoutActivityType.Running` 。

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

## <a name="ending-and-saving-the-workout-session"></a>結束和儲存健身會話

當使用者完成測驗時，應用程式將需要結束目前的健身會話，並將它儲存至 HealthKit 資料庫。 儲存至 HealthKit 的 Workouts 會自動顯示在 [健身活動] 清單中。

IOS 10 的新手，這也包括使用者 iPhone 上的健身活動清單清單。 因此，即使 Apple Watch 不在附近，測驗也會顯示在電話上。

包含能源範例的 Workouts 會更新活動應用程式中的使用者行動電話，因此協力廠商應用程式現在可以參與使用者的每日移動目標。

若要結束並儲存健身會話，必須執行下列步驟：

[![結束和儲存健身會話圖表](workout-apps-images/workout05.png)](workout-apps-images/workout05.png#lightbox)

1. 首先，應用程式將需要結束健身會話。
2. 健身會話會儲存至 HealthKit。
3. 將任何範例（例如能源燒錄或距離）新增至已儲存的健身會話。

### <a name="ending-the-session"></a>結束會話

若要結束健身會話，請呼叫 `EndWorkoutSession` 傳入的方法 `HKHealthStore` `HKWorkoutSession` ：

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

這會將裝置感應器重設為正常模式。 當 HealthKit 完成測驗時，它會收到的 `DidChangeToState` 方法回呼 `HKWorkoutSessionDelegate` ：

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

一旦應用程式結束了測驗會話，就必須建立健身（ `HKWorkout` ）並將它（以及事件）儲存到 HealthKit 資料存放區（ `HKHealthStore` ）：

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

這段程式碼會建立需要將測驗的總能源和距離作為 `HKQuantity` 物件。 會建立定義健身的元資料字典，並指定健身的位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

新 `HKWorkout` 物件的建立方式，會與 `HKWorkoutActivityType` `HKWorkoutSession` 、開始和結束日期、事件清單（從上述各節累積）、能源燒錄量、總距離和元資料字典相同。 此物件會儲存至健康狀態存放區，並處理任何錯誤。  

### <a name="adding-samples"></a>新增範例

當應用程式將一組範例儲存至健身時，HealthKit 會產生範例和健身本身之間的連線，讓應用程式可以在日後針對與特定健身相關聯的所有樣本進行查詢 HealthKit。 應用程式可以使用此資訊，從健身資料產生圖形，並根據健身時間軸繪製圖表。

若要讓應用程式參與活動應用程式的移動環，它必須包含具有已儲存之健身的能源範例。 此外，距離和能源的總計必須符合應用程式與已儲存的健身相關聯之任何樣本的總和。

若要將範例新增至已儲存的健身，請執行下列動作：

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

（選擇性）應用程式可以計算並建立較小的樣本子集或一或一張百萬位元樣本（橫跨整個測驗範圍），然後與已儲存的健身相關聯。

## <a name="workouts-and-ios-10"></a>Workouts 和 iOS 10

每個 watchOS 3 健身應用程式都有以 iOS 10 為基礎的父系應用程式，而這是 iOS 10 的新手，此 iOS 應用程式可用來啟動將 Apple Watch 放在健身模式中的健身（不需要使用者介入），並在背景執行模式中執行 watchOS 應用程式（如需詳細資訊，請參閱關於在上面執行的[背景](#about-background-running)）。

當 watchOS 應用程式正在執行時，它可以使用 WatchConnectivity 來進行訊息處理，並與父系 iOS 應用程式通訊。

請看一下這個程式的運作方式：

[![iPhone 和 Apple Watch 通訊圖表](workout-apps-images/workout06.png)](workout-apps-images/workout06.png#lightbox)

1. IPhone 應用程式會建立 `HKWorkoutConfiguration` 物件，並設定健身類型和位置。
2. `HKWorkoutConfiguration`物件會傳送 Apple Watch 版的應用程式，如果尚未執行，則會由系統啟動。
3. WatchOS 3 應用程式會使用傳入的健身設定，啟動新的健身會話（ `HKWorkoutSession` ）。

> [!IMPORTANT]
> 為了讓父 iPhone 應用程式啟動 Apple Watch 的健身，watchOS 3 應用程式必須啟用背景。 如需詳細資訊，請參閱啟用上面的[背景](#enabling-background-running)執行。

此流程與直接在 watchOS 3 應用程式中啟動健身會話的流程非常類似。 在 iPhone 上，使用下列程式碼：

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

此程式碼可確保已安裝 watchOS 版的應用程式，且 iPhone 版本可以先連線到該版本：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

然後，它會 `HKWorkoutConfiguration` 照常建立，並使用的 `StartWatchApp` 方法將 `HKHealthStore` 它傳送至 Apple Watch 並啟動應用程式和健身會話。

在監看式 OS 應用程式上，使用中的下列程式碼 `WKExtensionDelegate` ：

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

它會採用 `HKWorkoutConfiguration` ，並建立新的 `HKWorkoutSession` 並附加自訂的實例 `HKWorkoutSessionDelegate` 。 健身會話會針對使用者的 HealthKit 健康狀態存放區啟動。

## <a name="bringing-all-the-pieces-together"></a>將所有元件結合在一起

採用本檔所提供的所有資訊，watchOS 3 型健身應用程式及其以父系 iOS 10 為基礎的健身應用程式可能包括下列部分：

1. **iOS 10 `ViewController.cs` **-處理從監看式連線會話開始，以及 Apple Watch 上的健身。
2. **watchOS 3 `ExtensionDelegate.cs` **-處理 watchOS 3 版本的健身應用程式。
3. **watchOS 3 `OutdoorRunDelegate.cs` **-用 `HKWorkoutSessionDelegate` 來處理健身事件的自訂。

> [!IMPORTANT]
> 下列各節所顯示的程式碼只包含在 watchOS 3 中執行提供給健身應用程式的新增強功能所需的元件。 不包含所有支援的程式碼和呈現和更新 UI 的程式碼，但可以遵循我們的其他 watchOS 檔來輕鬆建立。<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`應用程式的父系 iOS 10 版本中的檔案會包含下列程式碼：

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

`ExtensionDelegate.cs`WatchOS 3 版本的健身應用程式中的檔案會包含下列程式碼：

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

`OutdoorRunDelegate.cs`WatchOS 3 版本的健身應用程式中的檔案會包含下列程式碼：

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

在 watchOS 3 和 iOS 10 中設計和執行健身應用程式時，Apple 建議使用下列最佳作法：

- 確定 watchOS 3 健身應用程式仍可正常運作，即使無法連線到 iPhone 和 iOS 10 版本的應用程式也一樣。
- 當 GPS 無法使用時，請使用 HealthKit 距離，因為它能夠在沒有 GPS 的情況下產生距離樣本。
- 允許使用者從 Apple Watch 或 iPhone 啟動健身。
- 允許應用程式在其歷程記錄資料檢視中顯示其他來源的 workouts （例如其他協力廠商應用程式）。
- 請確定應用程式不會在歷程記錄資料中顯示已刪除的 workouts。

## <a name="summary"></a>總結

本文涵蓋了 Apple 對 watchOS 3 中的健身應用程式所做的增強，以及如何在 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
