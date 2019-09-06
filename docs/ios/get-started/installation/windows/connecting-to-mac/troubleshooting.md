---
title: Xamarin.iOS 組建主機的連線疑難排解
description: 本指南提供使用新連線管理員時可能遇到之問題 (包括連線能力和 SSH 問題) 的疑難排解步驟。
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 8a6cdd5ccec0d2c60eba18418327fd786201f11b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285637"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Xamarin.iOS 組建主機的連線疑難排解

_本指南提供使用新連線管理員時可能遇到之問題 (包括連線能力和 SSH 問題) 的疑難排解步驟。_

## <a name="log-file-location"></a>記錄檔位置

- **Mac** – ~/Library/Logs/Xamarin-[MAJOR.MINOR]
- **Windows** – %LOCALAPPDATA%\Xamarin\Logs

您可以在 Visual Studio 中瀏覽至 [說明] &gt; [Xamarin] &gt; [Zip 記錄檔] 來找出記錄檔。


## <a name="wheres-the-xamarin-build-host-app"></a>Xamarin 組建主機應用程式在哪裡？

目前已不再需要來自舊版 Xamarin.iOS 的「Xamarin 組建主機」。 Visual Studio 現在會自動透過「遠端登入」部署代理程式，並在背景中執行它。 沒有任何額外的應用程式將在 Mac 或 Windows 電腦上執行。


## <a name="troubleshooting-remote-login"></a>針對遠端登入進行疑難排解

> [!IMPORTANT]
> 這些疑難排解步驟主要是針對在新系統初始設定時發生的問題。  若您先前曾經在特定環境中使用連線，但連線突然或間歇性停止運作，您可以 (在大部分的情況下) 跳過並直接檢查下列資訊是否有幫助： 
> - 刪除剩餘的處理序，如下面的[由於現有建置主機處理序所發生的錯誤](#errors)。 
> - 依照[清除訊息代理程式、IDB、建置與設計代理程式](#clearing)所述清除代理程式，然後使用有線網際網路連線並根據[無法連線到 MacBuildHost.local。請重試。](#tryagain)所述透過 IP 位址直接連線。  
> 如果那些選項都沒有幫助，則請依照[步驟 9](#stepnine) 中的指示，提出新的錯誤 (Bug) 報告。

1. 確認您的 Mac 上已安裝相容的 Xamarin.iOS 版本。 若要使用 Visual Studio 2017 來執行此操作，請確定您位於 Visual Studio for Mac 中的**穩定**散發通道上。 在 Visual Studio 2015 和更早的版本中，請確定您在兩個 IDE 上都是位於相同的散發通道上。
    - 在 Visual Studio for Mac 中，移至 [Visual Studio for Mac] > [檢查更新] 以檢視或變更 [更新通道]。
    - 在 Visual Studio 2015 和更早的版本中，請在 [工具] > [選項] > [Xamarin] > [其他] 底下查看散發通道。

2. 確定 Mac 上已啟用 [遠端登入]。 為 [僅以下使用者] 設定存取權，然後確定群組清單中包含您的 Mac 使用者：

    [![](troubleshooting-images/troubleshooting-image1.png "為 [僅以下使用者] 設定存取權")](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. 確認您的防火牆允許透過連接埠 22 (SSH 的預設連接埠) 的連入連線：

    [![](troubleshooting-images/troubleshooting-image2.png "確認防火牆允許透過連接埠 22 的連入連線")](troubleshooting-images/troubleshooting-image2.png#lightbox)

    如果您已停用 [自動允許已簽名的軟體接收傳入連線]，OS X 將會在配對過程中顯示對話方塊，詢問是否要允許 `mono-sgen` 或 `mono-sgen32` 接收傳入連線。 在此對話方塊上，請務必按一下 [允許]：

    [![](troubleshooting-images/troubleshooting-image4a.png "在此對話方塊上按一下 [允許]")](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. 確認您已登入該 Mac 上的使用者帳戶，並且具有作用中的 GUI 工作階段。

5. 確定您目前是以「使用者名稱」而不是「全名」連線到 Mac。 這可避免已知的全名限制，包括加重音的字元。

    您可以在 **Terminal.app** 中執行 `whoami` 命令來找出您的「使用者名稱」。

    例如，從下面的螢幕擷取畫面中，帳戶名稱將會是 **amyb**，而不是 **Amy Burns**：

    [![](troubleshooting-images/troubleshooting-image5a.png "從 [終端機] 應用程式取得帳戶名稱")](troubleshooting-images/troubleshooting-image5a.png#lightbox)


6. 確認您用於 Mac 的 IP 位址正確。 您可以在 Mac 上的 [系統偏好設定] > [共享] > [遠端登入] 底下找到 IP 位址。

    [![](troubleshooting-images/troubleshooting-image17.png "[系統喜好設定] 應用程式中的 IP 位址")](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. 確認 Mac 的 IP 位址之後，請嘗試在 Windows 上的 `cmd.exe` 中對該位址執行 `ping`：

    ```
    ping 10.1.8.95
    ```
    
    如果 Ping 失敗，即表示無法從 Windows 電腦「路由」至 Mac。 此問題將必須在兩部電腦間的區域網路組態層級解決。 請確定兩部機器都位於相同的「區域網路」上。

8. 接著，測試來自 OpenSSH `ssh` 用戶端是否可以從 Windows 成功連線到 Mac。 其中一個安裝此程式的方法是安裝 [Git for Windows](https://git-for-windows.github.io/)(英文\)。 您可以接著啟動 **Git Bash** 命令提示字元，然後嘗試使用您的使用者名稱和 IP 位址透過 `ssh` 連線到 Mac：

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine" />

9. 如果**步驟 8 成功**，您便可以嘗試透過連線執行簡單的命令 (例如 `ls`)：

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    這應該會列出 Mac 上主目錄的內容。 如果 `ls` 命令正確運作，但 Visual Studio 連線仍然失敗，您可以查看[已知的問題與限制](#knownissues)一節以了解 Xamarin 特定的難題。 如果其中沒有任何一個與您的問題相符，請移至 Visual Studio 中的 [說明] > [傳送意見反應] > [回報問題]，並附加[查看詳細資訊記錄檔](#verboselogs)底下所述的記錄檔，在開發人員社群中提出新的 Bug 報告。

10. 如果**步驟 8 失敗**，您可以在 Mac 上的 [終端機] 中執行下列命令，以了解 SSH 伺服器是否接受「任何」連線：

    ```bash
    ssh localhost
    ```
    
11. 如果步驟 8 失敗但**步驟 10 成功**，則問題最有可能是因網路組態的緣故而無法從 Windows 存取 Mac 組建主機上的連接埠 22。 可能的組態問題包括：

    - OS X 防火牆設定不允許連線。 請務必仔細檢查步驟 3。

        OS X 防火牆的個別應用程式組態偶爾也會最後變成無效狀態，亦即 [系統偏好設定] 中顯示的設定並未反映實際的行為。 刪除組態檔 ( **/Library/Preferences/com.apple.alf.plist**) 並重新啟動電腦將可幫助還原預設行為。 其中一個刪除檔案的方法是在 Finder 中的 [前往] &gt; [前往檔案夾] 底下輸入 **/Library/Preferences**，然後將 **com.apple.alf.plist** 檔案移至垃圾桶。

    - Mac 與 Windows 電腦之間其中一個路由器的防火牆設定封鎖連線。

    - Windows 本身不允許對遠端連接埠 22 進行輸出連線。 這是不尋常的行為。 您可以將 Windows 防火牆設定為不允許輸出連線，但預設設定是會允許所有輸出連線。

    - Mac 組建主機不允許透過 `pfctl` 規則從所有外部主機存取連接埠 22。 這是不太可能的情況，除非您知道您在過去已設定 `pfctl`。

12. 如果步驟 8 失敗且**步驟 10 也失敗**，則問題可能是 Mac 上的 SSH 伺服器處理序未執行，或未設定為允許目前的使用者登入。 在此情況下，請務必先仔細檢查來自步驟 2 的 [遠端登入] 設定，然後再調查任何更複雜的可能情況。

<a name="knownissues" />

### <a name="known-issues-and-limitations"></a>已知的問題與限制

> [!NOTE]
> 本節僅適用於您已經使用 OpenSSH SSH 用戶端以 Mac 使用者名稱和密碼成功連線到 Mac 組建主機 (如上述步驟 8 和 9 所述) 的情況。

#### <a name="invalid-credentials-please-try-again"></a>「認證無效。 請重試。」

已知的原因：

- **限制** – 嘗試使用帳戶「全名」來登入組建主機而該名稱包含加重音字元時，就可能發生此錯誤。 這是 Xamarin 用來進行 SSH 連線之 [SSH.NET 程式庫](https://sshnet.codeplex.com/)的限制。 **因應措施**：請參閱上述步驟 5。

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>「無法使用 SSH 金鑰進行驗證。 請嘗試先使用認證登入」

已知的原因：

- **SSH 安全性限制** – 此訊息最常意謂著 Mac 上 **$HOME/.ssh/authorized\_keys** 完整路徑中的其中一個檔案或目錄已針對 _other_ 或 _group_ 成員啟用寫入權限。 **一般修正**：在 Mac 上的終端機命令提示字元中執行 `chmod og-w "$HOME"`。 如需有關是哪個特定檔案或目錄造成問題的詳細資料，請在 [終端機] 中執行 `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"`，然後從您的桌面開啟 **sshd.log** 檔案並尋找 "Authentication refused: bad ownership or modes" (驗證被拒：無效的擁有權或模式)。

#### <a name="trying-to-connect-never-completes"></a>「正在嘗試連線...」永遠無法完成

- **Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** – 如果在 [系統偏好設定] &gt; [使用者與群組] 中，Mac 使用者 [進階選項] 操作功能表中的 [登入 Shell] 設定為 **/bin/bash** 以外的值，在 Xamarin 4.1 上就可能發生此問題。 (從 Xamarin 4.2 開始，此情況會改為導致產生「無法連線」錯誤訊息)。**因應措施**：將 [登入 Shell] 變更回原始預設值 **/bin/bash**。

<a name="tryagain" />

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>「無法連線到 MacBuildHost.local。 請重試。」

回報的原因：

- **Bug** – 一些使用者在嘗試使用 Active Directory 或其他目錄服務網域使用者帳戶來登入組建主機時，在記錄檔中看到此訊息且伴隨更詳細的錯誤「為使用者設定 SSH 時發生未預期的錯誤...工作階段作業逾時」。 **因應措施：** 改用本機使用者帳戶來登入組建主機。

- **Bug** – 一些使用者在嘗試於連線對話方塊中按兩下 Mac 名稱來連線到組建主機時看到此錯誤。 **可能的因應措施**：使用 IP 位址來[手動新增 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac)。

- **Bug [#35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)** – 一些使用者在於 Mac 組建主機與 Windows 之間使用無線網路連線遇到此錯誤。 **可能的因應措施**：將這兩部電腦移至有線網路連線。

- **Bug [#36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)** – 在 Xamarin 4.0 上，只要 Mac 上的 **$HOME/.bashrc** 檔案中包含錯誤，就會出現此訊息。 (從 Xamarin 4.1 開始， **.bashrc** 檔案中的錯誤將不再影響連線程序)。**因應措施**：將 **.bashrc** 檔案移至備份位置 (或如果您知道已不需要此檔案，則可將其刪除)。

- **Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** – 如果在 [系統偏好設定] > [使用者與群組] 中，Mac 使用者 [進階選項] 操作功能表中的 [登入 Shell] 設定為 **/bin/bash** 以外的值，就可能出現此錯誤。 **因應措施**：將 [登入 Shell] 變更回原始預設值 **/bin/bash**。

- **限制** – 如果 Mac 組建主機連線到無法存取網際網路的路由器 (或如果 Mac 使用的 DNS 伺服器在要求 Windows 電腦的反向 DNS 查閱時逾時)，就可能出現此錯誤。 Visual Studio 會花費大約 30 秒的時間來擷取 SSH 指紋，但最終無法連線。

    **可能的因應措施**：將 "UseDNS no" 新增至 **sshd\_config** 檔案。 請務必先了解此 SSH 設定，然後再進行變更。 如需範例，請參閱 [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](http://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option)(英文\)。

    下列步驟說明一個變更此設定的方式。 您將需要登入 Mac 上的系統管理員帳戶，才能完成這些步驟。

    1. 在 [終端機] 命令提示字元中執行 `ls /etc/ssh/sshd_config` 和 `ls /etc/sshd_config`來確認 **sshd\_config** 檔案的位置。 針對所有剩餘的步驟，請務必使用「不會」傳回「無此檔案或目錄」的位置。

        [![](troubleshooting-images/troubleshooting-image18.png "在 [終端機] 中執行 `ls /etc/ssh/sshd_config` 和 `ls /etc/sshd_config`")](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. 在 [終端機] 中執行 `cp /etc/ssh/sshd_config "$HOME/Desktop/"` 以將檔案複製到您的桌面。

    3. 從您的桌面以文字編輯器開啟檔案。 例如，您可以在 [終端機] 中執行 `open -a TextEdit "$HOME/Desktop/sshd_config"`。

    4. 在檔案結尾新增下列行：

        ```
        UseDNS no
        ```

    5. 移除任何顯示 `UseDNS yes` 的行，以確定讓新設定生效。

    6. 儲存檔案。

    7. 在 [終端機] 中執行 `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` 以將編輯過的檔案複製回原位。 如果出現提示，請輸入您的密碼。

    8. 在 [系統偏好設定] &gt; [共享] &gt; [遠端登入] 底下，將 [遠端登入] 停用後再啟用，以重新啟動 SSH 伺服器。

<a name="clearing" />

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>清除 Mac 上的 Broker、IDB、組建及 Designer 代理程式

如果您的記錄檔在任何 Mac 代理程式的「安裝」、「上傳」或「啟動」步驟期間顯示問題，您可以嘗試刪除 [XMA] 快取資料夾以強制 Visual Studio 重新上傳它們。

1. 在 Mac 上的 [終端機] 中執行下列命令：

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. 按住 Control 同時按一下 [XMA] 資料夾，然後選取 [丟到垃圾桶]：

    [![](troubleshooting-images/troubleshooting-image8.png "將 [XMA] 資料夾移至垃圾桶")](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. 在 Windows 上也有它可協助清除的快取。 在 Windows 上以系統管理員身分開啟命令提示字元：

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>警告訊息

本節探討可能出現在 [輸出] 視窗和記錄檔中但您通常可以忽略的一些訊息。

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>「安裝的 Xamarin.iOS 與本機 Xamarin.iOS 不符」

只要您已確認 Mac 和 Windows 都已更新成相同的散發通道，便可忽略此警告。

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>「無法執行 'ls /usr/bin/mono':ExitStatus=1」

只要 Mac 執行的是 OS X 10.11 (El Capitan) 或更新版本，便可忽略此訊息。 此訊息在 OS X 10.11 上並不成問題，因為 Xamarin 也會檢查 **/usr/local/bin/mono**，這是 OS X 10.11 上 `mono` 的正確預期位置。

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>「Bonjour 服務 'MacBuildHost' 未使用其 IP 位址回應。」

除非您注意到連線對話方塊並未顯示 Mac 組建主機的 IP 位址，否則可忽略此訊息。 如果該對話方塊中「遺漏」IP 位址，您仍然可以[手動新增 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac)。

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>「來自 10.1.8.95 的使用者 a 無效" 及 "input\_userauth\_request：無效的使用者 a [preauth]」

如果您查看 **sshd.log**，可能就會注意到此訊息。 這些訊息是正常連線程序的一部分。 之所以會出現這些訊息，是因為 Xamarin 在擷取「SSH 指紋」時，暫時使用了使用者名稱 **a**。

### <a name="output-window-and-log-files"></a>輸出視窗與記錄檔

如果 Visual Studio 在連線到組建主機時遇到錯誤，有 2 個位置可供檢查是否有額外訊息：[輸出] 視窗與記錄檔。

#### <a name="output-window"></a>輸出視窗

[輸出] 視窗是最佳的起點。 它會顯示與主要連線步驟和錯誤相關的訊息。 檢視 [輸出] 視窗中的 Xamarin 訊息：

1. 從功能表中選取 [檢視] > [輸出]，或按一下 [輸出] 索引標籤。
2. 按一下 [顯示輸出來源] 下拉式功能表。
3. 選取 [Xamarin]。

[![](troubleshooting-images/troubleshooting-image11.png "選取 [輸出] 索引標籤中的 [Xamarin]")](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>記錄檔

如果 [輸出] 視窗所包含的資訊不足以診斷問題，接下來便可查看記錄檔。 記錄檔包含未顯示在 [輸出] 視窗中的額外診斷訊息。 檢視記錄檔：

1. 啟動 Visual Studio。

    > [!IMPORTANT]
    > 請注意，預設並不會啟用 **.svclogs**。 若要存取它們，您將需要啟動 Visual Studio 搭配詳細資訊記錄檔，如[詳細資訊記錄檔](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs)指南所述。 如需詳細資訊，請參閱[使用活動記錄來針對擴充功能進行疑難排解](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/) \(英文\) 部落格。

2. 嘗試連線到組建主機。

3. 在 Visual Studio 遇到連線錯誤之後，請從 [說明] > [Xamarin] > [Zip 記錄檔] 收集記錄檔：

    [![](troubleshooting-images/troubleshooting-image12.png "從 [說明] > [Xamarin] > [Zip 記錄檔] 收集記錄檔")](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. 當您開啟 .zip 檔案時，將會看到類似以下範例的檔案清單。 就連線錯誤而言，最重要的檔案是 **\*Ide.log** 和 **\*Ide.svclog** 檔案。 這些檔案以兩種略為不同的格式包含相同的資訊。 **.svclog** 是 XML，如果您想要瀏覽訊息，此檔案會相當有用。 **.log** 是純文字，如果您想要使用命令列工具來篩選訊息，此檔案會相當有用。

    若要瀏覽所有訊息，請選取並開啟 **.svclog** 檔案：

    [![](troubleshooting-images/troubleshooting-image13.png "選取 svclog 檔案")](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. **.svclog** 檔案會在 **Microsoft Service Trace Viewer** 中開啟。 您可以依執行緒瀏覽訊息，以查看相關的訊息群組。 若要依執行緒瀏覽，請先選取 [圖形] 索引標籤，然後按一下 [版面配置模式] 下拉式功能表並選取 [執行緒]：

    [![](troubleshooting-images/troubleshooting-image14.png "按一下 [版面配置模式] 下拉式功能表並選取 [執行緒]")](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs" />

#### <a name="verbose-log-files"></a>詳細資訊記錄檔

如果一般記錄檔提供的資訊仍然不足以診斷問題，最後一個可嘗試的技巧就是啟用詳細資訊記錄功能。 詳細資訊記錄檔也是 Bug 報告上慣用的記錄檔。

1. 結束 Visual Studio。

2. 啟動[**開發人員命令提示字元**](https://msdn.microsoft.com/library/ms229859(v=vs.110).aspx)。

3. 在命令提示字元中執行下列命令，以啟動 Visual Studio 搭配詳細資訊記錄功能：

    ```bash
    devenv /log
    ```

4. 嘗試從 Visual Studio 連線到組建主機。

5. 在 Visual Studio 遇到連線錯誤之後，請從 [說明] > [Xamarin] > [Zip 記錄檔] 收集記錄檔。

6. 在 Mac 上的 [終端機] 中執行下列命令，以將所有最近的記錄檔訊息從 SSH 伺服器複製到您桌面上的檔案中：

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

如果這些詳細資訊記錄檔並未提供足以直接解決問題的線索，請[提出新的 Bug 報告](https://bugzilla.xamarin.com/newbug)，並附加步驟 5 的 .zip 檔案和步驟 6 的 .log 檔案。

## <a name="troubleshooting-automatic-mac-provisioning"></a>針對自動 Mac 佈建進行疑難排解

### <a name="ide-log-files"></a>IDE 記錄檔

如果您在使用[自動 Mac 佈建](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning)時遇到任何問題，請查看儲存於 **%LOCALAPPDATA%\Xamarin\Logs\15.0** 的 Visual Studio 2017 IDE 記錄。

## <a name="troubleshooting-build-and-deployment-errors"></a>針對組建與部署錯誤進行疑難排解

本節涵蓋 Visual Studio 成功連線到組建主機之後可能發生的一些問題。

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>「無法以 User='macuser' 連接到 Address='192.168.1.2:22'」

已知的原因：

- **Xamarin 4.1 安全性功能** – 如果您在使用 Xamarin 4.1 或更新版本之後降級成 Xamarin 4.0，就「會」發生此錯誤。 在此情況下，錯誤會伴隨有額外的警告「私密金鑰已加密，但複雜密碼是空的」。 這是一個因 Xamarin 4.1 中的新安全性功能而進行的「刻意」變更。 **建議的修正**：從 **%LOCALAPPDATA%\Xamarin\MonoTouch** 中刪除 **id\_rsa** 和 **id\_rsa.pub**，然後重新連線到 Mac 組建主機。

- **SSH 安全性限制** - 當此訊息伴隨有額外警告「無法使用現有的 SSH 金鑰驗證使用者」時，最常意謂著 Mac 上 **$HOME/.ssh/authorized\_keys** 完整路徑中的其中一個檔案或目錄已針對 _other_ 或 _group_ 成員啟用寫入權限。 **一般修正**：在 Mac 上的終端機命令提示字元中執行 `chmod og-w "$HOME"`。 如需有關是哪個特定檔案或目錄造成問題的詳細資料，請在 [終端機] 中執行 `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"`，然後從您的桌面開啟 **sshd.log** 檔案並尋找 "Authentication refused: bad ownership or modes" (驗證被拒：無效的擁有權或模式)。

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>無法從網路共用載入解決方案

只有當解決方案位於本機 Windows 檔案系統或對應的磁碟機上時，系統才會編譯解決方案。

儲存在網路共用中的解決方案可能會擲回錯誤，或完全拒絕編譯。 Visual Studio 中使用的任何 **.sln** 檔案都應該儲存在本機 Windows 檔案系統上。

以下是因此問題而擲回的錯誤：

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

相關 Bug：[#36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>遺漏佈建設定檔或「無法建立 fat 程式庫」錯誤

請在 Mac 上啟動 Xcode，並確定已登入您的 Apple 開發人員帳戶且已下載您的「iOS 開發設定檔」：

[![](troubleshooting-images/troubleshooting-image7.png "確定已登入 Apple 開發人員帳戶且已下載「iOS 開發設定檔」")](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>「嘗試對無法連線的網路進行通訊端作業」

回報的原因：

- **增強 [#36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)** – 當 Visual Studio 使用 IPv6 位址來連線到組建主機時，此錯誤可讓組建無法成功。 (組建主機連線尚不支援 IPv6 位址)。

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>重新安裝搶鮮版 (Beta)/Alpha 版通道之後，Xamarin.iOS Visual Studio 外掛程式無法載入

相關的 Bug [#40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781)。

當 Visual Studio 無法重新整理 MEF 元件快取時，就可能發生此問題。 如果是這種情況，安裝此 Visual Studio 延伸模組可能會有幫助：[https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)

這會清除 Visual Studio MEF 元件快取來修正快取損毀的問題。

<a name="errors" />

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>因 Mac 上有現有的組建主機處理序而發生錯誤

來自先前組建主機連線的處理序有時會干擾目前作用中連線的行為。 若要檢查是否有現有的處理序，請關閉 Visual Studio，然後在 Mac 上的 [終端機] 中執行下列命令：

```bash
ps -A | grep mono
```

[![](troubleshooting-images/troubleshooting-image10.png "在 Mac 上的 [終端機] 中執行命令")](troubleshooting-images/troubleshooting-image10.png#lightbox)

若要終止現有的處理序，請使用下列命令：

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>清除 Mac 組建快取

如果您要對組建問題進行疑難排解，並想要確定該行為與任何儲存在 Mac 上的暫時組建檔案無關，您可以刪除組建快取資料夾。

1. 在 Mac 上的 [終端機] 中執行下列命令：

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. 按住 Control 同時按一下 [mtbs] 資料夾，然後選取 [丟到垃圾桶]：

    [![](troubleshooting-images/troubleshooting-image9.png "將 [mtbs] 資料夾移至垃圾桶")](troubleshooting-images/troubleshooting-image9.png#lightbox)


## <a name="related-links"></a>相關連結

- [與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Xamarin Mac 組建代理程式影片](https://www.youtube.com/watch?v=MBAPBtxkjFQ) \(英文\)
