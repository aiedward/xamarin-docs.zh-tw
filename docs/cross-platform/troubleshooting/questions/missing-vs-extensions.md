---
title: 安裝之後遺漏 Visual Studio 擴充功能
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: e47cfc4de77a6310a81867eefb07c3c1e5cc7060
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="missing-visual-studio-extensions-after-installation"></a>安裝之後遺漏 Visual Studio 擴充功能

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>這個專案是與目前的 Visual Studio 版本不相容的錯誤訊息：

請確定已安裝相容版本的 Visual Studio:

-   Visual Studio 2017 （Community、 Professional 或 Enterprise）
-   Visual Studio 2015 （Community、 Professional 或 Enterprise）

另請參閱[Windows 需求](~/cross-platform/get-started/requirements.md#windows)。

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>可能的修正 1： 變更以確定已安裝 Visual Studio 擴充功能安裝

在某些情況下，Xamarin 安裝程式可能會自動取消核取 Visual Studio 擴充功能的安裝選項。 如果是問題的原因，安裝遺失的 Visual Studio 擴充功能使用安裝程式的**變更**命令。 例如，若要安裝 Visual Studio 2013 的擴充功能：

1. 開啟 Windows**程式和功能**控制台。

2. 以滑鼠右鍵按一下**Xamarin**項目，然後選取**變更**。

3. 按一下**下一步**，然後**變更**。

4. 請確定**Xamarin for Visual Studio 2013**選項設定為安裝：

    ![](missing-vs-extensions-images/installer.png "Xamarin 啟用 Visual Studio 2013 安裝選項")

5. 繼續執行安裝程式精靈的其餘部分。

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>可能的修正 2： 要求 Visual Studio 來設定延伸

1. 檢查是否 Xamarin 延伸模組已複製至 Visual Studio 擴充功能資料夾：

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    如果延伸模組已正確安裝 （適用於版本 3.1.228），在資料夾中會有 60 的項目：


    ![](missing-vs-extensions-images/folder.png "在 [總管] 的 'Xamarin\3.1.228.0' 資料夾內容的清單")

2. 在您確認此資料夾正確無誤後，指示 Visual Studio，請嘗試再次設定延伸模組：

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>可能的修正 3： 嘗試全新重新安裝的 Xamarin

1.  從 Windows 控制台中解除安裝有下列任何一項：

    *   Xamarin

    *   Xamarin for Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin for Visual Studio

2.  在 [總管] 中，刪除其餘的檔案從 Visual Studio 的 Xamarin 擴充功能資料夾 (所有版本，包括**Program Files**和**Program Files (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  也請檢查 「 VirtualStore"目錄，以查看 是否可能會有任何 「 重疊 」 的複本的任何延伸目錄中：

    `%LOCALAPPDATA%\VirtualStore`

4.  開啟登錄編輯程式 (regedit)。

5.  尋找此機碼：

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  尋找並刪除所有符合此模式的項目：

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  尋找此機碼：

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  刪除所有看起來可能與 Xamarin 相關的項目。 例如，這裡的一個，會用來 Xamarin 的舊版本會造成問題：

    _Mono.VisualStudio.Shell,1.0_

9.  重新開機。

10.  重新安裝 Xamarin，從目前穩定版本[visualstudio.com](https://visualstudio.com/xamarin)。

## <a name="possible-fix-4-repair-visual-studio-installation"></a>可能的修正 4： 修復 Visual Studio 安裝

1.  開啟 Windows**程式和功能**控制台。

2.  以滑鼠右鍵按一下相關的 Microsoft Visual Studio 項目，然後選取**變更**

3.  按一下**修復**中開啟的 Visual Studio 對話方塊按鈕。
