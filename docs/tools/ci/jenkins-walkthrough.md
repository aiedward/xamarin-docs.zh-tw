---
title: 搭配 Xamarin 使用 Jenkins
description: 本文件說明如何使用 Jenkins 搭配 Xamarin 應用程式進行持續整合。 它討論如何安裝、 設定及使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: 2e6a75fa3c4c63e8dea402c6761f8ef753908540
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047486"
---
# <a name="using-jenkins-with-xamarin"></a>搭配 Xamarin 使用 Jenkins

_本指南說明如何設定 Jenkins 持續整合伺服器，並將自動化編譯建立使用 Xamarin 的行動應用程式。它說明如何在 OS X 上安裝 Jenkins、 加以設定，以及設定作業，若要變更認可至原始程式碼管理系統時，編譯 Xamarin.iOS 和 Xamarin.Android 應用程式。_

[使用 Xamarin 的連續整合簡介](~/tools/ci/intro-to-ci.md)導入了有用的軟體開發做法是提供早期警告的損毀或不相容的程式碼的持續整合。 CI 可在發生，並會保留在合適的部署狀態的軟體，讓開發人員解決問題和問題。 此逐步解說將說明如何搭配使用這兩個文件中的內容。

本指南說明如何在執行 OS X 在專用電腦上安裝 Jenkins，並將它設定為在電腦啟動時自動執行。 Jenkins 安裝之後，我們將安裝額外的外掛程式，以支援 MS Build。 Jenkins 立即可用的支援 Git。 如果使用 TFS 進行原始程式碼控制，也必須安裝額外的外掛程式和命令列公用程式。

一旦 Jenkins 已設定且已安裝任何必要的外掛程式，我們將建立一或多個工作，以編譯 Xamarin.Android 和 Xamarin.iOS 專案。 作業是一組步驟和執行一些工作所需的中繼資料。 作業通常包含下列任一動作：

- **來源的程式碼管理 (SCM)** – 這是中繼資料中的項目 Jenkins 組態檔，其中包含如何連線到原始程式碼控制，以及哪些檔案要擷取的資訊。
- **觸發程序**– 用來啟動特定動作，例如當開發人員會認可變更原始程式碼存放庫為基礎的工作觸發程序。
- **組建指示**– 這是外掛程式或將編譯的原始程式碼和產生二進位檔，可在行動裝置上安裝的指令碼。
- **選擇性的建置動作**– 這可能包括執行單元測試、 執行靜態分析的程式碼，簽署程式碼，或從另一項工作執行其他組建相關的工作。
- **通知**– 作業可能會送出某種類型的組建的狀態相關通知。
- **安全性**– 雖然這是選擇性的強烈建議也啟用了 Jenkins 安全性功能。

本指南會逐步解說如何設定 Jenkins 伺服器涵蓋每個點。 它的結束時，我們應該充分了解如何安裝和設定 Jenkins 以建立我們的 Xamarin 行動專案的 IPA 與 APK。

## <a name="requirements"></a>需求

在理想的組建伺服器是專門用來建置和可能測試應用程式的唯一目的的獨立電腦。 在專用的電腦可確保，可能還需要其他角色 （例如，web 伺服器） 的成品不會污染組建。 比方說，如果組建伺服器也做為 web 伺服器中，web 伺服器可能需要一些通用的程式庫版本。 由於這項衝突的網頁伺服器可能無法正確運作或 Jenkins 可能會建立部署至使用者時，無法運作的組建。

Xamarin 行動應用程式的組建伺服器是設定非常類似開發人員的工作站。 它有在哪一個 Jenkins，Visual Studio for Mac 中的使用者帳戶，並將安裝 Xamarin.iOS 和 Xamarin.Android。 所有的程式碼簽署憑證，佈建的設定檔，以及金鑰存放區必須一併安裝。 *通常在組建伺服器的使用者帳戶與不同開發人員帳戶-請務必安裝及設定所有軟體、 金鑰和憑證時使用的組建伺服器使用者帳戶登入。*

下圖說明所有這些項目在一般的 Jenkins 組建伺服器上：

[![](jenkins-walkthrough-images/image1.png "此圖說明所有這些項目在一般的 Jenkins 組建伺服器")](jenkins-walkthrough-images/image1.png#lightbox)

iOS 應用程式可以只建置並執行 macOS 的電腦上簽署。 Mac Mini 是合理的低成本選項，但任何能夠執行 OS X 10.10 (Yosemite) 的電腦，或更高版本就已足夠。

如果使用 TFS 進行原始程式碼控制，您會想要安裝[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)。 Team Explorer Everywhere 提供跨平台存取 TFS 在 macOS 中的終端機。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安裝 Jenkins

使用 Jenkins 的第一個工作是將其安裝。 有三種方式來執行 OS x： 上的 Jenkins

- 為精靈，在背景中執行。
- 在例如 Tomcat、 Jetty 或 JBoss servlet 容器。
- 為使用者帳戶下執行的一般程序。

大部分傳統的持續整合應用程式在背景執行，以作為精靈 (在 OS X 上或\*nix) 或與服務 （於 Windows)。 不不需要任何 GUI 互動，而且可以輕鬆執行安裝程式的建置環境，這是適合的案例。 行動裝置應用程式也需要 keystores 和簽署憑證，可能會有問題，因為若要存取 Jenkins 執行為精靈。 基於這些考量，本文件著重於第三個案例： 使用者帳戶下執行 Jenkins 組建伺服器上。

Jenkins.App 是一個便利的方式安裝 Jenkins。 這是 AppleScript 包裝函式，可簡化的開始和停止的 Jenkins 伺服器。 而不是在 bash 殼層中執行，Jenkins 會應用程式的圖示在 Dock 中，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image2.png "而不是在 bash 殼層中執行，Jenkins 會應用程式的圖示在 Dock 中，此螢幕擷取畫面所示")](jenkins-walkthrough-images/image2.png#lightbox)

啟動或停止 Jenkins 是啟動或停止 Jenkins.App 一樣簡單。

若要安裝 Jenkins.App，下載最新版本從專案的 [下載] 頁面中，以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image3.png "應用程式，下載最新版本的專案中下載頁面上，如圖所示，在此螢幕擷取畫面")](jenkins-walkthrough-images/image3.png#lightbox)

若要壓縮檔 zip 檔案`/Applications`上您的組建伺服器上，它就像任何其他 OS X 應用程式的啟動資料夾。
第一次您啟動 Jenkins.App，它會顯示一個對話方塊，告訴您它會下載 Jenkins:

[![](jenkins-walkthrough-images/image4.png "應用程式，它會顯示一個對話方塊，告訴您它會下載 Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

一旦 Jenkins.App 完成其下載，它會顯示另一個對話方塊，要求您，是否您想要自訂 Jenkins 啟動時，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image5.png "應用程式已完成其下載，便會顯示另一個對話方塊，要求您，是否您想要自訂 Jenkins 啟動時，此螢幕擷取畫面所示")](jenkins-walkthrough-images/image5.png#lightbox)

自訂 Jenkins 是選擇性的而且沒有要進行每次啟動應用程式 – 時的預設設定，Jenkins 將大多數的情況下運作。

如果有必要自訂 Jenkins，按一下**變更預設值** 按鈕。 這會顯示兩個連續的對話方塊： Java 命令列參數，要求您提供的其中一個，而另一個要求輸入 Jenkins 命令列參數。 下列的兩個螢幕擷取畫面顯示這些兩個對話方塊：

[![](jenkins-walkthrough-images/image6.png "此螢幕擷取畫面顯示對話方塊")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "此螢幕擷取畫面顯示對話方塊")](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 開始執行之後，您可能想要將它設為登入項目，如此它就會啟動每次使用者登入電腦。 您可以在 Dock 中的 [Jenkins] 圖示上按一下滑鼠右鍵，然後選擇**選項 > 在登入時開啟**，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image8.png "您可以在 Dock 中的 [Jenkins] 圖示上按一下滑鼠右鍵，然後選擇 OptionsOpen 在登入時，此螢幕擷取畫面所示")](jenkins-walkthrough-images/image8.png#lightbox)

這會導致 Jenkins.App 自動啟動 每次使用者登入，但不是啟動電腦時啟動。 您可指定 OS X 在開機時，會使用自動使用的登入的使用者帳戶。 開啟**系統偏好設定**，然後選取**使用者和群組**圖示，如以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image9.png "開啟 系統偏好設定，然後選取使用者群組圖示，如以下螢幕擷取畫面所示")](jenkins-walkthrough-images/image9.png#lightbox)

按一下 **登入選項**按鈕，然後再選擇 OS X 在開機時，會使用登入的帳戶。

目前已安裝 Jenkins。 不過，如果我們想要建置 Xamarin 行動應用程式，我們必須安裝一些外掛程式。

### <a name="installing-plugins"></a>安裝外掛程式

Jenkins.App 安裝程式完成時，它將會啟動 Jenkins，並啟動 web 瀏覽器的 url http://localhost:8080，如以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image10.png "8080，如以下螢幕擷取畫面所示")](jenkins-walkthrough-images/image10.png#lightbox)

從這個頁面上，選取**Jenkins > 管理 Jenkins > 管理外掛程式**從功能表中的左上角，如以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image11.png "從這個頁面上，請從左上角的功能表中選取 Jenkins 管理 Jenkins 管理外掛程式")](jenkins-walkthrough-images/image11.png#lightbox)

這會顯示**Jenkins 外掛程式管理員**頁面。 如果您按一下 [可用] 索引標籤上，您會看到一份可供下載和安裝的超過 600 個外掛程式。 這是由以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image12.png "如果您按一下 [可用] 索引標籤上，您會看到一份可供下載和安裝的超過 600 個外掛程式")](jenkins-walkthrough-images/image12.png#lightbox)

捲動所有 600 外掛程式來尋找一些可能會非常繁瑣又容易出錯。 Jenkins 提供介面的右上角的篩選器搜尋 欄位。 使用此篩選條件欄位來搜尋會簡化尋找並已安裝的其中一個或所有下列外掛程式：

- **Jenkins MSBuild 外掛程式**– 此外掛程式可讓您建置 Visual Studio 和 Visual Studio for Mac 方案 (.sln) 和專案 (.csproj)。
- **環境載入程式外掛程式**– 這是選擇性的但很有用的外掛程式，讓您能夠在工作設定環境變數，並建置層級。 它也提供額外的保護，變數，例如程式碼所使用的密碼登入應用程式。 有時縮寫為*EnvInject 外掛程式*。
- **Team Foundation Server 外掛程式**– 這是選擇性的外掛程式，它只需要如果您使用 Team Foundation Server 或 Team Foundation Services 進行原始程式碼控制。

Jenkins 支援 Git，而不需要任何額外的外掛程式。

安裝之後所有的外掛程式，您會想要重新啟動 Jenkins，並設定每個外掛程式的全域設定。 外掛程式的全域設定可以選取，即可找到**Jenkins > 管理 Jenkins > 設定系統**從左上角，如以下螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image13.png "藉由選取 Jenkins 找外掛程式的全域設定 / 管理 Jenkins / 從左上方的 設定系統交給角")](jenkins-walkthrough-images/image13.png#lightbox)

當您選取此功能表選項時，您將會進入**設定的系統 [Jenkins]** 頁面。 此頁面包含各節來設定本身的 Jenkins，並設定一些通用的外掛程式值。  以下螢幕擷取畫面說明了此頁面的範例：

[![](jenkins-walkthrough-images/image14.png "此螢幕擷取畫面說明了此頁面的範例")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>設定 MSBuild 外掛程式

MSBuild 外掛程式必須設定為使用 **/Library/Frameworks/Mono.framework/Commands/xbuild**編譯 Visual Studio for Mac 方案和專案檔。 向下捲動**設定的系統 [Jenkins]** 頁面上，直到**新增 MSBuild**按鈕隨即出現，如以下螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image15.png "捲動 [設定系統 Jenkins] 頁面上，直到新增 MSBuild 按鈕出現")](jenkins-walkthrough-images/image15.png#lightbox)

按一下此按鈕，並填妥**名稱**並**路徑**來**MSBuild**上出現的表單欄位。 名稱您**MSBuild**安裝應該仍有意義的名稱，同時**MSBuild 路徑**必須為路徑`xbuild`，這通常是 **/Library/架構 /Mono.framework/Commands/xbuild**。 我們按一下 儲存 或 在頁面底部的 套用 按鈕儲存變更之後，Jenkins 將能夠使用`xbuild`編譯您的解決方案。

#### <a name="configuring-the-tfs-plugin"></a>設定 TFS 外掛程式

此區段是必要項目，如果您想要使用 TFS 進行原始程式碼控制。

為了讓 TFS 伺服器上，與互動的 macOS 工作站[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)必须在工作站上。 Team Explorer Everywhere 是一組包含用於存取 TFS 的跨平台命令列用戶端的 microsoft 工具。 Team Explorer Everywhere 可從 Microsoft 下載並安裝在三個步驟：

1. 解壓縮封存檔案的使用者帳戶可以存取的目錄。 例如，您可能會在其中解壓縮檔案以 **~/tee**。
2. 設定的殼層或系統的路徑，以包含保存已在步驟上方解壓縮之檔案的資料夾。 例如，套用至物件的

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. 若要確認已安裝 Team Explorer Everywhere，請開啟終端機工作階段，並執行`tf`命令。 如果 tf 已正確設定，您會看到下列輸出，您的終端機工作階段中：

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

TFS 的命令列用戶端安裝之後，必須使用的完整路徑中設定 Jenkins`tf`命令列用戶端。 向下捲動**設定的系統 [Jenkins]** 頁面上，直到您找出 Team Foundation Server 區段中，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image17.png "設定系統 Jenkins 頁面上向下捲動，直到您找到 [Team Foundation Server] 區段")](jenkins-walkthrough-images/image17.png#lightbox)

輸入完整路徑，以`tf`命令，然後按一下**儲存** 按鈕。

### <a name="configure-jenkins-security"></a>設定 Jenkins 安全性

最初安裝時，Jenkins 會有已停用，安全性，因此您有任何使用者設定，並以匿名方式執行任何種類的工作。 本節說明如何使用 Jenkins 使用者資料庫來設定驗證和授權設定安全性。

可找到的安全性設定，請選取**Jenkins > 管理 Jenkins > 設定全域安全性**，在此螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image18.png "可找到的安全性設定，請選取 Jenkins / 管理 Jenkins / 設定全域安全性")](jenkins-walkthrough-images/image18.png#lightbox)

在上**設定全域安全性**頁面上，核取**啟用安全性**核取方塊並**存取控制**表單應該會出現，類似於下一步的螢幕擷取畫面：

[![](jenkins-walkthrough-images/image19.png "在 [設定全域安全性] 頁面中，勾選 啟用安全性的核取方塊和存取控制表單應該會出現，類似這個螢幕擷取畫面")](jenkins-walkthrough-images/image19.png#lightbox)

切換的選項按鈕**Jenkins 自己的使用者資料庫**中**安全性領域 > 一節**，並確定**允許使用者註冊**也勾選，如所示下列螢幕擷取畫面：

[![](jenkins-walkthrough-images/image20.png "切換 Jenkins 自己的使用者資料庫，在安全性領域 > 一節中的選項按鈕，並確定 允許使用者登入，也會核取")](jenkins-walkthrough-images/image20.png#lightbox)

最後，重新啟動 Jenkins，並建立新的帳戶。 第一個建立的帳戶是根帳戶，和此帳戶將會自動升級為系統管理員。 瀏覽回到**設定全域安全性**頁面，然後取消核取**矩陣為基礎的安全性**選項按鈕。 根帳戶應該授與完整存取權，以及匿名帳戶應該被授與唯讀存取權，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image21.png "根帳戶應該授與完整存取權，以及匿名帳戶應該被授與唯讀存取權")](jenkins-walkthrough-images/image21.png#lightbox)

一旦這些設定會儲存並重新啟動 Jenkins，安全性會開啟。

#### <a name="disabling-security"></a>停用的安全性

發生忘記密碼或 Jenkins 全鎖定時，就可以停用安全性，依照下列步驟：

1. 停止 Jenkins。 如果您使用 Jenkins.app，您可以停駐、 Jenkins.App 圖示上按一下滑鼠右鍵，然後從快顯功能表中選取 Quit:

    ![停駐，並從快顯功能表中選取 Quit 中應用程式圖示](jenkins-walkthrough-images/image19.png)

2. 開啟檔案 **~/.jenkins/config.xml**在文字編輯器中。
3. 值變更`<usesecurity></usesecurity>`項目`true`至`false`。
4. 刪除`<authorizationstrategy></authorizationstrategy>`而`<securityrealm></securityrealm>`檔案中的項目。
5. 重新啟動 Jenkins。

## <a name="setting-up-a-job"></a>設定工作

在最上層，Jenkins 將所有的組織建置軟體所需的各種工作*作業*。 作業也都有與其相關聯，例如如何取得原始碼、 組建執行的頻率、 任何特殊的變數所需的建置，以及如何在建置失敗通知開發人員建置的資訊提供的中繼資料。

工作藉由選取**Jenkins > 新的工作**從功能表中的右上角，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image22.png "從右上角的功能表中選取新的 Jenkins 作業建立作業")

這會顯示**新的工作 [Jenkins]** 頁面。 輸入作業名稱，然後選取**建置自由樣式的軟體專案**選項按鈕。 下列螢幕擷取畫面會顯示這個範例：

![](jenkins-walkthrough-images/image23.png "輸入作業名稱，然後選取組建的軟體自由樣式專案選項按鈕")

按一下 **確定**按鈕會顯示作業的 組態 頁面。 這看起來應該像下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image24.png "這看起來應該像這個螢幕擷取畫面")

Jenkins 會組織在位於下列路徑的硬碟上的目錄中的作業： **~/.jenkins/jobs/[JOB 名稱]**

此資料夾包含的所有檔案和構件特有的工作，例如記錄檔、 組態檔，以及需要重新編譯的原始程式碼。

一旦建立初始作業，它必須設定一或多個項目：

- 必須指定原始程式碼管理系統。
- 一或多個*建置動作*必須加入至專案。 這些是步驟或建置應用程式所需的工作。
- 作業必須被指派其中一個*組建觸發程序*– 一組通知頻率 Jenkins 擷取程式碼，並建置最終專案的指示。

### <a name="configuring-source-code-control"></a>設定原始程式碼控制

Jenkins 會在第一個工作是從原始程式碼管理系統中擷取的原始程式碼。 Jenkins 可支援許多熱門的原始碼程式碼管理系統立即可用。 本章節涵蓋兩個熱門的系統，Git 和 Team Foundation Server。 下列各節更詳細地討論這些原始程式碼管理系統。

#### <a name="using-git-for-source-code-control"></a>使用 Git 進行原始程式碼控制

如果您使用 TFS 原始程式碼控制，如[略過](#using-tfs-for-source-code-management)此區段，然後繼續進行下一節中使用 TFS。

Jenkins 支援 Git 現成 – 沒有額外的外掛程式所需。 若要使用 Git，請按一下**Git**選項按鈕，並針對 Git 存放庫中，輸入 URL，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image25.png "若要使用 Git，按一下 [Git] 選項按鈕並輸入 Git 存放庫的 URL")

一旦儲存變更，Git 組態已完成。

#### <a name="using-tfs-for-source-code-management"></a>使用原始程式碼管理 TFS

本節僅適用於 TFS 使用者。

按一下  **Team Foundation Server**選項按鈕和 TFS 的組態區段應該會出現，類似於下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image26.png "按一下 Team Foundation Server 的選項按鈕和 TFS 的組態區段應該會出現")

提供適用於 TFS 的必要資訊。 下列螢幕擷取畫面示範完成的表單：

![](jenkins-walkthrough-images/image27.png "此螢幕擷取畫面示範完成的表單")

#### <a name="testing-the-source-code-control-configuration"></a>測試來源的程式碼控制組態

一旦已設定適當的原始程式碼控制，按一下**儲存**以儲存變更。 這會回到您的作業，將會類似下列螢幕擷取畫面的 [首頁] 頁面：

![](jenkins-walkthrough-images/image28.png "這會傳回您要進行作業，將會類似這個螢幕擷取畫面的 [首頁] 頁面")

驗證已正確設定的原始程式碼控制的最簡單方式是以手動觸發組建，即使沒有指定的建置動作。 若要手動啟動組建，首頁上的作業已**立即建置**連結在左側功能表中，如以下螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image29.png "若要手動啟動組建，首頁上的作業會有立即組建連結在左手邊的功能表中")

啟動組建之後，[組建歷程記錄] 對話方塊會顯示閃爍的藍色圓形、 進度列、 組建編號和組建開始時間，類似下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image30.png "啟動組建之後，[組建歷程記錄] 對話方塊會顯示閃爍藍色圓形、 進度列、 組建編號和組建的開始時間")

如果作業成功時，將會顯示藍色圓形。 如果作業失敗時，將會顯示一個紅色圓圈。

為了協助進行疑難排解的建置過程中可能發生的問題，Jenkins 會擷取所有工作的主控台輸出。 若要查看的主控台輸出，按一下中的作業**建置的歷程記錄**，然後在**主控台輸出**左側功能表中的連結。 下列螢幕擷取畫面示**主控台輸出**連結，以及一些成功的作業的輸出：

![](jenkins-walkthrough-images/image31.png "此螢幕擷取畫面顯示 [主控台輸出] 連結，以及一些成功的作業的輸出")

#### <a name="location-of-build-artifacts"></a>組建成品的位置

Jenkins 會擷取到呼叫的特殊資料夾的完整原始程式碼*工作區*。 在下列位置的資料夾內，就可以找到這個目錄：

    ```
    ~/.jenkins/jobs/[JOB NAME]/workspace
    ```

工作區的路徑會儲存在名為環境變數`$WORKSPACE`。

您可依瀏覽至作業的登陸頁面，然後按一下瀏覽在 Jenkins 中的工作區資料夾**工作區**左側功能表中的連結。 下列螢幕擷取畫面顯示名為作業的工作區的範例**HelloWorld**:

![](jenkins-walkthrough-images/image32.png "此螢幕擷取畫面顯示名為 HelloWorld 工作的工作區的範例")

### <a name="build-triggers"></a>建立觸發程序

有數個不同的策略，來起始在 Jenkins 中的組建 – 這些值稱為*組建觸發程序*。 組建觸發程序可協助您決定何時要啟動的工作，並建置專案的 Jenkins。 兩個較常見的組建觸發程序如下：

- **定期建置**– 此觸發程序會導致啟動的工作，以指定的間隔，例如每隔兩小時，或在工作日午夜 Jenkins。 建置將會啟動不論是否有任何變更過原始程式碼存放庫。
- **輪詢 SCM** – 此觸發程序會輪詢以規則為基礎的原始程式碼控制。 如果任何變更都已認可至原始程式碼存放庫，Jenkins 將會啟動新的組建。

輪詢 SCM 是受歡迎的觸發程序，因為它提供快速的意見反應，當開發人員認可會造成組建中斷的變更。 這是適用於一些最近認可的程式碼會造成問題，並可讓開發人員解決問題，還記憶猶新記住所做的變更時警示小組。

定期的組建是通常用來建立應用程式散發給測試人員版本。 比方說，定期組建可能會排在星期五晚上，QA 小組的成員可測試前一週中的工作。

### <a name="compiling-a-xamarinios-applications"></a>編譯 Xamarin.iOS 應用程式
Xamarin.iOS 專案可以在命令列使用編譯`xbuild`或`msbuild`。 Shell 命令會執行 Jenkins 的使用者帳戶內容中執行。 很重要的使用者帳戶具有佈建設定檔的存取權，使應用程式正確封裝準備散發。 您可將此 shell 命令新增至 [作業組態] 頁面。

向下捲動至**建置**一節。 按一下**新增組建步驟**按鈕，然後選取**執行殼層**，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image33.png "按一下 [新增組建步驟] 按鈕，然後選取 Execute shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>建置 Xamarin.Android 專案

建置 Xamarin.Android 專案是非常類似的概念來建置的 Xamarin.iOS 專案。 若要建立從 Xamarin.Android 專案的 APK，Jenkins 必須設定為執行下列兩個步驟：

- 使用 MSBuild 外掛程式將專案編譯
- 符號和 zip 對齊 APK 使用有效的發行金鑰儲存區。

這兩個步驟將在下面兩節中詳細涵蓋。

### <a name="creating-the-apk"></a>建立 APK

按一下 **新增組建步驟**按鈕，然後選取**建置 Visual Studio 專案或方案使用 MSBuild**，如以下螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image36.png "建立 APK 按一下 [新增組建步驟] 按鈕，然後選取建置 Visual Studio 專案或方案使用 MSBuild")

一旦建置步驟新增至專案，請填寫出現的表單欄位。 下列螢幕擷取畫面是表單的已完成的其中一個範例：

![](jenkins-walkthrough-images/image37.png "一旦建置步驟新增至專案，填滿中出現的表單欄位")

這個組建步驟會執行`xbuild`中 **$WORKSPACE**資料夾。 MSBuild 建置檔案設成**Xamarin.Android.csproj**檔案。 **命令列引數**指定的目標版本組建**包含 PackageForAndroid**。 此步驟的產品會 APK，在下列位置：

    ```
    $WORKSPACE/[PROJECT NAME]/bin/Release
    ```

下列螢幕擷取畫面顯示此 APK 的範例：

![](jenkins-walkthrough-images/image38.png "此螢幕擷取畫面顯示此 APK 的範例")

此 APK 不準備好進行部署，因為它尚未簽署使用私用金鑰儲存區，而且必須 zip 對齊。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>簽署及 Zipalign APK 版本

簽署及 zipalign APK 技術上兩個個別工作，它們由兩個個別的命令列工具，從 Android SDK。 不過，很方便執行中的一個建置動作。 如需有關簽署及 zipalign APK 的詳細資訊，請參閱有關準備 Android 應用程式發行的 Xamarin 的文件。

這兩個命令都需要命令列參數都有不同的專案對專案。 此外，某些命令列參數都不應該出現在主控台輸出執行組建時的密碼。 我們將在環境變數中儲存某些命令列參數。 下表說明所需的簽署及/或 zip 對齊的環境變數：

|環境變數|描述|
|--- |--- |
|KEYSTORE_FILE|這是簽署 APK 的 keystore 的路徑|
|KEYSTORE_ALIAS|將用來簽署 APK 的金鑰存放區中的金鑰。|
|INPUT_APK|所建立的 APK `xbuild`。|
|SIGNED_APK|所產生的已簽署的 APK `jarsigner`。|
|FINAL_APK|這是 zip 對齊由所產生的 APK `zipalign`。|
|STORE_PASS|這是用來存取內容的金鑰存放區來簽署檔案的密碼。|

需求 > 一節所述，則可以使用 EnvInject 外掛程式在建置期間設定這些環境變數。 作業都應該具有新的組建步驟已新增根據插入環境變數，在下一步 的螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image39.png "作業都應該具有新的組建步驟已新增根據插入環境變數")

在 **內容屬性**表單會出現的欄位、 加入變數的環境，每行一個，以下列格式：

    ```
    ENVIRONMENT_VARIABLE_NAME = value
    ```

下列螢幕擷取畫面會顯示所需的簽署 APK 的環境變數：

![](jenkins-walkthrough-images/image40.png "此螢幕擷取畫面顯示環境變數所需的簽署 APK")

請注意，某些環境變數的 APK 檔案的建置`WORKSPACE`環境變數。

最終的環境變數是密碼以存取金鑰存放區的內容： `STORE_PASS`。 密碼是敏感值應隱藏在程式碼中或在記錄檔中省略。 若要保護這些值，使它們不會出現在記錄檔中，可以設定 EnvInject 外掛程式。

之前，立即**建置**工作組態的區段**Build Environment**一節。 當**插入密碼**切換核取方塊、 一些表單欄位才會出現。 這些表單欄位用來擷取名稱和環境變數的值。 下列螢幕擷取畫面是新增的範例`STORE_PASS`環境變數：

![](jenkins-walkthrough-images/image41.png "此螢幕擷取畫面是新增 STOREPASS 環境變數的範例")

一旦環境變數都已初始化下, 一個步驟是新增建置步驟用於簽章和壓縮對齊 APK。 立即建置步驟来插入的環境變數會是另一個後**執行殼層**將會執行的命令建置`jarsigner`和`zipalign`。 每個命令會佔用一行，如下列程式碼片段所示：

    ```
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK
    ```

下列螢幕擷取畫面顯示如何輸入的範例`jarsigner`和`zipalign`命令放在 「 步驟：

![](jenkins-walkthrough-images/image42.png "此螢幕擷取畫面顯示如何在此步驟中輸入 jarsigner 及 zipalign 命令的範例")

一旦所有建置動作會都準備就緒之後，就很好的作法，以確認一切運作正常的手動組建觸發程序。 如果建置失敗，**主控台輸出**應檢閱有關造成建置失敗的原因。

### <a name="submitting-tests-to-test-cloud"></a>提交至 Test Cloud 的測試

自動化的測試可以提交至 Test Cloud 使用殼層命令。 如需在 Xamarin Test Cloud 中執行的測試設定的詳細資訊，請參閱本指南使用[Xamarin.UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)。

## <a name="summary"></a>總結

本指南中我們導入做為組建伺服器，在 macOS 上的 Jenkins，並設定它要編譯並備妥版本的 Xamarin 行動應用程式。 我們在數個外掛程式，以支援建置程序以及 macOS 電腦上安裝 Jenkins。 我們會建立並設定將會從 TFS 或 Git 提取程式碼，並再將該程式碼編譯成發行準備應用程式的工作。 我們也探索了兩種不同方式排定工作應該執行。

## <a name="related-links"></a>相關連結

- [持續整合](~/tools/ci/index.md)
- [App Center 測試](https://docs.microsoft.com/appcenter/test-cloud/)
