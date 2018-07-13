---
title: 硬體加速以提升模擬器效能
description: 本文說明如何使用您電腦的硬體加速功能將 Android Emulator 提升到最大效能。
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: c2bef2c614d4cc0655deb9732ccefec223a8318a
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848463"
---
# <a name="hardware-acceleration-for-emulator-performance"></a>硬體加速以提升模擬器效能

_本文說明如何使用您電腦的硬體加速功能將 Android Emulator 提升到最大效能。_

## <a name="overview"></a>總覽

Visual Studio 讓開發人員使用 Android Emulator，在 Android 裝置無法使用或不實用的情況下，更容易測試和偵錯他們的 Xamarin.Android 應用程式。
不過，如果執行 Android 模擬器的電腦上無法使用硬體加速，Android 模擬器的執行速度會很慢。 使用以 x86 硬體為目標的特殊虛擬裝置映像，結合兩種虛擬化技術的其中之一，可大幅提升 Android 模擬器的效能：

1. **Microsoft 的 Hyper-V 和 Hypervisor 平台**. Hyper-V 是 Windows 的虛擬化功能，可讓您在實體主機電腦上執行虛擬化的電腦系統。 這是建議用於加速 Android Emulator 的虛擬化技術。 若要深入了解 Hyper-V，請參閱 [Windows 10 上的 HYPER-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/)。

2. **Intel Hardware Accelerated Execution Manager (HAXM)**. 
   HAXM 是執行 Intel Cpu 之電腦的虛擬化引擎。
   針對無法執行 Hyper-V 的電腦，建議使用此虛擬化引擎。

如果符合下列準則，Android Emulator 會自動使用硬體加速：

-   開發電腦上有硬體加速可用，並已啓用。

-   模擬器正在執行專為 **x86** 型虛擬裝置建立的模擬器映像。

如需使用 Android Emulator 啟動和偵錯的資訊，請參閱 [Debugging on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上偵錯)。

## <a name="hyper-v"></a>Hyper-V

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Preview 目前支援 Hyper-V。

強烈建議使用 Windows 10 (2018 年 4 月更新或更新版本) 的開發人員使用 Microsoft Hyper-V 來加速 Android Emulator。 搭配 Hyper-V 使用 Android Emulator：

1. **更新至 Windows 10 2018 4 月更新 (1803 組建) 或更新版本**。
   若要確認所執行的 Windows 版本，請按一下 Cortana 搜尋列，然後鍵入 **About**。 在搜尋結果中選取 [About your PC] \(電腦相關\)。 向下捲動 [有關] 對話方塊至 [Windows 規格] 區段。 [版本] 至少應為 1803：

    [![Windows 規格](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **啓用 Windows Hypervisor 平台**。
   在 Cortana 搜尋列中，鍵入 **Turn Windows features on or off**。
   向下捲動 [Windows 功能] 對話方塊，確定已啟用 **Windows Hypervisor 平台**：

    [![已啓用 Windows Hypervisor 平台](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

   啟用 **Windows Hypervisor 平台**會自動啟用 Hyper-V。 完成這項變更後，最好重新啟動 Windows。

3. **安裝 [Visual Studio 15.8 Preview 1 或更新版本](https://visualstudio.microsoft.com/vs/preview/)**。
   這個版本的 Visual Studio 提供 IDE 支援，可搭配 Hyper-V 執行 Android Emulator。
 
4. **安裝 Android Emulator 套件 27.2.7 或更新版本**。 若要安裝此套件，請在 Visual Studio 中，巡覽至 [工具] > [Android] > [Android SDK Manager]。 選取 [工具] 索引標籤，確定 Android 模擬器的版本至少為 27.2.7。 另請確定 Android SDK Tools 的版本為 26.1.1 或更新版本：

    [![Android SDK 及工具對話方塊](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. 如果模擬器版本至少為 27.2.7，但低於 27.3.1，則需要下列因應措施才能使用 Hyper-V：

    1.  在 **C:\\Users\\_username_\\.android** 資料夾中，建立稱為 **advancedFeatures.ini** 的檔案 (如果檔案不存在)。

    2.  將下列行新增至 **advancedFeatures.ini**：
        ```
        WindowsHypervisorPlatform = on
        ```


### <a name="known-issues"></a>已知問題

-   如果您在更新至 Visual Studio Preview 之後無法更新至模擬器 27.2.7 版或更新版本，您可能必須直接安裝[預覽版安裝程式](http://aka.ms/hyperv-emulator-dl)來啟用較新的模擬器版本。

-   使用特定 Intel 和 AMD 型處理器時，效能可能會降低。

-   Android 應用程式載入部署需要花費的時間可能異常。

-   MMIO 存取錯誤可能斷斷續續阻止 Android 模擬器開機。 重新啟動模擬器應該會解決此問題。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Hyper-V 支援需要 Windows 10。 如需詳細資料，請參閱 [Hyper-V 需求](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements)。

-----

## <a name="haxm"></a>HAXM

HAXM 為硬體輔助的虛擬化引擎 (Hypervisor)，使用 Intel 虛擬化技術 (VT) 來加速主機電腦上的 Android 應用程式模擬。 使用 HAXM 結合 Intel 提供的 Android x86 模擬器映像，可在已啟用 VT 的系統上加快 Android 模擬的速度。

如果您在配備具有 VT 功能之 Intel CPU 的電腦上開發，可以利用 HAXM 大幅加速 Android Emulator (如果您不確定 CPU 是否支援 VT，請參閱 [Does My Processor Supports Intel Virtualization Technology?](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (我的處理器是否支援 Intel 虛擬化技術？)。

> [!NOTE]
> 您無法在另一部 VM 執行 VM 加速的模擬器，例如 VirtualBox、VMWare 或 Docker 所裝載的 VM。 您必須[直接在系統硬體上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)執行 Android 模擬器。

在您第一次使用 Android Emulator 前，最好先確認已安裝 HAXM 並可供 Android Emulator 使用。

### <a name="verifying-haxm-installation"></a>驗證 HAXM 安裝

您可以在模擬器啟動時，藉由檢視 [正在啟動 Android Emulator] 視窗，以檢查 HAXM 是否可供使用。 若要啟動 Android Emulator，請執行下列動作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按一下 [工具] > [Android] > [Android Device Manager] 啟動 Android Device Manager：

    [![Android Device Manager 功能表項目位置](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/win/11-perf-warn.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如 **VisualStudio\_android-23\_x86\_phone**)，然後按一下 [開始]：

    ![使用預設虛擬裝置映像啟動 Android Emulator](hardware-acceleration-images/win/02-start-default-avd.png)

4. 模擬器啟動時，請連一 [正在啟動 Android Emulator] 對話方塊視窗。 如果已安裝 HAXM，您將會看到「HAX 正在運作且模擬器會在快速虛擬模式中執行」的訊息，如這個螢幕擷取畫面所示：

    ![HAXM 在 [正在啟動 Android Emulator] 對話方塊中顯示為可用](hardware-acceleration-images/win/03-haxm-detected.png)

   如果您未看到此訊息，則可能未安裝 HAXM。 例如，以下螢幕擷取畫面顯示在無法使用 HAXM 時，您可能會看到的訊息：

    ![這台電腦上沒有 HAXM 可供使用](hardware-acceleration-images/win/04-haxm-error.png)

   如果您的電腦上沒有 HAXM 可供使用，請使用下一節的步驟來安裝 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按一下 [工具] > [Device Manager] 啟動 Android Device Manager：

    [![Android Device Manager 功能表項目位置](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/mac/04-avd-warning.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如，**Android\_Accelerated\_x86**) 然後按一下 [播放]：

    [![使用預設虛擬裝置映像啟動 Android Emulator](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. 模擬器啟動時，請連一 [正在啟動 Android Emulator] 對話方塊視窗。 如果已安裝 HAXM，您將會看到「HAX 正在運作且模擬器會在快速虛擬模式中執行」的訊息，如這個螢幕擷取畫面所示：

    ![HAXM 在 [正在啟動 Android Emulator] 對話方塊中顯示為可用](hardware-acceleration-images/mac/03-haxm-detected.png)

   如果您的電腦上沒有 HAXM 可供使用 (例如，如果您看到類似「請確定 Intel HAXM 已正確安裝且可使用」的錯誤訊息)，請使用下一節的步驟來安裝 HAXM。

-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>安裝 HAXM

如果模擬器無法啟動，可能必須手動安裝 HAXM。 適用於 Windows 和 macOS 的 HAXM 安裝套件，可從 [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager) \(英文\) 頁面取得。 使用下列步驟來手動下載並安裝 HAXM：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 從 Intel 網站，下載適用於 Windows 的最新 [HAXM 虛擬化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。 直接從 Intel 網站下載 HAXM 安裝程式的優點，在於可確保您使用的是最新版本。

   或者，您可以使用 SDK 管理員來下載 HAXM 安裝程式 (在 SDK 管理員中，按一下 [工具] > [額外項目] > [Intel x86 Emulator Accelerator (HAXM 安裝程式)])。 Android SDK 通常會將 HAXM 安裝程式下載到下列位置：

   **C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   請注意，SDK 管理員不會安裝 HAXM，它只會將 HAXM 安裝程式下載到上述位置；您仍然必須以手動方式啟動它。

2. 執行 **intelhaxm android.exe** 來啟動 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   ![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>硬體加速與 AMD CPU

因為 Android 模擬器目前[僅在 Lunux 上](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies)支援 AMD 硬體加速，所以無法在執行 Windows 的 AMD 電腦上使用硬體加速。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 從 Intel 網站，下載適用於 macOS 的最新 [HAXM 虛擬化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。

2. 執行 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   [![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>相關連結

* [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (在 Android 模擬器上執行應用程式)
