---
title: 解除安裝 Xamarin
description: 本文件描述如何在 Windows 上從 Visual Studio 解除安裝 Xamarin。
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
ms.openlocfilehash: 4c9096edddeb00070aaabc3e93b283f2d55c1bfa
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304314"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>從 Visual Studio 解除安裝 Xamarin

本指南說明如何在 Windows 上，從 Visual Studio 移除 Xamarin。

<a name="uninstallvs2017" />

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 和 Visual Studio 2017

您可使用安裝程式應用程式從 Visual Studio 2019 和 Visual Studio 2017 解除安裝 Xamarin：

1. 使用 [開始] 功能表來開啟 **Visual Studio 安裝程式**。

2. 按下您想要變更之執行個體的 [修改] 按鈕。

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. 在 [工作負載] 索引標籤中，取消選取 [使用 .NET 進行行動開發] 選項 (位於**行動與遊戲**區塊)。

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. 按一下視窗右下角的 [修改] 按鈕。

5. 安裝程式會移除取消選取的元件 (Visual Studio 2017 必須在安裝程式進行任何變更之前關閉)。

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

個別的 Xamarin 元件 (例如 Profiler 或 Workbooks) 可透過在步驟 3 中切換至 [個別元件] 索引標籤，然後取消選取特定的元件來解除安裝：

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

若要完全解除安裝 Visual Studio 2017，請從 [啟動] 按鈕旁邊的三列功能表中選擇 [解除安裝]。

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> 若您並存 (SxS) 安裝兩個 (或更多個) Visual Studio 執行個體 (例如發行版和預覽版)，則將一個執行個體解除安裝可能會移除另一個 Visual Studio 執行個體中的一些 Xamarin 功能，包括：
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remote iOS Simulator
> - Apple Bonjour SDK
>
> 在特定情況下，解除安裝其中一個並存 (SxS) 執行個體可能會不正確的移除這些功能。 這可能會降低移除並存執行個體後仍然留在系統上之 Visual Studio 執行個體上 Xamarin 平台的執行效能。
>
>您可以透過在 Visual Studio 安裝程式中執行 [修復] 選項來解決這個問題。該選項會重新安裝遺失的元件。

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 和更早版本

若要完全移除 Visual Studio 2015，請使用 [visualstudio.com 上的支援解答](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/)。

您可以透過**控制台**從 Windows 電腦解除安裝 Xamarin。 巡覽至 [程式和功能] 或 [程式] > [解除安裝程式]，如下圖所示：

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox)

從 [控制台] 將以下存在的所有項目解除安裝：

- Xamarin
- Xamarin for Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin for Visual Studio

在總管中，刪除 Xamarin Visual Studio 延伸模組資料夾 (所有版本，包含 Program Files 與 Program Files (x86)) 中的所有剩餘檔案：

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

刪除 Visual Studio 的 MEF 元件快取目錄，該目錄應該在下列位置：

```
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

簽入 **VirtualStore** 目錄以查看 Windows 是否已為 **Extensions\Xamarin** 或 **ComponentModelCache** 目錄在此處存放任何覆疊檔案：

```
%LOCALAPPDATA%\VirtualStore
```

開啟登錄編輯程式 (regedit) 並尋找下列機碼：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

尋找並刪除所有符合此模式的項目：

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

尋找此機碼：

```
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

刪除所有看起來可能與 Xamarin 相關的項目。 例如，任何包含 `mono` 或 `xamarin` 字詞的項目。

開啟系統管理員 cmd.exe 命令提示字元，然後對每個安裝的 Visual Studio 版本執行 `devenv /setup` 與 `devenv /updateconfiguration` 命令。 例如，若為 Visual Studio 2015：

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```
