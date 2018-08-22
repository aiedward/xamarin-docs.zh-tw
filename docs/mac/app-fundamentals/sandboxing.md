---
title: 沙箱 Xamarin.Mac 應用程式
description: 本文章涵蓋沙箱 Xamarin.Mac 應用程式在 App Store 上發行。 它涵蓋了所有進入沙箱功能，例如容器目錄、 權利，使用者所決定的權限、 權限的分隔和核心強制項目。
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9e6cfd39260b76c5097d8faa24f406f45681ac8d
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251151"
---
# <a name="sandboxing-a-xamarinmac-app"></a>沙箱 Xamarin.Mac 應用程式

_本文章涵蓋沙箱 Xamarin.Mac 應用程式在 App Store 上發行。它涵蓋了所有進入沙箱功能，例如容器目錄、 權利，使用者所決定的權限、 權限的分隔和核心強制項目。_

## <a name="overview"></a>總覽

在 Xamarin.Mac 應用程式中使用 C# 和.NET 時, 一樣使用 OBJECTIVE-C 或 Swift 時有沙箱應用程式的相同功能。

[![執行中應用程式範例](sandboxing-images/intro01.png "執行的應用程式的範例")](sandboxing-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin.Mac 應用程式和所有項目移入沙箱的沙箱所使用的基本概念： 容器目錄、 權利、 使用者決定的權限、 權限的分隔和核心強制執行。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="about-the-app-sandbox"></a>關於應用程式沙箱

應用程式沙箱提供強式防禦可能藉由限制應用程式對系統資源的存取權的 Mac 上執行的惡意應用程式所造成的損害。

非沙箱的應用程式具有完整權限執行應用程式的使用者，並可以存取或執行任何動作，使用者可以。 如果安全性漏洞 （或任何它所使用的架構），就會包含應用程式，駭客可能可以利用這些弱點，並使用該應用程式取得的 Mac 上執行的控制權。

藉由限制每個應用程式為基礎的資源的存取權，沙箱化應用程式會提供一行盜用、 損毀或部分使用者的電腦上執行的應用程式的惡意攻擊的防禦。

應用程式沙箱是一種存取控制技術，內建於 macOS （在核心層級強制執行），提供兩個策略：

1. 應用程式沙箱開發人員用來描述_如何_與 OS，並以這種方式，將互動應用程式，被授與僅所需的工作完成，並且不再需要的存取權限。
2. 應用程式沙箱可讓使用者順暢地授與進一步存取透過開放系統和儲存對話方塊、 拖放作業和其他常見的使用者互動。

### <a name="preparing-to-implement-the-app-sandbox"></a>準備實作應用程式沙箱

將在本文中詳細討論應用程式沙箱的項目如下所示：

- 容器的目錄
- 權利
- 使用者所決定的權限
- 權限的區隔
- 核心強制

了解這些詳細資料之後，您可以建立採用 Xamarin.Mac 應用程式中的應用程式沙箱的計劃。

首先，您必須判斷您的應用程式是否適合做為沙箱功能 （大部分的應用程式相同）。 接下來，您必須解決任何 API 不相容，並判斷您需要的應用程式沙箱哪些項目。 最後，可以考慮使用以最大化您的應用程式的防禦層級的權限隔離。

當採用應用程式沙箱，某些應用程式所使用的檔案系統位置將會不同。 特別是，您的應用程式必須將用於應用程式支援檔案、 資料庫、 快取和無使用者文件的任何其他檔案的容器目錄。 MacOS 和 Xcode 提供支援，以將這些類型的檔案從舊位置移轉到的容器。

## <a name="sandboxing-quick-start"></a>沙箱快速入門

在本節中，我們將建立簡單的 Xamarin.Mac 應用程式使用 Web 檢視 （這需要網路連線，除非在特別要求，在沙箱會限制） 的應用程式沙箱使用者入門範例。

我們會確認已實際沙箱化應用程式，並了解如何進行疑難排解並解決常見的應用程式沙箱錯誤。

### <a name="creating-the-xamarinmac-project"></a>建立 Xamarin.Mac 專案

讓我們執行下列命令來建立我們的範例專案：

1. 啟動 Visual Studio for Mac，然後按一下 **新方案...** 連結，將該帳戶加入 [檔案] > [帳戶設定] 對話方塊左側的 [所有帳戶] 清單中。
2. 從**新的專案**對話方塊中，選取**Mac** > **應用程式** > **Cocoa 應用程式**: 

    [![建立新的 Cocoa 應用程式](sandboxing-images/sample01.png "建立新的 Cocoa 應用程式")](sandboxing-images/sample01-large.png#lightbox)
3. 按一下 **下一步**按鈕，輸入`MacSandbox`為專案名稱，然後按一下**建立**按鈕： 

    [![輸入應用程式名稱](sandboxing-images/sample02.png "輸入應用程式名稱")](sandboxing-images/sample02-large.png#lightbox)
4. 在  **Solution Pad**，按兩下**Main.storyboard**檔案將它開啟以在 Xcode 中編輯： 

    [![編輯主要分鏡腳本](sandboxing-images/sample03.png "編輯主要分鏡腳本")](sandboxing-images/sample03-large.png#lightbox)
5. 拖曳**網頁檢視**拖曳至視窗中，將其填滿內容區域，並將它設定為增加和縮減與視窗大小： 

    [![加入 web 檢視](sandboxing-images/sample04.png "加入 web 檢視")](sandboxing-images/sample04-large.png#lightbox)
6. 建立稱為 web 檢視輸出`webView`: 

    [![建立新的輸出](sandboxing-images/sample05.png "建立新的輸出")](sandboxing-images/sample05-large.png#lightbox)
7. 返回 Visual Studio for Mac，然後按兩下**ViewController.cs**中的檔案**Solution Pad**以開啟它進行編輯。
8. 新增下列 using 陳述式： `using WebKit;`
9. 請`ViewDidLoad`方法外觀如下所示： 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. 儲存您的變更。

執行您的應用程式，並確保，Apple 網站會顯示在視窗中，如下所示：

[![顯示執行範例應用程式](sandboxing-images/sample06.png "顯示執行範例應用程式")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>簽署與佈建應用程式

我們可以讓應用程式沙箱之前，我們必須先佈建，並簽署 Xamarin.Mac 應用程式。

可讓下列作業：

1. 登入 Apple 開發人員入口網站： 

    [![登入 Apple 開發人員入口網站](sandboxing-images/sign01.png "登入 Apple 開發人員入口網站")](sandboxing-images/sign01-large.png#lightbox)
2. 選取 **憑證、 識別碼與設定檔**: 

    [![選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔](sandboxing-images/sign02.png "選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔")](sandboxing-images/sign02-large.png#lightbox)
3. 底下**Mac 應用程式**，選取**識別碼**: 

    [![選取 識別項](sandboxing-images/sign03.png "選取識別碼")](sandboxing-images/sign03-large.png#lightbox)
4. 建立新的應用程式識別碼： 

    [![建立新的應用程式識別碼](sandboxing-images/sign04.png "建立新的應用程式識別碼")](sandboxing-images/sign04-large.png#lightbox)
5. 底下**佈建設定檔**，選取**開發**: 

    [![選取開發](sandboxing-images/sign05.png "選取開發")](sandboxing-images/sign05-large.png#lightbox)
6. 建立新的設定檔，然後選取**Mac 應用程式開發**: 

    [![建立新的設定檔](sandboxing-images/sign06.png "建立新的設定檔")](sandboxing-images/sign06-large.png#lightbox)
7. 選取我們先前建立的應用程式識別碼： 

    [![選取的應用程式識別碼](sandboxing-images/sign07.png "選取的應用程式識別碼")](sandboxing-images/sign07-large.png#lightbox)
8. 選取此設定檔的開發人員： 

    [![新增開發人員](sandboxing-images/sign08.png "新增開發人員")](sandboxing-images/sign08-large.png#lightbox)
9. 選取此設定檔的電腦： 

    [![選取允許的電腦](sandboxing-images/sign09.png "選取允許的電腦")](sandboxing-images/sign09-large.png#lightbox)
10. 指定設定檔的名稱： 

    [![為設定檔命名](sandboxing-images/sign10.png "命名設定檔")](sandboxing-images/sign10-large.png#lightbox)
11. 按一下 [**完成**] 按鈕。

> [!IMPORTANT]
> 在某些情況下，您可能需要直接從 Apple 開發人員入口網站下載新的佈建設定檔，然後按兩下以安裝。 您也可能需要停止並重新啟動 Visual Studio for Mac，然後才會無法存取新的設定檔。

接下來，我們需要在開發電腦上載入新的應用程式識別碼和設定檔。 讓我們執行下列作業：

1. 啟動 Xcode，然後選取**偏好**從**Xcode**功能表： 

    ![編輯在 Xcode 中的帳戶](sandboxing-images/sign11.png "編輯在 Xcode 中的帳戶")
2. 按一下**檢視詳細資料...** 按鈕： 

    ![按一下 [檢視詳細資料] 按鈕](sandboxing-images/sign12.png "按一下 [檢視詳細資料] 按鈕")
3. 按一下 [**重新整理**（以較低的左下角）] 按鈕。
4. 按一下 [**完成**] 按鈕。

接下來，我們需要我們 Xamarin.Mac 專案中，選取新的應用程式識別碼和佈建設定檔。 讓我們執行下列作業：

1. 在  **Solution Pad**，按兩下**Info.plist**檔案，以開啟它進行編輯。
2. 請確認**套件組合識別碼**符合我們前面所建立的應用程式識別碼 (範例： `com.appracatappra.MacSandbox`): 

    [![編輯 Bundle Identifier](sandboxing-images/sign13.png "編輯套件組合識別碼")](sandboxing-images/sign13-large.png#lightbox)
3. 接下來，按兩下**Entitlements.plist**檔案，並確定我們**iCloud 索引鍵-值存放區**並**iCloud 容器**所有符合我們前面所建立的應用程式識別碼 (範例：`com.appracatappra.MacSandbox`): 

    [![編輯 Entitlements.plist 檔案](sandboxing-images/sign17.png "編輯 Entitlements.plist 檔案")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存您的變更。
4. 在  **Solution Pad**，按兩下專案檔，以開啟它進行編輯的選項：  

    ![Editign 解決方案的選項](sandboxing-images/sign14.png "Editign 解決方案的選項")
5. 選取  **Mac 簽署**，然後核取**簽署應用程式套件組合**並**簽署安裝程式套件**。 底下**佈建設定檔**，選取我們上面所建立： 

    ![設定佈建設定檔](sandboxing-images/sign15.png "設定佈建設定檔")
6. 按一下 [**完成**] 按鈕。

> [!IMPORTANT]
> 您可能必須結束並重新啟動 Visual Studio for Mac，以取得它，來辨識新的應用程式識別碼和佈建設定檔所安裝的 Xcode。

#### <a name="troubleshooting-provisioning-issues"></a>佈建進行疑難排解

此時您應該嘗試執行應用程式，並確定所有項目是用來簽署，並正確佈建。 如果應用程式仍在執行之前一樣，所有項目是很好。 發生問題時，您可能會收到如下所示的對話方塊：

[![舉例來說，佈建問題 對話方塊](sandboxing-images/sign16.png "佈建問題 對話方塊範例")](sandboxing-images/sign16-large.png#lightbox)

佈建和簽署問題最常見原因如下：

- 應用程式配套識別碼不符合選取的設定檔的應用程式識別碼。
- 開發人員識別碼不符合選取的設定檔的開發人員識別碼。
- Mac 上進行測試的 UUID 未註冊為選取的設定檔的一部分。

如果發生問題，是修正此問題，在 Apple 開發人員入口網站，重新整理在 Xcode 中的設定檔並執行乾淨的組建，在 Visual Studio for mac。

### <a name="enable-the-app-sandbox"></a>啟用應用程式沙箱

您可以在您的專案選項中選取核取方塊，以啟用應用程式沙箱。 請執行下列動作：

1. 在  **Solution Pad**，按兩下**Entitlements.plist**檔案，以開啟它進行編輯。
2. 檢查兩者**啟用權利**並**進行應用程式沙箱化**: 

    [![編輯權利，並啟用沙箱](sandboxing-images/sign17.png "編輯權利，並啟用沙箱")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存您的變更。

此時，您已啟用應用程式沙箱，但您沒有進行 Web 檢視提供所需的網路存取。 如果您執行應用程式現在，您應該會看到空白的視窗：

[![顯示 web 存取會封鎖](sandboxing-images/sample08.png "顯示正在封鎖的 web 存取")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>正在驗證應用程式已沙箱化

除了封鎖行為的資源，有三種主要的方法，來告訴 Xamarin.Mac 應用程式是否已成功沙箱化：

1. 在搜尋工具，請檢查的內容`~/Library/Containers/`資料夾-如果應用程式已沙箱化，則會命名為您的應用程式套件組合識別碼的資料夾 (範例： `com.appracatappra.MacSandbox`): 

    [![開啟應用程式的套件組合](sandboxing-images/sample09.png "開啟應用程式的套件組合")](sandboxing-images/sample09-large.png#lightbox)
2. 系統會將視為在 「 活動監視器沙箱化應用程式：
    - 啟動活動監視器 (下`/Applications/Utilities`)。 
    - 選擇**檢視** > **資料行**，並確定**沙箱**檢查功能表項目。
    - 請確定 [沙箱] 欄會讀取`Yes`應用程式： 

    [![檢查 活動監視器中的應用程式](sandboxing-images/sample10.png "檢查活動監視器中的應用程式")](sandboxing-images/sample10-large.png#lightbox)
3. 請檢查二進位應用程式已沙箱化：
    - 啟動終端機應用程式。
    - 瀏覽至應用程式`bin`目錄。
    - 發出此命令： `codesign -dvvv --entitlements :- executable_path` (其中`executable_path`是您的應用程式的路徑): 

    [![檢查命令列上的應用程式](sandboxing-images/sample11.png "檢查命令列上的應用程式")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>沙箱化應用程式偵錯

偵錯工具連接到 Xamarin.Mac 應用程式，透過 TCP，這表示，依預設啟用的沙箱功能時，無法連線到應用程式，因此如果您嘗試執行應用程式沒有啟用適當的權限，您會收到錯誤 *「 無法連接到偵錯工具 」*。 

[![設定所需的選項](sandboxing-images/debug01.png "設定所需的選項")](sandboxing-images/debug01-large.png#lightbox)

**允許連出網路連線 （用戶端）** 權限就是所需的偵錯工具，啟用這其中一個可正常地偵錯。 因為您無法偵錯，而不需要它，我們已更新`CompileEntitlements`為目標`msbuild`自動將該權限新增至權利，針對任何已沙箱化偵錯的應用程式的組建。 發行組建應使用指定在權利檔案中，未經修改的權利。

### <a name="resolving-an-app-sandbox-violation"></a>解決應用程式沙箱違規

如果沙箱的 Xamarin.Mac 應用程式嘗試存取的資源不會明確允許，就會發生應用程式沙箱違規。 比方說，我們網頁檢視再也無法顯示 Apple 網站。

在 Visual Studio for Mac 所指定的權限設定不符合應用程式的需求時，就會發生最常見的應用程式沙箱違規的來源。 同樣地，回到我們的範例中，遺漏的網路連線無法運作讓 Web 檢視的權利。

#### <a name="discovering-app-sandbox-violations"></a>探索應用程式沙箱違規

如果您懷疑發生應用程式沙箱違規發生在 Xamarin.Mac 應用程式中，發現問題的最快方式是使用**主控台**應用程式。

請執行下列動作：

1. 編譯應用程式有問題，並執行它從 Visual Studio for mac。
2. 開啟**主控台**應用程式 (從`/Applications/Utilties/`)。
3. 選取**所有的訊息**提要欄位中輸入`sandbox`中搜尋： 

    [![在主控台中的沙箱問題的範例](sandboxing-images/resolve01.png "沙箱問題在主控台中的範例")](sandboxing-images/resolve01-large.png#lightbox)

對於我們上面的範例應用程式，您可以查看正在封鎖 Kernal`network-outbound`流量，因為應用程式沙箱，因為我們不要求該權限。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>與權利的違規修正應用程式沙箱

既然我們已了解如何尋找應用程式沙箱違規，讓我們來看看如何解決這些藉由調整我們的應用程式的權利。

請執行下列動作：

1. 在  **Solution Pad**，按兩下**Entitlements.plist**檔案，以開啟它進行編輯。
2. 底下**權利**區段中，按一下**允許連出網路連線 （用戶端）** 核取方塊： 

    [![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存應用程式所做的變更。

如果我們執行我們的範例應用程式，然後建置並執行，如預期般現在會顯示 web 內容。

## <a name="the-app-sandbox-in-depth"></a>深入的應用程式沙箱

應用程式沙箱所提供的存取控制機制是幾個且容易理解。 不過，每個應用程式會採用應用程式沙箱的方式是唯一且根據應用程式的需求。

指定您要保護 Xamarin.Mac 應用程式免於受到入侵的惡意程式碼的最大努力，需要有只有單一的弱點可能會在應用程式 （或它所使用的其中一個程式庫或架構） 控制應用程式的互動系統。

應用程式沙箱的設計是為防止接管 （或限制它可能會造成的損害） 可讓您指定與系統的應用程式的預期的互動。 系統只會授與存取權的應用程式完成其工作所需的資源，不多不少。

在設計應用程式沙箱時，您要設計的最糟的狀況。 如果應用程式會成為入侵，惡意程式碼，它會限制為只有檔案和應用程式的沙箱中的資源存取。

### <a name="entitlements-and-system-resource-access"></a>權利和系統資源的存取

如我們所見上方，已沙箱化的 Xamarin.Mac 應用程式會授與的完整權限和執行應用程式之使用者的存取權。 如果入侵的惡意程式碼，未受保護的應用程式就可以使用範圍可能危害整個做為有害行為，代理程式。

藉由啟用應用程式沙箱，全部移除權限，您的最小集合，然後重新啟用需要專用的為基礎，使用 Xamarin.Mac 應用程式的權利。 

您可以修改您的應用程式的應用程式沙箱資源藉由編輯其**Entitlements.plist**檔案及檢查或選取從編輯器下拉式清單方塊中所需的權限：

[![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器的目錄和檔案系統存取

當您的 Xamarin.Mac 應用程式會採用應用程式沙箱時，它會有存取到下列位置：

- **應用程式的容器目錄**-在初次執行時，作業系統會建立特殊_容器的 Directory_其中所有資源說只有它才能存取。 應用程式會有完整的讀取/寫入存取此目錄。
- **應用程式群組的容器目錄**-您的應用程式可以獲授與存取權到一或多個_的群組容器_，應用程式之間共用相同的群組。
- **使用者指定的檔案**-應用程式會自動取得檔案的明確開啟或拖曳和卸除該應用程式互動，使用者的存取。
- **相關項目**-與適當的權利，您的應用程式具有可存取具有相同名稱但不同的副檔名的檔案。 例如，作為已儲存的文件`.txt`檔案和`.pdf`。
- **暫存目錄、 命令列工具的目錄，並可 world 讀取的特定位置**-您的應用程式中其他定義完善的位置，系統所指定的檔案有不同程度的存取。

#### <a name="the-app-container-directory"></a>應用程式的容器目錄

Xamarin.Mac 應用程式的應用程式的容器目錄具有下列特性：

- 它會在使用者的主目錄中隱藏的位置 (通常`~Library/Containers`)，而且可以存取與`NSHomeDirectory`應用程式內的函式 （如下所示）。 因為它是主目錄中，每個使用者會取得自己的容器應用程式。
- 應用程式具有不受限制的讀取/寫入存取的容器目錄和所有其子目錄和檔案中。
- MacOS 的路徑尋找 Api 大部分都是相對於應用程式的容器。 例如，容器會有它自己**程式庫**(透過存取`NSLibraryDirectory`)，**應用程式支援**並**喜好設定**子目錄。
- macOS 建立，並強制執行之間的連線和應用程式和其容器透過程式碼簽署。 即使是另一個應用程式會嘗試欺騙應用程式使用其**套件組合識別碼**，它不能存取容器，因為程式碼簽署。
- 容器不是使用者產生的檔案。 相反地，它是應用程式如資料庫、 快取或其他特定資料類型所使用的檔案。
- 針對_shoebox_類型的應用程式 （如 Apple 的相片應用程式），使用者的內容將會移至容器。

> [!IMPORTANT]
> 不幸的是，Xamarin.Mac 沒有尚未 （不同於 Xamarin.iOS)、 100 %api 涵蓋範圍結果`NSHomeDirectory`API 未在目前的 Xamarin.Mac 版本對應。

暫時的解決方法，您可以使用下列程式碼：

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>應用程式群組的容器目錄

應用程式開始 Mac macOS 10.7.5 （和更新版本），可以使用`com.apple.security.application-groups`存取共用的容器通用的群組中的所有應用程式的權利。 針對非使用者公開的內容，例如資料庫或其他類型的支援檔案 （例如快取），您可以使用此共用的容器。

群組容器會自動新增至每個應用程式沙箱容器 （如果它們是群組的一部分） 並儲存在`~/Library/Group Containers/<application-group-id>`。 群組識別碼_必須_開始使用您的開發小組識別碼和一段時間，例如：

```plist
<team-id>.com.company.<group-name>
```

如需詳細資訊，請參閱 Apple[新增至應用程式群組應用程式](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)中[權利機碼參考](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>在應用程式容器之外的 Powerbox 和檔案系統存取權

沙箱的 Xamarin.Mac 應用程式可以透過下列方式存取其容器以外的檔案系統位置：

- 在 （透過開啟和儲存對話方塊或其他方法，例如拖放） 使用者特定的方向。
- 使用權利，代表特定的檔案系統位置 (例如`/bin`或`/usr/lib`)。
* 檔案系統位置時是世界 （例如共用） 的可讀取特定目錄中。

_Powerbox_是 macOS 與展開沙箱 Xamarin.Mac 應用程式的檔案存取權限的使用者互動的安全性技術。 Powerbox 已沒有可用的 API，但應用程式呼叫時以透明的方式啟動`NSOpenPanel`或`NSSavePanel`。 已啟用 Powerbox 存取，透過您設定 Xamarin.Mac 應用程式的權利。

當沙箱化應用程式會顯示 開啟或儲存對話方塊時，視窗會出示 Powerbox （和不 AppKit），並因此可以存取任何的檔案或目錄的使用者擁有存取權。

當使用者選取檔案或目錄，從 開啟或儲存對話方塊 （或拖曳至應用程式的圖示的其中一個） 時，則 Powerbox 會將相關聯的路徑將應用程式的沙箱。

此外，系統會自動允許下列命令來沙箱化應用程式：

- 連接到系統的輸入方法。
- 呼叫從使用者所選取的服務**Services**功能表 (唯一的服務標示為_沙箱應用程式的安全_服務提供者)。
- 從使用者選擇開啟的檔案**開啟新**功能表。
- 使用其他應用程式之間的複製和貼。
- 從下列可 world 讀取位置讀取檔案：
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- 讀取和寫入檔案中所建立的目錄`NSTemporaryDirectory`。

是預設，開啟或沙箱的 Xamarin.Mac 應用程式所儲存的檔案仍然可以存取應用程式終止 （除非應用程式結束時，仍保持開啟的檔案） 為止。 開啟的檔案將會自動還原至透過 macOS 的恢復功能的應用程式的沙箱下一次啟動應用程式。

若要提供持續性檔案位於外部 Xamarin.Mac 應用程式的容器，使用安全性範圍的書籤 （如下所示）。

#### <a name="related-items"></a>相關項目

應用程式沙箱可讓應用程式存取有相同的檔案名稱，但不同的擴充功能的相關項目。 此功能有兩個部分： a） 的應用程式的相關的延伸模組清單`Info.plst`檔案，b） 程式碼，告訴沙箱功能的應用程式會進行與這些檔案。

有兩種案例，這一點可以理解：

1. 應用程式必須能夠儲存至檔案 （最新的延伸模組） 的不同版本。 例如，匯出`.txt`檔案`.pdf`檔案。 若要處理這種情況下，您必須使用`NSFileCoordinator`存取檔案。 您將呼叫`WillMove(fromURL, toURL)`方法前，將檔案移至新的延伸模組，然後再呼叫`ItemMoved(fromURL, toURL)`。
2. 應用程式必須使用不同的擴充功能開啟一個副檔名的主要檔案和數個支援的檔案。 例如，電影及翻譯字幕檔案。 使用`NSFilePresenter`才能存取次要檔案。 主要檔案提供給`PrimaryPresentedItemURL`屬性和次要的檔案，以`PresentedItemURL`屬性。 主要的檔案開啟時，呼叫`AddFilePresenter`方法的`NSFileCoordinator`次要檔案註冊的類別。

在這兩種情況下，應用程式的**Info.plist**檔案必須宣告該應用程式可以開啟的文件類型。 針對任何檔案類型，新增`NSIsRelatedItemType`(值為`YES`) 中的項目至`CFBundleDocumentTypes`陣列。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>開啟和儲存與沙箱化應用程式的對話方塊行為

下列限制會置於`NSOpenPanel`和`NSSavePanel`從沙箱的 Xamarin.Mac 應用程式呼叫它們時：

- 您無法以程式設計方式叫用**確定** 按鈕。
- 您無法以程式設計的方式改變中使用者的選取項目`NSOpenSavePanelDelegate`。

此外，下列的繼承修改已就緒：

- **非沙箱化應用程式** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全性範圍的書籤與持續性的資源存取

如上所述，沙箱的 Xamarin.Mac 應用程式可以存取的檔案或透過直接使用者互動 （如 PowerBox 所提供） 其容器以外的資源。 不過，這些資源的存取權不會自動保存跨應用程式啟動或系統重新啟動。

藉由使用_Security-Scoped 書籤_，沙箱的 Xamarin.Mac 應用程式可以保留使用者的想法，並維持存取指定的資源應用程式之後重新啟動。

#### <a name="security-scoped-bookmark-types"></a>安全性範圍的書籤類型

使用 Security-Scoped 書籤和持續性的資源存取時，有兩個 sistine 的使用案例：

- **App-Scoped 書籤提供持續存取權的使用者指定的檔案或資料夾。** 

    例如，如果沙箱的 Xamarin.Mac 應用程式可讓用來開啟外部的文件進行編輯 (使用`NSOpenPanel`)，應用程式可以建立 App-Scoped 書籤，以便它可以在未來再次存取相同的檔案。
- **Document-Scoped 書籤會提供特定文件的持續存取子的檔案。** 

比方說，建立專案檔的視訊編輯應用程式可以存取個別的映像、 視訊剪輯和更新版本會結合成單一影片的音效檔。

當使用者匯入專案的資源檔 (透過`NSOpenPanel`)，應用程式會建立 Document-Scoped 書籤在對儲存在專案中的項目，以便檔案一律會存取應用程式。

任何可以開啟 書籤資料和文件本身的應用程式，就可以解決 Document-Scoped 書籤。 這支援可攜性，讓使用者能夠傳送至其他使用者，並讓所有的書籤也適用於它們的專案檔。

> [!IMPORTANT]
> Document-Scoped 書籤可以_僅_指向單一檔案並不是資料夾，該檔案不能是系統所使用的位置 (例如`/private`或`/Library`)。

#### <a name="using-security-scoped-bookmarks"></a>使用安全性範圍的書籤

使用任一種 Security-Scoped 書籤，要求您執行下列步驟：

1. **需要使用 Security-Scoped 書籤的 Xamarin.Mac 應用程式中設定適當的權利**-For App-Scoped 書籤，設定`com.apple.security.files.bookmarks.app-scope`權利關鍵`true`。 Document-Scoped 書籤，設定`com.apple.security.files.bookmarks.document-scope`權利關鍵`true`。
2. **建立 Security-Scoped 書籤**-要這樣做的任何檔案或資料夾的使用者提供存取權 (透過`NSOpenPanel`例如)，應用程式必須持續存取權。 使用`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`方法的`NSUrl`類別來建立書籤。
3. **解決 Security-Scoped 書籤**-應用程式需要存取一次的資源 （例如，在之後重新啟動） 時需要解析的安全性範圍的 URL 為書籤。 使用`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`方法的`NSUrl`解析書籤的類別。
4. **明確通知您想要從 Security-Scoped URL 存取檔案系統**-這個步驟需要取得上述的 Security-Scoped URL 之後，立即執行或當您稍後想要有之後，重新取得資源的存取權relinquished 您存取它。 呼叫`StartAccessingSecurityScopedResource ()`方法的`NSUrl`開始存取 Security-Scoped URL 的類別。
5. **明確通知完成系統存取的檔案從 Security-Scoped URL** -儘速，您應該通知系統時的應用程式不再需要檔案的存取權 （例如，如果使用者關閉它）。 呼叫`StopAccessingSecurityScopedResource ()`方法的`NSUrl`停止存取 Security-Scoped URL 的類別。

放棄資源的存取權之後，您必須返回步驟 4，再以重新建立存取權限。 重新啟動的 Xamarin.Mac 應用程式時，您必須回到步驟 3，並重新解析書籤。

> [!IMPORTANT]
> 釋放 Security-Scoped URL 資源的存取權失敗會導致洩漏核心資源 Xamarin.Mac 應用程式。 如此一來，應用程式將不再能夠新增到其容器的檔案系統位置，直到重新啟動為止。

### <a name="the-app-sandbox-and-code-signing"></a>應用程式沙箱和程式碼簽署

啟用應用程式沙箱後，當您啟用您的 Xamarin.Mac 應用程式 （透過權利） 的特定需求時，您必須撰寫程式碼簽署專案的沙箱，才會生效。 您必須執行的程式碼簽署，因為應用程式沙箱所需的權利連結至應用程式的簽章。 

macOS 會強制執行的應用程式的容器和其程式碼簽章，沒有其他應用程式可以存取該容器中，即使它詐騙的應用程式套件組合識別碼。 這種方式之間的連結 這項機制的運作方式，如下所示：

1. 當系統建立應用程式的容器時，它會設定_存取控制清單_(ACL) 在該容器。 初始的存取控制項目清單中包含的應用程式_指定的需求_(DR)，其中描述如何在未來版本的應用程式可辨識 （當它已升級）。
2. 每次具有相同的套件組合識別碼的應用程式啟動時，系統會檢查應用程式的程式碼簽章符合指定之需求的容器的 ACL 中的項目。 如果系統找不到相符項目，它會防止應用程式啟動。

程式碼簽署的運作方式如下：

1. 之前建立 Xamarin.Mac 專案，請從 Apple 開發人員入口網站中取得開發憑證 」、 「 散發憑證和 「 開發人員識別碼憑證。
2. 當 Mac App Store 散發的 Xamarin.Mac 應用程式時，它是以 Apple 的程式碼簽章進行簽署。

當測試和偵錯，您會使用您登入 （這會用來建立應用程式容器） 的 Xamarin.Mac 應用程式的版本。 稍後，如果您想要測試，或從 Apple App Store 安裝的版本，它會使用 Apple 簽章來簽署並啟動 （因為它不具有相同的程式碼簽章，做為原始的應用程式容器） 將會失敗。 在此情況下，您會看到一個損毀報告如下所示：

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要修正此問題，您需要調整 ACL 項目，以指向應用程式的 Apple 簽署版本。

如需有關建立和下載所需的沙箱佈建設定檔的詳細資訊，請參閱[簽署與佈建應用程式](#Signing_and_Provisioning_the_App)上一節。

#### <a name="adjusting-the-acl-entry"></a>調整 ACL 項目

若要允許執行的 Xamarin.Mac 應用程式的 Apple 帶正負號的版本，執行下列作業：

1. 開啟 Terminal 應用程式 (在`/Applications/Utilities`)。
2. 開啟搜尋工具視窗的 Xamarin.Mac 應用程式的 Apple 簽署版本。
3. 型別`asctl container acl add -file `終端機視窗中。
4. 將 Xamarin.Mac 應用程式的圖示從 [搜尋] 視窗拖放在終端機視窗中。
5. 檔案的完整路徑會新增至終端機中的命令。
6. 按下**Enter**執行命令。

將容器的 ACL 現在包含兩個版本的 Xamarin.Mac 應用程式的指定程式碼需求，而且 macOS 現在允許執行的其中一個版本。

#### <a name="display-a-list-of-acl-code-requirements"></a>顯示一份 ACL 的程式碼需求

您可以檢視容器的 ACL 中的一份程式碼需求，透過下列方式：

1. 開啟 Terminal 應用程式 (在`/Applications/Utilities`)。
2. 輸入 `asctl container acl list -bundle <container-name>`。
3. 按下**Enter**執行命令。

`<container-name>`通常是 Xamarin.Mac 應用程式的套件組合識別碼。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>設計 Xamarin.Mac 應用程式的應用程式沙箱

沒有為應用程式沙箱設計 Xamarin.Mac 應用程式時應遵循的常見工作流程。 話雖如此，應用程式中實作沙箱的細節將會唯一指定的應用程式功能。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>採用應用程式沙箱的六個步驟

設計 Xamarin.Mac 應用程式的應用程式沙箱，通常包含下列步驟：

1. 判斷是否適用於沙箱應用程式。
2. 設計的開發和散佈的策略。
3. 解決任何 API 不相容。
4. 套用必要的應用程式沙箱權利給 Xamarin.Mac 專案。
5. 新增使用 XPC 的權限隔離。
6. 實作的移轉策略。

> [!IMPORTANT]
> 您必須不僅沙箱中您應用程式套件組合，但也會每個包含協助程式的主要可執行檔應用程式或該配套中的工具。 這是為了從 Mac App Store 散發的任何應用程式，而且可能的話，應該完成任何其他形式的應用程式散發。

如需在 Xamarin.Mac 應用程式的套件組合中的所有可執行檔二進位檔的清單，請在終端機中輸入下列命令：

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`是應用程式的套件組合的路徑與名稱。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>判斷是否適合進行沙箱化處理 Xamarin.Mac 應用程式

大部分的 Xamarin.Mac 應用程式會與應用程式沙箱完全相容，因此，適用於沙箱。 如果應用程式需要一種行為，不允許應用程式沙箱，您應該考慮的替代方式。

如果您的應用程式必須使用其中一個下列的行為，它與不相容應用程式沙箱：

- **授權服務**-使用應用程式沙箱，您無法使用函式中所述[授權服務 C 參考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)。
- **協助工具 Api** -您不能沙箱輔助應用程式，例如螢幕助讀程式或控制其他應用程式的應用程式。
- **將 Apple 事件傳送至任意的應用程式**-如果應用程式需要 Apple 事件傳送至未知的任意應用程式，則它不能對沙箱化。 被呼叫的應用程式已知清單中，應用程式仍然可以沙箱化以及權利必須包含被呼叫的應用程式清單。
- **在分散式的通知中傳送使用者資訊的字典，其他工作**-您不能包含與應用程式沙箱`userInfo`字典時張貼到`NSDistributedNotificationCenter`傳訊其他工作的物件。
- **載入核心延伸**-核心延伸模組的載入禁止的應用程式沙箱。
- **模擬使用者輸入檔案中開啟和儲存對話方塊**-以程式設計方式操作開啟或儲存對話方塊，來模擬或變更使用者的輸入所禁止的應用程式沙箱。
- **存取或其他應用程式上的設定喜好設定**-操作的其他應用程式設定為禁止的應用程式沙箱。
- **設定網路設定**-管理網路設定應用程式沙箱所禁止的。
- **終止其他應用程式**-應用程式沙箱會禁止使用`NSRunningApplication`來終止其他應用程式。

### <a name="resolving-api-incompatibilities"></a>解決 API 不相容性

在設計 Xamarin.Mac 應用程式的應用程式沙箱時，您可能會遇到的一些 macOS Api 使用方式與不相容。

以下是一些常見的問題和您可以如何解決這些問題的項目：

- **開啟、 儲存和追蹤的文件**-如果您要管理而不使用任何技術文件`NSDocument`，您應該切換到它，因為應用程式沙箱內建支援。 `NSDocument` 自動搭配 PowerBox，並保留您的沙箱內的文件，如果使用者移它們在尋找工具中提供支援。
- **保留檔案系統資源的存取權**-如果應用程式相依於其容器以外之資源的持續存取 Xamarin.Mac 使用安全性範圍的書籤，以維持存取權。
- **建立應用程式的登入項目**-使用應用程式沙箱，您無法建立項目使用的登入`LSSharedFileList`您可以管理使用的啟動服務的狀態，也不`LSRegisterURL`。 使用`SMLoginItemSetEnabled`函式 a 中所述[將使用服務管理架構的登入項目加入](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)文件。
- **存取使用者資料**-如果您正在使用 POSIX 函式，例如`getpwuid`若要從目錄服務中取得使用者的主目錄，請考慮使用 Cocoa 或核心基礎符號，例如`NSHomeDirectory`。
- **存取喜好設定的其他應用程式**-因為修改該容器內的喜好設定會發生，以及存取中的另一個應用程式喜好設定不允許應用程式沙箱會指示路徑-尋找 Api 應用程式的容器。 
- **在 Web 檢視中使用 HTML5 內嵌影片**-如果 Xamarin.Mac 應用程式會使用 WebKit 播放內嵌的 HTML5 視訊，您也必須連結對 AV Foundation 架構應用程式。 應用程式沙箱會防止 CoreMedia play 否則這些影片。

### <a name="applying-required-app-sandbox-entitlements"></a>套用必要的應用程式沙箱權利

您必須編輯您想要在應用程式沙箱中執行，並檢查任何 Xamarin.Mac 應用程式的權利**啟用應用程式沙箱**核取方塊。

根據應用程式的功能，您可能需要啟用存取作業系統功能或資源的其他權利。 因此最適合您盡量減少您要求執行您的應用程式所需的最低限度的權利的應用程式沙箱功能可以運作隨便啟用權利。

若要判斷哪一個 Xamarin.Mac 應用程式所需的權利，執行下列作業：

1. 啟用應用程式沙箱並執行的 Xamarin.Mac 應用程式。
2. 執行應用程式的功能。
3. 開啟 主控台應用程式 (用於`/Applications/Utilities`)，並尋找`sandboxd`中的違規**所有訊息**記錄檔。
4. 每個`sandboxd`違規，解決此問題，可以使用應用程式容器，而不其他檔案系統位置，或套用以存取受限制的作業系統功能的應用程式沙箱權利。
5. 重新執行，並再次測試 Xamarin.Mac 應用程式的所有功能。
6. 重複直到所有`sandboxd`違規已獲得解決。

### <a name="add-privilege-separation-using-xpc"></a>新增權限隔離使用 XPC

當開發應用程式沙箱 Xamarin.Mac 應用程式，查看 的權限和存取權，條款中的 應用程式的行為，然後請考慮區隔成自己 XPC 服務的高風險作業。

如需詳細資訊，請參閱 Apple[建立 XPC 服務](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)並[精靈和服務程式設計指南](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>實作的移轉策略

如果您要釋出不是先前沙箱的 Xamarin.Mac 應用程式的新沙箱化版本，您必須確保目前的使用者有順利的升級路徑。 

 如需如何實作容器移轉資訊清單的詳細資訊，請閱讀 Apple[將應用程式移轉至沙箱](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)文件。

## <a name="summary"></a>總結

這篇文章詳細的說明了所沙箱 Xamarin.Mac 應用程式。 首先，我們會建立只 Xamarin.Mac 應用程式中顯示的應用程式沙箱基本概念。 接下來，我們示範了如何解決沙箱違規。 然後，我們花了深入查看 應用程式沙箱，最後，我們探討設計 Xamarin.Mac 應用程式的應用程式沙箱。



## <a name="related-links"></a>相關連結

- [發行至 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [關於應用程式沙箱](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [常見的應用程式沙箱問題](https://developer.apple.com/library/content/qa/qa1773/_index.html)
