---
title: Xamarin 中的檔選擇器
description: 本檔說明 iOS 檔選擇器，並討論如何在 Xamarin 中使用它。 它會查看 iCloud、檔、通用安裝程式碼、檔提供者延伸模組等等。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: c8cffc9593b6de059a877654994e6747204ce34d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435401"
---
# <a name="document-picker-in-xamarinios"></a>Xamarin 中的檔選擇器

檔選擇器可讓您在應用程式之間共用檔。 這些檔可能儲存在 iCloud 或不同應用程式的目錄中。 檔是透過使用者已安裝在其裝置上的 [檔提供者延伸](~/ios/platform/extensions.md) 模組集來共用。

因為在應用程式和雲端之間保持檔同步處理的困難，所以會導致特定數量的必要複雜度。

## <a name="requirements"></a>需求

若要完成本文中所述的步驟，必須具備下列專案：

- **Xcode 7 和 ios 8 或更新版本** –必須在開發人員的電腦上安裝和設定 Apple 的 Xcode 7 和 ios 8 或更新的 api。
- **Visual Studio 或 Visual Studio for Mac** –應該安裝最新版本的 Visual Studio for Mac。
- **Ios 裝置** –執行 ios 8 或更新版本的 ios 裝置。

## <a name="changes-to-icloud"></a>ICloud 的變更

若要執行檔選擇器的新功能，已對 Apple 的 iCloud 服務進行下列變更：

- ICloud Daemon 已使用 CloudKit 完全重寫。
- 現有的 iCloud 功能已重新命名為 iCloud 磁片磁碟機。
- 已將 Microsoft Windows 作業系統的支援新增至 iCloud。
- 在 Mac OS 搜尋工具中新增了 iCloud 資料夾。
- iOS 裝置可以存取 Mac OS iCloud 資料夾的內容。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="what-is-a-document"></a>什麼是檔？

在 iCloud 中參考檔時，它是單一的獨立實體，而且應該會由使用者察覺。 使用者可能會想要使用電子郵件來修改檔或與其他使用者共用 (，例如) 。

有數種類型的檔案可讓使用者立即辨識為檔，例如頁面、演說或數位檔案。 不過，iCloud 不限於此概念。 例如，您可以將遊戲的狀態 (（例如國際象棋相符的) ）視為檔並儲存在 iCloud 中。 此檔案可在使用者的裝置之間傳遞，並可讓他們在不同的裝置上，挑選他們離開的遊戲。

## <a name="dealing-with-documents"></a>處理檔

在深入探討搭配 Xamarin 使用檔選擇器所需的程式碼之前，本文將說明使用 iCloud 檔的最佳作法，以及對支援檔選擇器所需的現有 Api 所做的幾項修改。

### <a name="using-file-coordination"></a>使用檔案協調

因為可以從數個不同的位置修改檔案，所以必須使用協調來防止資料遺失。

 [![使用檔案協調](document-picker-images/image1.png)](document-picker-images/image1.png#lightbox)

讓我們看看上圖：

1. 使用檔案協調的 iOS 裝置會建立新的檔，並將它儲存到 iCloud 資料夾。
2. iCloud 將修改過的檔案儲存至雲端，以散發到每部裝置。
3. 附加的 Mac 會看到 iCloud 資料夾中修改過的檔案，並使用檔案協調將變更複製到檔案。
4. 未使用檔案協調的裝置會對檔案進行變更，並將它儲存到 iCloud 資料夾。 這些變更會立即複寫到其他裝置。

假設原始的 iOS 裝置或 Mac 正在編輯檔案，現在其變更會遺失，並以不協調裝置的檔案版本覆寫。 為了避免資料遺失，檔案協調在使用雲端式檔時必須是。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument` 藉 `NSDocument` 由執行開發人員的所有繁重工作，使 (或在 macOS) 上進行的工作變得簡單許多。 它提供與背景佇列的內建檔案協調，以防止封鎖應用程式的 UI。

 `UIDocument` 公開多個高階 Api，可針對開發人員所需的任何目的，簡化 Xamarin 應用程式的開發工作。

下列程式碼會建立的子類別， `UIDocument` 以執行可用於儲存及取出 iCloud 的文字的一般文字檔：

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

`GenericTextDocument`當您在 Xamarin. iOS 8 應用程式中使用檔選擇器和外部檔時，將會在本文中使用上面提供的類別。

## <a name="asynchronous-file-coordination"></a>非同步檔案協調

iOS 8 透過新的檔案協調 Api 提供數個新的非同步檔案協調功能。 在 iOS 8 之前，所有現有的檔案協調 Api 都是完全同步的。 這代表開發人員必須負責執行自己的背景佇列，以防止檔案協調封鎖應用程式的 UI。

新 `NSFileAccessIntent` 類別包含指向檔案的 URL，以及用來控制所需協調類型的數個選項。 下列程式碼示範如何使用意圖將檔案從一個位置移到另一個位置：

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>探索和列出檔

探索和列出檔的方式是使用現有的 `NSMetadataQuery` api。 本節將討論新增至的新功能 `NSMetadataQuery` ，讓您可以比以往更輕鬆地使用檔。

### <a name="existing-behavior"></a>現有的行為

在 iOS 8 之前， `NSMetadataQuery` 取貨本機檔案的變更速度很慢，例如：刪除、建立和重新命名。

 [![NSMetadataQuery 本機檔案變更總覽](document-picker-images/image2.png)](document-picker-images/image2.png#lightbox)

在上圖中：

1. 對於已存在於應用程式容器中的檔案，  `NSMetadataQuery` 已  `NSMetadata` 預先建立現有的記錄和多工緩衝處理，讓應用程式可以立即使用這些記錄。
1. 應用程式會在應用程式容器中建立新的檔案。
1. 在  `NSMetadataQuery` 查看應用程式容器的修改之前，會有延遲，並建立必要的  `NSMetadata` 記錄。

由於記錄的建立延遲 `NSMetadata` ，應用程式必須開啟兩個數據源：一個用於本機檔案變更，另一個用於雲端式變更。

### <a name="stitching"></a>拼接

在 iOS 8 中， `NSMetadataQuery` 可以更輕鬆地直接使用稱為「拼接」的新功能：

 [![使用稱為「拼接」的新功能 NSMetadataQuery](document-picker-images/image3.png)](document-picker-images/image3.png#lightbox)

在上圖中使用拼接：

1. 同樣地，對於已經存在於應用程式容器中的檔案，  `NSMetadataQuery` 已  `NSMetadata` 預先建立和多工緩衝處理的現有記錄。
1. 應用程式會在應用程式容器中使用檔案協調來建立新的檔案。
1. 應用程式容器中的勾點會看到修改和呼叫  `NSMetadataQuery` ，以建立所需的  `NSMetadata` 記錄。
1. `NSMetadata`記錄會直接在檔案之後建立，並可供應用程式使用。

藉由使用拼接應用程式，不再需要開啟資料來源來監視本機和以雲端為基礎的檔案變更。 現在應用程式可以直接依賴 `NSMetadataQuery` 。

> [!IMPORTANT]
> 只有在應用程式使用檔案協調時（如上一節所示），才能進行拼接。 如果未使用檔案協調，則 Api 會預設為現有的 iOS 8 之前行為。

### <a name="new-ios-8-metadata-features"></a>新的 iOS 8 中繼資料功能

IOS 8 中新增了下列新功能 `NSMetadataQuery` ：

- `NSMetatadataQuery` 現在可以列出儲存在雲端的非本機檔。
- 新增了新的 Api，以存取雲端式檔的中繼資料資訊。
- 有一個新的  `NSUrl_PromisedItems` API，將會存取檔案的檔案屬性，這些檔案可能或可能不會在本機提供其內容。
- 您  `GetPromisedItemResourceValue` 可以使用方法來取得指定檔案的相關資訊，或使用  `GetPromisedItemResourceValues` 方法，一次取得多個檔案的相關資訊。

已新增兩個新的檔案協調旗標來處理中繼資料：

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly`
- `NSFileCoordinatorWriteContentIndependentMetadataOnly`

使用上述旗標時，檔檔的內容不需要在本機使用，就可以使用。

下列程式碼片段會示範如何使用 `NSMetadataQuery` 來查詢特定檔案是否存在，並建立檔案（如果不存在）：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

#region Static Properties
public const string TestFilename = "test.txt";
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),             NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>檔縮圖

Apple 認為，在列出應用程式的檔時，最佳使用者體驗是使用預覽。 這會提供使用者內容，讓他們可以快速地識別要使用的檔。

在 iOS 8 之前，顯示檔預覽需要自訂的執行。 IOS 8 的新手是檔案系統屬性，可讓開發人員快速處理檔縮圖。

#### <a name="retrieving-document-thumbnails"></a>正在抓取檔縮圖

藉由呼叫 `GetPromisedItemResourceValue` 或 `GetPromisedItemResourceValues` 方法， `NSUrl_PromisedItems` `NSUrlThumbnailDictionary` 會傳回 API。 此字典中目前唯一的索引鍵是 `NSThumbnial1024X1024SizeKey` 和其相符的 `UIImage` 。

#### <a name="saving-document-thumbnails"></a>儲存檔縮圖

儲存縮圖最簡單的方式是使用 `UIDocument` 。 藉由呼叫的 `GetFileAttributesToWrite` 方法 `UIDocument` 並設定縮圖，就會在檔檔為時自動儲存。 ICloud Daemon 會看到此變更，並將其傳播到 iCloud。 在 Mac OS X 上，快速流覽外掛程式會自動為開發人員產生縮圖。

有了使用 iCloud 檔的基本概念，以及對現有 API 的修改，我們已準備好在 Xamarin iOS 8 行動應用程式中執行檔選擇器查看控制器。

## <a name="enabling-icloud-in-xamarin"></a>在 Xamarin 中啟用 iCloud

您必須在應用程式中以及透過 Apple 啟用 iCloud 支援，才能在 Xamarin 應用程式中使用檔選擇器。

下列步驟會逐步解說 iCloud 的布建程式。

1. 建立 iCloud 容器。
2. 建立包含 iCloud App Service 的應用程式識別碼。
3. 建立包含此應用程式識別碼的布建設定檔。

[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南會逐步解說前兩個步驟。 若要建立布建設定檔，請遵循「布建 [設定檔](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) 指南」中的步驟。

下列步驟會逐步解說為 iCloud 設定應用程式的程式：

執行下列動作：

1. 在 Visual Studio for Mac 中開啟專案或 Visual Studio。
2. 在 [ **方案總管**中，以滑鼠右鍵按一下專案，然後選取 [選項]。
3. 在 [選項] 對話方塊中，選取 [ **IOS 應用程式**]，確定套件組合識別碼與上面針對應用程式所建立的**應用**程式識別碼中所定義的套件組合**識別碼**相符。
4. 選取 [IOS 套件組合 **簽署**]，選取 **開發人員身分識別** 和上面建立的布建 **設定檔** 。
5. 按一下 [ **確定]** 按鈕以儲存變更並關閉對話方塊。
6. 以滑鼠右鍵按一下 `Entitlements.plist` **方案總管** ，以在編輯器中開啟它。

    > [!IMPORTANT]
    > 在 Visual Studio 您可能需要以滑鼠右鍵按一下 [權利編輯器]，然後選取 [**開啟方式 ...** ]，開啟 [權利編輯器]。 和選取屬性清單編輯器

7. 檢查  **啟用 icloud** 、  **icloud 檔** 、機  **碼值儲存體** 和  **CloudKit** 。
8. 請確定應用程式 (的 **容器** 存在於上述) 上建立。 範例：`iCloud.com.your-company.AppName`
9. 將變更儲存至檔案。

如需有關權利的詳細資訊，請參閱使用 [權利](~/ios/deploy-test/provisioning/entitlements.md) 指南。

設定好上述設定之後，應用程式現在可以使用雲端式檔和新的檔選擇器 View 控制器。

## <a name="common-setup-code"></a>一般安裝程式碼

開始使用檔選擇器 View 控制器之前，需要一些標準的設定程式碼。 先修改應用程式的檔案 `AppDelegate.cs` ，使其看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt";
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                 new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }

        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");    

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {    
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();

        }

        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }

        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }

        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }

        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> 上述程式碼包含上述探索和列出檔區段中的程式碼。 它會在整個中顯示，因為它會出現在實際的應用程式中。 為了簡單起見，此範例僅適用于單一的硬式編碼檔案 (`test.txt`) 。

上述程式碼會公開數個 iCloud 磁片磁碟機快捷方式，讓您更輕鬆地在應用程式的其餘部分使用。

接下來，將下列程式碼新增至將使用檔選擇器或使用雲端式檔的任何 view 或 view 容器：

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

這會加入快捷方式，以 `AppDelegate` 存取並存取上方所建立的 iCloud 快捷方式。

當此程式碼備妥後，讓我們看看如何在 Xamarin iOS 8 應用程式中執行檔選擇器查看控制器。

## <a name="using-the-document-picker-view-controller"></a>使用檔選擇器 View 控制器

在 iOS 8 之前，很難從另一個應用程式存取檔，因為無法從應用程式中探索應用程式外部的檔。

### <a name="existing-behavior"></a>現有的行為

 [![現有的行為總覽](document-picker-images/image31.png)](document-picker-images/image31.png#lightbox)

讓我們看看如何存取 iOS 8 之前的外部檔：

1. 首先，使用者必須開啟最初建立檔的應用程式。
1. 檔會被選取，並且  `UIDocumentInteractionController` 用來將檔傳送至新的應用程式。
1. 最後，原始檔案的複本會放在新的應用程式容器中。

您可以從該處取得可供第二個應用程式開啟和編輯的檔。

### <a name="discovering-documents-outside-of-an-apps-container"></a>探索應用程式容器之外的檔

在 iOS 8 中，應用程式可以輕鬆地存取自己的應用程式容器之外的檔：

 [![探索應用程式容器之外的檔](document-picker-images/image32.png)](document-picker-images/image32.png#lightbox)

使用新的 iCloud 檔選擇器 ( `UIDocumentPickerViewController`) ，iOS 應用程式可以直接在其應用程式容器之外探索及存取。 `UIDocumentPickerViewController`提供一種機制，讓使用者透過許可權授與存取權，並編輯這些探索到的檔。

應用程式必須選擇讓其檔顯示在 iCloud 檔選擇器中，並且可供其他應用程式探索及使用。 若要讓 Xamarin iOS 8 應用程式共用其應用程式容器，請 `Info.plist` 在標準文字編輯器中編輯該檔案，然後將下列兩行新增至) 標記之間的字典 (的底部 `<dict>...</dict>` ：

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供絕佳的新 UI，讓使用者可以選擇檔。 若要在 Xamarin iOS 8 應用程式中顯示 [檔選擇器] 查看控制器，請執行下列動作：

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> 開發人員必須先呼叫的 `StartAccessingSecurityScopedResource` 方法， `NSUrl` 才能存取外部檔。 您 `StopAccessingSecurityScopedResource` 必須呼叫方法，以便在檔載入後立即釋放安全性鎖定。

### <a name="sample-output"></a>範例輸出

以下是在 iPhone 裝置上執行時，上述程式碼如何顯示檔選擇器的範例：

1. 使用者啟動應用程式，並顯示主要介面：   

    [![主要介面隨即顯示](document-picker-images/image33.png)](document-picker-images/image33.png#lightbox)
1. 使用者按一下畫面頂端的 [ **動作** ] 按鈕，系統會要求您從可用提供者的清單中選取 **檔提供者** ：   

    [![從可用提供者清單中選取檔提供者](document-picker-images/image34.png)](document-picker-images/image34.png#lightbox)
1. 選取的**檔提供者**會顯示 [**檔選擇器] View 控制器**：   

    [![[檔選擇器] View 控制器隨即顯示](document-picker-images/image35.png)](document-picker-images/image35.png#lightbox)
1. 使用者可以按 **檔資料夾** 來顯示其內容：   

    [![檔資料夾內容](document-picker-images/image36.png)](document-picker-images/image36.png#lightbox)
1. 使用者選取 **檔** ，並關閉 **檔選擇器** 。
1. 主要介面會重新顯示，從外部容器載入 **檔** ，以及顯示其內容。

檔選擇器 View 控制器的實際顯示取決於使用者已安裝在裝置上的檔提供者，以及已執行的檔選擇器模式。 上述範例使用開放式模式，以下將詳細討論其他模式類型。

## <a name="managing-external-documents"></a>管理外部檔

如先前所述，在 iOS 8 之前，應用程式只能存取其應用程式容器中的檔。 在 iOS 8 中，應用程式可以從外部來源存取檔：

 [![管理外部檔總覽](document-picker-images/image37.png)](document-picker-images/image37.png#lightbox)

當使用者選取外部來源的檔時，會將參考檔寫入指向原始檔案的應用程式容器。

為了協助將這項新功能新增至現有的應用程式，API 已新增數項新功能 `NSMetadataQuery` 。 一般而言，應用程式會使用廣泛的檔範圍來列出應用程式容器內的檔。 使用此範圍時，只會繼續顯示應用程式容器內的檔。

使用新的通用外部檔範圍將會傳回存留于應用程式容器之外的檔，並傳回這些檔的中繼資料。 `NSMetadataItemUrlKey`會指向檔實際所在的 URL。

有時候，應用程式不會想要使用第一個參考所指向的檔。 相反地，應用程式會想要直接使用參考檔。 例如，應用程式可能會想要在 UI 中的應用程式資料夾中顯示檔，或允許使用者在資料夾內移動參考。

在 iOS 8 中，已提供新的， `NSMetadataItemUrlInLocalContainerKey` 可直接存取參考檔。 此索引鍵指向應用程式容器中外部檔的實際參考。

`NSMetadataUbiquitousItemIsExternalDocumentKey`用來測試檔案是否在應用程式的容器之外。 `NSMetadataUbiquitousItemContainerDisplayNameKey`用來存取裝載外部檔之原始複本的容器名稱。

### <a name="why-document-references-are-required"></a>為什麼需要檔參考

IOS 8 使用參考存取外部檔的主要原因是安全性。 沒有任何應用程式可存取任何其他應用程式的容器。 只有檔選擇器可以執行這項作業，因為它是跨進程執行，而且具有系統範圍的存取權。

若要取得應用程式容器外部的檔，唯一的方法是使用檔選擇器，而且如果選擇器所傳回的 URL 具有安全性範圍，則為。 安全性範圍的 URL 只包含足夠的資訊來選取檔，以及授與應用程式存取檔所需的範圍許可權。

請務必注意，如果安全性範圍的 URL 已序列化為字串，然後再進行還原序列化，則會遺失安全性資訊，而且無法從 URL 存取檔案。 檔參考功能提供一種機制，可取回這些 Url 所指向的檔案。

因此，如果應用程式 `NSUrl` 從其中一個參考檔取得，則它已附加安全性範圍，並可用於存取檔案。 基於這個理由，強烈建議開發人員使用， `UIDocument` 因為它會處理這些資訊和流程。

### <a name="using-bookmarks"></a>使用書籤

列舉應用程式的檔，以取得特定檔（例如，在執行狀態還原時），並不一定可行。 iOS 8 提供一種機制，可建立直接以指定檔為目標的書簽。

下列程式碼會從的屬性建立書簽 `UIDocument` `FileUrl` ：

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

現有的書簽 API 是用來針對現有的書簽建立書簽 `NSUrl` ，您可以儲存和載入該書簽，以提供外部檔案的直接存取。 下列程式碼會還原先前建立的書簽：

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>開啟 vs. 匯入模式和檔選擇器

檔選擇器 View 控制器具有兩種不同的作業模式：

1. **開啟模式** –在此模式中，當使用者選取和外部檔時，檔選擇器會在應用程式容器中建立安全性範圍的書簽。   

    [![應用程式容器中的安全性範圍書簽](document-picker-images/image37.png)](document-picker-images/image37.png#lightbox)
1. 匯**入模式**：在此模式中，當使用者選取和外部檔時，檔選擇器不會建立書簽，而會改為將檔案複製到暫存位置，並提供應用程式在這個位置的存取權：   

    [![檔選擇器會將檔案複製到暫存位置，並提供應用程式存取此位置的檔](document-picker-images/image38.png)](document-picker-images/image38.png#lightbox)   
 應用程式因為任何原因而終止時，會清空暫存位置並移除該檔案。 如果應用程式需要維護檔案的存取權，則應建立複本，並將它放在其應用程式容器中。

當應用程式想要與另一個應用程式共同作業，並與該應用程式共用對檔所做的任何變更時，開啟模式會很有用。 當應用程式不想與其他應用程式共用檔的修改時，會使用匯入模式。

## <a name="making-a-document-external"></a>將檔設為外部

如上所述，iOS 8 應用程式無法存取其本身應用程式容器以外的容器。 應用程式可以在本機或暫存位置寫入自己的容器，然後使用特殊的檔案模式，將產生的檔移至應用程式容器之外，移至使用者選擇的位置。

若要將檔移至外部位置，請執行下列動作：

1. 首先在本機或暫存位置建立新檔。
1. 建立  `NSUrl` 指向新檔的。
1. 開啟新的檔選擇器 View 控制器，並使用的模式將它傳遞給它  `NSUrl` `MoveToService` 。
1. 當使用者選擇新的位置時，檔將會從目前的位置移至新的位置。
1. 參考檔將會寫入至應用程式的應用程式容器，讓建立的應用程式仍然可以存取該檔案。

下列程式碼可以用來將檔移至外部位置： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

上述進程所傳回的參考檔，與檔選擇器的開啟模式所建立的參考檔完全相同。 不過，有時候應用程式可能會想要移動檔，而不需要保留它的參考。

若要移動檔而不產生參考，請使用 `ExportToService` 模式。 範例：`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

使用模式時 `ExportToService` ，會將檔案複製到外部容器，而現有的複本會保留在原始位置。

## <a name="document-provider-extensions"></a>檔提供者延伸模組

在 iOS 8 中，Apple 希望終端使用者能夠存取其任何雲端式檔，而不論其實際位於何處。 為了達成此目標，iOS 8 提供新的檔提供者擴充機制。

### <a name="what-is-a-document-provider-extension"></a>什麼是檔提供者延伸模組？

簡單地說，檔提供者延伸模組是一種可讓開發人員或協力廠商提供的應用程式替代檔儲存體，可透過與現有 iCloud 儲存位置完全相同的方式來存取。

使用者可以從檔選擇器選取其中一個替代的儲存位置，而且可以使用完全相同的存取模式 (開啟、匯入、移動或匯出) ，以使用該位置中的檔案。

這會使用兩個不同的擴充功能來執行：

- **檔選擇器擴充** 功能-提供一個子  `UIViewController` 類別，可提供圖形化介面讓使用者從替代儲存位置選擇檔。 此子類別會顯示為檔選擇器 View 控制器的一部分。
- 檔案**提供延伸**模組–這是一種非 UI 延伸模組，可處理實際提供檔案內容的方式。 這些延伸模組是透過檔案協調 ( `NSFileCoordinator` ) 來提供。 這是需要檔案協調的另一個重要案例。

下圖顯示使用檔提供者延伸模組時的一般資料流程：

 [![下圖顯示使用檔提供者延伸模組時的一般資料流程](document-picker-images/image39.png)](document-picker-images/image39.png#lightbox)

將會發生下列進程：

1. 應用程式會提供檔選擇器控制器，讓使用者選取要使用的檔案。
1. 使用者選取替代檔案位置，並呼叫自訂  `UIViewController` 延伸模組來顯示使用者介面。
1. 使用者從這個位置選取檔案，並將 URL 傳回到檔選擇器。
1. 檔選擇器會選取檔案的 URL，並將它傳回給應用程式供使用者使用。
1. URL 會傳遞至檔案協調器，以將檔案內容傳回到應用程式。
1. 檔案協調器會呼叫自訂檔案提供者延伸模組，以取得檔案。
1. 檔案的內容會傳回至檔案協調器。
1. 檔案的內容會傳回給應用程式。

### <a name="security-and-bookmarks"></a>安全性和書簽

本節將快速查看透過書簽來存取的安全性和持續性檔案如何搭配檔提供者延伸模組使用。 與 iCloud 檔提供者不同的是，它會自動將安全性和書簽儲存至應用程式容器，而不是檔提供者延伸模組，因為它們不是檔參考系統的一部分。

例如：在提供專屬全公司安全資料存放區的企業設定中，系統管理員不希望公開 iCloud 伺服器存取或處理機密的公司資訊。 因此，無法使用內建的檔參考系統。

書簽系統仍然可以使用，而且檔案提供者延伸模組必須負責正確地處理加上書簽的 URL，並傳回它所指向之檔的內容。

基於安全性考慮，iOS 8 有隔離層，可保存哪些應用程式可存取哪些檔案提供者內的識別碼的相關資訊。 請注意，所有檔案存取都是由這個隔離層控制。

下圖顯示使用書簽和檔提供者延伸模組時的資料流程：

 [![下圖顯示使用書簽和檔提供者延伸模組時的資料流程](document-picker-images/image40.png)](document-picker-images/image40.png#lightbox)

將會發生下列進程：

1. 應用程式即將進入背景，而且需要保存其狀態。 它會呼叫 `NSUrl` 以在替代儲存體中建立檔案的書簽。
1. `NSUrl` 呼叫檔案提供者延伸模組，以取得檔的持續性 URL。
1. 檔案提供者延伸模組會以字串的形式傳回 URL `NSUrl` 。
1. `NSUrl`會將 URL 組合成書簽，然後將它傳回給應用程式。
1. 當應用程式在背景中 awakes，且需要還原狀態時，它會將書簽傳遞給 `NSUrl` 。
1. `NSUrl` 以檔案的 URL 呼叫檔案提供者延伸模組。
1. 副檔名提供者會存取檔案，並將檔案的位置傳回 `NSUrl` 。
1. 檔案位置會與安全性資訊配套，並傳回給應用程式。

從這裡開始，應用程式可以存取檔案，並正常使用它。

### <a name="writing-files"></a>寫入檔案

本節將快速瞭解如何使用檔提供者延伸模組將檔案寫入替代位置。 IOS 應用程式會使用檔案協調將資訊儲存至應用程式容器內的磁片。 在成功寫入檔案之後不久，檔案提供者延伸模組將會收到變更的通知。

此時，檔案提供者延伸模組可以開始將檔案上傳到替代位置 (或將檔案標示為已變更，並需要上傳) 。

### <a name="creating-new-document-provider-extensions"></a>建立新的檔提供者延伸模組

建立新的檔提供者延伸模組不在這篇簡介文章的範圍內。 此處提供這項資訊以顯示，根據使用者在其 iOS 裝置中載入的擴充功能，應用程式可能可以存取 Apple 提供的 iCloud 位置以外的檔儲存位置。

當您使用檔選擇器並使用外部檔時，開發人員應該注意這一點。 它們不應假設這些檔是以 iCloud 裝載。

如需有關建立儲存區提供者或檔選擇器擴充功能的詳細資訊，請參閱 [應用程式擴充](~/ios/platform/extensions.md) 功能檔的簡介。

## <a name="migrating-to-icloud-drive"></a>正在遷移至 iCloud 磁片磁碟機

在 iOS 8 上，使用者可以選擇繼續使用 iOS 7 (和先前系統) 中使用的現有 iCloud 檔案系統，也可以選擇將現有的檔遷移至新的 iCloud 磁片磁碟機機制。

在 Mac OS X Yosemite 上，Apple 不會提供回溯相容性，因此所有檔都必須遷移至 iCloud 磁片磁碟機，否則不會再跨裝置更新。

將使用者的帳戶遷移到 iCloud 磁片磁碟機之後，只有使用 iCloud 磁片磁碟機的裝置才能將變更傳播至這些裝置上的檔。

> [!IMPORTANT]
> 開發人員應該注意，本文所涵蓋的新功能只有在使用者的帳戶已遷移至 iCloud 磁片磁碟機時才可供使用。

## <a name="summary"></a>摘要

本文涵蓋了支援 iCloud 磁片磁碟機的現有 iCloud Api，以及新的檔選擇器視圖控制器所需的變更。 它涵蓋檔案協調，以及使用雲端式檔時的重要原因。 它涵蓋了在 Xamarin iOS 應用程式中啟用雲端式檔所需的設定，並介紹使用檔選擇器視圖控制器在應用程式的應用程式容器外部使用檔的簡介。

此外，這篇文章簡要說明了檔提供者延伸模組，以及開發人員在撰寫可處理雲端式檔的應用程式時，應如何察覺這些延伸模組。

## <a name="related-links"></a>相關連結

- [DocPicker (範例) ](/samples/xamarin/ios-samples/ios8-docpicker)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)