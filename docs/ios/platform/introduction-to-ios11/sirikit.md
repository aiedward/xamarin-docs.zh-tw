---
title: IOS 11 中的 SiriKit 更新
description: 本檔說明如何在 iOS 11 中使用 SiriKit。 尤其是，它會探討如何使用工作和注意事項，以及如何提供應用程式的替代名稱。
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/07/2017
ms.openlocfilehash: 7bc102069d673b9459c863282b0423952c8fa59d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437328"
---
# <a name="sirikit-updates-in-ios-11"></a>IOS 11 中的 SiriKit 更新

SiriKit 是在 iOS 10 引進的，其中包含多個服務網域 (包括 workouts、下預約，以及) 進行呼叫。 請參閱 [SiriKit 一節](~/ios/platform/sirikit/index.md) 中的 SiriKit 概念，以及如何在您的應用程式中執行 SiriKit。

![Siri 工作清單示範](sirikit-images/sirikit.png)

IOS 11 中的 SiriKit 新增了這些新的和更新的意圖網域：

- [**清單和附注**](#listsnotes) –新的！ 提供應用程式用來處理工作和附注的 API。
- **Visual 代碼** – New！ Siri 可以顯示 QR 代碼以共用連絡人資訊或參與付款交易。
- **付款** -已新增用於付款互動的搜尋和轉移意圖。
- **預約預約** -新增了取消的和意見反應意圖。

其他新功能包括：

- [**替代的應用程式名稱**](#alternativenames) ：提供別名，可協助客戶透過提供替代名稱/發音來指示 Siri 以應用程式為目標。
- **啟動 Workouts** –提供在背景中啟動測驗的能力。

以下將說明其中一些功能。 如需其他詳細資訊，請參閱 [Apple 的 SiriKit 檔](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes"></a>

## <a name="lists-and-notes"></a>清單和附注

新的清單和 notes 網域會提供 API，讓應用程式透過 Siri voice 要求處理工作和附注。

**工作**

- 具有標題和完成狀態。
- 選擇性地包含期限和位置。

**備註**

- 擁有標題和內容欄位。

工作和附注都可以組織成群組。 本節的其餘部分將說明如何使用 [TasksNotes SiriKit 範例](/samples/xamarin/ios-samples/ios11-sirikitsample)，透過 SiriKit 來執行這個新的網域。

### <a name="how-to-process-a-sirikit-request"></a>如何處理 SiriKit 要求

遵循下列步驟來處理 SiriKit 要求：

1. **解決** –驗證參數，並要求使用者 (的詳細資訊（如有需要）) 。
2. **確認** –可處理要求的最終驗證和驗證。
3. **控制碼** –執行作業 (更新資料或執行) 的網路作業。

前兩個步驟是選擇性的 (雖然建議) ，而且需要最後一個步驟。
[SiriKit 一節](~/ios/platform/sirikit/index.md)中有更詳細的指示。

### <a name="resolve-and-confirm-methods"></a>解決和確認方法

這些選用方法可讓您的程式碼執行驗證、選取預設值，或向使用者要求其他資訊。

例如，針對 `IINCreateTaskListIntent` 介面，需要的方法是 `HandleCreateTaskList` 。 有四個選用方法可讓您更充分掌控 Siri 互動：

- `ResolveTitle` –驗證標題、設定預設標題 (適當的) ，或表示不需要資料。
- `ResolveTaskTitles` –驗證使用者所說的工作清單。
- `ResolveGroupName` –驗證組名、選擇預設群組，或表示不需要資料。
- `ConfirmCreateTaskList` –驗證您的程式碼可以執行要求的作業，但不會執行它 (只有 `Handle*` 方法應該修改資料) 。

### <a name="handle-the-intent"></a>處理意圖

清單和 notes 網域中有六個意圖，三個用於工作，三個用於備註。
您必須執行以處理這些意圖的方法如下：

- 針對工作：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 針對附注：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每個方法都有傳遞給它的特定意圖型別，其中包含 Siri 從使用者的 (要求剖析的所有資訊，而且可能會在 `Resolve*` 和 `Confirm*` 方法) 中更新。
您的應用程式必須剖析所提供的資料，然後執行一些動作來儲存或處理資料，並傳回 Siri 演講並向使用者顯示的結果。

### <a name="response-codes"></a>回應碼

必要 `Handle*` 和選擇性 `Confirm*` 方法會藉由在傳遞給其完成處理常式的物件上設定值，來指出回應碼。 回應來自 `INCreateTaskListIntentResponseCode` 列舉：

- `Ready` –在確認階段 (ie 中傳回。從 `Confirm*` 方法，但不是從 `Handle*` 方法) 。
- `InProgress` –用於長時間執行的工作 (例如網路/伺服器操作) 。
- `Success` –只有從方法) ，才會回應成功作業 (的詳細資料 `Handle*` 。
- `Failure` –表示發生錯誤，無法完成作業。
- `RequiringAppLaunch` –無法由意圖處理，但在應用程式中可能會發生此操作。
- `Unspecified` –不要使用：系統將會向使用者顯示錯誤訊息。

若要深入瞭解這些方法和回應，請參閱 Apple 的 [SiriKit 清單和附注檔](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>執行清單和注意事項

[TasksNotes SiriKit 範例](/samples/xamarin/ios-samples/ios11-sirikitsample)是使用下列步驟建立，以將 SiriKit 支援新增至空白的 iOS 應用程式。

首先，若要新增 SiriKit 支援，請針對您的 iOS 應用程式遵循下列步驟：

1. **Plist**中的刻度**SiriKit** 。
2. 將 **隱私權-Siri 使用方式描述** 索引鍵新增至 **Info. plist**，以及適用于您客戶的訊息。
3. `INPreferences.RequestSiriAuthorization`在應用程式中呼叫方法，以提示使用者允許 Siri 互動。
4. 在開發人員入口網站上將 SiriKit 新增至您的應用程式識別碼，並重新建立您的布建設定檔以包含新的權利。

然後，將新的擴充功能專案新增至您的應用程式以處理 Siri 要求：

1. 以滑鼠右鍵按一下您的方案，然後選擇 [**加入新專案] >**
2. 選擇 **iOS > 擴充功能 > 意圖延伸** 模組範本。
3. 將新增兩個新的專案：意圖和 IntentUI。 自訂 UI 是選擇性的，因此範例只會在 **意圖** 專案中包含程式碼。

擴充功能專案是所有 SiriKit 要求的處理位置。 它是個別的擴充功能，並不會自動與您的主要應用程式通訊，這通常是藉由使用應用程式群組來執行共用檔案儲存體來解決。

#### <a name="configure-the-intenthandler"></a>設定 IntentHandler

`IntentHandler`類別是 Siri 要求的進入點–每個意圖都會傳遞至 `GetHandler` 方法，以傳回可處理要求的物件。

下列程式碼顯示簡單的實作為：

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

類別必須繼承自 `INExtension` ，而且因為範例將會處理清單和 notes 意圖，所以它也會執行 `IINNotebookDomainHandling` 。

> [!NOTE]
>
> - 在 .NET 中，有一個慣例是以大寫字母作為前置詞 `I` ，而 Xamarin 會在從 IOS SDK 系結通訊協定時遵守。
> - Xamarin 也會保留 iOS 的型別名稱，而且 Apple 會使用類型名稱中的前兩個字元來反映類型所屬的架構。
> - 針對此 `Intents` 架構，類型前面會加上 `IN*` (例如 `INExtension`) 但這些 _不_ 是介面。
> - 此外，它也會遵循 (的通訊協定，而這些通訊協定會成為 c # 中的介面 ) 最後會有兩個 `I` ，例如 `IINAddTasksIntentHandling` 。

#### <a name="handling-intents"></a>處理意圖

每個意圖 (新增工作、設定工作屬性等) 都會在類似如下所示的單一方法中執行。 方法應該執行三個主要功能：

1. **處理意圖** ： Siri 所剖析的資料會在意圖類型專屬的 `intent` 物件中提供。 您的應用程式可能已使用選擇性方法驗證該資料 `Resolve*` 。
2. **驗證並更新資料存放區** -使用應用程式群組將資料儲存至檔案系統 (，讓主要的 iOS 應用程式也能) 或透過網路要求來存取它。
3. **提供回應** -使用 `completion` 處理常式將回應傳回給 Siri，以讀取/顯示給使用者：

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

請注意， `null` 會以第二個參數的形式傳遞至回應，這是使用者活動參數，而未提供時，則會使用預設值。
您可以設定自訂活動類型，只要您的 iOS 應用程式透過 plist 中的金鑰來支援它即可 `NSUserActivityTypes` 。 **Info.plist** 然後，您可以在應用程式開啟時處理此案例，並執行特定的作業 (例如，開啟至相關的視圖控制器，然後從 Siri 作業) 載入資料。

此範例也會 callpriority `Success` 結果，但在真實的案例中，應新增適當的錯誤報表。

### <a name="test-phrases"></a>測試片語

下列測試片語應可在範例應用程式中運作：

- 「在 TasksNotes 中使用蘋果、香蕉和 pears 製作雜貨清單」
- "Add task WWDC in TasksNotes"
- "將工作 WWDC 新增至 TasksNotes 中的定型清單"
- 「Mark 參加 WWDC as complete in TasksNotes」
- "In TasksNotes 提醒我在我家裡買 iphone"
- 「Mark 在 TasksNotes 中以已完成的方式購買 iPhone」
- 「提醒我在上午8點的 TasksNotes 中離開回家」

![建立新的清單範例](sirikit-images/createtasklist-sml.png) ![將工作設定為完整範例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 模擬器支援使用 Siri (測試，與舊版) 不同。
>
> 如果在實際裝置上進行測試，請記得為 SiriKit 支援設定您的應用程式識別碼和布建設定檔。

<a name="alternativenames"></a>

## <a name="alternative-names"></a>替代名稱

這項新的 iOS 11 功能，表示您可以為應用程式設定替代名稱，以協助使用者以 Siri 正確地觸發。 將下列機碼新增至 iOS 應用程式專案的 **plist** 檔案：

![Plist 顯示替代的應用程式名稱金鑰和值](sirikit-images/alternative-names.png)

設定替代的應用程式名稱之後，下列片語也適用于實際命名為 **TasksNotes**) 的範例應用程式 (：

- 「在 _MonkeyNotes_中使用蘋果、香蕉和 pears 製作雜貨清單」
- "Add task WWDC in _MonkeyTodo_"

## <a name="troubleshooting"></a>疑難排解

當您執行範例或將 SiriKit 新增至自己的應用程式時，可能會遇到的一些錯誤：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_目標-C 擲回例外狀況。 名稱： NSInternalInconsistencyException 原因：從應用程式使用類別 <INPreferences： 0x60400082ff00> 需要許可權 .com. siri。您是否已在 Xcode 專案中啟用 Siri 功能？_

- SiriKit 是核取的 **plist**。
- **Plist** 是在 [專案選項] 中設定， **> 建立 > IOS**套件組合簽署]。

  [![顯示正確設定權利的專案選項](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- 裝置部署的 () 應用程式識別碼已啟用 SiriKit 且已下載布建設定檔。

## <a name="related-links"></a>相關連結

- [SiriKit (Apple) ](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 範例](/samples/xamarin/ios-samples/ios11-sirikitsample)
- [SiriKit (WWDC 的新功能)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/214/)