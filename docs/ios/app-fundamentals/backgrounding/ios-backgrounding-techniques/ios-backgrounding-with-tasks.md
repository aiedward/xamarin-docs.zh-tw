---
title: iOS 背景與工作
description: 本文件說明如何使用背景工作之後的應用程式會放在背景中執行長時間執行的工作。
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9d304ee64e7716413febc475e721f5eb39043109
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351534"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS 背景與工作

執行背景在 iOS 上的最簡單方式是 backgrounding 需求分成工作，並在背景中執行的工作。 工作下嚴格的時間限制，且通常取得 iOS 7 + 上的 約 600 秒 （10 分鐘） 的處理時間之後應用程式時，已移至背景上，在 iOS 6, 和 10 分鐘內。

背景工作可分成三個類別：

1.  **背景安全工作**-中的任何位置呼叫應用程式，您可在其中擁有的工作不想中斷應用程式應該輸入背景。
1.  **DidEnterBackground 工作**呼叫期間-`DidEnterBackground`應用程式生命週期方法，協助清除和儲存狀態。
1.  **背景傳輸 (iOS 7 +)** -一種特殊的背景工作用來在 iOS 7 上執行網路傳輸。 不同於一般工作，背景傳送沒有預先決定的時間限制。


背景安全和`DidEnterBackground`工作可放心地使用 iOS 6 和 iOS 7，但有些微的差異。 讓我們調查這兩種類型的更詳細的工作。

## <a name="creating-background-safe-tasks"></a>建立安全的背景工作

有些應用程式包含不應中斷 iOS 應用程式應該變更狀態的工作。 正在中斷時，防止這些工作的方法之一是向 iOS 為長時間執行的工作。 您可以使用此模式任何位置中您的應用程式不需要中斷的工作應該在背景的使用者放入應用程式的情況。 此模式的絕佳候選項目會將新使用者的註冊資訊傳送給您的伺服器，或驗證登入資訊等工作。

下列程式碼片段示範如何註冊要在背景中執行的工作：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

註冊程序組的唯一識別碼，與工作`taskID`，並再將它包裝在比對`BeginBackgroundTask`和`EndBackgroundTask`呼叫。 若要產生的識別項，我們呼叫以`BeginBackgroundTask`方法`UIApplication`物件，然後再開始長時間執行工作，通常新的執行緒上。 工作完成時，我們呼叫`EndBackgroundTask`並傳遞相同的識別項中。 這很重要，因為 iOS 會終止應用程式，如果`BeginBackgroundTask`呼叫沒有相對應的`EndBackgroundTask`。

> [!IMPORTANT]
> 在主執行緒或背景執行緒，視應用程式的需求而定，可以執行背景安全工作。


## <a name="performing-tasks-during-didenterbackground"></a>執行工作期間 DidEnterBackground

除了讓長時間執行的任務背景安全，註冊可用來啟動應用程式會放入背景工作。 iOS 提供事件方法中的*AppDelegate*類別，稱為`DidEnterBackground`，可用來儲存應用程式狀態、 儲存使用者資料和應用程式進入背景之前加密敏感的內容。 應用程式有從這個方法傳回大約五秒，或會將它終止。 因此，可能需要五秒以上才能完成的清除工作可以從呼叫內`DidEnterBackground`方法。 這些工作必須在個別執行緒上叫用。

此處理序幾乎完全相同的註冊長時間執行工作。 下列程式碼片段會說明這點作用中：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

首先藉由覆寫`DidEnterBackground`方法中的`AppDelegate`，其中我們註冊我們的工作，透過`BeginBackgroundTask`如同我們在上一個範例。 接下來，我們會繁衍新的執行緒，並執行我們的長時間執行工作。 請注意，`EndBackgroundTask`現在從進行呼叫，在長時間執行的工作，因為`DidEnterBackground`都已傳回方法。

> [!IMPORTANT]
> 使用 iOS[看門狗機制](http://developer.apple.com/library/ios/qa/qa1693/_index.html)以確保應用程式的 UI 保持回應。 應用程式花費太多時間在`DidEnterBackground`會變成無回應 UI 中。 開始在背景中執行的工作可讓`DidEnterBackground`傳回及時，保留 UI 回應，並避免看門狗終止應用程式。


## <a name="handling-background-task-time-limits"></a>處理背景工作時間限制

iOS 會嚴格限制可以執行長時間，以背景工作，以及如果`EndBackgroundTask`配置的時間內未進行呼叫，將會終止應用程式。 持續追蹤的剩餘背景的時間，及使用到期處理常式在必要時，我們就可以避免 iOS 終止應用程式。

### <a name="accessing-background-time-remaining"></a>存取背景剩餘的時間

如果應用程式與已註冊的工作取得移到背景中，已註冊的工作會執行取得約 600 秒。 我們可以檢查工作必須使用靜態完成多少時間`BackgroundTimeRemaining`屬性`UIApplication`類別。 下列程式碼會讓我們的時間，以秒為單位，我們的背景工作已離開：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>避免使用到期的處理常式的應用程式終止

除了提供存取權`BackgroundTimeRemaining`屬性，iOS 會提供優雅的方式，來處理透過背景時間逾期**到期處理常式**。 這是選擇性的區塊的工作分配的時間到期時取得執行的程式碼。 到期的處理常式中的程式碼會呼叫`EndBackgroundTask`，並傳入工作識別碼，這表示應用程式順利運作，並防止 iOS 終止應用程式，即使工作執行時間。 `EndBackgroundTask` 必須在到期處理常式中，以及正常執行的呼叫。 

到期的處理常式會以匿名函式使用 lambda 運算式，如下所示：

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

到期的處理常式則不需要執行的程式碼，您應該一律使用的過期處理常式，以背景工作。

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>在 iOS 7 + 中的背景工作

在 iOS 7 方面的背景工作中最大的改變是不工作實作方式，但當它們執行。

您應該記得前的 iOS 7、 在背景中執行的工作有 600 秒才能完成。 此限制的其中一個原因是工作的，在背景中執行的工作會讓裝置醒著的持續時間：

 [![](ios-backgrounding-with-tasks-images/ios6.png "讓應用程式保持甦醒狀態前 iOS 7 的工作的圖形")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 背景處理適合用於延長電池壽命。 在 iOS 7 中，背景會變成隨機： 而不是讓裝置保持甦醒狀態，工作會尊重時進入睡眠狀態，並改為執行區塊處理，以處理撥打電話、 通知、 內送電子郵件和其他裝置會甦醒時的裝置常見的中斷。 下圖提供深入了解可能中斷工作組成：

 [![](ios-backgrounding-with-tasks-images/ios7.png "工作正在分成區塊後的 iOS 7 的圖形")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

因為工作執行階段不會再持續，執行網路傳輸的工作必須處理以不同的方式在 iOS 7。 開發人員都使用`NSURlSession`API 來處理網路傳輸。 下一節是背景傳送的概觀。

 <a name="background-transfers" />

## <a name="background-transfers"></a>背景傳送

在 iOS 7 中的背景傳送的骨幹的新`NSURLSession`API。 `NSURLSession` 可讓我們建立的工作：

1.  傳輸內容透過網路與裝置中斷。
1.  上傳和下載大型檔案 (*背景傳送服務*)。


讓我們看看其運作方式。

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` 功能強大的開發是用於透過網路傳輸內容。 它提供一組工具來處理透過網路中斷和應用程式狀態變更的資料傳輸。

`NSURLSession` API 會建立一或多個依次繁衍工作，以在網路上的快速導覽相關的資料區塊的工作階段。 若要快速而可靠地傳輸資料以非同步方式執行工作。 因為`NSURLSession`是非同步的每個工作階段需要讓系統和應用程式知道完成傳輸時的完成處理常式區塊。

若要執行適用於預先 iOS 7 和後置的 iOS 7 使用網路轉移，請檢查如果`NSURLSession`適用於加入佇列的傳輸來說，來執行傳輸，如果不是使用一般的背景工作：

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
> 請避免對 iOS 6 不支援背景 UI 更新，以及將會終止應用程式更新 iOS 6 相容的程式碼，在背景從 UI 進行呼叫。


`NSURLSession` API 包含一組豐富的功能，以處理驗證、 管理失敗的傳輸，並報告用戶端-但沒有伺服器端的錯誤。 它可協助的橋接器在工作中斷執行 iOS 7 導入的時間，並快速且可靠地傳送大型檔案也提供支援。 下一節將探討這個第二個功能。

### <a name="background-transfer-service"></a>背景傳送服務

在 iOS 7、 之前上傳或下載檔案，在背景中的是不可靠的。 背景工作在有限的時間，若要執行，但是傳輸檔案所花費的時間會隨著網路和檔案的大小。 在 iOS 7 中，我們可以使用`NSURLSession`已成功上傳和下載大型檔案。 特定`NSURLSession`會處理在背景中的大型檔案的網路傳輸的工作階段型別稱為*背景傳送服務*。

使用背景傳送服務起始的傳輸由作業系統，並提供 Api 來處理驗證和錯誤。 因為傳輸不會受任意的時間限制，它們可用來上傳或下載大型檔案，自動更新的內容中的背景和更多功能。 請參閱[背景傳送的逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)如需有關如何實作服務。

背景傳送服務通常搭配背景擷取或遠端的通知，以協助應用程式的重新整理在背景中的內容。 在接下來兩節中，我們將介紹註冊在背景中執行 iOS 6 和 iOS 7 上的整個應用程式的概念。

