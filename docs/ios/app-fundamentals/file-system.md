---
title: 使用 Xamarin.iOS 中的檔案系統
description: 本文件說明如何使用 Xamarin.iOS 中的檔案系統。 它討論讀取檔案、 XML 和 JSON 序列化、 應用程式沙箱，共用透過 iTunes，及其他檔案的目錄。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: cf595b57906cf1c47acdcdbcddf04bfbdc963393
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113426"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>使用 Xamarin.iOS 中的檔案系統

您可以使用 Xamarin.iOS 和`System.IO`中的類別 *.NET 基底類別庫 (BCL)* 存取 iOS 檔案系統。 `File`類別可讓您建立、 刪除和讀取檔案，而`Directory`類別可讓您建立、 刪除或列舉目錄的內容。 您也可以使用`Stream`子類別，其可提供更高的控制檔案作業 （例如壓縮或位置搜尋檔案中）。

iOS 會為應用程式可進行以檔案系統保留的應用程式資料的安全性，保護使用者免於惡意應用程式的一些限制。 這些限制屬於*應用程式沙箱*– 一組規則，以限制對檔案喜好設定、 網路資源、 硬體、 等的應用程式的存取。應用程式只限於讀取和寫入其主目錄 （安裝位置;） 中的檔案它無法存取另一個應用程式的檔案。

iOS 也有一些檔案系統的特定功能： 特定目錄需要特殊處理方式備份和升級，而且應用程式也可以共用透過 iTunes 檔案。

這篇文章討論的功能和限制 iOS 的詳細資訊，在檔案系統，其中包含示範如何使用 Xamarin.iOS 來執行一些簡單的檔案系統作業的範例應用程式：

 [![](file-system-images/05-sampleapp.png "IOS 執行一些簡單的檔案系統作業的範例")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>一般檔案存取

Xamarin.iOS 可讓您使用.NET`System.IO`在 iOS 上的檔案系統作業的類別。

下列程式碼片段會說明一些常見的檔案作業。 您會發現它們全都下方`SampleCode.cs`檔案，請在本文的範例應用程式。

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>使用目錄

此程式碼會列舉在目前的目錄的子目錄 (所指定 「。 /"參數)，這是應用程式可執行檔的位置。
您的輸出會是一份所有檔案和資料夾與您 （顯示在主控台視窗時進行偵錯） 的應用程式一起部署。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>讀取檔案

若要讀取的文字檔案，您只需要一行程式碼。 此範例中會顯示在 [應用程式輸出] 視窗的文字檔案的內容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>XML 序列化

雖然使用 完整`System.Xml`命名空間已超出本文的範圍，輕鬆地將使用這類以 StreamReader 從檔案系統的 XML 文件還原序列化：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

請參閱 MSDN 文件[System.Xml](http://msdn.microsoft.com/library/system.xml.aspx)如需詳細資訊的命名空間[序列化](http://msdn.microsoft.com/library/system.xml.serialization.aspx)。 您也應該檢閱[Xamarin.iOS 文件](~/ios/deploy-test/linker.md)連結器 – 通常您需要新增`[Preserve]`屬性設定為您想要序列化的類別。

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>建立檔案和目錄

此範例示範如何使用`Environment`類別來存取文件 資料夾，我們可以在其中建立檔案和目錄。

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

建立目錄是一個非常類似的程序：

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

如需有關 System.IO 命名空間的詳細資訊，請參閱 < [MSDN 文件](http://msdn.microsoft.com/library/system.io.aspx)。


### <a name="serializing-json"></a>序列化的 Json

使用 Json 的 Xamarin.iOS 應用程式中的資料是很容易使用[Json.NET](http://www.newtonsoft.com/json)高效能的 JSON 架構適用於.NET NuGet 套件。 只要加入您的應用程式專案的 NuGet 套件： 

[![](file-system-images/json01.png "將 NuGet 套件新增至應用程式專案")](file-system-images/json01.png#lightbox)

接下來，將類別加入做為序列化/還原序列化的資料模型 (在此情況下`Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        #region Computed Properties
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }
        #endregion

        #region Constructors
        public Account() {

        }
        #endregion
    }
}
```

最後，建立的執行個體`Account`類別，請將其序列化為 json 資料，以及寫入檔案：

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```
Json.NET 的是指[文件](http://www.newtonsoft.com/json/help)如需有關使用.NET 應用程式中的 json 資料。

<a name="Special_Considerations" />

## <a name="special-considerations"></a>特殊的考量

儘管 Xamarin.iOS 和.NET 的檔案作業之間的相似性，iOS 和 Xamarin.iOS 與不同.NET 某些重要的方面。

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>讓專案檔可在執行階段存取

根據預設，如果您將檔案加入至您的專案，它便會將不會包含在最終的組件，並因此將無法使用您的應用程式。 若要包含在組件中的檔案，您必須標示為特殊的建置動作，稱為內容。

若要標記包含的檔案，以滑鼠右鍵按一下檔案，然後選擇 **建置動作&gt;內容**在 Visual Studio for mac。 您也可以變更**建置動作**中的檔案**屬性**工作表。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>區分大小寫

請務必了解 iOS 檔案系統*區分大小寫*。 這表示您的檔案和目錄名稱必須完全符合，– README.txt 及 readme.txt 就會被視為不同的檔案名稱。

這可能會造成混淆的.NET 開發人員比較熟悉 Windows 檔案系統中，也就是*不區分大小寫*– 「 檔案 」、 「 檔案 」 和 「 檔案 」 會全部參考相同的目錄。

因此，雖然 iOS 裝置會區分大小寫，您的程式碼應該寫入時，記住，的 iOS 模擬器不案例區分預設。 這表示如果您的檔案名稱大小寫不同檔案本身和它的參考，在程式碼中，您的程式碼仍可在模擬器中，但在實際裝置上將會失敗。 這是為什麼很重要部署到實際的裝置，及早且經常在 iOS 開發期間的原因之一。

 <a name="Path_Separator" />


### <a name="path-separator"></a>路徑分隔符號

iOS 使用正斜線 '/' 做為路徑分隔符號 (這是不同於 Windows，它會使用反斜線 ' \')。

由於這個令人混淆的差異，是很好的做法是使用`System.IO.Path.Combine`方法，以調整目前的平台，而不是硬式編碼為特定的路徑分隔符號。 這是簡單的步驟，使您的程式碼更容易移植到其他平台。

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>應用程式沙箱

您的應用程式存取檔案系統 （及其他資源，例如網路和硬體功能） 僅限於基於安全性考量。 這項限制就所謂*應用程式沙箱*。 檔案系統中，根據您的應用程式僅限於建立和刪除其主目錄中檔案和目錄。

主目錄會儲存您的應用程式及其所有資料的檔案系統中是唯一的位置。 您無法選擇 （或變更） 您應用程式的主目錄的位置不過 iOS 和 Xamarin.iOS 提供屬性和方法來管理檔案和目錄內。

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>應用程式套件組合

*應用程式套件組合*是包含您的應用程式的資料夾。
它是.app 尾碼為加入的目錄名稱來區別其他資料夾。 您的應用程式套件組合會包含您可執行檔和所有內容 （檔案、 影像等） 所需的專案。

當您瀏覽至您的應用程式套件組合，在 Mac OS 時，時，會顯示不同的圖示比您看到另一個目錄中 （和隱藏.app 尾碼）;不過，它是只要作業系統以不同方式顯示規則的目錄。

若要檢視的範例程式碼應用程式套件組合，以滑鼠右鍵按一下 Visual Studio 中的專案適用於 Mac 和選取**開啟包含的資料夾**。 然後瀏覽至**bin/Debug/** ，您應該會發現應用程式圖示 （類似於以下螢幕擷取畫面）。

 [![](file-system-images/40-bundle.png "瀏覽至 bin/Debug 尋找類似以下螢幕擷取畫面的應用程式圖示")](file-system-images/40-bundle.png#lightbox)

以滑鼠右鍵按一下此圖示，然後選擇 **檢視封裝內容**瀏覽應用程式套件組合目錄的內容。 內容顯示如同一般的目錄的內容，如下所示：

 [![](file-system-images/45-bundle.png "應用程式套件組合的內容")](file-system-images/45-bundle.png#lightbox)

應用程式套件組合安裝的項目在模擬器或裝置上測試期間，且最終仍是什麼應用程式市集中的包含提交給 Apple。

 <a name="Application_Directories" />


## <a name="application-directories"></a>應用程式目錄

在裝置上安裝您的應用程式時，作業系統會建立其主目錄，並將放在您的應用程式套件組合。 您的程式碼可以存取應用程式套件組合，來讀取資料，但不是應該寫入至該根目錄，以及已簽署的任何修改將使您的應用程式，並避免其啟動。

如此，則應該寫入至根目錄中，雖然<b>在 iOS 7 及更早版本中</b>建立數字中之目錄的應用程式的根目錄，可供使用。 <b>在 iOS 8 中的使用者可存取的目錄會<a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">找不到</a>內的應用程式根目錄</b>。

這些目錄和其用途如下：

&nbsp;

|Directory|描述|
|---|---|
|[ApplicationName].app/|**在 iOS 7 及更早版本中**這是`ApplicationBundle`儲存您的應用程式可執行檔的目錄。 您在您的應用程式中建立的目錄結構存在於此目錄 （例如，映像和其他您已標示為資源，在您的 Visual Studio for Mac 專案中的檔案類型）。<br /><br />如果您需要存取您的應用程式套件組合的內容檔案，此目錄的路徑是可透過`NSBundle.MainBundle.BundlePath`屬性。|
|文件 /|您可以使用此目錄來儲存使用者文件和應用程式資料檔案。<br /><br />此目錄的內容可供使用者透過 iTunes 檔案共用 （雖然這會停用預設值）。 新增`UIFileSharingEnabled`布林值機碼至 Info.plist 檔案，以允許使用者存取這些檔案。<br /><br />即使應用程式不立即啟用檔案共用，您應該避免放置應該從您在此目錄中的使用者隱藏的檔案 (例如資料庫檔案，除非您想要共用它們)。 機密檔案仍會是隱藏的因為這些檔案不會被公開 （和可能移動，修改或刪除由 iTunes） 如果在未來版本中已啟用檔案共用。<br /><br /> 您可以使用`Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)`方法來取得您的應用程式的文件目錄的路徑。<br /><br />此目錄的內容會由 iTunes。|
|程式庫 /|程式庫目錄是不直接由使用者，例如資料庫或其他應用程式所產生的檔案建立的檔案儲存的好地方。 使用者可透過 iTunes 永遠不會公開此目錄的內容。<br /><br />您可以在程式庫; 來建立您自己的子目錄不過，已經有某些系統建立目錄這裡，您應該留意，包括喜好設定和快取。<br /><br />此目錄 （除了 [快取] 子目錄中） 的內容會由 iTunes。 將備份您在程式庫中建立的自訂目錄。|
|喜好設定程式庫 / /|應用程式特定的喜好設定檔案會儲存在此目錄中。 請勿直接建立這些檔案。 請改用`NSUserDefaults`類別。<br /><br />此目錄的內容會由 iTunes。|
|快取程式庫 / /|儲存資料檔案，可協助您的應用程式的好地方執行，但就會輕鬆重新建立必要的快取目錄。 應用程式應該建立和刪除這些檔案，視需要以及能夠視需要重新建立這些檔案。 不過，它並不是應用程式執行時，iOS 5 可能也會刪除 （在極低的儲存體的情況下），這些檔案。<br /><br />此目錄的內容不會備份透過 iTunes，這表示它們不會出現，如果使用者會將裝置還原，和他們可能不存在之後安裝您的應用程式的更新的版本。<br /><br />比方說，如果您的應用程式無法連線到網路，您可能會使用快取目錄來儲存資料或檔案，可提供良好的離線體驗。 應用程式可以儲存及快速擷取這項資料，同時等候網路回應，但它不需要進行備份，可以輕鬆地復原或還原或版本更新之後重新建立。|
|tmp /|應用程式可以儲存只在短期間內，此目錄中所需的暫存檔案。 為了節省空間，應該在不再需要時刪除檔案。 當應用程式未在執行作業系統也可能會從這個目錄刪除檔案。<br /><br />此目錄的內容不會透過 iTunes 備份。<br /><br />比方說，tmp 目錄可能會用來儲存所下載的 （例如 Twitter 虛擬人偶或電子郵件附件），對使用者顯示，但一旦他們已被檢視 （並再次下載它們是否需要在未來無法刪除該暫存檔).|

這個螢幕擷取畫面會顯示在尋找工具視窗中的目錄結構：

 [![](file-system-images/08-library-directory.png "此螢幕擷取畫面顯示在搜尋工具視窗中的目錄結構")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>以程式設計方式存取其他目錄

存取舊版目錄和檔案範例`Documents`目錄。 要寫入至另一個目錄路徑，使用，您必須建構"..."語法，如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

建立目錄是非常類似：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

路徑`Caches`和`tmp`目錄可以建構如下：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>使用者已透過 iTunes 與共用檔案

使用者可以存取您的應用程式的文件目錄中的檔案，藉由編輯`Info.plist`並建立**應用程式支援 iTunes 分享**(`UIFileSharingEnabled`) 中的項目**來源** 檢視中，為如下所示：

 [![](file-system-images/09-uifilesharingenabled-plist.png "新增應用程式支援 iTunes 分享屬性")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

這些檔案可以存取在 iTunes 中，當裝置連線，並在使用者選擇`Apps` 索引標籤。比方說，下列螢幕擷取畫面會顯示檔案中所選的應用程式可透過 iTunes 共用：

 [![](file-system-images/10-itunes-file-sharing.png "這個螢幕擷取畫面顯示選取的應用程式可透過 iTunes 共用檔案")](file-system-images/10-itunes-file-sharing.png#lightbox)

使用者只能存取透過 iTunes 此目錄中的最上層項目。 （雖然它們可以將它們複製到他們的電腦，或刪除它們），他們無法看到任何子目錄的內容。 比方說，使用 GoodReader，PDF 和 EPUB 檔案可以共用應用程式，讓使用者可以讀取它們在其 iOS 裝置上。

如果使用者不小心，修改其文件 資料夾的內容的使用者可能會造成問題。 您的應用程式應該將這列入考量，而且可以具有恢復功能的文件 資料夾的破壞性更新。

這篇文章的範例程式碼會在文件 資料夾中建立檔案和資料夾 (在**SampleCode.cs**)，並啟用中的檔案共用**Info.plist**檔案。 此螢幕擷取畫面會顯示這些顯示在 iTunes 中的方式：

 [![](file-system-images/15-itunes-file-sharing-example.png "此螢幕擷取畫面顯示如何在 iTunes 中顯示的檔案")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)一文以了解如何設定應用程式及任何自訂文件類型的圖示您所建立。

如果`UIFileSharingEnabled`金鑰是 false 或不存在，則檔案共用時，預設會停用，而且使用者不能與您 Documentsdirectory 互動。

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>備份與還原

當裝置由 iTunes 時，將儲存在您的應用程式的主目錄中建立的所有目錄，但下列除外：

-   **[ApplicationName].app** – 應用程式套件組合*沒有*會加以備份，但僅限於組合已變更時 （應用程式更新已安裝，例如） 時。 總之，因為它已簽署，因此必須保留不動安裝之後，您不應該修改此目錄。 
-   **程式庫/快取**– 快取目錄適用於不需要備份的工作檔案。 
-   **tmp** – 此目錄用於建立和刪除不再需要時，暫存檔案或檔案，iOS 會刪除需要空間時。 


備份大量資料可能需要很長的時間。 如果您決定您需要備份任何特定文件或資料，您的應用程式應該只使用文件和程式庫的資料夾。 針對暫時性資料或從網路擷取的檔案，使用快取或 tmp 目錄。

請注意，iOS 會 'clean' 檔案系統裝置的磁碟空間嚴重不足執行時。 此程序會移除所有檔案程式庫/快取和 tmp 資料夾目前未執行的應用程式。

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>遵守 iOS5 iCloud 備份限制

導入的 Apple *iCloud 備份*ios 5 的功能。 啟用 iCloud 備份時，您的應用程式的主目錄中的所有檔案 (目錄不正常備份，例如，應用程式套件組合，範圍中排除`Caches`和`tmp`) 會備份到 iCloud 的伺服器。 這項功能會提供使用者的完整備份，萬一裝置遺失、 遭竊或損毀。

因為 iCloud 只提供給每位使用者 5 Gb 的 「 免費 」 的空間，並以避免不必要地使用頻寬，Apple 會預期應用程式，以只備份重要使用者產生的資料。 若要符合 iOS 資料儲存體的指導方針中，您應該限制由下列項目符合取得備份的資料量：

-  只會儲存使用者產生的資料或無法重新建立，在文件目錄 （也就是備份） 中的資料。 
-  儲存任何其他資料，可以輕鬆地重新建立，或重新下載`Library/Caches`或`tmp`（這不是備份和無法 '清除'）。 
-  如果您有可能適合的檔案`Library/Caches`或`tmp`資料夾，但您不想 '清除 '，將它們儲存到其他位置 (例如`Library/YourData`)，並套用' 執行備份向上 ' 屬性，以避免檔案用完 iCloud 備份 bandwidth 和儲存體空間。 因此您應該仔細管理它，並在刪除可能的話，這項資料仍會使用裝置上的空間。 

'不會重新啟動' 屬性設定使用`NSFileManager`類別。 請確定您的類別就`using Foundation`並呼叫`SetSkipBackupAttribute`如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

當`SetSkipBackupAttribute`是`true`將不會備份，不論它儲存在目錄的檔案 (即使`Documents`目錄)。 您可以查詢屬性使用`GetSkipBackupAttribute`方法，而且您可以重設它藉由呼叫`SetSkipBackupAttribute`方法`false`，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>共用資料之間 iOS 應用程式和應用程式擴充功能

因為主應用程式 （相對於其包含的應用程式） 的過程中執行應用程式擴充功能，共用資料不會自動包含讓所需的額外工作。 應用程式群組是機制 iOS 使用以允許不同的應用程式共用資料。 如果應用程式都已正確設定正確的權利和佈建，他們可以存取其正常的 iOS 沙箱外部共用的目錄。

### <a name="configure-an-app-group"></a>設定應用程式群組

使用設定共用的位置[應用程式群組](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)，在已設定**憑證、 識別碼與設定檔**區段[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/)。 此值也必須在每個專案的參考**Entitlements.plist**。

如需建立和設定 「 應用程式群組的資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)指南。

### <a name="files"></a>檔案

IOS 應用程式和擴充功能也可以共用使用常見的檔案路徑的檔案 (假設已正確設定正確的權利和佈建):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> 如果群組路徑傳回是`null`、 檢查權利和佈建設定檔的組態，並確定它們是否正確。


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>應用程式的版本更新

下載您的應用程式的新版本之後，iOS 會建立新的主目錄，並將它儲存新的應用程式套件組合。 iOS 然後從您的應用程式套件組合的舊版本，下列資料夾移動您新的主目錄：

-  **文件**
-  **程式庫**


其他目錄可能也之間複製並放在新的主目錄下，但它們不保證進行複製，讓您的應用程式不應依賴此系統行為。

<a name="Summary" />

## <a name="summary"></a>總結

本文說明檔案系統作業是使用 Xamarin.iOS 如同任何其他.NET 應用程式一樣簡單。 它也導入的應用程式沙箱，並檢查它所造成的安全性含意。 接下來，它會探索應用程式套件組合的概念。 最後，它會列舉可供您的應用程式特定的目錄，並說明其角色在應用程式升級和備份期間。


## <a name="related-links"></a>相關連結

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [檔案系統程式設計指南](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [註冊檔案類型的應用程式支援](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
