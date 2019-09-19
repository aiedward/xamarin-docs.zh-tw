---
title: 使用 iCloud 搭配 Xamarin. iOS
description: 本檔說明 iCloud 和其在 Xamarin iOS 應用程式中的使用方式。 它討論索引鍵/值儲存體、檔儲存體和 iCloud 備份。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/09/2016
ms.openlocfilehash: f2bc6ce6c709f99c744554d80b065e34961904ac
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105848"
---
# <a name="using-icloud-with-xamarinios"></a>使用 iCloud 搭配 Xamarin. iOS

IOS 5 中的 iCloud 儲存體 API 可讓應用程式將使用者檔和應用程式特定的資料儲存到中央位置，並從所有使用者的裝置存取這些專案。

有四種可用的儲存體類型：

- 索引**鍵/值儲存體**-在使用者的其他裝置上與您的應用程式共用少量資料。

- **UIDocument 儲存體**-使用 UIDocument 的子類別，將檔和其他資料儲存在使用者的 iCloud 帳戶中。

- **CoreData** -SQLite 資料庫儲存體。

- **個別檔案和目錄**-用於直接在檔案系統中管理許多不同的檔案。

本檔討論前兩種類型-索引鍵/值組和 UIDocument 子類別，以及如何在 Xamarin 中使用這些功能。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

- 最新穩定版本的 Xamarin. iOS
- Xcode 10
- Visual Studio for Mac 或 Visual Studio 2019。

## <a name="preparing-for-icloud-development"></a>準備 iCloud 開發

應用程式必須設定為在 Apple 布建[入口網站](https://developer.apple.com/account/ios/overview.action)和專案本身中使用 iCloud。 針對 iCloud 進行開發（或嘗試執行範例）之前，請遵循下列步驟。

若要正確地設定應用程式來存取 iCloud：

- **尋找您的 TeamID**登入[developer.apple.com](https://developer.apple.com) ，並流覽**成員中心 > 您的帳戶 > 開發人員帳戶摘要**，以取得您的小組識別碼（或單一開發人員的個別識別碼）。 它會是10個字元的字串（例如**A93A5CM278** ）-這會形成「容器識別碼」的一部分。

- **建立新的應用程式識別碼**-若要建立應用程式識別碼，請依照裝置布建指南中的布建[儲存技術一節](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)中所述的步驟進行，並務必檢查**iCloud**是否為允許的服務：

 [![](introduction-to-icloud-images/icloud-sml.png "檢查 iCloud 是否為允許的服務")](introduction-to-icloud-images/icloud.png#lightbox)

- **建立新的布建設定檔**-若要建立布建設定檔，請依照[裝置](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device)布建指南中所述的步驟進行。

- **將容器識別碼新增至 plist** -容器識別碼格式為`TeamID.BundleID`。 如需詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 設定**專案屬性**-在 plist 檔案中，確保配套**識別碼**符合建立[應用程式 ID](~/ios/deploy-test/provisioning/capabilities/index.md)時所設定的套件組合**ID** ;IOS 套件組合簽署會使用包含具有 iCloud App Service 之應用程式識別碼的布建**設定檔**，以及選取的**自訂權利**檔案。 這一切都可以在 [專案屬性] 窗格下的 Visual Studio 中完成。

- **在您的裝置上啟用 iCloud** -移至 [**設定] > iCloud** ，並確定裝置已登入。
選取並開啟 [**檔 & 資料**] 選項。

- **您必須使用裝置來測試 iCloud** ，而不會在模擬器上作用。
事實上，您真的需要使用相同的 Apple ID 登入兩個或多個裝置，才能看到 iCloud 的作用。

## <a name="key-value-storage"></a>索引鍵/值儲存

索引鍵/值儲存體適用于少量的資料，使用者可能會喜歡在不同裝置之間保存，例如他們在書籍或雜誌中觀看的最後一頁。 索引鍵/值儲存體不應用於備份資料。

使用索引鍵/值儲存體時，有一些限制需要注意：

- **金鑰大小上限**-索引鍵名稱長度不能超過64個位元組。

- **最大值大小**-您不能在單一值中儲存超過 64 kb。

- 應用程式的索引**鍵/值存放區大小上限-最**多隻能儲存 64 kb 的索引鍵/值資料。 嘗試設定超出該限制的金鑰將會失敗，而先前的值將會保存。

- **資料類型**-只能儲存基本類型，例如字串、數位和布林值。

**ICloudKeyValue**範例會示範其運作方式。 範例程式碼會為每個裝置建立名為的金鑰：您可以在一個裝置上設定此金鑰，並監看此值是否會傳播給其他裝置。 它也會建立名為 "Shared" 的金鑰，可在任何裝置上編輯-如果您同時在多個裝置上進行編輯，iCloud 會決定哪個值為「wins」（使用變更上的時間戳記）並傳播。

此螢幕擷取畫面顯示使用中的範例。 從 iCloud 收到變更通知時，會將其列印在畫面底部的滾動文字視圖中，並在輸入欄位中更新。

 [![](introduction-to-icloud-images/icloud-kv-arrows.png "裝置之間的訊息流程")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>設定和抓取資料

這段程式碼會示範如何設定字串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

呼叫同步可確保此值只會保存到本機磁片儲存體。 ICloud 的同步處理會在背景中執行，且不能由應用程式代碼「強制」。 有了良好的網路連線，同步處理通常會在5秒內發生，但是如果網路不佳（或連線中斷），更新可能需要更長的時間。

您可以使用下列程式碼來抓取值：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

此值會從本機資料存放區抓取-此方法不會嘗試連線 iCloud 伺服器以取得「最新」值。 iCloud 會根據自己的排程來更新本機資料存放區。

### <a name="deleting-data"></a>刪除資料

若要完全移除索引鍵/值組，請使用 Remove 方法，如下所示：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>觀察變更

當 iCloud 變更值時，應用程式也可以接收通知，方法是將觀察者`NSNotificationCenter.DefaultCenter`加入至。
下列來自**KeyValueViewController.cs** `ViewWillAppear`方法的程式碼示範如何接聽這些通知，並建立已變更金鑰的清單：

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

然後，您的程式碼可以對已變更的索引鍵清單採取一些動作，例如更新其本機複本或以新的值更新 UI。

可能的變更原因包括：ServerChange （0）、InitialSyncChange （1）或 QuotaViolationChange （2）。 您可以視需要存取原因並執行不同的處理（例如，您可能需要移除一些金鑰，因為*QuotaViolationChange*）。

## <a name="document-storage"></a>檔儲存體

iCloud 檔儲存體是設計來管理對您的應用程式（和使用者）很重要的資料。 它可以用來管理您的應用程式需要執行的檔案和其他資料，同時在所有使用者的裝置上提供 iCloud 型備份與共享功能。

此圖顯示如何配合。 每個裝置都有儲存在本機儲存體（UbiquityContainer）上的資料，而作業系統的 iCloud Daemon 會負責在雲端中傳送和接收資料。 所有對 UbiquityContainer 的檔案存取都必須透過 FilePresenter/FileCoordinator 完成，以防止平行存取。 類別`UIDocument`會為您執行這些工作，此範例示範如何使用 UIDocument。

 [![](introduction-to-icloud-images/icloud-overview.png "檔儲存體總覽")](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 範例會執行包含單一`UIDocument`文字欄位的簡單子類別。 文字會在中`UITextView`轉譯，而編輯會由 iCloud 傳播到其他裝置，並以紅色顯示通知訊息。 範例程式碼不會處理更先進的 iCloud 功能，例如衝突解決。

此螢幕擷取畫面顯示範例應用程式-變更文字並按下**UpdateChangeCount**後，檔會透過 iCloud 同步至其他裝置。

 [![](introduction-to-icloud-images/iclouduidoc.png "此螢幕擷取畫面顯示變更文字後的範例應用程式，並按下 UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

ICloudUIDoc 範例共有五個部分：

1. **存取 UbiquityContainer** -判斷是否已啟用 iCloud，如果是，則為應用程式 icloud 存放區域的路徑。

1. **建立 UIDocument 子類別**-建立一個類別，以在 iCloud 儲存體與您的模型物件之間進行中繼。

1. **尋找和開啟 icloud 檔**-使用`NSFileManager`和`NSPredicate`來尋找 icloud 檔並加以開啟。

1. **顯示 iCloud 檔**-從您`UIDocument`的公開屬性，讓您可以與 UI 控制項互動。

1. **儲存 iCloud 檔**-確保在 UI 中進行的變更會保存至磁片和 iCloud。

所有 iCloud 作業會以非同步方式執行（或應執行），使其在等候發生問題時不會封鎖。 在範例中，您會看到三種不同的方式來完成這項操作：

 **執行緒**-在`AppDelegate.FinishedLaunching` `GetUrlForUbiquityContainer`的初始呼叫中，會在另一個執行緒上完成，以避免封鎖主執行緒。

 **NotificationCenter** -在非同步作業（例如`NSMetadataQuery.StartQuery`完成）時註冊通知。

 **完成處理常式**-傳入方法，以在完成非同步作業（例如`UIDocument.Open`）時執行。

### <a name="accessing-the-ubiquitycontainer"></a>存取 UbiquityContainer

使用 iCloud 檔儲存體的第一個步驟是判斷是否已啟用 iCloud，如果是，則為 "無所不在 container" 的位置（已啟用 iCloud 的檔案儲存在裝置上的目錄）。

此程式碼位於範例`AppDelegate.FinishedLaunching`的方法中。

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

雖然此範例不會這麼做，但當應用程式進入前景時，Apple 建議呼叫 GetUrlForUbiquityContainer。

### <a name="creating-a-uidocument-subclass"></a>建立 UIDocument 子類別

所有 iCloud 檔案和目錄（也就是儲存在 UbiquityContainer 目錄中的任何專案）都必須使用 NSFileManager 方法來管理，並透過 NSFileCoordinator 執行 NSFilePresenter 通訊協定和寫入。
這麼做的最簡單方式就是不要自己撰寫，但是子類別 UIDocument 會為您完成這一切。

有兩種方法，您必須在 UIDocument 子類別中執行，才能使用 iCloud：

- **LoadFromContents** -傳入檔案內容的以外 nsdata，以供您在模型類別中解壓縮。

- **ContentsForType** -要求您提供模型類別的以外 nsdata 標記法，以儲存至磁片（和雲端）。

來自**iCloudUIDoc\MonkeyDocument.cs**的這個範例程式碼會顯示如何執行 UIDocument。

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

在此情況下，資料模型非常簡單-單一文字欄位。 您的資料模型可以視需要複雜，例如 Xml 檔或二進位資料。 UIDocument 執行的主要角色是在您的模型類別和可以在磁片上儲存/載入的以外 nsdata 標記法之間進行轉譯。

### <a name="finding-and-opening-icloud-documents"></a>尋找和開啟 iCloud 檔

範例應用程式只會處理單一檔案-test .txt-因此， **AppDelegate.cs**中的程式碼會建立`NSPredicate`並`NSMetadataQuery`特別尋找該檔案名。 會以非同步方式執行，並在完成時傳送通知。`NSMetadataQuery` `DidFinishGathering`會由通知觀察者呼叫，停止查詢並呼叫 LoadDocument，這會使用`UIDocument.Open`方法搭配完成處理常式來嘗試載入檔案，並將它顯示`MonkeyDocumentViewController`在中。

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

### <a name="displaying-icloud-documents"></a>顯示 iCloud 檔

顯示 UIDocument 不應該與其他任何模型類別的任何不同-屬性會顯示在 UI 控制項中，可能是由使用者編輯，然後寫回模型。

在範例**iCloudUIDoc\MonkeyDocumentViewController.cs**中，會在中顯示 MonkeyDocument `UITextView`文字。 `ViewDidLoad`接聽`MonkeyDocument.LoadFromContents`方法中所傳送的通知。 `LoadFromContents`當 iCloud 有檔案的新資料時呼叫，因此通知會指出檔已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

範例程式碼通知處理常式會呼叫方法來更新 UI，在此情況下不會偵測到任何衝突或解決問題。

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>正在儲存 iCloud 檔

若要將 UIDocument 新增至 iCloud，您`UIDocument.Save`可以直接呼叫（僅適用于新檔），或使用`NSFileManager.DefaultManager.SetUbiquitious`來移動現有的檔案。 範例程式碼會在無所不在容器中，使用此程式碼直接建立新的檔（這裡有兩個完成處理常式`Save` ，一個用於作業，另一個用於開啟）：

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

檔的後續變更不會直接「儲存」，而是會告訴`UIDocument`它已使用`UpdateChangeCount`變更，而且它會自動排程「儲存至磁片」作業：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 檔

使用者可以透過 [設定]，在應用程式外部的 [無所不在容器] 的 [**檔**] 目錄中管理 iCloud 檔;他們可以查看檔案清單，並將它滑動以刪除。 應用程式程式碼應該能夠處理使用者刪除檔的情況。 不要將內部應用程式資料儲存在**Documents**目錄中。

 [![](introduction-to-icloud-images/icloudstorage.png "管理 iCloud 檔工作流程")](introduction-to-icloud-images/icloudstorage.png#lightbox)

當使用者嘗試從其裝置移除已啟用 iCloud 的應用程式時，也會收到不同的警告，以通知他們與該應用程式相關的 iCloud 檔狀態。

 [![](introduction-to-icloud-images/icloud-delete1.png "當使用者嘗試從其裝置移除已啟用 iCloud 的應用程式時的範例對話方塊")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "當使用者嘗試從其裝置移除已啟用 iCloud 的應用程式時的範例對話方塊")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 備份

雖然備份至 iCloud 並不是由開發人員直接存取的功能，但您設計應用程式的方式可能會影響使用者體驗。
Apple 提供[Ios 資料儲存指導方針](https://developer.apple.com/icloud/documentation/data-storage/)，供開發人員在其 iOS 應用程式中遵循。

最重要的考慮是您的應用程式是否儲存不是由使用者產生的大型檔案（例如，在每個問題中儲存一百美元的內容的雜誌讀取器應用程式）。 Apple 偏好您不要儲存這類資料，其中將會備份至 iCloud，並不必要地填滿使用者的 iCloud 配額。

儲存大量資料（如這類）的應用程式，應該將它儲存在未備份的其中一個使用者目錄中（例如， 快取或 tmp）， `NSFileManager.SetSkipBackupAttribute`或用來將旗標套用至這些檔案，讓 iCloud 在備份作業期間忽略這些檔案。

## <a name="summary"></a>總結

本文引進了 iOS 5 隨附的新 iCloud 功能。 它會檢查將您的專案設定為使用 iCloud 所需的步驟，然後提供如何執行 iCloud 功能的範例。

索引鍵/值儲存範例示範如何使用 iCloud 來儲存少量資料，類似儲存 NSUserPreferences 的方式。 UIDocument 範例示範如何透過 iCloud 來儲存及同步處理多個裝置上的複雜資料。

最後，它包含了有關新增 iCloud 備份如何影響應用程式設計的簡短討論。

## <a name="related-links"></a>相關連結

- [ICloud 簡介（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/introductiontoicloud)
- [iCloud 研討會範例程式碼](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研討會](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 儲存體](https://support.apple.com/kb/HT4847)
