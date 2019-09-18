---
title: IOS 11 中的 SiriKit 更新
description: 本檔說明如何使用 iOS 11 中的 SiriKit。 特別是，它會檢查如何使用工作和便箋，以及如何提供應用程式的替代名稱。
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/07/2017
ms.openlocfilehash: 27adc8aa2ed0fec09fe38b9ea31834ea6e75f845
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033092"
---
# <a name="sirikit-updates-in-ios-11"></a>IOS 11 中的 SiriKit 更新

SiriKit 是在 iOS 10 中引進，其中包含數個服務網域（包括 workouts、預約預訂及進行呼叫）。 請參閱[SiriKit 一節](~/ios/platform/sirikit/index.md)中的 SiriKit 概念，以及如何在您的應用程式中執行 SiriKit。

![Siri 工作清單示範](sirikit-images/sirikit.png)

IOS 11 中的 SiriKit 新增了這些新的和更新的意圖網域：

- [**清單和附注**](#listsnotes)–新增！ 提供應用程式 API 來處理工作和附注。
- **Visual 代碼**–新增！ Siri 可以顯示 QR 代碼，以共用連絡人資訊或參與付款交易。
- **付款**–新增用於付款互動的搜尋和轉移意圖。
- 您的**預約**–新增了取消的與意見反應意圖。

其他新功能包括：

- [**替代應用程式名稱**](#alternativenames)–提供別名，可協助客戶藉由提供替代名稱/發音來告知 Siri 目標應用程式。
- **啟動 Workouts** –提供在背景啟動測驗的功能。

以下說明其中一些功能。 如需其他專案的詳細資訊，請參閱[Apple 的 SiriKit 檔](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes" />

## <a name="lists-and-notes"></a>清單和附注

新的清單和 notes 網域提供應用程式的 API，可透過 Siri 語音要求來處理工作和附注。

**工作**

- 具有 [標題] 和 [完成] 狀態。
- 選擇性地包含期限和位置。

**注意事項**

- 具有 [標題] 和 [內容] 欄位。

工作和附注都可以組織成群組。 本節的其餘部分將說明如何使用[TasksNotes SiriKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)，透過 SiriKit 來執行這個新的網域。

### <a name="how-to-process-a-sirikit-request"></a>如何處理 SiriKit 要求

依照下列步驟處理 SiriKit 要求：

1. **Resolve** –驗證參數並向使用者要求進一步的資訊（如有需要）。
2. **確認**–可處理要求的最終驗證和驗證。
3. **處理**–執行操作（更新資料或執行網路作業）。

前兩個步驟是選擇性的（雖然鼓勵），而最後一個步驟是必要的。
[SiriKit 一節](~/ios/platform/sirikit/index.md)中有更詳細的指示。

### <a name="resolve-and-confirm-methods"></a>Resolve 和 Confirm 方法

這些選擇性的方法可讓您的程式碼執行驗證、選取預設值，或向使用者要求其他資訊。

例如，針對`IINCreateTaskListIntent`介面，所需的方法是`HandleCreateTaskList`。 有四個選擇性的方法可讓您更充分掌控 Siri 互動：

- `ResolveTitle`–驗證標題、設定預設標題（如果有的話），或表示不需要資料的信號。
- `ResolveTaskTitles`–驗證使用者所說的工作清單。
- `ResolveGroupName`-驗證組名、選擇預設群組，或表示不需要資料的信號。
- `ConfirmCreateTaskList`-驗證您的程式碼可以執行要求的作業，但不會執行它（只有`Handle*`方法應該修改資料）。

### <a name="handle-the-intent"></a>處理意圖

清單和附注網域中有六個意圖，三個用於工作，而三個用於附注。
您必須執行以處理這些意圖的方法如下：

- 針對工作：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 關於附注：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每個方法都有特定的意圖類型，其中包含從使用者的要求（也可能在`Resolve*`和`Confirm*`方法中更新）中 Siri 的所有資訊。
您的應用程式必須剖析提供的資料，然後執行一些動作來儲存或處理資料，並傳回 Siri 演說並向使用者顯示的結果。

### <a name="response-codes"></a>回應碼

必要`Handle*`和選擇性`Confirm*`方法會在物件傳遞至完成處理常式的物件上設定值，以指出回應碼。 回應來自`INCreateTaskListIntentResponseCode`列舉：

- `Ready`–在確認階段（即來自`Confirm*`方法，但不是`Handle*`從方法）傳回。
- `InProgress`–用於長時間執行的工作（例如網路/伺服器操作）。
- `Success`-回應成功作業的詳細資料（僅從`Handle*`方法）。
- `Failure`–表示發生錯誤，且作業無法完成。
- `RequiringAppLaunch`–無法由意圖處理，但可在應用程式中運作。
- `Unspecified`–不使用：將會向使用者顯示錯誤訊息。

若要深入瞭解這些方法和回應，請[參閱 Apple 的 SiriKit 清單和附注檔](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>執行清單和附注

[TasksNotes SiriKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)是使用下列步驟建立的，以將 SiriKit 支援新增至空白 iOS 應用程式。

首先，若要新增 SiriKit 支援，請針對您的 iOS 應用程式執行下列步驟：

1. **Plist**中的滴答**SiriKit** 。
2. 將 [**隱私權– Siri 使用說明**] 索引鍵新增至**plist**，並將訊息提供給您的客戶。
3. 呼叫應用`INPreferences.RequestSiriAuthorization`程式中的方法，以提示使用者允許 Siri 互動。
4. 在開發人員入口網站上，將 SiriKit 新增至您的應用程式識別碼，然後重新建立您的布建設定檔以包含新的權利。

然後將新的擴充功能專案新增至您的應用程式，以處理 Siri 要求：

1. 以滑鼠右鍵按一下您的方案，然後選擇 [新增] **> [加入新專案**]。
2. 選擇**iOS > 擴充功能 > 意圖延伸**模組 範本。
3. 將會加入兩個新的專案：意圖和 IntentUI。 自訂 UI 是選擇性的，因此範例只會在**意圖**專案中包含程式碼。

延伸模組專案是所有 SiriKit 要求的處理位置。 作為獨立的延伸模組，它不會自動與您的主要應用程式通訊，這通常是藉由使用應用程式群組來執行共用檔案儲存體來解決。

#### <a name="configure-the-intenthandler"></a>設定 IntentHandler

類別是 Siri 要求的進入點–每個意圖都會傳遞`GetHandler`至方法，這會傳回可處理要求的物件。 `IntentHandler`

下列程式碼顯示簡單的執行方式：

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

類別必須繼承自`INExtension`，而且因為此範例將會處理清單和附注意圖，所以它也會實行。 `IINNotebookDomainHandling`

> [!NOTE]
>
> - 在 .net 中，介面的慣例是以大寫`I`開頭，而 Xamarin 會在從 iOS SDK 系結通訊協定時遵守。
> - Xamarin 也會保留 iOS 的類型名稱，而 Apple 會使用類型名稱中的前兩個字元來反映類型所屬的架構。
> - 針對架構，類型前面會加上`IN*` （例如`Intents` `INExtension`），但這些_不_是介面。
> - 它也會遵循這種通訊協定（在C#中變成介面）， `I`最後會有兩`IINAddTasksIntentHandling`個，例如。

#### <a name="handling-intents"></a>處理意圖

每個意圖（新增工作、設定工作屬性等等）都會在類似下面所示的單一方法中執行。 方法應該執行三個主要功能：

1. **處理意圖**： Siri 所剖析的資料可在特定于意圖類型`intent`的物件中使用。 您的應用程式可能已使用選擇性`Resolve*`方法驗證該資料。
2. **驗證和更新資料存放區**–將資料儲存到檔案系統（使用應用程式群組，讓主要 iOS 應用程式也可以存取），或透過網路要求。
3. **提供回應**–使用`completion`處理常式將回應傳回給 Siri，以對使用者進行讀取/顯示：

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

請注意`null` ，會當做第二個參數傳遞至回應–這是使用者活動參數，當未提供時，將會使用預設值。
只要您的 iOS 應用程式透過`NSUserActivityTypes` **plist**中的金鑰來支援它，您就可以設定自訂活動類型。 接著，您可以在應用程式開啟時處理這種情況，並執行特定作業（例如開啟相關的視圖控制器並從 Siri 作業載入資料）。

此範例也會 callpriority `Success`結果，但在實際案例中，應新增適當的錯誤報表。

### <a name="test-phrases"></a>測試片語

下列測試片語應可在範例應用程式中使用：

- 「使用蘋果、香蕉和 pears 在 TasksNotes 中建立雜貨店」
- "Add task WWDC in TasksNotes"
- 「將工作 WWDC 新增至 TasksNotes 中的訓練清單」
- 「Mark 在 TasksNotes 中參加 WWDC complete」
- 「在 TasksNotes 時提醒我購買 iphone」
- 「Mark 在 TasksNotes 中購買 iPhone as completed」
- 「提醒我將家裡留在上午8點的 TasksNotes 中」

![建立新的清單範例](sirikit-images/createtasklist-sml.png) ![將工作設定為完整範例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 模擬器支援使用 Siri 進行測試（與舊版不同）。
>
> 如果在實際裝置上進行測試，別忘了設定應用程式識別碼和布建設定檔來 SiriKit 支援。

<a name="alternativenames" />

## <a name="alternative-names"></a>替代名稱

這項新的 iOS 11 功能表示您可以設定應用程式的替代名稱，以協助使用者使用 Siri 正確地觸發它。 將下列機碼新增至 iOS 應用程式專案的**plist**檔案：

![Plist 顯示替代的應用程式名稱金鑰和值](sirikit-images/alternative-names.png)

設定替代的應用程式名稱之後，下列片語也適用于範例應用程式（實際上是名為**TasksNotes**）：

- 「使用蘋果、香蕉和 pears 在_MonkeyNotes_中建立雜貨店」
- "Add task WWDC in _MonkeyTodo_"

## <a name="troubleshooting"></a>疑難排解

當您執行範例或將 SiriKit 新增至您自己的應用程式時，可能會遇到一些錯誤：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_已擲回目標-C 例外狀況。名稱：NSInternalInconsistencyException 原因：使用類別 < INPreferences：應用程式的 0x60400082ff00 > 需要 siri 的權利。您是否已在 Xcode 專案中啟用 Siri 功能？_

- SiriKit 是核取在**plist**。
- **Plist**是在 專案選項 中設定 **> 組建 > IOS**套件組合簽署。

  [![顯示正確設定權利的專案選項](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- （適用于裝置部署）應用程式識別碼已啟用 SiriKit，且已下載布建設定檔。

## <a name="related-links"></a>相關連結

- [SiriKit （Apple）](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)
- [SiriKit 的新功能（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/214/)
