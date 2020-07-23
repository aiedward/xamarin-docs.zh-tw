---
title: Xamarin 中的檔案系統存取
description: 本檔說明如何在 Xamarin 中使用檔案系統。 其中討論目錄、讀取檔案、XML 和 JSON 序列化、應用程式沙箱、透過 iTunes 共用檔案等等。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: a552b7c395634c264114a378ee3f5c67d164d9f3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937952"
---
# <a name="file-system-access-in-xamarinios"></a>Xamarin 中的檔案系統存取

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

您可以使用 Xamarin 和 `System.IO` *.Net 基類庫（BCL）* 中的類別來存取 iOS 檔案系統。 `File` 類別可讓您建立、刪除和讀取檔案，而 `Directory` 類別可讓您建立、刪除或列舉目錄內容。 您也可以使用 `Stream` 子類別，以提供更高程度的檔案作業控制（例如壓縮或檔案內的位置搜尋）。

iOS 對應用程式可對檔案系統執行哪些動作，以保留應用程式資料的安全性，以及保護使用者不受惡性應用程式的限制。 這些限制是*應用程式沙箱*的一部分–一組規則，可限制應用程式對檔案、喜好設定、網路資源、硬體等的存取權。應用程式只能在其主目錄（安裝的位置）中讀取和寫入檔案;它無法存取另一個應用程式的檔案。

iOS 也有一些檔案系統特有的功能：某些目錄在備份和升級方面需要特別的處理，而且應用程式也可以與其他檔案和**檔案應用程式**共用檔案（自 iOS 11），以及透過 iTunes。

本文討論 iOS 檔案系統的功能和限制，並包含範例應用程式，示範如何使用 Xamarin 來執行一些簡單的檔案系統作業：

[![執行一些簡單檔案系統作業的 iOS 範例](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>一般檔案存取

在 ios 上，Xamarin 可讓您使用 .NET `System.IO` 類別來進行檔案系統作業。

下列程式碼片段說明一些常見的檔案作業。 您會在本文的範例應用程式中的**SampleCode.cs**檔案中找到這些檔案。

### <a name="working-with-directories"></a>使用目錄

這段程式碼會列舉目前目錄中的子目錄（由 "./" 參數指定），也就是應用程式可執行檔的位置。
您的輸出會是與您的應用程式一起部署的所有檔案和資料夾清單（當您進行調試時，會顯示在主控台視窗中）。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>讀取檔案

若要讀取文字檔，您只需要一行程式碼。 這個範例會在 [應用程式輸出] 視窗中顯示文字檔的內容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>XML 序列化

雖然使用完整的 `System.Xml` 命名空間已超出本文的範圍，但您可以使用類似以下的 StreamReader，輕鬆地從檔案系統還原序列化 XML 檔：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

如需詳細資訊，請參閱[System.Xml](xref:System.Xml)和[序列化](xref:System.Xml.Serialization)的檔。 請參閱連結器上的[Xamarin 檔](~/ios/deploy-test/linker.md)–通常您必須將 `[Preserve]` 屬性加入至您想要序列化的類別。

### <a name="creating-files-and-directories"></a>建立檔案和目錄

這個範例會示範如何使用 `Environment` 類別來存取可建立檔案和目錄的 [檔] 資料夾。

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

建立目錄是類似的程式：

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

如需詳細資訊，請參閱[SYSTEM.IO API 參考](xref:System.IO)。

### <a name="serializing-json"></a>序列化 JSON

[Json.NET](http://www.newtonsoft.com/json)是高效能的 Json 架構，適用于 Xamarin，並可在 NuGet 上取得。 使用 Visual Studio for Mac 中的 [**新增 nuget** ]，將 nuget 套件新增至您的應用程式專案：

[![將 NuGet 套件新增至應用程式專案](file-system-images/json01.png)](file-system-images/json01.png#lightbox)

接下來，新增類別作為序列化/還原序列化的資料模型（在此案例中為 `Account.cs` ）：

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

最後，建立類別的實例 `Account` ，將它序列化為 json 資料，並將它寫入檔案：

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

如需在 .NET 應用程式中使用 json 資料的詳細資訊，請參閱 Json 的[檔](https://www.newtonsoft.com/json/help)。

## <a name="special-considerations"></a>特殊考量

儘管在 Xamarin 和 .NET 檔案作業、iOS 和 Xamarin 之間有相似之處，但在某些重要的方面，iOS 與 .NET 的不同。

### <a name="making-project-files-accessible-at-runtime"></a>讓專案檔可在執行時間存取

根據預設，如果您將檔案新增至專案，它將不會包含在最終元件中，因此不會提供給您的應用程式使用。 若要在元件中包含檔案，您必須使用特殊的組建動作（稱為 Content）來標示檔案。

若要將檔案標示為包含，請在檔案上按一下滑鼠右鍵，然後選擇 [Visual Studio for Mac 中的 [**組建動作 &gt; 內容**]。 您也可以在檔案的**屬性**表中變更 [**建立] 動作**。

### <a name="case-sensitivity"></a>區分大小寫

請務必瞭解 iOS 檔案系統*區分大小寫*。 區分大小寫表示您的檔案和目錄名稱必須完全相符– **README.txt** ，且**readme.txt**會視為不同的檔案名。

對於較熟悉 Windows 檔案系統的 .NET 開發人員而言，這可能會造成混淆，但不*區分大小寫* **–檔案** **、檔案和檔案**全都參考相同的目錄。 **files**

> [!WARNING]
> IOS 模擬器不區分大小寫。
> 如果檔案本身和程式碼中的檔案名大小寫不同，則您的程式碼可能仍會在模擬器中使用，但在實際裝置上會失敗。 這是很重要的原因之一，那就是在 iOS 開發期間及早且經常在實際裝置上進行部署和測試。

### <a name="path-separator"></a>路徑分隔符號

iOS 使用正斜線 '/' 做為路徑分隔符號（這與使用反斜線 ' \ ' 的 Windows 不同）。

由於這種混亂的差異，因此最好使用 `System.IO.Path.Combine` 方法，它會針對目前平臺進行調整，而不是硬式編碼特定的路徑分隔符號。 這是一個簡單的步驟，可讓您的程式碼更容易移植到其他平臺。

## <a name="application-sandbox"></a>應用程式沙箱

您的應用程式對檔案系統（以及網路和硬體功能等其他資源）的存取會受到限制，因為安全性的緣故。 這種限制就稱為「*應用程式沙箱*」。 就檔案系統而言，您的應用程式只能用來建立和刪除其主目錄中的檔案和目錄。

主目錄是檔案系統中的唯一位置，其中儲存您的應用程式及其所有資料。 您無法選擇（或變更）應用程式主目錄的位置;不過，iOS 和 Xamarin 會提供屬性和方法來管理內的檔案和目錄。

## <a name="the-application-bundle"></a>應用程式套件組合

*應用程式*套件組合是包含您應用程式的資料夾。
它會與其他資料夾區別，其方式是將. 應用程式尾碼新增至目錄名稱。 您的應用程式套件組合包含您的可執行檔，以及您的專案所需的所有內容（檔案、影像等等）。

當您流覽至 Mac OS 中的應用程式套件組合時，它會顯示不同于您在其他目錄中看到的圖示（而且會隱藏**應用程式**尾碼）;不過，它只是作業系統顯示方式不同的一般目錄。

若要查看範例程式碼的應用程式套件組合，請以滑鼠右鍵按一下**Visual Studio for Mac**中的專案，然後選取 [**在搜尋工具中顯示**]。 然後流覽至您應該在其中找到應用程式圖示的**bin/** 目錄（如下列螢幕擷取畫面所示）。

![流覽 bin 目錄以尋找類似此螢幕擷取畫面的應用程式圖示](file-system-images/40-bundle.png)

在此圖示上按一下滑鼠右鍵，然後選擇 [**顯示套件內容**] 以流覽應用程式配套目錄的內容。 內容看起來就像一般目錄的內容一樣，如下所示：

[![應用程式套件組合的內容](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

應用程式套件組合是在測試期間安裝在模擬器或裝置上的內容，最後是提交給 Apple 以包含在 App Store 中的內容。

## <a name="application-directories"></a>應用程式目錄

當您的應用程式安裝在裝置上時，作業系統會為您的應用程式建立主目錄，並在應用程式根目錄中建立可供使用的一些目錄。 自 iOS 8 之後，使用者可存取的目錄[不會位於](https://developer.apple.com/library/ios/technotes/tn2406/_index.html)應用程式根目錄內，因此您無法從使用者目錄衍生應用程式套件組合的路徑，反之亦然。

這些目錄、如何判斷其路徑，以及其用途如下所示：

&nbsp;

|目錄|描述|
|---|---|
|[ApplicationName]. app/|**在 iOS 7 和更早版本中**，這是 `ApplicationBundle` 儲存應用程式可執行檔的目錄。 您在應用程式中建立的目錄結構存在於此目錄中（例如，您在 Visual Studio for Mac 專案中標示為資源的影像和其他檔案類型）。<br /><br />如果您需要存取應用程式套件組合內的內容檔案，可以透過屬性取得此目錄的路徑 `NSBundle.MainBundle.BundlePath` 。|
|單據|使用此目錄來儲存使用者檔和應用程式資料檔。<br /><br />此目錄的內容可供使用者透過 iTunes 檔案共用使用（雖然預設為停用）。 將 `UIFileSharingEnabled` 布林值索引鍵新增至 plist 檔案，以允許使用者存取這些檔案。<br /><br />即使應用程式不會立即啟用檔案共用，您仍應避免將應該在此目錄中的使用者隱藏的檔案（例如資料庫檔案）放入，除非您想要共用這些檔案。 只要機密檔案保持隱藏，如果未來版本中已啟用檔案共用，則這些檔案不會公開（且可能會在 iTunes 中移動、修改或刪除）。<br /><br /> 您可以使用 `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` 方法來取得應用程式之檔目錄的路徑。<br /><br />ITunes 會備份此目錄的內容。|
|機庫|程式庫目錄是儲存不是由使用者直接建立之檔案的好位置，例如資料庫或其他應用程式產生的檔案。 此目錄的內容永遠不會透過 iTunes 公開給使用者。<br /><br />您可以在程式庫中建立自己的子目錄;不過，這裡已經有一些系統建立的目錄，您應該要注意，包括喜好設定和快取。<br /><br />ITunes 會備份此目錄的內容（[快取] 子目錄除外）。 您在程式庫中建立的自訂目錄將會進行備份。|
|媒體櫃/喜好設定/|應用程式特定的喜好設定檔案會儲存在這個目錄中。 請勿直接建立這些檔案。 請改用 `NSUserDefaults` 類別。<br /><br />ITunes 會備份此目錄的內容。|
|程式庫/快取/|[快取] 目錄是儲存資料檔案的好地方，可協助您的應用程式執行，但可輕鬆地重新建立。 應用程式應該視需要建立和刪除這些檔案，並能夠在必要時重新建立這些檔案。 iOS 5 也可能會刪除這些檔案（在儲存空間不足的情況下），但在應用程式執行時不會這麼做。<br /><br />ITunes 不會備份此目錄的內容，這表示如果使用者還原裝置，它們將不會出現，而且在安裝應用程式的更新版本之後可能不會顯示。<br /><br />比方說，如果您的應用程式無法連線到網路，您可以使用 [快取] 目錄來儲存資料或檔案，以提供良好的離線體驗。 應用程式可以在等候網路回應時快速儲存和抓取此資料，但不需要進行備份，而且可以在還原或版本更新之後輕鬆地復原或重新建立。|
|.tmp|應用程式可以在此目錄中儲存只需要短時間的暫存檔案。 若要節省空間，當不再需要檔案時，應該刪除檔案。 當應用程式未執行時，作業系統也可能會從這個目錄刪除檔案。<br /><br />ITunes 不會備份此目錄的內容。<br /><br />例如，tmp 目錄可能用來儲存下載以顯示給使用者的暫存檔（例如 Twitter 虛擬人偶或電子郵件附件），但在流覽後（如果日後需要，再次下載）可能會予以刪除。|

這個螢幕擷取畫面顯示搜尋工具視窗中的目錄結構：

[![這個螢幕擷取畫面顯示搜尋工具視窗中的目錄結構](file-system-images/08-library-directory.png)](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>以程式設計方式存取其他目錄

先前的目錄和檔案範例會存取 `Documents` 目錄。 若要寫入另一個目錄，您必須使用 ".." 語法來建立路徑，如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

建立目錄類似：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

`Caches`和目錄的路徑 `tmp` 可以如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>與檔案應用程式共用

iOS 11 引進檔案**應用程式**-適用于 iOS 的檔案瀏覽器，可讓使用者在 iCloud 中查看其檔案並與其互動，也會由任何支援它的應用程式儲存。 若要允許使用者直接存取您應用程式中的檔案，請在**plist**檔案中建立新的布林值索引鍵， `LSSupportsOpeningDocumentsInPlace` 並將它設定為 `true` ，如下所示：

![在 Info. plist 中設定 LSSupportsOpeningDocumentsInPlace](file-system-images/51-supports-opening.png)

應用程式的**Documents**目錄現在可在**檔案應用程式**中流覽。 在 [**檔案**] 應用程式中，流覽至 [**我的 iPhone] 上**的，並顯示每個具有共用檔案的應用程式。 下列螢幕擷取畫面顯示[FileSystem 範例應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)看起來的樣子：

![iOS 11 檔案應用程式](file-system-images/50-files-app-1-sml.png) ![流覽我的 iPhone 檔案](file-system-images/50-files-app-2-sml.png) ![範例應用程式檔](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>透過 iTunes 與使用者共用檔案

使用者可以在 `Info.plist` 來源視圖中編輯並建立**支援 iTunes 共用**（）專案的應用程式，以存取應用程式檔目錄中的檔案 `UIFileSharingEnabled` ，如下所示： **Source**

[![新增應用程式支援 iTunes 共用屬性](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

當裝置連線且使用者選擇索引標籤時，可以在 iTunes 中存取這些檔案 `Apps` 。例如，下列螢幕擷取畫面顯示所選取應用程式中透過 iTunes 共用的檔案：

[![此螢幕擷取畫面顯示所選取應用程式中透過 iTunes 共用的檔案](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

使用者只能透過 iTunes 存取此目錄中的最上層專案。 他們無法看到任何子目錄的內容（雖然它們可以將它們複製到電腦或刪除它們）。 例如，使用 GoodReader，PDF 和 EPUB 檔案可以與應用程式共用，讓使用者可以在其 iOS 裝置上讀取這些檔案。

如果使用者不小心，修改 [檔] 資料夾的內容會造成問題。 您的應用程式應該將這一點納入考慮，並且能夠復原檔資料夾的破壞性更新。

本文的範例程式碼會同時在 [檔] 資料夾中建立檔案和資料夾（在**SampleCode.cs**中），並在**plist**檔案中啟用檔案共用。 此螢幕擷取畫面顯示這些在 iTunes 中的顯示方式：

[![此螢幕擷取畫面顯示檔案在 iTunes 中的顯示方式](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

如需如何為應用程式和您所建立的任何自訂檔案類型設定圖示的相關資訊，請參閱[使用影像](~/ios/app-fundamentals/images-icons/index.md)一文。

如果機 `UIFileSharingEnabled` 碼為 false 或不存在，則檔案共用預設為停用，使用者將無法與您的檔目錄互動。

## <a name="backup-and-restore"></a>備份與還原

當 iTunes 備份裝置時，將會儲存在應用程式的主目錄中建立的所有目錄，但下列目錄除外：

- **[ApplicationName]. app** –不寫入此目錄，因為它已簽署，因此在安裝後必須維持不變。 它可能包含您從程式碼存取的資源，但它們不需要備份，因為它們會藉由重新下載應用程式來還原。
- 連結**庫/** 快取–快取目錄適用于不需要備份的工作檔案。
- **tmp** –此目錄適用于不再需要時所建立和刪除的暫存檔案，或 iOS 在需要空間時所刪除的檔案。

備份大量資料可能需要很長的時間。 如果您決定需要備份任何特定檔或資料，您的應用程式應該使用 [檔] 和 [文件庫] 資料夾。 對於可從網路輕鬆抓取的暫時性資料或檔案，請使用快取或 tmp 目錄。

> [!NOTE]
> 當裝置執行的磁碟空間嚴重不足時，iOS 會「清理」檔案系統。
> 此程式將會從目前未執行之應用程式的 [程式庫/快取] 和 [tmp] 資料夾中移除所有檔案。

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>遵守 iOS 5 iCloud 備份限制

> [!NOTE]
> 雖然這是 iOS 5 首度引進的原則（這似乎很長一段時間），但本指南仍會與目前的應用程式相關。

Apple 引進了與 iOS 5 的*ICloud 備份*功能。 當 iCloud 備份啟用時，您應用程式主目錄中的所有檔案（不包括通常不會備份的目錄，例如應用程式套件組合、 `Caches` 和 `tmp` ）都會備份到 iCloud 伺服器。 這項功能可讓使用者在裝置遺失、遭竊或損毀時，擁有完整的備份。

因為 iCloud 只會為每位使用者提供 5 Gb 的可用空間，並避免不必要地使用頻寬，所以 Apple 預期應用程式只會備份重要的使用者所產生的資料。 若要遵守 iOS 資料儲存指導方針，您應該藉由遵循下列專案來限制備份的資料量：

- 在 [檔] 目錄（已備份）中，只儲存使用者產生的資料或無法重新建立的資料。
- 儲存可以在或中輕鬆重新建立或重新下載的任何其他資料 `Library/Caches` `tmp` （不會備份，也可以「已清理」）。
- 如果您的檔案可能適用于 `Library/Caches` 或 `tmp` 資料夾，但您不想要「清理」，請將其儲存在其他位置（例如）， `Library/YourData` 並套用「不要備份」屬性，以防止檔案使用 iCloud 備份頻寬和儲存空間。 此資料仍會使用裝置上的空間，因此您應該小心管理它，並盡可能刪除它。

「不要備份」屬性是使用類別來設定 `NSFileManager` 。 請確定您的類別為 `using Foundation` ，並呼叫 `SetSkipBackupAttribute` 如下：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

當 `SetSkipBackupAttribute` 為時 `true` ，將不會備份檔案，不論其儲存在哪個目錄（甚至是 `Documents` 目錄）。 您可以使用方法來查詢屬性 `GetSkipBackupAttribute` ，也可以使用呼叫方法來重設它 `SetSkipBackupAttribute` `false` ，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>在 iOS 應用程式與應用程式擴充功能之間共用資料

由於應用程式延伸模組會當做主應用程式（相對於其包含的應用程式）的一部分來執行，因此不會自動包含共用資料，因此需要額外的工作。 應用程式群組是 iOS 用來允許不同應用程式共用資料的機制。 如果應用程式已正確設定正確的權利和布建，他們就可以存取其一般 iOS 沙箱以外的共用目錄。

### <a name="configure-an-app-group"></a>設定應用程式群組

共用位置是使用[應用程式群組](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)進行設定，此群組是在[IOS 開發人員中心](https://developer.apple.com/devcenter/ios/)的 [**憑證]、[識別碼 & 設定檔**] 區段中設定。 您也必須在每個專案的權利中參考此值**plist**。

如需建立和設定應用程式群組的相關資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)指南。

### <a name="files"></a>檔案儲存體

IOS 應用程式和延伸模組也可以使用通用檔案路徑來共用檔案（假設它們已正確設定正確的權利和布建）：

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> 如果傳回的群組路徑是 `null` ，請檢查權利和布建設定檔的設定，並確定其正確無誤。

## <a name="application-version-updates"></a>應用程式版本更新

下載應用程式的新版本時，iOS 會建立新的主目錄，並在其中儲存新的應用程式套件組合。 然後 iOS 會將下列資料夾從舊版的應用程式套件組合移至新的主目錄：

- **文件**
- **程式庫**

其他目錄也可以複製並放在新的主目錄底下，但不保證會複製它們，因此您的應用程式不應該依賴此系統行為。

## <a name="summary"></a>總結

本文說明了 Xamarin 的檔案系統作業與任何其他 .NET 應用程式類似。 它也引進了應用程式沙箱，並檢查它所造成的安全性含意。 接下來，它會探索應用程式套件組合的概念。 最後，它會列舉應用程式可用的特製化目錄，並在應用程式升級和備份期間說明其角色。

## <a name="related-links"></a>相關連結

- [檔案系統範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)
- [檔案系統程式設計指南](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [註冊應用程式支援的檔案類型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
