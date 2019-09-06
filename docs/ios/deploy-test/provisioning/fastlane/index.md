---
title: 適用於 iOS 的 fastline 簡介
description: 本指南介紹可用來對 iOS 應用程式進行程式碼簽署的 fastlane 工具。 描述如何更新、安裝及使用 fastlane 工具。
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 290c90820bad2d213f8f3a978437a55f7ca6069e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285788"
---
# <a name="introduction-to-fastlane-for-ios"></a>適用於 iOS 的 fastline 簡介

fastlane 是一個開放原始碼專案，其建立目的是要將發行 iOS 和 Android 應用程式時既容易混淆又通常乏味冗長的流程簡化。 它是由數個公用程式所組成，每個公用程式都處理特定的應用程式發行層面，例如：

- [deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme) – 管理螢幕擷取畫面、中繼資料及應用程式套件組合，以及將這些項目上傳到 iTunes Connect。
- [produce](https://github.com/fastlane/fastlane/tree/master/produce#readme) – 在 iTunes Connect 和 Developer Portal (開發人員入口網站) 中建立應用程式 (通常稱為 AppID)。 它也包含對「應用程式群組」和應用程式服務的支援。
- [pem](https://github.com/fastlane/fastlane/tree/master/pem#readme) – 建立和管理「推播通知佈建設定檔」。
- [gym](https://github.com/fastlane/fastlane/tree/master/gym#readme) – 這可用來組建和簽署 iOS 應用程式。 (Xamarin 應用程式已經使用 MSBuild 來組建、簽署及封存應用程式)
- [cert](https://github.com/fastlane/fastlane/tree/master/cert#readme) – 建立和管理程式碼簽署憑證 
- [sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme) – 建立和管理佈建設定檔
- [match](https://github.com/fastlane/fastlane/tree/master/match#readme) – 建立和維護憑證與設定檔，並將它們儲存在 Git 存放庫中，以便在整個開發小組中進行同步。

fastlane 有各種不同使用方式：透過終端機命令、透過檔案型方法，或藉由使用環境變數來進行持續整合組建。 

本指南特別說明如何設定裝置來進行 iOS 應用程式開發，並將焦點放在 **cert** **sigh** 及 **match** 公用程式。 

所提供的內容可作為輔助應用程式散發的跳板，其中包括將持續整合伺服器上的流程完全自動化。 不過，請務必注意，fastlane 是一個製作工具來支援 Xcode 專案的協力廠商，因此有些工具或命令 (例如 `fastlane init`) 在與 csproj 檔案搭配使用時，可能不會如預期般運作。 如需有關使用 fastlane、額外工具或使用 fastlane 來針對 Android 進行發行的詳細資訊，請參閱 [https://fastlane.tools/](https://fastlane.tools/)

<a name="Installation" />

## <a name="installation"></a>安裝

1. 確定 Xcode 命令列工具已安裝在 macOS 電腦上。 若要安裝這些工具，請在終端機中使用 `xcode-select --install` 命令。 如果已經安裝這些工具，將會顯示以下錯誤：

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. 從 [https://download.fastlane.tools](https://download.fastlane.tools) 下載 fastlane 工具。 

    > [!NOTE]
    > 您可以使用 `brew cask install fastlane` 從 Homebrew 安裝 fastlane 工具，或使用 `sudo gem install fastlane –NV` 透過 Rubygems (2.0 或更新版本) 安裝這些工具。 不過，使用安裝程式將可確保有正確的相依性可供使用。 

3. 將檔案解壓縮來安裝 fastlane，然後按兩下 `install` 可執行檔。 如果您收到通知您檔案「無法開啟，因為它來自無法識別的開發者」的錯誤，請按 [確定] 並執行下列動作：
    - 按住 Control 鍵再按一下 `install` 可執行檔。 這會顯示以下的對話方塊：

     ![](images/fastlane-image12.png "安裝對話方塊")

    - 按 [確定] 以開始安裝 fastlane 工具

4. 終端機將會提是您以下所示的對話方塊。 按 `y`：

   ![](images/fastlane-image13.png "終端機提示")

5. 第一次使用 fastlane 之前，先執行 `which fastlane`。 路徑看起來應該如下所示： 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

6. 如果路徑與上述相同，表示您已經做好開始的準備。

     如果沒有，請執行下列動作：在 macOS 上，使用下列命令來開啟 `.bash_profile`，這是主目錄中的隱藏純文字檔：

    ```bash
    open ~/.bash_profile
    ```

7. 新增下列 PATH 環境變數，然後儲存它： 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

8. 再次執行 `which fastlane`，以確認路徑看起來像 `/Users/[user]/.fastlane/bin`


## <a name="updating-fastlane"></a>更新 fastlane

fastlane 是一個定期推出新版本、非常活躍的開放原始碼專案。 如果有新版 fastlane 可供使用，在您執行任何 fastlane 命令時，就會收到通知：

[![](images/fastlane-image0.png "fastlane 更新提示")](images/fastlane-image0.png#lightbox)


若要更新至新版 fastlane，請從[這裡](https://download.fastlane.tools)下載最新的套件，然後按兩下安裝套件來執行它：

[![](images/fastlane-image0a.png "執行安裝套件")](images/fastlane-image0a.png#lightbox)


## <a name="contents"></a>內容

這系列的指南介紹一些工具，fastlane 會使用這些工具來針對您的 iOS 應用程式進行程式碼簽署，以做好開發或散發準備。 目前涵蓋的工具包括：

- [cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [match](~/ios/deploy-test/provisioning/fastlane/match.md)

cert 和 sigh 可用來在本機電腦上建立和管理簽署憑證與部署設定檔。 match 可進一步處理此流程。 它會建立和管理憑證與佈建設定檔，並將它們儲存在 Git 存放庫中，以供開發小組中的所有成員存取。 請閱讀每個小節以了解它們的運作方式及用法。

## <a name="using-fastlane-tools-with-xamarin"></a>搭配 Xamarin 使用 fastlane 工具

使用 fastlane 來建立簽署身分識別和佈建設定檔之後，在 Visual Studio for Mac 中設定套件組合簽署選項應該相當簡單，前提是憑證和私密金鑰在 macOS 鑰匙圈中，且佈建設定檔位於 `~/Library/MobileDevice/Provisioning Profiles` 資料夾中。

若要為 Xamarin.iOS 應用程式設定程式碼簽署選項，請在專案名稱上按一下滑鼠右鍵，選取 [專案選項] > [建置] > [iOS 套件組合簽署]，然後明確設定 [簽署身分識別] 和 [佈建設定檔]，如下所示：

[![](images/fastlane-image11.png "明確設定 [簽署身分識別] 和 [佈建設定檔]")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>相關連結

- [fastlane 文件](https://fastlane.tools/)
- [fastlane 程式碼簽署文件](https://docs.fastlane.tools/codesigning/getting-started/)
- [程式碼簽署指南](https://codesigning.guide/)
