---
title: 與 Xamarin.iOS 使用 iCloud
description: 本文件說明 iCloud 與 Xamarin.iOS 應用程式中的使用。 它討論索引鍵-值存放區、 文件儲存體和 iCloud 備份。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: 009e061726f655999c08192b5839a5c962d35e24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091407"
---
# <a name="using-icloud-with-xamarinios"></a>與 Xamarin.iOS 使用 iCloud

在 iOS 5 中的 iCloud 儲存體 API 可讓應用程式，將使用者文件和應用程式專屬資料儲存到中央位置，然後從所有使用者的裝置存取這些項目。

有可用的四種類型的儲存體：

- **索引鍵-值存放區**-共用上的少量資料，您的應用程式使用者的其他裝置。

- **UIDocument 儲存體**-若要將文件和其他資料儲存在使用者的 iCloud 帳戶使用的 UIDocument 子類別。

- **CoreData** -SQLite 資料庫儲存體。

- **個別的檔案和目錄**-直接在檔案系統中管理許多不同的檔案。

本文件討論的前兩個類型-索引鍵 / 值組以及 UIDocument 子類別-以及如何在 Xamarin.iOS 中使用這些功能。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

- 最新穩定版本的 Xamarin.iOS
- Xcode 10
- Visual Studio for Mac 或 Visual Studio 2019。

## <a name="preparing-for-icloud-development"></a>準備 iCloud 開發

應用程式必須設定為使用在 iCloud [Apple Provisioning Portal](https://developer.apple.com/account/ios/overview.action)和專案本身。 之前開發的 iCloud （或試用範例），遵循下列步驟。

若要正確設定應用程式存取 iCloud:

-   **尋找您 TeamID** -登入[developer.apple.com](https://developer.apple.com)並瀏覽**Member Center > 您的帳戶 > 開發人員帳戶摘要**以取得您小組的 ID （或適用於單一開發人員的個別識別碼). 它會是 10 個字元的字串 ( **A93A5CM278**例如)-這會形成 「 容器識別碼 」 的一部分。

-   **建立新的應用程式識別碼**-若要建立應用程式識別碼，請依照下列所述的步驟[佈建存放區技術區段的 裝置佈建的快速入門](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)，並務必**iCloud**為允許的服務：

 [![](introduction-to-icloud-images/icloud-sml.png "為允許服務的核取 iCloud")](introduction-to-icloud-images/icloud.png#lightbox)

- **建立新的佈建設定檔**-若要建立佈建設定檔，請依照下列所述的步驟[裝置佈建指南](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device)。

- **將容器識別碼新增至 Entitlements.plist** -是容器識別碼格式`TeamID.BundleID`。 如需詳細資訊請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- **設定專案屬性**-在 Info.plist 檔案確保**套件組合識別碼**符合**套件組合識別碼**時，設定[建立應用程式識別碼](~/ios/deploy-test/provisioning/capabilities/index.md);使用 iOS 套件組合簽署**佈建設定檔**包含 iCloud App Service 應用程式識別碼並**自訂權利**選取檔案。 這全都可以完成在 Visual Studio [專案屬性] 窗格底下。

- **啟用您的裝置上的 iCloud** -前往**設定 > iCloud** ，並確定裝置已登入。
選取並開啟**文件和資料**選項。

- **您必須使用裝置來測試 iCloud** -不適用於模擬器。
事實上，您真的需要兩個或多個裝置所有登入相同的 Apple ID，才能查看 iCloud 作用中。


## <a name="key-value-storage"></a>索引鍵-值存放區

索引鍵-值存放區適用於少量的使用者可能想保存跨裝置-例如他們檢視書本或雜誌中的最後一頁的資料。 索引鍵-值存放區不應用於備份資料。

有一些限制需要注意時使用索引鍵 / 值儲存體：

- **最大金鑰大小**-索引鍵名稱不能超過 64 個位元組。

- **最大值大小**-您無法在單一值中儲存超過 64 kb。

- **應用程式的最大索引鍵-值存放區大小**-應用程式可以只儲存總計最多 64 kb 的索引鍵-值資料。 嘗試設定超過該限制的索引鍵將會失敗，並將保存先前的值。

- **資料型別**-只有基本類型像是字串、 數字，而且可以儲存布林值。

**ICloudKeyValue**範例將示範其運作方式。 範例程式碼會建立名為每個裝置的金鑰： 您可以在一部裝置上設定此機碼，並觀看傳播到其他的值。 它也會建立金鑰，稱為 「 共用 」，可在任何裝置上編輯，如果您一次編輯許多裝置上，iCloud 會決定哪一個值"wins"（使用時間戳記的變更），並取得傳播。

此螢幕擷取畫面顯示使用中的範例。 從 iCloud 收到變更通知時它們會印在捲動文字檢視螢幕的底部，輸入欄位中更新。



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "裝置之間的訊息流程")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>設定和擷取資料

此程式碼示範如何設定字串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

呼叫同步處理，可確保值會保存到本機磁碟儲存體。 同步處理到 icloud 的功能會在背景執行，並無法 「 強制 」 應用程式程式碼。 使用良好的網路連線同步處理通常會在 5 秒內，不過，如果網路不佳 （或已中斷連線） 更新可能需要更長的時間。

您可以擷取此程式碼的值：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

本機資料存放區擷取的值-此方法不會嘗試連絡 iCloud 伺服器，以取得 「 最新 」 的值。 iCloud 會更新本機資料存放區，根據自己的排程。

### <a name="deleting-data"></a>刪除資料

若要完全移除的索引鍵 / 值組，請使用 Remove 方法，就像這樣：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>觀察變更

應用程式也可以在變更新增至觀察者的 iCloud 值時也收到通知`NSNotificationCenter.DefaultCenter`。
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

您的程式碼可以採取某些動作的清單已變更的索引鍵，例如更新的本機副本，或使用新值更新 UI。

可能的變更原因如下：ServerChange (0)、 InitialSyncChange (1) 或 QuotaViolationChange (2)。 您可以存取的原因，並在必要時，執行不同的處理 (例如，您可能需要移除的某些索引鍵*QuotaViolationChange*)。

## <a name="document-storage"></a>文件儲存體

iCloud 文件儲存體被設計來管理您的應用程式 （和使用者） 的重要資料。 它可用來管理檔案和其他應用程式執行，而是在同時提供 iCloud 為基礎的備份，並在 所有使用者的裝置之間共用功能所需的資料。

下圖顯示這一切如何相互配合。 每個裝置都儲存在本機儲存體 (UbiquityContainer) 和作業系統的 iCloud 服務精靈會負責傳送和接收資料，在雲端中的資料。 所有的檔案存取權 UbiquityContainer 必須透過 FilePresenter/FileCoordinator 以防止並行存取。 `UIDocument`類別會實作的您，此範例示範如何使用 UIDocument。

 [![](introduction-to-icloud-images/icloud-overview.png "文件儲存體概觀")](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 範例實作簡單`UIDocument`包含單一的文字欄位的子類別。 在中呈現的文字`UITextView`及編輯會傳播到其他裝置的 iCloud 以紅色顯示的通知訊息。 範例程式碼不會處理更進階的 iCloud 功能，例如衝突解決。

此螢幕擷取畫面顯示範例應用程式-將文字變更，並按下後的**UpdateChangeCount**透過 iCloud 與其他裝置同步處理文件。

 [![](introduction-to-icloud-images/iclouduidoc.png "此螢幕擷取畫面顯示範例應用程式，將文字變更，並按下 UpdateChangeCount 之後")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

有五個部分 iCloudUIDoc 範例：

1. **存取 UbiquityContainer** -決定是否啟用 iCloud 時，如果是您的應用程式 iCloud 儲存區的路徑。

1. **建立 UIDocument 子類別**-建立 iCloud 儲存體和模型物件之間居中協調的類別。

1. **尋找及開啟 iCloud 文件**-使用`NSFileManager`和`NSPredicate`尋找 iCloud 文件，並開啟它們。

1. **顯示 iCloud 文件**-從屬性公開 （expose) 您`UIDocument`，讓您可以互動的 UI 控制項。

1. **儲存 iCloud 文件**-請確定在 UI 中所做的變更會保存到磁碟和 iCloud。

ICloud 的所有作業執行 （或應該執行） 以非同步方式，讓它們不會封鎖等候發生某件事。 您會看到三個不同的方式的範例中完成這項作業：

 **執行緒**-在`AppDelegate.FinishedLaunching`初始呼叫`GetUrlForUbiquityContainer`在另一個執行緒，以避免封鎖主執行緒上完成。

 **NotificationCenter** -註冊通知，當非同步作業，例如`NSMetadataQuery.StartQuery`完成。

 **完成處理常式**-傳入方法來完成非同步作業，例如執行`UIDocument.Open`。

### <a name="accessing-the-ubiquitycontainer"></a>存取 UbiquityContainer

使用 iCloud 文件儲存體的第一個步驟是判斷是否已啟用 iCloud，而且，如果 「 無所不在容器 」 的位置 （在裝置儲存 iCloud 功能檔案的目錄）。

此程式碼位於`AppDelegate.FinishedLaunching`方法的範例。

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

雖然此範例不會這麼做，Apple 建議呼叫 GetUrlForUbiquityContainer，每當應用程式則來到前景。

### <a name="creating-a-uidocument-subclass"></a>建立 UIDocument 子類別

所有 iCloud 檔案和目錄 (ie。 儲存在 UbiquityContainer 目錄中的任何項目) 必須使用 NSFileManager 方法，實作 NSFilePresenter 通訊協定和 NSFileCoordinator 透過撰寫管理。
執行所有動作，最簡單方式是不是用來寫入它，不過子類別 UIDocument 這會為您所有。

有您必須在使用 iCloud UIDocument 子類別中實作的只有兩個方法：

- **LoadFromContents** -傳入的檔案的內容，以解壓縮到您模型的類別/es 以外。

- **ContentsForType** -要求您提供您模型的類別/es 以外表示法，將儲存到磁碟 （和雲端）。

此範例程式碼**iCloudUIDoc\MonkeyDocument.cs**示範如何實作 UIDocument。

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

在此情況下的資料模型是很簡單-單一的文字欄位。 您的資料模型可以很複雜，例如 必要項目，例如 Xml 文件或二進位資料。 UIDocument 實作的主要角色是您的模型類別和可以是磁碟上儲存/載入以外表示法之間進行轉譯。

### <a name="finding-and-opening-icloud-documents"></a>尋找及開啟 iCloud 文件

範例應用程式只處理單一檔案-test.txt-所以中的程式碼**AppDelegate.cs**會建立`NSPredicate`和`NSMetadataQuery`特別尋找該檔案名稱。 `NSMetadataQuery`以非同步方式執行，並會在完成時傳送通知。 `DidFinishGathering` 通知觀察器呼叫，會停止查詢並呼叫 LoadDocument，它會使用`UIDocument.Open`方法具有完成處理常式嘗試載入檔案，並顯示在`MonkeyDocumentViewController`。

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

顯示 UIDocument 不得為任何差異，以便在任何其他的模型類別
- 屬性會顯示在 UI 控制項，可能是由使用者編輯和覆寫回至模型。

在範例中**iCloudUIDoc\MonkeyDocumentViewController.cs**顯示 MonkeyDocument 文字`UITextView`。 `ViewDidLoad` 傳送的通知會接聽`MonkeyDocument.LoadFromContents`方法。 `LoadFromContents` 會將 iCloud 檔案的新資料時呼叫，以便通知指出文件已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

範例程式碼通知處理常式呼叫方法，以在此情況下沒有任何衝突的偵測或解決更新 UI。

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

若要加入到 icloud 的功能，您可以呼叫 UIDocument`UIDocument.Save`直接 （適用於新的文件） 或移動現有的檔案使用`NSFileManager.DefaultManager.SetUbiquitious`。 範例程式碼直接無所不在容器中以下列程式碼建立新文件 (有兩個完成處理常式，一個用於`Save`作業，而另一個開啟):

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

文件的後續變更不會 「 儲存 」 直接，相反地，我們告訴`UIDocument`它已變更與`UpdateChangeCount`，以及它將會自動排程儲存至磁碟作業：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 文件

使用者可以管理在 iCloud 文件**文件**目錄"無所不在容器 」 您的應用程式，透過設定; 之外的他們可以檢視檔案清單和撥動以刪除。 應用程式程式碼應該能夠處理這種情況由使用者刪除文件的位置。 不會儲存在內部應用程式資料**文件**目錄。

 [![](introduction-to-icloud-images/icloudstorage.png "管理 iCloud 文件工作流程")](introduction-to-icloud-images/icloudstorage.png#lightbox)



嘗試移除已啟用 iCloud 的應用程式從他們的裝置，以便告知他們 iCloud 文件與該應用程式相關的狀態時，使用者也會收到不同的警告。

 [![](introduction-to-icloud-images/icloud-delete1.png "當使用者嘗試從他們的裝置移除啟用 iCloud 的應用程式的範例對話方塊")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "當使用者嘗試從他們的裝置移除啟用 iCloud 的應用程式的範例對話方塊")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 備份

雖然備份到 iCloud 並不是由開發人員直接存取的功能，您將應用程式設計的方式可能會影響使用者體驗。
Apple 提供[iOS 資料儲存體的指導方針](https://developer.apple.com/icloud/documentation/data-storage/)的開發人員在其 iOS 應用程式追蹤。

最重要的考量是內容的您的應用程式是否會儲存大型檔案不是內容的使用者產生的 （例如 magazine 讀者應用程式，其中儲存的每個問題 hundred-plus mb 為單位）。 Apple 會偏好不會儲存這類資料，它會被備份到 icloud 的功能並不必要地填入使用者的 iCloud 配額。

儲存大量資料的應用程式應該是將它儲存在其中一個備份 （例如不是使用者目錄。 快取或 tmp） 或使用`NSFileManager.SetSkipBackupAttribute`將套用至這些檔案的旗標，以便 iCloud 會略過它們在備份作業期間。

## <a name="summary"></a>總結

本文會介紹 iOS 5 中所包含的新 iCloud 功能。 它會檢查的步驟，才能將專案設定為使用 iCloud 和則提供有關如何實作 iCloud 功能的範例。

索引鍵-值存放區範例示範如何 iCloud 可以用來儲存少量資料 NSUserPreferences 儲存的方式類似。 UIDocument 範例示範了如何更多複雜的資料可儲存及同步處理跨多個裝置透過 iCloud。

最後加入的 iCloud 備份應該如何影響您的應用程式設計的簡短討論包含它。



## <a name="related-links"></a>相關連結

- [簡介至 iCloud （範例）](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [iCloud 研討會範例程式碼](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研討會的投影片](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 儲存體](https://support.apple.com/kb/HT4847)
