---
title: 搭配 Xamarin 使用 Jenkins
description: 本檔說明如何使用 Jenkins 來與 Xamarin 應用程式進行持續整合。 它會討論如何安裝、設定及使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eb792f7b2e24e5a3a25fd48ed0ec60cbaceea107
ms.sourcegitcommit: 424eaef56fd2933c98e72f1d3e7ac71730fe4835
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758119"
---
# <a name="using-jenkins-with-xamarin"></a>搭配 Xamarin 使用 Jenkins

_本指南說明如何將 Jenkins 設定為連續整合伺服器，並自動編譯以 Xamarin 建立的行動應用程式。它說明如何在 OS X 上安裝 Jenkins、進行設定，以及設定作業，以在將變更認可至原始程式碼管理系統時，編譯 Xamarin 和 Xamarin. Android 應用程式。_

[與 Xamarin 的持續整合簡介簡介](~/tools/ci/intro-to-ci.md) 將持續整合引進為實用的軟體發展實務，可提供中斷或不相容程式碼的早期警告。 CI 可讓開發人員在發生問題時解決問題，並讓軟體處於適合部署的狀態。 本逐步解說涵蓋如何同時使用兩份檔的內容。

本指南說明如何在執行 OS X 的專用電腦上安裝 Jenkins，並將它設定為在電腦啟動時自動執行。 Jenkins 安裝完成後，我們會安裝其他外掛程式以支援 MS Build。 Jenkins 支援現成的 Git。 如果 TFS 是用於原始程式碼控制，則也必須安裝額外的外掛程式和命令列公用程式。

設定 Jenkins 並安裝任何必要的外掛程式之後，我們會建立一或多個作業來編譯 Xamarin. Android 和 Xamarin. iOS 專案。 作業是執行一些工作所需的步驟和元資料集合。 作業通常包含下列各項：

- **原始程式碼管理 (SCM)** –這是 Jenkins 設定檔中的中繼資料專案，其中包含如何連線至原始程式碼控制和要取出哪些檔案的資訊。
- **觸發** 程式–觸發程式可用來根據特定動作啟動作業，例如當開發人員認可原始程式碼存放庫的變更時。
- **組建指示** –這是外掛程式或腳本，會編譯原始程式碼並產生可安裝在行動裝置上的二進位檔。
- **選擇性的組建動作** –這可能包括執行單元測試、在程式碼上執行靜態分析、簽署程式碼，或啟動另一個工作來執行其他組建相關工作。
- **通知** –作業可能會傳送某種關於組建狀態的通知。
- **安全性** –雖然是選擇性的，但強烈建議也啟用 Jenkins 安全性功能。

本指南將逐步解說如何設定涵蓋上述各點的 Jenkins 伺服器。 在結束時，我們應該已充分瞭解如何設定和設定 Jenkins，以建立適用于 Xamarin 行動專案的 IPA 和 APK。

## <a name="requirements"></a>需求

理想的組建伺服器是獨立的電腦，專門用來建立及測試應用程式的唯一用途。 專用電腦可確保其他角色可能需要的成品 (例如 web 伺服器) 不會污染組建。 例如，如果組建伺服器也做為 web 伺服器，則網頁伺服器可能需要某些通用程式庫的衝突版本。 由於這項衝突，web 伺服器可能無法正常運作，或 Jenkins 可能會建立在部署至使用者時無法運作的組建。

Xamarin 行動應用程式的組建伺服器的設定非常類似于開發人員的工作站。 它有一個使用者帳戶，其中將會安裝 Jenkins、Visual Studio for Mac 和 Xamarin. Android。 所有程式碼簽署憑證、布建設定檔和金鑰存放區也必須一併安裝。 *組建伺服器的使用者帳戶通常與您的開發人員帳戶不同-在使用組建伺服器使用者帳戶登入時，請務必安裝並設定所有的軟體、金鑰和憑證。*

下圖說明一般 Jenkins 組建伺服器上的所有這些元素：

[![下圖說明一般 Jenkins 組建伺服器上的所有這些元素](jenkins-walkthrough-images/image1.png)](jenkins-walkthrough-images/image1.png#lightbox)

iOS 應用程式只能在執行 macOS 的電腦上建立和簽署。 Mac 迷你是合理的低成本選項，但是任何能夠執行 OS X 10.10 (Yosemite) 或更高版本的電腦都已足夠。

如果 TFS 是用於原始程式碼控制，您會想要安裝 [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/)。 Team Explorer Everywhere 在 macOS 的終端機提供對 TFS 的跨平臺存取。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安裝 Jenkins

使用 Jenkins 的第一個工作是安裝它。 在 OS X 上執行 Jenkins 的方法有三種：

- 作為在背景中執行的 daemon。
- 在 servlet 容器內（例如 Tomcat、Jetty 或 JBoss）。
- 做為在使用者帳戶下執行的一般進程。

大部分傳統的持續整合應用程式會在背景中執行，例如 OS X 或 nix) 的背景程式 (， \* 或 Windows) 上的服務 (。 這適用于不需要 GUI 互動的案例，以及可在何處輕鬆執行組建環境的設定。 行動裝置應用程式也需要金鑰庫和簽署憑證，在 Jenkins 以 daemon 身分執行時可能會有問題。 基於這些考慮，本檔著重于第三個案例-在組建伺服器上的使用者帳戶下執行 Jenkins。

Jenkins 可讓您輕鬆地安裝 Jenkins。 這是可簡化 Jenkins 伺服器啟動和停止的 AppleScript 包裝函式。 Jenkins 會以具有 Dock 圖示的應用程式執行，而不是在 bash shell 中執行，如下列螢幕擷取畫面所示：

[![Jenkins 會以具有 Dock 圖示的應用程式執行，而不是在 bash shell 中執行，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image2.png)](jenkins-walkthrough-images/image2.png#lightbox)

啟動或停止 Jenkins 就像啟動或停止 Jenkins 一樣簡單。

若要安裝 Jenkins，請從專案的下載頁面下載最新版本，如下列螢幕擷取畫面所示：

[![應用程式，從 [專案下載] 頁面下載最新版本，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image3.png)](jenkins-walkthrough-images/image3.png#lightbox)

將 zip 檔案解壓縮至 `/Applications` 組建伺服器上的資料夾，然後像任何其他 OS X 應用程式一樣啟動它。
當您第一次啟動 Jenkins 時，它會顯示一個對話方塊，通知您它將會下載 Jenkins：

[![應用程式會顯示一個對話方塊，通知您它將會下載 Jenkins](jenkins-walkthrough-images/image4.png)](jenkins-walkthrough-images/image4.png#lightbox)

Jenkins 完成後，應用程式會顯示另一個對話方塊，詢問您是否要自訂 Jenkins 啟動，如下列螢幕擷取畫面所示：

[![應用程式已完成下載，它會顯示另一個對話方塊，詢問您是否想要自訂 Jenkins 啟動，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image5.png)](jenkins-walkthrough-images/image5.png#lightbox)

自訂 Jenkins 是選擇性的，不需要在每次啟動應用程式時執行– Jenkins 的預設設定將適用于大部分的情況。

如果需要自訂 Jenkins，請按一下 [ **變更預設值** ] 按鈕。 這會顯示兩個連續的對話：一個要求 JAVA 命令列參數，另一個則要求 Jenkins 命令列參數。 以下兩個螢幕擷取畫面顯示這兩個對話方塊：

[![此螢幕擷取畫面顯示要求 JAVA 命令列參數的對話方塊。](jenkins-walkthrough-images/image6.png)](jenkins-walkthrough-images/image6.png#lightbox)

[![此螢幕擷取畫面顯示要求 Jenkins 命令列參數的對話方塊。](jenkins-walkthrough-images/image7.png)](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 執行之後，您可能會想要將它設定為登入專案，以便每次使用者登入電腦時都會啟動。 若要這樣做，您可以用滑鼠右鍵按一下 Dock 中的 Jenkins 圖示，然後選擇 [選項]， **> 在登入時開啟**]，如下列螢幕擷取畫面所示：

[![若要這樣做，您可以用滑鼠右鍵按一下 Dock 中的 Jenkins 圖示，然後選擇 [登入時 OptionsOpen]，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image8.png)](jenkins-walkthrough-images/image8.png#lightbox)

這會導致 Jenkins 在使用者每次登入時自動啟動，而不是在電腦啟動時自動啟動。 您可以指定 OS X 將用來在開機時自動登入的使用者帳戶。 開啟 [ **系統偏好** 設定]，然後選取 [ **使用者 & 群組** ] 圖示，如下列螢幕擷取畫面所示：

[![開啟 [系統偏好設定]，然後選取 [使用者群組] 圖示，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image9.png)](jenkins-walkthrough-images/image9.png#lightbox)

按一下 [ **登入選項** ] 按鈕，然後選擇 OS X 在開機時將用來登入的帳戶。

此時已安裝 Jenkins。 但是，如果我們想要建立 Xamarin 行動應用程式，則需要安裝一些外掛程式。

### <a name="installing-plugins"></a>安裝外掛程式

當 Jenkins 安裝程式完成時，它會開始 Jenkins 並使用 URL 啟動網頁瀏覽器 http://localhost:8080 ，如下列螢幕擷取畫面所示：

[![8080，如下列螢幕擷取畫面所示](jenkins-walkthrough-images/image10.png)](jenkins-walkthrough-images/image10.png#lightbox)

從這個頁面中，選取左上角功能表中的 [ **Jenkins > 管理 Jenkins > 管理外掛程式** ]，如下列螢幕擷取畫面所示：

[![從這個頁面中，從左上角的功能表中選取 [Jenkins 管理 Jenkins 管理外掛程式]](jenkins-walkthrough-images/image11.png)](jenkins-walkthrough-images/image11.png#lightbox)

這會顯示 [ **Jenkins 外掛程式管理員** ] 頁面。 如果您按一下可用的索引標籤，就會看到可供下載和安裝的600以上外掛程式清單。 如下圖所示：

[![如果您按一下可用的索引標籤，就會看到可供下載和安裝的600以上外掛程式清單](jenkins-walkthrough-images/image12.png)](jenkins-walkthrough-images/image12.png#lightbox)

滾動所有600外掛程式以找出一些可能會很繁瑣且容易出錯。 Jenkins 會在介面右上角提供篩選搜尋欄位。 使用此篩選欄位進行搜尋將可簡化尋找並安裝下列其中一項或所有外掛程式的程式：

- **Jenkins MSBuild 外掛程式** –此外掛程式可讓您建立 Visual Studio 和 Visual Studio for Mac 方案 ( .sln) 和專案 ( .csproj) 。
- **環境 Injector 外掛程式** –這是選擇性但有用的外掛程式，可讓您在作業和組建層級設定環境變數。 它也為變數提供額外的保護，例如用於撰寫程式碼簽署應用程式的密碼。 有時會縮寫為  *EnvInject 外掛程式* 。
- **Team Foundation Server 外掛程式** –這是選擇性的外掛程式，只有當您使用 Team Foundation Server 或 Team Foundation Services 進行原始程式碼控制時才需要。

Jenkins 支援不含任何額外外掛程式的 Git。

安裝所有外掛程式之後，您會想要重新開機 Jenkins，並為每個外掛程式設定全域設定。 從左上角選取 [ **Jenkins > 管理 Jenkins > 設定系統** ]，即可找到外掛程式的全域設定，如下列螢幕擷取畫面所示：

[![從左上角選取 [Jenkins]/[管理 Jenkins]/[設定系統]，即可找到外掛程式的全域設定](jenkins-walkthrough-images/image13.png)](jenkins-walkthrough-images/image13.png#lightbox)

當您選取此功能表選項時，將會進入 **[設定系統 [Jenkins]]** 頁面。 此頁面包含設定 Jenkins 本身以及設定部分全域外掛程式值的區段。  下列螢幕擷取畫面說明此頁面的範例：

[![此螢幕擷取畫面說明此頁面的範例](jenkins-walkthrough-images/image14.png)](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>設定 MSBuild 外掛程式

MSBuild 外掛程式必須設定為使用 **/Library/Frameworks/Mono.framework/Commands/xbuild** 來編譯 Visual Studio for Mac 方案和專案檔案。 在 **[設定系統 [Jenkins]]** 頁面上向下移動，直到出現 [ **加入 MSBuild** ] 按鈕，如下列螢幕擷取畫面所示：

 [![在 [設定系統 Jenkins] 頁面上向下移動，直到出現 [加入 MSBuild] 按鈕為止](jenkins-walkthrough-images/image15.png)](jenkins-walkthrough-images/image15.png#lightbox)

按一下這個按鈕，然後在出現的表單上填入 **MSBuild** 欄位的 **名稱** 和 **路徑**。 **Msbuild** 安裝的名稱應該有意義，而 **msbuild 的路徑** 應該是的路徑 `xbuild` ，通常是 **/Library/Frameworks/Mono.framework/Commands/xbuild**。 在儲存變更之後，按一下頁面底部的 [儲存] 或 [套用] 按鈕，Jenkins 就能用 `xbuild` 來編譯您的解決方案。

#### <a name="configuring-the-tfs-plugin"></a>設定 TFS 外掛程式

如果您想要使用 TFS 進行原始程式碼控制，則必須使用此區段。

為了讓 macOS 工作站與 TFS 伺服器互動， [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) 必須安裝在工作站上。 Team Explorer Everywhere 是 Microsoft 的一組工具，包含用於存取 TFS 的跨平臺命令列用戶端。 您可以從 Microsoft 下載 Team Explorer Everywhere，並以三個步驟安裝：

1. 將封存檔案解壓縮到使用者帳戶可存取的目錄。 例如，您可能會將檔案解壓縮至 **~/tee**。
2. 將 shell 或系統路徑設定為包含資料夾，而該資料夾包含在上述步驟1中解壓縮的檔案。 例如，

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. 若要確認已安裝 Team Explorer Everywhere，請開啟終端機會話，然後執行 `tf` 命令。 如果已正確設定 tf，您將會在終端機會話中看到下列輸出：

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

安裝 TFS 的命令列用戶端之後，必須使用 `tf` 命令列用戶端的完整路徑來設定 Jenkins。 在 **[設定系統 [Jenkins]]** 頁面上向下移動，直到您找到 Team Foundation Server 區段，如下列螢幕擷取畫面所示：

[![在 [設定系統 Jenkins] 頁面上向下移動，直到您找到 Team Foundation Server 區段](jenkins-walkthrough-images/image17.png)](jenkins-walkthrough-images/image17.png#lightbox)

輸入命令的完整路徑 `tf` ，然後按一下 [ **儲存** ] 按鈕。

### <a name="configure-jenkins-security"></a>設定 Jenkins 安全性

第一次安裝時，Jenkins 會停用安全性，讓任何使用者都能以匿名方式設定和執行任何種類的工作。 本節說明如何使用 Jenkins 使用者資料庫來設定驗證和授權，以設定安全性。

您可以藉由選取 **Jenkins > 管理 Jenkins > 設定全域安全性** 來找到安全性設定，如下列螢幕擷取畫面所示：

[![您可以藉由選取 [Jenkins]/[管理 Jenkins/設定全域安全性] 來找到安全性設定](jenkins-walkthrough-images/image18.png)](jenkins-walkthrough-images/image18.png#lightbox)

在 [ **設定全域安全性** ] 頁面上，核取 [ **啟用安全性** ] 核取方塊和 [ **存取控制** ] 表單應該會出現，類似于下一個螢幕擷取畫面：

[![在 [設定全域安全性] 頁面上，核取 [啟用安全性] 核取方塊和 [存取控制] 表單應該會出現，類似于下列螢幕擷取畫面](jenkins-walkthrough-images/image19.png)](jenkins-walkthrough-images/image19.png#lightbox)

在 [**安全性領域] 區段** 中，切換 **Jenkins [自己的使用者資料庫**] 的選項按鈕，並確定也已核取 [**允許使用者註冊**]，如下列螢幕擷取畫面所示：

[![在 [安全性領域] 區段中，切換 Jenkins 專屬使用者資料庫的選項按鈕，並確定也已核取 [允許使用者註冊]。](jenkins-walkthrough-images/image20.png)](jenkins-walkthrough-images/image20.png#lightbox)

最後，重新開機 Jenkins 並建立新的帳戶。 第一個建立的帳戶是根帳號，此帳戶將會自動升級為系統管理員。 流覽回到 [ **設定全域安全性** ] 頁面，並核取 [以 **矩陣為基礎的安全性** ] 選項按鈕。 根帳號應被授與完整存取權，而匿名帳戶應提供唯讀存取權，如下列螢幕擷取畫面所示：

[![根帳號應被授與完整存取權，而匿名帳戶應提供唯讀存取權](jenkins-walkthrough-images/image21.png)](jenkins-walkthrough-images/image21.png#lightbox)

一旦儲存這些設定並重新啟動 Jenkins，將會開啟安全性。

#### <a name="disabling-security"></a>停用安全性

如果忘記密碼或全 Jenkins 鎖定，可以遵循下列步驟來停用安全性：

1. 停止 Jenkins。 如果您使用 Jenkins，您可以用滑鼠右鍵按一下 Dock 中的 [Jenkins] 圖示，然後從出現的功能表中選取 [結束]：

    ![應用程式圖示在 Dock 中，然後從彈出的功能表中選取 [退出]。](jenkins-walkthrough-images/image19.png)

2. 在文字編輯器中開啟 file **~/.jenkins/config.xml** 。
3. 將元素的值 `<usesecurity></usesecurity>` 從變更 `true` 為 `false` 。
4. 刪除檔案 `<authorizationstrategy></authorizationstrategy>` 中的和 `<securityrealm></securityrealm>` 元素。
5. 重新啟動 Jenkins。

## <a name="setting-up-a-job"></a>設定作業

在最上層，Jenkins 會將建立軟體所需的各種工作組織成一個 *工作*。 作業也有與其相關聯的中繼資料，提供組建的相關資訊，例如如何取得原始程式碼、組建應該執行的頻率、建立任何必要的特殊變數，以及如何在組建失敗時通知開發人員。

若要建立工作，請從右上角的功能表中選取 [ **Jenkins > 新增工作** ]，如下列螢幕擷取畫面所示：

![作業的建立方式是從右上角的功能表中選取 [Jenkins 新增作業]。](jenkins-walkthrough-images/image22.png)

這會顯示 **新的工作 [Jenkins]** 頁面。 輸入作業的名稱，然後選取 [ **建立任意樣式的軟體專案** ] 選項按鈕。 下列螢幕擷取畫面顯示此範例：

![輸入作業的名稱，然後選取 [建立任意樣式的軟體專案] 選項按鈕。](jenkins-walkthrough-images/image23.png)

按一下 [ **確定]** 按鈕會顯示作業的設定頁面。 這應該類似下列螢幕擷取畫面：

![這應該類似此螢幕擷取畫面](jenkins-walkthrough-images/image24.png)

Jenkins 會將工作組織在位於下列路徑的硬碟目錄中： **~/.jenkins/jobs/[作業名稱]**

此資料夾包含工作專屬的所有檔案和構件，例如記錄檔、設定檔案，以及需要編譯的原始程式碼。

建立初始工作之後，必須使用下列一或多項設定：

- 必須指定原始程式碼管理系統。
- 必須將一個或多個 *組建動作* 加入至專案。 以下是建立應用程式所需的步驟或工作。
- 作業必須獲指派一個 *組建觸發* 程式，也就是一組指示 Jenkins 取得程式碼和建立最終專案的頻率。

### <a name="configuring-source-code-control"></a>設定原始程式碼控制

第一個工作 Jenkins 會從原始程式碼管理系統中取出原始程式碼。 Jenkins 支援現今可用的許多熱門原始程式碼管理系統。 本節涵蓋兩個熱門的系統（Git 和 Team Foundation Server）。 下列各節將更詳細地討論這些原始程式碼管理系統中的每一個。

#### <a name="using-git-for-source-code-control"></a>使用 Git 進行原始程式碼控制

如果您使用 TFS 進行原始程式碼控制，請 [略過](#using-tfs-for-source-code-management) 本節，並使用 tfs 繼續進行下一節。

Jenkins 支援現成的 Git –不需要額外的外掛程式。 若要使用 Git，請按一下 **git** 選項按鈕，然後輸入 git 存放庫的 URL，如下列螢幕擷取畫面所示：

![若要使用 Git，請按一下 Git 選項按鈕，然後輸入 Git 存放庫的 URL](jenkins-walkthrough-images/image25.png)

儲存變更之後，Git 設定就會完成。

#### <a name="using-tfs-for-source-code-management"></a>使用 TFS 進行原始程式碼管理

本節僅適用于 TFS 使用者。

按一下 [ **Team Foundation Server** ] 選項按鈕，[TFS 設定] 區段應該會出現，類似于下列螢幕擷取畫面所示：

![按一下 Team Foundation Server 的選項按鈕，[TFS 設定] 區段應該會出現](jenkins-walkthrough-images/image26.png)

提供 TFS 的必要資訊。 下列螢幕擷取畫面顯示已完成的表單範例：

![此螢幕擷取畫面顯示已完成表單的範例](jenkins-walkthrough-images/image27.png)

#### <a name="testing-the-source-code-control-configuration"></a>測試來源程式碼控制項設定

設定適當的原始程式碼控制項後，按一下 [ **儲存** ] 以儲存變更。 這會讓您回到工作的首頁，類似下列螢幕擷取畫面：

![這會讓您回到工作的首頁，這會類似此螢幕擷取畫面](jenkins-walkthrough-images/image28.png)

若要驗證是否已正確設定原始程式碼控制項，最簡單的方式就是手動觸發組建，即使沒有指定任何組建動作也是一樣。 若要手動啟動組建，作業的首頁在左側的功能表中會有 [ **立即建立** ] 連結，如下列螢幕擷取畫面所示：

![若要手動啟動組建，作業的首頁在左側的功能表中會有 [立即建立] 連結](jenkins-walkthrough-images/image29.png)

當組建開始時，[組建歷程記錄] 對話方塊會顯示閃爍的藍色圓圈、進度列、組建編號和組建開始的時間，類似于下列螢幕擷取畫面：

![當組建開始時，[組建歷程記錄] 對話方塊會顯示閃爍的藍色圓圈、進度列、組建編號和組建開始的時間](jenkins-walkthrough-images/image30.png)

如果作業成功，則會顯示藍色圓形。 如果作業失敗，則會顯示紅色圓圈。

為了協助疑難排解組建中可能產生的問題，Jenkins 將會取得作業的所有主控台輸出。 若要查看主控台輸出，請在 [組建歷程 **記錄**] 中按一下作業，然後在左側功能表的 [ **主控台輸出** ] 連結上按一下。 下列螢幕擷取畫面顯示 **主控台輸出** 連結，以及成功作業的部分輸出：

![此螢幕擷取畫面顯示主控台輸出連結，以及成功作業的部分輸出](jenkins-walkthrough-images/image31.png)

#### <a name="location-of-build-artifacts"></a>組建成品的位置

Jenkins 會將整個原始程式碼捕獲到稱為 *工作區* 的特殊資料夾中。 您可以在下列位置的資料夾中找到這個目錄：

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

工作區的路徑將會儲存在名為的環境變數中 `$WORKSPACE` 。

您可以流覽至作業的登陸頁面，然後按一下左側功能表中的 [ **工作區** ] 連結，以流覽 Jenkins 中的工作區資料夾。 下列螢幕擷取畫面顯示名為 **HelloWorld** 之作業的工作區範例：

![此螢幕擷取畫面顯示名為 HelloWorld 之工作的工作區範例](jenkins-walkthrough-images/image32.png)

### <a name="build-triggers"></a>組建觸發程式

有幾個不同的策略可在 Jenkins 中起始組建，這些都稱為「 *建立觸發* 程式」。 組建觸發程式可協助 Jenkins 決定何時啟動作業並建立專案。 有兩個常見的組建觸發程式：

- **定期建立** ：此觸發程式會導致 Jenkins 依指定的間隔啟動作業，例如每隔兩個小時，或在工作日午夜。 無論原始程式碼存放庫中是否有任何變更，組建都會啟動。
- **輪詢 SCM** –此觸發程式會定期輪詢原始程式碼控制。 如果任何變更已認可至原始程式碼存放庫，Jenkins 將會啟動新的組建。

輪詢 SCM 是常用的觸發程式，因為它會在開發人員認可導致組建中斷的變更時，提供快速的意見反應。 這適用于警示小組，某些最近認可的程式碼造成問題，並可讓開發人員在變更仍在重新整理時解決問題。

定期組建通常用來建立可散發給測試人員的應用程式版本。 例如，定期組建可能會排程在星期五晚上，讓 QA 小組的成員可以測試上周的工作。

### <a name="compiling-a-xamarinios-applications"></a>編譯 Xamarin iOS 應用程式
您可以使用或，在命令列中編譯 Xamarin. iOS 專案 `xbuild` `msbuild` 。 Shell 命令將會在執行 Jenkins 的使用者帳戶內容中執行。 使用者帳戶必須具有布建設定檔的存取權，才能正確封裝應用程式以供散發。 您可以將此 shell 命令新增至 [作業設定] 頁面。

向下滾動至 [ **組建** ] 區段。 按一下 [ **新增組建步驟** ] 按鈕並選取 [ **執行 shell**]，如下列螢幕擷取畫面所示：

![按一下 [新增組建步驟] 按鈕，然後選取 [執行 shell]](jenkins-walkthrough-images/image33.png)

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>建立 Xamarin. Android 專案

建立 xamarin. Android 專案在建立 Xamarin iOS 專案的概念上非常類似。 若要從 Xamarin. Android 專案建立 APK，Jenkins 必須設定為執行下列兩個步驟：

- 使用 MSBuild 外掛程式編譯專案
- Sign 和 zip 將 APK 與有效的版本金鑰儲存區一致。

接下來的兩節將會更詳細地討論這兩個步驟。

### <a name="creating-the-apk"></a>建立 APK

按一下 [ **新增組建步驟** ] 按鈕，然後選取 [ **使用 MSBuild 建立 Visual Studio 專案或方案**]，如下列螢幕擷取畫面所示：

![建立 APK 按一下 [新增組建步驟] 按鈕，然後選取 [使用 MSBuild 建立 Visual Studio 專案或方案]](jenkins-walkthrough-images/image36.png)

將組建步驟新增至專案之後，請填寫出現的表單欄位。 下列螢幕擷取畫面是完成表單的其中一個範例：

![將組建步驟新增至專案之後，請填入顯示的表單欄位](jenkins-walkthrough-images/image37.png)

此組建步驟將會 `xbuild` 在 **$WORKSPACE** 資料夾中執行。 MSBuild 組建檔案會設定為 **Xamarin** 檔案。 **命令列引數** 會指定目標 **PackageForAndroid** 的發行組建。 此步驟的產品將會是下列位置的 APK：

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

下列螢幕擷取畫面顯示此 APK 的範例：

![此螢幕擷取畫面顯示此 APK 的範例](jenkins-walkthrough-images/image38.png)

此 APK 尚未準備好進行部署，因為它尚未以私用金鑰儲存區簽署且必須保持一致。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>簽署和 Zipalign 發行的 APK

簽署和 zipalign APK 在技術上是兩個不同的工作，這些工作是由 Android SDK 的兩個不同命令列工具所執行。 不過，在一個組建動作中執行它們是很方便的。 如需有關簽署和 zipalign APK 的詳細資訊，請參閱 Xamarin 的檔，以瞭解如何準備 Android 應用程式以進行發行。

這兩個命令都需要命令列參數，這些參數可能會因專案而異。 此外，其中有些命令列參數是在組建執行時，不應該出現在主控台輸出中的密碼。 我們會將其中一些命令列參數儲存在環境變數中。 下表說明簽署和/或壓縮對齊所需的環境變數：

|環境變數|描述|
|--- |--- |
|KEYSTORE_FILE|這是用來簽署 APK 的金鑰存放區的路徑|
|KEYSTORE_ALIAS|金鑰儲存區中將用來簽署 APK 的金鑰。|
|INPUT_APK|所建立的 APK `xbuild` 。|
|SIGNED_APK|所產生的已簽署 APK `jarsigner` 。|
|FINAL_APK|這是所產生的 zip 對齊 APK `zipalign` 。|
|STORE_PASS|這是用來存取唱歌檔案之金鑰儲存區內容的密碼。|

如需求一節中所述，您可以使用 EnvInject 外掛程式在組建期間設定這些環境變數。 作業應該會根據插入環境變數新增一個新的組建步驟，如下一個螢幕擷取畫面所示：

![作業應根據插入環境變數新增新的組建步驟](jenkins-walkthrough-images/image39.png)

在出現的 [ **屬性內容** 表單] 欄位中，會新增環境變數（每行一個），格式如下：

```
ENVIRONMENT_VARIABLE_NAME = value
```

下列螢幕擷取畫面顯示簽署 APK 所需的環境變數：

![此螢幕擷取畫面顯示簽署 APK 所需的環境變數](jenkins-walkthrough-images/image40.png)

請注意，APK 檔案的部分環境變數是以環境變數為基礎 `WORKSPACE` 。

最終環境變數是用來存取金鑰儲存區內容的密碼： `STORE_PASS` 。 密碼是應該在記錄檔中遮蔽或省略的機密值。 您可以設定 EnvInject 外掛程式來保護這些值，使其不會顯示在記錄中。

在作業設定的 [ **組建** ] 區段之前，緊接在 [ **組建環境** ] 區段之前。 切換 [ **插入密碼** ] 核取方塊時，會出現一些表單欄位。 這些表單欄位可用來捕捉環境變數的名稱和值。 下列螢幕擷取畫面是新增環境變數的範例 `STORE_PASS` ：

![這個螢幕擷取畫面是新增 STOREPASS 環境變數的範例](jenkins-walkthrough-images/image41.png)

一旦環境變數已初始化，下一個步驟就是新增簽署的組建步驟，並將 APK 的 zip 對齊。 緊接在插入環境變數的組建步驟之後，將會執行和的另一個 **執行 shell** 命令 `jarsigner` 組建 `zipalign` 。 每個命令會佔用一行，如下列程式碼片段所示：

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

下列螢幕擷取畫面顯示如何在 `jarsigner` 步驟中輸入和命令的範例 `zipalign` ：

![此螢幕擷取畫面顯示如何在步驟中輸入 jarsigner 和 zipalign 命令的範例](jenkins-walkthrough-images/image42.png)

一旦所有的組建動作都已就緒，就可以觸發手動組建來確認一切運作正常。 如果組建失敗，則應檢查 **主控台輸出** ，以取得導致組建失敗的原因相關資訊。

### <a name="submitting-tests-to-test-cloud"></a>將測試提交至測試雲端

您可以使用 shell 命令將自動化測試提交到測試雲端。 如需在 Xamarin Test Cloud 中設定測試回合的詳細資訊，請參閱 [準備 xamarin. Android 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) 和 [準備 Xamarin iOS 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)。

## <a name="summary"></a>[摘要]

在本指南中，我們引進了 Jenkins 做為 macOS 上的組建伺服器，並將其設定為可編譯和準備 Xamarin 行動應用程式以供發行。 我們已在 macOS 電腦上安裝 Jenkins，以及數個外掛程式來支援組建程式。 我們已建立並設定將從 TFS 或 Git 提取程式碼的作業，然後將該程式碼編譯成發行就緒的應用程式。 我們也探討了兩種不同的方式來排程應執行工作的時間。

## <a name="related-links"></a>相關連結

- [持續整合](~/tools/ci/index.md)
- [App Center 測試](/appcenter/test-cloud/)