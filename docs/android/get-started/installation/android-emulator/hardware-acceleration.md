---
title: 硬體加速以提升模擬器效能 (Hyper-V 與 HAXM)
description: 本文說明如何使用您電腦的硬體加速功能將 Android Emulator 提升到最大效能。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: jondouglas
ms.author: jodou
ms.date: 02/13/2020
ms.openlocfilehash: 2a9260bc782d2b19e6fd74356738276e1648dcf2
ms.sourcegitcommit: 16847681df17ed59b3b3528761c02e8fb48ffc4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104352"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>硬體加速以提升模擬器效能 (Hyper-V 與 HAXM)

_本文說明如何使用您電腦的硬體加速功能將 Android Emulator 提升到最大效能。_

Visual Studio 讓開發人員使用 Android Emulator，在 Android 裝置無法使用或不實用的情況下，更容易測試和偵錯其 Xamarin.Android 應用程式。
不過，如果執行 Android 模擬器的電腦上無法使用硬體加速，Android 模擬器的執行速度會很慢。 使用特殊的 x86 虛擬裝置映像，結合電腦的虛擬化功能，可大幅提升 Android Emulator 的效能。

| 狀況    | HAXM        | WHPX       | Hypervisor.Framework |
| ----------- | ----------- | -----------| ----------- |
| 您有 Intel 處理器 | X | X | X |
| 您有 AMD 處理器   |   | X |   |
| 您想要支援 Hyper-V |   | X |   |
| 您想要支援巢狀虛擬化 |   | 限制 |   |
| 您想要使用 Docker 之類的技術  |   | X | X |

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>在 Windows 上加速 Android Emulator

下列虛擬化技術可用於加速 Android Emulator：

1. **Microsoft 的 Hyper-V 與 Windows Hypervisor 平台 (WHPX)**。
   [Hyper-v](https://docs.microsoft.com/virtualization/hyper-v-on-windows/)是 Windows 的虛擬化功能，可讓您在實體主機電腦上執行虛擬化的電腦系統。

2. **Intel Hardware Accelerated Execution Manager (HAXM)**.
   HAXM 是執行 Intel Cpu 之電腦的虛擬化引擎。

若要在 Windows 中擁有最佳體驗，建議您使用 WHPX 來加速 Android 模擬器。 如果您的電腦上沒有 WHPX，則可以使用 HAXM。 如果符合下列準則，Android Emulator 會自動使用硬體加速：

- 您的開發電腦有提供並已啟用硬體加速。

- 模擬器正在執行為 **x86** 型虛擬裝置建立的系統映像。

> [!IMPORTANT]
> 您無法在另一部 VM 執行 VM 加速的模擬器，例如 VirtualBox、VMWare 或 Docker 所裝載的 VM。 您必須[直接在系統硬體上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)執行 Android 模擬器。

如需使用 Android Emulator 啟動和偵錯的資訊，請參閱 [Debugging on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上偵錯)。

<a name="hyper-v-win"></a>

## <a name="accelerating-with-hyper-v"></a>使用 Hyper-V 加速

在啟用 Hyper-V 之前，請先閱讀下列章節，確認您的電腦支援 Hyper-V。

### <a name="verifying-support-for-hyper-v"></a>確認 Hyper-V 支援

Hyper-V 是在 Windows Hypervisor 平台上執行。 若 Hyper-V 要與 Android Emulator 搭配使用，您的電腦必須符合下列準則，以支援 Windows Hypervisor 平台：

- 您的電腦硬體必須符合下列需求：

  - 支援第二層位址轉譯 (SLAT) 的 64 位元 Intel 或 AMD Ryzen CPU。
  - VM 監視器模式延伸模組的 CPU 支援 (Intel CPU 上的 VT-c)。
  - 至少 4 GB 的記憶體。

- 在您電腦的 BIOS 中必須啟用下列項目：

  - 虛擬化技術 (視主機板製造商而定，可能會有不同的標籤)。
  - 硬體強制執行資料執行防止。

- 您的電腦必須更新至 Windows 2018 年 4 月 10 日更新 (組建 1803) 或更新版本。 您可以使用下列步驟來確認您的 Windows 是最新版本：

  1. 在 Windows 搜尋方塊中輸入**關於**。
  2. 在搜尋結果中選取 [About your PC] \(電腦相關\)****。
  3. 在 [**關於**] 對話方塊中向下流覽至 [ **Windows 規格**] 區段。
  4. 確認**版本**至少是 1803：

      [![Windows 規格](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

若要確認電腦的軟硬體是否與 Hyper-V 相容，請開啟命令提示字元並鍵入下列命令：

```cmd
systeminfo
```

如果所有列出的 Hyper-V 需求值皆為 **Yes**，則表示您的電腦可支援 Hyper-V。 例如：

[![範例 systeminfo 輸出](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>啟用 Hyper-V 加速

如果您的電腦符合上述準則，請使用下列步驟，使用 Hyper-V 來加速 Android Emulator：

1. 在 Windows 搜尋方塊中輸入 **Windows 功能**，然後在搜尋結果中選取 [開啟或關閉 Windows 功能]****。 在 [Windows 功能]**** 對話方塊中，同時啟用 **Hyper-V** 與 **Windows Hypervisor 平台**：

    [![啟用 Hyper-v 和 Windows 虛擬機器平臺](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   進行這些變更之後，請重新啟動電腦。
   
> [!IMPORTANT]
>
> 在 Windows 10 2018 年 10 月更新 (RS5) 與更高版本中，您只需要啟用 Hyper-v，原因是其會自動使用 Windows Hypervisor 平台 (WHPX)。

2. **安裝 [Visual Studio 15.8 或更新版本](https://visualstudio.microsoft.com/vs/)** (這個版本的 Visual Studio 提供 IDE 支援，可搭配 Hyper-V 執行 Android Emulator)。

3. **安裝 Android Emulator 套件 27.2.7 或更新版本**。 若要安裝此套件，請在 Visual Studio 中，巡覽至 [工具] > [Android] > [Android SDK Manager]****。 選取 [**工具**] 索引標籤，並確定 Android 模擬器版本至少為27.2.7。 另請確定 Android SDK Tools 的版本為 26.1.1 或更新版本：

    [![Android Sdk 和工具對話方塊](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

當您建立虛擬裝置時（請參閱[使用 Android Device Manager 來管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)），請務必選取**x86**型系統映射。 如果您使用 ARM 型系統映像，則虛擬裝置不會加速，且執行速度會變慢。

## <a name="accelerating-with-haxm"></a>使用 HAXM 加速

如果您的電腦不支援 Hyper-V，請使用 HAXM 來加速 Android Emulator。 如果您想要使用 HAXM，您必須[停用 Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard)。

### <a name="verifying-haxm-support"></a>確認 HAXM 支援

若要判斷您的硬體是否支援 HAXM，請遵循 [Does My Processor Support Intel Virtualization Technology?](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (我的處理器是否支援 Intel 虛擬化技術？) 中的步驟。
如果您的硬體支援 HAXM，您可以使用下列步驟來查看是否已安裝 HAXM：

1. 開啟命令提示字元視窗，然後輸入下列命令：

    ```cmd
    sc query intelhaxm
    ```

2. 檢查輸出，查看 HAXM 處理序是否正在執行中。 如果是，您應該會看到將 `intelhaxm` 狀態列為 `RUNNING` 的輸出。 例如：

    ![HAXM 可用時的 sc 查詢命令輸出](hardware-acceleration-images/win/05-sc_query-w158.png)

   如果 `STATE` 未設為 `RUNNING`，則表示未安裝 HAXM。

如果您的電腦可支援 HAXM，但卻未安裝 HAXM，請使用下一節中的步驟來安裝 HAXM。

<a name="install-haxm-win"></a>

### <a name="installing-haxm"></a>安裝 HAXM

您可以從[Intel Hardware Accelerated Execution Manager](https://github.com/intel/haxm/releases) GitHub 版本頁面取得適用于 WINDOWS 的 HAXM 安裝套件。 使用下列步驟來下載並安裝 HAXM：

1. 從 Intel 網站，下載適用於 Windows 的最新 [HAXM 虛擬化引擎](https://github.com/intel/haxm/releases) \(英文\) 安裝程式。 直接從 Intel 網站下載 HAXM 安裝程式的優點，在於可確保您使用的是最新版本。

2. 執行 **intelhaxm android.exe** 來啟動 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   ![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/win/06-haxm-installer.png)

當您建立虛擬裝置時（請參閱[使用 Android Device Manager 來管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)），請務必選取**x86**型系統映射。 如果您使用 ARM 型系統映像，則虛擬裝置不會加速，且執行速度會變慢。

## <a name="troubleshooting"></a>疑難排解

如需針對硬體加速問題進行疑難排解的說明，請參閱 Android Emulator 的[疑難排解](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win)指南。

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>在 macOS 上加速 Android Emulator

下列虛擬化技術可用於加速 Android Emulator：

1. **Apple 的 Hypervisor 架構**。
   [Hypervisor](https://developer.apple.com/documentation/hypervisor) 是讓您能夠在 Mac 上執行虛擬機器的 macOS 10.10 和更新版本功能。

2. **Intel Hardware Accelerated Execution Manager (HAXM)**.
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)是執行 Intel cpu 之電腦的虛擬化引擎。

建議您使用 Hypervisor 架構來加速 Android 模擬器。 如果 Hypervisor 架構不適用於您的 Mac，則可以使用 HAXM。 如果符合下列準則，Android Emulator 會自動使用硬體加速：

- 開發電腦上有硬體加速可用，並已啓用。

- 模擬器正在執行為 **x86** 型虛擬裝置建立的系統映像。

> [!IMPORTANT]
>
> 您無法在另一部 VM 執行 VM 加速的模擬器，例如 VirtualBox、VMWare 或 Docker 所裝載的 VM。 您必須[直接在系統硬體上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)執行 Android 模擬器。

如需使用 Android Emulator 啟動和偵錯的資訊，請參閱 [Debugging on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上偵錯)。

<a name="hypervisor"></a>

## <a name="accelerating-with-the-hypervisor-framework"></a>使用 Hypervisor 架構加速

若要搭配 Hypervisor 架構使用 Android Emulator，您的 Mac 必須符合下列準則：

- 您的 Mac 必須執行 macOS 10.10 或更新版本。

- 您的 Mac CPU 必須能夠支援 Hypervisor 架構。

如果您的 Mac 符合這些準則，Android 模擬器即會自動使用 Hypervisor 架構來加速。 如果您不確定 Mac 是否支援 Hypervisor 架構，請參閱[疑難排解](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues)指南，以取得確認您的 Mac 是否支援 Hypervisor 的方法。

如果您的 Mac 不支援 Hypervisor 架構，您可以使用 HAXM 來加速 Android Emulator (如下所述)。

<a name="haxm-mac"></a>

## <a name="accelerating-with-haxm"></a>使用 HAXM 加速

如果您的 Mac 不支援 Hypervisor 架構 (或使用 macOS 10.10 以前的版本)，您可以使用 **Intel 的 Hardware Accelerated Execution Manager** ([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) 來加速 Android Emulator。

在您第一次搭配 HAXM 使用 Android Emulator 前，最好先確認已安裝 HAXM 並可供 Android Emulator 使用。

### <a name="verifying-haxm-support"></a>確認 HAXM 支援

您可以使用下列步驟來查看是否已安裝 HAXM：

1. 開啟終端機，並輸入下列命令：

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   此命令假設 Android SDK 安裝在 **~/Library/Developer/Xamarin/android-sdk-macosx** 的預設位置；如果不是，請將以上路徑修改為 Mac 上的 Android SDK 位置。

2. 如果已安裝 HAXM，上述命令會傳回一則類似於下列結果的訊息：

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   如果「未」** 安裝 HAXM，則會傳回類似於下列輸出的訊息：

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

如果未安裝 HAXM，請使用下一節中的步驟來安裝 HAXM。

<a name="install-haxm-mac"></a>

### <a name="installing-haxm"></a>安裝 HAXM

適用於 macOS 的 HAXM 安裝套件可從 [Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager) 頁面取得。 使用下列步驟來下載並安裝 HAXM：

1. 從 Intel 網站，下載適用於 macOS 的最新 [HAXM 虛擬化引擎](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。

2. 執行 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   [![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>疑難排解

如需針對硬體加速問題進行疑難排解的說明，請參閱 Android Emulator 的[疑難排解](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac)指南。

::: zone-end

## <a name="related-links"></a>相關連結

- [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (在 Android 模擬器上執行應用程式)
