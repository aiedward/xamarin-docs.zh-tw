---
title: 在 Xamarin 中 watchOS 背景工作
description: 本檔說明如何在 Xamarin 中使用 watchOS 的背景工作，查看背景工作的類型、使用資源、執行背景工作、排程、最佳作法等等。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/13/2017
ms.openlocfilehash: 98f7f7b23fc437dee28f6797c3401bae472fa672
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433358"
---
# <a name="watchos-background-tasks-in-xamarin"></a>在 Xamarin 中 watchOS 背景工作

WatchOS 3 有三個主要的方式，可讓 watch 應用程式將其資訊保持在最新狀態：

- 使用其中一項新的背景工作。
- 在觀賞臉部上有一個複雜的 (提供額外的時間來更新) 。
- 讓使用者將應用程式釘選到新的 Dock (將其保留在記憶體中，並經常更新) 。

## <a name="keeping-an-app-up-to-date"></a>讓應用程式保持在最新狀態

在討論開發人員可以保留 watchOS 應用程式資料和消費者介面目前和更新之前的所有方式之前，本節將探討一組一般的使用模式，以及使用者在一天內的哪一天和目前 (執行的活動（例如駕駛) ）之間，如何移動其 iPhone 與其 Apple Watch。

以下列範例為例：

[![使用者可能會在一天內的 iPhone 與其 Apple Watch 之間移動](background-tasks-images/update00.png)](background-tasks-images/update00.png#lightbox)

1. 在早上，在等候咖啡時，使用者會在其 iPhone 上流覽目前的新聞數分鐘。
2. 在離開咖啡廳之前，他們會在其監看面上快速檢查天氣。
3. 在午餐之前，他們使用 iPhone 上的地圖應用程式來尋找附近的餐廳，並預訂保留以符合用戶端。
4. 當旅行給餐廳時，他們會收到 Apple Watch 的通知，並快速概覽，他們知道他們的午餐約會是遲到的。
5. 在晚上，他們會使用 iPhone 上的 Maps 應用程式來檢查流量，然後再推動首頁。
6. 在首頁上，他們會在 Apple Watch 上收到 iMessage 通知，要求他們挑選一些牛奶，並使用快速回復功能傳送回應「確定」。

由於「快速概覽」 (不到三秒的) 本質上，使用者想要如何使用 Apple Watch 應用程式，所以通常沒有足夠的時間可讓應用程式在向使用者顯示所需的資訊並更新其 UI 之前。

藉由使用 Apple 隨附于 watchOS 3 的新 Api，應用程式可以排程 _背景_ 重新整理，並在使用者要求之前備妥所需的資訊。 請採用上述的氣象複雜範例：

[![天氣複雜的範例](background-tasks-images/update01.png)](background-tasks-images/update01.png#lightbox)

1. 應用程式會在特定時間由系統喚醒。
2. 應用程式會提取產生更新所需的資訊。
3. 應用程式會重新產生其消費者介面以反映新的資料。
4. 當使用者在應用程式的複雜情況下，它會有最新的資訊，而不需要使用者等待更新。

如上所示，watchOS 系統會使用一或多個工作喚醒應用程式，其中會有相當有限的集區可用：

[![WatchOS 系統會使用一或多個工作將應用程式喚醒](background-tasks-images/update02.png)](background-tasks-images/update02.png#lightbox)

Apple 建議您充分利用 (這項工作，因為它是應用程式) 的有限資源，只要在應用程式完成更新程式之後，就可以將它保留到應用程式。

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

當應用程式完成指定的工作後，它會將其標示為已完成，以將其傳回給系統：

[![工作會將其標示為已完成，以返回系統](background-tasks-images/update03.png)](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks"></a>

## <a name="new-background-tasks"></a>新的背景工作

watchOS 3 引進了數個背景工作，可讓應用程式用來更新其資訊，以確保在開啟應用程式之前，其具有使用者需要的內容，例如：

- **背景應用程式** 重新整理- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) 工作可讓應用程式在背景中更新其狀態。 這通常會包含另一項工作，例如使用 [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)從網際網路下載新的內容。
- **背景快照** 集重新整理： [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 工作可讓應用程式在系統取得將用來擴展 Dock 的快照之前，更新其內容和 UI。
- **背景監看** 式連線-從配對的 iPhone 接收背景資料時，會為應用程式啟動 [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) 工作。
- **背景 URL 會話** -背景傳輸需要授權或成功完成 (或) 錯誤時，即會啟動應用程式的 [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) 工作。

下列各節將詳細討論這些工作。

<a name="WKApplicationRefreshBackgroundTask"></a>

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是可排程讓應用程式在未來日期喚醒的一般工作：

[![未來日期的 WKApplicationRefreshBackgroundTask 喚醒](background-tasks-images/update04.png)](background-tasks-images/update04.png#lightbox)

在工作的執行時間內，應用程式可以進行任何種類的本機處理，例如更新複雜的時間軸，或使用來提取某些必要的資料 `NSUrlSession` 。

<a name="WKURLSessionRefreshBackgroundTask"></a>

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

`WKURLSessionRefreshBackgroundTask`當資料完成下載並準備好要由應用程式處理時，系統會傳送 a：

[![資料完成下載時的 WKURLSessionRefreshBackgroundTask](background-tasks-images/update05.png)](background-tasks-images/update05.png#lightbox)

當資料在背景下載時，不會保留應用程式的執行狀態。 相反地，應用程式會排程資料的要求，然後會暫停，而且系統會處理資料的下載，只有在下載完成時才會 reawakening 應用程式。

<a name="WKSnapshotRefreshBackgroundTask"></a>

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速加以存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集的資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後按一下應用程式以啟動它，並將快照集取代為執行中的應用程式介面。

[![以正在執行的應用程式介面取代快照集](background-tasks-images/update06.png)](background-tasks-images/update06.png#lightbox)

系統會定期建立應用程式 UI 的快照 (，方法是傳送 `WKSnapshotRefreshBackgroundTask`) ，並使用這些快照集來擴展 Dock。 watchOS 讓應用程式有機會在建立此快照集之前更新其內容和 UI。

WatchOS 3 中的快照集非常重要，因為它們可作為應用程式的預覽和啟動映射。 如果使用者在 Dock 中的應用程式上進行結算，它會展開為全螢幕、輸入前景並開始執行，因此快照集必須是最新的：

[![如果使用者在 Dock 中對應用程式進行結算，它會展開為全螢幕](background-tasks-images/update07.png)](background-tasks-images/update07.png#lightbox)

同樣地，系統會發出， `WKSnapshotRefreshBackgroundTask` 讓應用程式可以在建立快照集之前，藉由更新資料和 UI) 來準備 (：

[![應用程式可以藉由在建立快照集之前更新資料和 UI 來進行準備](background-tasks-images/update08.png)](background-tasks-images/update08.png#lightbox)

當應用程式標示為 `WKSnapshotRefreshBackgroundTask` 已完成時，系統會自動取得應用程式 UI 的快照。

> [!IMPORTANT]
> 請務必在 `WKSnapshotRefreshBackgroundTask` 應用程式收到新的資料並更新其消費者介面之後，一律排程，否則使用者將不會看到修改過的資訊。

此外，當使用者從應用程式收到通知，並將其指向前景時，快照集必須是最新的，因為它也會作為啟動畫面：

[![使用者會收到來自應用程式的通知，並將其點擊以將應用程式帶入前景](background-tasks-images/update09.png)](background-tasks-images/update09.png#lightbox)

如果在使用者與 watchOS 應用程式互動之後已超過一小時，就能回到其預設狀態。 預設狀態可能會對不同的應用程式表示不同的專案，而且根據應用程式的設計，可能完全沒有預設狀態。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask"></a>

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

在 watchOS 3 中，Apple 透過新的，與背景重新整理 API 整合了監看連線能力 `WKWatchConnectivityRefreshBackgroundTask` 。 使用這項新功能，iPhone 應用程式可以將最新的資料傳遞至其 watch 應用程式的對應專案，而 watchOS 應用程式會在背景中執行：

[![IPhone 應用程式可以將新資料傳遞至其 watch 應用程式的對應專案，而 watchOS 應用程式會在背景中執行](background-tasks-images/update10.png)](background-tasks-images/update10.png#lightbox)

從 iPhone 應用程式起始複雜的推送、應用程式內容、傳送檔案或更新使用者資訊，將會在背景喚醒 Apple Watch 應用程式。

透過將 watch 應用程式喚醒時， `WKWatchConnectivityRefreshBackgroundTask` 必須使用標準 API 方法從 iPhone 應用程式接收資料。

[![WKWatchConnectivityRefreshBackgroundTask 資料流程](background-tasks-images/update11.png)](background-tasks-images/update11.png#lightbox)

1. 確定會話已啟用。
2. 只要監視新 `HasContentPending` 屬性的值為 `true` ，應用程式仍會有要處理的資料。 如同之前，應用程式應保留到工作，直到完成所有資料的處理。
3. 如果沒有其他要處理的資料 (`HasContentPending = false`) ，請將工作標示為已完成，以將其傳回系統。 若無法這樣做，將會耗盡應用程式的配置背景執行時間，導致損毀報表。

<a name="The-Background-API-Lifecycle"></a>

## <a name="the-background-api-lifecycle"></a>背景 API 生命週期

將所有新的背景工作 API 全部放在一起，一組一般的互動方式如下：

[![背景 API 生命週期](background-tasks-images/update12.png)](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 應用程式會排定背景工作，以在未來的某個時間點提醒于。
2. 系統會喚醒應用程式並傳送工作。
3. 應用程式會處理工作來完成所需的任何工作。
4. 由於處理工作的結果，應用程式可能需要排程更多背景工作，以在未來完成更多工作，例如使用下載更多內容 `NSUrlSession` 。
5. 應用程式會將工作標示為已完成，並將其傳回系統。

<a name="Using-Resources-Responsibly"></a>

## <a name="using-resources-responsibly"></a>負責任地使用資源

很重要的是，在此生態系統中，watchOS 應用程式在系統的共用資源上進行了負責任的行為，是很重要的。

請看看下列案例：

[![WatchOS 應用程式會限制其耗盡系統的共用資源](background-tasks-images/update13.png)](background-tasks-images/update13.png#lightbox)

1. 使用者會在下午1:00 啟動 watchOS 應用程式。
2. 應用程式會排程工作，以在下午2:00 的一小時內喚醒並下載新的內容。
3. 在下午1:50 時，使用者會重新開啟應用程式，讓它在此時更新其資料和 UI。
4. 應用程式應該重新排程工作，以在稍後下午2:50 執行一小時，而不是讓工作在10分鐘內再次喚醒應用程式。

雖然每個應用程式都不同，但 Apple 建議您尋找使用方式的模式（例如以上所示的模式），以協助節省系統資源。

<a name="Implementing-Background-Tasks"></a>

## <a name="implementing-background-tasks"></a>執行背景工作

舉例而言，本檔將使用假的 MonkeySoccer 體育應用程式，將足球分數報告給使用者。

請看看下列一般使用案例：

[![一般使用案例](background-tasks-images/update14.png)](background-tasks-images/update14.png#lightbox)

使用者的最愛足球團隊會在下午7:00 到9:00 的時間內播放很大的相符專案，因此應用程式應該會預期使用者定期檢查分數，並決定30分鐘的更新間隔。

1. 使用者會開啟應用程式，並在30分鐘後排程背景更新的工作。 背景 API 只允許在指定時間執行一種類型的背景工作。
2. 應用程式會接收工作並更新其資料和 UI，然後在30分鐘後排程另一個背景工作。 開發人員必須記得要排定另一個背景工作，否則應用程式永遠不會重新喚醒以取得更多更新。
3. 同樣地，應用程式會收到工作並更新其資料、更新其 UI，並在30分鐘後排程另一個背景工作。
4. 相同的進程會再次重複。
5. 最後會收到背景工作，而應用程式會再次更新其資料和 UI。 因為這是最後一個分數，所以不會排程新的背景重新整理。

<a name="Scheduling-for-Background-Update"></a>

## <a name="scheduling-for-background-update"></a>排程背景更新

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

`NSDate`當應用程式想要喚醒並建立以保存所要求工作的詳細資料時，它會在未來建立30分鐘的時間 `NSMutableDictionary` 。 的 `ScheduleBackgroundRefresh` 方法 `SharedExtension` 是用來要求排程工作。

如果系統無法排程要求的工作，系統將會傳回 `NSError` 。

<a name="Processing-the-Update"></a>

## <a name="processing-the-update"></a>處理更新

接下來，請仔細查看5分鐘的時間範圍，以顯示更新分數所需的步驟：

[![顯示更新分數所需步驟的5分鐘時間範圍](background-tasks-images/update15.png)](background-tasks-images/update15.png#lightbox)

1. 在下午7:30:02，應用程式是由系統喚醒，並提供更新背景工作。 第一個優先順序是從伺服器取得最新的分數。 請參閱下方 [的排程 NSUrlSession](#Scheduling-a-NSUrlSession) 。
2. 在7:30:05 中，應用程式會完成原始工作，系統會將應用程式置於睡眠狀態，並繼續在背景下載要求的資料。
3. 當系統完成下載時，它會建立新的工作來喚醒應用程式，讓它可以處理下載的資訊。 請參閱下方的[處理背景](#Handling-Background-Tasks)[工作及處理下載](#Handling-the-Download-Completing)。
4. 應用程式會儲存更新的資訊，並將工作標示為已完成。 目前，開發人員可能會想要更新應用程式的消費者介面，但 Apple 建議排程快照集工作來處理該處理常式。 請參閱下方 [的排程快照集更新](#Scheduling-a-Snapshot-Update) 。
5. 應用程式會收到快照集工作、更新其消費者介面，並將工作標示為已完成。 請參閱下面 [的處理快照集更新](#Handling-a-Snapshot-Update) 。

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

它會設定並建立新的 `NSUrlSession` ，然後使用該會話來使用方法建立新的下載工作 `CreateDownloadTask` 。 它會呼叫 `Resume` 下載工作的方法來啟動會話。

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

`HandleBackgroundTasks`方法會迴圈執行系統已將應用程式傳送 (的所有工作， `backgroundTasks`) 搜尋 `WKUrlSessionRefreshBackgroundTask` 。 如果找到一個，就會重新加入會話，並附加 `NSUrlSessionDownloadDelegate` 以處理下載完成 (請參閱以下) [處理下載完成](#Handling-the-Download-Completing) ：

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

它會在工作完成之後，將控制碼保存到集合中，直到完成為止：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有傳送至應用程式的工作都必須完成，對於目前未處理的任何工作，請將它標示為已完成：

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing"></a>

## <a name="handling-the-download-completing"></a>正在處理下載完成

MonkeySoccer 應用程式會使用下列 `NSUrlSessionDownloadDelegate` 委派來處理完成的下載，並處理要求的資料：

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

初始化時，會將控制碼保留給產生 `ExtensionDelegate` 它的和 `WKRefreshBackgroundTask` 。 它會覆寫 `DidFinishDownloading` 方法以處理下載完成。 然後使用的 `CompleteTask` 方法， `ExtensionDelegate` 通知工作已完成，並將其從暫止的工作集合中移除。 請參閱處理上述的 [背景](#Handling-Background-Tasks) 工作。

<a name="Scheduling-a-Snapshot-Update"></a>

## <a name="scheduling-a-snapshot-update"></a>排程快照集更新

下列程式碼可用於排程快照集工作，以最新的分數更新 UI：

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

如同 `ScheduleURLUpdateSession` 上述方法，它會 `NSDate` 在應用程式想要喚醒時建立新的，並建立 `NSMutableDictionary` 以保存所要求工作的詳細資料。 的 `ScheduleSnapshotRefresh` 方法 `SharedExtension` 是用來要求排程工作。

如果系統無法排程要求的工作，系統將會傳回 `NSError` 。

<a name="Handling-a-Snapshot-Update"></a>

## <a name="handling-a-snapshot-update"></a>處理快照集更新

若要處理快照集工作， `HandleBackgroundTasks` 方法 (參閱 [處理](#Handling-Background-Tasks) 上述) 的背景工作，如下所示：

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

方法會測試所處理的工作類型。 如果它是 `WKSnapshotRefreshBackgroundTask` 取得工作的存取權：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

方法會更新消費者介面然後建立 `NSDate` ，以便在快照集過時時告訴系統。 它會建立 `NSMutableDictionary` 具有使用者資訊的，以描述新的快照集，並將快照集工作標示為已完成，並提供此資訊：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它也會告知快照集工作，應用程式不會在第一個參數) 中返回預設狀態 (。 沒有預設狀態概念的應用程式應該一律將此屬性設定為 `true` 。

<a name="Working-Efficiently"></a>

## <a name="working-efficiently"></a>有效率地工作

如上述的五分鐘範圍範例所示，MonkeySoccer 應用程式藉由使用新的 watchOS 3 背景工作來更新其分數，並使用新的3背景工作，應用程式的使用時間總計為15秒：

[![應用程式的使用時間總計為15秒](background-tasks-images/update16.png)](background-tasks-images/update16.png#lightbox)

這會降低應用程式對可用 Apple Watch 資源和電池壽命的影響，也可讓應用程式更能與監看的其他應用程式一起運作。

<a name="How-Scheduling-Works"></a>

## <a name="how-scheduling-works"></a>排程的運作方式

當 watchOS 3 應用程式在前景時，一律會排程執行，而且可以執行任何類型的處理，例如更新資料或重繪其 UI。 當應用程式移到背景時，通常會由系統暫停，而且所有執行時間作業都會暫停。

當應用程式在背景時，系統可能會將它設為目標，以快速執行特定的工作。 因此，在 watchOS 2 中，系統可能會暫時喚醒背景應用程式，以進行處理長時間的檢查，或更新應用程式的複雜專案。 在 watchOS 3 中，有數種新的方式可讓應用程式在背景中執行。

當應用程式在背景時，系統會在其上強加數項限制：

- 只有幾秒鐘的時間才能完成任何指定的工作。 系統不只會考慮經過的時間量，也會考慮應用程式耗用多少 CPU 電源來衍生此限制。
- 任何超過其限制的應用程式將會終止，並出現下列錯誤碼：
  - **CPU** -0xc51bad01
  - 0xc51bad02**時**
- 系統會根據其要求應用程式執行的背景工作類型來強加不同的限制。 例如， `WKApplicationRefreshBackgroundTask` 和工作 `WKURLSessionRefreshBackgroundTask` 的執行時間會比其他類型的背景工作有稍微更長的執行時間。

<a name="Complications-and-App-Updates"></a>

### <a name="complications-and-app-updates"></a>複雜和應用程式更新

除了 Apple 已新增至 watchOS 3 的新背景工作之外，watchOS 應用程式的複雜功能也可能會影響應用程式接收背景更新的方式和時機。

複雜的是小型的視覺元素，提供有用的資訊。 根據選取的監看式臉部，使用者可以自訂具有一或多個複雜的監看式臉部，並可透過 watchOS 3 中的 watch 應用程式提供此功能。

如果使用者在其監看面中包含其中一個應用程式的複雜功能，它會為應用程式提供下列更新的優點：

- 它會讓系統將應用程式保持在可立即啟動的狀態，在此情況下，它會嘗試在背景中啟動應用程式、將它保存在記憶體中，並提供額外的時間來更新。
- 每日至少有50個推送更新保證會有複雜性。

開發人員應該一律致力於為其應用程式帶來吸引人的複雜性，以因應上述原因而將使用者新增至監看面。

在 watchOS 2 中，複雜的是應用程式在背景中接收執行時間的主要方式。 在 watchOS 3 中，仍可確保每小時收到多個更新的複雜應用程式，不過，它可以用 `WKExtensions` 來要求更多執行時間來更新其複雜性。

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

它會使用的 `RemainingComplicationUserInfoTransfers` 屬性 `WCSession` 來查看應用程式一天剩下多少高優先順序的傳輸，然後根據該數位採取動作。 如果應用程式開始執行的傳輸不足，它可以關閉傳送次要更新，並且只在發生重大變更時傳送資訊。

<a name="Scheduling-and-Dock"></a>

### <a name="scheduling-and-the-dock"></a>排程和 Dock

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速加以存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集的資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後按一下應用程式以啟動它，並將快照集取代為執行中的應用程式介面。

[![Dock](background-tasks-images/dock01.png)](background-tasks-images/dock01.png#lightbox)

系統會定期取得應用程式 UI 的快照，並使用這些快照集來填入檔。watchOS 讓應用程式有機會在建立此快照集之前更新其內容和 UI。

已釘選到 dock 的應用程式可能會預期如下：

- 每小時最少可獲得一次更新。 這包括應用程式重新整理工作和快照集工作。
- 更新預算會散佈在 Dock 中的所有應用程式之間。 因此，使用者已釘選的應用程式越少，每個應用程式會收到的潛在更新越多。
- 應用程式將會保留在記憶體中，因此應用程式會在從 Dock 選取時快速恢復。

使用者執行的最後一個應用程式將被視為 _最近使用_ 的應用程式，而且會佔用 Dock 中的最後一個位置。 如此一來，使用者就可以選擇將它永久釘選到 Dock。 最近使用的會被視為使用者已釘選到 Dock 的任何其他最愛應用程式。

> [!IMPORTANT]
> 只有新增至主畫面的應用程式不會提供任何一般排程。 若要接收定期排程和背景更新， _必須_ 將應用程式新增至 Dock。

如本檔稍早所述，快照集在 watchOS 3 中非常重要，因為它們可作為應用程式的預覽和啟動映射。 如果使用者在 Dock 中的應用程式上進行結算，它會展開為全螢幕、輸入前景並開始執行，因此快照集必須是最新的。

有時候系統可能會決定需要應用程式 UI 的新快照。 在這種情況下，快照集要求將不會計入應用程式的執行時間預算。 下列將會觸發系統快照集要求：

- 複雜的時間軸更新。
- 使用者與應用程式的通知互動。
- 從前景切換到背景狀態。
- 在處於背景狀態的一小時之後，應用程式就可以回到預設狀態。
- WatchOS 首次開機時。

<a name="Best-Practices"></a>

## <a name="best-practices"></a>最佳做法

Apple 建議在使用背景工作時使用下列最佳作法：

- 排定應用程式需要更新的頻率。 每次應用程式執行時，都應該重新評估未來的需求，並視需要調整此排程。
- 如果系統傳送背景重新整理工作，但應用程式不需要更新，請延遲工作，直到實際需要更新為止。
- 請考慮應用程式可使用的所有執行時間機會：
  - 停駐和前景啟用。
  - 通知。
  - 複雜的更新。
  - 背景重新整理。
- 用於 `ScheduleBackgroundRefresh` 一般用途的背景執行時間，例如：
  - 輪詢系統以取得資訊。
  - 排程未來 `NSURLSessions` 要求背景資料。
  - 已知的時間轉換。
  - 觸發複雜的更新。

<a name="Snapshot-Best-Practices"></a>

## <a name="snapshot-best-practices"></a>快照集最佳做法

使用快照集更新時，Apple 會提出下列建議：

- 只有在必要時才使快照集失效，例如，當有重大內容變更時。
- 避免高頻率的快照集失效。 例如，計時器應用程式不應該每秒更新快照集，它應該只在計時器結束時完成。

<a name="App-Data-Flow"></a>

## <a name="app-data-flow"></a>應用程式資料流程

Apple 針對使用資料流程提供下列建議：

[![應用程式資料流程圖](background-tasks-images/update17.png)](background-tasks-images/update17.png#lightbox)

外來事件 (例如 Watch 連線) 喚醒應用程式。 這會強制應用程式更新其資料模型 (，以代表應用程式的目前狀態) 。 由於資料模型變更，應用程式將需要更新其複雜性、要求新的快照集，可能會啟動背景 `NSURLSession` 來提取更多資料，並排程進一步的背景重新整理。

<a name="The-App-Lifecycle"></a>

## <a name="the-app-lifecycle"></a>應用程式生命週期

由於 Dock 和將我的最愛應用程式釘選到它的能力，Apple 認為使用者將會在更多應用程式之間移動，而這些應用程式會使用 watchOS 2。 因此，應用程式應該準備好處理這種變更，並快速地在前景和背景狀態之間移動。

Apple 有下列建議：

- 請確定應用程式在進入前景啟用時儘快完成任何背景工作。
- 請務必先完成所有前景工作，然後再呼叫來進入背景 `NSProcessInfo.PerformExpiringActivity` 。
- 在 watchOS 模擬器中測試應用程式時，將不會強制執行任何工作預算，讓應用程式可以視需要重新整理，以正確地測試功能。
- 請一律在實際 Apple Watch 硬體上進行測試，以確保應用程式在發佈至 iTunes Connect 之前未執行超過其預算。
- Apple 建議在測試和偵測時，將 Apple Watch 保留在充電器上。
- 確定已徹底測試冷啟動和繼續應用程式。
- 確認所有的應用程式工作都已完成。
- 變更固定在 Dock 中的應用程式數目，以測試最佳和最糟的案例。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 Apple 對 watchOS 的增強功能，以及如何使用這些增強功能讓 watch 應用程式保持在最新狀態。 首先，它涵蓋了 Apple 在 watchOS 3 中新增的所有新背景工作。 然後，它會討論背景 API 生命週期，以及如何在 Xamarin watchOS 應用程式中執行背景工作。 最後，它涵蓋了排程的運作方式，並提供一些最佳作法。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)