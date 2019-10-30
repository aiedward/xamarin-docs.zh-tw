---
title: Android Emulator 疑難排解
description: 本文說明如何診斷和因應使用 Android Emulator 時可能發生的問題。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: 675e6cebab95d2a997bf48a6c9231b5e8c661342
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020849"
---
# <a name="android-emulator-troubleshooting"></a>Android Emulator 疑難排解

_本文說明設定和執行 Android Emulator 時最常見的警告訊息和問題。此外，它也會說明解決這些錯誤的解決方案，以及各種疑難排解秘訣，以協助您診斷模擬器的問題。_

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>Windows 上的部署問題

當您部署應用程式時，模擬器可能會顯示一些錯誤訊息。 以下說明最常見的錯誤及解決方式。

### <a name="deployment-errors"></a>部署錯誤

如果您看到有關無法在模擬器上安裝 APK 或無法執行 Android Debug Bridge (**adb**) 的錯誤，請確認 Android SDK 可連線至您的模擬器。 若要確認模擬器連線，請使用下列步驟：

1. 從 **Android Device Manager**啟動模擬器 (選取您的虛擬裝置並按一下 [啟動])。

2. 開啟命令提示字元，然後移至安裝 **adb** 的資料夾。 如果 Android SDK 安裝在其預設位置，則 **adb** 會位於 **C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**；如果不是，請將此路徑修改為您電腦上的 Android SDK 位置。

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

### <a name="mmio-access-error"></a>MMIO 存取錯誤

若顯示 [發生 MMIO 存取錯誤] 訊息，請重新啟動模擬器。

<a name="gps-win" />

## <a name="missing-google-play-services"></a>遺漏 Google Play Services

若您在模擬器中執行的虛擬裝置未安裝 Google Play Services 或 Google Play 商店，則這種情況通常是由於建立的虛擬裝置未包含這些套件所造成。 當您建立虛擬裝置時 (請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md))，請務必選取下列其中一或兩個選項：

- **Google API** &ndash; 在虛擬裝置中包含 Google Play Services。
- **Google Play 商店** &ndash; 在虛擬裝置中包含 Google Play 商店。

例如，此虛擬裝置將會包含 Google Play Services 和 Google Play 商店：

[![啟用 Google Play Services 和 Google Play 商店的範例 AVD](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Google Play 商店影像僅適用於某些基底裝置類型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

<a name="perf-win" />

## <a name="performance-issues"></a>效能問題

效能問題通常是由於下列其中一個問題所造成：

- 模擬器正在執行但未使用硬體加速。

- 在模擬器中執行的虛擬裝置未使用 x86 型系統映像。

下列各節會更詳細地說明這些案例。

### <a name="hardware-acceleration-is-not-enabled"></a>未啟用硬體加速

如果未啟用硬體加速，從 Device Manager 啟動虛擬裝置將會產生包含錯誤訊息的對話方塊，指出未正確設定 Windows Hypervisor 平台 (WHPX)：

![Device Manager 警告範例](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

如果未顯示此錯誤訊息，請參閱下面的[硬體加速問題](#accel-issues-win)，以了解您可以採取哪些步驟來確認及啟用硬體加速。

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>已啟用加速但模擬器執行速度太慢 

此問題的常見原因是未在您的虛擬裝置 (AVD) 中使用 x86 型映像。 當您建立虛擬裝置時 (請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md))，請務必選取 x86 型系統映像：

[![針對虛擬裝置選取 x86 系統映像](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)

<a name="accel-issues-win" />

## <a name="hardware-acceleration-issues"></a>硬體加速問題

無論您使用 Hyper-V 或 HAXM 進行硬體加速，都可能會在電腦上遇到設定問題或與其他軟體衝突。 您可以開啟命令提示字元並輸入下列命令，來確認是否已啟用硬體加速 (以及模擬器使用哪種加速方法)：

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

此命令假設 Android SDK 安裝在 **C:\\Program Files (x86)\\Android\\android-sdk** 的預設位置；如果不是，請將以上路徑修改為您電腦上的 Android SDK 位置。

### <a name="hardware-acceleration-not-available"></a>硬體加速無法使用

如果 Hyper-V 可供使用，則會從 **emulator-check.exe accel** 命令傳回類似下列範例的訊息：

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

如果 HAXM 可供使用，則會傳回類似下列範例的訊息：

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

如果硬體加速無法使用，則會顯示類似下列範例的訊息 (如果模擬器找不到 Hyper-V，則會尋找 HAXM)：

```cmd
HAXM is not installed on this machine
```

如果硬體加速無法使用，請參閱[使用 Hyper-V 加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win)，以了解如何在您的電腦上啟用硬體加速。

### <a name="incorrect-bios-settings"></a>不正確的 BIOS 設定

如果未正確設定 BIOS 支援硬體加速，當您執行 **emulator-check.exe accel** 命令時，則會顯示類似下列範例的訊息：

```cmd
VT feature disabled in BIOS/UEFI
```

若要修正此問題，請讓您的電腦重新開機進入 BIOS 並啟用下列選項：

- 虛擬化技術 (視主機板製造商而定，可能會有不同的標籤)。
- 硬體強制執行的資料執行防止。

如果已啟用硬體加速並正確設定 BIOS，模擬器應該會使用硬體加速順利執行。
不過，由於 Hyper-V 和 HAXM 的特定問題，可能仍會造成問題，如下所述。

### <a name="hyper-v-issues"></a>Hyper-V 問題

在某些情況下，在 [開啟或關閉 Windows 功能] 對話方塊中同時啟用 **Hyper-V** 和 **Windows Hypervisor 平台**可能會無法適當地啟用 Hyper-V。 若要確認是否已啟用 Hyper-V，請使用下列步驟：

1. 在 Windows 搜尋方塊中，輸入 **powershell**。

2. 以滑鼠右鍵按一下搜尋結果中的 **Windows PowerShell**，然後選取 [以系統管理員身分執行]。

3. 在 PowerShell 主控台中，輸入下列命令：

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    如果未啟用 Hyper-V，則會顯示類似下列範例的訊息，指出 Hyper-V 的狀態為 [已停用]：

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. 在 PowerShell 主控台中，輸入下列命令：

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```

    如果未啟用 Hypervisor，則會顯示類似下列範例的訊息，指出 Hypervisor 平台的狀態為 [已停用]：

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

如果未啟用 Hyper-V 及/或 Hypervisor 平台，請使用下列 PowerShell 命令予以啟用：

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

這些命令完成之後，請重新開機。 

如需啟用 Hyper-V 的詳細資訊 (包括使用部署映像服務與管理工具啟用 Hyper-V 的技術)，請參閱[安裝 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。

### <a name="haxm-issues"></a>HAXM 問題

HAXM 問題通常是與其他虛擬化技術發生衝突、設定不正確或 HAXM 驅動程式過期所導致的結果。

### <a name="haxm-process-is-not-running"></a>HAXM 處理序未執行

如果已安裝 HAXM，您可以開啟命令提示字元並輸入下列命令，來確認 HAXM 處理序是否正在執行：

```cmd
sc query intelhaxm
```

如果 HAXM 處理序正在執行，您應該會看到類似下列結果的輸出：

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

如果 `STATE` 不是設定為 `RUNNING`，請參閱 [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (如何使用 Intel Hardware Accelerated Execution Manager) 來解決問題。

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>HAXM 虛擬化衝突

HAXM 可能與其他使用虛擬化的技術發生衝突，例如 Hyper-V、Windows Device Guard 及某些防毒軟體：

- **Hyper-V** &ndash; 如果您使用的是 **Windows 10 2018 年 4 月更新 (組建1803)** 之前的 Windows 版本，而且啟用了 Hyper-V，請遵循[停用 Hyper-V](#disable-hyperv) 中的步驟執行，以便啟用 HAXM。

- **Device Guard** &ndash; Device Guard 和 Credential Guard 可防止在 Windows 機器上停用 Hyper-V。 若要停用 Device Guard 和 Credential Guard，請參閱[停用 Device Guard](#disable-devguard)。

- **防毒軟體** &ndash; 如果您正在執行使用硬體輔助虛擬化的防毒軟體 (例如 Avast)，請將此軟體停用或解除安裝、重新開機，然後重試 Android Emulator。

#### <a name="incorrect-bios-settings"></a>不正確的 BIOS 設定

如果您在 Windows PC 上使用 HAXM，除非在 BIOS 中啟用虛擬化技術 (Intel VT-x)，否則 HAXM 將無法運作。 如果已停用 VT-x，則當您嘗試啟動 Android Emulator 時，會收到類似下列錯誤：

**此電腦符合 HAXM 的要求，但未開啟 Intel 虛擬化技術 (VT-x)。**

若要修正此錯誤，請讓電腦開機進入 BIOS，同時啟用 VT-x 和 SLAT (第二層位址轉譯)，然後讓電腦重新啟動回到 Windows。

<a name="disable-hyperv" />

#### <a name="disabling-hyper-v"></a>停用 Hyper-V

如果您使用的是 **Windows 10 2018 4 月更新 (1803 組建)** 以前的 Windows 版本，而且啟用了 HYPER-V，您必須停用 HYPER-V 並重新啟動電腦，才能安裝及使用 HAXM。 如果您使用 **Windows 10 2018 4 月更新 (1803 組建)** 或更新版本，Android Emulator 27.2.7 版或更新版本可以使用 HYPER-V (不是 HAXM) 為硬體加速，因此沒有必要停用 HYPER-V。

您可以遵循下列步驟從控制台停用 Hyper-V：

1. 在 Windows 搜尋方塊中輸入 **Windows 功能**，然後在搜尋結果中選取 [開啟或關閉 Windows 功能]。

2. 取消核取 [Hyper-V]：

    ![正在 [Windows 功能] 對話方塊中停用 Hyper-V](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. 重新啟動電腦。

或者，您可以使用下列 PowerShell 命令來停用 Hyper-V Hypervisor：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不可同時啟動。 不幸的是，不重新啟動電腦就沒有辦法在 Hyper-V 和 HAXM 之間進行切換。 

如果已啟用 Device Guard 和 Credential Guard，在某些情況下使用上述步驟將無法成功停用 Hyper-V。 如果您無法停用 Hyper-V (或是在停用後仍然無法安裝 HAXM)，請使用下一節的步驟來停用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>停用 Device Guard

Device Guard 和 Credential Guard 可能會防止在 Windows 電腦上停用 Hyper-V。 此情況通常會發生在已加入網域且由組織進行設定和控制的電腦上。 在 Windows 10 上，使用下列步驟來查看 **Device Guard** 是否正在執行：

1. 在 Windows 搜尋方塊中輸入**系統資訊**，然後在搜尋結果中選取 [系統資訊]。

2. 在 [系統摘要] 中，查看 [Device Guard 虛擬化型安全性] 是否存在且處於 [執行中] 狀態：

   [![Device Guard 存在且正在執行](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

如果已啟用 Device Guard，請使用下列步驟來停用它：

1. 確認 [Hyper-V] 已停用 (位於 [開啟或關閉 Windows 功能] 下方)，如上一節所述。

2. 在 Windows 搜尋方塊中，輸入 **gpedit**，然後選取 [編輯群組原則] 搜尋結果。 這些步驟會啟動 [本機群組原則編輯器]。

3. 在 [本機群組原則編輯器] 中，瀏覽至 [電腦設定] > [系統管理範本] > [系統] > [Device Guard]：

   [![[本機群組原則編輯器] 中的 Device Guard](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. 將 [開啟虛擬化型安全性] 變更為 [已停用] (如上所示)，然後結束 [本機群組原則編輯器]。

5. 在 Windows 搜尋方塊中，輸入 **cmd**。 當 [命令提示字元] 在搜尋結果中出現時，以滑鼠右鍵按一下 [命令提示字元]，然後選取 [以系統管理員身分執行]。

6. 複製下列命令，並將之貼入命令提示字元視窗 (如果磁碟機 **Z:** 正在使用中，請改為挑選未使用的磁碟機代號)：

    ```cmd
    mountvol Z: /s
    copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
    bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
    bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
    mountvol Z: /d
    ```

7. 重新啟動電腦。 在開機畫面上，您應該會看到類似下列訊息的提示：

   **Do you want to disable Credential Guard?** \(是否要停用 Credential Guard？\)

   出現提示時，請按下指定的按鍵來停用 Credential Guard。

8. 重新啟動電腦之後，再次檢查以確定 Hyper-V 已停用 (如先前步驟中所述)。

如果 Hyper-V 仍未停用，您已加入網域之電腦的原則可能正在阻止您停用 Device Guard 或 Credential Guard。 在此情況下，您可以要求網域管理員給予特例，讓您能夠選擇不使用 Credential Guard。 或者，如果您必須使用 HAXM，您可以使用未加入網域的電腦。

## <a name="additional-troubleshooting-tips"></a>其他疑難排解祕訣

下列建議在診斷 Android Emulator 問題方面通常很有幫助。

### <a name="starting-the-emulator-from-the-command-line"></a>從命令列啟動模擬器

如果模擬器並未執行，您可以從命令列將它啟動 (而不是從 Visual Studio 中) 來檢視其輸出。 一般而言，Android Emulator AVD 映像會儲存在下列位置 (請以您的 Windows 使用者名稱取代 *username*)：

**C:\\Users\\*username*\\.android\\avd**

您可以傳入 AVD 的資料夾名稱來使用此位置的 AVD 映像啟動模擬器。 例如，此命令會啟動名為 **Pixel_API_27** 的 AVD：

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

此範例假設 Android SDK 安裝在 **C:\\Program Files (x86)\\Android\\android-sdk** 的預設位置；如果不是，請將以上路徑修改為您電腦上的 Android SDK 位置。

當您執行此命令時，它會在模擬器啟動時產生多行輸出。 具體而言，如果硬體加速已啟用並正常運作 (在此範例中使用 HAXM 進行硬體加速)，則會列印類似下列範例的程式碼行：

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>檢視 Device Manager 記錄

通常，您可以檢視 Device Manager 記錄來診斷模擬器問題。 這些記錄會寫入下列位置：

**C:\\Users\\*username*\\AppData\\Roaming\\XamarinDeviceManager**

您可以使用文字編輯器 (例如 [記事本]) 來檢視每個 **DeviceManager.log** 檔案。 下列範例記錄項目指出在電腦上找不到 HAXM：

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>macOS 上的部署問題

當您部署應用程式時，模擬器可能會顯示一些錯誤訊息。 以下說明最常見的錯誤及解決方式。

### <a name="deployment-errors"></a>部署錯誤

如果您看到有關無法在模擬器上安裝 APK 或無法執行 Android Debug Bridge (**adb**) 的錯誤，請確認 Android SDK 可連線至您的模擬器。 若要確認連線，請使用下列步驟：

1. 從 **Android Device Manager**啟動模擬器 (選取您的虛擬裝置並按一下 [啟動])。

2. 開啟命令提示字元，然後移至安裝 **adb** 的資料夾。 如果 Android SDK 安裝在其預設位置，則 **adb** 會位於 **~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb**；如果不是，請將此路徑修改為您電腦上的 Android SDK 位置。

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

### <a name="mmio-access-error"></a>MMIO 存取錯誤

如果顯示 [發生 MMIO 存取錯誤]，請重新啟動模擬器。

<a name="gps-mac" />

## <a name="missing-google-play-services"></a>遺漏 Google Play Services

如果您在模擬器中執行的虛擬裝置未安裝 Google Play Services 或 Google Play 商店，這種情況通常是由於建立的虛擬裝置未包含這些套件所造成。 當您建立虛擬裝置時 (請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md))，請務必選取下列其中一或兩項：

- **Google API** &ndash; 在虛擬裝置中包含 Google Play Services。
- **Google Play 商店** &ndash; 在虛擬裝置中包含 Google Play 商店。

例如，此虛擬裝置將會包含 Google Play Services 和 Google Play 商店：

[![啟用 Google Play Services 和 Google Play 商店的範例 AVD](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Google Play 商店影像僅適用於某些基底裝置類型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

<a name="perf-mac" />

## <a name="performance-issues"></a>效能問題

效能問題通常是由於下列其中一個問題所造成：

- 模擬器正在執行但未使用硬體加速。

- 在模擬器中執行的虛擬裝置未使用 x86 型系統映像。

下列各節會更詳細地說明這些案例。

### <a name="hardware-acceleration-is-not-enabled"></a>未啟用硬體加速

如果未啟用硬體加速，當您將應用程式部署至 Android Emulator 時，則會快顯包含訊息的對話方塊，例如 [裝置會在未加速狀態下執行]。 如果您不確定電腦上是否已啟用硬體加速 (或您想要了解哪個技術提供加速)，請參閱下面的[硬體加速問題](#accel-issues-mac)，以了解您可以採取哪些步驟來確認及啟用硬體加速。

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>已啟用加速但模擬器執行速度太慢 

此問題的常見原因是未在虛擬裝置中使用 x86 型映像。 當您建立虛擬裝置時 (請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md))，請務必選取 x86 型系統映像：

[![針對虛擬裝置選取 x86 系統映像](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac" />

## <a name="hardware-acceleration-issues"></a>硬體加速問題

無論您使用 Hypervisor 架構或 HAXM 進行模擬器的硬體加速，都可能會遇到由於安裝問題或 macOS 版本已過期所造成的問題。 下列各節可協助您解決此問題。

<a name="hypervisor-issues" />

### <a name="hypervisor-framework-issues"></a>Hypervisor 架構問題

如果您在新版 Mac 上使用 macOS 10.10 或更新版本，Android Emulator 會自動使用 Hypervisor 架構進行硬體加速。 不過，某些舊版 Mac 或執行 macOS 10.10 以前版本的 Mac 可能不會提供 Hypervisor 架構支援。

若要判斷您的 Mac 是否支援 Hypervisor 架構，請開啟終端機並輸入下列命令：

```bash
sysctl kern.hv_support
```

如果您的 Mac 支援 Hypervisor 架構，上述命令將會傳回下列結果：

```bash
kern.hv_support: 1
```

如果您的 Mac 上沒有 Hypervisor 架構可用，您可以遵循[使用 HAXM 加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac)中的步驟執行，以改用 HAXM 進行加速。

### <a name="haxm-issues"></a>HAXM 問題

如果 Android Emulator 未正確啟動，此問題通常是由於 HAXM 的相關問題所造成。 HAXM 問題通常是與其他虛擬化技術發生衝突、設定不正確或 HAXM 驅動程式過期所導致的結果。 請使用[安裝 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac)中所述的步驟，來嘗試重新安裝 HAXM 驅動程式。

## <a name="additional-troubleshooting-tips"></a>其他疑難排解祕訣

下列建議在診斷 Android Emulator 問題方面通常很有幫助。

### <a name="starting-the-emulator-from-the-command-line"></a>從命令列啟動模擬器

如果模擬器並未執行，您可以從命令列將它啟動 (而不是從 Visual Studio for Mac 中) 來檢視其輸出。 一般而言，Android Emulator AVD 映像會儲存在下列位置：

**~/.android/avd**

您可以傳入 AVD 的資料夾名稱來使用此位置的 AVD 映像啟動模擬器。 例如，此命令會啟動名為 **Pixel_2_API_28** 的 AVD：

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

如果 Android SDK 安裝在其預設位置，則模擬器會位於 **~/Library/Developer/Xamarin/android-sdk-macosx/emulator** 目錄；如果不是，請將此路徑修改為您電腦上的 Android SDK 位置。

當您執行此命令時，它會在模擬器啟動時產生多行輸出。 具體而言，如果硬體加速已啟用並正常運作 (在此範例中使用 Hypervisor 架構進行硬體加速)，則會列印類似下列範例的程式碼行：

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>檢視 Device Manager 記錄

通常，您可以檢視 Device Manager 記錄來診斷模擬器問題。 這些記錄會寫入下列位置：

**~/Library/Logs/XamarinDeviceManager**

您可以按兩下 **Android Devices.log** 檔案以在主控台應用程式中開啟它來檢視每個檔案。 下列範例記錄項目指出找不到 HAXM：

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
