---
title: iOS 背景處理與工作
description: 本檔說明如何在應用程式置於背景後，使用背景工作來執行長時間執行的工作。
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: a8259cf47f8af6e356c9a860c61ad0eea0c8927a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932973"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS 背景處理與工作

在 iOS 上執行背景處理最簡單的方式，就是將您的背景處理需求分解成工作，並在背景執行工作。 工作處於嚴格的時間限制，在應用程式移至 iOS 6 上的背景並于 iOS 7 + 上小於10分鐘時，通常會有大約600秒（10分鐘）的處理時間。

背景工作可分成三個類別：

1. **背景安全**的工作-在應用程式中的任何位置呼叫您不想要中斷的工作，應用程式應進入背景。
1. **DidEnterBackground**工作-在 `DidEnterBackground` 應用程式生命週期方法期間呼叫，以協助清除和儲存狀態。
1. **背景傳輸（ios 7 +）** -一種特殊類型的背景工作，用來在 iOS 7 上執行網路傳輸。 不同于一般工作，背景傳輸沒有預先決定的時間限制。

`DidEnterBackground`在 ios 6 和 ios 7 上都可以安全地使用背景安全和工作，但有一些些許差異。 讓我們更詳細地調查這兩種類型的工作。

## <a name="creating-background-safe-tasks"></a>建立背景安全工作

某些應用程式包含 iOS 應該不會在應用程式變更狀態時中斷的工作。 保護這些工作不被中斷的其中一種方式，就是將其註冊為 iOS 做為長時間執行的工作。 您可以在應用程式中的任何位置使用此模式，而不希望使用者將應用程式放入背景。 這種模式的絕佳候選，就是將新使用者的註冊資訊傳送到您的伺服器，或驗證登入資訊等工作。

下列程式碼片段示範如何註冊要在背景中執行的工作：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

註冊程式會將具有唯一識別碼的工作配對， `taskID` 然後將它包裝在比對 `BeginBackgroundTask` 和 `EndBackgroundTask` 呼叫中。 為了產生識別碼，我們對 `BeginBackgroundTask` 物件呼叫方法 `UIApplication` ，然後啟動長時間執行的工作，通常是在新的執行緒上。 當工作完成時，我們會呼叫 `EndBackgroundTask` 並傳入相同的識別碼。 這很重要，因為如果呼叫沒有相符的，iOS 會終止應用程式 `BeginBackgroundTask` `EndBackgroundTask` 。

> [!IMPORTANT]
> 背景安全的工作可以在主執行緒或背景執行緒上執行，視應用程式的需求而定。

## <a name="performing-tasks-during-didenterbackground"></a>在 DidEnterBackground 期間執行工作

除了讓長時間執行的工作成為背景安全，註冊也可以在應用程式進入背景時用來啟動工作。 iOS 提供名為的*AppDelegate*類別中的事件方法， `DidEnterBackground` 可在應用程式進入背景之前，用來儲存應用程式狀態、儲存使用者資料，以及將機密內容加密。 應用程式大約有五秒的時間可從這個方法傳回，否則將會終止。 因此，可能需要超過五秒才能完成的清除工作，可以從方法內呼叫 `DidEnterBackground` 。 這些工作必須在不同的執行緒上叫用。

此程式與註冊長時間執行之工作的過程幾乎完全相同。 下列程式碼片段將示範此動作：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

我們一開始會先覆寫 `DidEnterBackground` 中的方法，我們將透過來 `AppDelegate` 註冊我們的 `BeginBackgroundTask` 工作，就像我們在上一個範例中所做的一樣。 接下來，我們會產生新的執行緒，並執行我們的長時間執行工作。 請注意， `EndBackgroundTask` 現在會從長時間執行的工作內進行呼叫，因為 `DidEnterBackground` 方法已經傳回。

> [!IMPORTANT]
> iOS 使用[看門狗機制](https://developer.apple.com/library/ios/qa/qa1693/_index.html)來確保應用程式的 UI 保持回應。 在中花費太多時間的應用程式， `DidEnterBackground` 在 UI 中會變得沒有回應。 開始 off 在背景執行的工作允許 `DidEnterBackground` 及時傳回，讓 UI 保持回應，並防止看門狗終止應用程式。

## <a name="handling-background-task-time-limits"></a>處理背景工作時間限制

iOS 會嚴格限制背景工作可執行檔時間長度，而且如果在 `EndBackgroundTask` 分配的時間內未進行呼叫，應用程式將會終止。 藉由追蹤剩餘的背景處理時間，並在必要時使用到期處理常式，我們可以避免 iOS 終止應用程式。

### <a name="accessing-background-time-remaining"></a>存取剩餘的背景時間

如果有已註冊工作的應用程式移至背景，則已註冊的工作大約會有600秒的時間執行。 我們可以使用類別的靜態屬性來檢查工作必須完成多少時間 `BackgroundTimeRemaining` `UIApplication` 。 下列程式碼會提供我們的背景工作已離開的時間（以秒為單位）：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>使用到期處理常式避免應用程式終止

除了授與屬性的存取權之外 `BackgroundTimeRemaining` ，iOS 還提供了一種正常的方式，可透過**到期處理常式**來處理背景時間到期。 這是選擇性的程式碼區塊，會在為工作分配的時間即將到期時執行。 到期處理常式中的程式碼 `EndBackgroundTask` 會呼叫並傳入工作識別碼，這表示應用程式的行為良好，並防止 iOS 終止應用程式，即使工作執行時間已用盡也一樣。 `EndBackgroundTask`必須在到期處理常式內，以及在正常執行的過程中呼叫。 

到期處理常式會使用 lambda 運算式來表示為匿名函式，如下所示：

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

雖然執行程式碼時不需要到期處理常式，但您應該一律使用具有背景工作的到期處理常式。

 <a name="background_tasks_in_iOS_7"></a>

## <a name="background-tasks-in-ios-7"></a>IOS 7 + 中的背景工作

IOS 7 在背景工作方面的最大變更，不是工作的執行方式，而是在執行時。

回想一下，iOS 之前7，在背景中執行的工作有600秒的時間完成。 這項限制的其中一個原因是在背景執行的工作會讓裝置在工作期間處於喚醒狀態：

 [![讓應用程式在 iOS 前7之前保持喚醒的工作圖形](ios-backgrounding-with-tasks-images/ios6.png)](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 背景處理已針對較長的電池壽命進行優化。 在 iOS 7 中，背景處理會變得很有機會：而不是讓裝置保持在作用中狀態，而是當裝置進入睡眠狀態時，以區塊方式進行處理，而當裝置喚醒以處理電話、通知、內送電子郵件和其他常見的中斷時，則會以區塊執行。 下圖提供如何中斷工作的深入解析：

 [![在 iOS 7 之後分成區塊的工作圖形](ios-backgrounding-with-tasks-images/ios7.png)](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

由於工作執行時間不會再持續，執行網路傳輸的工作必須在 iOS 7 中以不同的方式處理。 建議開發人員使用 `NSURlSession` API 來處理網路傳輸。 下一節是背景傳輸的總覽。

 <a name="background-transfers"></a>

## <a name="background-transfers"></a>背景傳輸

IOS 7 中的背景傳輸骨幹是新的 `NSURLSession` API。 `NSURLSession`可讓我們建立工作來執行下列作業：

1. 透過網路和裝置中斷來傳輸內容。
1. 上傳和下載大型檔案（*背景傳輸服務*）。

讓我們進一步瞭解其運作方式。

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession`是功能強大的 API，可透過網路傳輸內容。 它提供了一組工具，可透過網路中斷和應用程式狀態的變更來處理資料傳輸。

此 `NSURLSession` API 會建立一或多個會話，然後在整個網路中產生工作，使相關資料得以快速地封鎖。 工作會以非同步方式執行，以快速且可靠地傳輸資料。 因為 `NSURLSession` 是非同步，所以每個會話都需要完成處理常式區塊，讓系統和應用程式知道何時傳送完成。

若要執行在 iOS 前7和後 iOS 7 上都有效的網路轉移，請檢查 `NSURLSession` 是否有可供排入佇列的傳輸，並使用一般的背景工作來執行傳輸（如果不是的話）：

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
> 避免在 iOS 6 相容程式碼的背景進行呼叫以更新 UI，因為 iOS 6 不支援背景 UI 更新，並會終止應用程式。

此 `NSURLSession` API 包含一組豐富的功能，可處理驗證、管理失敗的傳輸，以及報告用戶端但不是伺服器端錯誤。 它有助於橋接 iOS 7 中引進的工作執行時間中斷，同時也提供快速且可靠地傳輸大型檔案的支援。 下一節將探討第二個功能。

### <a name="background-transfer-service"></a>背景傳送服務

在 iOS 7 之前，在背景上傳或下載檔案並不可靠。 背景工作的執行時間有限，但傳輸檔案所需的時間會因網路和檔案大小而異。 在 iOS 7 中，我們可以使用 `NSURLSession` 來成功上傳和下載大型檔案。 在 `NSURLSession` 背景處理大型檔案之網路傳輸的特定會話類型，稱為「*背景傳輸服務*」。

使用背景傳輸服務起始的傳輸是由作業系統所管理，並提供 Api 來處理驗證和錯誤。 因為傳輸不是以任意時間限制來系結，所以可以用來上傳或下載大型檔案、在背景中自動更新內容等等。 如需如何執行服務的詳細資訊，請參閱[背景傳輸逐步](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)解說。

背景傳送服務通常會與背景提取或遠端通知配對，以協助應用程式在背景中重新整理內容。 在接下來的兩節中，我們引進了註冊整個應用程式以在 iOS 6 和 iOS 7 的背景中執行的概念。
