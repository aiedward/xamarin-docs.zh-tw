---
title: watchOS Xamarin 的背景工作
description: 本文件說明如何使用 Xamarin，看看一個背景工作類型、 使用的資源，背景工作、 排程、 最佳做法及多個實作中 watchOS 背景工作。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/13/2017
ms.openlocfilehash: 5ab53d4aea32cf41c492e286c18cbe85a619889a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792043"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS Xamarin 的背景工作

使用 watchOS 3，有三種主要的方法，監看式應用程式可以保持在其資訊： 

- 使用其中一種數個新的背景工作。 
- 有一個其複雜性 （讓它的額外時間來更新） watch 錶面上。 
- 需要新的停駐到應用程式的使用者 pin 碼 (其中其保留在記憶體中，經常更新)。 

## <a name="keeping-an-app-up-to-date"></a>保持最新狀態的應用程式

之前討論的所有開發人員可以保留 watchOS 應用程式的資料和使用者介面，目前和更新的方式，本節會介紹在一般設定中的使用模式和使用者可能會移動其 iPhone 之間全天根據其 Apple Watch 的方式 日期的活動與目前執行 （例如開車）。

請使用以下範例：

[![](background-tasks-images/update00.png "使用者可能會移動其 iPhone 和他們的 Apple Watch 全天之間的方式")](background-tasks-images/update00.png#lightbox)

1. 在早上，在咖啡，列在等待時使用者瀏覽幾分鐘的時間的最新新聞，其 iPhone 上。
2. 之前離開咖啡廳，他們可以快速檢查其 watch 錶面上編譯與氣象。
3. 之前用餐，用以對應應用程式在 iPhone 上尋找附近的餐廳和活頁簿的保留項目，以符合用戶端。
4. 出差到 「 餐廳 」 中，他們會收到通知，他們 Apple Watch 上且具有快速瀏覽，才會知道其午餐約會晚執行。
5. 在晚上，它們使用對應應用程式在 iPhone 上檢查流量之前開車首頁。
6. 方式家時，它們 iMessage 上收到通知，要求他們其 Apple Watch 挑選某些牛奶並使用 「 快速回覆 」 功能來傳送回應 [確定]。

因為 「 快速瀏覽 」 （少於三秒） 的使用者想要使用 Apple Watch 應用程式中，有通常的方式本質不足夠的時間來擷取所需的資訊，並顯示給使用者之前，先更新其 UI 的應用程式。

使用新的應用程式開發介面 Apple 已包含 watchOS 3，在應用程式可以排定_背景重新整理_和使用者要求它之前已準備所需的資訊。 採取上面所討論的天氣複雜的範例：

[![](background-tasks-images/update01.png "天氣複雜功能的範例")](background-tasks-images/update01.png#lightbox)

1. 應用程式的排程，以被喚醒系統在特定時間。 
2. 應用程式會擷取它將需要產生更新的資訊。
3. 應用程式會重新產生其使用者介面，以反映新的資料。
4. 當使用者 glances 在複雜應用程式的功能時，它會，使用者不需等候更新的最新的資訊。

如以上所示，watchOS 系統醒來時使用一或多個工作，其中的非常有限的集區可用的應用程式：

[![](background-tasks-images/update02.png "WatchOS 系統喚醒使用一個或多個工作的應用程式")](background-tasks-images/update02.png#lightbox)

Apple 建議進行這項工作的大部分 （因為它是這種有限的資源，應用程式） 藉由保留至其本身，直到應用程式已經完成更新本身的程序。

系統會傳遞這些工作藉由呼叫新`HandleBackgroundTasks`方法`WKExtensionDelegate`委派。 例如: 

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

當應用程式已完成指定的工作時，它就會傳回系統標記完成：

[![](background-tasks-images/update03.png "此工作會傳回系統標記完成")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>新背景工作

watchOS 3 會介紹幾個背景工作的應用程式可以用來更新使用者必須先開啟應用程式，例如其資訊確保它具有內容：

- **背景應用程式重新整理**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可以讓此應用程式更新其狀態在背景中的。 通常這包括另一個工作，例如下載新的內容，從網際網路使用[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **背景重新整理快照集**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可以讓此應用程式更新其內容和 UI，系統會將用來填入停駐的快照集之前。
- **背景監看式連線**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)配對 iPhone 從接收背景資料時，啟動工作的應用程式。
- **URL 的工作階段的背景**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)背景傳送需要授權，或完成 （成功或錯誤） 時，啟動工作的應用程式。

將在以下各節中詳細討論這些工作。

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是可以排程決定未來的應用程式的一般工作：

[![](background-tasks-images/update04.png "決定未來 WKApplicationRefreshBackgroundTask")](background-tasks-images/update04.png#lightbox)

內工作的執行階段，應用程式進行本機處理，例如更新的任何複雜功能時間軸或擷取與某些必要的資料`NSUrlSession`。


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

系統會傳送`WKURLSessionRefreshBackgroundTask`當資料已完成下載並準備好要處理的應用程式：

[![](background-tasks-images/update05.png "當資料已完成下載 WKURLSessionRefreshBackgroundTask")](background-tasks-images/update05.png#lightbox)

應用程式不會繼續執行時在背景下載資料。 相反地，應用程式排程資料的要求已暫止然後系統會處理資料，下載完成時，只有 reawakening 應用程式的下載。

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

WatchOS 3，在 Apple 已加入停駐讓使用者可以釘選自己喜愛的應用程式及快速存取。 當使用者按下 Apple Watch 並排 按鈕時，將會顯示組件庫的已釘選的應用程式的快照集。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選應用程式來啟動它以執行的應用程式介面取代快照集。

[![](background-tasks-images/update06.png "快照集取代為正在執行的應用程式介面")](background-tasks-images/update06.png#lightbox)

系統會定期會採用應用程式的 UI 的快照集 (藉由傳送`WKSnapshotRefreshBackgroundTask`)，並使用這些快照集來填入停駐。 watchOS 可讓應用程式更新其內容和 UI 之前擷取這個快照集。

快照集是 watchOS 3 中非常重要，因為它們作為 preview 並啟動應用程式的映像。 如果使用者 settles 停駐在應用程式為基礎，其將展開為全螢幕、 輸入前景並開始執行，因此務必快照集是最新的：

[![](background-tasks-images/update07.png "如果使用者 settles 中停駐的應用程式上，它會展開為全螢幕")](background-tasks-images/update07.png#lightbox)

同樣地，系統會發出`WKSnapshotRefreshBackgroundTask`，讓應用程式之前，可以準備 （藉由更新資料與 UI） 擷取快照集：

[![](background-tasks-images/update08.png "應用程式可以藉由更新資料及 UI，在擷取快照之前準備")](background-tasks-images/update08.png#lightbox)

當應用程式會將標示`WKSnapshotRefreshBackgroundTask`完成時，系統會自動讓應用程式的 UI 的快照集。

> [!IMPORTANT]
> 請務必一律排程` WKSnapshotRefreshBackgroundTask`應用程式已收到新的資料，並更新其使用者介面或使用者將不會看到已修改的資訊之後。




此外，當使用者從應用程式會收到通知，並點選它將應用程式帶到前景，快照集必須是最新狀態，因為它做為啟動螢幕：

[![](background-tasks-images/update09.png "使用者從應用程式會收到通知，並點選它帶到前景應用程式")](background-tasks-images/update09.png#lightbox)

如果因為使用者已互動 watchOS 應用程式已超過一小時，它可以還原成預設狀態。 預設狀態可能表示不同的應用程式的不同事物，並根據應用程式的設計，它可能不完全預設狀態。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

WatchOS 3，在 Apple 已透過新的背景重新整理 api 整合監看式連線`WKWatchConnectivityRefreshBackgroundTask`。 使用這項新功能，iPhone 應用程式可以重新將資料傳送到其監看式應用程式對應項目 watchOS 應用程式在背景中執行時：

[![](background-tasks-images/update10.png "IPhone 應用程式可以重新將資料傳送到其監看式應用程式對應項目 watchOS 應用程式在背景中執行時")](background-tasks-images/update10.png#lightbox)

起始複雜功能推播時，應用程式內容的檔案傳送，或更新的 iPhone 應用程式的使用者資訊會喚醒 Apple Watch 應用程式，在背景中。

監看式應用程式時透過事項`WKWatchConnectivityRefreshBackgroundTask`它必須使用標準的應用程式開發介面方法將從 iPhone 應用程式接收資料。

[![](background-tasks-images/update11.png "WKWatchConnectivityRefreshBackgroundTask 資料流程")](background-tasks-images/update11.png#lightbox)

1. 請確認是否已啟動工作階段。
2. 監視新`HasContentPending`屬性只要值是`true`，則應用程式仍必須處理的資料。 之前，應用程式應該儲存至工作，直到它已完成處理的所有資料。
3. 當沒有要處理更多資料 (`HasContentPending = false`)，標記完成才可將其傳回至系統的工作。 無法執行此動作會耗盡導致損毀報表的應用程式的配置的背景執行階段。

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>背景 API 生命週期

將所有新的背景工作 API 的項目放在一起，一組典型互動看起來會如下所示：

[![](background-tasks-images/update12.png "背景 API 生命週期")](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 應用程式會排定在背景工作，以在未來甦為某個時間點。
2. 應用程式是由系統決定，且傳送工作。
3. 應用程式會處理工作完成任何工作所需。
4. 如此一來處理工作，應用程式可能需要排程更多背景工作，以在未來，完成更多工作，例如下載其他內容使用`NSUrlSession`。
5. 應用程式標示工作已完成，並傳回至系統。

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>以負責的態度使用資源

就非常重要，watchOS 應用程式的行為以負責的態度這個生態系統內藉由限制系統的共用資源的消耗。

看看下列案例：

[![](background-tasks-images/update13.png "WatchOS 應用程式限制系統的共用資源的消耗")](background-tasks-images/update13.png#lightbox)

1. 使用者在下午 1:00 啟動 watchOS 應用程式。
2. 應用程式會排程工作，喚醒並下載新內容，在一小時內，在下午 2:00。
3. 在下午 1:50 使用者重新開啟應用程式，讓它在此階段中更新其資料和 UI。
4. 而非讓工作喚醒應用程式在 10 分鐘後，應用程式應該重新排程工作，稍後在下午 2:50 執行一小時。

雖然每個應用程式不同，Apple 建議尋找上述，以節省系統資源的 的使用方式，類似的模式。

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>實作的背景工作

這是因為範例中，這份文件會使用向使用者回報足球分數假 MonkeySoccer 運動應用程式。 

看看下列的一般使用案例：

[![](background-tasks-images/update14.png "一般使用案例")](background-tasks-images/update14.png#lightbox)

使用者的我的最愛足球小組正在播放大相符項目從 7:00 PM 為 9:00 PM，讓應用程式應預期使用者會定期檢查分數和它決定在 30 分鐘的更新間隔。

1. 在使用者開啟應用程式，它會排程工作的背景更新 30 分鐘後。 背景應用程式開發介面可讓背景工作，一個給定時間執行的一種。
2. 應用程式接收工作，並更新其資料和使用者介面，然後排程另一個背景工作 30 分鐘後。 請務必排程另一個背景工作，會記住的開發人員，或應用程式，便永遠不會重新喚醒以取得更多的更新。
3. 同樣地，應用程式接收工作及更新其資料，更新其 UI，並且排定另一個背景工作 30 分鐘後。
4. 相同的程序會重複一次。
5. 工作收到的最後一個背景和應用程式再次更新它的資料與 UI。 由於這是最終的分數，它不會針對新的背景重新整理排程。 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>排程背景更新

指定上述案例，MonkeySoccer 應用程式可以使用下列程式碼，來排程在背景更新：

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

它會建立新`NSDate`30 分鐘到未來時應用程式想要會喚醒並建立`NSMutableDictionary`保留要求的工作的詳細資料。 `ScheduleBackgroundRefresh`方法`SharedExtension`用來排定工作的要求。

系統將會傳回`NSError`如果它無法排程要求的工作。

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>處理更新

接下來，製作顯示更新的分數所需的步驟 5 分鐘時段詳述：

[![](background-tasks-images/update15.png "顯示更新的分數所需的步驟 5 分鐘時段")](background-tasks-images/update15.png#lightbox)

1. 在下午 7:30:02 應用程式是喚醒系統，並指定更新背景工作。 從伺服器取得最新的分數為其第一優先順序。 請參閱[排程 NSUrlSession](#Scheduling-a-NSUrlSession)下方。
2. 7:30:05 在應用程式完成原始的工作，系統將進入睡眠狀態的應用程式，並下載所要求的資料，在背景中繼續進行。
3. 當系統完成下載時，它會建立新工作喚醒應用程式，使它可以處理的已下載的資訊。 請參閱[處理背景工作](#Handling-Background-Tasks)和[處理下載完成](#Handling-the-Download-Completing)下方。 
4. 應用程式儲存更新的資訊，並將標示工作已完成。 開發人員可能會想要在這個階段中，更新應用程式的使用者介面，不過 Apple 建議排程快照集工作，以便處理該程序。 請參閱[排程快照集更新](#Scheduling-a-Snapshot-Update)下方。
5. 應用程式接收快照集工作中，更新其使用者介面，並將標記工作已完成。 請參閱[處理快照集更新](#Handling-a-Snapshot-Update)下方。

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>排程 NSUrlSession

下列程式碼可以用於排程下載最新的分數：

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

它會設定並建立新`NSUrlSession`，然後使用該工作階段建立新的下載工作使用`CreateDownloadTask`方法。 它會呼叫`Resume`下載用來啟動工作階段工作的方法。

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>處理背景工作

藉由覆寫`HandleBackgroundTasks`方法`WKExtensionDelegate`，應用程式可以處理傳入的背景工作：

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

`HandleBackgroundTasks`方法不斷循環的所有工作，系統已傳送應用程式 (在`backgroundTasks`) 搜尋`WKUrlSessionRefreshBackgroundTask`。 如果找到一個物件，將它重新加入工作階段並附加`NSUrlSessionDownloadDelegate`處理下載完成 (請參閱[處理下載完成](#Handling-the-Download-Completing)下方):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

工作中，直到它已完成將它加入至集合，它會保存在控點：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有應用程式收到的工作需要完成，目前正在處理，任何工作標示為已完成：

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>處理下載完成

MonkeySoccer 應用程式會使用下列`NSUrlSessionDownloadDelegate`委派處理下載完成，並處理要求的資料：

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

當初始化時，它會同時保存的控制代碼`ExtensionDelegate`和`WKRefreshBackgroundTask`，產生它。 它會覆寫`DidFinishDownloading`方法以處理下載完成。 然後使用`CompleteTask`方法`ExtensionDelegate`通知工作，就已經完成，並移除暫止工作的集合。 請參閱[處理背景工作](#Handling-Background-Tasks)上方。

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>排程快照集更新

下列程式碼可以用於排程快照集工作以更新 UI，使用最新的分數：

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

就像`ScheduleURLUpdateSession`上述方法，它會建立新`NSDate`時使用的應用程式想要會喚醒建立`NSMutableDictionary`保留要求的工作的詳細資料。 `ScheduleSnapshotRefresh`方法`SharedExtension`用來排定工作的要求。

系統將會傳回`NSError`如果它無法排程要求的工作。

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>處理快照集更新

若要處理 「 快照集工作`HandleBackgroundTasks`方法 (請參閱[處理背景工作](#Handling-Background-Tasks)上方) 會修改成如下所示：

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

正在處理的工作類型的測試方法。 如果是`WKSnapshotRefreshBackgroundTask`存取工作：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

方法會更新使用者介面，然後建立`NSDate`告訴系統時，快照集將會過期。 它會建立`NSMutableDictionary`含使用者資訊來描述新的快照集和標記快照工作已完成，但這項資訊：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它也會告訴 「 快照集工作的應用程式未傳回 （在第一個參數） 的預設狀態。 沒有任何概念的預設狀態的應用程式應該一律將此屬性設`true`。

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>有效率地運作

如同上述範例中更新其分數，有效率地運作，並使用新 watchOS MonkeySoccer 應用程式所需的五分鐘視窗 3 的背景工作，應用程式只是 active，總共 15 秒： 

[![](background-tasks-images/update16.png "應用程式只是 15 秒總共有作用中")](background-tasks-images/update16.png#lightbox)

這可減少應用程式會對可用的 Apple Watch 資源和電池壽命的影響，也可讓應用程式能更加配合監看式上執行其他應用程式。

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>排程的運作方式

在前景 watchOS 3 應用程式時，它一定會排定執行，並可以執行例如更新的資料所需的處理任何類型或重繪其 UI。 當應用程式移到背景時，通常由系統暫停，並會停止所有執行階段作業。 

在背景中應用程式時，它可能會成為系統快速執行特定工作的目標。 因此，在 watchOS 2，系統可能會暫時喚醒背景應用程式執行下列動作處理長的查詢通知，或更新應用程式的複雜性。 在 watchOS 3，有數個可以在背景中執行應用程式的新方法。

在背景中應用程式時，系統就會具有數個限制：

- 只會給予幾秒鐘的時間才能完成任何指定的工作。 系統會考量，不只傳遞的時間量，但也多少 CPU 能力的應用程式耗用來衍生此限制。
- 任何超出其限制的應用程式就會終止具有下列的錯誤代碼：
    - **CPU** -0xc51bad01
    - **時間**-0xc51bad02
- 系統將會根據要求來執行應用程式的背景工作的類型不同的限制。 例如，`WKApplicationRefreshBackgroundTask`和`WKURLSessionRefreshBackgroundTask`工作會提供稍微較長的執行階段透過其他類型的背景工作。

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>複雜性和應用程式更新

Apple 已新增到 watchOS 3 新背景工作，除了 watchOS 應用程式的複雜性可能會影響應用程式如何及何時接收背景更新。

複雜功能是提供有用的資訊一眼的小型視覺元素。 根據選取的監看式字體，使用者會具有自訂 watch 錶面與一或多個可由監看式中的應用程式 watchOS 3 提供的複雜功能的能力。

如果使用者可以包含一個應用程式的複雜性及其 watch 錶面上，它可讓應用程式更新下列優點：

- 它會導致系統上，以保留應用程式中啟動的已備妥狀態，其中它嘗試啟動應用程式在背景中的保存在記憶體，讓它額外時間來更新。
- 複雜性會保證至少 50 推送更新每日。

開發人員應一律盡可能建立自己的應用程式的極具吸引力的複雜性引誘使用者新增至其 watch 錶面上面所列的原因。

複雜性 watchOS 2，是應用程式收到在背景中的執行階段的主要方法。 WatchOS 3，在複雜應用程式將仍可確保接收每小時的多個更新，不過，它使用`WKExtensions`來要求更多的執行階段，以更新其複雜性。

看看下列用來更新連接的 iPhone 應用程式從複雜的程式碼：

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

它會使用`RemainingComplicationUserInfoTransfers`屬性`WCSession`查看多少個高優先權傳輸應用程式已離開的日期和然後根據該數字會採用動作。 如果應用程式開始不足傳輸，它可以拖延上傳送的次要更新，並沒有重大變更時，只傳送資訊。

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>排程和停駐

WatchOS 3，在 Apple 已加入停駐讓使用者可以釘選自己喜愛的應用程式及快速存取。 當使用者按下 Apple Watch 並排 按鈕時，將會顯示組件庫的已釘選的應用程式的快照集。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選應用程式來啟動它以執行的應用程式介面取代快照集。

[![](background-tasks-images/dock01.png "停駐")](background-tasks-images/dock01.png#lightbox)

系統會定期接受快照集的應用程式的 UI，並會使用這些快照集來填入文件。watchOS 可讓應用程式更新其內容和 UI 之前擷取這個快照集。

若要停駐釘選的應用程式可預期下列：

- 會接收至少有一個每小時更新。 這包括應用程式重新整理工作以及快照集工作。
- 更新預算分散所有停駐在應用程式。 因此更少的應用程式使用者已釘選，就越可能會收到每個應用程式的更新。
- 應用程式將會保存在記憶體中，所以當從停駐選取應用程式將會快速地繼續。

最後一個使用者執行的應用程式會被視為_最近使用_應用程式，將所佔用的停駐中的最後一個位置。 從那里的該處，使用者可以選擇將其釘選永久停駐到。 會處理最近使用的像是任何其他我的最愛應用程式使用者具有已釘選到停駐。

> [!IMPORTANT]
> 只有已加入主畫面的應用程式就不會有任何規則的排程。 若要接收一般排程和背景更新，應用程式_必須_加入停駐。

如同本文件稍早所述，因為它們作為 preview 並啟動應用程式的映像快照集是 watchOS 3 中非常重要。 如果使用者 settles 停駐在應用程式為基礎，其將展開為全螢幕、 輸入前景並開始執行，因此務必快照集是最新的。

有些時候可能會當系統決定需要全新的快照集的應用程式的 UI。 在此情況下，不會列入應用程式的執行階段預算的快照集要求。 下列將會觸發系統快照集要求：

- 複雜功能時間軸的更新。
- 與應用程式通知的使用者互動。
- 從前景切換為背景狀態。
- 在處於背景狀態的一小時，因此應用程式可以返回預設狀態。
- 當 watchOS 第一次開機。

<a name="Best-Practices" />

## <a name="best-practices"></a>最佳作法 

使用背景工作時，Apple 建議以下的最佳作法：

- 視應用程式需要更新排程。 每次執行應用程式應該重新評估其未來需求並視需要此排程的調整。
- 如果系統會傳送背景重新整理工作的應用程式不需要更新，直到實際需要的更新，延後工作。
- 請考慮所有可用的應用程式的執行階段商機：
    - 停駐、 前景啟用。
    - 通知。
    - 複雜功能的更新。
    - 背景重新整理。
- 使用`ScheduleBackgroundRefresh`一般用途的背景執行階段，例如：
    - 輪詢系統有任何資訊。
    - 排程未來`NSURLSessions`要求背景資料。 
    - 已知的時間會轉換。
    - 觸發複雜功能更新。

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>快照集的最佳作法

當使用快照集更新時，Apple 會進行下列建議：

- 使快照集只在需要時，比方說，明顯的內容變更。
- 避免高頻率的快照集失效。 例如，計時器應用程式不應該更新每秒快照集，它應該只有在執行時計時器已結束。

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>應用程式的資料流程

Apple 的建議使用資料流的下：

[![](background-tasks-images/update17.png "應用程式資料流程圖")](background-tasks-images/update17.png#lightbox)

外部事件 （例如監看式連線） 喚醒應用程式。 這會強制此應用程式更新其資料模型 （表示應用程式的目前狀態）。 結果的資料模型變更應用程式需要更新其複雜性，要求新的快照集，可能是啟動背景`NSURLSession`提取更多資料，並進一步排程背景重新整理。

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>應用程式生命週期

由於停駐和釘選它 Apple 認為使用者將會移動更多的應用程式之間的最愛的應用程式的功能經常然後一樣 watchOS 2 使用。 如此一來，應用程式應該準備好要處理這項變更，並快速移動之間的前景和背景的狀態。

Apple 有下列建議：

- 請確定應用程式完成儘速在輸入前景啟動任何背景工作。
- 請確定完成所有前景工作之前，請先在背景呼叫`NSProcessInfo.PerformExpiringActivity`。
- 在 watchOS 模擬器中測試應用程式時，沒有任何工作預算會強制執行所需正確測試功能，才能重新整理應用程式。
- 一律在實際的 Apple Watch 硬體，以確保應用程式未執行過發行前其預算 iTunes Connect 上進行測試。
- Apple 建議讓 Apple Watch 上充電器維持時測試和偵錯。
- 請確定同時冷啟動與繼續應用程式都會 throughly 進行測試。
- 請確認正在完成所有的應用程式工作。
- 變更固定於測試最佳與最差停駐的應用程式的數目的案例。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋蘋果對 watchOS，以及如何使用它們來追蹤監看式應用程式的最新的增強功能。 首先，它涵蓋所有新的背景工作 Apple 加入 watchOS 3。 然後，它涵蓋背景 API 生命週期，以及如何實作中的 Xamarin watchOS 應用程式的背景工作。 最後，它涵蓋如何排程運作，並提供一些最佳作法。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
