---
title: 在 Xamarin 中的背景傳輸和 NSURLSession
description: 本檔提供逐步解說，示範如何使用背景傳送和 NSUrlSession 來開始下載大型影像，並在應用程式放在背景時繼續進行下載。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/02/2020
ms.openlocfilehash: b95b78ed36fcec122006cab7ce9663c43f9b3096
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436658"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>在 Xamarin 中的背景傳輸和 NSURLSession

背景傳輸是藉由設定背景 `NSURLSession` 和佇列上傳或下載工作來起始。 如果工作在應用程式背景執行、暫停或終止時完成，iOS 會在應用程式的 *AppDelegate*中呼叫完成處理常式來通知應用程式。 下圖將示範這項操作：

 [![背景傳輸是藉由設定背景 NSURLSession 和佇列上傳或下載工作來起始](background-transfer-walkthrough-images/transfer.png)](background-transfer-walkthrough-images/transfer.png#lightbox)

## <a name="configuring-a-background-session"></a>設定背景會話

若要建立背景會話，請建立新的， `NSUrlSession` 並使用物件進行設定 `NSUrlSessionConfiguration` 。

設定物件可決定會話可以執行的作業，以及可執行檔工作類型。
使用方法設定的會話 `CreateBackgroundSessionConfiguration` 將會在個別的進程中執行，並執行任意 (WiFi) 傳輸，以保留資料和電池壽命。
下列程式碼範例將示範如何使用 `CreateBackgroundSessionConfiguration` 方法和唯一的字串識別碼，正確設定背景傳送會話：

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, new MySessionDelegate(), new NSOperationQueue());

}
```

除了設定物件之外，會話也需要會話委派和佇列。
佇列會決定工作的完成順序。 會話委派會 chaperones 傳輸程式，並處理驗證、快取和其他與會話相關的問題。

## <a name="working-with-tasks-and-delegates"></a>使用工作和委派

現在我們已設定背景會話，接下來讓我們開始處理傳送作業。 我們可以使用 `NSUrlSessionDelegate` 名為會話委派的實例來追蹤這些工作。 會話委派負責在背景喚醒終止或暫停的應用程式，以處理驗證、錯誤或傳輸完成。

`NSUrlSessionDelegate`提供下列基本方法來檢查傳輸狀態：

- *DidFinishEventsForBackgroundSession* -當所有工作都已完成，且傳送完成時，會呼叫此方法。
- *DidReceiveChallenge* -在需要授權時呼叫以要求認證。
- *DidBecomeInvalidWithError* -如果變成無效，則呼叫  `NSURLSession` 。

背景會話需要更特製化的委派，視執行的工作類型而定。 背景會話限制為兩種類型的工作：

- *上傳* 工作-類型的工作：  `NSUrlSessionUploadTask` 使用執行的 `INSUrlSessionTaskDelegate` 介面 `INSUrlSessionDelegate` 。 這會提供其他方法來追蹤上傳進度、處理 HTTP 重新導向等等。
- *下載* 工作-類型的工作-  `NSUrlSessionDownloadTask` 使用 `INSUrlSessionDownloadDelegate` 介面來執行 `INSUrlSessionDelegate` 和 `INSUrlSessionTaskDelegate` 。 這會提供上傳工作的所有方法，以及下載特定的方法來追蹤下載進度，並判斷下載工作已恢復或完成的時間。

下列程式碼會定義可用於從 URL 下載影像的工作。 工作是透過在背景會話呼叫來啟動 `CreateDownloadTask` ，並傳入 URL 要求：

```csharp
const string DownloadURLString = "http://xamarin.com/images/xamarin.png"; // or other hosted file
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接著，建立新的會話下載委派，以追蹤此會話中的所有下載工作。 委派類別應該繼承 `NSObject` ，並執行必要的介面：

```csharp
public class MySessionDelegate : NSObject, INSUrlSessionDownloadDelegate
{
  public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

若要找出下載工作的進度，請覆寫 `DidWriteData` 方法以追蹤進度，甚至更新 UI。 如果應用程式在前景，或將在下次開啟應用程式時等待使用者，則會立即顯示 UI 更新。

會話委派 API 提供廣泛的工具組，可與工作進行互動。 如需會話委派方法的完整清單，請參閱 `NSUrlSessionDelegate` API 檔。

> [!IMPORTANT]
> 背景會話會在背景執行緒上啟動，因此任何更新 UI 的呼叫都必須在 UI 執行緒上明確地執行，方法是呼叫 `InvokeOnMainThread` ，以避免 iOS 終止應用程式。 

## <a name="handling-transfer-completion"></a>處理傳輸完成

最後一個步驟是讓應用程式知道與會話相關聯的所有工作都已完成，並處理新的內容。

在中 `AppDelegate` ，訂閱 `HandleEventsForBackgroundUrl` 事件。 當應用程式進入背景且傳送會話正在執行時，系統會呼叫此方法，而系統會將完成處理常式傳遞給我們：

```csharp
public System.Action backgroundSessionCompletionHandler;

public void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

使用完成處理常式，讓 iOS 知道應用程式完成處理的時間。

回想一下，會話可能會產生數個工作以處理傳輸。 當最後一個工作完成時，暫停或終止的應用程式會重新開機到背景中。 然後，應用程式會 `NSURLSession` 使用唯一的會話識別碼重新連接至，並 `DidFinishEventsForBackgroundSession` 在會話委派上呼叫。 此方法是應用程式處理新內容的機會，包括更新 UI 以反映傳輸的結果：

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

完成處理新的內容之後，請呼叫完成處理常式，讓系統知道可以安全地建立應用程式的快照，並返回睡眠狀態：

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

本逐步解說涵蓋在 iOS 7 和更新版本中執行背景傳送服務的基本步驟。

## <a name="related-links"></a>相關連結

- [簡單的背景傳輸 (範例) ](/samples/xamarin/ios-samples/simplebackgroundtransfer)