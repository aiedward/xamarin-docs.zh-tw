---
title: 文件選擇器
description: 文件選擇器檢視控制器授與使用者存取應用程式的沙箱以外的檔案。 它是簡單的機制，共用應用程式之間的文件。 因為使用者可以編輯具有多個應用程式的單一文件，它也可讓更複雜的工作流程。 本文章提供簡介 Xamarin.iOS 應用程式中使用文件選擇器和 iCloud 文件中的變更才能支援它。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: d9b98611c7d269e590ce6fe2ce0270ef71dacf1e
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="document-picker"></a>文件選擇器

_文件選擇器檢視控制器授與使用者存取應用程式的沙箱以外的檔案。它是簡單的機制，共用應用程式之間的文件。因為使用者可以編輯具有多個應用程式的單一文件，它也可讓更複雜的工作流程。本文章提供簡介 Xamarin.iOS 應用程式中使用文件選擇器和 iCloud 文件中的變更才能支援它。_

文件選擇器可讓應用程式之間共用的文件。 在 icloud 的功能或不同的應用程式的目錄中，可能會儲存這些文件。 文件會透過一組共用[文件提供者擴充](~/ios/platform/extensions.md)使用者在其裝置上安裝。 

保留跨應用程式及雲端同步處理的文件的困難，因為它們會導致必要的複雜性數量。

## <a name="requirements"></a>需求

需要下列項目才能完成本文章中呈現的步驟：

-  **Xcode 7 和 iOS 8 或更新版本**– Apple Xcode 7 和 iOS 8 或更新版本的應用程式開發介面需要安裝和設定開發人員的電腦上。
-  **Visual Studio 或 Visual Studio for Mac** – 應該安裝最新版的 Visual Studio for Mac。
-  **iOS 裝置**– iOS 裝置執行 iOS 8 或更新版本。

## <a name="changes-to-icloud"></a>變更至 iCloud

若要實作的新功能的文件選擇器，到 Apple iCloud 服務已進行下列變更：

-  精靈已經完全改寫使用 CloudKit iCloud。
-  現有的 iCloud 的功能已被重新命名磁碟機 icloud 的功能。
-  Microsoft Windows 作業系統的支援已到 icloud 的功能。
-  在 Mac OS Finder 中已加入 iCloud 資料夾。
-  iOS 裝置可以存取的 Mac OS iCloud 資料夾的內容。

> [!IMPORTANT]
> Apple[提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)可協助開發人員適當地處理歐盟的一般資料保護規定 (GDPR)。

## <a name="what-is-a-document"></a>什麼是文件？

參照 icloud 的功能中的文件，它是單一的獨立實體，並應該使用者看得見在這種情況。 使用者可能想要修改文件，或是與其他使用者共用 （透過使用電子郵件，例如）。

有幾種類型的檔案，使用者會立即將視為文件，例如網頁、 Keynote 或數字的檔案。 不過，不限於此概念 icloud 的功能。 比方說，可以視為文件和 iCloud 中儲存的遊戲 （例如 Chess 相符項目） 的狀態。 此檔案傳遞至使用者的裝置之間，並允許其挑選遊戲而中斷的地方不同裝置上。

## <a name="dealing-with-documents"></a>處理文件

之前一頭栽進來使用文件選擇器來搭配 Xamarin 的程式碼，這篇文章已移至涵蓋 iCloud 的文件的最佳作法和數個現有的 api 所做的修改，才能支援文件選擇器。

### <a name="using-file-coordination"></a>使用檔案協調

從數個不同的位置，就可以修改檔案，因為協調必須可用來防止資料遺失。

 [![](document-picker-images/image1.png "使用檔案協調")](document-picker-images/image1.png#lightbox)

讓我們看一下上圖中：

1.  使用檔案協調的 iOS 裝置建立新的文件，並將它儲存至資料夾 icloud 的功能。
2.  icloud 的功能會將發佈至每個裝置的雲端中的已修改的檔案。
3.  附加的 Mac 看到 iCloud 資料夾中已修改的檔案，並使用檔案協調下所做的變更複製到檔案。
4.  不使用檔案協調的裝置對檔案進行變更，並將它儲存至資料夾 icloud 的功能。 這些變更會立即複寫到其他裝置。

假設原始的 iOS 裝置或 Mac 已編輯的檔案，其變更會遺失且未經協調的裝置從檔案的版本覆寫。 若要避免資料遺失，檔案協調時，必須使用以雲端為基礎的文件。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument` 簡化了項目 (或`NSDocument`macOS 上)，開發人員執行所有繁重的工作。 它會提供內建檔案協調與背景佇列，若要防止封鎖應用程式的 UI。

 `UIDocument` 會公開多個需要高層級的任何用途，開發人員簡化了 Xamarin 應用程式的開發工作的 Api。

下列程式碼建立的子類別`UIDocument`實作泛型文字為基礎文件可以用來儲存和擷取文字從 icloud 的功能：

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

`GenericTextDocument`使用文件選擇器和 Xamarin.iOS 8 應用程式中的外部文件時，將整篇文章使用以上所顯示的類別。

## <a name="asynchronous-file-coordination"></a>非同步檔案協調

iOS 8 提供數個新檔案協調 Api 透過新的非同步檔案協調功能。 之前 iOS 8，所有現有的檔案協調 Api 都完全同步的。 這表示開發人員所負責實作自己的背景佇列，以防止檔案協調封鎖應用程式的 UI。

新`NSFileAccessIntent`類別包含的檔案和數個選項來控制協調所需的型別所指向的 URL。 下列程式碼示範如何將檔案從一個位置移到另一個使用方式：

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

## <a name="discovering-and-listing-documents"></a>探索及列出文件

探索並列出的文件的方式是使用現有的`NSMetadataQuery`應用程式開發介面。 本章節將說明如何加入新功能`NSMetadataQuery`，讓使用文件甚至比以前更容易。

### <a name="existing-behavior"></a>現有的行為

在 iOS 8 之前,`NSMetadataQuery`很慢收取的本機檔案變更例如： 刪除、 建立並重新命名。

 [![](document-picker-images/image2.png "NSMetadataQuery 本機檔案變更概觀")](document-picker-images/image2.png#lightbox)

上圖中：

1.  應用程式容器中已存在的檔案`NSMetadataQuery`具有現有`NSMetadata`記錄預先建立，並使其應用程式立即可供多工緩衝處理。
1.  應用程式的應用程式容器中建立新的檔案。
1.  沒有之前的延遲`NSMetadataQuery`會將應用程式容器的修改，並建立必要`NSMetadata`記錄。


因為在建立延遲`NSMetadata`記錄，應用程式必須有兩個資料來源開啟： 一個本機檔案變更，一個用於雲端型的變更。

### <a name="stitching"></a>連結

在 iOS 8，`NSMetadataQuery`更輕鬆地使用直接與稱為連結的新功能：

 [![](document-picker-images/image3.png "與新的功能 NSMetadataQuery 呼叫連結")](document-picker-images/image3.png#lightbox)

使用上圖中的連結：

1.  如往常一般，如應用程式容器中已經存在檔案`NSMetadataQuery`具有現有`NSMetadata`預先建立的記錄，以及多工緩衝處理。
1.  應用程式會使用檔案協調在應用程式容器中建立新的檔案。
1.  修改與呼叫，會看到應用程式容器中的攔截`NSMetadataQuery`建立必要`NSMetadata`記錄。
1.  `NSMetadata`記錄檔案後，直接建立，而且可應用程式。


使用連結的應用程式不再具有開啟資料來源來監視本機和雲端基礎檔案變更。 現在應用程式都可以依賴`NSMetadataQuery`直接。

> [!IMPORTANT]
> 連結只適用於應用程式正在使用檔案協調，如上節所描述。 如果未使用檔案協調，Api 會預設為現有的前 iOS 8 行為。




### <a name="new-ios-8-metadata-features"></a>IOS 8 的中繼資料的新功能

下列新功能已經加入至`NSMetadataQuery`iOS 8 中：

-   `NSMetatadataQuery` 現在列出儲存在雲端中的非本機文件。
-  已加入新的 Api 來存取雲端架構文件上的中繼資料資訊。 
-  新`NSUrl_PromisedItems`API，將會存取檔案的屬性，可能會或可能沒有可用的內容在本機的檔案。
-  使用`GetPromisedItemResourceValue`方法，以取得指定的檔案的相關資訊，或使用`GetPromisedItemResourceValues`方法，一次取得多個檔案的資訊。


處理中繼資料已加入兩個新的檔案協調旗標：

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


使用上述的旗標，可供在本機使用不需要文件檔案的內容。

下列程式碼片段示範如何使用`NSMetadataQuery`查詢特定的檔案是否存在，並建置該檔案，如果不存在：

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

### <a name="document-thumbnails"></a>文件縮圖

Apple 感覺最佳的使用者體驗時列出的應用程式的文件是使用預覽。 這會讓終端使用者內容，以便可以快速地識別他們想要使用的文件。

在 iOS 8 之前, 顯示文件預覽所需的自訂實作。 新增 ios 8 是檔案系統屬性，可讓開發人員快速使用文件縮圖。

#### <a name="retrieving-document-thumbnails"></a>擷取文件縮圖 

藉由呼叫`GetPromisedItemResourceValue`或`GetPromisedItemResourceValues`方法`NSUrl_PromisedItems`API， `NSUrlThumbnailDictionary`，就會傳回。 目前在此字典中的唯一索引鍵是`NSThumbnial1024X1024SizeKey`和其比對`UIImage`。

#### <a name="saving-document-thumbnails"></a>儲存文件縮圖

若要儲存縮圖的最簡單方式是使用`UIDocument`。 藉由呼叫`GetFileAttributesToWrite`方法`UIDocument`和設定縮圖，它會自動儲存的文件檔案時。 精靈 icloud 的功能將會看到這項變更，並傳播到 icloud 的功能。 在 Mac OS X 上，縮圖會自動產生的開發人員快速尋找外掛程式。

最基本的使用 iCloud 基礎文件的位置，以及修改現有的應用程式開發介面，我們都準備好要實作文件選擇器檢視控制器中的 Xamarin iOS 8 行動裝置應用程式。


## <a name="enabling-icloud-in-xamarin"></a>啟用在 Xamarin iCloud

文件選擇器可以用於 Xamarin.iOS 應用程式之前，icloud 的功能支援，就必須啟用您的應用程式中，透過 Apple。 

下列步驟逐步解說的 iCloud 佈建程序。

1. 建立容器 iCloud。
2. 建立應用程式識別碼，其中包含應用程式服務 icloud 的功能。
3. 建立佈建的設定檔，其中包含此應用程式識別碼。

[功能使用](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南逐步解說前兩個步驟。 若要建立佈建設定檔，請遵循[佈建設定檔](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile)指南。



下列步驟逐步解說設定 iCloud 您的應用程式的程序：

請執行下列動作：

1.  Mac 或 Visual Studio 中開啟 Visual Studio 中的專案。
2.  在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取選項。
3.  在 [選項] 對話方塊選取**iOS 應用程式**，請確認**配套識別碼**符合已定義的一個**應用程式識別碼**上面所建立的應用程式。 
4.  選取**iOS 套件組合簽署**，選取**開發人員識別**和**佈建設定檔**上面所建立。
5.  按一下**確定**按鈕以儲存變更並關閉對話方塊。
6.  以滑鼠右鍵按一下`Entitlements.plist`中**方案總管 中**在編輯器中開啟它。

    > [!IMPORTANT]
    > 在 Visual Studio 中您可能需要開啟權利編輯器，以滑鼠右鍵按一下它，選取**開啟方式...** 選取屬性清單編輯器

7.  請檢查**啟用 iCloud** ， **iCloud 文件**，**機碼值的儲存體**和**CloudKit** 。
8.  請確定**容器**存在應用程式 （如上面所建立）。 範例：`iCloud.com.your-company.AppName`
9.  將變更儲存到檔案。

如需權利的詳細資訊請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

進行就地上述的安裝程式，應用程式現在可以使用以雲端為基礎的文件和新的文件選擇器檢視控制器。

## <a name="common-setup-code"></a>安裝程式的通用程式碼

入門文件選擇器檢視控制器之前, 沒有所需的一些標準安裝程式碼。 藉由修改應用程式的啟動`AppDelegate.cs`檔案，並讓它看起來如下所示：

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
                    // Yes, inform caller and save location the the Application Container
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
> 上述程式碼包含上述發掘和列出文件區段中的程式碼。 它會顯示以下失真，就會出現在實際的應用程式中。 為了簡單起見，此範例會使用單一的硬式編碼的檔案 (`test.txt`) 只。

上述程式碼會公開數個 iCloud 磁碟機快速鍵，使其更容易使用的應用程式的其餘部分。

接下來，加入下列程式碼的任何檢視或檢視的容器會使用文件選擇器或使用以雲端為基礎的文件：

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

這會將捷徑移至`AppDelegate`及存取上面所建立的 iCloud 快速鍵。

這個位置中的程式碼，讓我們來看看 Xamarin iOS 8 應用程式中實作文件選擇器檢視控制器。

## <a name="using-the-document-picker-view-controller"></a>使用文件選擇器檢視控制器

在 iOS 8 之前, 是很難從另一個應用程式存取文件，因為沒有任何方法可探索在應用程式從應用程式內外部的文件。

### <a name="existing-behavior"></a>現有的行為

 [![](document-picker-images/image31.png "現有的行為概觀")](document-picker-images/image31.png#lightbox)

讓我們看看存取 iOS 8 之前的外部文件：

1.  使用者會先開啟原先建立文件的應用程式。
1.  選取文件和`UIDocumentInteractionController`用來傳送文件的新應用程式。
1.  最後，原始文件的複本被放置在新的應用程式容器中。


從該處文件適用於第二個應用程式開啟及編輯。

### <a name="discovering-documents-outside-of-an-apps-container"></a>探索應用程式的容器之外的文件

在 iOS 8 中，應用程式是可以輕鬆存取自己的應用程式容器之外的文件：

 [![](document-picker-images/image32.png "探索應用程式的容器之外的文件")](document-picker-images/image32.png#lightbox)

使用新的文件選擇器 iCloud ( `UIDocumentPickerViewController`)、 iOS 應用程式可以直接找出並存取其應用程式容器之外。 `UIDocumentPickerViewController`提供的機制，讓使用者存取權授與和編輯這些權限透過找到的文件。

應用程式必須在選擇將其文件出現在文件選擇器 icloud 的功能，並可供其他應用程式探索及使用它們。 Xamarin iOS 8 應用程式共用其應用程式的容器，並加以編輯`Info.plist`標準文字編輯器中的檔案，並將下列兩行加入至字典的下方 (之間`<dict>...</dict>`標記):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供絕佳的新 UI，讓使用者選擇文件。 若要在 Xamarin iOS 8 的應用程式顯示文件選擇器檢視控制器，執行下列作業：

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
> 開發人員必須呼叫`StartAccessingSecurityScopedResource`方法`NSUrl`之前可以存取外部的文件。 `StopAccessingSecurityScopedResource`必須呼叫方法，在載入文件時，立即釋放安全性鎖定。

### <a name="sample-output"></a>範例輸出

上述程式碼會顯示文件選擇器 iPhone 裝置上執行時的方式的範例如下：

1.  使用者啟動應用程式，並顯示的主要介面：   
 
    [![](document-picker-images/image33.png "顯示的主要介面")](document-picker-images/image33.png#lightbox)
1.  使用者點選**動作**螢幕頂端的按鈕，並要求選取**文件提供者**從可用的提供者的清單：   
 
    [![](document-picker-images/image34.png "從可用的提供者清單中選取文件提供者")](document-picker-images/image34.png#lightbox)
1.  **文件選擇器檢視控制器**會顯示選定**文件提供者**:   
 
    [![](document-picker-images/image35.png "顯示文件選擇器檢視控制器")](document-picker-images/image35.png#lightbox)
1.  使用者在點選**文件資料夾**以顯示其內容：   
 
    [![](document-picker-images/image36.png "文件資料夾內容")](document-picker-images/image36.png#lightbox)
1.  使用者選取**文件**和**文件選擇器**已關閉。
1.  主要介面會重新顯示，**文件**取自載入外部的容器，並顯示其內容。


實際文件選擇器檢視控制器的顯示取決於文件的提供者，使用者已安裝在裝置上的文件選擇器模式已實作。 上述範例中使用開啟模式，將下面將詳細討論的模式類型。

## <a name="managing-external-documents"></a>管理外部文件

如上所述前 8、 iOS, 應用程式無法只能存取其應用程式容器的一部分的文件。 在 iOS 8 應用程式可以存取文件，從外部來源：

 [![](document-picker-images/image37.png "管理外部文件概觀")](document-picker-images/image37.png#lightbox)

當使用者選取從外部來源的文件時，參考文件會寫入至應用程式容器所指原始文件。

若要協助將加入到現有的應用程式的新功能，數個新功能有尚未加入`NSMetadataQuery`應用程式開發介面。 一般而言，應用程式會使用普遍文件範圍清單即時應用程式容器中的文件。 使用這個範圍內，應用程式容器中的文件將會繼續顯示。

使用新的無所不在外部文件範圍，會傳回文件，即時應用程式容器之外，並傳回它們的中繼資料。 `NSMetadataItemUrlKey`會指向 URL 實際上位於文件所在。

有時候應用程式不想要處理第參考所指向的文件。 相反地，應用程式想要直接使用參考文件。 例如，應用程式可能想要顯示在 UI 中，應用程式的資料夾中的文件，或允許使用者在移動資料夾，參考。

在 iOS 8，新`NSMetadataItemUrlInLocalContainerKey`已提供直接存取參考文件。 此機碼指向實際的應用程式容器中的外部文件參考。

`NSMetadataUbiquitousItemIsExternalDocumentKey`用來測試是否已在外部應用程式的容器文件。 `NSMetadataUbiquitousItemContainerDisplayNameKey`用來存取罩外部文件的原始副本容器的名稱。

### <a name="why-document-references-are-required"></a>為何需要文件參考

該 iOS 8 使用參考來存取外部的文件的主要原因是安全性。 沒有任何應用程式會提供存取任何其他應用程式的容器。 文件選擇器可以這樣做，因為已執行的-處理序外，而且具有整個系統的存取。

若要取得應用程式容器之外的文件的唯一方法是使用文件選擇器，而且所傳回的 URL 選擇器則安全性範圍。 安全性範圍 URL 包含選取的文件以及已設定領域的權限授與文件的應用程式存取所需的足夠資訊。

請務必請注意，是否安全性範圍 URL 已序列化為字串，然後還原序列化安全性資訊可能會遺失，檔案會導致無法從 URL 存取。 [文件參考] 功能提供一個機制，返回到這些 Url 所指向的檔案。

因此，如果應用程式取得`NSUrl`從其中一個參考文件，它已有附加的安全性範圍，而且可以用來存取檔案。 基於這個理由，強烈建議開發人員使用`UIDocument`因為它會先處理所有的這個資訊和程序，它們。

### <a name="using-bookmarks"></a>使用書籤

您不一定永遠可行列舉應用程式的文件，才能回到特定文件，例如，當執行狀態還原。 iOS 8 提供一個機制，建立書籤，直接以給定的文件為目標。

下列程式碼會建立從書籤`UIDocument`的`FileUrl`屬性：

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

現有的書籤 API 可以用來建立對現有書籤`NSUrl`，可以儲存和載入為可直接存取外部檔案。 下列程式碼將會還原已上面所建立的書籤：

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>開啟 vs。匯入模式和文件選擇器

文件選擇器檢視控制站功能的兩個不同的作業模式：

1.  **開啟模式**-在此模式中，當使用者選取外部的文件，文件選擇器會建立一個安全性範圍的書籤應用程式容器中。   
 
    [![](document-picker-images/image37.png "安全性範圍的應用程式容器中的書籤")](document-picker-images/image37.png#lightbox)
1.  **匯入模式**-在此模式中，當使用者選取和外部文件、 文件選擇器將不建立書籤，但相反地，複製到暫存位置的檔案以及提供應用程式存取此位置處的文件：   
 
    [![](document-picker-images/image38.png "文件選擇器會將檔案複製到暫存位置，並提供此位置的文件的應用程式存取")](document-picker-images/image38.png#lightbox)   
 一旦應用程式結束，因為任何原因，會清空暫存位置，並移除檔案。 如果應用程式需要維護檔案的存取權，它應該製作複本，並將它放在其應用程式容器中。


開啟模式時，應用程式希望另一個應用程式與共同作業及共用與該應用程式的文件進行任何變更。 匯入模式是在應用程式不想與其他應用程式共用文件對其進行修改時使用。

## <a name="making-a-document-external"></a>讓外部文件

如先前所述，iOS 8 應用程式沒有自己的應用程式容器之外的容器的存取權。 應用程式可以寫入其本身的容器，在本機，或到暫存位置，然後使用特殊的文件模式將應用程式容器之外產生的文件移至 使用者選擇的位置。

若要移至外部位置的文件，執行下列作業：

1.  先建立新文件中的本機或暫存位置。
1.  建立`NSUrl`，它會指向新的文件。
1.  開啟新的文件選擇器檢視控制器，並將它傳遞`NSUrl`模式與`MoveToService`。 
1.  一旦使用者選擇新的位置，文件會從其目前位置移到新位置。
1.  參考文件會寫入應用程式的應用程式的容器，以便建立應用程式仍然可以存取檔案。


下列程式碼可以用來將文件移至外部位置： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

上述程序所傳回的參考文件是完全開啟的文件選擇器模式所建立的其中一個相同。 不過，但有些時候，應用程式可能會想要移動的文件沒有保持它的參考。

若要移動文件，而不會產生參考，請使用`ExportToService`模式。 範例：`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

當使用`ExportToService`模式中，文件會複製到外部的容器，並將現有複本留在原來的位置。

## <a name="document-provider-extensions"></a>文件提供者擴充功能

使用 iOS 8，Apple 想要能夠存取其雲端為基礎的文件，不論其實際所在的任何使用者。 為了達成這個目標，iOS 8 提供新的文件提供者延伸模組機制。

### <a name="what-is-a-document-provider-extension"></a>什麼是文件提供者延伸模組？

簡言之，文件提供者延伸模組可讓開發人員或協力廠商提供應用程式替代文件儲存體中現有的 iCloud 儲存位置完全一樣的方式可存取。

使用者可以從文件選擇器選取其中一個這些替代儲存位置，而且它們可以使用完全相同的存取模式 （開啟、 匯入、 移動或匯出） 使用在該位置的檔案。

這是使用兩個不同的延伸模組來實作：

-  **文件選擇器延伸模組**– 提供`UIViewController`提供圖形介面，讓使用者選擇從替代儲存位置的文件的子類別。 這個子類別會顯示為文件選擇器檢視控制器的一部分。
-  **提供副檔名**– 這是在非 UI 延伸模組處理實際上提供檔案內容。 這些擴充功能透過檔案協調提供 ( `NSFileCoordinator` )。 這是另一個重要的情況下，需要檔案協調的地方。


下圖顯示典型的資料流程，使用文件提供者擴充功能時：

 [![](document-picker-images/image39.png "使用文件提供者延伸模組時，此圖表會顯示一般資料流程")](document-picker-images/image39.png#lightbox)

下列程序：

1.  應用程式顯示文件選擇器控制器能夠允許使用者選取要使用的檔案。
1.  使用者選擇替代檔案位置和自訂`UIViewController`呼叫以顯示使用者介面的延伸模組。
1.  使用者會從這個位置來選取檔案和 URL 會傳遞至文件選擇器。
1.  文件選擇器選取檔案的 URL，並傳回要用於使用者的應用程式。
1.  URL 會傳遞給應用程式傳回檔案內容檔案的協調器。
1.  檔案協調器會呼叫自訂檔案提供者延伸模組，以擷取檔案。
1.  檔案的內容會傳回給檔案的協調器。
1.  檔案的內容會傳回至應用程式。


### <a name="security-and-bookmarks"></a>安全性和書籤

本節會快速查看藉由提供者文件副檔名的書籤運作存取安全性和永續檔案的方式。 不同於 iCloud 文件提供者，會自動儲存到應用程式容器的安全性和書籤，文件提供者延伸模組不因為它們不是文件的參考系統的一部分。

例如： 在企業設定中，提供它自己全公司的安全資料存放區，系統管理員不想要存取，或由公用 iCloud 伺服器所處理的機密公司資訊。 因此，無法使用內建的文件的參考系統。

書籤系統仍可使用，來正確地處理已標記書籤的 URL，並傳回它所指向的文件的內容提供者副檔名負責。

基於安全性考量，iOS 8 有隔離層保存有關哪些應用程式可以存取哪些檔案提供者內的識別項的資訊。 請注意，所有的檔案存取由此隔離層。

使用書籤與文件提供者延伸模組時下, 圖顯示資料流程中的資料：

 [![](document-picker-images/image40.png "使用書籤與文件提供者延伸模組時，此圖表會顯示資料流程中的資料")](document-picker-images/image40.png#lightbox)

下列程序：

1.  應用程式即將進入背景，而且需要保存其狀態。 它會呼叫`NSUrl`替代儲存體中建立書籤到檔案。
1.  `NSUrl` 呼叫檔案提供者延伸模組，以取得文件的持續性 URL。 
1.  副檔名提供者以字串形式傳回 URL `NSUrl` 。
1.  `NSUrl`組合成書籤的 URL 並將其傳回至應用程式。
1.  當應用程式在背景中進行醒來了，且需要還原狀態時，它會將傳遞的書籤`NSUrl`。
1.  `NSUrl` 呼叫檔案提供者延伸模組檔案的 url。
1.  檔案的擴充提供者存取檔案，並傳回檔案的位置`NSUrl`。
1.  檔案位置是與安全性資訊包裝在一起，並傳回到應用程式。


從這裡開始，應用程式可以存取該檔案，並像平常一樣使用它。

### <a name="writing-files"></a>寫入檔案

本節會快速查看如何寫入檔案到替代位置與文件提供者延伸模組的運作方式。 IOS 應用程式會將資訊儲存到應用程式容器內的磁碟使用檔案協調。 短時間內已順利寫入檔案之後，變更將會收到通知檔案提供者延伸模組。

此時，檔案提供者延伸模組可以啟動 上傳檔案到替代位置 （或檔案標示為中途和需要的上傳）。

### <a name="creating-new-document-provider-extensions"></a>建立新的文件提供者擴充功能

建立新的文件提供者延伸模組是簡介本文的範圍內。 這項資訊會在提供這裡顯示，根據使用者在其 iOS 裝置中載入的延伸模組，應用程式可能具有存取權之外提供 iCloud 位置的 Apple 文件儲存位置。

使用文件選擇器時，開發人員應該瞭解這個情況和使用外部的文件。 它們不應該假設這些文件裝載於 icloud 的功能。

如需有關如何建立儲存體提供者或文件選擇器延伸模組的詳細資訊，請參閱[應用程式延伸模組簡介](~/ios/platform/extensions.md)文件。

## <a name="migrating-to-icloud-drive"></a>移轉至 iCloud 磁碟機

在 iOS 8，使用者可以選擇繼續使用現有 icloud 的功能使用 iOS 7 （與先前的系統） 中的文件系統，或他們可以選擇將現有的文件移轉到新的 iCloud 磁碟機機制。

在 Mac OS X Yosemite，Apple 不提供回溯相容性，因此所有文件必須移轉到 iCloud 的磁碟機，或將不再更新裝置上。

使用者的帳戶已移轉至 iCloud 磁碟機之後，只有使用 iCloud 的磁碟機的裝置可以將變更傳播至文件在這些裝置上。

> [!IMPORTANT]
> 開發人員應該要知道這篇文章中涵蓋的新功能才可使用使用者的帳戶已移轉至 iCloud 磁碟機。 

## <a name="summary"></a>總結

這篇文章已涵蓋現有 icloud 的功能，應用程式開發介面支援 iCloud 磁碟機和新的文件選擇器檢視控制站時所需的變更。 它已涵蓋檔案協調和它為何如此重要時使用以雲端為基礎的文件。 它涵蓋安裝程式才能啟用 Xamarin.iOS 應用程式中以雲端為基礎的文件並提供簡介查看使用文件選擇器檢視控制站的應用程式的應用程式容器之外的文件。

此外，本文簡要涵蓋文件提供者延伸模組，而且為什麼開發人員應該知道它們的存在撰寫可以處理以雲端為基礎的文件的應用程式時。

## <a name="related-links"></a>相關連結

- [DocPicker （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)
