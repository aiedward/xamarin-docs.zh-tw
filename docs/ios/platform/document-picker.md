---
title: Xamarin 中的檔選擇器
description: 本檔說明 iOS 檔選擇器, 並討論如何在 Xamarin 中使用它。 它會探討 iCloud、檔、一般設定程式碼、檔提供者擴充功能等等。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: cd38facb62c5864f1c933611d8d9dcda94589066
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528235"
---
# <a name="document-picker-in-xamarinios"></a>Xamarin 中的檔選擇器

檔選擇器可讓您在應用程式之間共用檔。 這些檔可能會儲存在 iCloud 或不同的應用程式目錄中。 檔會透過使用者已安裝在其裝置上的一組[檔提供者延伸](~/ios/platform/extensions.md)模組來共用。 

由於在應用程式和雲端之間保持檔同步的困難, 因此會造成一定的複雜性。

## <a name="requirements"></a>需求

需要下列專案, 才能完成本文中顯示的步驟:

- **Xcode 7 和 ios 8 或更新版本**– Apple 的 Xcode 7 和 ios 8 或更新版本的 api 必須在開發人員的電腦上安裝及設定。
- **Visual Studio 或 Visual Studio for Mac** –應該安裝最新版本的 Visual Studio for Mac。
- **Ios 裝置**–執行 ios 8 或更新版本的 ios 裝置。

## <a name="changes-to-icloud"></a>ICloud 的變更

若要執行檔選擇器的新功能, Apple 的 iCloud 服務已進行下列變更:

- ICloud 守護程式已使用 CloudKit 完全重寫。
- 現有的 iCloud 功能已重新命名為 iCloud 磁片磁碟機。
- 已將 Microsoft Windows OS 的支援新增到 iCloud。
- 已在 Mac OS 搜尋工具中新增 iCloud 資料夾。
- iOS 裝置可以存取 Mac OS iCloud 資料夾的內容。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="what-is-a-document"></a>什麼是檔？

參考 iCloud 中的檔時, 它是單一的獨立實體, 而且應該被使用者察覺。 使用者可能會想要修改檔或與其他使用者共用 (例如, 使用電子郵件)。

使用者會立即將數種類型的檔案辨識為檔, 例如頁面、專題或數位檔案。 不過, iCloud 並不限於此概念。 例如, 遊戲的狀態 (例如, 國際象棋比賽) 可視為檔, 並儲存在 iCloud 中。 此檔案可能會在使用者的裝置之間傳遞, 並可讓他們在不同裝置上離開的遊戲中收取。

## <a name="dealing-with-documents"></a>處理檔

在深入探討使用檔選擇器搭配 Xamarin 所需的程式碼之前, 本文將討論使用 iCloud 檔的最佳作法, 以及對支援檔選擇器所需的現有 Api 所做的幾項修改。

### <a name="using-file-coordination"></a>使用檔案協調

因為可以從數個不同的位置修改檔案, 所以必須使用協調來防止資料遺失。

 [![](document-picker-images/image1.png "使用檔案協調")](document-picker-images/image1.png#lightbox)

讓我們看一下上面的圖例:

1. 使用「檔案協調」的 iOS 裝置會建立新的檔, 並將它儲存到 iCloud 資料夾。
2. iCloud 會將修改過的檔案儲存至雲端, 以散發到每部裝置。
3. 附加的 Mac 會在 iCloud 資料夾中看到修改過的檔案, 並使用檔案協調將變更複製到檔案中。
4. 未使用檔案協調的裝置會對檔案進行變更, 並將它儲存到 iCloud 資料夾。 這些變更會立即複寫到其他裝置。

假設原始的 iOS 裝置或 Mac 正在編輯檔案, 現在它們的變更會遺失, 並以未經協調裝置的檔案版本加以覆寫。 為避免資料遺失, 檔案協調是使用雲端式檔時的一種必須。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument`藉由為開發人員`NSDocument`執行所有繁重的工作, 讓事情變得簡單 (或在 macOS 上)。 它提供與背景佇列的內建檔案協調, 以防止封鎖應用程式的 UI。

 `UIDocument`公開多個高階 Api, 讓 Xamarin 應用程式的開發工作得以達到開發人員所需的任何目的。

下列程式碼會建立的`UIDocument`子類別, 以執行可用於儲存和抓取 iCloud 文字的一般文字型檔:

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

當`GenericTextDocument`您在 Xamarin iOS 8 應用程式中使用檔選擇器和外部檔時, 將會在本文中使用上面所提供的類別。

## <a name="asynchronous-file-coordination"></a>非同步檔案協調

iOS 8 透過新的檔案協調 Api 提供了數個新的非同步檔案協調功能。 在 iOS 8 之前, 所有現有的檔案協調 Api 都是完全同步的。 這表示開發人員負責執行自己的背景佇列, 以防止檔案協調封鎖應用程式的 UI。

新`NSFileAccessIntent`的類別包含指向檔案的 URL, 以及用來控制所需之協調類型的數個選項。 下列程式碼示範如何使用意圖, 將檔案從一個位置移至另一個位置:

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

探索和列出檔的方式是使用現有`NSMetadataQuery`的 api。 本節將涵蓋新增至`NSMetadataQuery`的新功能, 讓您更輕鬆地使用檔。

### <a name="existing-behavior"></a>現有的行為

在 iOS 8 之前, `NSMetadataQuery`拾取本機檔案變更的速度很慢, 例如: 刪除、建立和重新命名。

 [![](document-picker-images/image2.png "NSMetadataQuery 本機檔案變更總覽")](document-picker-images/image2.png#lightbox)

在上圖中:

1. 對於已經存在於應用程式容器中的檔案`NSMetadataQuery` , 會`NSMetadata`有預先建立和多工緩衝處理的現有記錄, 讓應用程式立即可供使用。
1. 應用程式會在應用程式容器中建立新的檔案。
1. 在看到應用程式容器`NSMetadataQuery`的修改並建立必要`NSMetadata`的記錄之前, 會有延遲。


由於建立`NSMetadata`記錄的延遲, 應用程式必須開啟兩個數據源: 一個用於本機檔案變更, 另一個用於雲端式變更。

### <a name="stitching"></a>縫合

在 iOS 8 中`NSMetadataQuery` , 使用稱為「裝訂」的新功能, 可讓您更輕鬆地直接使用:

 [![](document-picker-images/image3.png "具有新功能的 NSMetadataQuery, 稱為「裝訂」")](document-picker-images/image3.png#lightbox)

使用上圖中的 [裝訂]:

1. 如前所述, 對於已經存在於應用程式容器中的檔案, `NSMetadataQuery`會有`NSMetadata`預先建立和多工緩衝處理的現有記錄。
1. 應用程式會使用檔案協調在應用程式容器中建立新的檔案。
1. 應用程式容器中的勾點會看到修改, `NSMetadataQuery`並呼叫來建立`NSMetadata`必要的記錄。
1. `NSMetadata`記錄是直接在檔案之後建立, 並可供應用程式使用。


藉由使用裝訂應用程式, 不再需要開啟資料來源來監視本機和以雲端為基礎的檔案變更。 現在, 應用程式可以`NSMetadataQuery`直接依賴。

> [!IMPORTANT]
> 只有在應用程式使用如上一節所示的檔案協調時, 才會進行裝訂。 如果未使用檔案協調, Api 會預設為現有的 iOS 8 行為。




### <a name="new-ios-8-metadata-features"></a>新的 iOS 8 中繼資料功能

IOS 8 中的下列新功能已`NSMetadataQuery`新增至:

- `NSMetatadataQuery`現在可以列出儲存在雲端中的非本機檔。
- 已新增 Api 以存取雲端架構檔上的中繼資料資訊。 
- 有一個新`NSUrl_PromisedItems`的 API, 可存取檔案的檔案屬性, 這些檔案可能或可能沒有其內容可在本機上使用。
- 使用方法來取得指定檔案的相關資訊, 或`GetPromisedItemResourceValues`使用方法一次取得一個以上的檔案資訊。 `GetPromisedItemResourceValue`


已新增兩個新的檔案協調旗標來處理中繼資料:

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
- `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


使用上述旗標, 檔檔案的內容不需要在本機提供, 即可供使用。

下列程式碼片段顯示如何使用`NSMetadataQuery`來查詢特定檔案是否存在, 並建立檔案 (如果不存在的話):

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
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
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

Apple 認為列出應用程式檔的最佳使用者體驗是使用預覽。 這會提供終端使用者內容, 讓他們可以快速地識別他們想要使用的檔。

在 iOS 8 之前, 會顯示檔預覽, 需要自訂的執行。 IOS 8 的新手是檔案系統屬性, 可讓開發人員快速使用檔縮圖。

#### <a name="retrieving-document-thumbnails"></a>正在抓取檔縮圖 

藉由呼叫`GetPromisedItemResourceValue`或`GetPromisedItemResourceValues`方法, `NSUrl_PromisedItems`會傳回 API。 `NSUrlThumbnailDictionary` 目前在此字典中的唯一索引鍵`NSThumbnial1024X1024SizeKey`是和其`UIImage`相符的。

#### <a name="saving-document-thumbnails"></a>儲存檔縮圖

儲存縮圖最簡單的方式是使用`UIDocument`。 藉由呼叫`GetFileAttributesToWrite`的方法`UIDocument`並設定縮圖, 它會在檔檔案為時自動儲存。 ICloud Daemon 會看到這種變更, 並將其傳播到 iCloud。 在 Mac OS X 上, 快速查閱外掛程式會自動為開發人員產生縮圖。

有了使用 iCloud 檔的基本概念, 以及對現有 API 的修改, 我們已準備好在 Xamarin iOS 8 行動裝置應用程式中執行檔選擇器視圖控制器。


## <a name="enabling-icloud-in-xamarin"></a>在 Xamarin 中啟用 iCloud

您必須在應用程式中和透過 Apple 啟用 iCloud 支援, 才能在 Xamarin iOS 應用程式中使用檔選擇器。 

下列步驟將逐步解說布建 iCloud 的程式。

1. 建立 iCloud 容器。
2. 建立包含 iCloud App Service 的應用程式識別碼。
3. 建立包含此應用程式識別碼的布建設定檔。

[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南會逐步解說前兩個步驟。 若要建立布建設定檔, 請遵循布建[設定檔](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device)指南中的步驟。



下列步驟會逐步解說為 iCloud 設定應用程式的過程:

請執行下列動作：

1. 在 Visual Studio for Mac 或 Visual Studio 中開啟專案。
2. 在 **方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 選項。
3. 在 [選項] 對話方塊中, 選取 [ **IOS 應用程式**], 確定 [套件組合**識別碼**] 符合針對應用程式所建立的 [**應用**程式識別碼] 中所定義的套件組合。 
4. 選取 [IOS 套件組合**簽署**], 並選取 [**開發人員身分識別**] 和上面建立的布建**設定檔**。
5. 按一下 [**確定]** 按鈕以儲存變更並關閉對話方塊。
6. 在 **方案總管**中`Entitlements.plist` , 以滑鼠右鍵按一下 開啟, 在編輯器中開啟它。

    > [!IMPORTANT]
    > 在 Visual Studio 您可能需要以滑鼠右鍵按一下 [權利] 編輯器, 選取 [**開啟方式**...] 並選取 [屬性清單編輯器]

7. 勾選 [**啟用 icloud** , **icloud 檔**, 機**碼值儲存**和**CloudKit** ]。
8. 請確定應用程式的**容器**存在 (如上面所建立)。 範例：`iCloud.com.your-company.AppName`
9. 將變更儲存到檔案。

如需權利的詳細資訊, 請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

在上述設定就緒之後, 應用程式現在可以使用雲端式檔和新的檔選擇器視圖控制器。

## <a name="common-setup-code"></a>一般設定程式碼

開始使用檔選擇器視圖控制器之前, 需要一些標準設定程式碼。 一開始先修改應用程式`AppDelegate.cs`的檔案, 讓它看起來如下所示:

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
> 上述程式碼包含上述 [探索和列出檔] 區段中的程式碼。 它會完整顯示在這裡, 因為它會出現在實際的應用程式中。 為了簡單起見, 此範例僅適用于單一、硬式編碼的`test.txt`檔案 ()。

上述程式碼會公開數個 iCloud 磁片磁碟機快捷方式, 讓它們更容易在應用程式的其餘部分中使用。

接下來, 將下列程式碼新增至將使用檔選擇器或使用雲端式檔的任何 view 或 view 容器:

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

這會新增一個快捷方式, 以`AppDelegate`取得並存取上述所建立的 iCloud 快捷方式。

在此程式碼準備就緒之後, 讓我們看看如何在 Xamarin iOS 8 應用程式中執行檔選擇器視圖控制器。

## <a name="using-the-document-picker-view-controller"></a>使用檔選擇器視圖控制器

在 iOS 8 之前, 很難以從另一個應用程式存取檔, 因為無法從應用程式內探索應用程式外部的檔。

### <a name="existing-behavior"></a>現有的行為

 [![](document-picker-images/image31.png "現有的行為總覽")](document-picker-images/image31.png#lightbox)

讓我們看看如何存取 iOS 8 之前的外部檔:

1. 首先, 使用者必須開啟原先建立檔的應用程式。
1. 檔會被選取, 而且`UIDocumentInteractionController`會用來將檔傳送至新的應用程式。
1. 最後, 原始檔案的複本會放在新應用程式的容器中。


檔可供第二個應用程式開啟和編輯。

### <a name="discovering-documents-outside-of-an-apps-container"></a>探索應用程式容器外的檔

在 iOS 8 中, 應用程式可以輕鬆地存取自己的應用程式容器外的檔:

 [![](document-picker-images/image32.png "探索應用程式容器外的檔")](document-picker-images/image32.png#lightbox)

使用新的 iCloud 檔選擇器`UIDocumentPickerViewController`(), iOS 應用程式可以直接在其應用程式容器外部探索和存取。 `UIDocumentPickerViewController`提供一種機制, 讓使用者透過許可權授與和編輯這些探索到的檔。

應用程式必須加入宣告, 才能在 iCloud 檔選擇器中顯示其檔, 並可供其他應用程式探索及使用。 若要讓 Xamarin iOS 8 應用程式共用其應用程式容器, `Info.plist`請在標準文字編輯器中編輯它, 並將下列兩行新增至字典的底部 (在`<dict>...</dict>`標記之間):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供了絕佳的新 UI, 可讓使用者選擇檔。 若要在 Xamarin iOS 8 應用程式中顯示檔選擇器視圖控制器, 請執行下列動作:

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
> 開發人員必須先呼叫`StartAccessingSecurityScopedResource`的方法, `NSUrl`才能存取外部檔。 您必須呼叫方法, 以便在檔載入後立即釋放安全性鎖定。 `StopAccessingSecurityScopedResource`

### <a name="sample-output"></a>範例輸出

以下是在 iPhone 裝置上執行時, 上述程式碼如何顯示檔選擇器的範例:

1. 使用者啟動應用程式, 並顯示主要介面:   
 
    [![](document-picker-images/image33.png "會顯示主要介面")](document-picker-images/image33.png#lightbox)
1. 使用者會在畫面頂端按 [**動作**] 按鈕, 並要求您從可用的提供者清單中選取**檔提供者**:   
 
    [![](document-picker-images/image34.png "從可用的提供者清單中選取檔提供者")](document-picker-images/image34.png#lightbox)
1. 針對選取的**檔提供者**, 會顯示 [**檔選擇器視圖控制器**]:   
 
    [![](document-picker-images/image35.png "隨即顯示 [檔選擇器] 視圖控制器")](document-picker-images/image35.png#lightbox)
1. 使用者可以在**檔資料夾**上點擊以顯示其內容:   
 
    [![](document-picker-images/image36.png "檔資料夾內容")](document-picker-images/image36.png#lightbox)
1. 使用者選取**檔**, 並關閉**檔選擇器**。
1. 主要介面會重新顯示, 從外部容器載入**檔**及其內容。


檔選擇器視圖控制器的實際顯示取決於使用者在裝置上安裝的檔提供者, 以及已執行的檔選擇器模式。 上述範例使用 [開啟] 模式, 以下將詳細討論其他模式類型。

## <a name="managing-external-documents"></a>管理外部檔

如先前所述, 在 iOS 8 之前, 應用程式只能存取其應用程式容器中的檔。 在 iOS 8 中, 應用程式可以從外部來源存取檔:

 [![](document-picker-images/image37.png "管理外部檔總覽")](document-picker-images/image37.png#lightbox)

當使用者從外部來源選取檔時, 會將參考檔寫入指向原始檔案的應用程式容器。

為了協助將這項新功能新增到現有的`NSMetadataQuery`應用程式中, API 已新增數個新功能。 一般而言, 應用程式會使用普遍的檔範圍來列出其應用程式容器內的檔。 使用此範圍時, 只會繼續顯示應用程式容器內的檔。

使用新的通用外部檔範圍將會傳回應用程式容器外的檔, 並傳回它們的中繼資料。 `NSMetadataItemUrlKey`會指向檔實際所在的 URL。

有時候應用程式不想使用第一個參考所指向的檔。 相反地, 應用程式想要直接使用參考檔。 例如, 應用程式可能會想要在 UI 中的應用程式資料夾中顯示檔, 或允許使用者在資料夾內移動參考。

在 iOS 8 中, 已`NSMetadataItemUrlInLocalContainerKey`提供新的來直接存取參考檔。 此機碼會指向應用程式容器中外部檔的實際參考。

`NSMetadataUbiquitousItemIsExternalDocumentKey`是用來測試檔案是否在應用程式的容器外部。 `NSMetadataUbiquitousItemContainerDisplayNameKey`是用來存取存放外部檔之原始複本的容器名稱。

### <a name="why-document-references-are-required"></a>為什麼需要檔參考

IOS 8 使用參考存取外部檔的主要原因是安全性。 沒有任何應用程式可存取任何其他應用程式的容器。 只有檔選擇器可以這樣做, 因為是跨進程執行, 而且具有系統範圍的存取權。

若要取得應用程式容器外的檔, 唯一的方法是使用檔選擇器, 如果選擇器所傳回的 URL 是安全性範圍, 則為。 安全性範圍 URL 只包含足夠的資訊來選取檔, 以及授與應用程式存取檔所需的限定範圍許可權。

請務必注意, 如果安全性範圍的 URL 已序列化為字串, 然後再進行還原序列化, 安全性資訊將會遺失, 且無法從 URL 存取該檔案。 檔參考功能會提供一種機制, 讓您回到這些 Url 所指向的檔案。

因此, 如果應用程式`NSUrl`從其中一個參考檔取得, 它已經附加安全性範圍, 而且可以用來存取檔案。 基於這個理由, 強烈建議開發人員使用`UIDocument` , 因為它會處理所有這項資訊和處理常式。

### <a name="using-bookmarks"></a>使用書籤

您不一定可以列舉應用程式的檔以回到特定檔, 例如, 在執行狀態還原時。 iOS 8 提供一種機制, 可建立直接以指定檔為目標的書簽。

下列程式碼會從`UIDocument`的`FileUrl`屬性建立書簽:

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

現有的書簽 API 可用來針對現有`NSUrl`的建立書簽, 可以儲存並載入, 以提供對外部檔案的直接存取權。 下列程式碼將還原先前建立的書簽:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>開啟 vs。匯入模式和檔選擇器

檔選擇器視圖控制器具有兩種不同的作業模式:

1. **開啟模式**–在此模式中, 當使用者選取和外部檔時, 檔選擇器會在應用程式容器中建立安全性範圍的書簽。   
 
    [![](document-picker-images/image37.png "應用程式容器中的安全性範圍書簽")](document-picker-images/image37.png#lightbox)
1. 匯**入模式**–在此模式中, 當使用者選取和外部檔時, 檔選擇器將不會建立書簽, 而是會將檔案複製到暫存位置, 並在此位置提供應用程式存取權:   
 
    [![](document-picker-images/image38.png "檔選擇器會將檔案複製到暫存位置, 並提供應用程式存取此位置的檔")](document-picker-images/image38.png#lightbox)   
 一旦應用程式因任何原因而終止, 暫存位置就會清空, 並移除該檔案。 如果應用程式需要維護檔案的存取權, 則應該建立複本, 並將它放在其應用程式容器中。


當應用程式想要與另一個應用程式共同作業, 並與該應用程式共用對檔所做的任何變更時, [開啟] 模式非常有用。 當應用程式不想要與其他應用程式共用檔的修改時, 會使用匯入模式。

## <a name="making-a-document-external"></a>將檔設為外部

如先前所述, iOS 8 應用程式無法存取其本身應用程式容器以外的容器。 應用程式可以在本機寫入其本身的容器或暫存位置, 然後使用特殊檔案模式, 將所產生的檔移到應用程式容器之外的使用者選擇位置。

若要將檔移到外部位置, 請執行下列動作:

1. 首先, 在本機或暫存位置中建立新的檔。
1. `NSUrl`建立指向新檔的。
1. 開啟新的檔選擇器視圖控制器, 並使用`NSUrl`的`MoveToService`模式將它傳遞給它。 
1. 一旦使用者選擇新的位置, 檔就會從其目前的位置移至新的位置。
1. 參考檔將會寫入應用程式的應用程式容器, 讓建立應用程式仍可存取該檔案。


下列程式碼可以用來將檔移到外部位置:`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

上述程式所傳回的參考檔, 與檔選擇器的 [開啟] 模式所建立的內容完全相同。 不過, 有時候應用程式可能會想要移動檔, 而不需要保留其參考。

若要在`ExportToService`不產生參考的情況下移動檔, 請使用模式。 範例：`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

使用`ExportToService`模式時, 會將檔案複製到外部容器, 而現有的複本會保留在其原始位置。

## <a name="document-provider-extensions"></a>檔提供者延伸模組

在 iOS 8 中, Apple 希望終端使用者能夠存取其任何雲端式檔, 而不論其實際存在的位置。 為了達到此目標, iOS 8 提供了新的檔提供者延伸模組機制。

### <a name="what-is-a-document-provider-extension"></a>什麼是檔提供者延伸模組？

簡單地說, 檔提供者延伸模組是一種方法, 可讓開發人員或協力廠商提供應用程式替代檔儲存體, 其存取方式與現有 iCloud 儲存位置完全相同。

使用者可以從檔選擇器中選取其中一個替代的儲存位置, 而且它們可以使用完全相同的存取模式 (開啟、匯入、移動或匯出) 來處理該位置中的檔案。

這會使用兩個不同的擴充功能來執行:

- **檔選擇器延伸**模組– `UIViewController`提供一個子類別, 可提供圖形化介面讓使用者從替代儲存位置選擇檔。 這個子類別會顯示為檔選擇器視圖控制器的一部分。
- 檔案**提供延伸**模組–這是非 UI 延伸模組, 可處理實際提供檔案內容。 這些延伸模組是透過檔案協調 ( `NSFileCoordinator` ) 提供。 這是需要檔案協調的另一個重要案例。


下圖顯示使用檔提供者延伸模組時的一般資料流程:

 [![](document-picker-images/image39.png "下圖顯示使用檔提供者延伸模組時的一般資料流程")](document-picker-images/image39.png#lightbox)

會發生下列進程:

1. 應用程式會呈現檔選擇器控制器, 可讓使用者選取要使用的檔案。
1. 使用者選取替代檔案位置, 並呼叫自訂`UIViewController`延伸模組來顯示使用者介面。
1. 使用者從這個位置選取一個檔案, 然後將該 URL 傳回到檔選擇器。
1. 檔選擇器會選取檔案的 URL, 並將它傳回給應用程式供使用者使用。
1. URL 會傳遞至檔案協調器, 以將檔案內容傳回到應用程式。
1. 檔案協調器會呼叫自訂檔案提供者延伸模組來取出檔案。
1. 檔案的內容會傳回給檔案協調器。
1. 檔案的內容會傳回給應用程式。


### <a name="security-and-bookmarks"></a>安全性和書簽

本節將快速查看透過書簽的安全性和持續性檔案存取如何與檔提供者延伸模組搭配運作。 不同于 iCloud 檔提供者, 它會自動將安全性和書簽儲存至應用程式容器, 檔提供者延伸模組不是檔參考系統的一部分。

例如: 在提供專屬全公司安全資料存放區的企業設定中, 系統管理員不想要公用 iCloud 伺服器存取或處理機密的公司資訊。 因此, 無法使用內建的檔參考系統。

書簽系統仍然可以使用, 而且檔案提供者延伸模組必須負責正確地處理已加入書簽的 URL, 並傳回它所指向之檔的內容。

基於安全考慮, iOS 8 具有隔離層, 可保存哪些應用程式可存取檔案提供者內哪個識別碼的相關資訊。 請注意, 所有檔案存取都是由這個隔離層控制。

下圖顯示使用書簽和檔提供者延伸模組時的資料流程:

 [![](document-picker-images/image40.png "此圖表顯示使用書簽和檔提供者延伸模組時的資料流程")](document-picker-images/image40.png#lightbox)

會發生下列進程:

1. 應用程式即將進入背景, 而且必須保存其狀態。 它會`NSUrl`呼叫來建立替代儲存體中檔案的書簽。
1. `NSUrl`呼叫檔案提供者延伸模組, 以取得檔的持續性 URL。 
1. 檔案提供者延伸會以字串形式傳回的`NSUrl` URL。
1. 會`NSUrl`將 URL 組合成書簽, 並將其傳回到應用程式。
1. 當應用程式在背景 awakes, 而且需要還原狀態時, 它會將書簽傳遞至`NSUrl` 。
1. `NSUrl`以檔案的 URL 呼叫檔案提供者延伸模組。
1. 副檔名提供者會存取檔案, 並將檔案的位置傳回到`NSUrl` 。
1. 檔案位置會與安全性資訊配套, 並傳回給應用程式。


從這裡開始, 應用程式可以存取該檔案, 並正常使用它。

### <a name="writing-files"></a>寫入檔案

本節將快速介紹如何將檔案寫入具有檔提供者延伸模組的替代位置。 IOS 應用程式會使用「檔案協調」將資訊儲存至應用程式容器內的磁片。 成功寫入檔案之後, 檔案提供者延伸模組將會收到變更的通知。

此時, 檔案提供者延伸模組可以開始將檔案上傳到替代位置 (或將檔案標示為已變更, 並要求上傳)。

### <a name="creating-new-document-provider-extensions"></a>建立新的檔提供者延伸模組

建立新的檔提供者延伸模組不在這篇簡介文章的範圍內。 這裡提供這項資訊, 以顯示, 根據使用者在其 iOS 裝置中載入的擴充功能, 應用程式可能可以存取 Apple 提供的 iCloud 位置以外的檔儲存位置。

開發人員在使用檔選擇器並使用外部檔時, 應該要注意這一點。 它們不應假設這些檔是在 iCloud 中主控。

如需有關建立存放裝置提供者或檔選擇器擴充功能的詳細資訊, 請參閱[應用程式擴充功能簡介](~/ios/platform/extensions.md)檔。

## <a name="migrating-to-icloud-drive"></a>正在遷移至 iCloud 磁片磁碟機

在 iOS 8 上, 使用者可以選擇繼續使用 iOS 7 (和先前的系統) 中使用的現有 iCloud 檔案系統, 或者可以選擇將現有檔遷移至新的 iCloud 磁片磁碟機機制。

在 Mac OS X Yosemite 上, Apple 不提供回溯相容性, 因此所有檔都必須遷移至 iCloud 磁片磁碟機, 否則將無法在裝置上進行更新。

將使用者的帳戶遷移至 iCloud 磁片磁碟機之後, 只有使用 iCloud 磁片磁碟機的裝置才能將變更傳播到這些裝置上的檔。

> [!IMPORTANT]
> 開發人員應該注意, 只有在使用者的帳戶已遷移至 iCloud 磁片磁碟機時, 本文中所涵蓋的新功能才可供使用。 

## <a name="summary"></a>總結

本文涵蓋支援 iCloud 磁片磁碟機和新檔選擇器視圖控制器所需的現有 iCloud Api 變更。 其涵蓋檔案協調, 以及使用雲端式檔時的重要性。 其中涵蓋了在 Xamarin iOS 應用程式中啟用雲端式檔所需的設定, 並提供使用檔選擇器視圖控制器在應用程式容器外使用檔的簡介外觀。

此外, 本文簡要介紹了檔提供者延伸模組, 以及開發人員撰寫可處理雲端式檔的應用程式時, 應該要注意的原因。

## <a name="related-links"></a>相關連結

- [DocPicker (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-docpicker)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [應用程式擴充功能簡介](~/ios/platform/extensions.md)
