---
title: Android Emulator 硬體加速
description: 如何準備您的電腦以達到最大的 Google Android 模擬器效能
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/10/2018
ms.openlocfilehash: 2f0bb6f1371b9ce1b925b876851d58f3c4d01419
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Android Emulator 硬體加速

無硬體加速的 Google Android 模擬器極為緩慢。 使用以 x86 硬體為目標的特殊模擬器映像和兩種虛擬化技術其中之一，可大幅提升 Google Android 模擬器的效能：

1. **Microsoft Hyper-V 和 Hypervisor 平台** &ndash; Hyper-V 是 Windows 10 提供的虛擬元件，可在實體主機的上層執行虛擬電腦系統。 這是加速 Google Android 模擬器映像建議使用的虛擬化技術。 若要深入了解 Hyper-V，請參閱 [Windows 10 上的 Hyper-V 指南](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/)。
2. **Intel 的 Hardware Accelerated Execution Manager (HAXM)** &ndash;這是執行 Intel CPU 之電腦的虛擬化引擎。 無法使用 Hyper-V 的開發人員，建議使用此虛擬化引擎。

有硬體加速可用時，Android SDK Manager 會自動在執行模擬器映像時使用它，特別是 **x86** 型的虛擬裝置 (如[設定及使用](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中所述)。

## <a name="hyper-v-overview"></a>Hyper-V 概觀

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Preview 目前支援 Hyper-V。

強烈建議使用 Windows 10 (2018 年 4 月更新) 的開發人員使用 Microsoft Hyper-V。 Visual Studio Tools for Xamarin 讓開發人員在 Android 裝置無法使用或不實用的情況下，更容易測試和偵錯他們的 Xamarin.Android 應用程式。

開始使用 Hyper-V 和 Google Android 模擬器：

1. **Windows 10 2018 年 4 月 更新的更新 (組建 1803)** &ndash; 若要確認所執行的 Windows 版本，請按一下 Cortana 搜尋列，然後鍵入**有關**。 在搜尋結果中選取 [About your PC] \(電腦相關\)。 向下捲動 [有關] 對話方塊至 [Windows 規格] 區段。 [版本] 至少應為 1803：

    [![Windows 規格](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **同時啟用 Hyper-V 和 Windows Hypervisor 平台** &ndash; 在 Cortana 搜尋列中，鍵入**開啟或關閉 Windows 功能**。
   向下捲動 [Windows 功能] 對話方塊，確定已啟用 **Windows Hypervisor 平台**。

    [![Hyper-V 與 Windows Hypervisor 平台已啟用](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

    啟用 Hyper-V 和 Windows Hypervisor 平台之後，可能需要重新啟動 Windows。

3. **安裝 [Visual Studio 15.8 Preview 1 或更新版本](https://www.visualstudio.com/vs/preview/)** &ndash; 這個版本的 Visual Studio 提供 IDE 支援，可啟動具有 Hyper-V 支援的 Google Android 模擬器。

4. **安裝 Google Android 模擬器套件 27.2.7 或更新版本** &ndash; 若要安裝此套件，請在 Visual Studio 中，巡覽至 [工具] > [Android] > [Android SDK 管理員]。 選取 [工具] 索引標籤，確定 Android 模擬器元件的版本至少為 27.2.7。

    [![Android SDK 及工具對話方塊](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. 如果 Android 模擬器版本低於 27.3.1，則適用**已知問題** (下一節) 中所述的其他因應措施步驟。


### <a name="known-issues"></a>已知問題

-   如果模擬器版本至少為 27.2.7，但低於 27.3.1，則需要下列因應措施才能使用 Hyper-V：
    1.  在 **C:\\Users\\_username_\\.android** 資料夾中，建立稱為 **advancedFeatures.ini** 的檔案 (如果檔案不存在)。
    2.  將下列行新增至 **advancedFeatures.ini**：
        ```
        WindowsHypervisorPlatform = on
        ```

-   使用特定 Intel 和 AMD 型處理器時，效能可能會降低。

-   Android 應用程式載入部署需要花費的時間可能異常。

-   MMIO 存取錯誤可能斷斷續續阻止 Android 模擬器開機。 重新啟動模擬器應該會解決此問題。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Hyper-V 支援需要 Windows 10。 如需詳細資料，請參閱 [Hyper-V 需求](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements)。

-----

## <a name="haxm-overview"></a>HAXM 概觀

HAXM 為硬體輔助的虛擬化引擎 (Hypervisor)，使用 Intel 虛擬化技術 (VT) 來加速主機電腦上的 Android 應用程式模擬。 透過與由 Intel 和官方 Android SDK 管理員提供的 Android x86 模擬器映像搭配使用，HAXM 就能夠在已啟用 VT 的系統上加快 Android 模擬的速度。 

如果您是在配備具有 VT 功能之 Intel CPU 的電腦上進行開發，您可以利用 HAXM 大幅加速 Google Android 模擬器 (如果您不確定 CPU 是否支援 VT，請參閱[判斷您的處理器是否支援 Intel 虛擬化技術](https://www.intel.com/content/www/us/en/support/processors/000005486.html)。

> [!NOTE]
> 您無法在另一部 VM 執行 VM 加速的模擬器，例如 VirtualBox、VMWare 或 Docker 所裝載的 VM。 您必須[直接在系統硬體上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)執行 Google Android Emulator。

在您第一次使用 Google Android 模擬器之前，最好先確認 HAXM 已安裝並可供 Google Android 模擬器使用。

### <a name="verifying-haxm-installation"></a>驗證 HAXM 安裝

您可以在模擬器啟動時，藉由檢視 [正在啟動 Android Emulator] 視窗，以檢查 HAXM 是否可供使用。 若要啟動 Google Android 模擬器，請執行下列動作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按一下 [工具] > [Android] > [Android Emulator 管理員] 來啟動 Android Emulator 管理員：

    [![[Android Emulator 管理員] 功能表項目位置](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/win/11-perf-warn.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如 **VisualStudio\_android-23\_x86\_phone**)，按一下 [開始]，然後按一下 [啟動]：

    ![使用預設虛擬裝置映像啟動 Google Android 模擬器](hardware-acceleration-images/win/02-start-default-avd.png)

4. 模擬器啟動時，請連一 [正在啟動 Android Emulator] 對話方塊視窗。 如果已安裝 HAXM，您將會看到「HAX 正在運作且模擬器會在快速虛擬模式中執行」的訊息，如這個螢幕擷取畫面所示：

    ![HAXM 在 [正在啟動 Android Emulator] 對話方塊中顯示為可用](hardware-acceleration-images/win/03-haxm-detected.png)

   如果您未看到此訊息，則可能未安裝 HAXM。 例如，以下螢幕擷取畫面顯示在無法使用 HAXM 時，您可能會看到的訊息：

    ![這台電腦上沒有 HAXM 可供使用](hardware-acceleration-images/win/04-haxm-error.png)

   如果您的電腦上沒有 HAXM 可供使用，請使用下一節的步驟來安裝 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按一下 [工具] > [Google 模擬器管理員] 來啟動 Android Emulator 管理員：

    [![[Android Emulator 管理員] 功能表項目位置](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/mac/04-avd-warning.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如 **Android\_Accelerated\_x86**)，按一下 [開始]，然後按一下 [啟動]：

    [![使用預設虛擬裝置映像啟動 Google Android 模擬器](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

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

因為 Google 的 Android Emulator 目前[僅在 Lunux 上](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies)支援 AMD 硬體加速，所以無法在執行 Windows 的 AMD 電腦上使用硬體加速。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 從 Intel 網站，下載適用於 macOS 的最新 [HAXM 虛擬化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。

2. 執行 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   [![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>相關連結

* [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (在 Android 模擬器上執行應用程式)
