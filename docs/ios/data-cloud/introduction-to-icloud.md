---
title: 搭配使用 iCloud 與 Xamarin
description: 本檔說明 iCloud 及其在 Xamarin iOS 應用程式中的使用方式。 其中討論索引鍵/值儲存體、檔儲存體和 iCloud 備份。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/09/2016
ms.openlocfilehash: cace68a1b85a4404774ca88ec697d419920d05cb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432904"
---
# <a name="using-icloud-with-xamarinios"></a>搭配使用 iCloud 與 Xamarin

IOS 5 中的 iCloud 儲存 API 可讓應用程式將使用者檔和應用程式特定資料儲存到中央位置，並從使用者的所有裝置存取這些專案。

有四種可用的儲存體類型：

- 索引**鍵-值儲存體**-在使用者的其他裝置上與您的應用程式共用少量的資料。

- **UIDocument 儲存體** -使用 UIDocument 的子類別，在使用者的 iCloud 帳戶中儲存檔和其他資料。

- **CoreData** -SQLite 資料庫儲存體。

- **個別的檔案和目錄** -可直接在檔案系統中管理許多不同的檔案。

本檔討論前兩種類型-索引鍵/值組和 UIDocument 子類別，以及如何在 Xamarin 中使用這些功能。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

- 最新穩定版本的 Xamarin. iOS
- Xcode 10
- Visual Studio for Mac 或 Visual Studio 2019。

## <a name="preparing-for-icloud-development"></a>準備 iCloud 開發

您必須將應用程式設定為在 Apple 布建 [入口網站](https://developer.apple.com/account/ios/overview.action) 和專案本身中使用 iCloud。 在針對 iCloud (或試用範例之前，) 遵循下列步驟。

若要正確設定應用程式以存取 iCloud：

- **尋找您的 TeamID** -登入以  [Developer.apple.com](https://developer.apple.com) 並造訪  **會員中心 > 您的帳戶 > 開發人員帳戶摘要** ]，以) 的單一開發人員取得您的小組識別碼 (或個別識別碼。 它會是10個字元的字串 ( **A93A5CM278** 例如) -這會形成「容器識別碼」的一部分。

- **建立新的應用程式識別碼** -若要建立應用程式識別碼，請遵循裝置布建指南的「  [存放區技術](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)布建」一節中所述的步驟，並務必檢查 **iCloud** 是否為允許的服務：

 [![檢查 iCloud 是否為允許的服務](introduction-to-icloud-images/icloud-sml.png)](introduction-to-icloud-images/icloud.png#lightbox)

- **建立新** 的布建設定檔-若要建立布建設定檔，請遵循  [裝置](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) 布建指南中所述的步驟。

- **將容器識別碼新增至 plist** -容器識別碼格式為 `TeamID.BundleID` 。 如需詳細資訊，請參閱使用 [權利](~/ios/deploy-test/provisioning/entitlements.md) 指南。

- **設定專案屬性**-在 Info. plist 檔案中，確定**套件組合識別碼符合建立**[應用程式識別碼](~/ios/deploy-test/provisioning/capabilities/index.md)時所設定的套件組合**識別碼**;IOS 套件組合簽署使用的布建**設定檔**包含具有 iCloud App Service 的應用程式識別碼，以及選取的**自訂權利**檔案。 您可以在 [專案屬性] 窗格底下的 Visual Studio 完成這項操作。

- 在**您的裝置上啟用 iCloud** -移至 [**設定] > iCloud** ，並確定裝置已登入。
選取並開啟 [ **檔] & 資料** ] 選項。

- **您必須使用裝置來測試 iCloud** -它將無法在模擬器上運作。
事實上，您真的需要使用相同的 Apple ID 登入兩個以上的裝置，才能看到 iCloud 的作用。

## <a name="key-value-storage"></a>機碼值儲存體

索引鍵-值儲存體適用于少量的資料，使用者可能會喜歡將這些資料保存在不同的裝置上，例如他們在書籍或雜誌中看到的最後一頁。 索引鍵/值儲存體不應用於備份資料。

使用機碼值儲存體時，有一些限制需要注意：

- **金鑰大小上限** -索引鍵名稱不能超過64個位元組。

- **最大值大小** -您無法在單一值中儲存超過 64 kb。

- 應用程式的索引**鍵/值存放區大小上限**-應用程式最多隻能儲存 64 kb 的索引鍵/值資料。 嘗試設定超過該限制的索引鍵將會失敗，且先前的值會保存。

- **資料類型** -只可以儲存字串、數位和布林值等基本類型。

**ICloudKeyValue**範例會示範其運作方式。 此範例程式碼會為每個裝置建立名為的金鑰：您可以在一部裝置上設定此金鑰，並監看此值是否會傳播給其他人。 它也會建立名為「共用」的金鑰，可在任何裝置上進行編輯-如果您一次在許多裝置上編輯，iCloud 將會決定哪個值「獲勝」 (使用變更) 上的時間戳記，並傳播。

此螢幕擷取畫面顯示使用中的範例。 從 iCloud 收到變更通知時，會在畫面底部的滾動文字視圖中列印，並在輸入欄位中更新。

 [![裝置之間的訊息流程](introduction-to-icloud-images/icloud-kv-arrows.png)](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>設定和取出資料

此程式碼說明如何設定字串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

呼叫 Synchronize 可確保值只會保存到本機磁片儲存體。 對 iCloud 的同步處理會在背景中執行，而且應用程式程式碼無法「強制」。 使用良好的網路連線時，通常會在5秒內進行同步處理，不過如果網路 (或中斷連線，) 更新可能需要更長的時間。

您可以使用下列程式碼來取得值：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

此值取自本機資料存放區-此方法不會嘗試聯繫 iCloud 伺服器以取得「最新」值。 iCloud 會根據自己的排程來更新本機資料存放區。

### <a name="deleting-data"></a>刪除資料

若要完全移除索引鍵/值組，請使用如下所示的 Remove 方法：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>觀察變更

當 iCloud 變更值時，應用程式也可以藉由將觀察者新增至來接收通知 `NSNotificationCenter.DefaultCenter` 。
**KeyValueViewController.cs**方法中的下列程式碼示範 `ViewWillAppear` 如何接聽這些通知，以及如何建立已變更金鑰的清單：

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

然後，您的程式碼可以使用變更的索引鍵清單來採取一些動作，例如更新它們的本機複本，或以新的值更新 UI。

可能的變更原因包括： ServerChange (0) 、InitialSyncChange (1) 或 QuotaViolationChange (2) 。 您可以視需要存取原因並執行不同的處理 (例如，您可能需要移除某些索引鍵，因為 *QuotaViolationChange*) 的結果。

## <a name="document-storage"></a>檔儲存體

iCloud 檔儲存體的設計目的是要管理對您的應用程式 (和使用者) 而言很重要的資料。 您可以使用它來管理您的應用程式需要執行的檔案和其他資料，同時在所有使用者的裝置上提供 iCloud 型備份和共用功能。

下圖顯示其如何彼此搭配。 每個裝置都會將資料儲存在本機儲存體 (UbiquityContainer) ，而作業系統的 iCloud Daemon 會負責傳送和接收雲端中的資料。 UbiquityContainer 的所有檔案存取都必須透過 FilePresenter/FileCoordinator 完成，以防止平行存取。 `UIDocument`類別會為您執行這些程式，此範例示範如何使用 UIDocument。

 [![檔儲存體總覽](introduction-to-icloud-images/icloud-overview.png)](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 範例 `UIDocument` 會執行包含單一文字欄位的簡單子類別。 文字是在中轉譯 `UITextView` ，而編輯則是以紅色顯示通知訊息的方式傳播到其他裝置。 範例程式碼不會處理更先進的 iCloud 功能，例如衝突解決。

此螢幕擷取畫面顯示範例應用程式-變更文字並按下 **UpdateChangeCount** 時，檔會透過 iCloud 同步處理到其他裝置。

 [![此螢幕擷取畫面顯示變更文字並按下 UpdateChangeCount 後的範例應用程式](introduction-to-icloud-images/iclouduidoc.png)](introduction-to-icloud-images/iclouduidoc.png#lightbox)

ICloudUIDoc 範例有五個部分：

1. **存取 UbiquityContainer** -判斷是否已啟用 icloud，如果是，則為應用程式 iCloud 儲存區的路徑。

1. **建立 UIDocument 子類別** -建立一個類別，以在 iCloud 儲存體和您的模型物件之間進行中繼。

1. **尋找並開啟 icloud 檔** -使用 `NSFileManager` 和 `NSPredicate` 來尋找 icloud 檔並加以開啟。

1. **顯示 iCloud 檔** -公開的屬性， `UIDocument` 讓您可以與 UI 控制項互動。

1. **儲存 iCloud 檔** -確定在 UI 中所做的變更會保存到磁片和 iCloud。

所有 iCloud 作業都 (執行，或應該以非同步方式) 執行，如此一來，就不會在等候發生問題時進行封鎖。 在範例中，您會看到三種不同的方法來完成這項操作：

 **執行緒** -在 `AppDelegate.FinishedLaunching` 的初始呼叫中， `GetUrlForUbiquityContainer` 會在另一個執行緒上完成，以防止封鎖主執行緒。

 **NotificationCenter** -在非同步作業（例如完成）時註冊通知 `NSMetadataQuery.StartQuery` 。

 **完成處理常式** -傳遞在完成非同步作業（例如）時執行的方法 `UIDocument.Open` 。

### <a name="accessing-the-ubiquitycontainer"></a>存取 UbiquityContainer

使用 iCloud 檔儲存體的第一個步驟是判斷是否已啟用 iCloud，如果是，則「無不入容器」的位置會 (儲存在裝置) 上已啟用 iCloud 之檔案的目錄。

此程式碼位於 `AppDelegate.FinishedLaunching` 範例的方法中。

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

雖然此範例不會這麼做，但當應用程式進入前景時，Apple 建議您呼叫 GetUrlForUbiquityContainer。

### <a name="creating-a-uidocument-subclass"></a>建立 UIDocument 子類別

所有 iCloud 檔案和目錄都 (ie。儲存在 UbiquityContainer 目錄) 中的任何事項都必須使用 NSFileManager 方法來管理，並透過 NSFileCoordinator 來執行 NSFilePresenter 通訊協定和寫入。
最簡單的方法就是不要自行撰寫，但子類別 UIDocument 可為您完成。

在 UIDocument 子類別中，您必須執行兩個方法來使用 iCloud：

- **LoadFromContents** -傳遞檔案內容的以外 nsdata，以解壓縮至您的模型類別/es。

- **ContentsForType** -要求您提供模型類別/Es 的以外 nsdata 標記法，以儲存至磁片 (和雲端) 。

**ICloudUIDoc\MonkeyDocument.cs**中的這個範例程式碼會示範如何執行 UIDocument。

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

在此情況下，資料模型非常簡單-單一文字欄位。 您的資料模型可以視需要複雜，例如 Xml 檔或二進位資料。 UIDocument 執行的主要角色是在您的模型類別與可在磁片上儲存/載入的以外 nsdata 標記法之間進行轉譯。

### <a name="finding-and-opening-icloud-documents"></a>尋找並開啟 iCloud 檔

範例應用程式只會處理單一檔案 test.txt-因此 **AppDelegate.cs** 中的程式碼 `NSPredicate` 會建立，並 `NSMetadataQuery` 特別尋找該檔案名。 `NSMetadataQuery`會以非同步方式執行，並在完成時傳送通知。 `DidFinishGathering` 由通知觀察者呼叫、停止查詢並呼叫 LoadDocument，它會使用方法搭配 `UIDocument.Open` 完成處理常式來嘗試載入檔案，並將它顯示在中 `MonkeyDocumentViewController` 。

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

顯示 UIDocument 不應與任何其他模型類別相同-屬性會顯示在 UI 控制項中，可能會由使用者進行編輯，然後再回寫到模型中。

在範例 **iCloudUIDoc\MonkeyDocumentViewController.cs** 中，會顯示中的 MonkeyDocument 文字 `UITextView` 。 `ViewDidLoad` 接聽方法中所傳送的通知 `MonkeyDocument.LoadFromContents` 。 `LoadFromContents` 當 iCloud 有新的檔案資料時呼叫，讓通知指出檔已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

範例程式碼通知處理常式會呼叫方法來更新 UI-在此情況下，不會有任何衝突偵測或解決。

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

若要將 UIDocument 新增至 iCloud，您可以 `UIDocument.Save` 直接呼叫新檔的 (，) 或使用移動現有的檔案 `NSFileManager.DefaultManager.SetUbiquitious` 。 範例程式碼會使用下列程式碼，直接在無不入容器中建立新檔 (有兩個完成處理常式，一個用於作業， `Save` 另一個用於開啟的) ：

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

檔的後續變更並不會直接「儲存」，而是會告訴 `UIDocument` 它已變更 `UpdateChangeCount` ，且會自動排程儲存至磁片作業：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 檔

使用者可以透過 [設定]，在您的應用程式外部的「無不入容器」的 [ **檔** ] 目錄中管理 iCloud 檔;他們可以查看檔案清單並滑動來刪除。 應用程式程式碼應該能夠處理使用者刪除檔的情況。 請勿將內部應用程式資料儲存在 [ **檔** ] 目錄中。

 [![管理 iCloud 檔工作流程](introduction-to-icloud-images/icloudstorage.png)](introduction-to-icloud-images/icloudstorage.png#lightbox)

當使用者嘗試從其裝置移除啟用 iCloud 的應用程式時，也會收到不同的警告，以通知他們與該應用程式相關的 iCloud 檔狀態。

 [![當使用者嘗試從其裝置移除啟用 iCloud 的應用程式時的範例對話方塊](introduction-to-icloud-images/icloud-delete1.png)](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![當使用者嘗試從其裝置移除啟用 iCloud 的應用程式時的範例對話方塊](introduction-to-icloud-images/icloud-delete2.png)](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 備份

雖然備份至 iCloud 的功能不是開發人員直接存取的功能，但您設計應用程式的方式可能會影響使用者體驗。
Apple 提供 [Ios 資料儲存指導方針](https://developer.apple.com/icloud/documentation/data-storage/) ，讓開發人員在其 ios 應用程式中遵循。

最重要的考慮是您的應用程式是否會儲存非使用者產生的大型檔案 (例如，每個問題的雜誌讀取器應用程式（每個問題都會儲存數百個以上的內容）) 。 Apple 慣用的是，您不會儲存這類資料，並將資料備份到 iCloud，並不必要地填滿使用者的 iCloud 配額。

儲存大量資料的應用程式（例如這類）應該將它儲存在其中一個未備份的使用者目錄中 (例如， 快取或 tmp) 或用 `NSFileManager.SetSkipBackupAttribute` 來將旗標套用至這些檔案，讓 iCloud 在備份作業期間忽略這些檔案。

## <a name="summary"></a>摘要

本文引進了 iOS 5 隨附的新 iCloud 功能。 它會檢查將您的專案設定為使用 iCloud 所需的步驟，然後提供如何執行 iCloud 功能的範例。

索引鍵-值儲存體範例示範如何使用 iCloud 儲存少量的資料，類似于儲存 NSUserPreferences 的方式。 UIDocument 範例顯示如何透過 iCloud 跨多個裝置儲存和同步處理更複雜的資料。

最後，它還會介紹如何加入 iCloud 備份，以影響您的應用程式設計。

## <a name="related-links"></a>相關連結

- [ICloud (範例) 簡介 ](/samples/xamarin/ios-samples/introductiontoicloud)
- [iCloud 研討會範例程式碼](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研討會投影片](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 儲存空間](https://support.apple.com/kb/HT4847)