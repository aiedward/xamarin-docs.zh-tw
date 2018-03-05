---
title: "使用檔案系統"
description: "Xamarin.iOS 可以使用相同的 System.IO 類別，才能使用，您會使用任何.NET 應用程式在 iOS 中檔案和目錄。 不過，儘管熟悉類別和方法，iOS 可以建立或存取的檔案會實作一些限制，而且也會提供特殊功能為特定目錄。 本文概述這些限制與功能，並示範檔案存取 Xamarin.iOS 應用程式中的運作方式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: a2c3ce9e19340067d77a8bc131b5a247806ecfa1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-file-system"></a>使用檔案系統

_Xamarin.iOS 可以使用相同的 System.IO 類別，才能使用，您會使用任何.NET 應用程式在 iOS 中檔案和目錄。不過，儘管熟悉類別和方法，iOS 可以建立或存取的檔案會實作一些限制，而且也會提供特殊功能為特定目錄。本文概述這些限制與功能，並示範檔案存取 Xamarin.iOS 應用程式中的運作方式。_

您可以使用 Xamarin.iOS 和`System.IO`中的類別*.NET 基底類別程式庫 (BCL)*存取 iOS 檔案系統。 `File`類別可讓您建立、 刪除和讀取檔案，而`Directory`類別可讓您建立、 刪除或列舉的目錄內容。 您也可以使用`Stream`子類別，可提供更大的控制權檔案作業 （例如壓縮或位置的檔案內搜尋）。

iOS 會有些限制，應用程式可以做為檔案系統來保留的應用程式資料的安全性，以及保護使用者免於惡意應用程式。 這些限制屬於*應用程式沙箱*– 一組規則，以限制應用程式的存取權的檔案喜好設定、 網路資源、 硬體、 等等。應用程式僅限於讀取和寫入其主目錄 （安裝位置;） 中的檔案它無法存取另一個應用程式的檔案。

iOS 也有一些檔案系統的特定功能： 特定目錄需要特殊處理相對於備份和升級，而且應用程式也可以共用透過 iTunes 檔案。

這篇文章討論的功能和限制的 ios 檔案系統中的詳細資料，並包括範例應用程式，示範如何使用 Xamarin.iOS 執行一些簡單的檔案系統作業：

 [ ![](file-system-images/05-sampleapp.png "IOS 執行一些簡單的檔案系統作業的範例")](file-system-images/05-sampleapp.png)

 <a name="General_File_Access" />


## <a name="general-file-access"></a>一般檔案存取

Xamarin.iOS 可讓您使用.NET`System.IO`在 iOS 上的檔案系統作業的類別。

下列程式碼片段將說明一些常見的檔案作業。 您會發現在其下方所有`SampleCode.cs`檔案，請在這個發行項的範例應用程式。

 <a name="Working_with_directories" />


### <a name="working-with-directories"></a>使用目錄

此程式碼會列舉在目前的目錄的子目錄 (所指定 「。 /"參數)，這是您應用程式可執行檔的位置。
您的輸出會是一份所有檔案和資料夾 （顯示在主控台視窗進行偵錯時） 的應用程式使用部署。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>讀取檔案

若要讀取的文字檔案，您只需要一行程式碼。 這個範例會顯示在 [應用程式輸出] 視窗的文字檔的內容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>XML 序列化

雖然使用完整`System.Xml`命名空間已超出本文的範圍，輕鬆地將使用像這樣的 StreamReader 從檔案系統的 XML 文件還原序列化：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

請參閱 MSDN 文件[System.Xml](http://msdn.microsoft.com/en-us/library/system.xml.aspx)如需詳細資訊的命名空間[序列化](http://msdn.microsoft.com/en-us/library/system.xml.serialization.aspx)。 您也應該檢閱[Xamarin.iOS 文件](~/ios/deploy-test/linker.md)連結器 – 通常您會需要加入`[Preserve]`屬性設定為您想要序列化的類別。

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>建立檔案和目錄

這個範例示範如何使用`Environment`類別來存取文件 資料夾，我們可以在其中建立檔案和目錄。

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

如需 System.IO 命名空間的詳細資訊，請參閱[MSDN 文件](http://msdn.microsoft.com/en-us/library/system.io.aspx)。


### <a name="serializing-json"></a>序列化 Json

搭配使用 Json Xamarin.iOS 應用程式中的資料是很容易使用[Json.NET](http://www.newtonsoft.com/json)高效能 JSON framework for.NET NuGet Package。 只要加入 NuGet 套件加入您的應用程式專案： 

[ ![](file-system-images/json01.png "將 NuGet 封裝加入至應用程式專案")](file-system-images/json01.png)

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

最後，建立的執行個體`Account`類別、 將其序列化為 json 資料並將它寫入至檔案：

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
請參閱 Json.NET 的[文件](http://www.newtonsoft.com/json/help)如需有關使用.NET 應用程式中的 json 資料。

<a name="Special_Considerations" />

## <a name="special-considerations"></a>特殊考量

儘管 Xamarin.iOS 和.NET 檔案作業之間的相似性，iOS 和 Xamarin.iOS.NET 中不同一些重要的方法。

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>進行專案檔在執行階段存取

根據預設，如果您將檔案加入至您的專案，它便會將不會包含最後的組件，並因此將無法使用您的應用程式。 若要包含在組件中的檔案，您必須使用特殊的組建巨集，稱為內容標示。

若要將標示要納入的檔案，以滑鼠右鍵按一下檔案，然後選擇 **建置動作&gt;內容**在 Visual Studio for mac。 您也可以變更**建置動作**中檔案的**屬性**工作表。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>區分大小寫

請務必了解 iOS 檔案系統是*區分大小寫*。 這表示您的檔案和目錄名稱必須完全符合，– README.txt 和 readme.txt 會被視為不同的檔名。

這可能會造成混淆的.NET 開發人員更熟悉 Windows 檔案系統中，也就是*不區分大小寫*– 「 檔案 」、 「 檔案 」 和 「 檔案 」 會參照相同的目錄。

因此，雖然 iOS 裝置會區分大小寫，程式碼應該寫入時，請注意，在 iOS 模擬器是不案例區分預設。 這表示如果您的檔案名稱大小寫不同檔案本身以及它的參考程式碼中，您的程式碼仍可在模擬器中，但在實際裝置上將會失敗。 這是它為何如此重要部署至實際裝置及早且經常在 iOS 開發期間的原因之一。

 <a name="Path_Separator" />


### <a name="path-separator"></a>路徑分隔符號

iOS 會使用正斜線 '/' 做為路徑分隔符號 (不同於 Windows，會使用反斜線 ' \')。

由於這會造成混淆的差異，是很好的作法是將`System.IO.Path.Combine`方法，以調整為目前的平台，而非硬特定路徑分隔符號。 這是簡單的步驟，使您的程式碼移植到其他平台。

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>沙箱應用程式

基於安全性理由則您的應用程式存取檔案系統 （和其他資源，例如網路和硬體功能）。 這項限制就稱為*應用程式沙箱*。 檔案系統中，根據您的應用程式會限制建立和刪除其主目錄中檔案和目錄。

主目錄儲存您的應用程式及其所有資料的檔案系統中是唯一的位置。 您無法選擇 （或變更） 您的應用程式; 主目錄的位置不過 iOS 和 Xamarin.iOS 提供屬性和方法，以管理檔案和目錄內。

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>應用程式套件組合

*應用程式套件組合*是包含您的應用程式的資料夾。
它是具有.app 後置詞加入至目錄名稱來區別其他資料夾。 您的應用程式套件組合會包含可執行檔及所有內容 （檔案、 影像等等） 所需的專案。

當您瀏覽至您的應用程式套件組合，在 Mac OS 時，它會出現以不同的圖示比您看到另一個目錄中 （和隱藏.app 尾碼）;不過，它是只以不同方式顯示作業系統的規則目錄。

若要檢視的範例程式碼應用程式套件組合，以滑鼠右鍵按一下 Visual Studio 中的專案上的 Mac，並選取**開啟包含資料夾**。 瀏覽至**bin/Debug/**您應該在此找到應用程式圖示 （類似於以下螢幕擷取畫面）。

 [ ![](file-system-images/40-bundle.png "巡覽至 bin/Debug 尋找應用程式圖示類似這個螢幕擷取畫面")](file-system-images/40-bundle.png)

此圖示上按一下滑鼠右鍵，然後選擇 **檢視封裝內容**瀏覽應用程式套件組合目錄的內容。 內容顯示如同一般的目錄中，內容如下所示：

 [ ![](file-system-images/45-bundle.png "應用程式套件組合的內容")](file-system-images/45-bundle.png)

應用程式配套就是在模擬器或裝置上安裝在測試期間，以及最終仍是以包含在應用程式市集提交 Apple。

 <a name="Application_Directories" />


## <a name="application-directories"></a>應用程式目錄

在裝置上安裝應用程式時，作業系統就會建立其主目錄，並將放在您的應用程式套件組合。 您的程式碼可以存取應用程式套件組合，來讀取資料，但不是應該寫入至根目錄中，以及已簽署的任何修改將使您的應用程式，並防止它啟動。

因此，雖然不應撰寫成根目錄，但<b>iOS 7 及舊版中</b>建立數個應用程式根目錄中可供使用的目錄。 <b>在 iOS 8 的使用者可存取的目錄是<a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">不位於</a>應用程式根目錄內</b>。

以下列出這些目錄和其用途：

&nbsp;

<table>
  <tbody>
    <tr>
      <td>
Directory </td>
      <td>
描述 </td>
    </tr>
    <tr>
      <td>
        <p>[ApplicationName].app/</p>
      </td>
      <td>
        <p><b>在 iOS 7 及更早版本中</b>這是<code>ApplicationBundle</code>儲存您的應用程式可執行檔的目錄。 您在您的應用程式中建立的目錄結構存在於此目錄 （例如，映像和其他已標示為資源在您 Mac 專案的 Visual Studio 中的檔案類型）。</p>
        <p>如果您需要存取您的應用程式套件組合中的內容檔案，此目錄的路徑是可透過使用<code>NSBundle.MainBundle.BundlePath</code>屬性。</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>文件 /</p>
      </td>
      <td>
        <p>使用此目錄以儲存使用者文件和應用程式資料檔案。</p>
        <p>此目錄的內容可提供給使用者透過 iTunes 檔案共用 （雖然這會停用預設值）。 新增<code>UIFileSharingEnabled</code>布林金鑰至 Info.plist 檔案，以允許使用者存取這些檔案。</p>
        <p>即使應用程式不立即啟用檔案共用，您應該避免將應向使用者在此目錄中隱藏的檔案 (例如資料庫檔案，除非您想要分享)。 機密檔案仍然保持隱藏狀態，因為這些檔案將不會公開 （和可能移動、 修改或刪除由 iTunes） 如果檔案共用的未來版本中已啟用。</p>
        <p>您可以使用<code>Environment.GetFolderPath
(Environment.SpecialFolder.MyDocuments)</code>方法來取得您的應用程式的文件目錄的路徑。</p>
        <p>此目錄的內容所備份的 iTunes。</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>程式庫 /</p>
      </td>
      <td>
        <p>程式庫目錄是不直接由使用者，例如資料庫或其他應用程式所產生的檔案建立的檔案儲存的好地方。
使用者可透過 iTunes 永遠不會公開此目錄的內容。</p>
        <p>您可以在程式庫; 中建立您自己的子目錄不過，已有一些系統建立目錄這裡，您應該注意，其中包括喜好設定和快取。</p>
        <p>此目錄 （除了的快取子目錄） 的內容所備份的 iTunes。 將備份您在程式庫中建立的自訂目錄。</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>喜好設定程式庫 / /</p>
      </td>
      <td>
        <p>特定應用程式喜好設定的檔案會儲存在此目錄。 請勿直接建立這些檔案。 請改用<code>NSUserDefaults</code>類別。</p>
        <p>此目錄的內容所備份的 iTunes。</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Library/Caches/</p>
      </td>
      <td>
        <p>快取目錄是儲存資料檔案，可協助您的應用程式的好地方執行，但是可以輕鬆地重新建立必要的。 應用程式，建立和刪除這些檔案，視需要可以視需要重新建立這些檔案。 iOS 5 可能也會刪除這些檔案 （在非常低的存放裝置的情況下），但它並不是在執行應用程式。</p>
        <p>此目錄的內容不會備份由 iTunes，這表示它們不會出現，如果使用者會將裝置還原，及它們可能不存在您的應用程式的更新的版本安裝之後。</p>
        <p>比方說，如果您的應用程式無法連線到網路，您可以使用快取目錄來儲存資料或檔案，以提供良好的離線體驗。 應用程式可以儲存及快速擷取這項資料，等待網路回應，但它不需要進行備份，可輕鬆地復原或還原或版本更新之後重新建立。</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>tmp/</p>
      </td>
      <td>
        <p>應用程式可以儲存在此目錄中短暫的時間才會需要的暫存檔案。 為了節省空間，應該已不再需要時刪除檔案。 當應用程式未在執行作業系統也可能會從這個目錄刪除檔案。</p>
        <p>此目錄的內容不會由 iTunes 備份。</p>
        <p>例如，tmp 目錄可能會用來儲存暫存檔 （例如 Twitter 虛擬人偶或電子郵件附件），對使用者顯示的已下載，但他們已被檢視 （並如有必要在未來再次下載之後，就無法刪除的).</p>
      </td>
    </tr>
  </tbody>
</table>

這個螢幕擷取畫面會顯示目錄結構中尋找工具視窗：

 [ ![](file-system-images/08-library-directory.png "這個螢幕擷取畫面顯示的目錄結構中尋找工具視窗")](file-system-images/08-library-directory.png)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>以程式設計方式存取的其他目錄

存取舊版目錄和檔案範例`Documents`目錄。 要寫入另一個目錄路徑，使用，您必須建構".."語法，如下所示：

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


## <a name="sharing-files-with-the-user-through-itunes"></a>使用者已透過 iTunes 以共用檔案

使用者可以存取您的應用程式的文件目錄中的檔案，藉由編輯`Info.plist`和建立**應用程式支援 iTunes 共用**(`UIFileSharingEnabled`) 中的項目**來源** 檢視中，為如下所示：

 [ ![](file-system-images/09-uifilesharingenabled-plist.png "新增應用程式支援 iTunes 共用屬性")](file-system-images/09-uifilesharingenabled-plist.png)

這些檔案可以存取在 iTunes，當裝置連線，並在使用者選擇`Apps` 索引標籤。例如，下列螢幕擷取畫面顯示選取的應用程式可透過 iTunes 共用檔案：

 [ ![](file-system-images/10-itunes-file-sharing.png "這個螢幕擷取畫面顯示選取的應用程式可透過 iTunes 共用檔案")](file-system-images/10-itunes-file-sharing.png)

使用者只能存取透過 iTunes 此目錄中的最上層項目。 （不過它們可以將它們複製到他們的電腦，或刪除） 無法查看的任何子目錄的內容。 例如，與 GoodReader，PDF 和 EPUB 檔案可共用與應用程式，讓使用者可以讀取它們在其 iOS 裝置上。

如果它們不小心，修改其文件 資料夾的內容的使用者可能會造成問題。 您的應用程式應該將這點納入考量，並能彈性處理的文件 資料夾的破壞性更新。

這篇文章的範例程式碼文件資料夾中建立檔案和資料夾 (在**SampleCode.cs**)，並讓檔案共用中**Info.plist**檔案。 這個螢幕擷取畫面顯示這些 iTunes 中顯示的方式：

 [ ![](file-system-images/15-itunes-file-sharing-example.png "這個螢幕擷取畫面顯示如何將檔案出現在 iTunes")](file-system-images/15-itunes-file-sharing-example.png)

請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)文件以取得您所建立的資訊關於如何設定應用程式的任何自訂的文件類型的圖示。

如果`UIFileSharingEnabled`金鑰為 false 或不存在，則檔案共用，根據預設，停用以及使用者不能與您 Documentsdirectory 互動。

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>備份與還原

當裝置由 iTunes 備份時，在您的應用程式的主目錄中建立的所有目錄將都儲存但下列除外：

-   **[ApplicationName].app** – 應用程式套件組合*沒有*加以備份，但僅限於組合已變更 （應用程式安裝更新時，例如） 時。 總之，因為它已簽署，因此必須維持不變安裝之後，就不應該修改這個目錄。 
-   **程式庫/快取**– 快取目錄適用於不需要備份的工作檔案。 
-   **tmp** – 此目錄用於建立和刪除不再需要時，暫存檔案，或該 iOS 刪除需要空間時的檔案。 


備份大量資料可能需要很長的時間。 如果您決定您需要備份任何特定的文件或資料，您的應用程式應該只使用文件和程式庫的資料夾。 暫時性資料或從網路擷取的檔案，請使用快取或 tmp 目錄。

請注意，iOS 會 「 清除 」 檔案系統裝置的磁碟空間嚴重不足執行時。 此程序會移除所有檔案的程式庫/快取和 tmp 目前未執行的應用程式的資料夾。

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>遵守 iOS5 iCloud 備份限制

導入的 Apple *iCloud 備份*iOS 5 的功能。 啟用 iCloud 備份時，您的應用程式的主目錄中的所有檔案 (目錄不正常備份，例如，應用程式套件組合，範圍中排除`Caches`和`tmp`) 會備份到 iCloud 的伺服器。 這項功能的完整備份的使用者可在提供他們的裝置遺失、 遭竊或損毀。

由於 iCloud 只提供給每個使用者 5 Gb 的 'free' 空間，而且若要避免不必要地使用頻寬，Apple 預期只備份基本使用者產生資料的應用程式。 若要符合 iOS 資料存放區的指導方針中，您應該限制取得備份，藉由下列項目支援的資料量：

-  只儲存使用者產生的資料，或是資料無法重新建立，在文件目錄 （這是備份）。 
-  儲存任何其他的資料可以輕鬆地重新建立，或在重新下載`Library/Caches`或`tmp`（不是備份，以及無法 '清除'）。 
-  如果您有可能適用於檔案`Library/Caches`或`tmp`資料夾，但您不想要 '清除'，將它們儲存到其他位置 (例如`Library/YourData`)，並套用 '執行備份向上' 屬性，以防止使用向上箭號 iCloud 備份 ba 檔案ndwidth 和儲存空間。 因此請謹慎管理它，並刪除它時可能，這項資料仍會使用裝置上的空間。 

'不是執行回向上' 屬性設定使用`NSFileManager`類別。 請確定您的類別是`using Foundation`呼叫`SetSkipBackupAttribute`如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

當`SetSkipBackupAttribute`是`true`檔案就不會備份，不論它儲存在目錄 (即使`Documents`目錄)。 您可以查詢屬性使用`GetSkipBackupAttribute`方法，而且您可以將其重設呼叫`SetSkipBackupAttribute`方法`false`，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>共用資料之間 iOS 應用程式和應用程式擴充功能

主機應用程式 （相對於其包含的應用程式） 的過程中執行應用程式擴充功能，因為共用資料不是自動包含因此需要額外的工作。 應用程式群組是可讓共用資料的不同應用程式使用的機制 iOS。 如果應用程式已正確設定與正確的權利，並佈建，他們可以存取其正常 iOS 沙箱外部共用的目錄。

### <a name="configure-an-app-group"></a>設定應用程式群組

使用設定的共用的位置[應用程式群組](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)，該服務設定中**憑證、 識別項與設定檔**區段[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/)。 這個值也必須在每個專案的參考**Entitlements.plist**。

如需建立及設定應用程式群組資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)指南。

### <a name="files"></a>檔案

IOS 應用程式和延伸模組也可以共用檔案使用共通的檔案路徑 (指定已正確設定與正確的權利，並佈建):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> **注意：**如果群組路徑傳回為`null`，檢查權利和佈建設定檔的組態，並確定它們是否正確。


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>應用程式的版本更新

下載您的應用程式的新版本之後，iOS 會建立新的主目錄，並將它儲存新的應用程式套件組合。 iOS 然後從您的應用程式套件組合的先前版本，下列資料夾移動您新的主目錄：

-  **文件**
-  **程式庫**


其他目錄可能也在複製並放在新的主目錄，但它們不保證複製，因此您的應用程式不應依賴此系統行為。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章會顯示檔案系統作業是使用 Xamarin.iOS 如同任何其他.NET 應用程式一樣簡單。 它也導入沙箱應用程式，並檢查它所造成的安全性含意。 接下來，它會探索應用程式套件組合的概念。 最後，它會列舉應用程式可以使用特定的目錄，並說明其角色在應用程式升級和備份期間。


## <a name="related-links"></a>相關連結

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [檔案系統程式設計指南](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [註冊檔案的應用程式支援的型別](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
