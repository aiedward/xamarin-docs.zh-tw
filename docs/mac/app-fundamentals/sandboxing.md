---
title: 沙箱 Xamarin.Mac 應用程式
description: 本文涵蓋沙箱 Xamarin.Mac 應用程式，App Store 上的版本。 它涵蓋的所有項目移入沙箱化處理，例如容器目錄、 權利，決定使用者的權限、 權限分隔和核心強制執行。
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a02d7639975de092b05f31bacedd6bde4c9392f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787905"
---
# <a name="sandboxing-a-xamarinmac-app"></a>沙箱 Xamarin.Mac 應用程式

_本文涵蓋沙箱 Xamarin.Mac 應用程式，App Store 上的版本。它涵蓋的所有項目移入沙箱化處理，例如容器目錄、 權利，決定使用者的權限、 權限分隔和核心強制執行。_

## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，就像是使用 OBJECTIVE-C 或 Swift 時有沙箱應用程式的相同功能。

[![執行中應用程式的範例](sandboxing-images/intro01.png "執行的應用程式的範例")](sandboxing-images/intro01-large.png#lightbox)

在本文中，我們會使用沙箱 Xamarin.Mac 應用程式和所有進入沙箱化處理的項目中的基本概念： 容器目錄、 權利，決定使用者的權限、 權限分隔和核心強制執行。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="about-the-app-sandbox"></a>關於應用程式沙箱

沙箱應用程式提供強式防禦機制以防止可能由應用程式對系統資源的存取權限制在 Mac 上執行的惡意應用程式所造成的損害。

非沙箱化應用程式具有完整權限執行應用程式的使用者，可存取或執行任何動作，使用者可以。 如果應用程式包含安全性漏洞 （或任何它正在使用的架構），駭客可能可以利用這些弱點和使用該應用程式的 Mac 上執行的控制。

藉由限制每個應用程式為基礎的資源的存取權，沙箱化應用程式會提供防禦機制以防止遭竊、 損毀或惡意使用者的電腦上執行的應用程式部分行。

沙箱應用程式是內建 macOS （在核心層級強制執行），提供兩個策略的存取控制技術：

1. 沙箱應用程式開發人員用來描述_如何_應用程式互動的作業系統，而在這種方式，它被授予只才能取得這項工作完成，且沒有更多的存取權限。
2. 沙箱應用程式可讓使用者順暢地進一步存取透過開啟系統及儲存 對話方塊中，拖放作業和其他常見的使用者互動。

### <a name="preparing-to-implement-the-app-sandbox"></a>準備實作應用程式沙箱

將在本文中詳細討論應用程式沙箱的項目如下所示：

- 容器的目錄
- 權利
- 決定使用者的權限
- 權限分隔
- 核心強制

了解這些詳細資料之後，您可以建立採用應用程式沙箱 Xamarin.Mac 應用程式中的計劃。

首先，您必須判斷您的應用程式是否適合做為沙箱化處理 （大部分的應用程式都是）。 接下來，您必須解決任何 API 不相容，並判斷您需要的應用程式沙箱哪些項目。 最後，查看使用最大化您應用程式的深層級的權限分隔。

當採用沙箱應用程式之後，某些應用程式所使用的檔案系統位置將會不同。 特別是，您的應用程式必須將應用程式支援檔案、 資料庫、 快取和任何其他不是使用者的文件的檔案使用的容器目錄。 MacOS 和 Xcode 提供支援，以將這些類型的檔案從舊位置移轉到容器。

## <a name="sandboxing-quick-start"></a>沙箱化處理的快速入門

在本節中，我們將建立簡單的 Xamarin.Mac 應用程式會使用 Web 檢視 （這需要受限於下沙箱除非在特別要求的網路連線） 做為範例，開始使用應用程式沙箱。

我們將確認已實際沙箱化應用程式，並了解如何疑難排解及解決常見的應用程式沙箱錯誤。

### <a name="creating-the-xamarinmac-project"></a>建立 Xamarin.Mac 專案

讓我們執行下列命令以建立範例專案：

1. 啟動 Visual Studio 用於 Mac 和按一下**新方案...** 連結，將該帳戶加入 [檔案] > [帳戶設定] 對話方塊左側的 [所有帳戶] 清單中。
2. 從**新專案**對話方塊中，選取**Mac** > **應用程式** > **Cocoa 應用程式**: 

    [![建立新的應用程式 Cocoa](sandboxing-images/sample01.png "建立新的 Cocoa 應用程式")](sandboxing-images/sample01-large.png#lightbox)
3. 按一下**下一步**按鈕，輸入`MacSandbox`專案名稱，然後按一下**建立**按鈕： 

    [![輸入應用程式名稱](sandboxing-images/sample02.png "輸入應用程式名稱")](sandboxing-images/sample02-large.png#lightbox)
4. 在**方案板**，連按兩下**Main.storyboard**檔案將它開啟在 Xcode 中進行編輯： 

    [![編輯主要腳本](sandboxing-images/sample03.png "編輯主要腳本")](sandboxing-images/sample03-large.png#lightbox)
5. 拖曳**Web 檢視**拖曳到視窗中，調整大小以填滿內容區域，並將它設定為擴增和縮減與視窗： 

    [![加入 web 檢視](sandboxing-images/sample04.png "加入 web 檢視")](sandboxing-images/sample04-large.png#lightbox)
6. 建立 web 檢視呼叫插座`webView`: 

    [![建立新的輸出](sandboxing-images/sample05.png "建立新的輸出")](sandboxing-images/sample05-large.png#lightbox)
7. 返回 Visual Studio，針對 Mac 和按兩下**ViewController.cs**檔案**方案板**開啟進行編輯。
8. 加入下列 using 陳述式： `using WebKit;`
9. 請`ViewDidLoad`方法看起來像下列： 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. 儲存您的變更。

執行應用程式，並確保 Apple 網站將顯示在視窗中，如下所示：

[![顯示範例應用程式執行](sandboxing-images/sample06.png "顯示範例應用程式執行")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>簽署和佈建應用程式

我們可以啟用沙箱應用程式之前，我們先需要佈建並簽署 Xamarin.Mac 應用程式。

可讓下列作業：

1. 登入 Apple 開發人員入口網站： 

    [![登入 Apple 開發人員入口網站](sandboxing-images/sign01.png "登入 Apple 開發人員入口網站")](sandboxing-images/sign01-large.png#lightbox)
2. 選取**憑證、 識別項與設定檔**: 

    [![選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔](sandboxing-images/sign02.png "選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔")](sandboxing-images/sign02-large.png#lightbox)
3. 在下**Mac 應用程式**，選取**識別碼**: 

    [![選取識別項](sandboxing-images/sign03.png "選取識別項")](sandboxing-images/sign03-large.png#lightbox)
4. 建立新的應用程式識別碼： 

    [![建立新的應用程式識別碼](sandboxing-images/sign04.png "建立新的應用程式識別碼")](sandboxing-images/sign04-large.png#lightbox)
5. 在下**佈建的設定檔**，選取**開發**: 

    [![選取開發](sandboxing-images/sign05.png "選取開發")](sandboxing-images/sign05-large.png#lightbox)
6. 建立新的設定檔，並選取**Mac 應用程式開發**: 

    [![建立新的設定檔](sandboxing-images/sign06.png "建立新的設定檔")](sandboxing-images/sign06-large.png#lightbox)
7. 選取我們在上方建立的應用程式識別碼： 

    [![選取的應用程式識別碼](sandboxing-images/sign07.png "選取的應用程式識別碼")](sandboxing-images/sign07-large.png#lightbox)
8. 選取此設定檔的開發人員： 

    [![加入開發人員](sandboxing-images/sign08.png "加入開發人員")](sandboxing-images/sign08-large.png#lightbox)
9. 選取此設定檔的電腦： 

    [![選取 允許的電腦](sandboxing-images/sign09.png "選取允許的電腦")](sandboxing-images/sign09-large.png#lightbox)
10. 指定設定檔的名稱： 

    [![為設定檔命名](sandboxing-images/sign10.png "為設定檔指定的名稱")](sandboxing-images/sign10-large.png#lightbox)
11. 按一下**完成** 按鈕。

> [!IMPORTANT]
> 在某些情況下，您可能需要直接從 Apple 開發人員入口網站下載新的佈建設定檔，然後按兩下該安裝。 您也可能需要停止並重新啟動 Visual Studio for Mac 之前它能存取新的設定檔。

接下來，我們需要在開發電腦上載入新的應用程式識別碼和設定檔。 讓我們執行下列作業：

1. 啟動 Xcode，並選取**喜好設定**從**Xcode**功能表： 

    ![編輯帳戶在 Xcode 中的](sandboxing-images/sign11.png "編輯在 Xcode 中的帳戶")
2. 按一下**檢視詳細資料...** 按鈕： 

    ![按一下 [檢視詳細資料] 按鈕](sandboxing-images/sign12.png "按一下 [檢視詳細資料] 按鈕")
3. 按一下**重新整理**按鈕 （在較低的左下角）。
4. 按一下**完成** 按鈕。

接下來，我們需要我們 Xamarin.Mac 專案中選取新的應用程式識別碼和佈建設定檔。 讓我們執行下列作業：

1. 在**方案板**，連按兩下**Info.plist**檔案，以開啟它進行編輯。
2. 請確認**配套識別碼**符合我們前面所建立的應用程式識別碼 (範例： `com.appracatappra.MacSandbox`): 

    [![編輯套件組合識別碼](sandboxing-images/sign13.png "編輯配套識別碼")](sandboxing-images/sign13-large.png#lightbox)
3. 接下來，按兩下**Entitlements.plist**檔案，並確定我們**iCloud 鍵值存放區**和**iCloud 容器**全部都會相符我們前面所建立的應用程式識別碼 (範例：`com.appracatappra.MacSandbox`): 

    [![編輯 Entitlements.plist 檔案](sandboxing-images/sign17.png "編輯 Entitlements.plist 檔案")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存您的變更。
4. 在**方案板**，按兩下專案檔，以開啟它進行編輯的選項：  

    ![Editign 方案的選項](sandboxing-images/sign14.png "Editign 方案的選項")
5. 選取**Mac 簽署**，然後檢查**簽署應用程式套件組合**和**簽署安裝程式套件**。 在下**佈建設定檔**，請選取我們在上方建立： 

    ![設定佈建設定檔](sandboxing-images/sign15.png "設定佈建設定檔")
6. 按一下**完成** 按鈕。

> [!IMPORTANT]
> 您可能必須結束並重新啟動 Visual Studio for Mac，讓它能夠辨識新的應用程式識別碼和已安裝 Xcode 的佈建設定檔。

#### <a name="troubleshooting-provisioning-issues"></a>佈建的問題進行疑難排解

此時您應該嘗試執行應用程式，並確定所有項目是用來簽署，並正確佈建。 如果應用程式仍如常執行，所有項目是很好。 如果發生故障，您可能會收到與下列類似的對話方塊：

[![佈建問題 對話方塊範例](sandboxing-images/sign16.png "佈建問題 對話方塊範例")](sandboxing-images/sign16-large.png#lightbox)

以下是最常見的原因的佈建和簽章的問題：

- 應用程式配套識別碼不符合選取的設定檔的應用程式識別碼。
- 開發人員識別碼不符合選取的設定檔的開發人員識別碼。
- Mac 上所測試的 UUID 未註冊為選取的設定檔的一部分。

如果有問題，更正問題，Apple 開發人員入口網站上的，重新整理在 Xcode 中的設定檔並執行乾淨的組建，在 Visual Studio for mac。

### <a name="enable-the-app-sandbox"></a>啟用應用程式沙箱

專案選項中選取核取方塊以啟用應用程式沙箱。 請執行下列動作：

1. 在**方案板**，連按兩下**Entitlements.plist**檔案，以開啟它進行編輯。
2. 同時檢查**啟用權利**和**啟用應用程式沙箱**: 

    [![編輯權利，以及啟用沙箱](sandboxing-images/sign17.png "編輯權利，以及啟用沙箱")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存您的變更。

此時，您已啟用應用程式沙箱，但您先前未提供必要的網路存取 Web 檢視。 如果您執行應用程式現在，您應該取得空白視窗：

[![顯示 web 存取的封鎖](sandboxing-images/sample08.png "顯示被封鎖的 web 存取")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>正在驗證應用程式為沙箱化

除了封鎖行為的資源，有三種主要的方法，判斷是否已成功沙箱化 Xamarin.Mac 應用程式：

1. 在 尋找工具中，核取的內容`~/Library/Containers/`資料夾-如果應用程式為沙箱化，會有類似您的應用程式配套識別碼名為的資料夾 (範例： `com.appracatappra.MacSandbox`): 

    [![開啟應用程式套件組合](sandboxing-images/sample09.png "開啟應用程式套件組合")](sandboxing-images/sample09-large.png#lightbox)
2. 系統會為沙箱化 活動監視器中看到應用程式：
    - 啟動 活動監視器 (下`/Applications/Utilities`)。 
    - 選擇**檢視** > **資料行**，並確定**沙箱**勾選功能表項目。
    - 請確定沙箱的資料行讀取`Yes`應用程式： 

    [![檢查應用程式中，活動監視器](sandboxing-images/sample10.png "檢查活動監視器中的應用程式")](sandboxing-images/sample10-large.png#lightbox)
3. 請檢查應用程式二進位為沙箱化：
    - 啟動終端機應用程式。
    - 瀏覽至應用程式`bin`目錄。
    - 發出此命令： `codesign -dvvv --entitlements :- executable_path` (其中`executable_path`是您的應用程式的路徑): 

    [![檢查命令列上的應用程式](sandboxing-images/sample11.png "檢查命令列上的應用程式")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>偵錯的沙箱化應用程式

偵錯工具連接到 Xamarin.Mac 應用程式，透過 TCP，這表示，依預設啟用沙箱，當它是無法連線到應用程式，因此如果您嘗試執行應用程式沒有啟用適當的權限，您會收到錯誤 *「 無法連接到偵錯工具 」*。 

[![設定所需的選項](sandboxing-images/debug01.png "設定所需的選項")](sandboxing-images/debug01-large.png#lightbox)

**允許傳出網路連線 （用戶端）** 權限就是所需的偵錯工具，啟用此選項將允許正常地偵錯。 您無法偵錯，而它，因為我們已更新`CompileEntitlements`目標`msbuild`為沙箱化針對偵錯任何應用程式只建置的自動將該權限新增至權利。 發行組建應該使用未經修改的權利檔案中指定的權利。

### <a name="resolving-an-app-sandbox-violation"></a>解決應用程式沙箱違規

如果應用程式嘗試存取該資源的沙箱化 Xamarin.Mac 未明確允許，就會發生應用程式沙箱違規。 例如，我們 Web 檢視不再能夠顯示 Apple 網站。

適用於 Mac 的 Visual Studio 中所指定的權限設定不符合需求的應用程式時，就會發生最常見的應用程式沙箱違規的來源。 同樣地，回到我們的範例中，遺失網路連線保持無法運作的網頁檢視的權利。

#### <a name="discovering-app-sandbox-violations"></a>探索應用程式沙箱違規

如果您懷疑應用程式沙箱違規發生 Xamarin.Mac 應用程式中，以探索問題最快的方式是使用**主控台**應用程式。

請執行下列動作：

1. 編譯應用程式有問題，並執行它從 Visual Studio for mac。
2. 開啟**主控台**應用程式 (從`/Applications/Utilties/`)。
3. 選取**所有訊息**提要欄位中，輸入`sandbox`在搜尋中： 

    [![在主控台中的沙箱化處理問題的範例](sandboxing-images/resolve01.png "沙箱化處理問題，在主控台中的範例")](sandboxing-images/resolve01-large.png#lightbox)

對於我們上述的範例應用程式，您可以查看正在封鎖 Kernal`network-outbound`流量，因為應用程式的沙箱，因為我們不要求要有該權限。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>修正應用程式的沙箱與權利的違規

既然我們已經看到如何尋找應用程式沙箱違規，讓我們來看看如何解決它們藉由調整應用程式的權利。

請執行下列動作：

1. 在**方案板**，連按兩下**Entitlements.plist**檔案，以開啟它進行編輯。
2. 在下**權利**區段中，按一下**允許傳出網路連線 （用戶端）** 核取方塊： 

    [![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)
3. 將變更儲存至應用程式。

如果我們執行上述針對我們的範例應用程式，然後建置並執行它，如預期般現在會顯示 web 內容。

## <a name="the-app-sandbox-in-depth"></a>深入了解的沙箱應用程式

沙箱應用程式所提供的存取控制機制是少數而且容易了解。 不過，每個應用程式會採用應用程式沙箱的方式是唯一的且根據應用程式的需求。

提供您最大努力保護 Xamarin.Mac 應用程式遭到利用受惡意程式碼，需要有單一安全性漏洞的應用程式中 （或其中一個程式庫或架構會耗用） 取得的應用程式的互動與控制系統。

沙箱應用程式的設計是為了防止接管 （或限制它可能會造成的損害） 可讓您指定的應用程式預定的系統互動。 系統只會授與存取權的應用程式完成其工作所需的資源而無其他。

沙箱應用程式進行設計時，您為設計最糟的狀況。 如果應用程式未洩露出去惡意程式碼，則限制為只有檔案和應用程式的沙箱中的資源存取。

### <a name="entitlements-and-system-resource-access"></a>權利和存取系統資源

如我們所見上方，Xamarin.Mac 應用程式尚未被沙箱化授與的完整權限和執行應用程式之使用者的存取權。 受到惡意程式碼，如果未受保護的應用程式可以當做惡意行為，代理程式與寬，範圍可能的損害。

啟用應用程式沙箱，全部移除權限，您的最小集合，然後重新啟用以基礎只需要使用 Xamarin.Mac 應用程式的權利。 

您可以修改應用程式的應用程式沙箱資源藉由編輯其**Entitlements.plist**檔案和檢查，或選取編輯器的下拉式清單方塊中所需的權限：

[![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器的目錄和檔案系統存取

當 Xamarin.Mac 應用程式會採用應用程式沙箱時，它可以存取下列位置：

- **應用程式的容器目錄**位在第一次執行時，作業系統會建立特殊_容器目錄_其中所有的資源都將，只能夠存取。 應用程式將會有完整讀取/寫入存取此目錄。
- **應用程式群組的容器目錄**-您的應用程式可以被授與一或多個存取_群組容器_在應用程式之間共用相同的群組。
- **使用者指定的檔案**-您的應用程式中自動取得可明確地開啟或拖放到應用程式使用者的檔案的存取權。
- **相關項目**層與適當的權利，您的應用程式都可以存取檔案具有相同名稱但不同的擴充功能。 例如，儲存為兩者的文件`.txt`檔案和`.pdf`。
- **暫存目錄、 命令列工具的目錄和 world 可讀取的特定位置**-應用程式中其他妥善定義的位置，系統所指定的檔案有不同程度的存取。

#### <a name="the-app-container-directory"></a>應用程式的容器目錄

Xamarin.Mac 應用程式的應用程式的容器目錄具有下列特性：

- 它是在使用者的主目錄中隱藏的位置 (通常`~Library/Containers`)，而且可以存取與`NSHomeDirectory`應用程式中的函式 （請參閱下文）。 因為它是主目錄中，每個使用者會取得自己的容器應用程式。
- 應用程式具有不受限制讀取/寫入存取容器目錄和所有子目錄和檔案中。
- 大部分的 macOS 的路徑尋找應用程式開發介面會相對於應用程式的容器。 例如，容器會有它自己**文件庫**(透過存取`NSLibraryDirectory`)，**應用程式支援**和**喜好設定**子目錄。
- macOS 建立，並強制執行之間的連線及應用程式和其容器透過程式碼簽章。 即使是另一個應用程式嘗試欺騙應用程式使用其**配套識別碼**，它不能存取的容器，因為程式碼簽署。
- 容器不是針對使用者產生的檔案。 而是它的檔案，例如資料庫、 快取或其他特定類型的資料會使用您的應用程式。
- 如_鞋盒_類型的應用程式 （例如 Apple 的相片應用程式），使用者的內容將移到容器。

> [!IMPORTANT]
> 不幸的是，Xamarin.Mac 沒有 100%應用程式開發介面的涵蓋範圍尚未 （不同於 Xamarin.iOS)，因此`NSHomeDirectory`Xamarin.Mac 目前版本中尚未對應應用程式開發介面。

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

從 Mac macOS 10.7.5 （以及更高），可以使用應用程式`com.apple.security.application-groups`存取共用的容器，是通用群組中的所有應用程式的權限。 您可以使用此共用的容器，非使用者面對的內容，例如資料庫或其他類型的支援檔案 （例如快取）。

群組容器會自動加入至每個應用程式的沙箱容器 （如果它們是群組的一部分） 並儲存在`~/Library/Group Containers/<application-group-id>`。 群組識別碼_必須_開始您的開發小組 ID 與一段時間，例如：

```plist
<team-id>.com.company.<group-name>
```

如需詳細資訊，請參閱 Apple[加入至應用程式群組的應用程式](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)中[權利金鑰參考](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>應用程式容器之外的 Powerbox 和檔案系統存取

沙箱的 Xamarin.Mac 應用程式可以存取其容器之外的檔案系統位置，以下列方式：

- 在 （透過開啟和儲存對話方塊或其他方法，例如拖曳和卸除） 使用者的特定方向。
- 使用特定的檔案系統位置權利 (例如`/bin`或`/usr/lib`)。
* 檔案系統位置時在特定是世界可讀取 （例如共用） 的目錄。

_Powerbox_是 macOS 安全性技術，以展開沙箱化 Xamarin.Mac 應用程式的檔案存取權限的使用者進行互動。 Powerbox 已沒有可用的 API，但是當應用程式會呼叫會以透明的方式啟動`NSOpenPanel`或`NSSavePanel`。 Powerbox 存取已啟用透過您為 Xamarin.Mac 應用程式的權利。

當沙箱化應用程式會顯示 開啟或儲存對話方塊時，視窗呈現 Powerbox （以及不 AppKit），因此可以存取任何檔案或目錄的使用者具有存取權。

當使用者選取檔案或目錄的 開啟或儲存對話方塊 （或拖曳到應用程式的圖示是） 時，Powerbox 會將應用程式的沙箱關聯的路徑。

此外，系統會自動允許下列命令以沙箱化應用程式：

- 連線至系統的輸入方法。
- 呼叫服務從使用者選取**服務**功能表 (只針對服務標示為_沙箱應用程式的安全_服務提供者)。
- 開啟檔案選擇使用者透過**開啟最近**功能表。
- 其他應用程式之間使用複製和貼。
- 從下列全球讀取位置讀取檔案：
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- 讀取和寫入檔案中所建立的目錄`NSTemporaryDirectory`。

是預設值，開啟或儲存供沙箱化 Xamarin.Mac 應用程式的檔案仍然可以存取應用程式終止 （除非應用程式結束時仍開啟檔案） 為止。 開啟的檔案將會自動還原對透過 macOS 恢復功能的應用程式的沙箱應用程式啟動的下一次。

若要提供持續性位 Xamarin.Mac 應用程式容器之外的檔案，使用安全性範圍的書籤 （請參閱下文）。

#### <a name="related-items"></a>相關項目

沙箱應用程式可讓應用程式能夠存取相關的項目具有相同的檔名，但副檔名不同。 此功能有兩個部分： a） 中的應用程式相關的延伸模組清單`Info.plst`檔案，b） 程式碼，告訴沙箱應用程式將會做什麼與這些檔案。

有兩種案例，這可讓有意義：

1. 應用程式必須能夠將儲存至檔案 （最新的延伸模組） 的不同版本。 例如，匯出`.txt`檔案`.pdf`檔案。 若要處理這種情況下，您必須使用`NSFileCoordinator`存取檔案。 您會呼叫`WillMove(fromURL, toURL)`方法首先，將檔案移至新的延伸模組，然後呼叫`ItemMoved(fromURL, toURL)`。
2. 應用程式必須使用不同的延伸模組開啟一個副檔名的主要檔案和數個支援檔案。 例如，電影和子字幕檔案。 使用`NSFilePresenter`來存取的次要檔案。 主要檔案提供給`PrimaryPresentedItemURL`屬性和次要的檔案，以`PresentedItemURL`屬性。 主檔案開啟時，呼叫`AddFilePresenter`方法`NSFileCoordinator`次要檔案註冊的類別。

在這兩種情況下，應用程式的**Info.plist**檔案必須宣告應用程式可以開啟的文件型別。 針對任何檔案類型，加入`NSIsRelatedItemType`(值為`YES`) 中的項目至`CFBundleDocumentTypes`陣列。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>開啟和儲存對話方塊行為與沙箱化應用程式

下列限制會放在`NSOpenPanel`和`NSSavePanel`時從沙箱化 Xamarin.Mac 應用程式呼叫它們：

- 您無法以程式設計方式叫用**確定** 按鈕。
- 以程式設計的方式，您就無法變更使用者的選取項目中`NSOpenSavePanelDelegate`。

此外，下列繼承修改已就緒：

- **非沙箱化應用程式** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全性範圍的書籤和持續性資源存取

如前所述，Xamarin.Mac 沙箱化應用程式可以存取的檔案或其容器透過直接使用者互動 （所提供 PowerBox） 之外的資源。 不過，這些資源的存取權不會自動保存在啟動應用程式或系統重新啟動。

使用_Security-Scoped 書籤_，Xamarin.Mac 沙箱化應用程式可以保留使用者意圖，並保有存取指定資源應用程式後重新啟動。

#### <a name="security-scoped-bookmark-types"></a>安全性範圍的書籤的型別

當使用 Security-Scoped 書籤和持續性資源存取，有兩個 sistine 使用案例：

- **App-Scoped 書籤提供持續的存取權的使用者指定的檔案或資料夾。** 

    例如，如果沙箱 Xamarin.Mac 應用程式可讓用來開啟外部文件進行編輯 (使用`NSOpenPanel`)，應用程式可以建立 App-Scoped 書籤，以便在未來再次存取相同的檔案。
- **Document-Scoped 書籤提供特定文件永續性的存取子的檔案。** 

例如，視訊編輯應用程式可建立專案檔，可以存取個別影像、 視訊剪輯和更新版本會結合成單一的電影的音效檔。

當使用者匯入的資源檔加入專案 (透過`NSOpenPanel`)，應用程式會建立儲存在專案中之項目的 Document-Scoped 書籤，以便檔案永遠可供存取應用程式。

可以解析 Document-Scoped 書籤的書籤的資料和文件本身可以開啟任何應用程式。 這個支援可攜性，讓使用者將專案檔案傳送至另一個使用者，以及取得所有的書籤也使用它們。

> [!IMPORTANT]
> Document-Scoped 書籤可以_只_指向單一檔案，不是資料夾，該檔案不能是系統所使用的位置 (例如`/private`或`/Library`)。

#### <a name="using-security-scoped-bookmarks"></a>使用安全性範圍的書籤

使用任一種 Security-Scoped 書籤，需要您執行下列步驟：

1. **在需要使用 Security-Scoped 書籤 Xamarin.Mac 應用程式中設定適當的權利**-For App-Scoped 書籤，設定`com.apple.security.files.bookmarks.app-scope`權利鍵`true`。 Document-Scoped 書籤，設定`com.apple.security.files.bookmarks.document-scope`權利鍵`true`。
2. **建立 Security-Scoped 書籤**-會這麼做的任何檔案或資料夾的使用者提供存取權 (透過`NSOpenPanel`例如)，應用程式必須持續存取。 使用`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`方法`NSUrl`類別，建立書籤。
3. **解決 Security-Scoped 書籤**-應用程式需要存取一次資源 （例如，在重新啟動） 之後時它將需要解析安全性範圍的 URL 的書籤。 使用`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`方法`NSUrl`類別解析書籤。
4. **明確通知您想要從 Security-Scoped URL 存取檔案系統**-這個步驟必須完成之後立即取得上述 Security-Scoped URL 或當您稍後想要有之後重新取得資源的存取權時，釋放您的存取權。 呼叫`StartAccessingSecurityScopedResource ()`方法`NSUrl`開始存取 Security-Scoped URL 的類別。
5. **明確通知的系統在您完成從 Security-Scoped URL 存取的檔案**-儘速，您應該通知系統應用程式當不再需要時檔案的存取權 （例如，如果使用者將關閉）。 呼叫`StopAccessingSecurityScopedResource ()`方法`NSUrl`類別來停止存取 Security-Scoped URL。

放棄資源的存取權之後，您必須返回步驟 4，再次以重新建立存取。 重新啟動 Xamarin.Mac 應用程式，您必須回到步驟 3，並重新解決書籤。

> [!IMPORTANT]
> 釋放 Security-Scoped URL 資源的存取權的失敗會造成遺漏核心資源 Xamarin.Mac 應用程式。 如此一來，應用程式將無法再加入到其容器的檔案系統位置，直到重新啟動它。

### <a name="the-app-sandbox-and-code-signing"></a>沙箱應用程式和程式碼簽署

啟用應用程式沙箱及啟用的特定需求 （透過權利），Xamarin.Mac 應用程式之後，您必須撰寫程式碼簽署專案的沙箱才會生效。 您必須執行的程式碼簽署，因為所需的沙箱應用程式的權利連結至應用程式的簽章。 

macOS 會強制執行的應用程式的容器和其程式碼簽章，在沒有其他應用程式可以存取該容器中，即使它詐騙的應用程式配套識別碼。 這種方式之間的連結 這項機制的運作方式，如下所示：

1. 當系統建立應用程式的容器時，它會設定_存取控制清單_(ACL) 的容器上。 在清單中的初始存取控制項目包含應用程式的_指定需求_(DR) 期間，用來描述如何未來版本的應用程式可以時加以辨識 （升級）。
2. 每次使用相同的套件組合識別碼的應用程式啟動時，系統會檢查應用程式的程式碼簽章符合其中一種容器的 ACL 中的項目指定指定的需求。 如果系統找不到相符項目，它會防止應用程式啟動。

程式碼簽署的運作方式如下：

1. 建立之前 Xamarin.Mac 專案，請取得開發憑證、 發佈憑證及開發人員識別碼憑證從 Apple 開發人員入口網站。
2. 當 Mac App Store 分散 Xamarin.Mac 應用程式時，它是以 Apple 程式碼簽章進行簽署。

當測試和偵錯，您會使用您登入 （這將用來建立應用程式容器） Xamarin.Mac 應用程式的版本。 稍後，如果您想要測試，或從 Apple App Store 安裝的版本，它會使用的 Apple 簽章簽署，將無法啟動 （因為它沒有相同的程式碼簽章，做為原始的應用程式容器）。 在此情況下，您會看到當機報告如下所示：

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要修正此問題，您必須調整為指向 Apple 帶正負號版本的應用程式的 ACL 項目。

如需有關建立和下載佈建的設定檔所需進行沙箱化處理的詳細資訊，請參閱[簽署和佈建應用程式](#Signing_and_Provisioning_the_App)上一節。

#### <a name="adjusting-the-acl-entry"></a>調整 ACL 項目

若要允許 Xamarin.Mac 應用程式執行的 Apple 帶正負號的版本，執行下列作業：

1. 開啟 Terminal 應用程式 (在`/Applications/Utilities`)。
2. 開啟 Apple 帶正負號版本 Xamarin.Mac 應用程式的尋找工具視窗。
3. 型別`asctl container acl add -file `Terminal 視窗中。
4. 將 Xamarin.Mac 應用程式的圖示從 [搜尋] 視窗拖放在終端機視窗。
5. 檔案的完整路徑會新增至終端機中的命令。
6. 按**Enter**執行命令。

容器的 ACL 現在包含指定的程式碼的需求 Xamarin.Mac 應用程式的兩個版本，而且 macOS 現在允許執行的其中一個版本。

#### <a name="display-a-list-of-acl-code-requirements"></a>顯示一份 ACL 程式碼要求

您可以檢視程式碼要求的清單中容器的 ACL，執行下列動作：

1. 開啟 Terminal 應用程式 (在`/Applications/Utilities`)。
2. 輸入 `asctl container acl list -bundle <container-name>`。
3. 按**Enter**執行命令。

`<container-name>`通常是 Xamarin.Mac 應用程式的配套識別碼。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>設計應用程式沙箱 Xamarin.Mac 的應用程式

沒有設計應用程式沙箱 Xamarin.Mac 的應用程式時，應遵循的一般工作流程。 話雖如此，實作沙箱的應用程式中的特性會成為唯一指定的應用程式的功能。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>採用應用程式沙箱六個步驟

通常應用程式沙箱設計 Xamarin.Mac 的應用程式包含下列步驟：

1. 判斷應用程式則適合進行沙箱化處理。
2. 設計的開發和散發的策略。
3. 解決任何 API 不相容。
4. 適用於 Xamarin.Mac 專案所需的應用程式沙箱權利。
5. 加入使用 XPC 的權限分隔。
6. 實作移轉策略。

> [!IMPORTANT]
> 必須不只有沙箱您應用程式套件組合，但也會每個包含協助程式的主要可執行檔的應用程式或該配套中的工具。 這是為了從 Mac App Store 發佈任何應用程式，而且可能的話，應該執行任何其他形式的應用程式發佈。

如需所有可執行檔中的二進位檔 Xamarin.Mac 應用程式套件組合的清單，請在終端機中輸入下列命令：

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`的名稱和路徑的應用程式套件組合。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>判斷是否適合進行沙箱化處理 Xamarin.Mac 應用程式

大部分的 Xamarin.Mac 應用程式會與應用程式沙箱完全相容，因此，適用於沙箱化處理。 如果應用程式所需的行為，不允許應用程式沙箱，您應該考慮的替代方式。

如果您的應用程式需要下列其中一個下列行為，它與不相容應用程式沙箱：

- **授權服務**-與應用程式沙箱，您無法使用函式中所述[授權服務 C 參考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)。
- **協助工具應用程式開發介面**-您無法沙箱輔助應用程式，例如螢幕助讀程式或控制的其他應用程式的應用程式。
- **Apple 事件傳送至任意應用程式**-如果應用程式需要 Apple 事件傳送至未知的任意應用程式，不能沙箱化。 被呼叫的應用程式的已知清單，應用程式仍然可以被沙箱化以及權利必須包含呼叫的應用程式清單。
- **將使用者資訊字典在發佈通知傳送到其他工作**-與應用程式沙箱，您不能包含`userInfo`字典時張貼到`NSDistributedNotificationCenter`傳訊其他工作的物件。
- **載入核心延伸**-禁止應用程式沙箱核心延伸模組的載入。
- **模擬使用者輸入中開啟和儲存對話方塊**-以程式設計方式操作開啟或儲存對話方塊，以模擬或變更使用者的輸入禁止的應用程式沙箱。
- **存取或其他應用程式上的偏好設定**-操作的其他應用程式設定禁止應用程式沙箱。
- **設定網路設定**-禁止沙箱應用程式管理的網路設定。
- **結束其他應用程式**-應用程式沙箱禁止使用`NSRunningApplication`終止其他應用程式。

### <a name="resolving-api-incompatibilities"></a>解決應用程式開發介面不相容

在設計應用程式的沙箱時 Xamarin.Mac 應用程式，可能會遇到一些 macOS Api 的使用方式不相容。

以下是一些常見的問題及解決這些問題，您可以執行的動作：

- **開啟、 儲存和追蹤的文件**-如果您要管理以外使用任何技術文件`NSDocument`，則應該切換到它，因為沙箱應用程式的內建支援。 `NSDocument` 自動搭配 PowerBox，並保持您沙箱內的文件，如果使用者將它們移搜尋工具中提供支援。
- **保留檔案系統資源的存取權**-如果 Xamarin.Mac 取決於其容器，以外之資源的永久存取權的應用程式來保有存取使用安全性範圍的書籤。
- **建立應用程式的登入項目**-與應用程式沙箱，您無法建立項目使用的登入`LSSharedFileList`也可以處理使用的啟動服務的狀態不`LSRegisterURL`。 使用`SMLoginItemSetEnabled`函式 a 中所述[加入登入項目使用服務管理架構](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)文件。
- **存取使用者資料**-如果您使用 POSIX 函式，例如`getpwuid`從目錄服務中取得使用者的主目錄，請考慮使用 Cocoa 或核心 Foundation 符號，例如`NSHomeDirectory`。
- **存取喜好設定的其他應用程式**-因為修改喜好設定將該容器內，以及存取其他應用程式喜好設定中的不允許應用程式沙箱會指示路徑-尋找應用程式開發介面應用程式的容器。 
- **在 Web 檢視中使用 HTML5 內嵌視訊**-如果 Xamarin.Mac 應用程式使用 WebKit 播放內嵌的 HTML5 視訊，您也必須連結針對 AV Foundation framework 應用程式。 沙箱應用程式會防止 CoreMedia 播放否則這些影片。

### <a name="applying-required-app-sandbox-entitlements"></a>套用必要的沙箱應用程式權利

您必須編輯您想要在應用程式沙箱中執行，並檢查任何 Xamarin.Mac 應用程式的權利**啟用應用程式沙箱**核取方塊。

根據應用程式功能，您可能需要啟用存取作業系統功能或資源的其他權利。 沙箱應用程式運作，最適合您最小化您的要求執行您的應用程式所需的最低限度的權利因此只要隨機啟用的權利。

若要判斷哪些 Xamarin.Mac 應用程式需要的權利，執行下列作業：

1. 啟用應用程式沙箱並執行 Xamarin.Mac 應用程式。
2. 執行應用程式的功能。
3. 開啟主控台應用程式 (用於`/Applications/Utilities`)，並尋找`sandboxd`違規**所有訊息**記錄檔。
4. 每個`sandboxd`違規，而非其他的檔案系統位置使用應用程式容器解決問題，或適用於應用程式以存取受限制的作業系統功能的沙箱權利。
5. 重新執行，然後重新測試 Xamarin.Mac 應用程式的所有功能。
6. 重複直到所有`sandboxd`違規均已解決。

### <a name="add-privilege-separation-using-xpc"></a>新增使用 XPC 的權限分隔

當開發應用程式沙箱 Xamarin.Mac 應用程式，權限和存取權，條款中查看應用程式的行為，則請考慮將高風險的作業分成 XPC 服務。

如需詳細資訊，請參閱 Apple[建立 XPC 服務](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)和[精靈和服務程式設計指南 》](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>實作移轉策略

如果您要釋出 Xamarin.Mac 應用程式不是先前沙箱化的新沙箱化版本，您必須確定目前的使用者有順利的升級路徑。 

 如需如何實作容器的移轉資訊清單的詳細資訊，請閱讀 Apple 的[應用程式移轉至沙箱](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)文件。

## <a name="summary"></a>總結

這篇文章已取得沙箱詳細的探討 Xamarin.Mac 應用程式。 首先，我們會建立只 Xamarin.Mac 應用程式以顯示應用程式沙箱的基本概念。 接下來，我們也示範了如何解決沙箱違規。 然後，我們採用深入查看應用程式沙箱和我們探討了最後，應用程式沙箱設計 Xamarin.Mac 的應用程式。



## <a name="related-links"></a>相關連結

- [發行至 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [有關沙箱應用程式](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [沙箱應用程式的常見問題](https://developer.apple.com/library/content/qa/qa1773/_index.html)
