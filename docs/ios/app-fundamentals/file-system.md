---
title: Xamarin 中的檔案系統存取
description: 本檔說明如何在 Xamarin 中使用檔案系統。 它會討論目錄、讀取檔案、XML 和 JSON 序列化、應用程式沙箱、透過 iTunes 共用檔案等等。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: ffb49329b38705d097520b24d53285d5dbf15167
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371803"
---
# <a name="file-system-access-in-xamarinios"></a>Xamarin 中的檔案系統存取

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/ios-samples/filesystemsamplecode)

您可以使用 Xamarin 和 `System.IO` .Net 基類庫中的類別 *(BCL)* 來存取 iOS 檔案系統。 `File` 類別可讓您建立、刪除和讀取檔案，而 `Directory` 類別可讓您建立、刪除或列舉目錄內容。 您也可以使用 `Stream` 子類別，這可以提供更高程度的檔案作業控制 (例如，壓縮或在檔案) 內搜尋位置。

iOS 強加一些限制，可讓應用程式在檔案系統上做什麼，以保存應用程式資料的安全性，並保護使用者免于惡性的應用程式。 這些限制是 *Application Sandbox* 的一部分，這是一組規則，可限制應用程式對檔案、喜好設定、網路資源、硬體等的存取。應用程式只能在其主目錄中讀取和寫入檔案， (安裝的位置) ;它無法存取另一個應用程式的檔案。

iOS 也有一些檔案系統特有的功能：某些目錄需要特別處理備份和升級，而應用程式也可以與彼此共用檔案，以及從 iOS 11) 和 iTunes ( **檔案應用程式** 。

本文討論 iOS 檔案系統的功能和限制，並包含範例應用程式，示範如何使用 Xamarin 執行一些簡單的檔案系統作業：

[![執行一些簡單檔案系統作業的 iOS 範例](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>一般檔案存取

Xamarin 可讓您 `System.IO` 針對 ios 上的檔案系統作業使用 .net 類別。

下列程式碼片段說明一些常見的檔案作業。 您會在本文的範例應用程式中的 **SampleCode.cs** 檔案中找到這些專案。

### <a name="working-with-directories"></a>使用目錄

此程式碼會列舉 "./" 參數所指定之目前目錄中的子目錄 () ，也就是應用程式可執行檔的位置。
您的輸出將會是與您的應用程式一起部署的所有檔案和資料夾的清單， (在) 進行偵錯工具時顯示在主控台視窗中。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>讀取檔案

若要讀取文字檔，您只需要一行程式碼。 此範例會在應用程式輸出視窗中顯示文字檔的內容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>XML 序列化

雖然使用完整 `System.Xml` 命名空間已超出本文的範圍，但您可以使用類似下列程式碼片段的 StreamReader，從檔案系統輕鬆還原序列化 XML 檔：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

如需詳細資訊，請參閱 [System.Xml](xref:System.Xml) 和 [序列化](xref:System.Xml.Serialization)的檔。 請參閱連結器上的 [Xamarin 檔](~/ios/deploy-test/linker.md) ：通常您需要將 `[Preserve]` 屬性新增至您要序列化的類別。

### <a name="creating-files-and-directories"></a>建立檔案和目錄

這個範例示範如何使用 `Environment` 類別來存取可建立檔案和目錄的 [檔] 資料夾。

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

建立目錄的程式如下：

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

如需詳細資訊，請參閱 [SYSTEM.IO API 參考](xref:System.IO)。

### <a name="serializing-json"></a>序列化 JSON

[Json.NET](http://www.newtonsoft.com/json) 是適用于 Xamarin 的高效能 Json 架構，可在 NuGet 上取得。 使用 Visual Studio for Mac 中的 [ **新增 nuget** ]，將 nuget 套件新增至您的應用程式專案：

[![將 NuGet 套件新增至應用程式專案](file-system-images/json01.png)](file-system-images/json01.png#lightbox)

接下來，加入類別以作為序列化/還原序列化 (的資料模型，在此情況下 `Account.cs`) ：

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

最後，建立類別的實例 `Account` 、將其序列化為 json 資料，並將它寫入至檔案：

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

如需在 .NET 應用程式中使用 json 資料的詳細資訊，請參閱 Json. NET 的 [檔](https://www.newtonsoft.com/json/help)。

## <a name="special-considerations"></a>特殊考量

儘管在 Xamarin 與 .NET 檔案作業之間有相似之處，但 iOS 和 Xamarin 與 .NET 的差異在於一些重要的方式。

### <a name="making-project-files-accessible-at-runtime"></a>使專案檔可在執行時間存取

根據預設，如果您將檔案新增至專案，它將不會包含在最終的元件中，因此您的應用程式將無法使用它。 若要將檔案包含在元件中，您必須使用特殊的組建動作（稱為內容）來標示它。

若要標示要包含的檔案，請以滑鼠右鍵按一下檔案 (s) ，然後在 Visual Studio for Mac 中選擇 [ **建立動作 &gt; 內容** ]。 您也可以在檔案的 **屬性** 表中變更 **組建動作** 。

### <a name="case-sensitivity"></a>區分大小寫

請務必瞭解 iOS 檔案系統 *區分大小寫* 。 區分大小寫表示您的檔案和目錄名稱必須完全相符– **README.txt** 且 **readme.txt** 會被視為不同的檔案名。

如果 .NET 開發人員比較熟悉 Windows 檔案系統（不區分 *大小寫* **），則檔案、檔案****和檔案** 都會參考相同的 **目錄，這** 可能會令人困惑。

> [!WARNING]
> IOS 模擬器不區分大小寫。
> 如果檔案名大小寫在程式碼中的檔案本身與其參考不同，則您的程式碼可能仍在模擬器中運作，但是在實際裝置上將會失敗。 這是很重要的原因之一，就是在 iOS 開發期間，在實際裝置上部署和測試很重要的原因之一。

### <a name="path-separator"></a>路徑分隔符號

iOS 使用正斜線 '/' 做為路徑分隔符號 (這與 Windows 不同，後者使用反斜線 ' \ ' ) 。

由於這種混淆的差異，因此最好使用 `System.IO.Path.Combine` 方法，它會針對目前的平臺進行調整，而不是硬式編碼特定的路徑分隔符號。 這是可讓您的程式碼更容易移植到其他平臺的簡單步驟。

## <a name="application-sandbox"></a>應用程式沙箱

基於安全性考慮，您的應用程式對檔案系統 (和其他資源（例如網路和硬體功能) ）的存取權會受到限制。 這項限制稱為「 *應用程式沙箱* 」。 就檔案系統而言，您的應用程式只能在其主目錄中建立和刪除檔案和目錄。

主目錄是檔案系統中儲存應用程式及其所有資料的唯一位置。 您無法選擇 (或變更) 應用程式主目錄的位置;不過，iOS 和 Xamarin 會提供屬性和方法來管理內的檔案和目錄。

## <a name="the-application-bundle"></a>應用程式套件組合

*應用程式* 套件組合是包含應用程式的資料夾。
它可與其他資料夾區別，方法是將. 應用程式尾碼新增至目錄名稱。 您的應用程式套件組合包含您的可執行檔和您的專案所需的所有內容 (檔案、映射等 ) 。

當您流覽至 Mac OS 中的應用程式套件組合時，它會與您在其他目錄中看到的圖示不同（ (的圖示），而 **應用程式** 尾碼會隱藏) ;但是，這只是作業系統以不同方式顯示的一般目錄。

若要查看範例程式碼的應用程式套件組合，請在 **Visual Studio for Mac** 中的專案上按一下滑鼠右鍵，然後選取 [ **在搜尋工具中顯示** ]。 然後，流覽至您應該會在其中找到應用程式圖示的 **bin/** 目錄， (類似) 的螢幕擷取畫面。

![流覽 bin 目錄以尋找類似此螢幕擷取畫面的應用程式圖示](file-system-images/40-bundle.png)

以滑鼠右鍵按一下此圖示，然後選擇 [ **顯示封裝內容** ]，以流覽應用程式套件組合目錄的內容。 內容的顯示方式就像一般目錄的內容，如下所示：

[![應用程式套件組合的內容](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

應用程式套件組合是在測試期間安裝在模擬器或裝置上的應用程式套件組合，最後是提交至 Apple 以納入 App Store 中的內容。

## <a name="application-directories"></a>應用程式目錄

當您的應用程式安裝在裝置上時，作業系統會建立應用程式的主目錄，並在應用程式根目錄中建立可供使用的目錄數目。 自 iOS 8 起，使用者可存取的目錄 [不](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) 在應用程式根目錄中，因此您無法從使用者目錄衍生應用程式套件組合的路徑，反之亦然。

這些目錄、如何判斷其路徑，以及其用途如下所示：

&nbsp;

|目錄|描述|
|---|---|
|[ApplicationName]. app/|**在 iOS 7 及更早版本中** ，這是 `ApplicationBundle` 儲存應用程式可執行檔的目錄。 您在應用程式中建立的目錄結構存在於此目錄中 (例如，您在 Visual Studio for Mac 專案中標示為資源的影像和其他檔案類型) 。<br /><br />如果您需要存取應用程式配套中的內容檔案，可透過屬性取得此目錄的路徑 `NSBundle.MainBundle.BundlePath` 。|
|說明|使用這個目錄來儲存使用者檔和應用程式資料檔案。<br /><br />此目錄的內容可透過 iTunes 檔案共用 (提供給使用者，但預設為停用) 。 將 `UIFileSharingEnabled` 布林值索引鍵新增至 plist 檔案，以允許使用者存取這些檔案。<br /><br />即使應用程式不會立即啟用檔案共用，您仍應避免將應該隱藏在此目錄中的使用者隱藏的檔案 (例如資料庫檔案，除非您想要) 共用它們。 只要機密檔案保持隱藏，如果未來的版本已啟用檔案共用，這些檔案就不會公開 (，且可能會被 iTunes 的移動、修改或刪除) 。<br /><br /> 您可以使用 `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` 方法來取得應用程式之 [檔] 目錄的路徑。<br /><br />此目錄的內容是由 iTunes 所備份。|
|函數|程式庫目錄是儲存不直接由使用者建立之檔案的理想位置，例如資料庫或其他應用程式產生的檔案。 此目錄的內容絕不會透過 iTunes 向使用者公開。<br /><br />您可以在程式庫中建立自己的子目錄;不過，這裡已經有一些系統建立的目錄，您應該注意，包括喜好設定和快取。<br /><br />此目錄的內容 (除了由 iTunes 備份的 [快取] 子目錄) 。 您在程式庫中建立的自訂目錄將會進行備份。|
|程式庫/喜好設定/|應用程式特定的喜好設定檔案會儲存在此目錄中。 請勿直接建立這些檔案。 請改用 `NSUserDefaults` 類別。<br /><br />此目錄的內容是由 iTunes 所備份。|
|程式庫/快取/|快取目錄是儲存資料檔的理想位置，可協助您的應用程式執行，但您可以輕鬆地重新建立這些資料檔案。 應用程式應該視需要建立及刪除這些檔案，而且可以視需要重新建立這些檔案。 當應用程式執行時，iOS 5 也可能會在低儲存的) 情況下 (刪除這些檔案，但不會這麼做。<br /><br />ITunes 不會備份此目錄的內容，這表示如果使用者還原裝置，這些內容不會出現，而且在安裝應用程式的更新版本之後可能不會出現。<br /><br />比方說，如果您的應用程式無法連線到網路，您可以使用 [快取] 目錄來儲存資料或檔案，以提供良好的離線體驗。 應用程式可以在等候網路回應時快速儲存及取出此資料，但它不需要備份，而且可以在還原或版本更新之後輕鬆地復原或重新建立。|
|tmp|應用程式可以在此目錄中儲存只需要短時間的暫存檔案。 為了節省空間，當您不再需要檔案時，應該將它們刪除。 當應用程式未執行時，作業系統也可以從這個目錄中刪除檔案。<br /><br />ITunes 不會備份此目錄的內容。<br /><br />例如，您可以使用 tmp 目錄來儲存下載的暫存檔案，以顯示給使用者 (例如 Twitter 虛擬人偶或電子郵件附件) ，但在流覽 (之後，如果未來) 需要，則可以將其刪除。|

此螢幕擷取畫面顯示搜尋工具視窗中的目錄結構：

[![此螢幕擷取畫面顯示搜尋工具視窗中的目錄結構](file-system-images/08-library-directory.png)](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>以程式設計方式存取其他目錄

先前的目錄和檔案範例會存取 `Documents` 目錄。 若要寫入至另一個目錄，您必須使用 "..." 語法來建立路徑，如下所示：

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

`Caches`和目錄的路徑 `tmp` 可以像這樣：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>與檔案應用程式共用

iOS 11 引進了 **檔案應用程式，這** 是適用于 iOS 的檔案瀏覽器，可讓使用者查看 iCloud 中的檔案並與其互動，也可以由支援它的任何應用程式儲存。 若要允許使用者直接存取您應用程式中的檔案，請在 **plist** 檔案中建立新的布林值， `LSSupportsOpeningDocumentsInPlace` 並將它設定為 `true` ，如下所示：

![在 Info. plist 中設定 LSSupportsOpeningDocumentsInPlace](file-system-images/51-supports-opening.png)

應用程式的 [ **檔** **] 目錄現在** 將可在 [檔案] 應用程式中流覽。 **在 [檔案** ] 應用程式中，流覽至 **我的 iPhone** ，每個具有共用檔案的應用程式都可以看見。 下列螢幕擷取畫面顯示 [檔案系統範例應用程式](/samples/xamarin/ios-samples/filesystemsamplecode) 看起來的樣子：

![iOS 11 檔案應用程式](file-system-images/50-files-app-1-sml.png) ![流覽我的 iPhone 檔案](file-system-images/50-files-app-2-sml.png) ![範例應用程式檔](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>透過 iTunes 與使用者共用檔案

使用者可以藉由編輯 `Info.plist` 和建立 **支援 iTunes 共用的應用程式** `UIFileSharingEnabled` ，在 **來源** 視圖中 () 專案，以存取應用程式檔目錄中的檔案，如下所示：

[![新增應用程式支援 iTunes 共用屬性](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

當裝置已連線，且使用者選擇索引標籤時，可以在 iTunes 中存取這些檔案 `Apps` 。例如，下列螢幕擷取畫面顯示選取的應用程式透過 iTunes 共用的檔案：

[![此螢幕擷取畫面顯示選取的應用程式中透過 iTunes 共用的檔案](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

使用者只能透過 iTunes 存取此目錄中的最上層專案。 他們無法看到任何子目錄的內容 (但可以將它們複製到電腦上，或) 刪除它們。 例如，使用 GoodReader，PDF 和 EPUB 檔案可以與應用程式共用，讓使用者可以在其 iOS 裝置上讀取這些檔案。

如果使用者不小心，修改檔資料夾內容的使用者可能會造成問題。 您的應用程式應該將此納入考慮，並可在 [檔] 資料夾的破壞性更新中復原。

本文的範例程式碼會在 **SampleCode.cs** ) 的 [檔] 資料夾中建立檔案和資料夾 (，並在 **plist** 檔案中啟用檔案共用。 此螢幕擷取畫面顯示這些顯示在 iTunes 中的方式：

[![此螢幕擷取畫面顯示檔案如何顯示在 iTunes 中](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

如需如何設定應用程式圖示的資訊，以及您所建立之任何自訂檔案類型的相關資訊，請參閱「使用 [影像](~/ios/app-fundamentals/images-icons/index.md) 」一文。

如果機 `UIFileSharingEnabled` 碼為 false 或不存在，則檔案共用預設為停用，且使用者將無法與您的檔目錄互動。

## <a name="backup-and-restore"></a>備份與還原

當 iTunes 備份裝置時，會儲存在應用程式主目錄中建立的所有目錄，但下列目錄除外：

- **[ApplicationName]. app** –不寫入此目錄，因為它已簽署，因此在安裝之後必須保持不變。 它可能包含您從程式碼存取的資源，但不需要備份，因為它們會藉由重新下載應用程式來還原。
- 連結 **庫/** 快取-快取目錄適用于不需要備份的工作檔案。
- **tmp** –此目錄用於在不再需要時所建立和刪除的暫存檔案，或 iOS 在需要空間時所刪除的檔案。

備份大量資料可能需要很長的時間。 如果您決定需要備份任何特定的檔或資料，您的應用程式應該使用檔和文件庫資料夾。 針對可從網路輕鬆取出的暫時性資料或檔案，請使用快取或 tmp 目錄。

> [!NOTE]
> 當裝置執行的磁碟空間不足時，iOS 會「清除」檔案系統。
> 此程式將會從目前未執行之應用程式的程式庫/快取和 tmp 資料夾中移除所有檔案。

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>遵循 iOS 5 iCloud 備份限制

> [!NOTE]
> 雖然這項原則是在 iOS 5 中首次引進 (看起來很長一段時間) 本指南仍與現今的應用程式有關。

Apple 引進了 iOS 5 的 *ICloud 備份* 功能。 當 iCloud 備份啟用時，應用程式主目錄中的所有檔案都會 (排除通常未備份的目錄，例如，應用程式套件組合、 `Caches` 和 `tmp`) 都會備份到 iCloud 伺服器。 這項功能可讓使用者在裝置遺失、遭竊或損毀時進行完整備份。

因為 iCloud 只為每位使用者提供 5 Gb 的可用空間，而且為了避免不必要的頻寬使用，所以 Apple 預期應用程式只會備份重要的使用者所產生的資料。 為了符合 iOS 資料儲存指導方針，您應該遵循下列專案來限制備份的資料量：

- 在 [檔] 目錄中，只會儲存使用者產生的資料，或無法重新建立的資料， () 備份。
- 儲存任何可以在中輕鬆重新建立或重新下載的其他資料 `Library/Caches` `tmp` ，或 (不會備份的資料，而且可以「清除」 ) 。
- 如果您有可能適用于或資料夾的檔案 `Library/Caches` `tmp` ，但您不想要「清除」，請將其儲存在其他地方 (例如 `Library/YourData`) 並套用「請勿備份」屬性，以防止檔案使用 iCloud 備份頻寬和儲存空間。 此資料仍會使用裝置上的空間，因此您應該謹慎地加以管理，並盡可能將其刪除。

您可以使用類別來設定「不要備份」屬性 `NSFileManager` 。 確定您的類別是 `using Foundation` ，並呼叫 `SetSkipBackupAttribute` 如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

當 `SetSkipBackupAttribute` 為時 `true` ，不會備份檔案，不論其儲存在 (`Documents` 目錄) 。 您可以使用方法來查詢屬性，也可以透過 `GetSkipBackupAttribute` 呼叫方法來重設它 `SetSkipBackupAttribute` `false` ，就像這樣：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>在 iOS 應用程式與應用程式擴充功能之間共用資料

由於應用程式延伸模組會作為主應用程式的一部分執行 (而不是其包含的應用程式) ，因此不會自動包含共用資料，因此需要額外的工作。 應用程式群組是 iOS 使用的機制，可讓不同的應用程式共用資料。 如果已使用正確的權利和布建正確設定應用程式，他們就可以存取其一般 iOS 沙箱以外的共用目錄。

### <a name="configure-an-app-group"></a>設定應用程式群組

共用位置是使用 [ [IOS 開發人員中心](https://developer.apple.com/devcenter/ios/)] 的 [ **憑證、識別碼 & 設定檔** ] 區段中設定的 [應用程式群組](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)進行設定。 您也必須在每個專案的 **plist** 中參考此值。

如需建立和設定應用程式群組的相關資訊，請參閱 [應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) 指南。

### <a name="files"></a>檔案

IOS 應用程式和擴充功能也可以使用常見的檔案路徑共用檔案 (假設它們已正確設定正確的權利和布建) ：

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> 如果傳回的群組路徑為 `null` ，請檢查權利和布建設定檔的設定，並確定其正確無誤。

## <a name="application-version-updates"></a>應用程式版本更新

下載應用程式的新版本時，iOS 會建立新的主目錄，並在其中儲存新的應用程式套件組合。 iOS 接著會將下列資料夾從舊版的應用程式套件組合移至新的主目錄：

- **文件**
- **程式庫**

您也可以在新的主目錄下複製其他目錄，但不保證會將它們複製，因此您的應用程式不應該依賴此系統行為。

## <a name="summary"></a>總結

本文說明與 Xamarin 的檔案系統作業類似于任何其他 .NET 應用程式。 它也引進了應用程式沙箱，並檢查它所造成的安全性含意。 接下來，它會探討應用程式套件組合的概念。 最後，它會列舉應用程式可用的特製化目錄，並在應用程式升級與備份期間說明其角色。

## <a name="related-links"></a>相關連結

- [檔案系統範例程式碼](/samples/xamarin/ios-samples/filesystemsamplecode)
- [檔案系統程式設計指南](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [註冊您的應用程式支援的檔案類型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)