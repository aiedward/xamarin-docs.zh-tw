---
title: 使用 Jenkins 搭配 Xamarin
description: 本文件說明如何使用 Jenkins 連續整合與 Xamarin 應用程式。 它討論如何安裝、 設定及使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 8094f1ab78252e6d6bd8f5991bcb567b36ed1e9b
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935340"
---
# <a name="using-jenkins-with-xamarin"></a>使用 Jenkins 搭配 Xamarin

_本指南說明如何設定為持續整合伺服器 Jenkins 和自動化編譯建立使用 Xamarin 的行動應用程式。說明如何安裝 Jenkins OS X 上、 設定它，並設定作業，若要變更認可至原始程式碼管理系統時，編譯 Xamarin.iOS 和 Xamarin.Android 應用程式。_

[使用 Xamarin 的連續整合簡介](~/tools/ci/intro-to-ci.md)介紹持續整合提供早期警告中斷或不相容的程式碼的實用的軟體開發作法。 CI 在發生，並保留處於適當狀態的部署的軟體，可讓開發人員位址所發生的問題。 這個逐步解說將說明如何同時使用兩份文件中的內容。

本指南示範如何在執行 OS X 的專用電腦上安裝 Jenkins，並將它設定為電腦啟動時自動執行。 Jenkins 安裝之後，我們將會安裝支援 MS Build 的其他外掛程式。 Jenkins 現成提供的支援 Git。 如果使用 TFS 原始程式碼控制，也必須安裝其他的外掛程式和命令列公用程式。

一旦設定 Jenkins，而且已安裝任何必要的外掛程式，我們將建立一或多個工作，以編譯 Xamarin.Android 和 Xamarin.iOS 專案。 作業是步驟及執行一些工作所需的中繼資料的集合。 工作通常包含下列：

-  **來源的程式碼管理 (SCM)** – 這是中繼資料中的項目 Jenkins 組態檔，其中包含有關如何連接到原始程式碼控制和功能來擷取檔案。
-  **觸發程序**– 觸發程序用來啟動工作，根據特定動作，例如當開發人員變更認可至原始程式碼儲存機制。
-  **建置指令**– 這是外掛程式或指令碼來編譯原始程式碼，並產生可安裝在行動裝置的二進位檔。
-  **選擇性的建置動作**– 這可能包括執行單元測試，程式碼，簽署程式碼中，針對執行靜態分析或啟動另一個執行其他工作建立相關的工作。
-  **通知**– 作業可能會送出某種組建的狀態相關通知。
-  **安全性**– 雖然是選擇性的強烈建議也啟用 Jenkins 安全性功能。


本指南將逐步解說如何設定 Jenkins 伺服器涵蓋每個點。 結束，我們應該深入了解如何安裝和設定以建立我們 Xamarin 的行動裝置專案的 IPA 和 APK 的 Jenkins。

## <a name="requirements"></a>需求

理想的組建伺服器是專門用來建置和可能測試應用程式的唯一用途的獨立電腦。 在專用的電腦可確保，可能還需要其他角色 （例如，網頁伺服器） 的成品不會 contaminate 組建。 比方說，如果組建伺服器也做為 web 伺服器時，網頁伺服器可能需要一些通用程式庫的版本衝突。 由於這項衝突的 web 伺服器可能無法正常運作或 Jenkins 可能會建立組建部署至使用者時無法運作。

Xamarin 的行動裝置應用程式的組建伺服器已設定非常類似的開發人員工作站。 它的使用者帳戶中的 Jenkins，Visual Studio for Mac，而且將會安裝 Xamarin.iOS 和 Xamarin.Android。 所有的程式碼簽署憑證，佈建的設定檔，以及金鑰存放區必須一併安裝。 *通常組建伺服器的使用者帳戶不同於開發人員帳戶-請務必安裝和設定所有軟體、 金鑰和憑證時使用的組建伺服器使用者帳戶登入。*

下圖說明所有這些項目在一般的 Jenkins 組建伺服器上：

 [![](jenkins-walkthrough-images/image1.png "此圖說明所有這些一般的 Jenkins 組建伺服器上的項目")](jenkins-walkthrough-images/image1.png#lightbox)

可以只建置及執行 Mac OS X 電腦上簽署 iOS 應用程式。Mac Mini 是合理的低成本選項，但能夠執行 OS X 10.10 (Yosemite) 的任何電腦或更新版本的足夠。

如果使用 TFS 原始程式碼控制，您會想要安裝[Team Explorer Everywhere](http://www.microsoft.com/en-ca/download/details.aspx?id=40785)、 可以從 Microsoft 下載。 Team Explorer Everywhere 可跨平台存取 TFS 在 OS X 中的終端機。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安裝 Jenkins

使用 Jenkins 第一項工作是將其安裝。 有三種方式執行 OS x: Jenkins

-  為協助程式，在背景執行。
-  例如 Tomcat、 Jetty 或 JBoss servlet 容器。
-  為使用者帳戶下執行的一般程序。


大部分傳統的連續整合應用程式在背景中執行，以作為服務精靈 (在 OS X 上或\*nix) 或 （在 Windows 上） 的服務。 不不需要任何 GUI 互動，而且可以輕鬆地執行建置環境的設定，這是適用於情況。 行動裝置應用程式也需要 keystores 和簽署 Jenkins 以協助程式執行時可能會有問題，若要存取的憑證。 基於這些考量，本文件著重於第三個案例 – 使用者帳戶下執行 Jenkins 組建伺服器上。

Jenkins.App 是安裝 Jenkins 便利方式。 這是 AppleScript 包裝函式，可簡化的開始和停止的 Jenkins 伺服器。 而不是在 bash 殼層中執行，Jenkins 會應用程式的圖示中停駐，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image2.png "而不是在 bash 殼層中執行，Jenkins 會應用程式的圖示中停駐、 這個螢幕擷取畫面所示")](jenkins-walkthrough-images/image2.png#lightbox)

啟動或停止 Jenkins 是做為開始或停止 Jenkins.App 一樣簡單。

若要安裝 Jenkins.App，請從專案的下載頁面上，在下面的螢幕擷取畫面如圖所示下載最新版本：

 [![](jenkins-walkthrough-images/image3.png "應用程式，下載最新版本的專案中下載頁面上，如圖所示，在此螢幕擷取畫面")](jenkins-walkthrough-images/image3.png#lightbox)

將 zip 檔案解壓縮`/Applications`組建伺服器和啟動它就像任何其他 OS X 應用程式上的資料夾。
第一次您啟動 Jenkins.App，它將會顯示對話方塊，告知您它會下載 Jenkins:

 [![](jenkins-walkthrough-images/image4.png "應用程式時，它將會顯示對話方塊，告知您它會下載 Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

一旦 Jenkins.App 完成其下載，它會顯示另一個對話方塊，詢問您，是否您想要自訂 Jenkins 啟動時，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image5.png "應用程式已經完成其下載，它就會顯示另一個對話方塊，詢問您，是否您想要自訂 Jenkins 啟動時，此螢幕擷取畫面所示")](jenkins-walkthrough-images/image5.png#lightbox)

自訂 Jenkins 是選擇性的而且不需要每次啟動應用程式 – 時的預設設定的 Jenkins 適用於大部分的情況下執行。

如果有必要自訂 Jenkins，按一下 [上**變更預設值**] 按鈕。 這會顯示兩個連續的對話方塊： 一個要求 Java 命令列參數，以及另一個要求 Jenkins 命令列參數。 下列兩個螢幕擷取畫面顯示這些兩個對話方塊：

 [![](jenkins-walkthrough-images/image6.png "這個螢幕擷取畫面顯示對話方塊")](jenkins-walkthrough-images/image6.png#lightbox)

 [![](jenkins-walkthrough-images/image7.png "這個螢幕擷取畫面顯示對話方塊")](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 開始執行之後，您可能想要將它設定為登入項目，讓它將會啟動每次使用者登入電腦。 您可以停駐的 Jenkins 圖示上按一下滑鼠右鍵，然後選擇**選項...在登入時開啟**，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image8.png "您可以停駐的 Jenkins 圖示上按一下滑鼠右鍵，然後選擇 在登入時，OptionsOpen 這個螢幕擷取畫面所示")](jenkins-walkthrough-images/image8.png#lightbox)

這會導致 Jenkins.App 以自動啟動每次使用者登入，但不是當電腦開機。 您可指定 OS X 在開機時，將使用自動使用的登入的使用者帳戶。 開啟**系統偏好設定**，然後選取**使用者和群組**圖示這個螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image9.png "開啟 系統喜好設定，並選取使用者群組圖示，此螢幕擷取畫面所示")](jenkins-walkthrough-images/image9.png#lightbox)

按一下**登入選項**按鈕，然後再選擇 OS X 在開機時，會使用登入的帳戶。

此時 Jenkins 已安裝。 不過，如果我們想要建置 Xamarin 的行動應用程式，我們將需要安裝某些外掛程式。


### <a name="installing-plugins"></a>安裝外掛程式

當 Jenkins.App 安裝程式完成時，它將會啟動 Jenkins 並啟動 web 瀏覽器 url http://localhost:8080，如以下螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image10.png "8080，如下列螢幕擷取畫面所示")](jenkins-walkthrough-images/image10.png#lightbox)

從這個頁面上，選取**Jenkins > 管理 Jenkins > 管理外掛程式**功能表內的左上角，如以下螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image11.png "從這個頁面上，從左上角的功能表選取 Jenkins 管理 Jenkins 管理外掛程式")](jenkins-walkthrough-images/image11.png#lightbox)

這會顯示**Jenkins 外掛程式管理員**頁面。 如果您按一下 [可用] 索引標籤上，您會看到一份 600 的外掛程式，可以下載及安裝。 這是中以下螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image12.png "如果您按一下 [可用] 索引標籤上，您會看到一份 600 的外掛程式，可以下載並安裝")](jenkins-walkthrough-images/image12.png#lightbox)

瀏覽所有 600 外掛程式，以尋找可以是十分繁瑣，容易出錯。 Jenkins 提供介面的右上角的篩選搜尋欄位。 使用此篩選器欄位搜尋會簡化尋找並已安裝的一個或所有下列外掛程式：

-  **Jenkins MSBuild 外掛程式**– 此外掛程式會讓您能夠建置 Visual Studio 和 Visual Studio for Mac 方案 (.sln) 和專案 (.csproj)。
-  **環境 Injector 外掛程式**– 這是選擇性的但實用的外掛程式，可讓工作在設定環境變數，並建置層級。 它也提供變數的額外保護，例如程式碼使用的密碼登入應用程式。 有時縮寫為*EnvInject 外掛程式*。
-  **Team Foundation Server 外掛程式**– 這是選擇性的外掛程式，只需如果您使用 Team Foundation Server 或 Team Foundation Services 原始程式碼控制的。

Jenkins 任何額外的外掛程式不支援 Git。

之後安裝所有的外掛程式，您會想要重新啟動 Jenkins 和設定每個外掛程式的全域設定。 外掛程式的通用設定可以找到選取**Jenkins > 管理 Jenkins > 設定系統**從左上角，如以下螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image13.png "外掛程式的通用設定可以找到選取 Jenkins / 管理 Jenkins / 從右上方的 設定系統將傳遞的角")](jenkins-walkthrough-images/image13.png#lightbox)

當您選取此功能表選項時，會將您帶往**設定系統 [Jenkins]** 頁面。 此頁面包含區段設定 Jenkins 本身，並設定一些通用的外掛程式值。  下列螢幕擷取畫面說明了此頁面的範例：

 [![](jenkins-walkthrough-images/image14.png "這個螢幕擷取畫面說明了此頁面的範例")](jenkins-walkthrough-images/image14.png#lightbox)


#### <a name="configuring-the-msbuild-plugin"></a>設定 MSBuild 外掛程式

MSBuild 外掛程式必須設定為使用 **/Library/Frameworks/Mono.framework/Commands/xbuild**編譯 Visual Studio for Mac 方案和專案檔。 向下捲動**設定系統 [Jenkins]** 頁面上，直到**將加入 MSBuild**按鈕隨即出現，如下面的螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image15.png "設定系統 Jenkins 頁面上向下捲動，直到將加入 MSBuild 按鈕隨即顯示")](jenkins-walkthrough-images/image15.png#lightbox)

按一下這個按鈕，然後填寫**名稱**和**路徑**至**MSBuild**上出現的表單欄位。 名稱您**MSBuild**安裝應該仍有意義的同時**MSBuild 的路徑**必須為路徑`xbuild`，通常是 **/Library/架構 /Mono.framework/Commands/xbuild**。 我們按一下 儲存 或 在頁面底部的 套用 按鈕儲存變更之後，會無法使用 Jenkins`xbuild`編譯您的方案。

#### <a name="configuring-the-tfs-plugin"></a>設定 TFS 外掛程式

此區段是強制性，如果您想要使用 TFS 原始程式碼控制的。

為了讓與 TFS 伺服器互動的 OS X 工作站，Team Explorer Everywhere 必須安裝在工作站上。 Team Explorer Everywhere 是一組包含用於存取 TFS 的跨平台命令列用戶端的 microsoft 工具。 Team Explorer Everywhere 可以從 Microsoft 下載並安裝在三個步驟：

1. 解壓縮封存檔案的使用者帳戶可存取的目錄。 例如，您可能會在其中解壓縮檔案到 **~/tee**。
2. 設定殼層或系統路徑以包括保留已在上面步驟中解壓縮檔案的資料夾。 例如，套用至物件的

        echo export PATH~/tee/:$PATH' >> ~/.bash_profile

3. 若要確認已安裝 Team Explorer Everywhere，請開啟終端機工作階段，並執行`tf`命令。 Tf 已正確設定，您會看到您的終端機工作階段中的下列輸出：

        $ tf
        Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

        Available commands and their options:

TFS 的命令列用戶端安裝之後，必須使用的完整路徑來設定 Jenkins`tf`命令列用戶端。 向下捲動**設定系統 [Jenkins]** 頁面上，直到您找到 Team Foundation Server 區段中，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image17.png "設定系統 Jenkins 頁面上向下捲動，直到您找出 Team Foundation Server 區段")](jenkins-walkthrough-images/image17.png#lightbox)

輸入的完整路徑`tf`命令，然後按一下 [**儲存**] 按鈕。

### <a name="configure-jenkins-security"></a>設定 Jenkins 安全性

最初安裝時，Jenkins 會有安全性是停用，因此任何使用者設定和以匿名方式執行任何種類的工作可能會。 本節說明如何設定安全性設定驗證和授權使用 Jenkins 使用者資料庫。

安全性設定可以找到選取**Jenkins > 管理 Jenkins > 設定全域安全性**這個螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image18.png "安全性設定可以找到選取 Jenkins / 管理 Jenkins / 設定全域安全性群組")](jenkins-walkthrough-images/image18.png#lightbox)

在**設定全域安全性**頁面上，核取**啟用安全性**核取方塊和**存取控制**應該會顯示表單，類似於下一個螢幕擷取畫面：

 [![](jenkins-walkthrough-images/image19.png "在設定的全域安全性群組] 頁面上，勾選 [啟用安全性核取方塊和存取控制表單應該會出現，類似這個螢幕擷取畫面")](jenkins-walkthrough-images/image19.png#lightbox)

切換的選項按鈕**Jenkins 自己的使用者資料庫**中**安全性領域 > 一節**，並確定**允許使用者註冊**也勾選，如下所示下列螢幕擷取畫面：

 [![](jenkins-walkthrough-images/image20.png "切換 Jenkins 自己的使用者資料庫安全性領域 > 一節中的選項按鈕，並確定也選取 允許使用者註冊")](jenkins-walkthrough-images/image20.png#lightbox)

最後，重新啟動 Jenkins，並建立新的帳戶。 第一個建立的帳戶是根帳戶，而此帳戶會自動提升為系統管理員。 瀏覽回**設定全域安全性**頁面，然後取消核取**矩陣為基礎的安全性**選項按鈕。 根帳號應該授與完整存取權，以及匿名帳戶應該具備唯讀存取權，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image21.png "根帳號應該授與完整存取權，以及匿名帳戶應該具備唯讀存取")](jenkins-walkthrough-images/image21.png#lightbox)

一旦儲存這些設定，並重新啟動 Jenkins，安全性會開啟。


#### <a name="disabling-security"></a>停用安全性

萬一忘記的密碼或 Jenkins 全鎖定中，便可停用安全性，依照下列步驟：

1. 停止 Jenkins。 如果您使用 Jenkins.app，您可以停駐、 Jenkins.App 圖示上按一下滑鼠右鍵，然後從快顯功能表選取結束：

    ![](jenkins-walkthrough-images/image19.png "應用程式中停駐，並從快顯功能表中選取 [結束] 圖示")
2. 開啟檔案 **~/.jenkins/config.xml**文字編輯器中。
3. 值變更`<usesecurity></usesecurity>`項目從`true`至`false`。
4. 刪除`<authorizationstrategy></authorizationstrategy>`和`<securityrealm></securityrealm>`檔案中的項目。
5. 重新啟動 Jenkins。

## <a name="setting-up-a-job"></a>設定工作

在最上層 Jenkins 將所有的組織建置軟體所需的各種工作*作業*。 某項作業也會有與其相關聯，提供有關如何取得原始碼、 組建的執行頻率、 任何特殊變數所需的建置，以及如何通知開發人員，如果建置失敗之類的組建資訊中繼資料。

會建立工作，藉由選取**Jenkins > 新的工作**功能表內的右上角，如下列螢幕擷取畫面所示：


![](jenkins-walkthrough-images/image22.png "作業會建立右上角的功能表選取 Jenkins 新工作")

這會顯示**新工作 [Jenkins]** 頁面。 輸入工作的名稱，並選取**建置釋放樣式軟體專案**選項按鈕。 下列螢幕擷取畫面顯示此動作的範例：

![](jenkins-walkthrough-images/image23.png "輸入工作的名稱，並選取組建可用樣式軟體專案的選項按鈕")

按一下**確定**按鈕會呈現為工作的 [組態] 頁面。 這看起來應該像下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image24.png "這應該類似這個螢幕擷取畫面")

Jenkins 組織位於下列路徑的硬碟上的目錄中的工作： **~/.jenkins/jobs/[JOB 名稱]**

此資料夾包含的所有檔案和成品特有的工作，例如記錄檔、 組態檔，以及需要編譯的原始程式碼。

一旦建立初始工作之後，它必須設定一或多個項目：

 - 必須指定來源的程式碼管理系統。
 - 一或多個*建置動作*必須加入至專案。 這些是步驟或建置應用程式所需的工作。
 - 工作必須指派一個*組建觸發程序*– 一組指示通知頻率 Jenkins 擷取程式碼，並建置最終的專案。

### <a name="configuring-source-code-control"></a>設定原始程式碼控制

Jenkins 未在第一個工作會從原始程式碼管理系統擷取的原始程式碼。 Jenkins 支援許多熱門的原始碼程式碼管理系統目前可用。 本章節涵蓋兩個常用系統 Git 和 Team Foundation Server。 每個來源的程式碼管理系統將在以下各節將詳細討論。

#### <a name="using-git-for-source-code-control"></a>使用 Git 進行原始程式碼控制

如果您使用 TFS 原始程式碼控制的[略過](#Using_TFS_for_Source_Code_Management)這個區段，並繼續進行下一節中使用 TFS。

Jenkins 支援 Git 現成 – 沒有的額外外掛程式所需。 若要使用 Git，請按一下**Git**選項按鈕，然後輸入的 URL 將 Git 儲存機制，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image25.png "若要使用 Git，按一下 [Git] 選項按鈕上，輸入 Git 儲存機制的 URL")

一旦儲存變更，Git 設定已完成。

#### <a name="using-tfs-for-source-code-management"></a>使用原始程式碼管理 TFS

本節僅適用於 TFS 使用者。

按一下**Team Foundation Server**選項按鈕和 TFS 的組態區段應該會出現，類似於下列螢幕擷取畫面中：


![](jenkins-walkthrough-images/image26.png "按一下 [Team Foundation Server] 選項按鈕，然後 TFS 組態區段應該會出現")

提供適用於 TFS 的必要資訊。 下列螢幕擷取畫面顯示完成的表單的範例：

![](jenkins-walkthrough-images/image27.png "這個螢幕擷取畫面顯示完成的表單的範例")

#### <a name="testing-the-source-code-control-configuration"></a>測試來源的程式碼控制組態

一旦已設定適當的原始程式碼控制，按一下**儲存**儲存的變更。 這會傳回您要進行作業，將類似下列的螢幕擷取畫面的首頁：

![](jenkins-walkthrough-images/image28.png "這會傳回您要進行作業，將類似這個螢幕擷取畫面的 [首頁] 頁面")

驗證是否已正確設定原始程式碼控制的最簡單方式是以觸發組建，以手動方式，即使沒有指定的建置動作。 若要手動啟動建置，首頁上的作業已**現在建置**連結在左邊，功能表中，如以下螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image29.png "若要手動啟動建置，首頁上的作業會有現在建置連結左側功能表中")

啟動組建之後，建立歷程記錄 對話方塊會顯示閃爍藍色圓形、 進度列、 組建編號及組建開始時間，類似於下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image30.png "啟動組建之後，建立歷程記錄 對話方塊會顯示閃爍藍色圓形、 進度列、 組建編號及組建開始的時間")

如果作業成功時，將會顯示藍色圓形。 如果作業失敗時，將會顯示一個紅色圓圈。

為了協助疑難排解問題可能會產生組建的一部分，Jenkins 會擷取所有工作的主控台輸出。 若要查看主控台輸出，請按一下中的工作**建置記錄**，然後在**主控台輸出**左功能表中的連結。 下列螢幕擷取畫面顯示**主控台輸出**連結，以及某些成功的作業的輸出：

![](jenkins-walkthrough-images/image31.png "這個螢幕擷取畫面顯示主控台輸出連結，以及某些成功的作業的輸出")

#### <a name="location-of-build-artifacts"></a>組建成品的位置

Jenkins 會擷取到呼叫的特殊資料夾的整個原始程式碼*工作區*。 可以在下列位置的資料夾內找到這個目錄：

    ~/.jenkins/jobs/[JOB NAME]/workspace

工作區的路徑會儲存在名為環境變數`$WORKSPACE`。

您可導覽到作業的登陸頁面，然後按一下 瀏覽工作區中的資料夾 Jenkins**工作區**左功能表中的連結。 下列螢幕擷取畫面顯示名為的作業的工作區範例**HelloWorld**:

![](jenkins-walkthrough-images/image32.png "這個螢幕擷取畫面顯示名為 HelloWorld 作業 工作區的範例")

### <a name="build-triggers"></a>建立觸發程序

有幾個不同的策略來起始在 Jenkins 組建 – 這些值稱為*建立觸發程序*。 組建觸發程序可協助 Jenkins 決定何時啟動作業，並建置專案。 較常見的組建觸發程序的兩個是：

- **定期建立**– 此觸發程序會導致啟動的工作，以指定的間隔，例如每隔兩小時，或在上班日的午夜 Jenkins。 組建將會啟動而不論是否有任何變更過原始程式碼儲存機制。
- **輪詢 SCM** – 原始程式碼控制定期輪詢此觸發程序。 如果任何變更已認可到原始程式碼儲存機制，Jenkins 將啟動新的組建。

輪詢 SCM 是常用的觸發程序，因為開發人員認可會造成中斷組建的變更時，它會提供快速的意見反應。 這是適用於一些最近認可的程式碼會造成問題，並可讓開發人員解決問題，還記憶猶新記住所做的變更時警示小組。

定期組建通常會用於建立應用程式散發給軟體測試人員版本。 例如，定期組建可能會排程星期五晚上，以便 QA 小組的成員可以測試前一週中的工作。


### <a name="compiling-a-xamarinios-applications"></a>編譯 Xamarin.iOS 應用程式
可以在命令列中使用編譯 Xamarin.iOS 專案`xbuild`或`msbuild`。 Shell 命令會執行 Jenkins 的使用者帳戶的內容中執行。 請務必確認使用者帳戶具有存取權佈建設定檔，讓應用程式可以正常發佈的封裝。 您可將此 shell 命令加入至工作組態頁面。

向下捲動至**建置**> 一節。 按一下**加入建置步驟**按鈕，然後選取**執行殼層**，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image33.png "按一下 加入建置步驟按鈕，然後選取 執行殼層")


[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>建置 Xamarin.Android 專案

建置 Xamarin.Android 專案是在概念上建置的 Xamarin.iOS 專案非常類似。 若要從 Xamarin.Android 專案建立 APK，Jenkins 必須設定為執行下列兩個步驟：

 - 使用 MSBuild 外掛程式將專案編譯
 - 符號和 zip 對齊 APK 與有效的發行金鑰存放區。

將下兩節中詳細討論這兩個步驟。

### <a name="creating-the-apk"></a>建立 APK

按一下**加入建置步驟**按鈕，然後選取**建置 Visual Studio 專案或方案使用 MSBuild**，如以下螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image36.png "建立 APK 按一下加入建置步驟 按鈕，並選取組建上 Visual Studio 專案或方案使用 MSBuild")

建置步驟加入至專案時，一旦填入所顯示的表單欄位。 下列螢幕擷取畫面會完成的表單的其中一個範例：

![](jenkins-walkthrough-images/image37.png "建置步驟加入至專案時，一旦填入所顯示的表單欄位")


此建置步驟會執行`xbuild`中 **$WORKSPACE**資料夾。 MSBuild 建置檔案設定為**Xamarin.Android.csproj**檔案。 **命令列引數**指定版本組建目標的**包含 PackageForAndroid**。 這個步驟的產品會 APK 的下列位置：

    $WORKSPACE/[PROJECT NAME]/bin/Release

下列螢幕擷取畫面顯示此 APK 的範例：

![](jenkins-walkthrough-images/image38.png "這個螢幕擷取畫面顯示此 APK 的範例")

此 APK 不準備好進行部署，因為尚未簽署與私用金鑰存放區，而且必須 zip 對齊。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>簽署和 Zipaligning 發行 APK

簽署和 zipaligning APK 是技術上兩個不同的工作執行的 Android SDK 中的兩個不同的命令列工具。 不過，很容易在一個建置動作中的執行。 如需簽署和 zipaligning APK 的詳細資訊，請參閱準備 Android 應用程式版本中的 Xamarin 的文件。

這兩個命令都需要命令列參數的可能差異專案對專案。 此外，有些這些命令列參數是不應該出現在主控台輸出的組建正在執行時的密碼。 我們將在環境變數中儲存部分這些命令列參數。 下表描述所需的簽署及/或 zip 對齊的環境變數：

|環境變數|描述|
|--- |--- |
|KEYSTORE_FILE|這是要用於簽署 APK 的金鑰存放區的路徑|
|KEYSTORE_ALIAS|將用來簽署 APK 的金鑰存放區中的索引鍵。|
|INPUT_APK|所建立的 APK `xbuild`。|
|SIGNED_APK|所產生的帶正負號的 APK `jarsigner`。|
|FINAL_APK|這是 zip 對齊由所產生的 APK `zipalign`。|
|STORE_PASS|這是用來存取如 singing 檔案的金鑰存放區內容的密碼。|

需求 > 一節所述，可以使用 EnvInject 外掛程式在建置期間設定這些環境變數。 作業都應該具有新的建置步驟會根據加入插入的環境變數，在下一個螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image39.png "作業都應該具有新的建置步驟會根據加入插入的環境變數")

在**屬性內容**形成會出現的欄位、 環境變數會加入，每行一個，以下列格式：

    ENVIRONMENT_VARIABLE_NAME = value

下列螢幕擷取畫面顯示環境變數所需的簽署 APK:

![](jenkins-walkthrough-images/image40.png "這個螢幕擷取畫面顯示環境變數所需的簽署 APK")

請注意，某些 APK 檔環境變數建置`WORKSPACE`環境變數。

最終的環境變數是要存取金鑰存放區內容的密碼： `STORE_PASS`。 密碼是敏感值應該遮蔽或記錄檔中省略。 若要保護這些值，使它們不會顯示記錄檔中，可以設定 EnvInject 外掛程式。

之前，立即**建置**工作設定的區段是**Build Environment** > 一節。 當**插入密碼**切換核取方塊、 某種形式的欄位才會出現。 這些表單欄位可用來擷取名稱和環境變數的值。 下列螢幕擷取畫面是新增的範例`STORE_PASS`環境變數：

![](jenkins-walkthrough-images/image41.png "這個螢幕擷取畫面是新增 STOREPASS 環境變數的範例")

一旦環境變數初始化後下, 一個步驟是加入建置步驟，來簽署和 zip 等對齊 APK。 之後立即建置步驟来插入的環境變數會是另一個**執行殼層**將執行的命令組建`jarsigner`和`zipalign`。 每個命令將會佔用一行，如下列程式碼片段所示：


    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK

下列螢幕擷取畫面顯示如何輸入範例`jarsigner`和`zipalign`命令至步驟：

![](jenkins-walkthrough-images/image42.png "這個螢幕擷取畫面顯示如何輸入步驟的 jarsigner 和 zipalign 命令的範例")

組建的所有動作都備妥之後，最好以觸發手動的組建，以確認一切運作。 如果建置失敗，**主控台輸出**應該檢閱有關造成建置失敗的原因。

### <a name="submitting-tests-to-test-cloud"></a>正在提交測試來測試雲端

自動化的測試可以提交給測試雲端中使用殼層命令。 多個在 Xamarin Test Cloud 測試回合設定的詳細資訊，請參閱本指南，使用[Xamarin.UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)。


## <a name="summary"></a>總結

本指南中我們 Jenkins 推出 Mac OS X 上的組建伺服器，並設定它要編譯並備妥 Xamarin 行動應用程式版本。 我們在數個外掛程式，以支援建置程序以及 Mac OS X 電腦上安裝 Jenkins。 我們建立，而設定的作業，將會從 TFS 或 Git、 提取的程式碼，然後將該程式碼編譯成發行準備應用程式。 我們也會探索排程何時應執行工作的兩種不同方式。

## <a name="related-links"></a>相關連結

- [持續整合](https://developer.xamarin.com~/testcloud/ci/)
- [送出至 Xamarin Test Cloud 測試](https://developer.xamarin.com~/testcloud/submitting/)
- [為什麼 Jenkins 不支援 Xamarin？](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)
