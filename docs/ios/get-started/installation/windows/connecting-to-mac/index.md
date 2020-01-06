---
title: 為 Xamarin.iOS 開發與 Mac 配對
description: 本指南說明如何使用 [與 Mac 配對] 將 Visual Studio 2019 連線至 Mac 組建主機。 它會討論如何在 Mac 上啟用遠端登入、從 Visual Studio 2019 連線到 Mac、將 Mac 組建主機手動新增至 Windows 電腦等。
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: e93a12fec63dcb0a31e57de26b3d7ee8827e7864
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489059"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>為 Xamarin.iOS 開發與 Mac 配對

_本指南說明如何使用 [與 Mac 配對]，將 Visual Studio 2019 連線到 Mac 組建主機。相同的指示適用于 Visual Studio 2017。_

## <a name="overview"></a>概觀

建置原生 iOS 應用程式需要存取只在 Mac 上執行的 Apple 組建工具。 因此，Visual Studio 2019 必須連線至網路可存取 Mac，才能建置 Xamarin.iOS 應用程式。

Visual Studio 2019 的 [與 Mac 配對] 功能會探索、連線、驗證並記住 Mac 組建主機，讓以 Windows 為基礎的 iOS 開發人員的工作更有成效。

[與 Mac 配對] 會啟用下列開發工作流程：

- 開發人員可以在 Visual Studio 2019 中撰寫 Xamarin.iOS 程式碼。

- Visual Studio 2019 會開啟與 Mac 組建主機的網路連線，並在該電腦上使用組建工具編譯和簽署 iOS 應用程式。

- 不需要在 Mac 上執行個別的應用程式– Visual Studio 2019 會透過 SSH 安全地叫用 Mac 組建。

- 一旦有變更發生，就會立即通知 Visual Studio 2019。 例如，將 iOS 裝置插入至 Mac 或在網路上變成可用時，會立即更新 iOS 工具列。

- 多個 Visual Studio 2019 執行個體可以同時連線至 Mac。

- 您可以使用 Windows 命令列建置 iOS 應用程式。

> [!NOTE]
>
> 遵循本指南中的指示，完成下列步驟：
>
> - 在 Windows 電腦上，[安裝 Visual Studio 2019](~/get-started/installation/windows.md)
> - 在 Mac 上，[安裝 Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 和 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)
>   - _Xcode 之後手動開啟它_，以便它可已新增任何額外元件。
>
> 如果您不想要安裝 Visual Studio for Mac，則 Visual Studio 2019 可以使用 Xamarin.iOS 和 Mono 來自動設定 Mac 組建主機。
> 您仍然必須安裝並執行 Xcode。
> 如需詳細資訊，請參閱[自動 Mac 佈建](#automatic-mac-provisioning)。

## <a name="enable-remote-login-on-the-mac"></a>在 Mac 上啟用遠端登入

若要設定 Mac 組建主機，請先啟用遠端登入：

1. 在 Mac 上，開啟 [系統偏好設定]，並移至 [共享] 窗格。

2. 核取 [服務] 清單中的 [遠端登入]。

    ![啟用遠端登入](images/sharing.png "啟用遠端登入")

    確定已設定為允許 [所有使用者] 存取，或是允許使用者清單中包含您的 Mac 使用者名稱或群組。

3. 如果出現提示，請設定 macOS 防火牆。

    如果您已設定 macOS 防火牆封鎖傳入連線，則可能需要允許 `mono-sgen` 接收傳入連線。 如果是這種情況，則會顯示警示來提示您。

4. 如果它位在與 Windows 電腦相同的網路上，則 Visual Studio 2019 現在應該可以探索到 Mac。 如果仍然探索不到 Mac，則請嘗試[手動新增 Mac](#manually-add-a-mac) 或查看[疑難排解指南](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)。

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>從 Visual Studio 2019 連線至 Mac

既然已啟用遠端登入，請將 Visual Studio 2019 連線至 Mac。

1. 在 Visual Studio 2019 中，選擇 [檔案] > [新增] > [專案]，然後選取 iOS 專案範本，即可開啟現有 iOS 專案或建立新專案。

2. 開啟 [與 Mac 配對] 對話方塊。

    - 使用 [與 Mac 配對] 按鈕 iOS 工具列：

      ![已反白顯示 [與 Mac 配對] 按鈕的 iOS 工具列](images/ios-toolbar.png "已反白顯示 [與 Mac 配對] 按鈕的 iOS 工具列")

    - 或者，選取 [工具] > [iOS] > [與 Mac 配對]。

    - [與 Mac 配對] 對話方塊會顯示所有先前連線且目前可用的 Mac 組建主機清單：

      ![[與 Mac 配對] 對話方塊](images/pairtomac.png "[與 Mac 配對] 對話方塊")

3. 在清單中，選取 Mac。 按一下 **[Connect]** (連線)。

4. 輸入使用者名稱和密碼。

    - 第一次連線至任何特定 Mac 時，系統會提示您輸入該電腦的使用者名稱和密碼：

      ![輸入 Mac 的使用者名稱和密碼](images/auth.png "輸入 Mac 的使用者名稱和密碼")

      > [!TIP]
      > 登入時，請使用您的系統使用者名稱，而非完整名稱。

    - [與 Mac 配對] 會使用這些認證來建立與 Mac 的新 SSH 連線。 如果成功，則會將金鑰新增至 Mac 上的 **authorized_keys** 檔案。 後續連線至相同 Mac 將會自動登入。

5. [與 Mac 配對] 會自動設定 Mac。

    [從 Visual Studio 2019 15.6 版開始](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning)，Visual Studio 2019 會視需要安裝或更新已連線 Mac 組建主機上的 Mono 和 Xamarin.iOS (請注意，Xcode 仍須靠您手動安裝)。 如需詳細資訊，請參閱[自動 Mac 佈建](#automatic-mac-provisioning)。

6. 尋找連線狀態圖示。

    - Visual Studio 2019 連線至 Mac 時，[與 Mac 配對] 對話方塊中該 Mac 的項目會顯示圖示，指出目前已連線：

      ![連線的 Mac](images/connected.png "連線的 Mac")

      一次只能有一個已連線的 Mac。

      > [!TIP]
      > 以滑鼠右鍵按一下 [與 Mac 配對] 清單中的任何 Mac 都會啟動操作功能表，讓您可以 [連線...]、[不要記住這部 Mac] 或 [中斷連線]：
      >
      > ![[與 Mac 配對] 內容功能表](images/contextmenu.png "[與 Mac 配對] 內容功能表")
      >
      > 如果您選擇 [不要記住這部 Mac]，則會忘記所選取 Mac 的認證。 若要重新連線至該 Mac，您需要重新輸入使用者名稱和密碼。

如果您已成功與 Mac 組建主機配對，則已可開始在 Visual Studio 2019 中建置 Xamarin.iOS 應用程式。 請查看 [Xamarin.iOS for Visual Studio 簡介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)指南。

如果您還是無法與 Mac 配對，則請嘗試[手動新增 Mac](#manually-add-a-mac) 或查看[疑難排解指南](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)。

## <a name="manually-add-a-mac"></a>手動新增 Mac

如果 [與 Mac 配對] 對話方塊中未列出特定 Mac，則請手動予以新增：

1. 找到 Mac 的 IP 位址。

    - 在 Mac 上，開啟 [系統偏好設定] > [共享] > [遠端登入]：

      [![Mac 在系統喜好設定中的 IP 位址 > 共用](images/sharing-ipaddress.png "Mac 在系統喜好設定中的 IP 位址 > 共用")](images/sharing.png#lightbox)

    - 或者，使用命令列。 在 [終端機] 中，發出此命令：

      ```bash
      $ ipconfig getifaddr en0
      196.168.1.8
      ```

      根據網路組態，您可能需要使用 `en0` 以外的介面名稱。 例如：`en1`、`en2` 等等。

2. 在 Visual Studio 2019 的 [與 Mac 配對] 對話方塊中，選取 [新增 Mac...]：

    [![[與 Mac 配對] 對話方塊中的 [新增 Mac] 按鈕](images/addtomac.png "[與 Mac 配對] 對話方塊中的 [新增 Mac] 按鈕")](images/addtomac-large.png#lightbox)

3. 輸入 Mac 的 IP 位址，然後按一下 [新增]：

    ![輸入 Mac 的 IP 位址](images/enteripaddress.png "輸入 Mac 的 IP 位址")

4. 輸入 Mac 的使用者名稱和密碼：

    ![輸入使用者名稱和密碼](images/auth.png "輸入使用者名稱和密碼")

   > [!TIP]
   > 登入時，請使用您的系統使用者名稱，而非完整名稱。

5. 按一下 [登入]，透過 SSH 將 Visual Studio 2019 連線至 Mac，並將它新增至已知電腦的清單。

## <a name="automatic-mac-provisioning"></a>自動 Mac 佈建

從[Visual Studio 2019 15.6 版](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning)開始，與 mac 配對會自動布建 mac，其中包含建立 Xamarin ios 應用程式所需的軟體： Mono、Xamarin、ios （軟體架構，而不是 Visual Studio for Mac IDE）和各種 Xcode 相關工具（但不 Xcode 本身）。

> [!IMPORTANT]
>
> - [與 Mac 配對] 無法安裝 Xcode；您必須在 Mac 組建主機上手動安裝它。 它是進行 Xamarin.iOS 開發的必要項目。
> - 自動 Mac 佈建需要在 Mac 上啟用遠端登入，而且 Mac 必須可透過網路存取 Windows 電腦。 如需詳細資訊，請參閱[在 Mac 上啟用遠端登入](#enable-remote-login-on-the-mac)。
> - 自動化的 Mac 佈建需要 Mac 有 3 GB 的可用空間，才能安裝 Xamarin.iOS。

當 Visual Studio 2019 連線[到 mac](#connect-to-the-mac-from-visual-studio-2019)時，[與 mac 配對] 會執行必要的軟體安裝/更新。

### <a name="mono"></a>Mono

[與 Mac 配對] 將會確定已安裝 Mono。 如果未安裝，則 [與 Mac 配對] 將會在 Mac 下載和安裝 Mono 的最新穩定版本。

進度是以各種提示指出，如下列螢幕擷取畫面所示 (按一下以縮放)：

||安裝檢查|下載|安裝
|---|---|---|---|
|Mono|[![缺少 Mono 安裝](images/mono-missing.png "缺少 Mono 安裝")](images/mono-missing-large.png#lightbox)|[![下載 Mono](images/mono-downloading.png "下載 Mono")](images/mono-downloading-large.png#lightbox)|[![安裝 Mono](images/mono-installing.png "安裝 Mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

[與 Mac 配對] 會在 Mac 上升級 Xamarin.iOS，以符合 Windows 電腦上已安裝的版本。

> [!IMPORTANT]
> [與 Mac 配對] 不會將 Mac 上的 Xamarin.iOS 從 alpha/beta 降級至穩定。 如果您已安裝 Visual Studio for Mac，則請設定[發行通道](https://docs.microsoft.com/visualstudio/mac/update)，如下所示：
>
> - 如果您使用 Visual Studio 2019，請選取 Visual Studio for Mac 中的 [穩定] 更新通道。
> - 如果您使用 Visual Studio 2019 Preview，請選取 Visual Studio for Mac 中的 [Alpha] 更新通道。

進度是以各種提示指出，如下列螢幕擷取畫面所示 (按一下以縮放)：

||安裝檢查|下載|安裝
|---|---|---|---|
|Xamarin.iOS|[![缺少 Xamarin iOS 安裝](images/xamios-missing.png "缺少 Xamarin iOS 安裝")](images/xamios-missing-large.png#lightbox)|[![下載 Xamarin. iOS](images/xamios-downloading.png "下載 Xamarin. iOS")](images/xamios-downloading-large.png#lightbox)|[![安裝 Xamarin. iOS](images/xamios-installing.png "安裝 Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Xcode 工具和授權

[與 Mac 配對] 也會確認是否已安裝 Xcode 並接受其授權。 [與 Mac 配對] 未安裝 Xcode 時，系統會提示接受授權，如下列螢幕擷取畫面所示 (按一下以縮放)：

||安裝檢查|接受授權|
|---|---|---|
|Xcode|[![缺少 Xcode 安裝](images/xcode-missing.png "缺少 Xcode 安裝")](images/xcode-missing-large.png#lightbox)|[![Xcode 授權](images/xcode-license.png "Xcode 授權")](images/xcode-license-large.png#lightbox)|

此外，[與 Mac 配對] 將會安裝或更新利用 Xcode 所散發的各種套件。 例如：

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

這些套件的安裝會快速發生，而且不會出現提示。

> [!NOTE]
> 這些工具與 Xcode 命令列工具不同，而從 macOS 10.9 開始是[使用 Xcode 安裝](https://developer.apple.com/library/content/technotes/tn2339/_index.html)。

### <a name="troubleshooting-automatic-mac-provisioning"></a>針對自動 Mac 佈建進行疑難排解

如果您在使用自動 Mac 布建時遇到任何問題，請查看儲存在 **%LOCALAPPDATA%\Xamarin\Logs\16.0**中的 VISUAL STUDIO 2019 IDE 記錄。 這些記錄可能包含錯誤訊息，協助您進一步診斷失敗或取得支援。

## <a name="build-ios-apps-from-the-windows-command-line"></a>從 Windows 命令列建置 iOS 應用程式

[與 Mac 配對] 支援從命令列建置 Xamarin.iOS 應用程式。 例如：

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

上述範例中傳遞給 `msbuild` 的參數如下：

- `ServerAddress` - Mac 組建主機的 IP 位址。
- `ServerUser` - 要在登入 Mac 組建主機時使用的使用者名稱。
  請使用您的系統使用者名稱，而非完整名稱。
- `ServerPassword` - 要在登入 Mac 組建主機時使用的密碼。

> [!NOTE]
> Visual Studio 2019 會將 `msbuild` 儲存在下列目錄中： **C:\Program Files （x86） \Microsoft Visual Studio\2019\\&lt;版本&gt;\MSBuild\Current\Bin**

[與 Mac 配對] 第一次從 Visual Studio 2019 或命令列登入特定 Mac 組建主機時，會設定 SSH 金鑰。 如果使用這些金鑰，則未來的登入不需要使用者名稱或密碼。 新建立的金鑰會儲存在 **%LOCALAPPDATA%\Xamarin\MonoTouch** 中。

如果從命令列組建叫用來省略 `ServerPassword` 參數，則 [與 Mac 配對] 會嘗試使用已儲存的 SSH 金鑰來登入 Mac 組建主機。

## <a name="summary"></a>總結

本文說明如何使用 [與 Mac 配對] 將 Visual Studio 2019 連線至 Mac 組建主機，並讓 Visual Studio 2019 開發人員使用 Xamarin.iOS 建置原生 iOS 應用程式。

## <a name="next-steps"></a>後續步驟

- [連線疑難排解](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin Mac 組建代理程式影片](https://www.youtube.com/watch?v=MBAPBtxkjFQ) \(英文\)
- [Xamarin.iOS for Visual Studio 簡介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [適用於 Windows 的遠端 iOS 模擬器](~/tools/ios-simulator/index.md)
- [無線部署](~/ios/deploy-test/wireless-deployment.md)
