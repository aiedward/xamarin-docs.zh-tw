---
title: 在安裝後遺漏 Visual Studio 延伸模組
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 3e3d426e7b00725eafeba139de5bc46d416c368a
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070887"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>在安裝後遺漏 Visual Studio 延伸模組

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>錯誤訊息：這個專案與目前的 Visual Studio 版本不相容

請確定 Visual Studio 2017 （Community、 Professional 或 Enterprise） 或更新版本的已安裝。

另請參閱[Windows 需求](~/cross-platform/get-started/requirements.md#windows-requirements)。

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>可能的修正程式 1:變更要確定已安裝 Visual Studio 擴充功能安裝

在某些情況下，Xamarin 安裝程式可能會自動取消核取的 Visual Studio 擴充功能的安裝選項。 如果是問題的原因，安裝使用的安裝程式遺漏的 Visual Studio 擴充功能**變更**命令。 例如，若要安裝 Visual Studio 2013 的擴充功能：

1. 開啟 Windows**程式和功能**控制台。

2. 以滑鼠右鍵按一下**Xamarin**項目，然後選取**變更**。

3. 按一下 **下一步**，然後**變更**。

4. 請確定**適用於 Visual Studio 2013 的 Xamarin**選項設定為安裝：

    ![](missing-vs-extensions-images/installer.png "啟用 Xamarin for Visual Studio 2013 的安裝選項")

5. 繼續執行安裝程式精靈的其餘部分。

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>可能的修正程式 2:要求再次設定擴充功能的 Visual Studio

1. 檢查是否 Xamarin 延伸模組已複製到 Visual Studio 延伸模組資料夾：

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    如果延伸模組已正確安裝 （適用於版本 3.1.228），在資料夾中會有 60 個項目：


    ![](missing-vs-extensions-images/folder.png "在 [總管] 的 'Xamarin\3.1.228.0' 資料夾內容的清單")

2. 確認此資料夾看起來正確無誤之後，告訴 Visual Studio，請嘗試再次設定延伸模組：

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>可能的修正程式 3:試用全新重新安裝 Xamarin

1.  從 Windows 控制台的 解除安裝有下列任一項：

    *   Xamarin

    *   Xamarin for Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin for Visual Studio

2.  在 [總管] 中，刪除任何剩餘的檔案從 Visual Studio 的 Xamarin 延伸模組資料夾 (所有版本，包括**Program Files**並**Program Files (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  也請檢查位於"VirtualStore"目錄，以查看是否有任何 「 重疊 」 的複本的任何延伸模組目錄：

    `%LOCALAPPDATA%\VirtualStore`

4.  開啟登錄編輯程式 (regedit)。

5.  尋找此機碼：

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  尋找並刪除所有符合此模式的項目：

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  尋找此機碼：

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  刪除所有看起來可能與 Xamarin 相關的項目。 比方說，這裡的一個，會用來在較舊版本的 Xamarin 中造成麻煩：

    _Mono.VisualStudio.Shell,1.0_

9.  重新開機。

10.  重新安裝的 Xamarin 從目前的穩定版本[visualstudio.com](https://visualstudio.com/xamarin)。

## <a name="possible-fix-4-repair-visual-studio-installation"></a>可能的修正程式 4:修復 Visual Studio 安裝

1.  開啟 Windows**程式和功能**控制台。

2.  以滑鼠右鍵按一下相關的 Microsoft Visual Studio 項目，然後選取**變更**

3.  按一下 **修復**中開啟的 Visual Studio 對話方塊按鈕。
