---
title: 在背景中更新 Xamarin iOS 應用程式
description: 本檔說明在背景中更新 Xamarin iOS 應用程式的各種方式, 例如區域監視、背景提取和遠端通知。
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 68200d5cde78628e23e6bdb5c4adf7a16b646bf0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521306"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>在背景中更新 Xamarin iOS 應用程式

背景重新整理是喚醒已暫停或未執行之應用程式, 並以新內容更新它的進程。 iOS 提供三個選項來重新整理背景中的內容:

1. *區域監視*和*重要位置變更服務*-位置感知 api 會根據使用者位置的變更來觸發背景更新。 您可以謹慎使用這些 Api 來重新整理非位置型 iOS 6 應用程式中的內容, 而無法使用其他選項。
1. *背景提取 (iOS 7 +)* -重新整理*經常*更新之*非關鍵性*內容的時態性方法。
1. *遠端通知 (iOS 7 +)* -接收推播通知的應用程式可以使用通知來觸發背景內容重新整理。 這個方法可以用來更新具有*重要、時間*緊迫的內容 (*偶爾*會更新)。


下列各節涵蓋這些選項的基本概念。

## <a name="region-monitoring-and-significant-location-changes"></a>區域監視和重要位置變更

iOS 提供兩個具有背景處理功能的位置感知 Api:

1. *區域監視*是設定具有界限的區域, 以及在使用者進入或離開區域時喚醒裝置的程式。 區域是迴圈的, 而且可以有不同的大小。 當使用者跨越區域界限時, 裝置將會喚醒以處理事件, 通常是藉由引發通知或開始工作。 區域監視需要 GPS, 並增加電池和資料使用量。
1. *重要的位置變更服務*是一種更簡單、省電的選項, 可用於具有行動電話通訊無線電的裝置。 當裝置切換資料格塔時, 會通知接聽大量位置變更的應用程式。 這項服務可用來喚醒已暫停或已終止的應用程式, 並提供在背景檢查新內容的機會。 除非與[背景](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)工作配對, 否則背景活動限制為大約10秒。


應用程式不需要使用這些位置`UIBackgroundMode`感知 api 的位置。 由於 iOS 不會追蹤使用者位置中的變更喚醒裝置時可執行檔工作類型, 因此, 這些 Api 會提供在 iOS 6 背景中更新內容的解決方法。 請*記住, 使用以位置為基礎的 api 觸發背景更新將會在裝置資源上繪製, 而且可能會混淆不了解為什麼應用程式需要存取其位置的使用者*。 在尚未使用位置 Api 的應用程式中, 針對背景處理執列區域監視或重大位置變更時, 請謹慎使用。

使用位置監視進行背景處理的應用程式會在 iOS 6 中公開一個缺陷: 如果應用程式的需求不符合背景所需的類別, 則會有有限的背景處理選項。 透過引進兩個新的 Api、*背景提取*和*遠端通知*, iOS 7 (及更新版本) 可為更多應用程式提供背景處理的機會。 接下來的兩節將介紹這些新的 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>背景提取 (iOS 7 及更新版本)

在 iOS 6 中, 應用程式會進入載入新內容所需的前景時間, 簡要呈現使用者已看過的內容。 背景提取可讓應用程式在使用者啟動應用程式*之前*載入新的資料, 並為使用者提供最新的內容。

若要執行背景提取, 請編輯*plist* , 然後勾選 [**啟用背景模式**] 和 [**背景提取**] 核取方塊:

 [![](updating-an-application-in-the-background-images/fetch.png "編輯 Info.plist，並檢查 [啟用背景模式] 和背景擷取的核取方塊")](updating-an-application-in-the-background-images/fetch.png#lightbox)

接下來, 在`AppDelegate`中, 覆`FinishedLaunching`寫方法以設定最小提取間隔。 在此範例中, 我們會讓 OS 決定提取新內容的頻率:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

最後, 在中覆寫`PerformFetch`方法`AppDelegate`, 並傳入*完成處理常式*, 以執行提取。 完成處理常式是一個委派, 它會`UIBackgroundFetchResult`採用:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

當我們完成內容更新時, 我們會以適當的狀態呼叫完成處理常式, 讓 OS 知道。 iOS 提供三個完成處理常式狀態的選項:

1. `UIBackgroundFetchResult.NewData`-已提取新內容, 且已更新應用程式時呼叫。
1. `UIBackgroundFetchResult.NoData`-在提取新內容時呼叫, 但沒有可用的內容。
1. `UIBackgroundFetchResult.Failed`-適用于錯誤處理, 當提取無法通過時, 就會呼叫此方法。


使用背景提取的應用程式可以進行呼叫, 以從背景更新 UI。 當使用者開啟應用程式時, UI 將會是最新狀態並顯示新內容。 這也會更新應用程式的應用程式切換器快照, 讓使用者可以看到應用程式有新內容的時間。

> [!IMPORTANT]
> 呼叫`PerformFetch`之後, 應用程式大約需要30秒的時間來開始下載新的內容, 並呼叫完成處理常式區塊。 如果這段時間過長, 應用程式將會終止。 下載媒體或其他大型檔案時, 請考慮使用背景提取與_背景傳送服務_。


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上述範例程式碼中, 我們會將最小的提取間隔設定為`BackgroundFetchIntervalMinimum`, 讓 OS 決定提取新內容的頻率。 iOS 針對提取間隔提供三個選項:

1. `BackgroundFetchIntervalNever`-告訴系統永遠不會提取新的內容。 在某些情況下使用此項來關閉提取, 例如當使用者未登入時。 這是提取間隔的預設值。 
1. `BackgroundFetchIntervalMinimum`-讓系統根據使用者模式、電池壽命、資料使用方式, 以及其他應用程式的需求, 決定要提取的頻率。
1. `BackgroundFetchIntervalCustom`-如果您知道應用程式內容的更新頻率, 您可以在每次提取之後指定「睡眠」間隔, 在這段期間, 應用程式將無法取得新的內容。 一旦該間隔完成, 系統就會決定何時提取內容。


`BackgroundFetchIntervalMinimum` 和`BackgroundFetchIntervalCustom`都依賴系統來排程提取。 此間隔是動態的, 可適應裝置的需求以及個別使用者的習慣。 例如, 如果某個使用者每天早上檢查應用程式, 而另一個檢查每小時, 則 iOS 會確保每個使用者在每次開啟應用程式時都是最新的內容。

背景提取應用於經常更新為非重大內容的應用程式。 對於具有重大更新的應用程式, 則應使用遠端通知。 遠端通知是以背景提取為基礎, 並共用相同的完成處理常式。 接下來我們將深入探討遠端通知。

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>遠端通知 (iOS 7 及更新版本)

推播通知是透過*Apple Push Notification service (APNs)* 從提供者傳送到裝置的 JSON 訊息。

在 iOS 6 中, 連入推播通知會告訴系統警示使用者, 指出應用程式中發生了有趣的問題。 按一下通知會將應用程式從 [已暫停] 或 [已終止] 狀態提取出來, 應用程式就會開始更新內容。 iOS 7 (及更新版本) 延伸一般推播通知的方式, 是讓應用程式有機會在背景中更新內容,*然後才*通知使用者, 讓使用者可以開啟應用程式, 並立即顯示新內容。

若要執行遠端通知, 請編輯*plist* , 然後勾選 [**啟用背景模式**和**遠端通知**] 核取方塊:

 [![](updating-an-application-in-the-background-images/remote.png "背景模式設定為啟用背景模式和遠端通知")](updating-an-application-in-the-background-images/remote.png#lightbox)

接下來, 將`content-available`推播通知本身的旗標設定為1。 這可讓應用程式知道在顯示警示之前, 先提取新的內容:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在*AppDelegate*中, 覆寫`DidReceiveRemoteNotification`方法以檢查可用內容的通知承載, 並呼叫適當的完成處理常式區塊:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

遠端通知應用於不常更新, 並包含對應用程式功能非常重要的內容。 如需遠端通知的詳細資訊, 請參閱《 [iOS 中](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)的 Xamarin 推播通知》指南。

> [!IMPORTANT]
> 由於遠端通知中的更新機制是以背景提取為基礎, 因此應用程式必須開始下載新的內容, 並在接收通知的30秒內呼叫完成處理常式區塊, 否則 iOS 會終止應用程式。 在背景下載媒體或其他大型檔案時, 請考慮將遠端通知與_背景傳送服務_配對。


### <a name="silent-remote-notifications"></a>無訊息遠端通知

遠端通知是通知應用程式更新並開始提取新內容的簡單方法, 但在某些情況下, 我們不需要通知使用者已變更某個專案。 例如, 如果使用者將檔案標示為要進行同步, 我們就不需要在每次檔案更新時通知他們。 檔案同步不是令人驚訝的事件, 也不需要使用者立即注意。 當使用者開啟檔案時, 就會預期該檔案是最新的。

若為上述案例, iOS 可讓您以無訊息方式傳送推播通知 (也就是沒有警示)。 若要將一般通知轉換成無訊息, 請直接從通知承載移除警示:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

從開發人員的觀點來看, 一般和無訊息通知的最大差異在於, 無訊息推播會受到速率限制。 如果推播速率太高, APNs 會延遲將無訊息推播傳遞至裝置。 這是為了確保應用程式不會清空具有太多無訊息通知的裝置資源。

不過, APNs 會讓無訊息通知與一般的遠端通知或保持連線的回應一起「攜帶」。 由於一般通知不會受到速率限制, 因此可以用來將儲存的無訊息通知從 APNs 推送至裝置, 如下圖所示:

 [![](updating-an-application-in-the-background-images/silent.png "一般通知可用來將儲存的無訊息通知從 APNs 推送至裝置, 如下圖所示")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple 鼓勵開發人員在應用程式需要時傳送無訊息推播通知, 並讓 APNs 排程其傳遞。


在本節中, 我們已討論在背景中重新整理內容的各種選項, 以執行不符合背景必要類別的工作。 現在, 讓我們來看看其中一些 Api 的實際運作。

 [下一步：第4部分-iOS 背景處理逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
