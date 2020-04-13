---
title: 套用變更
description: 如何開始使用 Xamarin.Android 專案中的"應用更改」。
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80215345"
---
# <a name="apply-changes"></a>套用變更

通過應用"更改",您可以在不重新啟動應用的情況下將資源更改推送到正在運行的應用。 這有助於控制在保留設備或模擬器當前狀態的同時部署和測試小型增量更改時重新啟動應用的數量。

應用更改使用 Android [JVMTI 實現](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci)中支援的功能,這些功能在運行 Android 8.0(API 級別 26)或更高版本的設備或模擬器上得到支援。

## <a name="requirements"></a>需求

以下清單顯示使用應用程式變更的要求:

- **可視化工作室**- 在 Windows 上,更新至 Visual Studio 2019 版本 16.5 或更高版本。 在 macOS 上,更新至 Visual Studio 2019 的 Mac 版本 8.5 或更高版本。
- **Xamarin.Android** - Xamarin.Android 10.2 或更高版本必須安裝與 Visual Studio (Xamarin.Android 自動安裝作為**移動開發與 .NET**工作負載在 Windows 上的一部分,並作為**Mac 安裝程式的可視化工作室**的一部分)。
- **Android SDK** - Android API 28 或更高版本必須通過 Android SDK 管理器安裝。
- **目標設備或模擬器**- 您的設備或模擬器必須運行 Android 8.0(API 級別 26)或更高版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>開始使用

要開始應用更改,您需要確保設備或模擬程式運行 Android 8.0(API 級別 26)或更高版本。 然後運行您的Android應用程式有或不調試。

然後,您可以使用以下方法與應用程式更改進行互動:

1. **工具列圖示。** 您可以按下「應用更改」工具列圖示,將更改應用於目標設備或模擬器。

    [![套用變更 - 工具列圖示](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **捷徑。** 您可以使用 Shift **+ Alt + F5**鍵盤快速鍵將更改應用於目標設備或模擬器。
3. **調試功能表。** 您可以使用**除錯>應用更改「** 功能表項目將更改應用於目標設備或模擬器。

    [![套用變更 - 除錯選單](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="get-started"></a>開始使用

要開始應用更改,您需要確保設備或模擬程式運行 Android 8.0(API 級別 26)或更高版本。 然後運行您的Android應用程式有或不調試。

然後,您可以使用以下方法與應用程式更改進行互動:

1. **捷徑。** 您可以使用 + **+ + + R**鍵盤快速鍵將更改應用於目標設備或模擬器。
2. **運行功能表。** 您可以使用 **「執行>應用更改」** 選單項將更改應用於目標設備或模擬器。

    [![套用變更 - 除錯選單](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>限制

以下變更需要重新啟動應用程式:

- 更改 C# 代碼。
- 添加或刪除資源。
- 更改 AndroidManifest.xml。
- 更改本機庫 (.so 檔)。

## <a name="related-links"></a>相關連結

- [套用變更](https://developer.android.com/studio/run#apply-changes)
