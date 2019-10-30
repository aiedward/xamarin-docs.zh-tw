---
title: 如何徹底將 Xamarin for Visual Studio 解除安裝？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: ed0171c2b6bd98e5b29ec100d0235131d36acb05
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013345"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>如何徹底將 Xamarin for Visual Studio 解除安裝？

1. 從 Windows [控制台] 中，卸載下列任何一項：

    - Xamarin
    - Xamarin for Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin for Visual Studio

2. 在 Explorer 中，從 Xamarin Visual Studio 延伸模組資料夾中刪除任何剩餘的檔案（所有版本，包括_Program files_和_program files （x86）_ ）：

    _C：\\Program Files\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

3. 同時刪除 Visual Studio 的 MEF 元件快取目錄：

    _% LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*. 0\\ComponentModelCache_

    事實上，這個步驟本身通常就足以解決下列錯誤：

    - 「' XamarinShellPackage ' 封裝未正確載入」

    - 「專案檔 ...無法開啟。 遺漏專案子類型」

    - 「物件參考未設定為物件的實例。  在 VisualStudio. XamarinIOSPackage. Initialize （） "

    - 「封裝的 SetSite 失敗」（在 Visual Studio 的_ActivityLog_中）

    - 「封裝的 LegacySitePackage 失敗」（在 Visual Studio 的_ActivityLog_中）

    （另請參閱[CLEAR MEF 元件](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)快取 Visual Studio 延伸模組。  另請參閱[Bug 40781，批註19，](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19)以進一步瞭解造成這些錯誤之 Visual Studio 中的上游問題。）

4. 此外，請檢查_VirtualStore_目錄，以查看 Windows 是否可能已在其中儲存延伸模組的任何重迭檔案 _\\Xamarin_或_ComponentModelCache_目錄：

    _% LOCALAPPDATA%\\VirtualStore_

5. 開啟 [登錄編輯程式] （`regedit`）。

6. 尋找下列機碼：

    _HKEY\_本機\_機\\軟體\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Shareddll_

7. 尋找並刪除所有符合此模式的項目：

    _C：\\Program Files\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

8. 尋找此機碼：

    _HKEY\_CURRENT\_使用者\\軟體\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9. 刪除所有看起來可能與 Xamarin 相關的項目。  例如，以下是用來在舊版 Xamarin 中造成問題的一項：

    _VisualStudio，1。0_

10. 開啟系統管理員 `cmd.exe` 命令提示字元，然後針對每個已安裝的 Visual Studio 版本執行 `devenv /setup` 和 `devenv /updateconfiguration` 命令。  例如，若為 Visual Studio 2015：

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. 重新開機。

12. 使用從[visualstudio.com](https://visualstudio.com/xamarin/)重新安裝 Xamarin 的目前穩定版本。

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>「封裝未正確載入」的其他疑難排解步驟

在上述步驟無法解決「封裝未正確載入」錯誤的情況下，這裡還有幾個步驟可供您嘗試。

1. 建立新的 Windows 使用者帳戶。

2. 檢查 Xamarin Visual Studio 擴充功能是否載入，而不會對新使用者產生錯誤。

3. 如果擴充功能的載入正確，則問題最有可能是原始使用者的部分儲存設定所造成：

    - **在 Explorer 中**– _% LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*. 0_
    - **Regedit** – _HKEY\_目前\_使用者\\軟體\\Microsoft\\VisualStudio\\1\*。 0_
    - **In regedit** – _HKEY\_目前\_使用者\\軟體\\Microsoft\\VisualStudio\\1\*\_Config_

4. 如果這些儲存的設定確實似乎是問題，您可以嘗試將它們備份，然後將它們刪除。
