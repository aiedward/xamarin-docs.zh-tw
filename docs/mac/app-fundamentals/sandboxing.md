---
title: 沙箱化 Xamarin. Mac 應用程式
description: 本文說明如何在 App Store 上進行用於發行的 Xamarin. Mac 應用程式沙箱。 其中涵蓋所有進入沙箱的元素，例如容器目錄、權利、使用者決定的許可權、許可權區隔，以及核心強制。
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 4558a9bd19810f8759010861d8a2e4b8cab09c56
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770292"
---
# <a name="sandboxing-a-xamarinmac-app"></a>沙箱化 Xamarin. Mac 應用程式

_本文說明如何在 App Store 上進行用於發行的 Xamarin. Mac 應用程式沙箱。其中涵蓋所有進入沙箱的元素，例如容器目錄、權利、使用者決定的許可權、許可權區隔，以及核心強制。_

## <a name="overview"></a>總覽

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您的功能與使用目標-C 或 Swift 時一樣，可以沙箱應用程式。

[![執行中應用程式的範例](sandboxing-images/intro01.png "執行中應用程式的範例")](sandboxing-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用沙箱的基本概念，以及進入沙箱的所有元素：容器目錄、權利、使用者決定的許可權、許可權區隔，以及核心強制。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔的「將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md) 」一節，它會說明用來將`Register` C#類別`Export`連線到的和屬性。目標-C 物件和 UI 元素。

## <a name="about-the-app-sandbox"></a>關於應用程式沙箱

應用程式沙箱會藉由限制應用程式對系統資源的存取，提供強大的防護，以防止在 Mac 上執行的惡意應用程式所造成的損毀。

非沙箱化應用程式具有執行應用程式之使用者的完整許可權，而且可以存取或執行使用者可以進行的任何動作。 如果應用程式包含安全性漏洞（或其使用的任何架構），駭客可能會利用這些弱點，並使用應用程式來控制其執行所在的 Mac。

藉由限制每個應用程式的資源存取權，沙箱應用程式可針對使用者電腦上執行之應用程式的部分，提供一條防線來對抗遭竊、損毀或惡意意圖。

應用程式沙箱是內建于 macOS （在核心層級強制執行）的存取控制技術，可提供雙重策略：

1. 應用程式沙箱可讓開發人員描述應用程式與 OS 的互動_方式_，如此一來，就只會授與完成工作所需的存取權限，而且不再需要。
2. 應用程式沙箱可讓使用者透過 [開啟] 和 [儲存] 對話方塊、拖放作業和其他常見的使用者互動，順暢地授與系統的進一步存取權。

### <a name="preparing-to-implement-the-app-sandbox"></a>準備執行應用程式沙箱

本文會詳細討論應用程式沙箱的元素，如下所示：

- 容器目錄
- 權利
- 使用者決定的許可權
- 許可權分隔
- 核心強制

瞭解這些詳細資料之後，您將能夠建立在 Xamarin. Mac 應用程式中採用應用程式沙箱的計畫。

首先，您必須判斷您的應用程式是否為沙箱的理想候選（大部分應用程式都是）。 接下來，您必須解決所有 API 不相容的問題，並判斷您將需要哪些應用程式沙箱的元素。 最後，請參閱使用許可權分離來最大化應用程式的防禦層級。

採用應用程式沙箱時，您的應用程式使用的某些檔案系統位置將會不同。 特別是，您的應用程式將會有一個容器目錄，用於應用程式支援檔案、資料庫、快取和其他非使用者檔的檔案。 MacOS 和 Xcode 都支援將這些檔案類型從其舊版位置遷移至容器。

## <a name="sandboxing-quick-start"></a>沙箱快速入門

在本節中，我們將建立使用 Web View 的簡單 Xamarin. Mac 應用程式（這需要在沙箱下限制的網路連線，除非特別要求）作為應用程式沙箱入門的範例。

我們會驗證應用程式是否已實際進行沙箱化，並瞭解如何針對常見的應用程式沙箱錯誤進行疑難排解並加以解決。

### <a name="creating-the-xamarinmac-project"></a>建立 Xamarin. Mac 專案

讓我們執行下列步驟來建立範例專案：

1. 啟動 Visual Studio for Mac，然後按一下 [**新增方案]。** 連結，將該帳戶加入 [檔案] > [帳戶設定] 對話方塊左側的 [所有帳戶] 清單中。
2. 從 [**新增專案**] 對話方塊中，選取 [ **Mac**  >  **app**  >  **Cocoa 應用程式**]：

    [![建立新的 Cocoa 應用程式](sandboxing-images/sample01.png "建立新的 Cocoa 應用程式")](sandboxing-images/sample01-large.png#lightbox)
3. 按 [**下一步]** 按鈕，輸入`MacSandbox`做為專案名稱，然後按一下 [**建立**] 按鈕：

    [![輸入應用程式名稱](sandboxing-images/sample02.png "輸入應用程式名稱")](sandboxing-images/sample02-large.png#lightbox)
4. 在  **Solution Pad**中，按兩下**主要**的分鏡腳本檔案，在 Xcode 中開啟它進行編輯：

    [![編輯主要]分鏡腳本(sandboxing-images/sample03.png "編輯主要")分鏡腳本](sandboxing-images/sample03-large.png#lightbox)
5. 將 [ **Web 視圖**] 拖曳至視窗，並調整其大小以填滿內容區域，並將它設定為使用視窗放大和縮小：

    [![新增 web view](sandboxing-images/sample04.png "新增 web view")](sandboxing-images/sample04-large.png#lightbox)
6. 建立 web view 的插座，其名稱`webView`為：

    [![建立新的插座](sandboxing-images/sample05.png "建立新的插座")](sandboxing-images/sample05-large.png#lightbox)
7. 返回 Visual Studio for Mac，然後按兩下**Solution Pad**中的**ViewController.cs**檔案，將它開啟以供編輯。
8. 新增下列 using 語句：`using WebKit;`
9. `ViewDidLoad`讓方法看起來如下所示：

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. 儲存您的變更。

執行應用程式，並確定 Apple 網站顯示在視窗中，如下所示：

[![顯示範例應用程式執行](sandboxing-images/sample06.png "顯示範例應用程式執行")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>簽署和布建應用程式

在我們可以啟用應用程式沙箱之前，我們必須先布建並簽署我們的 Xamarin. Mac 應用程式。

讓我們執行下列動作：

1. 登入 Apple 開發人員入口網站：

    [![登入 Apple Developer 入口網站](sandboxing-images/sign01.png "登入 Apple Developer 入口網站")](sandboxing-images/sign01-large.png#lightbox)
2. 選取**憑證、識別碼 & 設定檔**：

    [![選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔](sandboxing-images/sign02.png "選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔")](sandboxing-images/sign02-large.png#lightbox)
3. 在 [ **Mac 應用程式**] 下，選取 [**識別碼**]：

    [![選取識別碼](sandboxing-images/sign03.png "選取識別碼")](sandboxing-images/sign03-large.png#lightbox)
4. 為應用程式建立新的識別碼：

    [![建立新的應用程式識別碼](sandboxing-images/sign04.png "建立新的應用程式識別碼")](sandboxing-images/sign04-large.png#lightbox)
5. 在 [布建**設定檔**] 底下，選取 [**開發**]：

    [![選取開發](sandboxing-images/sign05.png "選取開發")](sandboxing-images/sign05-large.png#lightbox)
6. 建立新的設定檔，然後選取 [ **Mac 應用程式開發**]：

    [![建立新的設定檔](sandboxing-images/sign06.png "建立新的設定檔")](sandboxing-images/sign06-large.png#lightbox)
7. 選取我們在上面建立的應用程式識別碼：

    [![選取應用程式識別碼](sandboxing-images/sign07.png "選取應用程式識別碼")](sandboxing-images/sign07-large.png#lightbox)
8. 選取此設定檔的開發人員：

    [![加入開發人員](sandboxing-images/sign08.png "加入開發人員")](sandboxing-images/sign08-large.png#lightbox)
9. 選取此設定檔的電腦：

    [![選取允許的電腦](sandboxing-images/sign09.png "選取允許的電腦")](sandboxing-images/sign09-large.png#lightbox)
10. 提供設定檔的名稱：

    [![提供設定檔的名稱](sandboxing-images/sign10.png "提供設定檔的名稱")](sandboxing-images/sign10-large.png#lightbox)
11. 按一下 [**完成**] 按鈕。

> [!IMPORTANT]
> 在某些情況中，您可能需要直接從 Apple 的開發人員入口網站下載新的布建設定檔，然後按兩下以進行安裝。 您也可能需要停止並重新啟動 Visual Studio for Mac，才能存取新的設定檔。

接下來，我們需要在開發電腦上載入新的應用程式識別碼和設定檔。 讓我們執行下列動作：

1. 啟動 Xcode，並從 [ **Xcode** ] 功能表選取 [**喜好**設定]：

    ![在 Xcode 中編輯帳戶](sandboxing-images/sign11.png "在 Xcode 中編輯帳戶")
2. 按一下 [ **View Details ...** ] 按鈕：

    ![按一下 [View Details] 按鈕](sandboxing-images/sign12.png "按一下 [View Details] 按鈕")
3. 按一下 [重新整理 **] 按鈕（** 位於左下角）。
4. 按一下 [**完成**] 按鈕。

接下來，我們需要在 Xamarin. Mac 專案中選取新的應用程式識別碼和布建設定檔。 讓我們執行下列動作：

1. 在  **Solution Pad**中，按兩下**plist**檔案以開啟它進行編輯。
2. 請確定套件組合**識別碼**符合我們在上面建立的應用程式 ID （ `com.appracatappra.MacSandbox`範例：）：

    [![編輯]套件組合識別碼(sandboxing-images/sign13.png "編輯")套件組合識別碼](sandboxing-images/sign13-large.png#lightbox)
3. 接下來，按兩下**plist**檔案，並確定**Icloud 金鑰值存放區**和**icloud 容器**全都符合我們在上面建立的應用程式識別碼（範例： `com.appracatappra.MacSandbox`）：

    [![編輯 plist]檔案(sandboxing-images/sign17.png "編輯 plist")檔案](sandboxing-images/sign17-large.png#lightbox)
4. 儲存您的變更。
5. 在  **Solution Pad**中，按兩下專案檔以開啟其編輯選項：

    ![Editign 解決方案的選項](sandboxing-images/sign14.png "Editign 解決方案的選項")
6. 選取 [ **Mac 簽署**]，然後核取 **[簽署應用程式**套件組合] 並**簽署安裝程式封裝**。 在 [布建**設定檔**] 底下，選取我們在上面建立的檔案：

    ![設定布建設定檔](sandboxing-images/sign15.png "設定布建設定檔")
7. 按一下 [**完成**] 按鈕。

> [!IMPORTANT]
> 您可能必須先結束並重新啟動 Visual Studio for Mac，才能讓它辨識 Xcode 所安裝的新應用程式識別碼和布建設定檔。

#### <a name="troubleshooting-provisioning-issues"></a>針對布建問題進行疑難排解

此時，您應該嘗試執行應用程式，並確定所有專案都已正確簽署和布建。 如果應用程式仍以先前的方式執行，則所有內容都很好。 發生失敗時，您可能會看到如下所示的對話方塊：

[![範例布建問題對話方塊](sandboxing-images/sign16.png "範例布建問題對話方塊")](sandboxing-images/sign16-large.png#lightbox)

以下是布建和簽署問題的最常見原因：

- 應用程式套件組合識別碼不符合所選設定檔的應用程式識別碼。
- 開發人員識別碼不符合所選設定檔的開發人員識別碼。
- 所測試之 Mac 的 UUID 不會註冊為所選設定檔的一部分。

在發生問題的情況下，請更正 Apple Developer 入口網站上的問題、重新整理 Xcode 中的設定檔，並在 Visual Studio for Mac 中執行乾淨的組建。

### <a name="enable-the-app-sandbox"></a>啟用應用程式沙箱

您可以選取 [專案] 選項中的核取方塊來啟用應用程式沙箱。 請執行下列動作：

1. 在  **Solution Pad**中，按兩下**plist**檔案以開啟它進行編輯。
2. 勾選 [**啟用權利**] 和 [**啟用應用程式沙箱**]：

    [![編輯權利及啟用沙箱](sandboxing-images/sign17.png "編輯權利及啟用沙箱")](sandboxing-images/sign17-large.png#lightbox)
3. 儲存您的變更。

此時，您已啟用應用程式沙箱，但尚未提供 Web View 所需的網路存取權。 如果您現在執行應用程式，您應該會看到空白視窗：

[![顯示封鎖的 web 存取](sandboxing-images/sample08.png "顯示封鎖的 web 存取")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>正在驗證應用程式是否已沙箱化

除了資源封鎖行為之外，還有三個主要的方式可分辨 Xamarin. Mac 應用程式是否已成功進行沙箱化：

1. 在搜尋工具中，檢查`~/Library/Containers/`資料夾的內容-如果應用程式已沙箱化，將會有一個名為的資料夾，如同您應用程式的套件組合識別碼（例如： `com.appracatappra.MacSandbox`）：

    [![開啟應用程式的]配套(sandboxing-images/sample09.png "開啟應用程式的")配套](sandboxing-images/sample09-large.png#lightbox)
2. 系統會在活動監視器中將應用程式視為沙箱：
    - 啟動活動監視器（在`/Applications/Utilities`底下）。
    - 選擇 [**視圖** > 資料**行**]，並確定已核取 [**沙箱**] 功能表項目。
    - 確定您的應用程式會`Yes`讀取 [沙箱] 資料行：

    [![檢查活動監視器中的應用程式](sandboxing-images/sample10.png "檢查活動監視器中的應用程式")](sandboxing-images/sample10-large.png#lightbox)
3. 檢查應用程式二進位檔是否已沙箱化：
    - 啟動終端機應用程式。
    - 流覽至 [應用`bin`程式] 目錄。
    - 發出此命令： `codesign -dvvv --entitlements :- executable_path` （其中`executable_path`是您的應用程式的路徑）：

    [![在命令列上檢查應用程式](sandboxing-images/sample11.png "在命令列上檢查應用程式")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>對沙箱應用程式進行調試

偵錯工具會透過 TCP 連線到 Xamarin. Mac 應用程式，這表示根據預設，當您啟用沙箱時，它無法連線到應用程式，因此如果您嘗試在未啟用適當許可權的情況下執行應用程式，就會收到錯誤「*無法連接到偵錯工具」* .

[![設定必要的選項](sandboxing-images/debug01.png "設定必要的選項")](sandboxing-images/debug01-large.png#lightbox)

「**允許外寄網路連線（用戶端）** 」許可權就是偵錯工具所需的許可權，讓這種連線能夠正常進行偵測。 因為您無法在沒有此程式的情況下進行`CompileEntitlements` debug， `msbuild`所以我們已更新的目標，以便將該許可權自動新增至任何已針對 debug build 進行沙箱化之應用程式的權利。 發行組建應使用權利檔案中所指定的權利（未經修改）。

### <a name="resolving-an-app-sandbox-violation"></a>解決應用程式沙箱違規

如果沙箱化的 Xamarin. Mac 應用程式嘗試存取未明確允許的資源，就會發生應用程式沙箱違規。 例如，我們的 Web View 無法再顯示 Apple 網站。

當 Visual Studio for Mac 中指定的權利設定不符合應用程式的需求時，就會發生最常見的應用程式沙箱違規來源。 同樣地，回到我們的範例，這是讓 Web View 無法運作的遺失網路連線權利。

#### <a name="discovering-app-sandbox-violations"></a>探索應用程式沙箱違規

如果您懷疑 Xamarin. Mac 應用程式中發生應用程式沙箱違規，探索問題最快的方法是使用**主控台**應用程式。

請執行下列動作：

1. 編譯有問題的應用程式，並從 Visual Studio for Mac 加以執行。
2. 開啟**主控台**應用程式（來自`/Applications/Utilties/`）。
3. 選取提要欄位中的 [**所有訊息**]，然後在搜尋中輸入`sandbox` ：

    [![主控台中沙箱問題的範例](sandboxing-images/resolve01.png "主控台中沙箱問題的範例")](sandboxing-images/resolve01-large.png#lightbox)

針對上述範例應用程式，您可以看到內核因為應用程式沙箱而`network-outbound`封鎖流量，因為我們並未要求該許可權。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>修正具有權利的應用程式沙箱違規

既然我們已瞭解如何找出應用程式沙箱違規，讓我們來瞭解如何藉由調整應用程式的權利來解決它們。

請執行下列動作：

1. 在  **Solution Pad**中，按兩下**plist**檔案以開啟它進行編輯。
2. 在 [**權利**] 區段底下，勾選 [**允許外寄網路連線（用戶端）** ] 核取方塊：

    [![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)
3. 將變更儲存至應用程式。

如果我們針對範例應用程式執行上述步驟，然後建立並執行它，web 內容現在會如預期般顯示。

## <a name="the-app-sandbox-in-depth"></a>深入瞭解應用程式沙箱

應用程式沙箱所提供的存取控制機制並不容易瞭解。 不過，根據應用程式的需求，每個應用程式將採用應用程式沙箱的方式是唯一的。

基於保護 Xamarin 應用程式不受惡意程式碼入侵的最佳做法，應用程式（或它使用的其中一個程式庫或架構）中只需要一個弱點，就可以控制應用程式與的互動筆記本電腦.

應用程式沙箱的設計目的是要防止接管（或限制它可能造成的損害），方法是讓您指定應用程式與系統的預期互動。 系統只會將存取權授與您的應用程式完成工作所需的資源，而不需要其他任何作業。

針對應用程式沙箱進行設計時，您會針對最糟的情況進行設計。 如果應用程式遭到惡意程式碼入侵，就只能存取應用程式沙箱中的檔案和資源。

### <a name="entitlements-and-system-resource-access"></a>權利與系統資源存取

如先前所見，尚未進行沙箱化的 Xamarin. Mac 應用程式會被授與執行應用程式之使用者的完整許可權和存取權。 如果遭到惡意程式碼入侵，未受保護的應用程式就可以做為惡意行為的代理程式，而且可能會造成損害的範圍。

藉由啟用應用程式沙箱，您可以移除所有的最低許可權集合，然後使用您的 Xamarin. Mac 應用程式的權利，在僅限需要的基礎上重新啟用。

您可以編輯應用程式的**plist**檔案，並從 [編輯器] 下拉式方塊中檢查或選取所需的許可權，藉此修改應用程式的沙箱資源：

[![編輯權利](sandboxing-images/sign17.png "編輯權利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器目錄和檔案系統存取

當您的 Xamarin. Mac 應用程式採用應用程式沙箱時，它具有下列位置的存取權：

- **應用程式容器目錄**-第一次執行時，作業系統會建立一個特殊_容器目錄_，其中所有資源都只會存取。 應用程式將會擁有此目錄的完整讀取/寫入權限。
- **應用程式群組容器目錄**-您的應用程式可以被授與在相同群組中的應用程式之間共用的一或多個_群組容器_的存取權。
- **使用者指定**的檔案-應用程式會自動取得檔案的存取權，這些檔案是由使用者明確開啟或拖放至應用程式。
- **相關專案**-具有適當的權利，您的應用程式可以存取具有相同名稱但副檔名不同的檔案。 例如，已經儲存為`.txt`檔案和的`.pdf`檔。
- **臨時目錄、命令列工具目錄，以及特定全球可讀取的位置**-您的應用程式對其他定義位置中的檔案具有不同程度的存取權，如系統所指定。

#### <a name="the-app-container-directory"></a>應用程式容器目錄

Xamarin 應用程式的應用程式容器目錄具有下列特性：

- 它位於使用者主目錄中的隱藏位置（通常`~Library/Containers`是），而且可以在您的應用程式中`NSHomeDirectory`使用函式（請參閱下文）進行存取。 因為它是在主目錄中，所以每個使用者都會取得自己的應用程式容器。
- 應用程式對容器目錄及其所有子目錄和檔案，具有不受限制的讀取/寫入存取權。
- 大部分的 macOS 路徑尋找 Api 都是相對於應用程式的容器。 例如，容器會有自己的連結**庫**（透過存取`NSLibraryDirectory`）、**應用程式支援**和**喜好**設定子目錄。
- macOS 會透過程式碼簽署，建立和強制執行與應用程式及其容器之間的連線。 即使是另一個應用程式嘗試使用其配套**識別碼**來詐騙應用程式，它還是無法存取該容器，因為它會進行程式碼簽署。
- 容器不適用於使用者產生的檔案。 而是針對您的應用程式所使用的檔案，例如資料庫、快取或其他特定類型的資料。
- 針對_shoebox_類型的應用程式（例如 Apple 的相片應用程式），使用者的內容會移至容器中。

> [!IMPORTANT]
> 可惜的`NSHomeDirectory`是，xamarin 尚未有 100% API 涵蓋範圍（不同于 xamarin），因此 API 尚未在目前的 xamarin 版本中對應。

暫時的因應措施是，您可以使用下列程式碼：

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>應用程式群組容器目錄

從 Mac macOS 10.7.5 （及更新版本）開始，應用程式可以使用`com.apple.security.application-groups`權利來存取群組中所有應用程式通用的共用容器。 您可以將此共用容器用於非使用者面向的內容，例如資料庫或其他類型的支援檔案（例如快取）。

群組容器會自動新增至每個應用程式的沙箱容器（如果它們是群組的一部分），並儲存在`~/Library/Group Containers/<application-group-id>`。 群組識別碼的開頭_必須_是您的開發小組識別碼和句點，例如：

```plist
<team-id>.com.company.<group-name>
```

如需詳細資訊，請參閱 Apple[將應用程式新增至應用程式群組](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)的[權利機碼參考](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>應用程式容器外的 Powerbox 和檔案系統存取

沙箱化的 Xamarin 應用程式可以透過下列方式存取其容器以外的檔案系統位置：

- 以使用者的特定方向（透過 [開啟] 和 [儲存] 對話方塊或其他方法，例如拖放）。
- 使用特定檔案系統位置的權利（例如`/bin`或`/usr/lib`）。
- 當檔案系統位置位於可供全球讀取的特定目錄時（例如共用）。

_Powerbox_是 macOS 的安全性技術，會與使用者互動以擴充您的沙箱化 Xamarin. Mac 應用程式的檔案存取權限。 Powerbox 沒有 API，但會在應用程式呼叫`NSOpenPanel`或`NSSavePanel`時以透明方式啟動。 Powerbox 存取是透過您為 Xamarin. Mac 應用程式所設定的權利來啟用。

當沙箱應用程式顯示 [開啟] 或 [儲存] 對話方塊時，視窗會由 Powerbox （而非 AppKit）提供，因此可存取使用者可存取的任何檔案或目錄。

當使用者從 [開啟] 或 [儲存] 對話方塊（或拖曳至應用程式的圖示）選取檔案或目錄時，Powerbox 會將相關聯的路徑新增至應用程式的沙箱。

此外，系統會自動允許下列沙箱應用程式：

- 連接到系統輸入方法。
- 從 [**服務**] 功能表呼叫使用者所選取的服務（僅適用于服務提供者標示為 [_安全] 的_服務）。
- 從 [**開啟最近使用**] 功能表中，開啟使用者選擇的檔案。
- 在其他應用程式之間使用複製 & 貼上。
- 讀取下列全球可讀取位置的檔案：
  - `/bin`
  - `/sbin`
  - `/usr/bin`
  - `/usr/lib`
  - `/usr/sbin`
  - `/usr/share`
  - `/System`
- 在所建立`NSTemporaryDirectory`的目錄中讀取和寫入檔案。

根據預設，沙箱化 Xamarin 所開啟或儲存的檔案會保持可存取狀態，直到應用程式終止為止（除非應用程式結束時檔案仍為開啟）。 開啟的檔案會在下一次啟動應用程式時，透過 macOS Resume 功能自動還原至應用程式的沙箱。

若要為位於 Xamarin 應用程式容器外的檔案提供持續性，請使用安全性範圍的書簽（請參閱下文）。

#### <a name="related-items"></a>相關專案

應用程式沙箱可讓應用程式存取具有相同檔案名但副檔名不同的相關專案。 此功能有兩個部分： a）應用程式`Info.plst`檔案中的相關延伸模組清單 b）程式碼，用來告知沙箱應用程式將使用這些檔案執行的動作。

有兩種情況是合理的：

1. 應用程式必須能夠儲存不同版本的檔案（使用新的副檔名）。 例如，將檔案匯出`.txt` `.pdf`至檔案。 若要處理這種情況，您必須`NSFileCoordinator`使用來存取檔案。 您會先呼叫`WillMove(fromURL, toURL)`方法，將檔案移至新的延伸模組，然後再`ItemMoved(fromURL, toURL)`呼叫。
2. 應用程式必須開啟具有一個副檔名的主要檔案，以及數個具有不同副檔名的支援檔案。 例如，電影和副標題檔案。 `NSFilePresenter`使用來取得次要檔案的存取權。 將主要檔案提供給`PrimaryPresentedItemURL`屬性，並將次要檔案提供`PresentedItemURL`給屬性。 開啟主要檔案時，請呼叫`AddFilePresenter` `NSFileCoordinator`類別的方法來註冊次要檔案。

在這兩種情況下，應用程式的**plist**檔案都必須宣告應用程式可以開啟的檔案類型。 針對任何檔案類型，將`NSIsRelatedItemType` （值為`YES`）新增`CFBundleDocumentTypes`至陣列中的專案。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>使用沙箱應用程式開啟並儲存對話行為

從沙箱化的 Xamarin 應用程式`NSOpenPanel`呼叫`NSSavePanel`時，會將下列限制放在和上：

- 您無法以程式設計方式叫用 [**確定]** 按鈕。
- 您無法以程式設計方式更改中`NSOpenSavePanelDelegate`的使用者選取專案。

此外，下列繼承修改已準備就緒：

- **非沙箱應用程式** -  `NSOpenPanel``NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全性範圍的書簽和持續性資源存取

如上所述，沙箱化 Xamarin. Mac 應用程式可以藉由直接使用者互動（如 PowerBox 所提供），存取其容器外的檔案或資源。 不過，這些資源的存取不會在應用程式啟動或系統重新開機時自動儲存。

藉由使用_安全性範圍的書簽_，沙箱化 Xamarin Mac 應用程式可以保留使用者意圖，並在應用程式重新開機後維持對指定資源的存取權。

#### <a name="security-scoped-bookmark-types"></a>安全性範圍的書簽類型

使用安全性範圍的書簽和持續性資源存取時，有兩個 sistine 使用案例：

- **應用程式範圍的書簽可提供使用者指定檔案或資料夾的持續存取權。**

    例如，如果沙箱化的 Xamarin 應用程式允許使用開啟外部檔進行編輯（使用`NSOpenPanel`），應用程式可以建立以應用程式為範圍的書簽，讓它可以在未來再次存取相同的檔案。
- **檔範圍的書簽會提供對子檔案的持續存取權的特定檔。**

例如，影片編輯應用程式會建立可存取個別影像、影片剪輯和音效檔的專案檔，稍後將會合並成單一電影。

當使用者將資源檔匯入至專案時（ `NSOpenPanel`透過），應用程式會針對儲存在專案中的專案建立以檔為範圍的書簽，讓應用程式一律可存取該檔案。

可以開啟書簽資料和檔本身的任何應用程式，都可以解析檔範圍的書簽。 這可支援可攜性，可讓使用者將專案檔案傳送給另一位使用者，並同時讓所有書簽都適用。

> [!IMPORTANT]
> 檔範圍的書簽_只能_指向單一檔案而不是資料夾，而且該檔案不能位於系統所使用的位置（例如`/private`或`/Library`）。

#### <a name="using-security-scoped-bookmarks"></a>使用安全性範圍的書簽

使用任何一種安全性範圍的書簽，都需要您執行下列步驟：

1. **在需要使用安全性範圍書簽的 Xamarin. Mac 應用程式中設定適當權利**-針對應用程式範圍的書簽，將`com.apple.security.files.bookmarks.app-scope`權利金鑰設定為。 `true` 若為檔範圍的書簽，請`com.apple.security.files.bookmarks.document-scope`將權利金鑰`true`設定為。
2. **建立安全性範圍的書簽**-您會針對使用者已提供存取權的任何檔案或資料夾執行此動作（ `NSOpenPanel`例如），應用程式將需要持續存取。 使用類別`NSUrl`的方法`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`來建立書簽。
3. **解析安全性範圍的書簽**-當應用程式需要再次存取資源時（例如重新開機後），必須將書簽解析為安全性範圍的 URL。 使用類別`NSUrl`的方法`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`來解析書簽。
4. **明確通知系統您想要從安全性範圍的 url 存取**檔案-此步驟必須在取得上述安全性範圍的 url 之後立即完成，或者，當您稍後想要重新存取資源之後，釋放您對它的存取權。 呼叫類別`NSUrl`的方法，以開始存取安全性`StartAccessingSecurityScopedResource ()`範圍的 URL。
5. **明確通知系統您已從安全性範圍的 URL 完成存取**檔案-儘快，您應該在應用程式不再需要存取檔案時通知系統（例如，使用者關閉它時）。 呼叫類別`NSUrl`的方法，以停止存取安全性`StopAccessingSecurityScopedResource ()`範圍的 URL。

在您放棄資源的存取權之後，您必須再次返回步驟4來重新建立存取權。 如果重新開機 Xamarin 應用程式，您必須返回步驟3並重新解析書簽。

> [!IMPORTANT]
> 無法發行對安全性範圍 URL 資源的存取權，會導致 Xamarin. Mac 應用程式流失核心資源。 因此，應用程式將無法再將檔案系統位置新增至其容器，直到重新開機為止。

### <a name="the-app-sandbox-and-code-signing"></a>應用程式沙箱和程式碼簽署

啟用應用程式沙箱並啟用 Xamarin 應用程式的特定需求之後（透過權利），您必須將專案編碼，才能讓沙箱生效。 因為應用程式沙箱所需的權利已連結至應用程式的簽章，所以您必須執行程式碼簽署。

macOS 會強制執行應用程式的容器與其程式碼簽章之間的連結，如此一來，即使它正在詐騙應用程式套件組合識別碼，其他應用程式也不能存取該容器。 此機制的運作方式如下：

1. 當系統建立應用程式的容器時，它會在該容器上設定_存取控制清單_（ACL）。 清單中的初始存取控制專案包含應用程式指定的_需求_（DR），其描述如何辨識應用程式的未來版本（已升級時）。
2. 每次啟動具有相同套件組合識別碼的應用程式時，系統會檢查應用程式的程式碼簽章是否符合容器 ACL 的其中一個專案中指定的指定需求。 如果系統找不到相符的結果，它會防止應用程式啟動。

程式碼簽署的運作方式如下：

1. 建立 Xamarin 專案之前，請先從 Apple 開發人員入口網站取得開發憑證、散發憑證和開發人員識別碼憑證。
2. 當 Mac App Store 散發 Xamarin. Mac 應用程式時，它會使用 Apple 代碼簽名進行簽署。

測試和偵測時，您會使用已簽署的 Xamarin. Mac 應用程式版本（將用來建立應用程式容器）。 之後，如果您想要從 Apple App Store 測試或安裝版本，它會使用 Apple 簽章進行簽署，而且將無法啟動（因為它的程式碼簽章不會與原始應用程式容器相同）。 在此情況下，您會收到與下列類似的當機報告：

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要修正此問題，您必須調整 ACL 專案，以指向應用程式的 Apple 已簽署版本。

如需建立和下載沙箱所需之布建設定檔的詳細資訊，請參閱上方的[簽署和布建應用程式](#Signing_and_Provisioning_the_App)一節。

#### <a name="adjusting-the-acl-entry"></a>調整 ACL 專案

若要允許執行 Xamarin 應用程式的 Apple 已簽署版本，請執行下列動作：

1. 開啟終端機應用程式（ `/Applications/Utilities`在中）。
2. 開啟 [搜尋工具] 視窗至 Apple 已簽署的 Xamarin. Mac 應用程式版本。
3. 在`asctl container acl add -file`終端機視窗中輸入。
4. 從 [搜尋工具] 視窗拖曳 [Xamarin] 應用程式的圖示，並將它放在終端機視窗上。
5. 檔案的完整路徑將會新增至終端機中的命令。
6. 按**enter**鍵以執行命令。

容器的 ACL 現在包含兩種 Xamarin. Mac 應用程式的指定程式碼需求，而 macOS 現在會允許執行任一版本。

#### <a name="display-a-list-of-acl-code-requirements"></a>顯示 ACL 程式碼需求清單

您可以執行下列動作，以在容器的 ACL 中查看程式碼需求的清單：

1. 開啟終端機應用程式（ `/Applications/Utilities`在中）。
2. 輸入 `asctl container acl list -bundle <container-name>`。
3. 按**enter**鍵以執行命令。

通常`<container-name>`是 Xamarin 應用程式的套件組合識別碼。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>設計適用于應用程式沙箱的 Xamarin. Mac 應用程式

在設計應用程式沙箱的 Xamarin. Mac 應用程式時，應該遵循的一般工作流程。 話雖如此，在應用程式中實行沙箱的細節將對指定的應用程式功能而言是唯一的。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>採用應用程式沙箱的六個步驟

設計適用于應用程式沙箱的 Xamarin. Mac 應用程式通常包含下列步驟：

1. 判斷應用程式是否適用于沙箱。
2. 設計開發和散發策略。
3. 解決任何 API 不相容的問題。
4. 將必要的應用程式沙箱權利套用到 Xamarin. Mac 專案。
5. 使用 XPC 新增許可權隔離。
6. 實行遷移策略。

> [!IMPORTANT]
> 您不能只將應用程式套件組合中的主要可執行檔，以及該組合中包含的每個 helper 應用程式或工具沙箱。 這對於從 Mac App Store 發佈的任何應用程式而言都是必要的，而且如果可能的話，應該針對任何其他形式的應用程式散發來完成。

如需 Xamarin 應用程式套件組合中所有可執行檔的清單，請在 [終端機] 中輸入下列命令：

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`是應用程式組合的名稱和路徑。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>判斷 Xamarin for Mac 應用程式是否適用于沙箱

大部分的 Xamarin. Mac 應用程式都與應用程式沙箱完全相容，因此適用于沙箱。 如果應用程式需要應用程式沙箱不允許的行為，您應該考慮使用替代方法。

如果您的應用程式需要下列其中一種行為，則它與應用程式沙箱不相容：

- **授權服務**-透過應用程式沙箱，您無法使用[授權服務 C 參考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)中所述的函式。
- **協助工具 api** -您不能沙箱處理輔助應用程式，例如可控制其他應用程式的螢幕讀取器或應用程式。
- **將 Apple 事件傳送至任意應用程式**-如果應用程式需要將 Apple 事件傳送至未知的任意應用程式，則無法將它設為沙箱。 針對已知的應用程式清單，應用程式仍然可以進行沙箱處理，而且權利必須包含所呼叫的應用程式清單。
- **將分散式通知中的使用者資訊字典傳送至其他**工作-使用應用程式沙箱，張貼至`userInfo` `NSDistributedNotificationCenter`物件以進行其他工作的訊息時，您不能包含字典。
- **載入核心延伸**模組-應用程式沙箱禁止載入核心延伸模組。
- **在 [開啟] 和 [儲存] 對話方塊中模擬使用者輸入**-應用程式沙箱禁止使用 [開啟] 或 [儲存] 對話方塊來模擬或改變使用者輸入。
- **存取或設定其他應用程式的喜好**設定-應用程式沙箱禁止操作其他應用程式的設定。
- 進行**網路設定**-應用程式沙箱禁止操作網路設定。
- **終止其他應用**程式-應用程式沙箱禁止`NSRunningApplication`使用來終止其他應用程式。

### <a name="resolving-api-incompatibilities"></a>解決 API 不相容

在設計適用于應用程式沙箱的 Xamarin. Mac 應用程式時，您可能會遇到某些 macOS Api 使用方式不相容的情況。

以下是一些您可以用來解決問題的常見問題和事項：

- **開啟、儲存和追蹤檔**-如果您要使用以外`NSDocument`的任何技術來管理檔，您應該切換到它，因為應用程式沙箱的內建支援。 `NSDocument`會自動使用 PowerBox，並提供在使用者于搜尋工具中移動檔的支援。
- **保留對檔案系統資源的存取**-如果 Xamarin 應用程式相依于其容器外的資源持續存取，請使用安全性範圍的書簽來維持存取權。
- **建立應用程式的登入專案**-使用應用程式沙箱，您無法使用`LSSharedFileList`建立登入專案，也無法使用`LSRegisterURL`來操作啟動服務的狀態。 如`SMLoginItemSetEnabled` [使用服務管理架構](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)檔中的蘋果加入登入專案所述，使用函數。
- **存取使用者資料**-如果您使用 POSIX 函式（例如`getpwuid` ）從目錄服務取得使用者的主目錄，請考慮使用 Cocoa 或核心的符號，例如`NSHomeDirectory`。
- **存取其他應用程式的喜好**設定-因為應用程式沙箱會將路徑尋找 api 導向應用程式的容器，所以修改喜好設定會在該容器內進行，並在不允許的情況下存取其他應用程式喜好設定。
- **在 Web Views 中使用 HTML5 Embedded 影片**-如果 Xamarin 應用程式使用 WebKit 來播放內嵌的 HTML5 影片，您也必須將應用程式與 AV 基礎架構連結。 應用程式沙箱會防止 CoreMedia 播放這些影片。

### <a name="applying-required-app-sandbox-entitlements"></a>套用必要的應用程式沙箱權利

您必須編輯您想要在應用程式沙箱中執行之任何 Xamarin. Mac 應用程式的權利，然後勾選 [**啟用應用程式沙箱**] 核取方塊。

根據應用程式的功能，您可能需要啟用其他權利以存取 OS 功能或資源。 當您將要求的權利降至最低以執行應用程式時，應用程式沙箱的效果最佳，因此只需隨機啟用權利即可。

若要判斷 Xamarin 應用程式所需的權利，請執行下列動作：

1. 啟用應用程式沙箱並執行 Xamarin. Mac 應用程式。
2. 執行應用程式的功能。
3. 開啟主控台應用程式（可在`/Applications/Utilities`中取得`sandboxd` ），並在 [**所有訊息**] 記錄中尋找違規。
4. 針對每`sandboxd`個違規，請使用應用程式容器（而不是其他檔案系統位置）來解決問題，或套用應用程式沙箱權利以啟用受限制的 OS 功能存取。
5. 重新執行並再次測試所有的 Xamarin. Mac 應用程式功能。
6. 重複此步驟`sandboxd` ，直到所有違規皆已解決為止。

### <a name="add-privilege-separation-using-xpc"></a>使用 XPC 新增許可權分隔

開發適用于應用程式沙箱的 Xamarin. Mac 應用程式時，請查看應用程式在許可權和存取方面的行為，然後考慮將高風險作業分成自己的 XPC 服務。

如需詳細資訊，請參閱 Apple 的[建立 XPC 服務](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)和[守護程式和服務程式設計指南](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>實行遷移策略

如果您發行的是新的、沙箱化版本的 Xamarin. Mac 應用程式，但先前尚未進行沙箱處理，您必須確保目前的使用者具有順暢的升級路徑。

 如需如何執行容器遷移資訊清單的詳細資訊，請參閱 Apple 將[應用程式遷移至沙箱](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)檔。

## <a name="summary"></a>總結

本文已詳細探討如何將 Xamarin. Mac 應用程式進行沙箱處理。 首先，我們建立了單純的 Xamarin Mac 應用程式，以顯示應用程式沙箱的基本概念。 接下來，我們示範了如何解決沙箱違規。 然後，我們會深入探討應用程式沙箱，最後，我們探討了如何設計應用程式沙箱的 Xamarin. Mac 應用程式。

## <a name="related-links"></a>相關連結

- [發行至 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [關於應用程式沙箱](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [常見的應用程式沙箱問題](https://developer.apple.com/library/content/qa/qa1773/_index.html)
