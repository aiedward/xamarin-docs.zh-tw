---
title: 在安裝後遺漏 Visual Studio 延伸模組
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 976d0882c5875c1d3e1c8f0ea1732de08df8e07f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996171"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>在安裝後遺漏 Visual Studio 延伸模組

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>錯誤訊息：這個專案與目前的 Visual Studio 版本不相容

請確定已安裝 Visual Studio 2017 （[社區]、[專業] 或 [企業]）或更新版本。

另請參閱[Windows 需求](~/cross-platform/get-started/requirements.md#windows-requirements)。

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>可能的修正1：變更安裝以確定已安裝 Visual Studio 延伸模組

在某些情況下，Xamarin 安裝程式可能會自動取消檢查 Visual Studio 延伸模組的安裝選項。 如果這是問題的原因，請使用安裝程式的**變更**命令來安裝遺漏的 Visual Studio 延伸模組。 例如，若要安裝 Visual Studio 2013 的擴充功能：

1. 開啟 [Windows**程式和功能**] 控制台。

2. 以滑鼠右鍵按一下 [ **Xamarin** ] 專案，然後選取 [**變更**]。

3. 按 **[下一步]**，然後**變更**。

4. 請確定 [ **Xamarin for Visual Studio 2013** ] 選項已設定為 [安裝]：

    ![啟用 Xamarin for Visual Studio 2013 安裝選項](missing-vs-extensions-images/installer.png)

5. 繼續執行安裝程式的其餘部分。

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>可能的修正程式2：要求 Visual Studio 重新設定擴充功能

1. 檢查 Xamarin 延伸模組是否已複製到 [Visual Studio extensions] 資料夾中：

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    如果已正確安裝延伸模組（針對版本3.1.228），資料夾中將會有60個專案：

    ![Explorer 中的 Xamarin\3.1.228.0 資料夾內容清單](missing-vs-extensions-images/folder.png)

2. 確認此資料夾看起來正確之後，請告訴 Visual Studio 再次嘗試設定擴充功能：

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>可能的修正3：嘗試全新重新安裝 Xamarin

1. 從 Windows [控制台] 中，卸載下列任何一項：

    * Xamarin

    * Xamarin for Windows

    * Xamarin.Android

    * Xamarin.iOS

    * Xamarin for Visual Studio

2. 在 Explorer 中，從 Xamarin Visual Studio 延伸模組資料夾中刪除任何剩餘的檔案（所有版本，包括**Program files**和**program files （x86）**）：

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3. 另請檢查 "VirtualStore" 目錄，以查看是否有任何延伸目錄的「重迭」複本：

    `%LOCALAPPDATA%\VirtualStore`

4. 開啟 [登錄編輯程式] （regedit）。

5. 尋找此機碼：

    _HKEY \_ 本機 \_ MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6. 尋找並刪除所有符合此模式的項目：

    _C:\Program Files \* \Microsoft Visual Studio 1 \* . 0 \ Common7\IDE\Extensions\Xamarin_

7. 尋找此機碼：

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8. 刪除所有看起來可能與 Xamarin 相關的項目。 例如，以下是用來在舊版 Xamarin 中造成問題的一項：

    _VisualStudio，1。0_

9. 重新開機。

10. 從[visualstudio.com](https://visualstudio.com/xamarin)重新安裝 Xamarin 的目前穩定版本。

## <a name="possible-fix-4-repair-visual-studio-installation"></a>可能的修正4：修復 Visual Studio 安裝

1. 開啟 [Windows**程式和功能**] 控制台。

2. 以滑鼠右鍵按一下相關的 Microsoft Visual Studio 專案，然後選取 [**變更**]

3. 在開啟的 [Visual Studio] 對話方塊中，按一下 [**修復**] 按鈕。
