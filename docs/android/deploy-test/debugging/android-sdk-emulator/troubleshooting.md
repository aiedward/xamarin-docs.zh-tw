---
title: Google Android 模擬器疑難排解
description: 如何識別及解決 Google Android 模擬器問題
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/04/2018
ms.openlocfilehash: 001fc21a519a251715d24b43acfdd4251b5fbc91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="google-android-emulator-troubleshooting"></a>Google Android 模擬器疑難排解

在本文中，將會說明 Google Android 模擬器 (和其解決方案) 的最常見警告訊息和問題。
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>效能警告

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

從 Visual Studio 2017 15.4 版開始，當您第一次將應用程式部署至「Android SDK 模擬器」時，可能會顯示效能警告對話方塊。 以下會說明這些警告對話方塊。

### <a name="computer-does-not-contain-an-intel-procesor"></a>電腦未包含 Intel 處理器

![電腦未包含 Intel 處理器](troubleshooting-images/01-no-intel-processor.png)

顯示此對話方塊時，表示您的電腦沒有 Intel 處理器，這是「Android SDK 模擬器」加速所需的處理器。 如果您的電腦沒有 Intel 處理器，建議您使用實體 Android 裝置來進行開發。

### <a name="hyper-v-is-installed-or-active"></a>Hyper-V 已安裝或作用中

![Hyper-V 已安裝或作用中](troubleshooting-images/02-hyper-v-active.png)

顯示此對話方塊時，表示 Hyper-V 已安裝或作用中，必須予以停用。 [停用 Hyper-V](#disable-hyperv)說明如何解決此問題。

### <a name="haxm-is-not-installed"></a>未安裝 HAXM

![未安裝 HAXM](troubleshooting-images/03-haxm-not-installed.png)

此對話方塊指出您的電腦有 Intel 處理器、已停用 Hyper-V，但未安裝 HAXM。
[安裝 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 說明了安裝 HAXM 的步驟。

### <a name="haxm-process-not-running"></a>HAXM 處理序未執行

![HAXM 處理序未執行](troubleshooting-images/04-haxm-process-not-running.png)

如果您的電腦有 Intel 處理器、已停用 Hyper-V、已安裝 Intel HAXM，但 HAXM 處理序未執行，就會顯示此對話方塊。 若要解決此問題，請開啟「命令提示字元」視窗，然後輸入下列命令：

```cmd
sc query intelhaxm
```

如果 HAXM 處理序正在執行，您應該會看到類似下方的輸出：

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```


如果 **STATE** 不是設定為 **RUNNING**，請參閱[如何使用 Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) \(英文\) 來解決問題。


### <a name="other-failures"></a>其他失敗

![其他失敗](troubleshooting-images/05-other-failure.png)

如果您的電腦有 Intel 處理器、已停用 Hyper-V、已安裝 Intel HAXM、HAXM 處理序正在執行，但模擬器因某個不明原因而無法啟動，就會顯示此對話方塊。
若要協助解決此錯誤，請參閱[如何使用 Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) \(英文\)。

### <a name="disabling-performance-warnings"></a>停用效能警告

如果您不想要看到效能警告，您可以將其停用。 在 Visual Studio 中，按一下 [工具] > [選項] > [Xamarin] > [Android 設定]，然後停用 [若不支援 AVD 加速即發出警告 (HAXM)] 選項：

[![停用 AVD 加速警告](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

從 Visual Studio for Mac 組建 7.2 (組建 559) 開始，當您第一次將應用程式部署至 Google Android 模擬器時，可能會顯示效能警告對話方塊。 以下會說明這些警告對話方塊。

### <a name="haxm-is-not-installed"></a>未安裝 HAXM

![未安裝 HAXM](troubleshooting-images/03-haxm-not-installed.png)

此對話方塊指出未安裝 HAXM。
[安裝 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 說明了安裝 HAXM 的步驟。

### <a name="haxm-process-not-running"></a>HAXM 處理序未執行

![HAXM 處理序未執行](troubleshooting-images/04-haxm-process-not-running.png)

如果 HAXM 處理序未執行，就會顯示此對話方塊。 如需詳細資訊以協助您解決此問題，請參閱[如何使用 Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)(英文\) 來解決問題。

### <a name="other-failures"></a>其他失敗

![其他失敗](troubleshooting-images/05-other-failure.png)

如果模擬器因某個不明的原因而無法啟動，就會顯示此對話方塊。 若要協助解決此錯誤，請參閱[如何使用 Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) \(英文\) 來解決問題。

-----


## <a name="solutions-to-common-problems"></a>常見問題的解決方案

許多常見的 Google Android 模擬器問題都可透過對電腦進行設定變更或安裝額外軟體來解決。 下列各節將說明這些問題並提供解決方案。


### <a name="deployment-issues"></a>部署問題

如果您收到有關無法在模擬器上安裝 APK 或無法執行 Android Debug Bridge (**adb**) 的錯誤，請確認 Android SDK 可連線至您的模擬器。 若要這麼做，請使用下列步驟：

1. 從 **Android 虛擬裝置 (AVD) 管理員**啟動模擬器 (選取您的虛擬裝置並按一下 [啟動])。

2. 開啟「命令提示字元」，然後移至安裝 **adb** 的資料夾。 例如，在 Windows 上，這可能位於：**C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**。

3. 輸入下列命令：

   ```shell
   adb devices
   ```

4. 如果可從 Android SDK 存取模擬器，模擬器應該會顯示在連接的裝置清單中。 例如: 

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. 如果模擬器未出現在此清單中，請啟動 [Android SDK 管理員]、套用所有更新，然後重新嘗試啟動模擬器。



### <a name="haxm-issues"></a>HAXM 問題

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果 Google Android 模擬器未正確啟動，這通常是 HAXM 的相關問題所造成。 HAXM 問題通常是與其他虛擬化技術發生衝突、設定不正確或 HAXM 驅動程式過期所導致的結果。

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>HAXM 虛擬化衝突

HAXM 可能與其他使用虛擬化的技術發生衝突，例如 Hyper-V、Windows Device Guard 及某些防毒軟體：

- **Hyper-V** &ndash; 如果您使用已啟用 Hyper-V 的 Windows，請依照[停用 Hyper-V](#disable-hyperv) 中所述的步驟進行操作。

- **Device Guard** &ndash; Device Guard 和 Credential Guard 可防止在 Windows 機器上停用 Hyper-V。 若要停用 Device Guard 和 Credential Guard，請參閱[停用 Device Guard](#disable-devguard)。

- **防毒軟體** &ndash; 如果您正在執行使用硬體輔助虛擬化的防毒軟體 (例如 Avast)，請將此軟體停用或解除安裝、重新開機，然後重試「Android SDK 模擬器」。


#### <a name="incorrect-bios-settings"></a>不正確的 BIOS 設定

如果您在 Windows PC 上使用 HAXM，除非在 BIOS 中啟用虛擬化技術 (Intel VT-x)，否則 HAXM 將無法運作。 如果已停用 VT-x，則在嘗試啟動 Google Android 模擬器時，您將會收到類似下方的錯誤：

**此電腦符合 HAXM 的要求，但未開啟 Intel 虛擬化技術 (VT-x)。**

若要更正此錯誤，請讓電腦開機進入 BIOS，同時啟用 VT-x 和 SLAT (第二層位址轉譯)，然後讓電腦重新啟動回到 Windows。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果 Google Android 模擬器未正確啟動，這通常是 HAXM 的相關問題所造成。 HAXM 問題通常是與其他虛擬化技術發生衝突、設定不正確或 HAXM 驅動程式過期所導致的結果。 請使用[安裝 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm)中所述的步驟，來嘗試重新安裝 HAXM 驅動程式。

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>停用 Hyper-V

如果您使用 Windows 且已啟用 Hyper-V，您必須將 Hyper-V 停用並重新啟動電腦，才能安裝並使用 HAXM。 您可以遵循下列步驟從控制台停用 Hyper-V：

1. 在 Windows 搜尋方塊中，輸入**程式和**，然後按一下 [程式和功能] 搜尋結果。

2. 在控制台的 [程式和功能] 對話方塊中，按一下 [開啟或關閉 Windows 功能]：

    ![開啟或關閉 Windows 功能](troubleshooting-images/win/07-turn-windows-features.png)

3. 取消選取 [Hyper-V]，然後重新啟動電腦：

    ![正在 [Windows 功能] 對話方塊中停用 Hyper-V](troubleshooting-images/win/08-uncheck-hyper-v.png)

或者，您可以使用下列 Powershell Cmdlet 來停用 Hyper-V：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不可同時啟動。 不幸的是，目前並沒有任何方法可在未重新啟動電腦的情況下，於 Hyper-V 和 HAXM 之間進行切換。 如果您想要使用 [Visual Studio 的 Android 模擬器](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (其需要使用 Hyper-V)，您將無法在未重新開機的情況下使用 Android SDK 模擬器。 同時使用 Hyper-V 和 HAXM 的其中一種方式是建立多重開機設定，如[建立沒有 Hypervisor 的開機項目](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/) \(英文\) 中所述。

如果已啟用 Device Guard 和 Credential Guard，在某些情況下使用上述步驟將無法成功停用 Hyper-V。 如果您無法停用 Hyper-V (或是在停用後仍然無法安裝 HAXM)，請使用下一節的步驟來停用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>停用 Device Guard

Device Guard 和 Credential Guard 可能會防止在 Windows 電腦上停用 Hyper-V。 此問題通常會發生在已加入網域且由組織進行設定和控制的電腦上。
在 Windows 10 上，使用下列步驟來查看 **Device Guard** 是否正在執行：

1. 在 [Windows 搜尋] 中，輸入**系統資訊**以啟動 [系統資訊] 應用程式。

2. 在 [系統摘要] 中，查看 [Device Guard 虛擬化型安全性] 是否存在且處於 [執行中] 狀態：

   [![Device Guard 存在且正在執行](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

如果已啟用 Device Guard，請使用下列步驟來停用它：

1. 確認 [Hyper-V] 已停用 (位於 [開啟或關閉 Windows 功能] 下方)，如上一節所述。

2. 在 Windows 搜尋方塊中，輸入 **gpedit**，然後選取 [編輯群組原則] 搜尋結果。 這會啟動 [本機群組原則編輯器]。

3. 在 [本機群組原則編輯器] 中，瀏覽至 [電腦設定] > [系統管理範本] > [系統] > [Device Guard]：

   [![[本機群組原則編輯器] 中的 Device Guard](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

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


# <a name="visual-studiotabvsmac"></a>[Visual Studio](#tab/vsmac)

OS X 或 macOS 不提供 Hyper-V。

-----

