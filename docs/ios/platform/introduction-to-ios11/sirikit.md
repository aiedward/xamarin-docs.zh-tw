---
title: SiriKit
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/07/2017
ms.openlocfilehash: a712f7da0d57e81872d7f779cf0eb52c30ee3bc2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="sirikit"></a>SiriKit

SiriKit 推出的 iOS 10，以服務的網域 （包括健身，騎預約，以及呼叫） 的數字。 請參閱[SiriKit 區段](~/ios/platform/sirikit/index.md)SiriKit 概念以及如何在您的應用程式中實作 SiriKit。

![Siri 工作清單示範](sirikit-images/sirikit.png)

SiriKit iOS 11 中的將這些新增和更新的意圖網域：

- [**列出並備忘稿**](#listsnotes) – 新增 ！ 提供 API 的應用程式，以處理工作和資訊。
- **視覺化程式碼**– 新增 ！ Siri 可以顯示 QR 代碼，來共用連絡人資訊或參與付款交易。
- **付款**– 新增付款互動的搜尋和傳輸方式。
- **寫預約**– 新增取消騎和意見反應的對應方式。

其他新功能包括：

- [**替代的應用程式名稱**](#alternativenames) – 可協助客戶提供別名告訴 Siri 藉由提供其他名稱/發音應用程式為目標。
- **啟動健身**– 可讓您在背景中啟動健身。

下面將說明這其中部分功能。 如需其他的詳細資訊，請參閱[Apple SiriKit 文件](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes" />

## <a name="lists-and-notes"></a>清單和注意事項

新的清單及便箋定義域會提供應用程式的 API，以處理工作以及透過使用 Siri 語音要求的資訊。

**工作**

- 具有標題和完成狀態。
- 選擇性地包含期限和位置。

**備註**

- 具有標題和內容的欄位。

工作和資訊可以組織成群組。 本章節的其餘部分說明如何實作這個新的網域與 SiriKit，使用[TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)。

### <a name="how-to-process-a-sirikit-request"></a>如何處理 SiriKit 要求

依照下列步驟處理 SiriKit 要求：

1. **解決**-驗證參數，並進一步資訊，向使用者要求 （如有必要）。
2. **確認**– 最終驗證和驗證可以處理的要求。
3. **處理**– 執行的作業 （更新資料或執行網路作業）。

前兩個步驟是選擇性 （雖然建議），而且需要的最後一個步驟。
有更詳細的指示，在[SiriKit 區段](~/ios/platform/sirikit/index.md)。

### <a name="resolve-and-confirm-methods"></a>解決並確認方法

這些選擇性的方法可讓您從使用者執行驗證、 選取預設值或要求其他資訊的程式碼。

例如，針對`IINCreateTaskListIntent`介面，所需的方法是`HandleCreateTaskList`。 有四個選擇性方法，提供更充分掌控 Siri 互動：

- `ResolveTitle` – 驗證標題、 設定預設的標題 （如果適用），或表示不需要的資料。
- `ResolveTaskTitles` – 驗證使用者說出的工作的清單。
- `ResolveGroupName` – 驗證群組名稱、 選擇的預設群組，或表示不需要的資料。
- `ConfirmCreateTaskList` – 驗證您的程式碼可以執行要求的作業，但不會執行 (只有`Handle*`方法應該修改的資料)。

### <a name="handle-the-intent"></a>目的控制代碼

共有六個清單和附註的網域中的對應方式，三個用於工作和三個用於附註。
您必須實作以處理這些項目的的方法包括：

- 工作：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 如需資訊：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每個方法都有特定意圖類型傳遞給它，其中包含從使用者的要求已剖析 Siri 的所有資訊 (而且可能更新中`Resolve*`和`Confirm*`方法)。
您的應用程式必須剖析資料，然後執行一些動作來儲存或其他方式處理資料，並傳回結果，Siri 來說，並向使用者顯示。

### <a name="response-codes"></a>回應碼

所需`Handle*`和選擇性`Confirm*`方法所傳遞的物件上設定值，其完成處理常式來表示的回應碼。 回應來自`INCreateTaskListIntentResponseCode`列舉型別：

- `Ready` – 在確認階段傳回 (ie。 從`Confirm*`方法，而不是從`Handle*`方法)。
- `InProgress` – 用於長時間執行工作 （例如網路/伺服器作業）。
- `Success` – 成功的作業的詳細資料回應 (只能從`Handle*`方法)。
- `Failure` – 表示發生錯誤，而且無法完成作業。
- `RequiringAppLaunch` -無法處理的意圖，但是作業也可以在應用程式。
- `Unspecified` – 請勿使用： 將會向使用者顯示錯誤訊息。

深入了解這些方法和回應的 Apple [SiriKit 列出並附註文件](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>實作清單和注意事項

[TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)SiriKit 支援加入空白 iOS 應用程式中使用下列步驟所建立。

首先，若要新增 SiriKit 支援，請依照下列步驟進行 iOS 應用程式：

1. 刻度**SiriKit**中**Entitlements.plist**。
2. 新增**隱私權 – Siri 使用方式描述**鍵**Info.plist**，以及提供給客戶的訊息。
3. 呼叫`INPreferences.RequestSiriAuthorization`應用程式，以提示使用者允許 Siri 互動的方法。
4. SiriKit 加入您在開發人員入口網站上的應用程式識別碼和重新建立您要包含新的權利的佈建設定檔。

將新的擴充功能專案加入您的應用程式處理 Siri 要求：

1. 在您的方案上按一下滑鼠右鍵，然後選擇 **新增 > 加入新的專案...**.
2. 選擇**iOS > 延伸模組 > 對應方式擴充**範本。
3. 將會加入兩個新的專案： 意圖，而且 IntentUI。 自訂 UI 是選擇性，因此此範例只包含程式碼中的**意圖**專案。

擴充功能專案中為所有 SiriKit 要求將會都處理。 為個別的擴充功能，它會自動沒有與主要的應用程式 – 這通常會解決藉由實作使用應用程式群組的共用的檔案存放裝置通訊的任何方法。

#### <a name="configure-the-intenthandler"></a>設定 IntentHandler

`IntentHandler`類別是進入點，用來要求 Siri – 每個意圖會傳遞至`GetHandler`方法，這個方法會傳回物件，來處理要求。

下列程式碼顯示簡單的實作：

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

此類別必須繼承自`INExtension`，範例會處理清單，並備忘稿對應方式，因為它也會實作`IINNotebookDomainHandling`。

> [!NOTE]
> - 適用於.NET 介面加上大寫字母沒有慣例`I`，其中 Xamarin 遵守 iOS SDK 中的繫結通訊協定時。
> - Xamarin 也會保留型別名稱，從 iOS 和 Apple 型別名稱中使用前兩個字元，以反映型別所屬的架構。
> - 如`Intents`framework 中，型別前面會加上`IN*`（例如。 `INExtension`)，但這些是_不_介面。
> - 它也會遵循 （這會成為在 C# 中的介面） 通訊協定結束具有兩個`I`s，例如`IINAddTasksIntentHandling`。

#### <a name="handling-intents"></a>處理方式

類似下列所示的單一方法中實作的每個意圖 （加入工作、 設定工作屬性等等）。 此方法應該執行三項主要功能：

1. **處理意圖**– Siri 剖析的資料可在`intent`意圖類型特有的物件。 您的應用程式驗證使用選擇性資料`Resolve*`方法。
2. **驗證及更新資料存放區**– 儲存到檔案系統 （以便主要的 iOS 應用程式也可以存取它，請使用應用程式群組），或透過網路要求的資料。
3. **提供回應**– 使用`completion`傳送給使用者的讀取/顯示 Siri 回應的處理常式：

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

請注意，`null`傳遞作為第二個參數，以回應 – 這是使用者活動參數，而且未提供，將會使用預設值。
您可以設定自訂活動型別，只要您的 iOS 應用程式支援透過`NSUserActivityTypes`中的索引鍵**Info.plist**。 您可以開啟您的應用程式時，處理這種情況，並執行特定作業 （例如開啟相關檢視控制站和載入資料，從 Siri 作業）。

此範例也限定`Success`結果，但在實際案例中，適當的錯誤報告應該加入。

### <a name="test-phrases"></a>測試片語

下列測試片語應該以範例應用程式中運作：

- 「 在 TasksNotes 讓蘋果、 香蕉，與梨子雜貨清單 」
- 「 加入工作 WWDC TasksNotes 中 」
- 「 工作 WWDC 訓練清單中加入 TasksNotes"
- "記號以完成在 TasksNotes 出席 WWDC"
- "TasksNotes 中提醒我時得到首頁購買的 iphone 」
- 「 標記為已完成在 TasksNotes 購買 iPhone 」
- 「 提醒我首頁保留 TasksNotes 在上午 8 點 」

![建立新的清單範例](sirikit-images/createtasklist-sml.png) ![設定工作的完整範例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> 使用 Siri （不同於先前的版本） 來測試 iOS 11 模擬器支援。
>
> 如果在實際裝置上測試，別忘了設定您應用程式識別碼和佈建設定檔 SiriKit 支援。

<a name="alternativenames" />

## <a name="alternative-names"></a>替代名稱

這項新 iOS 11 功能表示您可以設定您的應用程式，以協助使用者正確使用 Siri 觸發的替代名稱。 加入下列機碼以**Info.plist** iOS 應用程式專案檔：

![Info.plist 顯示替代的應用程式名稱索引鍵和值](sirikit-images/alternative-names.png)

替代的應用程式名稱設定下，下列片語也能運作的範例應用程式 (這實際上名為**TasksNotes**):

- 「 蘋果、 香蕉，與在梨子雜貨清單_MonkeyNotes_"
- 「 加入工作中的 WWDC _MonkeyTodo_"


## <a name="troubleshooting"></a>疑難排解

執行範例，或將 SiriKit 加入至您的應用程式時可能會遇到一些錯誤：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Objective C 擲回例外狀況。名稱： NSInternalInconsistencyException 原因： 這個類別的用法 < INPreferences: 0x60400082ff00 > 從應用程式需要權利 com.apple.developer.siri。您在您的 Xcode 專案中啟用 Siri 功能？_

- SiriKit 核中**Entitlements.plist**。
- **Entitlements.plist**中設定**專案選項 > 建置 > iOS 套件組合簽署**。

  [![專案顯示權利正確設定的選項](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- （適用於裝置的部署）應用程式識別碼已啟用的 SiriKit 和佈建設定檔下載。


## <a name="related-links"></a>相關連結

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [什麼是新增在 SiriKit (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/214/)
