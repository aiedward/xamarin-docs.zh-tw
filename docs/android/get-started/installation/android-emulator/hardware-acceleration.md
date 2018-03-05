---
title: "Android Emulator 硬體加速"
description: "如何準備您的電腦以達到最大的 Android SDK 模擬器效能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 12/22/2017
ms.openlocfilehash: 53dc85cab94bdf692e088d7c6eea6916d283ba84
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Android Emulator 硬體加速

由於 Android SDK 模擬器在沒有硬體加速的情況下會變得非常緩慢，因此建議使用 Intel 的 HAXM (Hardware Accelerated Execution Manager) 來大幅提升 Android SDK 模擬器的效能。

<a name="haxm-overview" />

## <a name="haxm-overview"></a>HAXM 概觀

HAXM 為硬體輔助的虛擬化引擎 (Hypervisor)，使用 Intel 虛擬化技術 (VT) 來加速主機電腦上的 Android 應用程式模擬。 透過與由 Intel 和官方 Android SDK 管理員提供的 Android x86 模擬器映像搭配使用，HAXM 就能夠在已啟用 VT 的系統上加快 Android 模擬的速度。 如果您是在配備具有 VT 功能之 Intel CPU 的電腦上進行開發，您可以利用 HAXM 大幅加速 Android SDK 模擬器 (如果您不確定 CPU 是否支援 VT，請參閱[判斷您的處理器是否支援 Intel 虛擬化技術](https://www.intel.com/content/www/us/en/support/processors/000005486.html) \(英文\))。

Android SDK 模擬器會自動在 HAXM 可供使用時加以使用。 當您選取 **x86** 型虛擬裝置時 (如[設定及使用](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中所述)，該虛擬裝置將使用 HAXM 進行硬體加速。 在您第一次使用 Android SDK 模擬器之前，最好先確認 HAXM 已安裝並可供 Android SDK 模擬器使用。

> [!NOTE]
> **注意：**您無法在虛擬機器上執行 HAXM。

<a name="verify-haxm" />

## <a name="verifying-haxm-installation"></a>驗證 HAXM 安裝

您可以在模擬器啟動時，藉由檢視 [正在啟動 Android Emulator] 視窗，以檢查 HAXM 是否可供使用。 若要啟動 Android SDK 模擬器，請執行下列動作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按一下 [工具] > [Android] > [Android Emulator 管理員] 來啟動 Android Emulator 管理員：

    [![[Android Emulator 管理員] 功能表項目位置](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/win/11-perf-warn.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如 **VisualStudio\_android-23\_x86\_phone**)，按一下 [開始]，然後按一下 [啟動]：

    ![使用預設虛擬裝置映像啟動 Android SDK 模擬器](hardware-acceleration-images/win/02-start-default-avd.png)

4. 模擬器啟動時，請連一 [正在啟動 Android Emulator] 對話方塊視窗。 如果已安裝 HAXM，您將會看到「HAX 正在運作且模擬器會在快速虛擬模式中執行」的訊息，如這個螢幕擷取畫面所示：

    ![HAXM 在 [正在啟動 Android Emulator] 對話方塊中顯示為可用](hardware-acceleration-images/win/03-haxm-detected.png)

   如果您未看到此訊息，則可能未安裝 HAXM。 例如，以下螢幕擷取畫面顯示在無法使用 HAXM 時，您可能會看到的訊息：

    ![這台電腦上沒有 HAXM 可供使用](hardware-acceleration-images/win/04-haxm-error.png)

   如果您的電腦上沒有 HAXM 可供使用，請使用下一節的步驟來安裝 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按一下 [工具] > [Google 模擬器管理員] 來啟動 Android Emulator 管理員：

    [![[Android Emulator 管理員] 功能表項目位置](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png)

2. 如果您看到類似下方的 [效能警告] 對話方塊，便代表您的電腦上尚未安裝或正確設定 HAXM：

    ![顯示 HAXM 尚未就緒的 [效能警告] 對話方塊](hardware-acceleration-images/mac/04-avd-warning.png)

   如果出現類似此對話方塊的 [效能警告] 對話方塊，請參閱[效能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)以找出原因並解決根本問題。

3. 選取 **x86** 映像 (例如 **Android\_Accelerated\_x86**)，按一下 [開始]，然後按一下 [啟動]：

    [![使用預設虛擬裝置映像啟動 Android SDK 模擬器](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png)

3. 模擬器啟動時，請連一 [正在啟動 Android Emulator] 對話方塊視窗。 如果已安裝 HAXM，您將會看到「HAX 正在運作且模擬器會在快速虛擬模式中執行」的訊息，如這個螢幕擷取畫面所示：

    ![HAXM 在 [正在啟動 Android Emulator] 對話方塊中顯示為可用](hardware-acceleration-images/mac/03-haxm-detected.png)

   如果您的電腦上沒有 HAXM 可供使用 (例如，如果您看到類似「請確定 Intel HAXM 已正確安裝且可使用」的錯誤訊息)，請使用下一節的步驟來安裝 HAXM。


-----

<a name="install-haxm" />

## <a name="installing-haxm"></a>安裝 HAXM

如果模擬器無法啟動，可能必須手動安裝 HAXM。 適用於 Windows 和 macOS 的 HAXM 安裝套件，可從 [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager) \(英文\) 頁面取得。 使用下列步驟來手動下載並安裝 HAXM：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 從 Intel 網站，下載適用於 Windows 的最新 [HAXM 虛擬化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。 直接從 Intel 網站下載 HAXM 安裝程式的優點，在於可確保您使用的是最新版本。

   或者，您可以使用 SDK 管理員來下載 HAXM 安裝程式 (在 SDK 管理員中，按一下 [工具] > [額外項目] > [Intel x86 Emulator Accelerator (HAXM 安裝程式)])。 Android SDK 通常會將 HAXM 安裝程式下載到下列位置：

   **C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   請注意，SDK 管理員不會安裝 HAXM，它只會將 HAXM 安裝程式下載到上述位置；您仍然必須以手動方式啟動它。

2. 執行 **intelhaxm android.exe** 來啟動 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   ![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/win/05-haxm-installer.png)

如果您看到下列錯誤對話方塊 (_這台電腦不支援 Intel 虛擬化技術 (VT-x)，或是 Hyper-V 正以獨佔方式使用它_)，則必須先停用 Hyper-V，才能安裝 HAXM：

![因發生 Hyper-V 衝突而導致無法安裝 HAXM](hardware-acceleration-images/win/06-cant-install-haxm.png)

下一節將說明如何停用 Hyper-V。

<a name="disable-hyperv" />

## <a name="disabling-hyper-v"></a>停用 Hyper-V

如果您使用 Windows 且已啟用 Hyper-V，您必須將 Hyper-V 停用並重新啟動電腦，才能安裝並使用 HAXM。 您可以遵循下列步驟從控制台停用 Hyper-V：

1. 在 Windows 搜尋方塊中，輸入**程式和**，然後按一下 [程式和功能] 搜尋結果。

2. 在控制台的 [程式和功能] 對話方塊中，按一下 [開啟或關閉 Windows 功能]：

    ![開啟或關閉 Windows 功能](hardware-acceleration-images/win/07-turn-windows-features.png)

3. 取消選取 [Hyper-V]，然後重新啟動電腦：

    ![正在 [Windows 功能] 對話方塊中停用 Hyper-V](hardware-acceleration-images/win/08-uncheck-hyper-v.png)

或者，您可以使用下列 Powershell Cmdlet 來停用 Hyper-V：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不可同時啟動。 不幸的是，目前並沒有任何方法可在未重新啟動電腦的情況下，於 Hyper-V 和 HAXM 之間進行切換。 如果您想要使用 [Visual Studio 的 Android 模擬器](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (其需要使用 Hyper-V)，您將無法在未重新開機的情況下使用 Android SDK 模擬器。 同時使用 Hyper-V 和 HAXM 的其中一種方式是建立多重開機設定，如[建立沒有 Hypervisor 的開機項目](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/) \(英文\) 中所述。

如果已啟用 Device Guard 和 Credential Guard，在某些情況下使用上述步驟將無法成功停用 Hyper-V。 如果您無法停用 Hyper-V (或是在停用後仍然無法安裝 HAXM)，請使用下一節的步驟來停用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

## <a name="disabling-device-guard"></a>停用 Device Guard

Device Guard 和 Credential Guard 可能會防止在 Windows 電腦上停用 Hyper-V。 此問題通常會發生在已加入網域且由組織進行設定和控制的電腦上。
在 Windows 10 上，使用下列步驟來查看 **Device Guard** 是否正在執行：

1. 在 [Windows 搜尋] 中，輸入**系統資訊**以啟動 [系統資訊] 應用程式。

2. 在 [系統摘要] 中，查看 [Device Guard 虛擬化型安全性] 是否存在且處於 [執行中] 狀態：

   [![Device Guard 存在且正在執行](hardware-acceleration-images/win/09-device-guard-sml.png)](hardware-acceleration-images/win/09-device-guard.png)

如果已啟用 Device Guard，請使用下列步驟來停用它：

1. 確認 [Hyper-V] 已停用 (位於 [開啟或關閉 Windows 功能] 下方)，如上一節所述。

2. 在 Windows 搜尋方塊中，輸入 **gpedit**，然後選取 [編輯群組原則] 搜尋結果。 這會啟動 [本機群組原則編輯器]。

3. 在 [本機群組原則編輯器] 中，瀏覽至 [電腦設定] > [系統管理範本] > [系統] > [Device Guard]：

   [![[本機群組原則編輯器] 中的 Device Guard](hardware-acceleration-images/win/10-group-policy-editor-sml.png)](hardware-acceleration-images/win/10-group-policy-editor.png)

4. 將 [開啟虛擬化型安全性] 變更為 [已停用] (如上所示)，然後結束 [本機群組原則編輯器]。

5. 在 Windows 搜尋方塊中，輸入 **cmd**。 當 [命令提示字元] 在搜尋結果中出現時，以滑鼠右鍵按一下 [命令提示字元]，然後選取 [以系統管理員身分執行]。

6. 複製下列命令，並將之貼入命令提示字元視窗 (如果磁碟機 **Z:** 正在使用中，請改為挑選未使用的磁碟機代號)：

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. 重新啟動電腦。 在開機畫面上，您應該會看到如下的提示：

   **Do you want to disable Credential Guard?** \(是否要停用 Credential Guard？\)

   出現提示時，請按下指定的按鍵來停用 Credential Guard。

8. 重新啟動電腦之後，再次檢查以確定 Hyper-V 已停用 (如先前步驟中所述)。

如果 Hyper-V 仍未停用，您已加入網域之電腦的原則可能正在阻止您停用 Device Guard 或 Credential Guard。 在此情況下，您可以要求網域管理員給予特例，讓您能夠選擇不使用 Credential Guard。 或者，您可以使用未加入網域的電腦來使用 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 從 Intel 網站，下載適用於 macOS 的最新 [HAXM 虛擬化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) \(英文\) 安裝程式。

2. 執行 HAXM 安裝程式。 接受安裝程式對話方塊中的預設值：

   [![Intel Hardware Accelerated Execution Manager 安裝視窗](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png)

-----
