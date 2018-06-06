---
title: iOS Backgrounding 與工作
description: 本文件說明如何使用背景工作後的應用程式會放在背景中執行長時間執行的工作。
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a95ca128bc6de7b2adc75511a581f5d2779d9c06
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784351"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding 與工作

最簡單的方式執行 backgrounding 在 iOS 上是要為工作時，中斷您 backgrounding 的需求，並在背景中執行工作。 工作受到嚴格的時間限制，並通常約 600 秒 （10 分鐘） 的處理時間後應用程式已移至背景上 iOS 6，10 分鐘內 iOS 7 + 上。

背景工作可分成三個類別：

1.  **安全背景工作**-呼叫中的任何位置有一項工作的所在應用程式不想中斷應用程式應輸入背景。
1.  **DidEnterBackground 工作**-呼叫期間`DidEnterBackground`協助您清理及狀態儲存的應用程式生命週期方法。
1.  **背景傳送 (iOS 7 +)** -一種特殊的背景工作用來執行 iOS 7 上的網路傳送。 不同於一般工作，背景傳送沒有預先決定的時間限制。


背景安全和`DidEnterBackground`工作可放心地使用 iOS 6 和 iOS 7，但有些微的差異。 讓我們來調查這兩種類型的更詳細的工作。

## <a name="creating-background-safe-tasks"></a>建立安全的背景工作

某些應用程式包含不應該會暫時中斷 iOS 應用程式應該會變更狀態的工作。 中斷時，防止這些工作的一種方式是以 iOS 長時間執行工作的形式進行註冊。 您可以使用此模式任何位置應用程式中，您不想中斷工作在背景中應該使用者放入應用程式。 此模式的絕佳候選項目會是工作，例如將新使用者的登錄資訊傳送給您的伺服器，或驗證登入資訊。

下列程式碼片段示範註冊要在背景中執行的工作：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

註冊程序組的唯一識別碼，與工作`taskID`，並再將它包裝在比對中`BeginBackgroundTask`和`EndBackgroundTask`呼叫。 若要產生的識別碼，我們對進行呼叫`BeginBackgroundTask`方法`UIApplication`物件，並通常是新的執行緒上啟動 「 長時間執行工作。 當工作完成時，我們稱之為`EndBackgroundTask`並傳入相同的識別項。 這是很重要，因為 iOS 會終止該應用程式，如果`BeginBackgroundTask`呼叫不含相符`EndBackgroundTask`。

> [!IMPORTANT]
> 安全背景工作可以在主執行緒或根據應用程式的需求而定，背景執行緒上執行。


## <a name="performing-tasks-during-didenterbackground"></a>DidEnterBackground 期間執行工作

除了背景安全讓長時間執行工作，註冊可用於開始應用程式會放入背景工作。 iOS 提供事件的方法中*AppDelegate*類別稱為 「 `DidEnterBackground` ，可以用來儲存應用程式狀態、 儲存使用者資料和應用程式進入背景之前加密敏感的內容。 應用程式已經從這個方法傳回大約五秒，或將取得終止。 因此，可能需要五秒以上才能完成的清除工作可以呼叫從內部`DidEnterBackground`方法。 這些工作必須在個別執行緒上叫用。

處理程序是幾乎完全相同的註冊長時間執行工作。 下列程式碼片段將說明這點在動作：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

我們藉由覆寫開始`DidEnterBackground`方法中的`AppDelegate`，其中我們註冊我們的工作，透過`BeginBackgroundTask`可以如同我們在先前的範例。 接下來，我們會產生新的執行緒，並執行我們的長時間執行工作。 請注意，`EndBackgroundTask`現在從進行呼叫，在長時間執行的工作，因為`DidEnterBackground`都已傳回方法。

> [!IMPORTANT]
> 使用 iOS[監視機制](http://developer.apple.com/library/ios/qa/qa1693/_index.html)以確保應用程式的 UI 保持回應。 應用程式於花費太多時間在`DidEnterBackground`將不會回應在 UI 中。 無法關閉在背景中執行的工作可讓`DidEnterBackground`傳回及時，讓 UI 保持回應，並防止監視終止應用程式。


## <a name="handling-background-task-time-limits"></a>處理背景工作時間限制

iOS 放置於嚴格限制可以執行長時間的背景工作，以及如果`EndBackgroundTask`未配置的時間內進行呼叫，應用程式將會終止。 持續追蹤的剩餘 backgrounding 時間，和使用到期的處理常式在必要時，我們可以避免 iOS 終止應用程式。

### <a name="accessing-background-time-remaining"></a>存取背景剩餘的時間

如果已註冊的工作的應用程式取得移至背景，已註冊的工作會執行收到約 600 秒。 我們可以檢查工作已經完成使用靜態多少時間`BackgroundTimeRemaining`屬性`UIApplication`類別。 下列程式碼會讓我們的時間 （秒），已離開我們的背景工作：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>避免使用過期的處理常式的應用程式終止

除了讓可存取`BackgroundTimeRemaining`屬性 iOS 提供依正常程序的方式來處理透過背景時間逾期**到期處理常式**。 這是選擇性的區塊，將取得在分配的時間，工作即將到期時執行的程式碼。 到期的處理常式中的程式碼會呼叫`EndBackgroundTask`並傳入工作識別碼，表示應用程式運作良好，並防止 iOS 終止應用程式，即使超出時間執行的工作。 `EndBackgroundTask` 必須在到期處理常式，以及正常執行過程中呼叫。 

匿名函式使用 lambda 運算式，以表示到期處理常式，如下所示：

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

無法執行程式碼需要到期處理常式時，您應該一律使用到期處理常式與背景工作。

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>在 iOS 7 + 中的背景工作

在 iOS 7 方面的背景工作的最大的變更是不工作中實作方式，但當它們執行。

前文提過，iOS 7 前，在背景執行的工作有 600 秒才能完成。 此限制的其中一個原因是工作的，在背景執行的工作會讓裝置醒著的持續時間:

 [![](ios-backgrounding-with-tasks-images/ios6.png "讓應用程式保持喚醒狀態前 iOS 7 的工作的圖形")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 背景處理適合用於較長的電池壽命。 在 iOS 7，backgrounding 會成為隨機： 工作尊重當裝置進入睡眠狀態，並改為處理撥打電話、 通知、 內送電子郵件，以及其他裝置甦醒時，請勿以區塊及其處理而不是讓裝置保持喚醒狀態，常見的中斷。 下圖提供深入了解如何工作可能會損毀組成：

 [![](ios-backgrounding-with-tasks-images/ios7.png "工作分解成區塊後的 iOS 7 的圖表")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

因為工作執行階段不會再連續，執行網路傳輸的工作必須先在 iOS 7 以不同的方式處理。 開發人員都使用`NSURlSession`API 來處理網路傳輸。 下一節是背景傳送的概觀。

 <a name="background-transfers" />

## <a name="background-transfers"></a>背景傳送

在 iOS 7 中的背景傳送的骨幹是新`NSURLSession`應用程式開發介面。 `NSURLSession` 可讓我們建立的工作：

1.  傳輸內容透過網路與裝置中斷。
1.  上傳和下載大型檔案 (*背景傳送服務*)。


讓我們進一步查看其運作方式。

### <a name="nsurlsession-api"></a>NSURLSession 應用程式開發介面

 `NSURLSession` 是功能強大的應用程式開發介面透過網路傳送內容。 它提供一組工具來處理透過網路中斷和應用程式狀態變更的資料傳輸。

`NSURLSession`應用程式開發介面會建立一或多個依次繁衍 （spawn） 在網路上的快速導覽相關的資料區塊的工作的工作階段。 將資料傳送快速且可靠地以非同步方式執行工作。 因為`NSURLSession`是非同步的每個工作階段需要讓系統和應用程式知道傳輸完成時完成處理常式區塊。

若要執行適用於預先 iOS 7 和後置 iOS 7 使用網路轉移，請檢查如果`NSURLSession`會提供給加入佇列的傳輸來說，以及使用一般的背景工作來執行傳輸，如果不是：

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> 請避免對更新 UI 的背景中 iOS 6 相容的程式碼，從 iOS 6 不支援背景 UI 更新，而且將會終止該應用程式進行呼叫。


`NSURLSession`應用程式開發介面包含一組豐富的功能，可處理驗證、 管理失敗的傳輸，以及報告用戶端-但沒有伺服器端的錯誤。 它可協助的橋接器在工作中斷執行 iOS 7，導入的時間，並也提供支援快速且可靠地傳送大型檔案。 下一節探討此第二個功能。

### <a name="background-transfer-service"></a>背景傳送服務

在 iOS 7 之前, 上傳或下載檔案，在背景中的已不可靠。 背景工作取得有限的時間，若要執行，但傳輸檔案所花費的時間會隨著網路和檔案的大小。 在 iOS 7 中，我們可以使用`NSURLSession`成功上傳和下載大型檔案。 特定`NSURLSession`處理在背景中的大型檔案的網路傳輸的工作階段型別稱為*背景傳送服務*。

傳輸使用背景傳送服務起始作業系統所管理，且提供 Api 來處理驗證及錯誤。 因為傳輸都不受限於任意的時間限制，它們可用來上傳或下載大型檔案，自動更新中的背景，以及其他內容。 請參閱[背景傳送的逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)如需如何實作服務的詳細資訊。

背景傳送服務通常搭配背景擷取或遠端通知以協助應用程式重新整理在背景中的內容。 在下面兩個區段中，我們引進註冊整個上 iOS 6 和 iOS 7 在背景中執行的應用程式的概念。

