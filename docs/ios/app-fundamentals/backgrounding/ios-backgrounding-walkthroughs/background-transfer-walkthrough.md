---
title: 在 Xamarin 中的背景傳輸和 NSURLSession
description: 本檔提供的逐步解說示範如何使用背景傳輸和 NSUrlSession 來開始下載大型影像，並在應用程式置於背景時繼續下載。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 3e27cffa9e2605c3697536f226fe87fbbf1bfbbd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286886"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>在 Xamarin 中的背景傳輸和 NSURLSession

背景傳輸是藉由設定背景`NSURLSession`和佇列上傳或下載工作來起始。 如果工作在背景執行、擱置或終止應用程式時完成，iOS 會在應用程式的*AppDelegate*中呼叫完成處理常式，以通知應用程式。 下圖將示範這項操作：

 [![](background-transfer-walkthrough-images/transfer.png "背景傳輸是藉由設定背景 NSURLSession 和佇列上傳或下載工作來起始")](background-transfer-walkthrough-images/transfer.png#lightbox)

讓我們來看看這在程式碼中的樣子。

## <a name="configuring-a-background-session"></a>設定背景會話

若要建立背景會話，請建立新`NSUrlSession`的，並`NSUrlSessionConfiguration`使用物件加以設定。

設定物件會決定會話可執行檔作業，以及可執行檔工作類型。
使用方法設定的`CreateBackgroundSessionConfiguration`會話會在個別的進程中執行，並執行任意（WiFi）傳輸以保留資料和電池壽命。
下列程式碼範例將示範如何使用`CreateBackgroundSessionConfiguration`方法和唯一的字串識別碼，正確設定背景傳輸會話：

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

除了設定物件之外，會話也需要會話委派和佇列。
佇列會決定工作的完成順序。 會話委派會 chaperones 傳輸進程，並處理驗證、快取和其他會話相關的問題。

## <a name="working-with-tasks-and-delegates"></a>使用工作和委派

我們已設定背景會話，接下來讓我們開始處理傳輸作業。 我們可以使用`NSUrlSessionDelegate`稱為會話委派的實例來追蹤這些工作。 會話委派負責在背景喚醒終止或暫停的應用程式，以處理驗證、錯誤或傳輸完成。

`NSUrlSessionDelegate`提供下列基本方法來檢查傳輸狀態：

- *DidFinishEventsForBackgroundSession* -當所有工作都已完成，且傳送完成時，會呼叫此方法。
- *DidReceiveChallenge* -在需要授權時呼叫以要求認證。
- *DidBecomeInvalidWithError* -如果變成無效`NSURLSession` ，則呼叫。


背景會話需要更多特製化的委派，視執行中的工作類型而定。 背景會話限制為兩種類型的工作：

- *上傳*工作-類型`NSUrlSessionUploadTask`的工作`NSUrlSessionTaskDelegate`會使用繼承自`NSUrlSessionDelegate`的。 此委派提供其他方法來追蹤上傳進度、處理 HTTP 重新導向等等。
- *下載*工作-類型`NSUrlSessionDownloadTask`的工作`NSUrlSessionDownloadDelegate`會使用繼承自`NSUrlSessionTaskDelegate`的。 此委派會提供上傳工作的所有方法，以及下載特定方法來追蹤下載進度，並判斷下載工作已繼續或完成的時間。


下列程式碼會定義可用於從 URL 下載影像的工作。 我們會在我們的背景會話`CreateDownloadTask`上呼叫，並傳入 URL 要求，以開始工作：

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接下來，我們會建立新的會話下載委派，以追蹤此會話中的所有下載工作：

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

如果我們想要找出下載工作的進度，我們可以覆寫`DidWriteData`方法來追蹤進度，甚至更新 UI。 如果應用程式在前景中，或將在下次開啟應用程式時等候使用者，UI 更新會立即出現。

會話委派 API 提供了用於與工作互動的廣泛工具組。 如需會話委派方法的完整清單，請參閱`NSUrlSessionDelegate` API 檔。

> [!IMPORTANT]
> 背景會話會在背景執行緒上啟動，因此任何更新 ui 的呼叫都必須在 ui 執行緒上明確地執行，方法`InvokeOnMainThread`是呼叫以避免 iOS 終止應用程式。 


## <a name="handling-transfer-completion"></a>處理傳輸完成

最後一個步驟是讓應用程式知道與會話相關聯的所有工作都已完成，並處理新的內容。

在*AppDelegate*中，訂閱`HandleEventsForBackgroundUrl`事件。 當應用程式進入背景並執行傳輸會話時，會呼叫這個方法，而系統會將完成處理常式傳遞給我們：

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

我們將使用完成處理常式，讓 iOS 知道我們的應用程式完成處理的時間。

回想一下，會話可能會產生數個工作來處理傳輸。 當最後一個工作完成時，暫停或終止的應用程式會重新開機到背景中。 然後，應用程式會`NSURLSession`使用唯一的會話識別碼重新連接到，並在會話委派上呼叫。 `DidFinishEventsForBackgroundSession` 這個方法是應用程式處理新內容的機會，包括更新 UI 以反映傳輸的結果：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

當我們完成新內容的處理之後，我們會呼叫完成處理常式，讓系統知道可以安全地建立應用程式的快照，並返回睡眠狀態：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
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

在本逐步解說中，我們涵蓋了在 iOS 7 中執行背景傳輸服務的基本步驟。



## <a name="related-links"></a>相關連結

- [簡單背景傳輸（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
