---
title: IOS 11 中 SiriKit 更新
description: 本文件說明如何使用 SiriKit iOS 11 中。 特別是，它會檢查如何使用工作和資訊以及如何提供應用程式的替代名稱。
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/07/2017
ms.openlocfilehash: 7e895dc2865880ec2789a40f8cdf047a20f8693b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400276"
---
# <a name="sirikit-updates-in-ios-11"></a>IOS 11 中 SiriKit 更新

SiriKit 引進在 iOS 10 （包括健身，預約，和進行呼叫） 的服務網域的數目。 請參閱[SiriKit 區段](~/ios/platform/sirikit/index.md)SiriKit 概念以及如何在您的應用程式中實作 SiriKit。

![Siri 工作清單示範](sirikit-images/sirikit.png)

SiriKit iOS 11 中的新增這些新的和更新的意圖網域：

- [**列出並備忘稿**](#listsnotes) – 新 ！ 提供應用程式的 API，以處理工作和資訊。
- **視覺化程式碼**– 新 ！ Siri 可以顯示 QR 代碼，來共用連絡人資訊，或參與付款交易。
- **付款**– 新增付款互動的搜尋服務 」 及 「 傳輸 」 用途。
- **度過預約**– 新增取消等及意見反應 」 用途。

其他新功能包括：

- [**替代的應用程式名稱**](#alternativenames) – 提供別名，以協助客戶告訴 Siri 鎖定您的應用程式，方法是提供其他名稱/發音。
- **啟動健身**– 讓您能夠在背景中啟動了。

以下說明其中部分功能。 如需其他詳細資料，請參閱[Apple SiriKit 文件](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes" />

## <a name="lists-and-notes"></a>清單和附註

新的清單和附註網域提供應用程式的 API，以處理工作，並透過使用 Siri 語音要求的資訊。

**工作**

- 具有標題和完成狀態。
- 選擇性地包含期限和位置。

**備註**

- 具有標題和內容的欄位。

工作和資訊可以組織成群組。 本節的其餘部分將說明如何實作 SiriKit，使用這個新的網域使用[TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)。

### <a name="how-to-process-a-sirikit-request"></a>如何處理 SiriKit 要求

依照下列步驟處理 SiriKit 要求：

1. **解決**– 驗證參數，並進一步資訊向使用者要求 （如有必要）。
2. **確認**– 最終驗證和處理要求的驗證。
3. **處理**– 執行作業 （更新資料或執行網路作業）。

前兩個步驟是選擇性 （雖然建議），且最後一個步驟是必要。
有更詳細的指示，在[SiriKit 區段](~/ios/platform/sirikit/index.md)。

### <a name="resolve-and-confirm-methods"></a>解決，然後確認方法

這些選擇性的方法可讓您從使用者執行驗證、 選取預設值或要求其他資訊的程式碼。

例如，針對`IINCreateTaskListIntent`介面後，所需的方法是`HandleCreateTaskList`。 有四個選擇性方法，可提供更充分掌控 Siri 互動：

- `ResolveTitle` – 驗證標題、 設定預設標題 （如果適用），或表示不需要的資料。
- `ResolveTaskTitles` – 驗證使用者所講出的工作的清單。
- `ResolveGroupName` – 驗證群組名稱、 選擇預設群組，或表示不需要的資料。
- `ConfirmCreateTaskList` -驗證您的程式碼可以執行要求的作業，但不會執行 (只有`Handle*`方法應該修改的資料)。

### <a name="handle-the-intent"></a>控制代碼的意圖

有六個的意圖，清單和附註的網域中，三個用於工作和三個用於備忘稿。
您必須實作以處理這些意圖的方法包括：

- 工作：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 如需資訊：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每個方法都有特定意圖類型傳遞給它，其中包含 Siri 已經剖析來自使用者的要求的所有資訊 (而且可能在更新`Resolve*`和`Confirm*`方法)。
您的應用程式必須剖析提供的資料執行一些動作來儲存或其他方式處理資料，然後傳回結果，Siri 發表演說，並向使用者顯示。

### <a name="response-codes"></a>回應碼

所需`Handle*`和選擇性`Confirm*`方法所傳遞的物件上設定值，其完成處理常式來表示回應碼。 回應來自`INCreateTaskListIntentResponseCode`列舉型別：

- `Ready` – 在確認階段傳回 (亦即。 從`Confirm*`方法，而不是從`Handle*`方法)。
- `InProgress` – 用於長時間執行工作 （例如，網路/伺服器作業）。
- `Success` – 成功的作業的詳細資料回應 (只能從`Handle*`方法)。
- `Failure` – 表示發生錯誤，而且無法完成作業。
- `RequiringAppLaunch` -無法處理的意圖，但作業可能在應用程式。
- `Unspecified` – 請勿使用： 將會向使用者顯示錯誤訊息。

深入了解這些方法與在 Apple 的回應[SiriKit 列出並 notes 文件](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>實作清單和附註

[TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)SiriKit 支援新增至空白 iOS 應用程式中使用下列步驟所建立。

首先，新增 SiriKit 支援，請遵循下列步驟將 iOS 應用程式：

1. 刻度**SiriKit**中**Entitlements.plist**。
2. 新增**隱私權-Siri 使用方式描述**機碼**Info.plist**，以及為您的客戶的訊息。
3. 呼叫`INPreferences.RequestSiriAuthorization`，提示使用者允許 Siri 互動的應用程式的方法。
4. 將 SiriKit 新增至您的應用程式識別碼，開發人員入口網站中，並重新建立您的佈建設定檔以包含新的權利。

然後加入您的應用程式處理 Siri 要求新的擴充功能專案：

1. 以滑鼠右鍵按一下您的方案，然後選擇 **新增 > 新增專案...**.
2. 選擇**iOS > 擴充功能 > Intents 延伸模組**範本。
3. 將新增兩個新的專案：意圖和 IntentUI。 自訂 UI 是選擇性的所以此範例只包含程式碼中的**意圖**專案。

擴充功能專案是所有 SiriKit 要求將會都處理。 做為個別的擴充功能，它會自動沒有與主要的應用程式 – 這通常會解決藉由實作使用應用程式群組共用的檔案儲存體通訊的任何方法。

#### <a name="configure-the-intenthandler"></a>設定 IntentHandler

`IntentHandler`類別是進入點，用來要求使用 Siri – 每個意圖會傳遞至`GetHandler`方法，以傳回物件，來處理要求。

下列程式碼顯示一個簡單的實作：

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

類別必須繼承自`INExtension`，因為此範例會處理清單，而且資訊意圖，它也會實作`IINNotebookDomainHandling`。

> [!NOTE]
> - 適用於.NET 介面来加上大寫中沒有慣例`I`，Xamarin iOS SDK 中的繫結通訊協定時遵守的。
> - Xamarin 也會保留型別名稱，從 iOS 和 Apple 型別名稱中使用的前兩個字元，以反映型別所屬的架構。
> - 針對`Intents`架構中，型別前面會加上`IN*`（例如。 `INExtension`)，但這些_不_介面。
> - 它也會遵循該通訊協定 (這成為介面，在C#) 會得到兩個`I`s，這類`IINAddTasksIntentHandling`。

#### <a name="handling-intents"></a>處理意圖

每個意圖 （加入工作、 設定工作屬性等） 被實作在單一方法類似於如下所示。 此方法應該執行三項主要功能：

1. **處理意圖**– 由 Siri 剖析的資料可在`intent`物件特有的目的型別。 您的應用程式驗證使用選擇性資料`Resolve*`方法。
2. **確認並更新資料存放區**– 儲存到檔案系統 （以便主要 iOS 應用程式也可以存取它，請使用應用程式群組），或透過網路要求的資料。
3. **提供回應**– 使用`completion`傳送回應給 Siri 讀取/顯示給使用者的處理常式：

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

請注意，`null`傳遞做為第二個參數來回應-這是使用者活動參數，而且未提供，將會使用預設值。
您可以設定自訂活動型別，只要您的 iOS 應用程式支援透過`NSUserActivityTypes`中的索引鍵**Info.plist**。 然後，您可以處理這種情況，您的應用程式開啟時，並執行特定作業 （例如開啟相關的檢視控制器和從 Siri 作業載入資料）。

此範例也硬式編碼`Success`結果，但在真實案例中，適當的錯誤報告應加入。

### <a name="test-phrases"></a>測試片語

範例應用程式用於下列測試片語：

- 「 在 TasksNotes 讓購物清單 apples、 bananas，與梨子 」
- 「 加入工作 WWDC TasksNotes 中 」
- 「 工作 WWDC 訓練清單中新增 TasksNotes"
- "標示為已完成在 TasksNotes 參加 WWDC"
- "中 TasksNotes 提醒我一次購買 iphone，當我回家"
- 「 標記為已完成在 TasksNotes 購買 iPhone 」
- "提醒我首頁保留 TasksNotes 在上午 8 點 」

![建立新的清單範例](sirikit-images/createtasklist-sml.png) ![設定工作的完整範例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 模擬器支援使用 Siri （不同於先前的版本） 進行測試。
>
> 如果在實際裝置上測試，別忘了，若要設定您的應用程式識別碼和佈建設定檔 SiriKit 支援。

<a name="alternativenames" />

## <a name="alternative-names"></a>替代名稱

這個新的 iOS 11 功能表示您可以設定您的應用程式，以協助使用者使用 Siri 正確觸發它的別名。 將下列索引鍵，加入**Info.plist**的 iOS 應用程式專案的檔案：

![顯示替代的應用程式名稱索引鍵和值的 Info.plist](sirikit-images/alternative-names.png)

替代的應用程式名稱設定之後，下列片語也適用於範例應用程式 (這實際上名為**TasksNotes**):

- 「 讓購物清單 apples、 bananas，與在梨子_MonkeyNotes_"
- 「 加入工作中的 WWDC _MonkeyTodo_"


## <a name="troubleshooting"></a>疑難排解

執行範例，或將 SiriKit 新增至您的應用程式時可能會遇到一些錯誤：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_OBJECTIVE-C 擲回例外狀況。名稱：NSInternalInconsistencyException 原因：這個類別的用法 < INPreferences:0x60400082ff00 > 從應用程式需要權利 com.apple.developer.siri。您並未啟用 Siri 功能在您的 Xcode 專案嗎？_

- SiriKit 已在核**Entitlements.plist**。
- **Entitlements.plist**中設定**專案選項 > 建置 > iOS 套件組合簽署**。

  [![顯示已正確設定權利的專案選項](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- （適用於裝置的部署）應用程式識別碼已啟用的 SiriKit 和佈建設定檔下載。


## <a name="related-links"></a>相關連結

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 範例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [什麼是新增在 SiriKit (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/214/)
