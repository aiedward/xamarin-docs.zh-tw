---
title: iCloud
description: "Apple 引進了 iCloud iOS 5 中的，以允許 Apple 的伺服器上儲存的資料，並讓它可以跨所有裝置 （透過其 Apple ID) 相同的人使用的同步處理的應用程式服務。 它也有備份的元件，其中您的裝置上的資料會備份到 Apple 的伺服器。 本文件說明如何使用某些 icloud 的功能 Api 提供 Apple 所儲存和擷取資料，從其伺服器使用，但 C# 範例儲存小的索引鍵-值資料組以及將儲存的文件。 它也會討論 iCloud 備份可能會如何影響您的應用程式的設計。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: 7e02c92f9c1aafeb97da4905c17898b02362c960
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="icloud"></a>iCloud

_Apple 引進了 iCloud iOS 5 中的，以允許 Apple 的伺服器上儲存的資料，並讓它可以跨所有裝置 （透過其 Apple ID) 相同的人使用的同步處理的應用程式服務。它也有備份的元件，其中您的裝置上的資料會備份到 Apple 的伺服器。本文件說明如何使用某些 icloud 的功能 Api 提供 Apple 所儲存和擷取資料，從其伺服器使用，但 C# 範例儲存小的索引鍵-值資料組以及將儲存的文件。它也會討論 iCloud 備份可能會如何影響您的應用程式的設計。_

IOS 5 iCloud 儲存體 API 可讓應用程式，將使用者文件和應用程式特定資料儲存到中央位置，然後從所有使用者的裝置存取這些項目。

有四種類型的儲存體：

- **索引鍵 / 值儲存體**-若要共用上的少量資料與應用程式使用者的其他裝置。

- **UIDocument 儲存體**-若要將文件和其他資料儲存在使用者的 iCloud 帳戶使用的 UIDocument 子類別。

- **發生 CoreData** -SQLite 資料庫儲存體。

- **個別的檔案和目錄**： 適用於管理直接在檔案系統中的許多不同的檔案。

本文討論的前兩個類型的索引鍵-值組和 UIDocument 子類別-以及如何在 Xamarin.iOS 中使用這些功能。

## <a name="requirements"></a>需求

- Xamarin.iOS 最新穩定版本
- Xcode 8 或更新版本
- Mac 或 Visual Studio 2015 及更新版本的 visual Studio。

## <a name="preparing-for-icloud-development"></a>準備 iCloud 開發

應用程式必須設定為使用中的 iCloud [Apple 佈建入口網站](https://developer.apple.com/account/ios/overview.action)和專案本身。 之前開發 icloud 的功能 （或試用範例），請遵循下列步驟。

若要正確設定應用程式存取 icloud 的功能：

-   **尋找您要求的**-登入[developer.apple.com](http://developer.apple.com) ，瀏覽**會員中心 > 您的帳戶 > 開發人員帳戶摘要**取得您小組 ID （或者個別的單一開發人員). 它會是 10 個字元的字串 ( **A93A5CM278**例如)-這會形成 「 容器的識別項 」 的一部分。

-   **建立新的應用程式識別碼**-若要建立應用程式識別碼，請依照下列所述的步驟[佈建的存放區技術指南中的章節裝置佈建](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)，一定要檢查和**iCloud**為允許的服務：

 [ ![](introduction-to-icloud-images/icloud-sml.png "為允許服務的核取 iCloud")](introduction-to-icloud-images/icloud.png)

- **建立新的佈建設定檔**-若要建立佈建設定檔，請依照下列所述的步驟[裝置佈建指南](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile)。

- **容器識別碼加入 Entitlements.plist** -容器識別碼格式是`TeamID.BundleID`。 如需詳細資訊請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- **設定專案屬性**-Info.plist 檔案確保在**配套識別碼**符合**套件組合識別碼**時設定[建立應用程式識別碼](~/ios/deploy-test/provisioning/capabilities/index.md);使用 iOS 套件組合簽署**佈建設定檔**，其中包含應用程式服務 icloud 的功能與應用程式識別碼和**自訂權利**選取檔案。 所有作法是在 Visual Studio 中的專案屬性 窗格底下。

- **啟用您的裝置上的 iCloud** -移至**設定 > iCloud** ，並確定裝置已登入。
選取並開啟**文件和資料**選項。

- **您必須使用裝置來測試 iCloud** -模擬器上將無法運作。
事實上，您確實需要兩個或多個裝置都登入相同的 Apple ID，若要查看作用中的 iCloud。


## <a name="key-value-storage"></a>索引鍵 / 值儲存體

索引鍵-值 」 儲存區適用於小量的使用者可能想保存跨裝置-例如他們檢視書籍或雜誌中的最後一頁的資料。 索引鍵-值存放裝置不應該用於備份資料。

有一些限制需要注意的使用索引鍵 / 值儲存體時：

- **最大金鑰大小**-索引鍵名稱不能超過 64 個位元組。

- **最大值大小**-您無法將超過 64 kb 儲存在單一的值。

- **應用程式的最大索引鍵-值存放區大小**-應用程式只可以儲存最多 64 kb 的資料索引鍵-值總計。 嘗試設定超過該限制的機碼將會失敗，並將保存先前的值。

- **資料型別**-只有基本類型例如字串、 數字，而且可以儲存布林值。

**ICloudKeyValue**範例會示範其運作方式。 範例程式碼會建立名為每個裝置的金鑰： 您可以在一個裝置上設定此機碼，並取得傳播給其他人的值。 它也會建立稱為 「 共用 」 可以編輯任何在裝置上-如果您一次編輯許多裝置上的索引鍵，icloud 的功能將會決定哪些值"wins"（使用時間戳記變更），並取得傳播。

這個螢幕擷取畫面會顯示使用中的範例。 當從 iCloud 收到變更通知時他們列印螢幕的底部捲動文字檢視中，輸入欄位中更新。



 [ ![](introduction-to-icloud-images/icloud-kv-arrows.png "裝置之間的訊息流程")](introduction-to-icloud-images/icloud-kv-arrows.png)

### <a name="setting-and-retrieving-data"></a>設定和擷取資料

此程式碼會示範如何設定字串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

呼叫同步處理可確保值會保存至本機磁碟儲存體。 會在背景同步處理到 icloud 的功能，並無法 「 強制 」 應用程式碼。 與網路連線良好的同步處理將通常會發生在 5 秒內，不過，如果網路不佳 （或已中斷連線） 更新可能會花更長時間。

您可以擷取值，這個程式碼：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

從本機資料存放區擷取的值，這個方法不會嘗試連絡 iCloud 伺服器來取得 「 最新 」 值。 icloud 的功能將會更新本機資料存放區根據自己的排程。

### <a name="deleting-data"></a>刪除資料

若要完全移除的索引鍵-值組，請使用 Remove 方法如下：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>觀察變更

應用程式也可以收到通知時變更加入到觀察者的 iCloud 值`NSNotificationCenter.DefaultCenter`。
下列程式碼會從**KeyValueViewController.cs** `ViewWillAppear`方法顯示如何將接聽這些通知，並建立索引鍵已變更的清單：

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

您的程式碼可以採取某些動作的清單已變更的機碼，例如更新的本機副本，或使用新值更新 UI。

有可能變更的原因： ServerChange (0)、 InitialSyncChange (1) 或 QuotaViolationChange (2)。 您可以存取的原因，並視需要執行不同的處理 (例如，您可能需要移除的一些金鑰*QuotaViolationChange*)。

## <a name="document-storage"></a>文件儲存體

文件儲存 icloud 的功能被設計來管理您的應用程式 （以及使用者） 是非常重要的資料。 它可以用來管理檔案和應用程式需要執行，而同時提供 icloud 的功能為基礎的備份，並在所有使用者的裝置之間共用功能的其他資料。

這個圖表可顯示所有它如何搭配運用。 每個裝置都儲存在本機儲存體 (UbiquityContainer) 和作業系統的 iCloud 服務精靈會負責傳送和接收資料在雲端上的資料。 所有的檔案存取權 UbiquityContainer 必須透過 FilePresenter/FileCoordinator 以防止並行存取。 `UIDocument`類別實作的您，此範例示範如何使用 UIDocument。

 [ ![](introduction-to-icloud-images/icloud-overview.png "文件儲存體概觀")](introduction-to-icloud-images/icloud-overview.png)

ICloudUIDoc 範例實作簡單`UIDocument`子類別，其中包含一個文字欄位。 在呈現文字`UITextView`和編輯以紅色顯示的通知訊息傳播到其他裝置 iCloud。 範例程式碼不會處理更多進階 iCloud 的功能，例如衝突解決。

這個螢幕擷取畫面顯示範例應用程式-將文字變更後按**UpdateChangeCount**透過 iCloud 到其他裝置同步處理文件。

 [ ![](introduction-to-icloud-images/iclouduidoc.png "這個螢幕擷取畫面顯示變更的文字，並按下 UpdateChangeCount 之後的範例應用程式")](introduction-to-icloud-images/iclouduidoc.png)

有五個部分 iCloudUIDoc 範例：

1. **存取 UbiquityContainer** -判斷如果 icloud 的功能已啟用，而且如果是您的應用程式 iCloud 儲存區的路徑。

1. **建立 UIDocument 子類別**-建立類別，以調解 iCloud 儲存體和模型物件。

1. **尋找及開啟 iCloud 文件**-使用`NSFileManager`和`NSPredicate`尋找 iCloud 文件，並開啟它們。

1. **顯示 iCloud 文件**-公開屬性，從您`UIDocument`，讓您可以互動的 UI 控制項。

1. **儲存 iCloud 文件**-請在 UI 中所做的變更會保存到磁碟和 icloud 的功能。

所有 iCloud 作業執行 （或應該） 以非同步方式如此才不會阻擋等候事情發生時。 您會看到三個不同的方式完成此範例中：

 **執行緒**-在`AppDelegate.FinishedLaunching`一次呼叫`GetUrlForUbiquityContainer`若要防止封鎖主執行緒的另一個執行緒上完成。

 **NotificationCenter** -註冊通知，當非同步作業，例如`NSMetadataQuery.StartQuery`完成。

 **完成處理常式**-傳入方法來完成非同步作業，例如執行`UIDocument.Open`。

### <a name="accessing-the-ubiquitycontainer"></a>存取 UbiquityContainer

使用 iCloud 文件儲存體的第一個步驟是判斷是否已啟用 icloud 的功能，而且如果是 「 普遍性而益發嚴重容器 」 的位置 （在裝置儲存 icloud 的功能啟用檔案的目錄）。

此程式碼位於`AppDelegate.FinishedLaunching`範例的方法。

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

雖然此範例不會這樣，Apple 建議呼叫 GetUrlForUbiquityContainer，每當應用程式移至前景。

### <a name="creating-a-uidocument-subclass"></a>建立 UIDocument 子類別

所有 iCloud 檔案和目錄 (ie。 任何項目儲存在 UbiquityContainer 目錄中) 必須使用 NSFileManager 方法，實作 NSFilePresenter 通訊協定和 NSFileCoordinator 透過撰寫管理。
執行所有作業，最簡單的方式不是將資料寫入您自己，但它它 UIDocument 子類別為您。

有您必須在使用 iCloud UIDocument 子類別中實作的只有兩個方法：

- **LoadFromContents** -傳入的檔案的內容，以解除封裝至您模型的類別/es NSData。

- **ContentsForType** -要求您提供您模型的類別/es NSData 表示法來儲存到磁碟 （和雲端）。

此範例程式碼，從**iCloudUIDoc\MonkeyDocument.cs**示範如何實作 UIDocument。

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

資料模型在此情況下是非常簡單的單一文字欄位。 您的資料模型可以很複雜，例如必要項目，例如 Xml 文件或二進位資料。 UIDocument 實作的主要角色是您在模型類別，以及可以是磁碟上儲存/載入的 NSData 表示之間進行轉譯。

### <a name="finding-and-opening-icloud-documents"></a>尋找及開啟 iCloud 文件

範例應用程式只會處理單一檔案-test.txt-所以中的程式碼**d**建立`NSPredicate`和`NSMetadataQuery`特別尋找該檔案名稱。 `NSMetadataQuery`以非同步方式執行，並完成時傳送通知。 `DidFinishGathering` 取得呼叫通知觀察器，會停止查詢，且呼叫 LoadDocument，會使用`UIDocument.Open`嘗試載入檔案，並顯示在完成處理常式方法`MonkeyDocumentViewController`。

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>顯示 iCloud 文件

顯示 UIDocument 不得為任何其他任何模型類別
- 屬性會顯示在 UI 控制項，使用者可能是編輯並接著寫回至模型。

在範例**iCloudUIDoc\MonkeyDocumentViewController.cs**顯示 MonkeyDocument 文字中的`UITextView`。 `ViewDidLoad` 接聽中傳送的通知`MonkeyDocument.LoadFromContents`方法。 `LoadFromContents` 會呼叫 icloud 的功能檔案的新資料時，以便通知會指示文件已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

範例程式碼通知處理常式呼叫方法以更新 UI-在此情況下沒有任何衝突偵測或解決。

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>正在儲存 iCloud 文件

若要加入到 icloud 的功能，您可以呼叫 UIDocument`UIDocument.Save`直接 （適用於新的文件） 或移動現有的檔案使用`NSFileManager.DefaultManager.SetUbiquitious`。 範例程式碼的新文件直接在容器中建立普遍性而益發嚴重這個程式碼 (有兩個完成處理常式，一個用於`Save`作業，而另一個為開啟):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

文件的後續變更不會 「 儲存 」 直接，相反地，我們告訴`UIDocument`它已變更與`UpdateChangeCount`，則會自動排程儲存至磁碟的作業和：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 文件

使用者可以管理 icloud 的功能中的文件**文件**"普遍性而益發嚴重容器 」 之外的 via 設定; 您的應用程式的目錄他們可以檢視檔案清單和要刪除的撥動。 應用程式程式碼應能處理這種情況的文件刪除使用者的位置。 無法儲存內部應用程式中的資料**文件**目錄。

 [ ![](introduction-to-icloud-images/icloudstorage.png "管理 iCloud 文件工作流程")](introduction-to-icloud-images/icloudstorage.png)



當他們嘗試移除 icloud 的功能啟用應用程式從其裝置，通知他們與該應用程式相關的 iCloud 文件的狀態時，使用者也會收到不同的警告。

 [ ![](introduction-to-icloud-images/icloud-delete1.png "當使用者嘗試從他們的裝置移除啟用 icloud 的功能的應用程式的範例對話方塊")](introduction-to-icloud-images/icloud-delete1.png)

 [ ![](introduction-to-icloud-images/icloud-delete2.png "當使用者嘗試從他們的裝置移除啟用 icloud 的功能的應用程式的範例對話方塊")](introduction-to-icloud-images/icloud-delete2.png)

## <a name="icloud-backup"></a>iCloud 備份

雖然備份至 icloud 的功能並不是由開發人員直接存取的功能，您將應用程式設計的方式可能會影響使用者體驗。
Apple 提供[iOS 資料存放區的指導方針](http://developer.apple.com/icloud/documentation/data-storage/)的開發人員在其 iOS 應用程式。

最重要的考量就是內容的您的應用程式是否儲存大型檔案不是內容的使用者產生的 （例如雜誌閱讀程式應用程式儲存的每個問題 hundred-plus mb 為單位）。 Apple 偏好您不要儲存這類資料，它會被備份到 icloud 的功能和不必要地填入使用者的 iCloud 配額。

儲存大量資料，就像這樣的應用程式應該是將它儲存在其中一個備份 （例如不是使用者目錄 快取或 tmp） 或使用`NSFileManager.SetSkipBackupAttribute`將套用至這些檔案的旗標，以便 icloud 的功能就會忽略它們在備份作業期間。

## <a name="summary"></a>總結

這篇文章導入了新 iCloud 的功能併入 iOS 5。 它會檢查的步驟，才能將專案設定為使用 icloud 的功能，然後提供如何實作 iCloud 的功能的範例。

索引鍵 / 值儲存體範例示範如何 icloud 的功能可以用來儲存少量資料 NSUserPreferences 儲存的方式類似。 UIDocument 範例示範如何更多複雜的資料可儲存及跨多個裝置透過 iCloud 同步處理。

最後，它會包含上新增 iCloud 備份應該如何影響您的應用程式設計的簡短討論。



## <a name="related-links"></a>相關連結

- [簡介至 iCloud （範例）](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [iCloud 研討會範例程式碼](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研討會投影片](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 儲存體](http://support.apple.com/kb/HT4847)
