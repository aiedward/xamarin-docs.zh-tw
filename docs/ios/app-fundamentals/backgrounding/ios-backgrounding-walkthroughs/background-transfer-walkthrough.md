---
title: 逐步解說-使用背景傳送服務和 NSURLSession
description: 在本逐步解說中，使用背景傳送的服務和 NSURLSession API 開始進行大型的映像，會下載應用程式時在背景中繼續下載。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bedfb6298e63ed06063707178af1ca48179d0dd7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-background-transfer-service-and-nsurlsession"></a>逐步解說-使用背景傳送服務和 NSURLSession

_在本逐步解說中，使用背景傳送的服務和 NSURLSession API 開始進行大型的映像，會下載應用程式時在背景中繼續下載。_

藉由設定背景起始背景傳送`NSURLSession`和上傳或下載工作的佇列。 如果工作完成時 backgrounded、 暫止，或結束應用程式時，iOS 會通知應用程式藉由在應用程式中呼叫完成處理常式*AppDelegate*。 下圖示範中的動作：

 [![](background-transfer-walkthrough-images/transfer.png "藉由設定背景 NSURLSession 起始背景傳送，並將上傳或下載工作")](background-transfer-walkthrough-images/transfer.png#lightbox)

我們來看看什麼這看起來像在程式碼中。

## <a name="configuring-a-background-session"></a>設定背景工作階段

若要讓背景工作階段，建立新`NSUrlSession`並設定它使用`NSUrlSessionConfiguration`物件。

組態物件可以決定可以執行的工作階段，它可以執行的工作類型。
設定使用的工作階段`CreateBackgroundSessionConfiguration`方法會在個別的處理序中執行，並執行判別 (WiFi) 傳輸，以保有資料與電池壽命。
下列程式碼範例示範正確的安裝程式的背景傳送工作階段使用`CreateBackgroundSessionConfiguration`方法和唯一字串識別項：

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate, new NSOperationQueue());

}
```

除了組態物件，工作階段也會需要工作階段代理人和佇列。
佇列會決定的工作會完成的順序。 工作階段代理人 chaperones 傳輸程序和處理驗證、 快取和其他工作階段相關的問題。

## <a name="working-with-tasks-and-delegates"></a>使用工作和委派

既然我們已經設定背景工作階段，讓我們開始處理傳輸的工作。 我們可以追蹤的使用這些工作`NSUrlSessionDelegate`呼叫工作階段代理人的執行個體。 工作階段代理人負責喚醒終止或暫停應用程式在背景中的處理驗證、 錯誤或傳輸完成。

`NSUrlSessionDelegate`提供下列基本的方法，若要檢查傳送狀態：

-  *DidFinishEventsForBackgroundSession* -當所有的工作完成，並傳送已完成時，會呼叫這個方法。
-  *DidReceiveChallenge* -要求認證需要授權時呼叫。
-  *DidBecomeInvalidWithError* -呼叫的如果`NSURLSession`失效。


背景工作階段需要更具特製化的委派，根據執行的工作類型。 背景工作階段會限制為兩種類型的工作項目：

-  *上傳工作*-類型的工作`NSUrlSessionUploadTask`使用`NSUrlSessionTaskDelegate`，後者繼承自`NSUrlSessionDelegate`。 這個委派會提供額外的方法，以追蹤上傳進度、 控制代碼 HTTP 重新導向，以及更多。
-  *下載工作*-類型的工作`NSUrlSessionDownloadTask`使用`NSUrlSessionDownloadDelegate`，後者繼承自`NSUrlSessionTaskDelegate`。 這個委派會提供的所有方法上都傳工作，以及下載特定的方法，來追蹤下載進度，並判斷當下載工作繼續，或已完成。


下列程式碼會定義可用來從 URL 下載映像的工作。 我們藉由呼叫啟動工作`CreateDownloadTask`在我們的背景工作階段，並傳入 URL 要求：

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接下來，我們將建立新的工作階段下載委派給追蹤的所有下載工作在此工作階段：

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

如果我們想要找出下載工作的進度，我們可以覆寫`DidWriteData`方法來追蹤進度，甚至更新 UI。 如果應用程式位於前景，或等候使用者的下次開啟應用程式時，會立即出現 UI 更新。

工作階段代理人 API 可提供廣泛的工具組與工作互動。 如需完整清單，工作階段的委派方法，請參閱`NSUrlSessionDelegate`API 文件。

> [!IMPORTANT]
> 背景工作階段並啟動背景執行緒，所以任何呼叫才能更新 UI 必須明確地執行 UI 執行緒上呼叫`InvokeOnMainThread`避免 iOS 終止應用程式。 


## <a name="handling-transfer-completion"></a>處理傳送完成

最後一個步驟是讓應用程式知道工作階段相關聯的所有工作都完成時，並處理新的內容。

在*AppDelegate*，訂閱`HandleEventsForBackgroundUrl`事件。 當應用程式進入背景傳送工作階段正在執行，會呼叫這個方法，系統會傳遞給我們完成處理常式：

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

我們將使用完成處理常式，讓 iOS 知道我們的應用程式完成時處理。

前文提過的工作階段便會產生數個工作来處理的傳輸。 最後一個工作完成時，暫止或終止應用程式是在背景重新啟動。 然後，應用程式重新連線至`NSURLSession`使用唯一的工作階段識別碼和呼叫`DidFinishEventsForBackgroundSession`工作階段在委派上。 這個方法是應用程式的機會，以處理新的內容，包括更新 UI，以反映傳輸的結果：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

一旦我們完成時處理新的內容，我們會呼叫完成處理常式，讓系統得知安全地建立應用程式的快照，然後回到睡眠狀態：

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

在本逐步解說中，我們會探討實作在 iOS 7 中的背景傳送服務的基本步驟。



## <a name="related-links"></a>相關連結

- [簡單背景傳送 （範例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
