---
title: 在 Xamarin 中 watchOS 背景工作
description: 本檔說明如何在 Xamarin 中搭配 watchOS 使用背景工作、查看背景工作類型、使用資源、執行背景工作、排程、最佳做法等等。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/13/2017
ms.openlocfilehash: 0ca65c56a742aaf23874f581b976ef50b3d16192
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574336"
---
# <a name="watchos-background-tasks-in-xamarin"></a>在 Xamarin 中 watchOS 背景工作

有了 watchOS 3，監看式應用程式可將其資訊保持在最新狀態的主要方式有三種： 

- 使用其中一項新的背景工作。 
- 在監看表面上有其中一項複雜的問題（提供更多時間來更新）。 
- 讓使用者將應用程式釘選到新的 Dock （其保留在記憶體中並經常更新）。 

## <a name="keeping-an-app-up-to-date"></a>讓應用程式保持在最新狀態

在討論開發人員可以將 watchOS 應用程式的資料和使用者介面保持在最新和更新的所有方式之前，本節將探討一組典型的使用模式，以及使用者在一天內如何根據當天時間和目前執行的活動（例如駕駛），在其上移動 iPhone 及其 Apple Watch。

請使用以下範例：

[![](background-tasks-images/update00.png "How a user might move between their iPhone and their Apple Watch throughout the day")](background-tasks-images/update00.png#lightbox)

1. 在早上，當等待咖啡時，使用者會在 iPhone 上流覽目前的新聞數分鐘。
2. 離開咖啡廳之前，他們會快速檢查天氣表面上是否有複雜的氣象。
3. 在午餐之前，他們會使用 iPhone 上的 Maps 應用程式來尋找附近的餐廳，並預訂預約以符合用戶端。
4. 當旅行到餐廳時，他們會在他們的 Apple Watch 上收到通知，並快速概覽，他們的午餐約會已順延強制。
5. 在晚上，他們會使用 iPhone 上的 Maps 應用程式來檢查流量，然後再駕駛回家。
6. 在家中，他們會在其 Apple Watch 收到 iMessage 通知，要求他們挑選一些牛奶，並使用快速回復功能來傳送回應「確定」。

由於使用者想要使用 Apple Watch 應用程式的「快速概覽」（少於三秒）本質，應用程式通常不會有足夠的時間來提取所需的資訊並更新其 UI，然後才向使用者顯示。

藉由使用 Apple 隨附于 watchOS 3 的新 Api，應用程式可以排程_背景_重新整理，並在使用者要求之前準備好所需的資訊。 請看上面討論過的天氣複雜範例：

[![](background-tasks-images/update01.png "An example of the Weather Complication")](background-tasks-images/update01.png#lightbox)

1. 應用程式會在特定時間由系統喚醒。 
2. 應用程式會提取產生更新所需的資訊。
3. 應用程式會重新產生其使用者介面，以反映新的資料。
4. 當使用者深入瞭解應用程式的複雜問題時，就會有最新的資訊，而不需要使用者等待更新。

如上所示，watchOS 系統會使用一或多個工作喚醒應用程式，其中有非常有限的集區可用：

[![](background-tasks-images/update02.png "The watchOS system wakes the app using one or more Tasks")](background-tasks-images/update02.png#lightbox)

Apple 建議您將這項工作（因為這對應用程式而言是有限的資源）放在應用程式完成更新本身的過程中，以充分發揮此工作的上限。

系統會藉由呼叫委派的新方法來傳遞這些工作 `HandleBackgroundTasks` `WKExtensionDelegate` 。 例如：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Constructors
    public ExtensionDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request here
      ...
    }
    #endregion
  }
}
```

當應用程式完成指定的工作時，它會將它標示為已完成，以將它傳回給系統：

[![](background-tasks-images/update03.png "The Task returns to the system by marking it completed")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks"></a>

## <a name="new-background-tasks"></a>新的背景工作

watchOS 3 引進數個背景工作，可讓應用程式用來更新其資訊，確保在開啟應用程式之前，它擁有使用者所需的內容，例如：

- **背景應用程式**重新整理- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可讓應用程式在背景中更新其狀態。 通常這會包含另一項工作，例如使用[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)從網際網路下載新內容。
- **背景快照**集重新整理- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可讓應用程式在系統取得將用來填入 Dock 的快照之前，更新其內容和 UI。
- **背景監看**連線-當應用程式從配對的 iPhone 收到背景資料時，會啟動[WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)工作。
- **背景 URL 會話**-背景傳輸需要授權或完成（成功或發生錯誤）時，會為應用程式啟動[WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)工作。

下列各節將詳細討論這些工作。

<a name="WKApplicationRefreshBackgroundTask"></a>

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是可排程在未來日期喚醒應用程式的一般工作：

[![](background-tasks-images/update04.png "A WKApplicationRefreshBackgroundTask woken at a future date")](background-tasks-images/update04.png#lightbox)

在工作的執行時間中，應用程式可以執行任何一種本機處理，例如更新複雜的時程表，或使用來提取一些必要的資料 `NSUrlSession` 。

<a name="WKURLSessionRefreshBackgroundTask"></a>

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

`WKURLSessionRefreshBackgroundTask`當資料完成下載並準備好供應用程式處理時，系統會傳送。

[![](background-tasks-images/update05.png "The WKURLSessionRefreshBackgroundTask when the data has finished downloading")](background-tasks-images/update05.png#lightbox)

當資料在背景下載時，應用程式不會保持執行狀態。 相反地，應用程式會排程資料的要求，然後將它暫停，而且系統會處理資料的下載，只會在下載完成時 reawakening 應用程式。

<a name="WKSnapshotRefreshBackgroundTask"></a>

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後點一下應用程式來啟動它，將快照集取代為執行中應用程式的介面。

[![](background-tasks-images/update06.png "Replacing the Snapshot with the running apps interface")](background-tasks-images/update06.png#lightbox)

系統會定期取得應用程式 UI （藉由傳送）的快照集 `WKSnapshotRefreshBackgroundTask` ，並使用這些快照集來填入 Dock。 watchOS 可讓應用程式在取得此快照集之前，更新其內容和 UI 的機會。

快照集在 watchOS 3 中非常重要，因為它們會當做應用程式的預覽和啟動映射。 如果使用者在停駐中的應用程式上執行，它會展開為全螢幕、輸入前景並開始執行，因此快照集必須是最新狀態：

[![](background-tasks-images/update07.png "If the user settles on an app in the Dock, it will expand to full screen")](background-tasks-images/update07.png#lightbox)

同樣地，系統會發出， `WKSnapshotRefreshBackgroundTask` 讓應用程式可以在建立快照集之前準備（藉由更新資料和 UI）：

[![](background-tasks-images/update08.png "The app can prepare by updating the data and the UI before the snapshot is taken")](background-tasks-images/update08.png#lightbox)

當應用程式標示為 `WKSnapshotRefreshBackgroundTask` 已完成時，系統會自動建立應用程式 UI 的快照。

> [!IMPORTANT]
> 請務必在 `WKSnapshotRefreshBackgroundTask` 應用程式收到新資料並更新其使用者介面之後，一律排程，否則使用者將不會看到修改過的資訊。

此外，當使用者從應用程式收到通知，並將應用程式帶到前景時，快照集必須是最新狀態，因為它也會作為啟動畫面：

[![](background-tasks-images/update09.png "The user receives a notification from the app and taps it to bring the app to the foreground")](background-tasks-images/update09.png#lightbox)

如果因為使用者與 watchOS 應用程式互動而超過一小時，它將能夠回到其預設狀態。 預設狀態對不同的應用程式可能會有不同的意義，而且根據應用程式的設計，它可能完全沒有預設狀態。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask"></a>

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

在 watchOS 3 中，Apple 透過新的，整合了監看與背景重新整理 API 的連線 `WKWatchConnectivityRefreshBackgroundTask` 。 使用這項新功能，iPhone 應用程式可以在背景中執行 watchOS 應用程式時，將最新的資料傳遞至其監看式應用程式對應項：

[![](background-tasks-images/update10.png "An iPhone app can deliver fresh data to its watch app counterpart, while the watchOS app is running in the background")](background-tasks-images/update10.png#lightbox)

起始複雜的推送、應用程式內容、從 iPhone 應用程式傳送檔案或更新使用者資訊，將會在背景喚醒 Apple Watch 應用程式。

透過喚醒監看式應用程式時， `WKWatchConnectivityRefreshBackgroundTask` 必須使用標準 API 方法來接收 iPhone 應用程式的資料。

[![](background-tasks-images/update11.png "The WKWatchConnectivityRefreshBackgroundTask data flow")](background-tasks-images/update11.png#lightbox)

1. 確定會話已啟用。
2. 監視新的 `HasContentPending` 屬性，只要值為 `true` ，應用程式仍會有要處理的資料。 如先前所述，應用程式必須先保留工作，直到處理完所有資料為止。
3. 當沒有其他要處理的資料（）時 `HasContentPending = false` ，請將工作標示為已完成，以將它傳回系統。 如果無法這麼做，將會耗盡應用程式佈建的背景執行時間，而導致當機報告。

<a name="The-Background-API-Lifecycle"></a>

## <a name="the-background-api-lifecycle"></a>背景 API 生命週期

將所有新的背景工作 API 放在一起，一般的互動集如下所示：

[![](background-tasks-images/update12.png "The Background API Lifecycle")](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 應用程式會排程背景工作，以在未來的某個時間點提醒于。
2. 系統會喚醒應用程式並傳送工作。
3. 應用程式會處理工作，以完成所需的任何工作。
4. 由於處理工作的結果，應用程式可能需要排程更多背景工作，以在未來完成更多工作，例如使用下載更多內容 `NSUrlSession` 。
5. 應用程式會將工作標示為已完成，並將它傳回系統。

<a name="Using-Resources-Responsibly"></a>

## <a name="using-resources-responsibly"></a>使用資源的責任

WatchOS 應用程式在此生態系統中的工作，藉由限制其對系統共用資源的清空，這是很重要的。

請查看下列案例：

[![](background-tasks-images/update13.png "A watchOS app limits its drain on the system's shared resources")](background-tasks-images/update13.png#lightbox)

1. 使用者會在下午1:00 啟動 watchOS 應用程式。
2. 應用程式會排程工作，以在下午2:00 的一小時內喚醒並下載新的內容。
3. 在下午1:50，使用者重新開啟應用程式，以允許它在此時更新其資料和 UI。
4. 應用程式不會讓工作在10分鐘內再次喚醒應用程式，而應重新排定工作，使其在下午2:50 執行一小時。

雖然每個應用程式都不同，但 Apple 建議尋找使用模式（如上面所示），以協助節省系統資源。

<a name="Implementing-Background-Tasks"></a>

## <a name="implementing-background-tasks"></a>執行背景工作

舉例來說，這份檔會使用向使用者報告足球分數的假 MonkeySoccer 運動應用程式。 

請參閱下列一般使用案例：

[![](background-tasks-images/update14.png "The typical usage scenario")](background-tasks-images/update14.png#lightbox)

使用者的我的最愛足球團隊現正播放比下午7:00 到 9:00 PM 的大型比對，因此應用程式應該會預期使用者會定期檢查分數，並決定在30分鐘的更新間隔中。

1. 使用者會開啟應用程式，並排程背景更新30分鐘後的工作。 背景 API 只允許在指定的時間執行一種背景工作。
2. 應用程式會接收工作並更新其資料和 UI，然後在30分鐘後排程另一個背景工作。 開發人員必須記得排定另一個背景工作，否則應用程式永遠不會重新喚醒以取得更多更新。
3. 同樣地，應用程式會收到工作並更新其資料、更新其 UI，並在30分鐘後排程另一個背景工作。
4. 相同的程式會再次重複。
5. 最後會收到背景工作，而應用程式會再次更新其資料和 UI。 因為這是最後的分數，所以不會針對新的背景重新整理進行排程。 

<a name="Scheduling-for-Background-Update"></a>

## <a name="scheduling-for-background-update"></a>背景更新排程

在上述案例中，MonkeySoccer 應用程式可以使用下列程式碼來排程背景更新：

```csharp
private void ScheduleNextBackgroundUpdate ()
{
  // Create a fire date 30 minutes into the future
  var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

  // Create 
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
  userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

`NSDate`當應用程式想要喚醒時，它會建立新的30分鐘，並建立 `NSMutableDictionary` 以保存所要求之工作的詳細資料。 的 `ScheduleBackgroundRefresh` 方法 `SharedExtension` 是用來要求排定的工作。

如果無法排程要求的工作，系統將會傳回 `NSError` 。

<a name="Processing-the-Update"></a>

## <a name="processing-the-update"></a>處理更新

接下來，請仔細查看 [5 分鐘] 視窗，其中會顯示更新分數所需的步驟：

[![](background-tasks-images/update15.png "The 5 minute window showing the steps required to update the score")](background-tasks-images/update15.png#lightbox)

1. 在下午7:30:02，應用程式會由系統喚醒，並提供更新背景工作。 其第一個優先順序是從伺服器取得最新分數。 請參閱以下[的排程 NSUrlSession](#Scheduling-a-NSUrlSession) 。
2. 在7:30:05，應用程式會完成原始工作，系統會讓應用程式進入睡眠狀態，並繼續在背景下載要求的資料。
3. 當系統完成下載時，它會建立新的工作來喚醒應用程式，讓它可以處理下載的資訊。 請參閱以下的[處理背景](#Handling-Background-Tasks)[工作和處理下載的](#Handling-the-Download-Completing)作業。 
4. 應用程式會儲存已更新的資訊，並將工作標示為已完成。 開發人員此時可能會想要更新應用程式的使用者介面，但 Apple 建議排程快照集工作來處理該進程。 請參閱下面[的排程快照集更新](#Scheduling-a-Snapshot-Update)。
5. 應用程式會接收快照集工作、更新其使用者介面，並將工作標示為已完成。 請參閱下面[的處理快照集更新](#Handling-a-Snapshot-Update)。

<a name="Scheduling-a-NSUrlSession"></a>

## <a name="scheduling-a-nsurlsession"></a>排程 NSUrlSession

您可以使用下列程式碼來排程最新分數的下載：

```csharp
private void ScheduleURLUpdateSession ()
{
  // Create new configuration
  var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

  // Create new session
  var backgroundSession = NSUrlSession.FromConfiguration (configuration);

  // Create and start download task
  var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
  downloadTask.Resume ();
}
```

它會設定並建立新的 `NSUrlSession` ，然後使用該會話，使用方法來建立新的下載工作 `CreateDownloadTask` 。 它會呼叫 `Resume` 下載工作的方法來啟動會話。

<a name="Handling-Background-Tasks"></a>

## <a name="handling-background-tasks"></a>處理背景工作

藉由覆寫的 `HandleBackgroundTasks` 方法 `WKExtensionDelegate` ，應用程式可以處理傳入的背景工作：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
    #endregion

    ...
    
    #region Public Methods
    public void CompleteTask (WKRefreshBackgroundTask task)
    {
      // Mark the task completed and remove from the collection
      task.SetTaskCompleted ();
      PendingTasks.Remove (task);
    }
    #endregion 

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request
      foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Is this a background session task?
        var urlTask = task as WKUrlSessionRefreshBackgroundTask;
        if (urlTask != null) {
          // Create new configuration
          var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

          // Create new session
          var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

          // Keep track of all pending tasks
          PendingTasks.Add (task);
        } else {
          // Ensure that all tasks are completed
          task.SetTaskCompleted ();
        }
      }
    }
    #endregion
    
    ...
  }
}
```

`HandleBackgroundTasks`方法會迴圈執行系統已傳送應用程式的所有工作（在中），以 `backgroundTasks` 搜尋 `WKUrlSessionRefreshBackgroundTask` 。 如果找到一個，它會重新加入會話，並附加 `NSUrlSessionDownloadDelegate` 以處理下載完成（請參閱以下[的處理下載](#Handling-the-Download-Completing)）：

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

它會保留工作的控制碼，直到其完成，方法是將它新增至集合：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有傳送至應用程式的工作都必須完成，針對目前未處理的工作，將其標示為完成：

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing"></a>

## <a name="handling-the-download-completing"></a>處理下載完成

MonkeySoccer 應用程式會使用下列 `NSUrlSessionDownloadDelegate` 委派來處理下載完成和處理要求的資料：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
  {
    #region Computed Properties
    public ExtensionDelegate WatchExtensionDelegate { get; set; }

    public WKRefreshBackgroundTask Task { get; set; }
    #endregion

    #region Constructors
    public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
    {
      // Initialize
      this.WatchExtensionDelegate = extensionDelegate;
      this.Task = task;
    }
    #endregion

    #region Override Methods
    public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
    {
      // Handle the downloaded data
      ...

      // Mark the task completed
      WatchExtensionDelegate.CompleteTask (Task);

    }
    #endregion
  }
}
```

初始化時，它會將控制碼同時保留給產生 `ExtensionDelegate` 它的和 `WKRefreshBackgroundTask` 。 它會覆寫 `DidFinishDownloading` 方法來處理下載完成。 接著會使用的 `CompleteTask` 方法 `ExtensionDelegate` 來通知工作它已完成，並將它從擱置中工作的集合中移除。 請參閱處理上述的[背景](#Handling-Background-Tasks)工作。

<a name="Scheduling-a-Snapshot-Update"></a>

## <a name="scheduling-a-snapshot-update"></a>排程快照集更新

下列程式碼可以用來排程快照集工作，以最新分數更新 UI：

```csharp
private void ScheduleSnapshotUpdate ()
{
  // Create a fire date of now
  var fireDate = NSDate.FromTimeIntervalSinceNow (0);

  // Create user info dictionary
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
  userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

就像 `ScheduleURLUpdateSession` 上述的方法，它會建立新 `NSDate` 的（當應用程式想要喚醒時），並建立 `NSMutableDictionary` 來保存所要求之工作的詳細資料。 的 `ScheduleSnapshotRefresh` 方法 `SharedExtension` 是用來要求排定的工作。

如果無法排程要求的工作，系統將會傳回 `NSError` 。

<a name="Handling-a-Snapshot-Update"></a>

## <a name="handling-a-snapshot-update"></a>處理快照集更新

若要處理快照集工作， `HandleBackgroundTasks` 會修改方法（請參閱上述的[處理背景](#Handling-Background-Tasks)工作），如下所示：

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
  // Handle background request
  foreach (WKRefreshBackgroundTask task in backgroundTasks) {
    // Take action based on task type
    if (task is WKUrlSessionRefreshBackgroundTask) {
      var urlTask = task as WKUrlSessionRefreshBackgroundTask;

      // Create new configuration
      var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

      // Create new session
      var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

      // Keep track of all pending tasks
      PendingTasks.Add (task);
    } else if (task is WKSnapshotRefreshBackgroundTask) {
      var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

      // Update UI
      ...

      // Create a expiration date 30 minutes into the future
      var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

      // Create user info dictionary
      var userInfo = new NSMutableDictionary ();
      userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
      userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

      // Mark task complete
      snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
    } else {
      // Ensure that all tasks are completed
      task.SetTaskCompleted ();
    }
  }
}
```

方法會測試所處理的工作類型。 如果是， `WKSnapshotRefreshBackgroundTask` 它會取得工作的存取權：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

方法會更新使用者介面，然後建立 `NSDate` ，以便在快照集過時時告訴系統。 它會建立 `NSMutableDictionary` 具有使用者資訊的，以描述新的快照集，並使用這項資訊將快照集工作標示為已完成：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它也會告訴快照集工作，應用程式不會回到預設狀態（在第一個參數中）。 沒有預設狀態概念的應用程式應該一律將此屬性設定為 `true` 。

<a name="Working-Efficiently"></a>

## <a name="working-efficiently"></a>有效率地工作

如上述範例所示，MonkeySoccer 應用程式使用這五分鐘的時間範圍來更新其分數、有效率地使用新的 watchOS 3 背景工作，應用程式只會啟用15秒： 

[![](background-tasks-images/update16.png "The app was only active for a total of 15 seconds")](background-tasks-images/update16.png#lightbox)

這會降低應用程式對可用 Apple Watch 資源和電池壽命的影響，同時也可讓應用程式更適合在監看式上執行的其他應用程式。

<a name="How-Scheduling-Works"></a>

## <a name="how-scheduling-works"></a>排程的運作方式

當 watchOS 3 應用程式在前景時，它一律會排程執行，而且可以執行任何必要的處理類型，例如更新資料或重繪其 UI。 當應用程式移至背景時，通常會由系統暫停，而且所有執行時間作業都會停止。 

當應用程式在背景時，系統可能會將其設為目標，以快速執行特定工作。 因此，在 watchOS 2 中，系統可能會暫時喚醒背景應用程式來執行一些動作，例如處理長時間的外觀通知，或更新應用程式的複雜問題。 在 watchOS 3 中，有幾種新的方式可讓應用程式在背景中執行。

當應用程式在背景時，系統會對其施加數個限制：

- 只有幾秒鐘的時間才能完成任何指定的工作。 系統不僅會考慮已通過的時間量，也會考慮應用程式耗用多少 CPU 功能來衍生此限制。
- 任何超過其限制的應用程式將會終止，並出現下列錯誤代碼：
  - **CPU** -0xc51bad01
  - 0xc51bad02**時**
- 系統會根據它要求應用程式執行的背景工作類型來強加不同的限制。 例如， `WKApplicationRefreshBackgroundTask` 和工作 `WKURLSessionRefreshBackgroundTask` 會透過其他類型的背景工作，提供稍微較長的執行時間。

<a name="Complications-and-App-Updates"></a>

### <a name="complications-and-app-updates"></a>複雜和應用程式更新

除了 Apple 已新增至 watchOS 3 的新背景工作之外，watchOS 應用程式的複雜性也會影響應用程式接收背景更新的方式和時機。

複雜之處是小型視覺元素，提供有用的資訊一目了然。 根據所選的監看式臉部，使用者可以自訂監看面，其中包含一或多個可由 watchOS 3 中的監看式應用程式提供的複雜功能。

如果使用者在其監看表面上包含其中一個應用程式的複雜性，它會為應用程式提供下列更新的優點：

- 它會讓系統將應用程式保持在準備啟動的狀態，並在其中嘗試在背景啟動應用程式、將它保留在記憶體中，並提供更多時間來更新。
- 每日至少會保證有50個推播更新的複雜性。

開發人員應一律努力為其應用程式創造引人注目的複雜性，以根據上述原因將使用者加入其監看面。

在 watchOS 2 中，複雜性是應用程式在背景中接收執行時間的主要方式。 在 watchOS 3 中，仍然可以確保複雜的應用程式每小時接收多個更新，不過，它可以用 `WKExtensions` 來要求更多執行時間來更新其複雜性。

請參閱下列用來從已連線的 iPhone 應用程式更新複雜的程式碼：

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

  // Get session and the number of remaining transfers
  var session = WCSession.DefaultSession;
  var transfers = session.RemainingComplicationUserInfoTransfers;

  // Create user info dictionary
  var iconattrs = new Dictionary<NSString, NSObject>
    {
      {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
      {new NSString ("reason"), new NSString ("UpdateScore")}
    };

  var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

  // Take action based on the number of transfers left
  if (transfers < 1) {
    // No transfers left, either attempt to send or inform
    // user of situation.
    ...
  } else if (transfers < 11) {
    // Running low on transfers, only send on important updates
    // else conserve for a significant change.
    ...
  } else {
    // Send data
    session.TransferCurrentComplicationUserInfo (userInfo);
  }
}
```

它會使用的 `RemainingComplicationUserInfoTransfers` 屬性 `WCSession` 來查看應用程式已保留一天的高優先順序傳輸數，然後根據該數目採取動作。 如果應用程式開始執行的傳輸量不足，它可以關閉傳送次要更新的功能，而且只會在發生重大變更時傳送資訊。

<a name="Scheduling-and-Dock"></a>

### <a name="scheduling-and-the-dock"></a>排程和 Dock

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後點一下應用程式來啟動它，將快照集取代為執行中應用程式的介面。

[![](background-tasks-images/dock01.png "The Dock")](background-tasks-images/dock01.png#lightbox)

系統會定期取得應用程式 UI 的快照，並使用這些快照集來填入檔。watchOS 可讓應用程式在取得此快照集之前，更新其內容和 UI 的機會。

已釘選到 dock 的應用程式可能會預期下列各項：

- 每小時最少會收到一次更新。 這包括應用程式重新整理工作和快照集工作。
- 更新預算會分散于 Dock 中的所有應用程式之間。 因此，使用者已釘選的應用程式越少，每個應用程式將會收到的可能更新越多。
- 應用程式會保留在記憶體中，讓應用程式在從停駐選取時快速恢復。

使用者執行的最後一個應用程式會被視為_最近使用_的應用程式，而且會佔用 Dock 中的最後一個插槽。 使用者可以從該處選擇永久將它釘選到 Dock。 最近使用的會視為使用者已釘選到 Dock 的任何其他最愛應用程式。

> [!IMPORTANT]
> 只有新增至 [首頁] 畫面的應用程式不會提供任何一般排程。 若要接收週期性排程和背景更新，_必須_將應用程式新增至 Dock。

如本檔稍早所述，快照集在 watchOS 3 中非常重要，因為它們會當做應用程式的預覽和啟動映射。 如果使用者在停駐中的應用程式上執行，它會展開為全螢幕、輸入前景並開始執行，因此快照集必須是最新狀態。

有時候系統可能會決定需要新的應用程式 UI 快照集。 在此情況下，快照集要求不會計入應用程式的執行時間預算。 下列程式會觸發系統快照集要求：

- 複雜的時間軸更新。
- 使用者與應用程式的通知互動。
- 從前景切換到背景狀態。
- 在背景狀態的一小時之後，應用程式就可以回到預設狀態。
- 當 watchOS 第一次啟動時。

<a name="Best-Practices"></a>

## <a name="best-practices"></a>最佳做法 

使用背景工作時，Apple 建議下列最佳作法：

- 排定應用程式需要更新的頻率。 每次執行應用程式時，它應該會重新評估其未來的需求，並視需要調整此排程。
- 如果系統傳送背景重新整理工作，但應用程式不需要更新，請順延強制，直到實際需要更新為止。
- 請考慮應用程式可使用的所有執行時間機會：
  - 停駐和前景啟用。
  - 通知。
  - 複雜的更新。
  - 背景重新整理。
- 用於 `ScheduleBackgroundRefresh` 一般用途的背景執行時間，例如：
  - 輪詢系統以取得相關資訊。
  - 排程未來 `NSURLSessions` 要求背景資料。 
  - 已知的時間轉換。
  - 觸發複雜的更新。

<a name="Snapshot-Best-Practices"></a>

## <a name="snapshot-best-practices"></a>快照集最佳做法

使用快照集更新時，Apple 會提供下列建議：

- 只有在必要時才會使快照集失效，例如，當有重大內容變更時。
- 避免高頻率快照集失效。 例如，計時器應用程式不應該每秒更新快照集，只有在計時器結束時才會執行。

<a name="App-Data-Flow"></a>

## <a name="app-data-flow"></a>應用程式資料流程

Apple 建議下列作業來處理資料流程：

[![](background-tasks-images/update17.png "App Data Flow Diagram")](background-tasks-images/update17.png#lightbox)

外來事件（例如監看式連線）會喚醒應用程式。 這會強制應用程式更新其資料模型（代表應用程式目前的狀態）。 由於資料模型變更，應用程式必須更新其複雜性、要求新的快照集，可能會啟動背景 `NSURLSession` 來提取更多的資料，並排程進一步的背景重新整理。

<a name="The-App-Lifecycle"></a>

## <a name="the-app-lifecycle"></a>應用程式生命週期

由於 Dock 並能夠將我的最愛應用程式釘選到它，因此 Apple 認為使用者會在更多應用程式之間移動，而這通常是透過 watchOS 2 來進行。 因此，應用程式應該準備好處理這種變更，並快速地在前景和背景狀態之間移動。

Apple 提供下列建議：

- 請確定應用程式在進入前景啟用時，儘快完成任何背景工作。
- 請務必先完成所有前景工作，再呼叫來進入背景 `NSProcessInfo.PerformExpiringActivity` 。
- 在 watchOS 模擬器中測試應用程式時，不會強制執行任何工作預算，因此應用程式可以視需要重新整理以適當地測試功能。
- 請務必在實際 Apple Watch 硬體上測試，以確保應用程式在發佈至 iTunes Connect 之前，不會執行超過其預算。
- Apple 建議在進行測試和調試時，將 Apple Watch 保留在充電器上。
- 請確定已徹底測試冷啟動和繼續應用程式。
- 確認所有應用程式工作都已完成。
- 會改變固定在 Dock 中的應用程式數目，以測試最佳和最糟的案例。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋 Apple 對 watchOS 的增強功能，以及如何使用它們來保持監看應用程式的最新狀態。 首先，它涵蓋了 Apple 在 watchOS 3 中新增的所有新背景工作。 然後，它涵蓋背景 API 生命週期，以及如何在 Xamarin watchOS 應用程式中執行背景工作。 最後，它涵蓋了排程的運作方式，並提供一些最佳作法。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
