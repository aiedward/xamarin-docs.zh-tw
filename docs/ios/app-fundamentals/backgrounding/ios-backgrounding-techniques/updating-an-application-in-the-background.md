---
title: "在背景中的將應用程式更新"
ms.topic: article
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d878f922b74ea3e95fd0e1ebce9e7445063a2946
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="updating-an-application-in-the-background"></a>在背景中的將應用程式更新

背景重新整理是喚醒裝置的應用程式已暫止或未執行，並更新為新的內容的程序。 iOS 提供重新整理內容，在背景中的三個的選項：

1.  *區域監視*和*顯著的位置變更服務*-定位感知應用程式開發介面的觸發程序背景更新根據的變更使用者的位置。 這些 Api 可用斟酌來重新整理在非位置為基礎的 iOS 6 應用程式的內容，就無法使用其他選項。
1.  *背景擷取 (iOS 7 +)* -時態方法重新整理*非關鍵*更新的內容*經常*。
1.  *遠端通知 (iOS 7 +)* -接收推播通知的應用程式可以使用通知來觸發背景內容重新整理。 這個方法可用來更新*重要、 且時間緊迫*更新的內容*偶發性*。


下列章節會涵蓋這些選項的基本概念。

## <a name="region-monitoring-and-significant-location-changes"></a>區域監視和變更的顯著的位置

iOS 提供兩個位置感知應用程式開發介面與 backgrounding 功能：

1.  *區域監視*是設定的區域與界限產生關聯，以及使用者進入或離開區域時，喚醒裝置的程序。 區域會循環，而且可以屬於不同的大小。 當使用者跨越區域界限時，裝置會喚醒處理事件，通常是發出的通知，或無法關閉的工作。 區域監視需要 GPS，並使用電池及資料使用量會增加。
1.  *顯著的位置變更服務*是更簡單、 電源節省選項適用於使用行動電話通訊無線電裝置。 當裝置切換資料格塔，系統會通知應用程式正在接聽的有效位置變更。 此服務可用來喚醒暫止或終止應用程式，並讓您有機會檢查有新的內容，在背景中。 背景活動僅限於約 10 秒，除非搭配[背景工作](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)。


應用程式不需要位置`UIBackgroundMode`使用這些位置感知應用程式開發介面。 IOS 不會追蹤裝置決定使用者的位置中的變更，才能執行的工作類型，因為這些 Api 提供解決方法是更新 iOS 6 上，在背景中的內容。 *請注意，觸發以位置為基礎應用程式開發介面的背景更新時，會繪製在裝置資源，而且可能會混淆使用者不了解為什麼應用程式需要存取其位置*。 實作背景處理應用程式不是正在使用應用程式開發介面的位置中的監視區域或位置的重大變更時，請使用自行決定。

使用位置監視用於背景處理應用程式公開 iOS 6 中的瑕疵： 如果應用程式的需求不放入背景需要類別，它具有有限的 backgrounding 選項。 兩個新的 api，介紹*背景擷取*和*遠端通知*，iOS 7 （和大於） 提供更多應用程式的 backgrounding 機會。 接下來兩節將介紹這些新的 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>背景擷取 (iOS 7 和更新版本)

在 iOS 6 中，輸入前景應用程式所需時間來載入新的內容，簡短地呈現它們已經看過的內容給使用者。 背景擷取可讓應用程式載入新資料*之前*使用者啟動應用程式，並提供使用者最新的內容。

若要實作背景擷取，請編輯*Info.plist*並檢查**啟用背景模式**和**背景擷取**核取方塊：

 [ ![](updating-an-application-in-the-background-images/fetch.png "編輯 Info.plist，並檢查啟用背景模式，以及背景提取的核取方塊")](updating-an-application-in-the-background-images/fetch.png)

接下來，在`AppDelegate`，覆寫`FinishedLaunching`方法來設定最小的擷取間隔。 在此範例中，我們會讓作業系統決定如何通常提取新的內容：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

最後，藉由覆寫執行 fetch`PerformFetch`方法中的`AppDelegate`，並傳入*完成處理常式*。 完成處理常式是委派，會取得`UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

當我們完成更新內容時，我們會讓作業系統知道，方法是呼叫的適當狀態的完成處理常式。 iOS 提供三個選項可以完成處理常式狀態：

1.  `UIBackgroundFetchResult.NewData` -已經提取新的內容，並在更新應用程式時呼叫。
1.  `UIBackgroundFetchResult.NoData` -新的內容擷取瀏覽，但是沒有內容可供使用時呼叫。
1.  `UIBackgroundFetchResult.Failed` -適用於錯誤處理，這就稱為提取無法瀏覽時。


使用背景所擷取的應用程式可以進行呼叫，來從背景更新 UI。 當使用者開啟應用程式時，UI 將最新，並且顯示新的內容。 這也會更新應用程式的應用程式切換器快照集，因此使用者可以看到應用程式時有新的內容。

> [!IMPORTANT]
> **請注意**： 一次`PerformFetch`是呼叫，應用程式有大約 30 秒來開始下載新的內容，並呼叫完成處理常式區塊。 如果花費的時間太長，應用程式將會終止。 請考慮使用背景擷取_背景傳送服務_下載媒體或其他大型檔案時。


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上述範例程式碼，我們會讓決定如何通常提取新的內容設定為 最小的擷取間隔的 OS `BackgroundFetchIntervalMinimum`。 iOS 的擷取間隔提供三個選項：

1.  `BackgroundFetchIntervalNever` -告訴系統永遠不會擷取新的內容。 若要關閉提取在某些情況下，例如當使用者未登入使用此項。 這是預設值的擷取間隔。 
1.  `BackgroundFetchIntervalMinimum` -可讓系統決定通常是用來擷取如何根據使用者模式、 電池壽命，資料使用量及其他應用程式的需求。
1.  `BackgroundFetchIntervalCustom` -如果您知道應用程式的內容取得更新的頻率，您可以指定 「 睡眠 」 間隔之後每回擷取時，在應用程式將無法擷取新的內容。 組成該間隔後，系統將會決定何時要擷取內容。


同時`BackgroundFetchIntervalMinimum`和`BackgroundFetchIntervalCustom`依賴系統以排程提取。 這個間隔是動態的調整到裝置的需求，以及個別使用者的習慣。 比方說，如果一位使用者會檢查應用程式每天早上，和另一個檢查每個小時，iOS 會確認內容是最新的兩個使用者每次開啟應用程式。

背景擷取應該用於使用非重大內容經常更新的應用程式。 對於具有重大更新的應用程式，應該使用遠端的通知。 遠端通知根據背景擷取，並共用相同的完成處理常式。 我們接下來將探討遠端通知。

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>遠端通知 (iOS 7 和更新版本)

推播通知是藉由裝置所傳送的提供者的 JSON 訊息*Apple Push Notification service (APNs)*。

在 iOS 6、 傳入的推播通知會告訴系統警示有趣的東西發生應用程式中的使用者。 按一下通知提取應用程式移出暫止或終止的狀態，並會從應用程式，更新內容。 iOS 7 （和大於） 延伸一般推播通知，讓應用程式有機會更新內容，在背景中的*之前*通知使用者，以便使用者可以開啟應用程式，並會出現新的內容立即。

若要實作遠端通知，請編輯*Info.plist*並檢查**啟用背景模式**和**遠端通知**核取方塊：

 [ ![](updating-an-application-in-the-background-images/remote.png "背景模式設定為啟用背景模式與遠端的通知")](updating-an-application-in-the-background-images/remote.png)

接下來，設定`content-available`推播通知給 1 上的旗標。 這可讓應用程式知道顯示警示之前擷取新的內容：

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在*AppDelegate*，覆寫`DidReceiveRemoteNotification`方法來檢查通知裝載可用內容，並呼叫適當的完成處理常式區塊：

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

遠端的通知應用於內容的關鍵應用程式的功能不頻繁的更新。 如需有關遠端通知的詳細資訊，請參閱 Xamarin[推播通知，在 iOS 中](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)指南。

> [!IMPORTANT]
> **請注意**： 因為遠端通知中的更新機制根據背景擷取、 應用程式必須開始下載新的內容，並呼叫接收通知的 30 秒內完成處理常式區塊或將 iOS結束應用程式。 請考慮配對與遠端通知_背景傳送服務_下載媒體或其他大型檔案，在背景中的時。


### <a name="silent-remote-notifications"></a>無訊息的遠端通知

遠端通知是簡單的方式通知應用程式的更新和擷取新的內容，開始進行，但我們不需要通知使用者內容已變更的情況下。 例如，如果使用者加上旗標的檔案同步，我們不需要通知他們每次更新檔案。 檔案同步處理不是令人意外的事件，也不會要求使用者立即處理。 使用者只應該開啟它時，是最新的檔案。

如同上面的情況下，iOS 會允許推播通知傳送以無訊息模式-也就是沒有警示。 若要開啟成無訊息通知規則，只需從通知裝載移除警示：

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

開發人員觀點正常和無訊息的通知之間最大的差異是無訊息的推播通知是有限的速率。 APNs 會延遲無訊息的推播通知的傳遞至裝置，如果推送速度變得太高。 這是為了確保應用程式不會清空以無訊息的通知太多裝置資源。

不過，APNs 會讓無訊息 」 藉由 「 連同一般遠端通知或保持回應的通知。 因為規則的通知沒有速率限制，所以可以使用來自 APNs 的總預存的無訊息通知推送至裝置，如下列圖表所示：

 [ ![](updating-an-application-in-the-background-images/silent.png "規則的通知可用來從 APNs 的預存的無訊息通知推播至裝置，如這個圖表所示")](updating-an-application-in-the-background-images/silent.png)

> [!IMPORTANT]
> **請注意**: Apple 鼓勵開發人員需要應用程式，並讓 APNs 排程傳送時，傳送無訊息的推播通知。


在本節中，我們所討論的各種選項重新整理內容，在背景中的執行為背景需要分類不符合的工作。 現在，我們來看看一些動作中的這些 Api。

 [下一步: 一部分 4-iOS Backgrounding 逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
