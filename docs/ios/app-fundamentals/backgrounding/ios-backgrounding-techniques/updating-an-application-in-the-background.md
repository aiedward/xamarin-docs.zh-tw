---
title: 更新背景中的 Xamarin.iOS 應用程式
description: 本文件說明更新是在背景中，例如區域監視、 背景擷取，以及遠端通知的 Xamarin.iOS 應用程式的各種方式。
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 835dccaea79467582f56fd4b8b6b3b8f42acd632
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392328"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>更新背景中的 Xamarin.iOS 應用程式

背景重新整理是喚醒已暫停的應用程式或未執行，並使用新的內容加以更新的程序。 iOS 提供三個選項可重新整理在背景中的內容：

1.  *區域監視*並*顯著的位置變更服務*-定位感知 Api 觸發程序的背景更新根據的使用者的位置中的變更。 這些 Api 可慎重地重新整理在非位置為基礎的 iOS 6 應用程式的內容，就無法使用其他選項。
1.  *背景擷取 (iOS 7 +)* -以時態性的方法，來重新整理*非關鍵*更新的內容*經常*。
1.  *遠端通知 (iOS 7 +)* -接收推播通知的應用程式可以使用通知來觸發背景內容重新整理。 這個方法可用來更新*重要、 且時間緊迫*更新的內容*偶發性*。


下列各節會討論這些選項的基本概念。

## <a name="region-monitoring-and-significant-location-changes"></a>區域監視和重要位置的變更

iOS 會提供與背景功能的兩個位置感知 Api:

1.  *區域監視*是設定區域的界限，以及使用者進入或離開區域時，喚醒裝置的程序。 區域是循環性，而且可以屬於不同的大小。 當使用者跨越區域界限時，裝置會喚醒來處理事件，通常是發出的通知，或啟動工作。 區域監視需要 GPS，並增加使用電池及資料使用量。
1.  *顯著的位置變更服務*是更簡單、 節省電源選項適用於行動電話無線電裝置。 當裝置切換資料格 towers，系統會通知應用程式接聽顯著的位置變更。 此服務可以用來喚醒暫止或終止的應用程式，並讓您有機會檢查有新的內容，在背景中。 背景活動僅限於約 10 秒，除非搭配[背景工作](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)。


應用程式不需要位置`UIBackgroundMode`使用這些位置感知的 Api。 IOS，不會追蹤裝置技術在使用者的位置變更時，可以執行的工作類型，因為這些 Api 提供解決方法是更新 iOS 6 在背景中的內容。 *請記住，觸發背景更新，以位置為基礎的 Api 將會在裝置的資源，來繪製，而不了解應用程式為什麼需要其位置的存取權的使用者可能會混淆*。 背景處理還未使用的位置 Api 的應用程式中實作監視區域或位置的重大變更時，請請慎重考慮。

使用位置監視背景處理應用程式公開 （expose) iOS 6 中的瑕疵： 如果應用程式的需求不放入背景必要類別，它具有有限的背景選項。 兩個新的 Api，隨著*背景提取*並*遠端通知*，iOS 7 （和更新版本） 提供更多應用程式的背景的機會。 下面兩節會介紹這些新的 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>背景擷取 (iOS 7 及更新版本)

在 iOS 6 中，輸入前景應用程式所需的時間載入新的內容，簡要向使用者顯示他們已經看過的內容。 背景擷取可讓應用程式，以將新的資料載入*之前*使用者啟動應用程式，並提供最新的內容中的使用者。

若要實作背景擷取，請編輯*Info.plist* ，並檢查**啟用背景模式**並**背景擷取**核取方塊：

 [![](updating-an-application-in-the-background-images/fetch.png "編輯 Info.plist，並檢查 [啟用背景模式] 和背景擷取的核取方塊")](updating-an-application-in-the-background-images/fetch.png#lightbox)

接下來，在`AppDelegate`，覆寫`FinishedLaunching`方法來設定最小的擷取間隔。 在此範例中，我們會由 OS 決定如何通常提取新的內容：

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

當我們完成更新內容時，我們會讓作業系統知道藉由呼叫適當的狀態完成處理常式。 iOS 提供三個選項可完成處理常式狀態：

1.  `UIBackgroundFetchResult.NewData` -當已經提取新的內容，且已更新應用程式時呼叫。
1.  `UIBackgroundFetchResult.NoData` -當有新內容擷取已執行，但沒有內容可供使用時呼叫。
1.  `UIBackgroundFetchResult.Failed` -適用於錯誤處理，這稱為時擷取無法瀏覽。


使用背景擷取應用程式可以呼叫來從背景更新 UI。 當使用者開啟應用程式時，此 UI 會是最新，並且顯示新的內容。 這也會更新應用程式的應用程式切換器快照集，因此使用者可以看到應用程式時有新的內容。

> [!IMPORTANT]
> 一次`PerformFetch`是呼叫，應用程式有大約 30 秒來開始下載新的內容，並呼叫完成處理常式區塊。 如果費時太長，將會終止應用程式。 請考慮使用背景擷取_背景傳送服務_下載媒體或其他大型檔案時。


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上述的範例程式碼，我們會由 OS 決定要擷取新的內容設為下限的擷取間隔多久`BackgroundFetchIntervalMinimum`。 iOS 的擷取間隔提供三個選項：

1.  `BackgroundFetchIntervalNever` -告訴系統永遠不會擷取新的內容。 若要關閉擷取在某些情況下，例如當使用者未登入時使用此項。 這是擷取間隔的預設值。 
1.  `BackgroundFetchIntervalMinimum` -可讓系統決定通常是用來擷取如何根據使用者模式、 電池壽命，資料使用量以及各種其他應用程式的需求。
1.  `BackgroundFetchIntervalCustom` -如果您知道應用程式的內容取得更新的頻率，您可以指定 「 睡眠 」 間隔之後每一項擷取，此時應用程式將無法擷取新的內容。 註冊這段時間後，系統將會決定何時要擷取內容。


兩者`BackgroundFetchIntervalMinimum`和`BackgroundFetchIntervalCustom`依賴排程擷取系統。 這個間隔是動態的適應裝置的需求，以及個別使用者的習慣。 例如，如果一位使用者會檢查應用程式每天早上，而另一個檢查每隔一小時，iOS 會確保內容是最新的兩個使用者每次開啟應用程式。

背景擷取應該用於使用非重大內容經常更新的應用程式。 對於具備重大更新的應用程式，應該使用遠端通知。 遠端通知根據背景擷取，並共用相同的完成處理常式。 我們接下來將探討遠端通知。

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>遠端通知 (iOS 7 及更新版本)

推播通知是透過裝置提供者傳送的 JSON 訊息*Apple Push Notification service (APNs)*。

在 iOS 6 中，內送的推播通知會告知來提醒使用者感興趣的項目發生應用程式中的系統。 按一下通知提取應用程式移出暫止或終止狀態，而且應用程式就會開始更新內容。 iOS 7 （和更新版本） 擴充一般的推播通知讓應用程式來更新在背景中的內容有機會*之前*通知使用者，以便使用者可以開啟應用程式，並向新的內容立即。

若要實作遠端通知，請編輯*Info.plist*並查看**啟用背景模式**並**遠端通知**核取方塊：

 [![](updating-an-application-in-the-background-images/remote.png "背景模式設定為 啟用背景模式及遠端通知")](updating-an-application-in-the-background-images/remote.png#lightbox)

接下來，設定`content-available`推播通知到 1 的旗標。 這可讓應用程式知道要顯示警示之前擷取新的內容：

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在  *AppDelegate*，覆寫`DidReceiveRemoteNotification`方法來檢查可用的內容的通知承載，並呼叫適當的完成處理常式區塊：

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

遠端通知應該用於不頻繁的更新，而是應用程式的功能很重要的內容。 如需有關遠端通知的詳細資訊，請參閱 Xamarin[在 iOS 中的推播通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)指南。

> [!IMPORTANT]
> 在 遠端通知中的更新機制根據背景擷取，因為應用程式必須開始下載新的內容，並接收通知，30 秒內呼叫完成處理常式區塊或 iOS 會終止應用程式。 請考慮配對遠端通知_背景傳送服務_下載媒體或其他大型檔案，在背景時。


### <a name="silent-remote-notifications"></a>無訊息的遠端通知

遠端通知是簡單的方法來通知應用程式的更新，並開始擷取新的內容，但我們不需要通知使用者的項目已變更的情況下。 比方說，如果使用者加上旗標的同步處理的檔案，我們不需要每次檔案更新通知他們。 檔案同步處理不是令人驚訝的事件，也不會要求使用者的立即處理。 使用者只應該開啟它時是最新的檔案。

如同上面的情況下，iOS 會允許推播通知傳送以無訊息模式-也就是沒有警示。 若要開啟無訊息的其中一個一般通知，只要移除通知承載中的警示：

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

開發人員觀點的正常和無訊息通知的最大差異是無訊息推播會受到速率限制。 如果推播速率太高，APNs 會延遲傳遞的無訊息推播至裝置。 這是為了確保應用程式不會耗盡使用太多的無訊息通知的裝置資源。

不過，APNs 會讓 「 借助 」 以及一般的遠端通知或保持回應的無訊息通知。 規則的通知不受速率限制，因為它們可用來向 APNs 推播註冊預存的無訊息通知，到裝置，如下列圖所示：

 [![](updating-an-application-in-the-background-images/silent.png "規則的通知可用來向 APNs 的預存的無訊息通知推送到裝置，如本圖所示")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple 建議傳送無訊息推播通知，每當應用程式需要，開發人員，並讓 APNs 排程其傳遞。


在本節中，我們所討論的各種選項重新整理內容，在背景中的執行為背景必要類別目錄的工作，不符合。 現在，我們來看看一些作用中的這些 Api。

 [下一步：第 4 部-iOS 背景逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
