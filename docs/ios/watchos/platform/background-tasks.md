---
title: watchOS 在 Xamarin 中的背景工作
description: 本文件說明如何在 Xamarin 中，查看背景工作類型、 使用的資源，實作背景工作、 排程、 最佳作法，以及更 watchOS 中使用的背景工作。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 4105193ea69eaf369ae62632090a281e641303f7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110364"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS 在 Xamarin 中的背景工作

運用 watchOS 3，有三種主要的方式，watch 應用程式可以保持在其資訊： 

- 使用其中一種數個新的背景工作。 
- 有其複雜性的錶 （讓它額外時間來更新）。 
- 使用者 pin，應用程式需要新的停駐 (其中其保留在記憶體中，而且經常更新)。 

## <a name="keeping-an-app-up-to-date"></a>保持最新的應用程式

之前討論的所有開發人員可以保留 watchOS 應用程式的資料和使用者介面，目前與更新的方式，此區段會看看一組典型的使用模式和如何使用者可能會在他們的 iPhone 與全天根據其 Apple Watch 之間移動 一天和活動的時間目前進行的 （例如驅動）。

請使用以下範例：

[![](background-tasks-images/update00.png "如何在使用者可能會移動他們的 iPhone 與他們的 Apple Watch 全天之間")](background-tasks-images/update00.png#lightbox)

1. 在早上，喝杯咖啡，列在等待時使用者瀏覽幾分鐘的時間在他們的 iPhone 上的目前新聞。
2. 才會離開咖啡廳，他們可以快速檢查其 watch 錶面上的複雜功能與天氣。
3. 再去午餐了，他們使用對應應用程式在 iPhone 上尋找附近的餐廳和書籍以符合用戶端保留區。
4. 雖然流動到 「 餐廳 」，他們會收到通知，他們的 Apple Watch 上且具有快速概覽，他們知道其午餐約會晚執行。
5. 在晚上，他們使用的對應應用程式的 iPhone 上首頁開車前先檢查流量。
6. 途中家中收到要求他們其 Apple Watch 上的挑選一些 milk iMessage 通知並使用 「 快速回覆 」 功能來傳送回應 [確定]。

因為 「 概覽 」 （少於 3 秒） 的使用者想要使用 Apple Watch 應用程式中，有一般的方式本質不足夠的時間讓應用程式，以擷取所需的資訊，並顯示給使用者之前，請先更新其 UI。

使用新 Api Apple 為首 watchOS 3，在應用程式可以將排程_背景重新整理_和擁有所需的資訊準備，才能在使用者要求。 採取上面所討論的天氣複雜功能的範例：

[![](background-tasks-images/update01.png "天氣複雜功能的範例")](background-tasks-images/update01.png#lightbox)

1. 要被喚醒系統在特定時間的應用程式排程。 
2. 應用程式會擷取它將需要產生更新的資訊。
3. 應用程式會重新產生它的使用者介面，以反映新的資料。
4. 當使用者 glances 在應用程式的複雜度時，它將在使用者無須等候更新的最新資訊。

如上所示，watchOS 系統就會喚醒使用一或多個工作，其中有一個非常有限的集區可用的應用程式：

[![](background-tasks-images/update02.png "WatchOS 系統醒來時使用一或多個工作的應用程式")](background-tasks-images/update02.png#lightbox)

Apple 建議充分利用這項工作 （因為它是這種有限的資源，應用程式） 透過保留到它，直到應用程式已完成更新本身的程序。

系統會傳遞這些工作所呼叫的新`HandleBackgroundTasks`方法的`WKExtensionDelegate`委派。 例如: 

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

當應用程式已完成指定的工作時，將它傳回給系統方式，將已完成：

[![](background-tasks-images/update03.png "方式，將完成此工作會傳回系統")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>新的背景工作

watchOS 3 引進了數個應用程式可用來更新使用者必須先開啟應用程式，例如其資訊確保其內容的背景工作：

- **背景應用程式重新整理**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可讓應用程式，以更新其在背景中的狀態。 通常這包括另一個工作，例如從網際網路使用下載新內容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **背景重新整理快照集**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可以讓此應用程式之前，系統會將用來填入 Dock 建立快照集更新其內容和 UI。
- **背景監看式連線**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)從配對的 iPhone 接收背景的資料時，啟動工作的應用程式。
- **URL 的工作階段的背景**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)背景傳送需要授權，或完成 （成功或錯誤） 時，啟動工作的應用程式。

這些工作將會在下列各節中詳細說明。

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是可以排程在未來日期事項應用程式的一般工作：

[![](background-tasks-images/update04.png "在未來日期事項 WKApplicationRefreshBackgroundTask")](background-tasks-images/update04.png#lightbox)

內工作的執行階段，應用程式可以執行任何一種本機處理，例如更新複雜功能時間軸，或擷取與某些必要的資料`NSUrlSession`。


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

系統會將傳送`WKURLSessionRefreshBackgroundTask`資料完成時下載並準備好處理應用程式：

[![](background-tasks-images/update05.png "當資料已完成下載 WKURLSessionRefreshBackgroundTask")](background-tasks-images/update05.png#lightbox)

應用程式不會保留在背景中下載資料時執行。 相反地，應用程式排程的要求資料，則它已暫停，而且系統會處理資料，只在下載完成時 reawakening 應用程式的下載。

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3，Apple 已新增的停駐讓使用者可以釘選自己最愛的應用程式及快速存取。 當使用者按下 Apple Watch 側邊的按鈕時，將會顯示已釘選的應用程式的快照集的資源庫。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選 應用程式，以啟動快照集取代為執行中應用程式的介面。

[![](background-tasks-images/update06.png "快照集取代為正在執行的應用程式介面")](background-tasks-images/update06.png#lightbox)

系統會定期建立的應用程式的 UI 的快照集 (藉由傳送`WKSnapshotRefreshBackgroundTask`)，並使用這些快照集來填入停駐。 watchOS 會讓應用程式有機會先更新其內容和 UI，才能建立此快照集。

快照集是在 watchOS 3 中非常重要，因為功能就如同將 預覽 與 啟動應用程式的映像。 如果使用者讓在 Dock 中的應用程式上，它會展開至全螢幕、 進入前景，並開始執行，因此請務必在快照集是最新狀態：

[![](background-tasks-images/update07.png "如果使用者讓在 Dock 中的應用程式上，它會展開至全螢幕")](background-tasks-images/update07.png#lightbox)

同樣地，系統將會發行`WKSnapshotRefreshBackgroundTask`，讓應用程式之前，可以準備 （藉由更新資料和 UI） 擷取快照集：

[![](background-tasks-images/update08.png "應用程式可以藉由更新資料和 UI，在擷取快照之前準備")](background-tasks-images/update08.png#lightbox)

當應用程式會將標示`WKSnapshotRefreshBackgroundTask`完成時，系統會自動建立快照集的應用程式的 UI。

> [!IMPORTANT]
> 請務必一律排程` WKSnapshotRefreshBackgroundTask`收到新的資料並更新其使用者介面應用程式或使用者將不會看到已修改的資訊之後。




此外，當使用者從應用程式會收到通知，並點選它將應用程式帶到前景，快照便需要保持最新狀態，因為它做為在啟動畫面：

[![](background-tasks-images/update09.png "使用者從應用程式會收到通知，並點選它帶到前景應用程式")](background-tasks-images/update09.png#lightbox)

如果因為使用者已產生互動的 watchOS 應用程式已超過一個小時，它能夠傳回至其預設狀態。 預設狀態可能表示不同的應用程式的不同事物，並根據應用程式的設計，就不會有預設的狀態完全。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

在 watchOS 3，Apple 已透過新的背景重新整理 api 整合監看式連線`WKWatchConnectivityRefreshBackgroundTask`。 使用這項新功能，iPhone 應用程式可以重新將資料傳送到其監看式應用程式對應項目，watchOS 應用程式在背景中執行時：

[![](background-tasks-images/update10.png "IPhone 應用程式可以提供新的資料與其監看式應用程式對應項中，同時 watchOS 應用程式在背景執行")](background-tasks-images/update10.png#lightbox)

起始複雜功能推送時，應用程式內容，傳送檔案，或更新的 iPhone 應用程式的使用者資訊會喚醒 Apple Watch 應用程式在背景中。

Watch 應用程式透過的事項時`WKWatchConnectivityRefreshBackgroundTask`它必須使用標準 API 方法，以從 iPhone app 中接收資料。

[![](background-tasks-images/update11.png "WKWatchConnectivityRefreshBackgroundTask 資料流程")](background-tasks-images/update11.png#lightbox)

1. 請確定已啟用工作階段。
2. 監視新`HasContentPending`只要這個值是屬性`true`，應用程式仍有要處理的資料。 之前，應用程式應該保存到工作，直到它已完成處理所有資料。
3. 當沒有要處理更多資料 (`HasContentPending = false`)，讓它回到系統完成將工作標示。 無法執行這項操作，將會耗盡導致損毀報表的應用程式的配置的背景執行階段。

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>背景 API 生命週期

將所有新的背景工作 api 的部分放在一起，典型的一組互動看起來會如下所示：

[![](background-tasks-images/update12.png "背景 API 生命週期")](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 應用程式會排定在背景工作會在未來某個時間點為提醒。
2. 此應用程式系統的事項而傳送工作。
3. 應用程式會處理工作完成所需的任何工作。
4. 如此一來處理工作，應用程式可能需要排程多個背景工作在未來，完成更多工作，例如下載其他內容使用`NSUrlSession`。
5. 應用程式將標記在工作完成並將它傳回至系統。

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>以負責的態度使用資源

很重要，watchOS 應用程式的行為會以負責的態度此生態系統內藉由限制其耗用系統的共用資源。

看看下列案例：

[![](background-tasks-images/update13.png "WatchOS 應用程式限制其耗用系統的共用資源")](background-tasks-images/update13.png#lightbox)

1. 使用者會在下午 1:00 啟動 watchOS 應用程式。
2. 應用程式排程工作將喚醒並下載新內容，在一個小時，下午 2:00。
3. 在下午 1:50 使用者重新開啟應用程式，讓它在這個階段中更新其資料和 UI。
4. 而不是讓工作喚醒應用程式，以在 10 分鐘後再次，應用程式應該重新排程工作，稍後在下午 2 點 50 執行一小時的時間。

雖然每個應用程式都不同，Apple 建議尋找上述，以協助節省系統資源的 使用方式，例如所顯示的模式。

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>實作背景工作

作為範例，本文件會使用假 MonkeySoccer 運動應用程式，向使用者回報美式足球分數。 

看看下列的一般使用案例：

[![](background-tasks-images/update14.png "一般使用案例")](background-tasks-images/update14.png#lightbox)

使用者的最愛的美式足球團隊正在播放大相符項目從下午 7:00 下午 9:00，因此應用程式應該預期使用者會定期檢查分數，並決定在 30 分鐘更新間隔。

1. 使用者開啟應用程式，它會排程工作的背景更新 30 分鐘後。 背景 API 可讓背景工作在指定的時間執行的一種。
2. 應用程式接收工作，並更新其資料和 UI，然後排程另一個背景工作 30 分鐘後。 請務必記得排程另一個背景工作中，開發人員，或應用程式，便永遠不會重新喚醒以取得更多更新。
3. 同樣地，應用程式接收工作並更新其資料、 更新其 UI 和排程另一個背景工作 30 分鐘後。
4. 相同的程序會重複一次。
5. 最後一個背景工作收到和應用程式會再次更新其資料和 UI。 由於這是最終的分數，它不會針對新的背景重新整理排程。 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>排程背景更新

根據上述案例中，MonkeySoccer 應用程式可以使用下列程式碼，來安排背景更新：

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

它會建立新`NSDate`30 分鐘到未來當應用程式想要會喚醒並建立`NSMutableDictionary`來保留要求的工作的詳細資料。 `ScheduleBackgroundRefresh`方法的`SharedExtension`用來要求排定的工作。

系統會傳回`NSError`如果找不到要求的工作排程。

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>處理更新

接下來，看看顯示更新分數所需的步驟 5 分鐘時段：

[![](background-tasks-images/update15.png "顯示更新分數所需的步驟 5 分鐘時段")](background-tasks-images/update15.png#lightbox)

1. 在下午 7:30:02 應用程式會喚醒系統，並更新背景工作。 其第一優先任務是從伺服器取得最新的分數。 請參閱[排程 NSUrlSession](#Scheduling-a-NSUrlSession)如下。
2. 7:30:05 在應用程式完成原始工作，系統會讓進入睡眠狀態的應用程式，並下載所要求的資料，在背景中繼續。
3. 當系統完成下載時，它會建立新的工作，來喚醒應用程式，讓它可以處理的已下載的資訊。 請參閱[處理背景工作](#Handling-Background-Tasks)並[處理在下載完成](#Handling-the-Download-Completing)如下。 
4. 應用程式儲存更新的資訊，並將標記在工作完成。 不過，Apple 建議排程快照集工作來處理該程序，開發人員可能會想要在這個階段中，更新應用程式的使用者介面。 請參閱[排程的快照集更新](#Scheduling-a-Snapshot-Update)如下。
5. 應用程式接收快照集工作，更新其使用者介面，並將標記在工作完成。 請參閱[處理的快照集更新](#Handling-a-Snapshot-Update)如下。

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>排程 NSUrlSession

下列程式碼可用來排程下載最新的分數：

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

它會設定並建立新`NSUrlSession`，然後使用該工作階段建立新的下載工作使用`CreateDownloadTask`方法。 它會呼叫`Resume`下載工作啟動工作階段的方法。

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>處理背景工作

藉由覆寫`HandleBackgroundTasks`方法的`WKExtensionDelegate`，應用程式可以處理連入的背景工作：

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

`HandleBackgroundTasks`方法不斷循環的所有工作，系統已傳送應用程式 (在`backgroundTasks`) 搜尋`WKUrlSessionRefreshBackgroundTask`。 如果有找到，它重新加入工作階段，並附加`NSUrlSessionDownloadDelegate`來處理在下載完成 (請參閱 <<c2> [ 處理在下載完成](#Handling-the-Download-Completing)下方):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

工作中，直到它完成藉由將它加入集合，它會保留在控點：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有應用程式收到的工作需要完成，不目前正在處理，任何工作標示為已完成：

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>處理在下載完成

MonkeySoccer 應用程式會使用下列`NSUrlSessionDownloadDelegate`委派來處理在下載完成，並處理要求的資料：

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

初始化時，它會同時保留的控制代碼`ExtensionDelegate`而`WKRefreshBackgroundTask`，繁衍它。 它會覆寫`DidFinishDownloading`方法以處理在下載完成。 然後使用`CompleteTask`方法的`ExtensionDelegate`以通知它已完成的工作，並移除暫止工作的集合。 請參閱[處理背景工作](#Handling-Background-Tasks)上方。

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>排程快照集更新

下列程式碼可以用來排程快照集工作以使用最新的分數更新 UI 中：

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

就如同`ScheduleURLUpdateSession`上述的方法，它建立新`NSDate`時使用的應用程式想要會喚醒建立`NSMutableDictionary`來保留要求的工作的詳細資料。 `ScheduleSnapshotRefresh`方法的`SharedExtension`用來要求排定的工作。

系統會傳回`NSError`如果找不到要求的工作排程。

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>處理的快照集更新

若要處理的快照集工作中，`HandleBackgroundTasks`方法 (請參閱[處理背景工作](#Handling-Background-Tasks)上方) 會修改成如下所示：

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

正在處理的工作類型，測試方法。 如果是`WKSnapshotRefreshBackgroundTask`它取得的存取權的工作：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

方法會更新使用者介面，則會建立`NSDate`來告訴系統時，快照集將會過時。 它會建立`NSMutableDictionary`含使用者資訊來描述新的快照集和標記完成，但這項資訊的快照集工作：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它也會告訴快照工作，應用程式不會傳回成預設狀態 （在第一個參數）。 沒有為預設狀態的概念的應用程式應該一律將此屬性設定為`true`。

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>有效率地運作

如同上述範例中的五分鐘視窗 MonkeySoccer 應用程式用來更新其分數，有效率地運作，並使用新 watchOS 3 個背景工作，應用程式只是 15 秒總共有作用中： 

[![](background-tasks-images/update16.png "應用程式只是 15 秒總共有作用中")](background-tasks-images/update16.png#lightbox)

這會降低應用程式必須在 Apple Watch 的可用資源和電池壽命的影響，也可讓應用程式，以更適合使用監看式上執行其他應用程式。

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>排程的運作方式

WatchOS 3 應用程式在前景時，它一律會排定為執行，而且可以執行例如更新的資料所需的處理任何類型或重繪其 UI。 當應用程式移到背景時，通常由系統暫止，並會停止所有執行階段作業。 

在應用程式在背景中時，它可能會成為系統快速地執行特定工作的目標。 因此，您也可以在 watchOS 2，系統可能會暫時喚醒的背景應用程式來執行下列動作處理長外觀通知或更新應用程式的複雜度。 在 watchOS 3，有許多新方法，可在背景中執行應用程式。

當應用程式處於背景時，系統會強制它數個限制：

- 只會給予幾秒鐘的時間才能完成任何指定的工作。 系統會納入考量，不只傳遞的時間量，但也多少 CPU 能力的應用程式取用來衍生此限制。
- 具有下列的錯誤代碼，便會刪除任何超過其限制的應用程式：
    - **CPU** -0xc51bad01
    - **時間**-0xc51bad02
- 系統將會造成不同的背景工作已要求執行的應用程式類型為基礎的限制。 例如，`WKApplicationRefreshBackgroundTask`和`WKURLSessionRefreshBackgroundTask`工作會提供稍微較長的執行階段透過其他類型的背景工作。

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>複雜功能和應用程式更新

Apple 已加入 watchOS 3 新背景工作，除了 watchOS 應用程式的複雜性可能會影響應用程式如何及何時收到背景更新。

複雜功能是小型的視覺化元素，提供一目了然的實用資訊。 根據選取 watch 錶面的情況下，使用者會具有自訂錶面與一或多個複雜功能可在 watchOS 3 中的監看式應用程式所提供的功能。

如果使用者可以包含其 watch 錶面上的應用程式的難題之一，它可讓應用程式更新下列優點：

- 它會導致系統在啟動-已準備好讓應用程式狀態，其中它會嘗試啟動應用程式在背景中的，將它保留在記憶體，並提供其更新的額外時間。
- 複雜性會保證至少 50 個每日的推播更新。

開發人員應該竭盡所能建立吸引人的複雜功能，其應用程式來引誘使用者新增至其 watch 錶面如上面所列的原因。

WatchOS 2 很複雜的情況是應用程式收到執行階段在背景中的主要方式。 WatchOS 3 中的複雜功能應用程式將仍然確保接收每小時的多個更新，不過，它可以在其中使用`WKExtensions`來要求更多的執行階段，以更新其複雜性。

看看下列用來更新已連線的 iPhone 應用程式從複雜的程式碼：

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

它會使用`RemainingComplicationUserInfoTransfers`屬性`WCSession`查看多少高優先順序傳送應用程式已離開進行一天，然後根據該數字的採取動作。 如果應用程式開始不足傳輸，它可以延後傳送的次要更新和重大變更時，只傳送資訊。

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>排程和停駐

在 watchOS 3，Apple 已新增的停駐讓使用者可以釘選自己最愛的應用程式及快速存取。 當使用者按下 Apple Watch 側邊的按鈕時，將會顯示已釘選的應用程式的快照集的資源庫。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選 應用程式，以啟動快照集取代為執行中應用程式的介面。

[![](background-tasks-images/dock01.png "停駐")](background-tasks-images/dock01.png#lightbox)

系統會定期擷取快照的應用程式的 UI，並會使用這些快照集來填入文件。watchOS 會讓應用程式有機會先更新其內容和 UI，才能建立此快照集。

具有已釘選到 dock 中的應用程式可以期待出現下列：

- 他們會收到至少有一個每小時更新。 這包括應用程式重新整理工作以及在快照集工作。
- 更新預算會分散之間所有在 Dock 中的應用程式。 所以較少的應用程式釘選的使用者，就越可能會收到每個應用程式的更新。
- 應用程式會保留在記憶體中，讓應用程式將繼續快速地選取從停駐。

最後一個使用者執行的應用程式會被視為_最近使用_應用程式，將所佔用的在 Dock 中的最後一個位置。 在那里的這裡，使用者可以選擇將它永久固定到 Dock。 將處理最近使用的像任何其他最愛的應用程式使用者具有已釘選到 Dock。

> [!IMPORTANT]
> 只有已新增至 [首頁] 畫面的應用程式不會提供任何一般的排程。 若要收到一般的排程和背景更新，應用程式_必須_新增到 Dock。

如本文件稍早所述，因為功能就如同將 預覽 與 啟動應用程式的映像，所以快照集是 watchOS 3 中非常重要。 如果使用者讓在 Dock 中的應用程式上，它會展開至全螢幕、 進入前景，並開始執行，因此請務必在快照集是最新狀態。

有時候可能會當系統決定需要全新的快照集的應用程式的 UI。 在此情況下，不會計算快照集要求對應用程式的執行階段預算。 以下將會觸發系統快照集要求：

- 複雜功能時間軸更新。
- 應用程式的通知與使用者互動。
- 從 前景切換成背景狀態。
- 一個小時之後的背景狀態，因此應用程式可以回到預設狀態。
- 當 watchOS 第一次開機。

<a name="Best-Practices" />

## <a name="best-practices"></a>最佳作法 

使用背景工作時，Apple 建議下列最佳作法：

- 視應用程式需要更新排程。 每次執行應用程式應該重新評估其未來的需求並調整此排程所需。
- 如果系統會傳送背景重新整理工作，而且應用程式不需要更新，延後工作，直到有實際需要的更新。
- 請考慮應用程式可用的所有執行階段機會：
    - 停駐] 和 [前景啟動。
    - 通知。
    - 複雜功能更新。
    - 背景重新整理。
- 使用`ScheduleBackgroundRefresh`適用於一般用途的背景執行階段，例如：
    - 輪詢系統以取得資訊。
    - 排程未來`NSURLSessions`要求背景的資料。 
    - 已知的時間進行轉換。
    - 觸發的複雜功能更新。

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>快照集的最佳作法

當使用快照集更新時，Apple 會讓下列建議：

- 快照集時所需，比方說，沒有明顯的內容變更時，才使其失效。
- 避免高頻率的快照集失效。 例如，計時器應用程式不應更新快照集每秒，它只應該在計時器結束時。

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>應用程式的資料流程

Apple 建議下列使用資料流程：

[![](background-tasks-images/update17.png "應用程式資料流程圖")](background-tasks-images/update17.png#lightbox)

外部事件 （例如 監看式連線） 會喚醒應用程式。 這會強制此應用程式更新其資料模型 （也就表示應用程式的目前狀態）。 如此一來的資料模型變更應用程式需要更新其複雜性，要求新的快照集，可能是啟動背景`NSURLSession`提取更多資料，並排程進一步的背景重新整理。

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>應用程式生命週期

因為停駐和釘選它，將更多的應用程式之間移動使用者的 Apple 認為慣用的應用程式的能力頻率遠，然後他們未運用 watchOS 2。 如此一來，應用程式應該準備好要處理這項變更，並快速在其間的前景和背景狀態移動。

Apple 會有下列建議：

- 請確定應用程式完成輸入前景啟動時儘速任何背景工作。
- 請務必完成所有前景工作後，再藉由呼叫進入背景`NSProcessInfo.PerformExpiringActivity`。
- WatchOS 模擬器中測試應用程式時，因此需要以適當測試的功能時，可以重新整理應用程式，無工作預算將會強制執行。
- 一定要測試實際的 Apple Watch 硬體，以確保應用程式未執行過發行前其預算，itunes Connect。
- 讓 Apple Watch 上充電器維持在測試和偵錯時，Apple 建議。
- 請確定 throughly 測試 冷啟動和繼續執行應用程式。
- 請確認所有的應用程式工作，正在完成。
- 變更測試最佳及最差的停駐在釘選的應用程式的數目的案例。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋 Apple 對 watchOS 以及如何使用它們來保持最新的監看式應用程式的增強功能。 首先，它涵蓋所有的新背景工作 Apple 已新增在 watchOS 3 中。 然後，它涵蓋背景 API 生命週期，以及如何在 Xamarin watchOS 應用程式中實作背景工作。 最後，它涵蓋如何排程的運作方式，並提供一些最佳作法。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
