---
title: 如何執行完整解除安裝 xamarin for Visual Studio？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 49577961026d9895912d2848975e71a9f7eebbd8
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>如何執行完整解除安裝 xamarin for Visual Studio？


1.  從 Windows 控制台中解除安裝有下列任何一項：

    -   Xamarin
    -   適用於 Windows 的 Xamarin
    -   Xamarin.Android
    -   Xamarin.iOS
    -   Xamarin for Visual Studio

2.  在 [總管] 中，刪除其餘的檔案從 Visual Studio 的 Xamarin 擴充功能資料夾 (所有版本，包括_Program Files_和_Program Files (x86)_):

    _C:\\程式檔案\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\延伸\\Xamarin_

3.  刪除 Visual Studio 的 MEF 元件以及用於快取目錄：

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    事實上，此步驟中的單獨使用時，通常會不足以解決錯誤，例如：

    -   「 'XamarinShellPackage' 封裝未正確載入 」

    -   「 專案檔 … 無法開啟。 沒有遺漏專案子類型"

    -   「 物件參考未設定物件的執行個體。  at Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    -   「 封裝失敗 SetSite 」 (在 Visual Studio 的_ActivityLog.xml_)

    -   「 封裝失敗 LegacySitePackage 」 (在 Visual Studio 的_ActivityLog.xml_)

    (請參閱[清除 MEF 元件快取](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)Visual Studio 擴充功能。  請參閱和[Bug 40781，註解 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19)更多有關上游的問題可能會導致這些錯誤的 Visual Studio 中的內容。)

4.  也在中檢查_VirtualStore_目錄，以查看是否 Windows 可能會有儲存任何重疊檔案_延伸\\Xamarin_或_ComponentModelCache_那里的目錄:

    _%LOCALAPPDATA%\\VirtualStore_

5.  開啟登錄編輯程式 (`regedit`)。

6.  尋找下列機碼：

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7.  尋找並刪除任何符合此模式的項目：

    _C:\\程式檔案\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\延伸\\Xamarin_

8.  尋找此機碼：

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9.  刪除任何項目看起來像是可能會與 Xamarin。  例如，這裡的一個，會用來 Xamarin 的舊版本會造成問題：

    _Mono.VisualStudio.Shell,1.0_

10. 開啟系統管理員`cmd.exe`命令提示字元，然後再執行`devenv /setup`和`devenv /updateconfiguration`每個已安裝版本的 Visual Studio 的命令。  例如，針對 Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. 重新開機。

12. 重新安裝目前的穩定版本，從使用 Xamarin 的[visualstudio.com](https://visualstudio.com/xamarin/)。

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>其他疑難排解步驟，針對 「 套件未正確載入 」

在上述步驟無法解決的 「 封裝未正確載入 」 的錯誤情況下，以下是幾個步驟，再試一次。

1.  建立新的 Windows 使用者帳戶。

2.  請檢查不會發生錯誤，新的使用者是否載入 Visual Studio 的 Xamarin 延伸模組。

3.  如果正確載入延伸模組，然後問題最可能被因某些儲存原始使用者的設定：

    -   **In Explorer** –  _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0_
    -   **在 regedit** – _HKEY\_目前\_使用者\\軟體\\Microsoft\\VisualStudio\\1\*.0_
    -   **在 regedit** – _HKEY\_目前\_使用者\\軟體\\Microsoft\\VisualStudio\\1\*.0\_組態_

4.  如果這些預存的設定確實會出現問題，您可以嘗試加以備份，並加以刪除。
