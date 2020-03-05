---
title: 搭配 Xamarin 使用 Jenkins
description: 本檔說明如何使用 Jenkins 來與 Xamarin 應用程式進行持續整合。 它會討論如何安裝、設定及使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 4f91e683b826657a9740de7e0b98137858130042
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292270"
---
# <a name="using-jenkins-with-xamarin"></a>搭配 Xamarin 使用 Jenkins

_本指南說明如何將 Jenkins 設定為持續整合伺服器，並自動編譯以 Xamarin 建立的行動應用程式。其中說明如何在 OS X 上安裝 Jenkins、設定它，以及在將變更認可至原始程式碼管理系統時，設定作業來編譯 Xamarin 和 Xamarin 應用程式。_

[與 Xamarin 的持續整合簡介](~/tools/ci/intro-to-ci.md)引進了持續整合做為實用的軟體發展實務，以提供較早的程式碼中斷或不相容的警告。 CI 可讓開發人員解決發生的問題，並讓軟體處於適當的部署狀態。 本逐步解說涵蓋如何同時使用這兩個檔的內容。

本指南說明如何在執行 OS X 的專用電腦上安裝 Jenkins，並將它設定為在電腦啟動時自動執行。 安裝 Jenkins 之後，我們會安裝其他外掛程式以支援 MS Build。 Jenkins 支援現成的 Git。 如果 TFS 用於原始碼控制，則也必須安裝額外的外掛程式和命令列公用程式。

設定 Jenkins 並安裝任何必要的外掛程式之後，我們會建立一或多個作業來編譯 Xamarin. Android 和 Xamarin 專案。 作業是執行某些工作所需的步驟和中繼資料的集合。 作業通常包含下列各項：

- **原始程式碼管理（SCM）** –這是 Jenkins 設定檔中的中繼資料專案，其中包含如何連接至原始程式碼控制以及要抓取哪些檔案的資訊。
- **觸發**程式–用來根據特定動作啟動作業的觸發程式，例如當開發人員認可原始程式碼存放庫的變更時。
- **組建指示**–這是外掛程式或腳本，它會編譯原始程式碼並產生可安裝在行動裝置上的二進位檔。
- **選擇性的組建動作**–這可能包括執行單元測試、對程式碼執行靜態分析、簽署程式碼，或啟動另一個作業來執行其他組建相關工作。
- **通知**–作業可能會傳送有關組建狀態的某種通知。
- **安全性**–雖然是選擇性的，但強烈建議您也要啟用 Jenkins 安全性功能。

本指南將逐步解說如何設定涵蓋每個重點的 Jenkins 伺服器。 最後，我們應該已充分瞭解如何設定和設定 Jenkins，以建立適用于 Xamarin 行動專案的 .IPA 和 APK。

## <a name="requirements"></a>需求

理想的組建伺服器是獨立的電腦，專門用來建立並可能測試應用程式。 專用電腦可確保其他角色（例如 web 伺服器）可能需要的成品不會污染組建。 例如，如果組建伺服器也做為 web 伺服器，則網頁伺服器可能需要某些通用程式庫的衝突版本。 因為這項衝突，web 伺服器可能無法正常運作，或 Jenkins 可能會建立在部署至使用者時無法運作的組建。

Xamarin 行動應用程式的組建伺服器設定起來非常類似開發人員的工作站。 它有一個使用者帳戶，其中會安裝 Jenkins、Visual Studio for Mac 和 Xamarin. iOS 和 Xamarin. Android。 所有的程式碼簽署憑證、布建設定檔和金鑰存放區也必須一併安裝。 *通常，組建伺服器的使用者帳戶會與您的開發人員帳戶分開-請務必在使用組建伺服器使用者帳戶登入時，安裝及設定所有軟體、金鑰和憑證。*

下圖說明一般 Jenkins 組建伺服器上的所有元素：

[![](jenkins-walkthrough-images/image1.png "This diagram illustrates all of these elements on a typical Jenkins build server")](jenkins-walkthrough-images/image1.png#lightbox)

iOS 應用程式只能在執行 macOS 的電腦上建立和簽署。 Mac 迷你是合理的低成本選項，但任何能夠執行 OS X 10.10 （Yosemite）或更高版本的電腦都已足夠。

如果 TFS 用於原始碼控制，您會想要安裝[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)。 Team Explorer Everywhere 在 macOS 的終端機上提供對 TFS 的跨平臺存取。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安裝 Jenkins

使用 Jenkins 的第一個工作是安裝它。 有三種方式可以在 OS X 上執行 Jenkins：

- 作為守護程式，在背景中執行。
- 在 servlet 容器內（例如 Tomcat、Jetty 或 JBoss）。
- 做為在使用者帳戶下執行的一般進程。

大部分傳統的持續整合應用程式都是在背景執行，可能是做為 daemon （在 OS X 或 \*nix 上）或做為服務（在 Windows 上）。 這適用于不需要 GUI 互動的情況，而且可以輕鬆地執行組建環境的設定。 行動應用程式也需要金鑰庫和簽署憑證，而當 Jenkins 做為 daemon 執行時，可能會有問題的存取權。 基於這些考慮，這份檔著重于第三個案例–在組建伺服器上的使用者帳戶下執行 Jenkins。

Jenkins 應用程式是安裝 Jenkins 的便利方式。 這是 AppleScript 的包裝函式，可簡化 Jenkins 伺服器的啟動和停止。 Jenkins 會在 Dock 中以具有圖示的應用程式執行，而不是在 bash shell 中執行，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image2.png "Instead of running in a bash shell, Jenkins runs as an app with icon in the Dock, as shown in this screenshot")](jenkins-walkthrough-images/image2.png#lightbox)

啟動或停止 Jenkins 就像啟動或停止 Jenkins 一樣簡單。

若要安裝 Jenkins，請從專案的下載頁面下載最新版本，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image3.png "App, download the latest version from the projects download page, pictured in this screenshot")](jenkins-walkthrough-images/image3.png#lightbox)

將 zip 檔案解壓縮至組建伺服器上的 `/Applications` 資料夾，並啟動它，就像任何其他 OS X 應用程式一樣。
當您第一次啟動 Jenkins 時，它會顯示一個對話方塊，通知您它會下載 Jenkins：

[![](jenkins-walkthrough-images/image4.png "App, it will present a dialog informing you that it will download Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Jenkins 完成下載之後，它會顯示另一個對話方塊，詢問您是否要自訂 Jenkins 啟動，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image5.png "App has finished its download, it will display another dialog asking you if you would like to customize the Jenkins startup, as seen in this screenshot")](jenkins-walkthrough-images/image5.png#lightbox)

自訂 Jenkins 是選擇性的，而且不需要在每次啟動應用程式時執行– Jenkins 的預設設定適用于大部分的情況。

如果需要自訂 Jenkins，請按一下 [**變更預設值**] 按鈕。 這會顯示兩個連續的對話方塊：一個要求使用 JAVA 命令列參數，另一個則要求 Jenkins 命令列參數。 以下兩個螢幕擷取畫面顯示這兩個對話方塊：

[![](jenkins-walkthrough-images/image6.png "This screenshot shows the dialogs")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "This screenshot shows the dialogs")](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 執行之後，您可能會想要將它設定為登入專案，以便在每次使用者登入電腦時啟動。 您可以用滑鼠右鍵按一下 [Dock] 中的 Jenkins 圖示，然後選擇 [選項 ...]，來執行此動作。 **> 在登入時開啟**，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image8.png "You can do this by right-clicking on the Jenkins icon in the Dock and choosing OptionsOpen at Login, as shown in this screenshot")](jenkins-walkthrough-images/image8.png#lightbox)

這會導致 Jenkins 在使用者每次登入時自動啟動，但不會在電腦開機時發生。 您可以指定 OS X 會在開機時用來自動登入的使用者帳戶。 開啟 [**系統偏好**設定]，然後選取 [**使用者 & 群組**] 圖示，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image9.png "Open the System Preferences, and select the User  Groups icon as shown in this screenshot")](jenkins-walkthrough-images/image9.png#lightbox)

按一下 [**登入選項**] 按鈕，然後選擇 OS X 在開機時將用來登入的帳戶。

此時，已安裝 Jenkins。 不過，如果我們想要建立 Xamarin 行動應用程式，就必須安裝一些外掛程式。

### <a name="installing-plugins"></a>安裝外掛程式

當 Jenkins 安裝程式完成時，它會開始 Jenkins，並以 http://localhost:8080的 URL 啟動網頁瀏覽器，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image10.png "8080, as shown in this screenshot")](jenkins-walkthrough-images/image10.png#lightbox)

在此頁面中，從左上角的功能表中選取 [ **Jenkins] > [管理 Jenkins] > [管理外掛程式**]，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image11.png "From this page, select Jenkins  Manage Jenkins  Manage Plugins from the menu in the upper left hand corner")](jenkins-walkthrough-images/image11.png#lightbox)

這會顯示 [ **Jenkins 外掛程式管理員**] 頁面。 如果您按一下 [可用] 索引標籤，您會看到可供下載和安裝的超過600個外掛程式清單。 這會在下列螢幕擷取畫面中說明：

[![](jenkins-walkthrough-images/image12.png "If you click on the Available tab, you will see a list of over 600 plugins that can be downloaded and installed")](jenkins-walkthrough-images/image12.png#lightbox)

所有600外掛程式的捲軸若要找出幾個，可能既繁瑣又容易出錯。 Jenkins 會在介面的右上角提供篩選搜尋欄位。 使用此篩選欄位來進行搜尋，將可簡化尋找並安裝下列其中一個或所有外掛程式的程式：

- **Jenkins MSBuild 外掛程式**–此外掛程式可讓您建立 Visual Studio 和 Visual Studio for Mac 方案（.sln）和專案（.csproj）。
- **環境 Injector 外掛程式**–這是選擇性但有用的外掛程式，可讓您在作業和組建層級設定環境變數。 它也為變數提供額外的保護，例如用來程式碼簽署應用程式的密碼。 它有時會縮寫為*EnvInject 外掛程式*。
- **Team Foundation Server 外掛程式**–這是選擇性外掛程式，只有在您使用 Team Foundation Server 或 Team Foundation service 進行原始程式碼控制時才需要。

Jenkins 支援 Git，而不需要任何額外的外掛程式。

安裝所有外掛程式之後，您會想要重新開機 Jenkins，並設定每個外掛程式的全域設定。 從左上角選取 [ **Jenkins] > [管理 Jenkins] > [設定系統**]，即可找到外掛程式的全域設定，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image13.png "The global settings for a plugin can be found by selecting Jenkins / Manage Jenkins / Configure System from the upper left hand corner")](jenkins-walkthrough-images/image13.png#lightbox)

當您選取此功能表選項時，您將會進入 **[設定系統 [Jenkins]]** 頁面。 此頁面包含的區段可設定 Jenkins 本身，以及設定部分全域外掛程式值。  下列螢幕擷取畫面說明此頁面的範例：

[![](jenkins-walkthrough-images/image14.png "This screenshot illustrates an example of this page")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>設定 MSBuild 外掛程式

MSBuild 外掛程式必須設定為使用 **/Library/Frameworks/Mono.framework/Commands/xbuild**來編譯 Visual Studio for Mac 方案和專案檔案。 在 [**新增 MSBuild** ] 按鈕出現之前，請向下滾動 **[設定系統 [Jenkins]]** 頁面，如下列螢幕擷取畫面所示：

 [![](jenkins-walkthrough-images/image15.png "Scroll down the Configure System Jenkins page until the Add MSBuild button appears")](jenkins-walkthrough-images/image15.png#lightbox)

按一下此按鈕，並在出現的表單上填入**MSBuild**欄位的**名稱**和**路徑**。 **Msbuild**安裝的名稱應該有意義，而**msbuild 的路徑**應該是 `xbuild`的路徑，通常是 **/Library/Frameworks/Mono.framework/Commands/xbuild**。 在我們按一下頁面底部的 [儲存] 或 [套用] 按鈕來儲存變更之後，Jenkins 就可以使用 `xbuild` 來編譯您的解決方案。

#### <a name="configuring-the-tfs-plugin"></a>設定 TFS 外掛程式

如果您想要將 TFS 用於原始程式碼控制項，則此區段是必要的。

為了讓 macOS 工作站能夠與 TFS 伺服器互動， [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)必須安裝在工作站上。 Team Explorer Everywhere 是 Microsoft 提供的一組工具，其中包含用於存取 TFS 的跨平臺命令列用戶端。 Team Explorer Everywhere 可以從 Microsoft 下載並以三個步驟安裝：

1. 將封存檔案解壓縮到使用者帳戶可存取的目錄。 例如，您可能會將檔案解壓縮至 **~/tee**。
2. 設定 shell 或系統路徑，使其包含在上述步驟一中解壓縮的檔案資料夾。 例如，

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. 若要確認是否已安裝 Team Explorer Everywhere，請開啟終端機會話，然後執行 `tf` 命令。 如果已正確設定 tf，您會在終端機會話中看到下列輸出：

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

安裝 TFS 的命令列用戶端之後，必須使用 `tf` 命令列用戶端的完整路徑來設定 Jenkins。 在 **[設定系統 [Jenkins]]** 頁面上向下流覽，直到您找到 [Team Foundation Server] 區段，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image17.png "Scroll down the Configure System Jenkins page until you find the Team Foundation Server section")](jenkins-walkthrough-images/image17.png#lightbox)

輸入 `tf` 命令的完整路徑，然後按一下 [**儲存**] 按鈕。

### <a name="configure-jenkins-security"></a>設定 Jenkins 安全性

當第一次安裝時，Jenkins 會停用安全性，因此任何使用者都可以匿名設定和執行任何一種作業。 本節涵蓋如何使用 Jenkins 使用者資料庫設定安全性，以設定驗證和授權。

若要找到安全性設定，請選取 [ **Jenkins] > [管理 Jenkins > 設定全域安全性**]，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image18.png "Security settings can be found by selecting Jenkins / Manage Jenkins / Configure Global Security")](jenkins-walkthrough-images/image18.png#lightbox)

在 [**設定全域安全性**] 頁面上，勾選 [**啟用安全性**] 核取方塊，[**存取控制**] 表單應該會出現，類似下一個螢幕擷取畫面：

[![](jenkins-walkthrough-images/image19.png "On the Configure Global Security page, check the Enable Security checkbox and the Access Control form should appear, similar to this screenshot")](jenkins-walkthrough-images/image19.png#lightbox)

在 [**安全性領域] 區段**中，切換**Jenkins [擁有的使用者資料庫**] 的選項按鈕，並確定也已核取 [**允許使用者註冊**]，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image20.png "Toggle the radio button for Jenkins own user database in the Security Realm Section, and ensure that Allow users to sign up is also checked")](jenkins-walkthrough-images/image20.png#lightbox)

最後，重新開機 Jenkins，並建立新的帳戶。 第一個建立的帳戶是根帳號，而此帳戶會自動升級為系統管理員。 流覽回 [**設定全域安全性**] 頁面，然後勾選 [**矩陣型安全性**] 選項按鈕。 根帳號應被授與完整存取權，且匿名帳戶應獲得唯讀存取權，如下列螢幕擷取畫面所示：

[![](jenkins-walkthrough-images/image21.png "The root account should be granted full access, and the anonymous account should be given read-only access")](jenkins-walkthrough-images/image21.png#lightbox)

儲存這些設定並重新啟動 Jenkins 之後，安全性就會開啟。

#### <a name="disabling-security"></a>停用安全性

如果忘記密碼或全 Jenkins 鎖定，您可以遵循下列步驟來停用安全性：

1. 停止 Jenkins。 如果您使用 Jenkins，可以用滑鼠右鍵按一下 Dock 中的 Jenkins 圖示，然後從快顯的功能表中選取 [Quit]，來執行此動作：

    ![停駐中的應用程式圖示，並從彈出的功能表中選取 [結束]](jenkins-walkthrough-images/image19.png)

2. 在文字編輯器中開啟檔案 **~/.jenkins/config.xml** 。
3. 將 `<usesecurity></usesecurity>` 元素的值從 `true` 變更為 `false`。
4. 從檔案中刪除 `<authorizationstrategy></authorizationstrategy>` 和 `<securityrealm></securityrealm>` 元素。
5. 重新啟動 Jenkins。

## <a name="setting-up-a-job"></a>設定作業

在最上層，Jenkins 會組織將軟體建立到*作業*所需的各種工作。 作業也有相關聯的中繼資料，提供有關組建的資訊，例如如何取得原始程式碼、組建應執行的頻率、建立所需的任何特殊變數，以及如何在組建失敗時通知開發人員。

建立作業的方式是從右上角的功能表中選取 [ **Jenkins] > [新增作業**]，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image22.png "Jobs are created by selecting Jenkins  New Job from the menu in the upper right hand corner")

這會顯示 **[新增作業 [Jenkins]]** 頁面。 輸入作業的名稱，然後選取 [**建立免費樣式的軟體專案**] 選項按鈕。 下列螢幕擷取畫面顯示這種情況的範例：

![](jenkins-walkthrough-images/image23.png "Enter a name for the job, and select the Build a free-style software project radio button")

按一下 [**確定]** 按鈕會顯示作業的 [設定] 頁面。 這應該類似下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image24.png "This should resemble this screenshot")

Jenkins 會在位於下列路徑的硬碟目錄中組織工作： **~/.jenkins/jobs/[作業名稱]**

此資料夾包含作業特定的所有檔案和成品，例如記錄、設定檔和需要編譯的原始程式碼。

建立初始作業之後，必須使用下列一或多項設定：

- 必須指定原始程式碼管理系統。
- 必須將一或多個*組建動作*加入至專案。 這些是建立應用程式所需的步驟或工作。
- 作業必須指派一個*組建觸發*程式–一組指示會通知 Jenkins 取得程式碼的頻率，並建立最終專案。

### <a name="configuring-source-code-control"></a>設定原始程式碼控制

第一個工作 Jenkins 會從原始程式碼管理系統中取出原始程式碼。 Jenkins 支援現今提供的許多熱門原始程式碼管理系統。 本節涵蓋兩個熱門的系統： Git 和 Team Foundation Server。 下列各節將更詳細地討論每個原始程式碼管理系統。

#### <a name="using-git-for-source-code-control"></a>使用 Git 進行原始程式碼控制

如果您使用 TFS 來進行原始程式碼控制，請[略過](#using-tfs-for-source-code-management)本節，然後使用 tfs 繼續進行下一節。

Jenkins 支援現成的 Git –不需要額外的外掛程式。 若要使用 Git，請按一下 [ **git** ] 選項按鈕，然後輸入 git 存放庫的 URL，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image25.png "To use Git, click on the Git radio button and enter the URL for the Git repository")

儲存變更之後，Git 設定就會完成。

#### <a name="using-tfs-for-source-code-management"></a>使用 TFS 進行原始碼管理

本節僅適用于 TFS 使用者。

按一下 [ **Team Foundation Server** ] 選項按鈕，[TFS 設定] 區段應該會出現，類似下列螢幕擷取畫面中的內容：

![](jenkins-walkthrough-images/image26.png "Click on the Team Foundation Server radio button and the TFS configuration section should appear")

提供 TFS 所需的資訊。 下列螢幕擷取畫面顯示已完成表單的範例：

![](jenkins-walkthrough-images/image27.png "This screenshot shows an example of the completed form")

#### <a name="testing-the-source-code-control-configuration"></a>測試原始程式碼控制設定

設定適當的原始程式碼控制項之後，請按一下 [**儲存**] 以儲存變更。 這會讓您回到作業的首頁，這看起來會類似下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image28.png "This will return you to the home page for the job, which will resemble this screenshot")

驗證原始程式碼控制項正確設定的最簡單方式，就是手動觸發組建，即使未指定任何組建動作也一樣。 若要手動啟動組建，作業的首頁在左側的功能表中有 [**立即建立**] 連結，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image29.png "To start a build manually, the home page of the job has a Build Now link in the menu on the left hand side")

當組建已啟動時，[組建歷程記錄] 對話方塊會顯示閃爍的藍色圓形、進度列、組建編號和組建開始的時間，類似下列螢幕擷取畫面：

![](jenkins-walkthrough-images/image30.png "When a build has been started, the Build History dialog displays a flashing blue circle, a progress bar, the build number and the time that the build started")

如果作業成功，將會顯示藍色圓圈。 如果作業失敗，則會顯示紅色圓圈。

為了協助疑難排解可能會在組建中產生的問題，Jenkins 將會捕獲作業的所有主控台輸出。 若要查看主控台輸出，請按一下 [組建歷程**記錄**] 中的作業，然後在左側功能表中的 [**主控台輸出**] 連結上。 下列螢幕擷取畫面顯示**主控台輸出**連結，以及成功作業的部分輸出：

![](jenkins-walkthrough-images/image31.png "This screenshot shows the Console Output link, as well as some of the output from a successful job")

#### <a name="location-of-build-artifacts"></a>組建成品的位置

Jenkins 會將整個原始程式碼捕獲到稱為*工作區*的特殊資料夾。 您可以在資料夾中的下列位置找到此目錄：

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

工作區的路徑將會儲存在名為 `$WORKSPACE`的環境變數中。

流覽至作業的登陸頁面，然後按一下左側功能表中的 [**工作區**] 連結，就可以在 Jenkins 中流覽工作區資料夾。 下列螢幕擷取畫面顯示名為**HelloWorld**之作業的工作區範例：

![](jenkins-walkthrough-images/image32.png "This screenshot shows an example of the workspace for a job named HelloWorld")

### <a name="build-triggers"></a>組建觸發程式

有幾個不同的策略可在 Jenkins 中起始組建，這些是所謂的*組建觸發*程式。 組建觸發程式可協助 Jenkins 決定何時啟動作業並建立專案。 有兩個常見的組建觸發程式：

- **定期建立**–此觸發程式會導致 Jenkins 在指定的間隔啟動作業，例如每隔兩個小時或在工作日午夜。 不論原始程式碼存放庫中是否有任何變更，組建都會啟動。
- **輪詢 SCM** –此觸發程式會定期輪詢原始程式碼控制。 如果原始程式碼存放庫已認可任何變更，Jenkins 就會啟動新的組建。

輪詢 SCM 是常用的觸發程式，因為當開發人員認可導致組建中斷的變更時，它會提供快速的意見反應。 這對於警示小組而言，這適用于某些最近認可的程式碼會造成問題，並可讓開發人員解決問題，而變更仍然是最新的。

定期組建通常用來建立可散發給測試人員的應用程式版本。 例如，定期組建可能會排程在星期五晚上，讓 QA 小組成員可以測試上一周的工作。

### <a name="compiling-a-xamarinios-applications"></a>編譯 Xamarin iOS 應用程式
您可以使用 `xbuild` 或 `msbuild`，在命令列上編譯 Xamarin 專案。 Shell 命令會在執行 Jenkins 的使用者帳戶內容中執行。 使用者帳戶必須具有布建設定檔的存取權，才能正確封裝應用程式以供散發。 您可以將此 shell 命令新增至 [作業設定] 頁面。

向下流覽至 [**組建**] 區段。 按一下 [**新增組建步驟**] 按鈕，然後選取 [**執行命令**介面]，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image33.png "Click the Add build step button and select Execute shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>建立 Xamarin Android 專案

建立 xamarin. Android 專案在概念上非常類似于建立 Xamarin iOS 專案。 若要從 Xamarin 專案建立 APK，必須將 Jenkins 設定為執行下列兩個步驟：

- 使用 MSBuild 外掛程式編譯專案
- Sign 和 zip 會將 APK 與有效的版本金鑰存放區對齊。

這兩個步驟將在接下來的兩節中更詳細地討論。

### <a name="creating-the-apk"></a>建立 APK

按一下 [**新增組建步驟**] 按鈕，然後選取 [**使用 MSBuild 建立 Visual Studio 專案或方案**]，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image36.png "Creating the APK  Click on the Add build step button, and select Build a Visual Studio project or solution using MSBuild")

將組建步驟加入至專案之後，請填寫出現的表單欄位。 下列螢幕擷取畫面是已完成表單的其中一個範例：

![](jenkins-walkthrough-images/image37.png "Once the build step is added to the project, fill in the form fields that appear")

此組建步驟會在 **$WORKSPACE**資料夾中執行 `xbuild`。 MSBuild 組建檔案會設定為 [ **Xamarin** ] 檔案。 **命令列引數**會指定目標**PackageForAndroid**的發行組建。 此步驟的產品將是位於下列位置的 APK：

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

下列螢幕擷取畫面顯示此 APK 的範例：

![](jenkins-walkthrough-images/image38.png "This screenshot shows an example of this APK")

此 APK 尚未準備好進行部署，因為它尚未以私人金鑰儲存區簽署，而且必須以 zip 對齊。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>簽署和 Zipalign 發行的 APK

簽署和 zipalign APK 在技術上是兩個不同的工作，由 Android SDK 中的兩個不同命令列工具所執行。 不過，在一個組建動作中執行它們會很方便。 如需有關簽署和 zipalign APK 的詳細資訊，請參閱關於準備 Android 應用程式以進行發行的 Xamarin 檔。

這兩個命令都需要命令列參數，可能會因專案而異。 此外，其中有些命令列參數是在組建執行時，不應該出現在主控台輸出中的密碼。 我們會將其中一些命令列參數儲存在環境變數中。 簽署和/或 zip 對齊所需的環境變數，如下表所述：

|環境變數|描述|
|--- |--- |
|KEYSTORE_FILE|這是用來簽署 APK 的金鑰儲存區路徑|
|KEYSTORE_ALIAS|金鑰儲存區中的索引鍵，將用來簽署 APK。|
|INPUT_APK|`xbuild`所建立的 APK。|
|SIGNED_APK|`jarsigner`所產生的帶正負號的 APK。|
|FINAL_APK|這是 `zipalign`所產生的 zip 對齊 APK。|
|STORE_PASS|這是用來存取金鑰儲存區內容以唱歌檔案的密碼。|

如需求一節所述，您可以使用 EnvInject 外掛程式，在組建期間設定這些環境變數。 作業應該根據插入環境變數加入新的組建步驟，如下列螢幕擷取畫面所示：

![](jenkins-walkthrough-images/image39.png "The job should have a new build step added based on the Inject environment variables")

在顯示的 [**屬性內容**] 表單欄位中，會加入環境變數（每行一個），其格式如下：

```
ENVIRONMENT_VARIABLE_NAME = value
```

下列螢幕擷取畫面顯示簽署 APK 所需的環境變數：

![](jenkins-walkthrough-images/image40.png "This screenshot shows the environment variables that are required for signing the APK")

請注意，APK 檔案的某些環境變數是建置於 `WORKSPACE` 環境變數上。

最後的環境變數是用來存取金鑰儲存區內容的密碼： `STORE_PASS`。 密碼是應該在記錄檔中遮蔽或省略的敏感性值。 您可以設定 EnvInject 外掛程式來保護這些值，使其不會顯示在記錄中。

[作業設定] 的 [**組建**] 區段前面緊接著 [**組建環境**] 區段。 切換 [**插入密碼**] 核取方塊時，會顯示某些表單欄位。 這些表單欄位是用來捕捉環境變數的名稱和值。 下列螢幕擷取畫面是新增 `STORE_PASS` 環境變數的範例：

![](jenkins-walkthrough-images/image41.png "This screenshot is an example of adding the STOREPASS environment variable")

在初始化環境變數之後，下一個步驟是新增用於簽署和 zip 對齊 APK 的組建步驟。 緊接在要插入環境變數的組建步驟之後，將會是另一個**執行 shell**命令組建，會執行 `jarsigner` 並 `zipalign`。 每個命令都會佔用一行，如下列程式碼片段所示：

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

下列螢幕擷取畫面顯示如何在步驟中輸入 `jarsigner` 和 `zipalign` 命令的範例：

![](jenkins-walkthrough-images/image42.png "This screenshot shows an example of how to enter the jarsigner and zipalign commands into the step")

當所有組建動作都已就緒之後，建議您觸發手動組建以確認一切運作正常。 如果組建失敗，應檢查**主控台輸出**，以取得導致組建失敗之原因的相關資訊。

### <a name="submitting-tests-to-test-cloud"></a>將測試提交至測試雲端

您可以使用 shell 命令將自動化測試提交至測試雲端。 如需在 Xamarin Test Cloud 中設定測試回合的詳細資訊，請參閱[準備 Xamarin Android 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)和[準備 xamarin 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)。

## <a name="summary"></a>摘要

在本指南中，我們在 macOS 上引進了 Jenkins 做為組建伺服器，並將其設定為編譯和準備要發行的 Xamarin 行動應用程式。 我們已在 macOS 電腦上安裝 Jenkins，並在其中包含數個外掛程式以支援組建程式。 我們已建立並設定將從 TFS 或 Git 提取程式碼的作業，然後將該程式碼編譯成發行準備就緒的應用程式。 我們也探討了兩種不同的方式來排程應執行作業的時間。

## <a name="related-links"></a>相關連結

- [持續整合](~/tools/ci/index.md)
- [App Center 測試](https://docs.microsoft.com/appcenter/test-cloud/)
