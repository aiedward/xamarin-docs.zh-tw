---
title: 在 Xamarin.iOS 中的文件選擇器
description: 本文件描述 iOS 文件選擇器，並討論如何在 Xamarin.iOS 中使用它。 它將探討 iCloud、 文件、 常見的安裝程式碼、 文件提供者擴充功能，和更多功能。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: ca0c7a6e655fdc44aa673a59be71bc83044d3085
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353330"
---
# <a name="document-picker-in-xamarinios"></a>在 Xamarin.iOS 中的文件選擇器

文件選擇器可讓應用程式之間共用的文件。 在 iCloud 或不同的應用程式的目錄中，可能會儲存這些文件。 透過一組所共用的文件[文件提供者擴充](~/ios/platform/extensions.md)使用者在其裝置上安裝。 

保持在應用程式和雲端進行同步處理的文件的困難，因為它們會引進一段所需的複雜性。

## <a name="requirements"></a>需求

需要下列項目來完成這篇文章所述的步驟：

-  **Xcode 7 和 iOS 8 或更新版本**– Apple 的 Xcode 7 和 iOS 8 或更新版本的 Api 需要安裝並設定開發人員電腦上。
-  **Visual Studio 或 Visual Studio for Mac** – 應該安裝最新版的 Visual Studio for Mac。
-  **iOS 裝置**– iOS 裝置執行 iOS 8 或更新版本。

## <a name="changes-to-icloud"></a>變更至 iCloud

若要實作的新功能的文件選擇器，已進行下列變更至 Apple 的 iCloud 服務：

-  精靈已全面改寫，使用 CloudKit iCloud。
-  現有的 iCloud 功能已被重新命名 iCloud Drive。
-  Microsoft Windows 作業系統的支援已新增到 icloud 的功能。
-  在 Mac OS 尋找工具中已新增 iCloud 資料夾。
-  iOS 裝置可以存取的 Mac OS iCloud 資料夾的內容。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="what-is-a-document"></a>什麼是文件？

當參考到 icloud 文件時，它是單一的獨立實體，而且這類使用者中發現。 使用者可能想要修改的文件，或與其他使用者共用 （透過電子郵件，例如）。

有數種類型的檔案，使用者會立即辨識為文件，例如頁面，重點演說或數字的檔案。 不過，不限於此概念 iCloud。 例如，可以視為文件和儲存在 iCloud 中 （例如 Chess 相符項目） 的遊戲狀態。 這個檔案可能使用者的裝置之間傳遞，並讓其挑選遊戲的地方在不同裝置上。

## <a name="dealing-with-documents"></a>處理文件

前一頭栽進程式碼，以使用 [文件選擇器]，請使用 Xamarin，這篇文章將介紹最佳的作法，來使用 iCloud 文件，以及數個現有的 api 所做的修改，才能支援文件選擇器。

### <a name="using-file-coordination"></a>使用檔案協調

因為可以修改檔案，從數個不同的位置，就必須使用協調以避免資料遺失。

 [![](document-picker-images/image1.png "使用檔案協調")](document-picker-images/image1.png#lightbox)

讓我們看一下上圖中：

1.  使用檔案協調的 iOS 裝置建立新的文件，並將它儲存至資料夾 icloud 的功能。
2.  iCloud 將修改過的檔案儲存至雲端，以發佈至每個裝置。
3.  附加的 Mac 看到 iCloud 資料夾中修改過的檔案，並使用檔案協調變更下複製到檔案中。
4.  裝置不使用檔案協調對檔案進行變更，並將它儲存至資料夾 icloud 的功能。 這些變更會立即複寫到其他裝置。

假設原始的 iOS 裝置或 Mac 已編輯檔案，其變更會遺失且未經協調的裝置中的檔案的版本覆寫。 若要避免資料遺失，檔案協調時，必須使用雲端架構的文件。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument` 使事情更簡單 (或`NSDocument`在 macOS 上) 執行所有繁重的工作，開發人員。 它提供內建檔案一起使用，以防止封鎖的應用程式 UI 的背景佇列。

 `UIDocument` 會公開多個，需要針對任何用途的開發人員簡化開發工作的 Xamarin 應用程式的高階 Api。

下列程式碼建立的子類別`UIDocument`實作泛型文字為基礎的文件，可用來儲存及擷取文字從 iCloud:

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

`GenericTextDocument`使用 Xamarin.iOS 8 應用程式中的外部文件與文件選擇器時，將這整篇文章使用以上所顯示的類別。

## <a name="asynchronous-file-coordination"></a>非同步檔案協調

iOS 8 提供數種新非同步檔案協調功能，透過新的檔案協調 Api。 IOS 8 之前所有的現有檔案協調 Api 已完全同步。 這表示開發人員負責實作自己的背景佇列來防止檔案協調封鎖應用程式的 UI。

新`NSFileAccessIntent`類別包含 URL，指向 檔案及數個選項可控制協調所需的類型。 下列程式碼示範如何將檔案從一個位置移到另一個使用意圖：

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

## <a name="discovering-and-listing-documents"></a>探索並列出文件

探索並列出的文件的方式是使用現有的`NSMetadataQuery`Api。 本節將討論新的功能新增至`NSMetadataQuery`，便於使用的文件甚至比以前更容易。

### <a name="existing-behavior"></a>現有的行為

在 iOS 8 之前`NSMetadataQuery`已收取的本機檔案的變更緩慢的這類： 刪除、 建立和重新命名。

 [![](document-picker-images/image2.png "NSMetadataQuery 本機檔案的變更概觀")](document-picker-images/image2.png#lightbox)

在上圖中：

1.  在應用程式容器中，已經存在的檔案`NSMetadataQuery`具有現有`NSMetadata`預先建立的記錄，以及多工緩衝處理如此就能立即提供給應用程式。
1.  應用程式的應用程式容器中建立新的檔案。
1.  沒有之前的延遲`NSMetadataQuery`會將應用程式容器的修改，並建立必要`NSMetadata`記錄。


由於建立延遲`NSMetadata`記錄，應用程式必須有兩個資料來源開啟： 一個本機檔案變更，另一個雲端架構的變更。

### <a name="stitching"></a>編結

在 iOS 8`NSMetadataQuery`直接使用新功能，稱為連結的工作變得更容易：

 [![](document-picker-images/image3.png "與新功能的 NSMetadataQuery 稱為連結")](document-picker-images/image3.png#lightbox)

使用在上圖中的連結：

1.  同樣地，針對存在於應用程式容器的檔案`NSMetadataQuery`具有現有`NSMetadata`預先建立的記錄，以及多工緩衝處理。
1.  應用程式會使用檔案協調的應用程式容器中建立新的檔案。
1.  修改和呼叫，會看到應用程式容器中的勾點`NSMetadataQuery`來建立必要`NSMetadata`記錄。
1.  `NSMetadata`記錄檔案後，直接建立，並會提供給應用程式。


使用連結的應用程式不再具有開啟資料來源來監視本機和雲端基礎檔案變更。 現在應用程式都可以依賴`NSMetadataQuery`直接。

> [!IMPORTANT]
> 編結僅適用於應用程式正在使用檔案協調，如上節所示。 如果未使用檔案協調，Api 預設為現有的預先 iOS 8 行為。




### <a name="new-ios-8-metadata-features"></a>IOS 8 的中繼資料的新功能

已新增下列新功能至`NSMetadataQuery`在 iOS 8 中：

-   `NSMetatadataQuery` 現在可以列出儲存在雲端中的非本機文件。
-  已新增新的 Api 來存取雲端架構的文件上的中繼資料資訊。 
-  新`NSUrl_PromisedItems`API，將會存取檔案的屬性，可能會或可能沒有提供其內容在本機的檔案。
-  使用 `GetPromisedItemResourceValue`方法來取得指定檔案的相關資訊，或使用`GetPromisedItemResourceValues`方法一次取得多個檔案的資訊。


已新增兩個新的檔案協調旗標來處理中繼資料：

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


使用上述的旗標，可供在本機使用不需要的文件檔案的內容。

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

Apple 會覺得最佳的使用者體驗時列出的應用程式的文件是使用預覽。 這可讓終端使用者內容中，因此它們可以快速識別他們想要使用的文件。

在 iOS 8 之前顯示的文件預覽所需的自訂實作。 剛接觸 iOS 8 都可讓開發人員快速地處理文件縮圖的檔案系統屬性。

#### <a name="retrieving-document-thumbnails"></a>擷取文件縮圖 

藉由呼叫`GetPromisedItemResourceValue`或是`GetPromisedItemResourceValues`方法`NSUrl_PromisedItems`API， `NSUrlThumbnailDictionary`，則會傳回。 這個字典中目前的唯一索引鍵是`NSThumbnial1024X1024SizeKey`和其比對`UIImage`。

#### <a name="saving-document-thumbnails"></a>正在儲存的文件縮圖

若要儲存縮圖的最簡單方式是使用`UIDocument`。 藉由呼叫`GetFileAttributesToWrite`方法的`UIDocument`和設定縮圖，它會自動儲存文件檔案時。 ICloud 服務精靈將會看到這項變更，並傳播到 icloud 的功能。 在 Mac OS X、 縮圖會自動為開發人員產生快速尋找外掛程式。

有的基本知識基礎的 iCloud 中的文件位置，以及修改現有的 API，我們就準備好要實作在 Xamarin iOS 8 中的文件選擇器檢視控制器行動應用程式。


## <a name="enabling-icloud-in-xamarin"></a>在 Xamarin 中啟用 iCloud

文件選擇器可用於 Xamarin.iOS 應用程式之前，就必須同時在您的應用程式，以及透過 Apple 啟用 iCloud 支援。 

下列步驟逐步解說的 iCloud 的佈建程序。

1. 建立 iCloud 容器。
2. 建立包含 iCloud App Service 應用程式識別碼。
3. 建立佈建設定檔，其中包含此應用程式識別碼。

[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南逐步說明的前兩個步驟。 若要建立佈建設定檔，請遵循[佈建設定檔](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device)指南。



下列步驟逐步解說設定 iCloud 的應用程式的程序：

請執行下列動作：

1.  在中開啟專案 Visual Studio for Mac 或 Visual Studio。
2.  在 [**方案總管] 中**，以滑鼠右鍵按一下專案，然後選取選項。
3.  在 [選項] 對話方塊中選取**iOS 應用程式**，請確認**套件組合識別碼**比對中所定義的一個**應用程式識別碼**上面建立的應用程式。 
4.  選取  **iOS 套件組合簽署**，選取**開發人員身分識別**並**佈建設定檔**上面所建立。
5.  按一下 **確定**按鈕以儲存變更並關閉對話方塊。
6.  以滑鼠右鍵按一下`Entitlements.plist`中**方案總管 中**在編輯器中開啟它。

    > [!IMPORTANT]
    > 在 Visual Studio 中您可能需要開啟權利編輯器上按一下滑鼠右鍵，選取**開啟方式...** 選取屬性清單編輯器

7.  請檢查**啟用 iCloud** ， **iCloud 文件**，**機碼值儲存體**並**CloudKit** 。
8.  請確定**容器**存在應用程式 （如上述所建立）。 範例：`iCloud.com.your-company.AppName`
9.  將變更儲存到檔案。

如需權利的詳細資訊請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

上述設定，以雲端為基礎的文件和新的文件選擇器檢視控制器，現在可以使用應用程式。

## <a name="common-setup-code"></a>常見的安裝程式碼

開始使用文件選擇器檢視控制器之前, 沒有所需的一些標準安裝程式碼。 藉由修改應用程式的啟動`AppDelegate.cs`檔案，並使它看起來如下：

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
> 上述程式碼包含發掘及列出的文件上一節中的程式碼。 它會顯示以下看來，就會出現在實際的應用程式中。 為了簡單起見，此範例中的運作方式與單一的硬式編碼的檔案 (`test.txt`) 只。

上述程式碼會公開數個 iCloud 磁碟機捷徑，以便更輕鬆在應用程式的其餘部分中使用。

接下來，加入下列程式碼的任何檢視或檢視的容器，以使用文件選擇器，或使用雲端架構的文件：

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

這會將新增的捷徑，以前往`AppDelegate`並存取先前建立的 iCloud 快速鍵。

此程式碼就緒之後，讓我們來看看實作 Xamarin iOS 8 應用程式中的文件選擇器檢視控制器。

## <a name="using-the-document-picker-view-controller"></a>使用文件選擇器檢視控制器

在 iOS 8 之前, 它是很難從另一個應用程式存取文件，因為沒有任何方法可探索從應用程式，應用程式內外部的文件項目。

### <a name="existing-behavior"></a>現有的行為

 [![](document-picker-images/image31.png "現有行為概觀")](document-picker-images/image31.png#lightbox)

讓我們看看存取外部文件在 iOS 8 之前：

1.  第一次使用者必須開啟原本建立文件的應用程式。
1.  選取文件和`UIDocumentInteractionController`用來將文件傳送至新的應用程式。
1.  最後，原始的文件的複本被放置在新的應用程式容器中。


從該處文件可供第二個應用程式開啟及編輯。

### <a name="discovering-documents-outside-of-an-apps-container"></a>探索應用程式的容器之外的文件

在 iOS 8 中，應用程式是能夠輕鬆地存取自己的應用程式容器之外的文件：

 [![](document-picker-images/image32.png "探索應用程式的容器之外的文件")](document-picker-images/image32.png#lightbox)

使用新的 iCloud 文件選擇器 ( `UIDocumentPickerViewController`)，iOS 應用程式以直接探索及存取其應用程式容器之外。 `UIDocumentPickerViewController`提供機制，以供使用者存取權授與和編輯這些權限透過探索文件。

應用程式必須 opt-in 以其顯示在 文件選擇器 icloud 的功能，並可供探索和使用方式的其他應用程式的文件。 Xamarin iOS 8 應用程式共用其應用程式容器，並加以編輯`Info.plist`檔案中標準文字編輯器，並將下列兩行新增至字典的底部 (之間`<dict>...</dict>`標記):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供絕佳的新 UI，可讓使用者選擇文件。 若要顯示的文件選擇器檢視控制器在 Xamarin iOS 8 應用程式中，執行下列作業：

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
> 開發人員必須呼叫`StartAccessingSecurityScopedResource`方法的`NSUrl`才能存取外部文件。 `StopAccessingSecurityScopedResource`必須呼叫方法來釋放安全性鎖定，只要在載入文件。

### <a name="sample-output"></a>範例輸出

上述程式碼會列印文件的顯示，請在 iPhone 裝置上執行時，文件選擇器的範例如下：

1.  使用者啟動應用程式，並顯示的主要介面：   
 
    [![](document-picker-images/image33.png "在顯示的主要介面")](document-picker-images/image33.png#lightbox)
1.  使用者點選**動作**在畫面頂端的按鈕，並選取 要求**文件提供者**從可用的提供者的清單：   
 
    [![](document-picker-images/image34.png "從可用的提供者清單中選取文件提供者")](document-picker-images/image34.png#lightbox)
1.  **文件選擇器檢視控制器**會顯示選定**文件提供者**:   
 
    [![](document-picker-images/image35.png "顯示文件選擇器檢視控制器")](document-picker-images/image35.png#lightbox)
1.  使用者點選**文件資料夾**以顯示其內容：   
 
    [![](document-picker-images/image36.png "文件資料夾內容")](document-picker-images/image36.png#lightbox)
1.  使用者選取**文件**並**文件選擇器**已關閉。
1.  主要的介面會重新顯示，**文件**從外部的容器，並顯示其內容會載入。


實際文件選擇器檢視控制器的顯示取決於使用者在裝置上安裝，並已實作的文件選擇器模式。 文件提供者。 上述範例中使用開啟模式，將以下將詳細討論其他的模式類型。

## <a name="managing-external-documents"></a>管理外部的文件

如上所述，在 iOS 8 之前的應用程式可能只會存取屬於其應用程式容器的文件。 在 iOS 8 中的應用程式可以存取文件，從外部來源：

 [![](document-picker-images/image37.png "管理外部文件概觀")](document-picker-images/image37.png#lightbox)

當使用者選取從外部來源的文件時、 參考文件會寫入原始文件會指向應用程式容器。

為了協助加入這項新功能到現有的應用程式，多項新功能已新增至`NSMetadataQuery`API。 一般而言，應用程式使用通用的文件範圍，來列出其應用程式容器內的文件。 使用此範圍，應用程式容器內的文件將會繼續顯示。

使用新的通用外部文件範圍，會傳回外部應用程式容器，並為其傳回的中繼資料文件。 `NSMetadataItemUrlKey`會指向 URL 文件所在實際位置。

有時候應用程式不想要使用 指向個參考的文件。 相反地，應用程式想要直接使用參考文件。 比方說，應用程式可能想要顯示在 UI 中，應用程式的資料夾中的文件，或讓使用者參考移動資料夾內。

在 iOS 8 的新`NSMetadataItemUrlInLocalContainerKey`已提供直接存取參考文件。 此機碼指向外部的文件，在應用程式容器中的實際參考。

`NSMetadataUbiquitousItemIsExternalDocumentKey`用來測試是否有文件是外部應用程式的容器。 `NSMetadataUbiquitousItemContainerDisplayNameKey`用來存取已裝載的外部文件的原始副本容器的名稱。

### <a name="why-document-references-are-required"></a>為什麼不需要文件參考

該 iOS 8 使用參考來存取外部文件的主要原因是安全性。 沒有任何應用程式可以存取任何其他應用程式的容器。 文件選擇器可以這麼做，因為是執行外處理，而且有整個系統的存取。

若要取得應用程式容器之外的文件的唯一方法是使用文件選擇器中，而且如果所傳回的 URL 選擇器安全性範圍。 安全性範圍 URL 中包含選取的文件，以及已設定領域的權限授與文件的應用程式存取所需的足夠資訊。

請務必請注意，是否安全性範圍 URL 已序列化為字串，然後還原序列化安全性資訊可能會遺失，檔案會導致無法從 URL 存取。 [文件參考] 功能提供一個機制，回到這些 Url 所指向的檔案。

因此，如果應用程式取得`NSUrl`從其中一個參考文件中，已經有連接的安全性範圍，而且可用來存取檔案。 基於這個理由，強烈建議開發人員使用`UIDocument`因為它會先處理所有此資訊和程序，為它們。

### <a name="using-bookmarks"></a>使用書籤

您不一定永遠可行列舉應用程式的文件，若要回到特定文件，例如，當執行狀態還原。 iOS 8 提供一個機制來建立直接以給定的文件為目標的書籤。

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

現有的書籤 API 用來建立針對現有的書籤`NSUrl`，可以儲存及提供直接存取的外部檔案載入。 下列程式碼將會還原上面所建立的書籤：

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

文件選擇器檢視控制器具有兩個不同的作業模式：

1.  **開啟 [模式]** -在此模式中，當使用者選取和外部的文件，文件選擇器會建立一個安全性範圍的書籤應用程式容器中。   
 
    [![](document-picker-images/image37.png "安全性範圍的應用程式容器中的書籤")](document-picker-images/image37.png#lightbox)
1.  **匯入模式**-在此模式中，當使用者選取和外部文件、 文件選擇器不會建立書籤，但相反地，複製到暫存位置的檔案和提供應用程式存取此位置的文件：   
 
    [![](document-picker-images/image38.png "文件選擇器會將檔案複製到暫存位置，並提供應用程式存取到這個位置的文件")](document-picker-images/image38.png#lightbox)   
 基於任何因素而終止應用程式，請清空暫存位置，而且移除檔案。 如果應用程式需要維護檔案的存取權，它應該建立複本，並將它放在其應用程式容器中。


開啟模式時，應用程式想要與其他應用程式共同作業及共用的文件與該應用程式進行任何變更。 應用程式不想與其他應用程式共用的文件的修改時，會使用匯入模式。

## <a name="making-a-document-external"></a>讓外部文件

如先前所述，iOS 8 應用程式並沒有自己的應用程式容器之外的容器的存取權。 應用程式可以寫入自己的容器，到暫存位置，或在本機，然後將應用程式容器外產生的文件移至 使用者選擇的位置使用的特殊文件模式。

若要移動到外部位置的文件，執行下列作業：

1.  第一次建立新的文件中的本機或暫存位置。
1.  建立`NSUrl`指向新的文件。
1.  開啟新的文件選擇器檢視控制器，並將它傳遞`NSUrl`模式與`MoveToService`。 
1.  一旦使用者選擇新的位置，文件會從其目前位置移到新位置。
1.  參考文件會寫入應用程式的應用程式容器，以便建立應用程式仍然可以存取檔案。


下列程式碼可用來將文件移到外部位置： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

上述的程序所傳回的參考文件正是其中一個建立的文件選擇器的開啟模式相同。 不過，有些應用程式可能會想要移動文件，而不保留其參考的時候。

若要移動文件，而不會產生參考，請使用`ExportToService`模式。 範例：`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

當使用`ExportToService`模式中，文件會複製到外部的容器，而現有的複本會保留在其原始位置。

## <a name="document-provider-extensions"></a>文件提供者擴充功能

Ios 8，Apple 會想要能夠存取其雲端為基礎的文件，不論其實際所在的任何使用者。 為了達成此目標，iOS 8 會提供新的文件提供者延伸模組機制。

### <a name="what-is-a-document-provider-extension"></a>什麼是文件提供者擴充功能？

簡言之，文件提供者擴充功能可讓開發人員或協力廠商提供給可與現有的 iCloud 儲存體位置完全相同的方式來存取應用程式替代的文件儲存體。

使用者可以從 文件選擇器選取其中一個這些替代儲存體位置，而且它們可以使用完全相同的存取模式 （開啟、 匯入、 移動或匯出） 使用該位置中的檔案。

這是使用兩個不同的延伸模組所實作的：

-  **文件選擇器擴充功能**– 提供`UIViewController`提供圖形化的介面，讓使用者選擇從替代儲存位置的文件的子類別。 做為文件選擇器檢視控制器的一部分，就會顯示這個子類別。
-  **提供副檔名**– 這是處理實際提供檔案內容的非 UI 延伸模組。 這些延伸模組提供透過檔案協調 ( `NSFileCoordinator` )。 這是另一個重要的情況下，需要檔案協調的情況。


下圖顯示典型的資料流程，使用 文件提供者擴充功能時：

 [![](document-picker-images/image39.png "使用 文件提供者擴充功能時下, 圖顯示典型的資料流程")](document-picker-images/image39.png#lightbox)

下列程序：

1.  應用程式提供的文件選擇器控制站，以允許使用者選取要使用的檔案。
1.  使用者選取替代檔案位置和自訂`UIViewController`呼叫延伸模組，以顯示使用者介面。
1.  使用者從這個位置選取檔案和 URL 傳回給文件選擇器。
1.  文件選擇器選取檔案的 URL，並傳回它來處理使用者的應用程式。
1.  URL 會傳遞至檔案協調器，以返回應用程式中的檔案內容。
1.  檔案協調器會呼叫自訂的檔案提供者延伸模組來擷取檔案。
1.  檔案的內容會回到檔案協調器。
1.  檔案的內容會傳回給應用程式。


### <a name="security-and-bookmarks"></a>安全性和書籤

本章節會快速查看如何安全性與持續性的檔案存取透過書籤與文件提供者擴充功能的運作方式。 ICloud 文件提供者，它會自動儲存至應用程式容器的安全性和書籤，與文件提供者擴充功能不因為它們不是文件的參考系統的一部分。

例如： 在企業設定中，提供它自己全公司的安全資料存放區，系統管理員不想存取或公開 iCloud 伺服器所處理的機密公司資訊。 因此，不能使用內建的文件的參考系統。

書籤系統仍然可用，而且檔案提供者延伸模組，來正確地處理加上書籤的 URL，並傳回它所指的文件內容的責任。

基於安全考量，iOS 8 有哪些應用程式有權限內的檔案提供者的識別項資訊保存隔離層。 請注意，所有的檔案存取權會受到此隔離層。

使用書籤和文件提供者擴充功能時下, 圖顯示資料流程：

 [![](document-picker-images/image40.png "使用書籤和文件提供者擴充功能時，此圖表會顯示資料流程")](document-picker-images/image40.png#lightbox)

下列程序：

1.  應用程式即將進入背景，而且必須保存其狀態。 它會呼叫`NSUrl`替代儲存體中建立檔案的書籤。
1.  `NSUrl` 呼叫檔案提供者延伸模組，以取得永續性文件 URL。 
1.  檔案提供者延伸模組以字串的形式傳回 URL `NSUrl` 。
1.  `NSUrl`組合成書籤的 URL 並將它傳回至應用程式。
1.  當應用程式在背景中進行醒來了，而且需要還原狀態時，會傳遞至書籤`NSUrl`。
1.  `NSUrl` 呼叫檔案提供者延伸模組檔案的 url。
1.  檔案延伸模組提供者會存取檔案，並傳回檔案的位置`NSUrl`。
1.  檔案位置是與安全性資訊包裝在一起，並傳回給應用程式。


從這裡開始，應用程式可以存取該檔案，並如往常一樣使用它。

### <a name="writing-files"></a>寫入檔案

本章節會快速瀏覽到替代位置寫入檔案與文件提供者擴充功能的運作方式。 IOS 應用程式會使用檔案協調將資訊儲存到應用程式容器內的磁碟。 短時間內的檔案已成功寫入後，系統會變更的通知檔案提供者延伸模組。

到目前為止，檔案提供者延伸模組可以開始將檔案上傳到替代位置 （或標示為中途和需要上傳的檔案）。

### <a name="creating-new-document-provider-extensions"></a>建立新的文件提供者擴充功能

建立新的文件提供者擴充功能是本簡介文章的範圍外。 這項資訊是提供這裡顯示，根據使用者在其 iOS 裝置中載入的延伸模組，應用程式可能必須存取 Apple 提供 iCloud 位置之外的文件儲存體位置。

使用文件選擇器時，開發人員應該瞭解這個情況與使用外部文件。 它們不應該假設在 iCloud 中裝載這些文件。

如需有關如何建立儲存體提供者或文件選擇器擴充功能的詳細資訊，請參閱[應用程式延伸模組簡介](~/ios/platform/extensions.md)文件。

## <a name="migrating-to-icloud-drive"></a>移轉至 iCloud Drive

在 iOS 8，使用者可以選擇繼續使用現有的 iCloud 使用 iOS 7 （和先前的系統） 中的文件系統，或他們可以選擇將現有的文件移轉至新的 iCloud 磁碟機機制。

在 Mac OS X Yosemite，Apple 不會提供回溯相容性讓所有的文件都必須移轉至 iCloud 的磁碟機，或將不再更新裝置上。

使用者的帳戶已移轉到 iCloud 的磁碟機之後，只有使用 iCloud 的磁碟機的裝置都能夠將變更傳播到文件在這些裝置。

> [!IMPORTANT]
> 開發人員應該留意這篇文章中涵蓋的新功能才會出現使用者帳戶已移轉到 iCloud Drive。 

## <a name="summary"></a>總結

這篇文章已涵蓋現有 iCloud 支援 iCloud 磁碟機與新的文件選擇器檢視控制器所需的 Api 中所做的變更。 它涵蓋檔案協調和為什麼很重要時使用雲端架構的文件。 它涵蓋可以讓雲端為基礎的 Xamarin.iOS 應用程式中的文件所需的設定，並提供簡介看如何使用外部應用程式的應用程式容器使用的文件選擇器檢視控制器的文件。

此外，這篇文章簡要涵蓋文件提供者擴充功能，以及為什麼撰寫可以處理雲端為基礎的文件的應用程式時，開發人員應該要注意它們。

## <a name="related-links"></a>相關連結

- [DocPicker （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)
