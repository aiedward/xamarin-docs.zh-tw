---
title: Xamarin Live Player 應用程式
description: 本檔說明 Xamarin Live Player 應用程式，可以用來預覽裝置上即時的程式碼變更。 其中討論設定、範例、記錄、設定、管理裝置等等。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: f1d3adfad3b9d2a64990ff11f81bf3a1a39825bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029651"
---
# <a name="xamarin-live-player-app"></a>Xamarin Live Player 應用程式

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 預覽已結束。 應用程式已無法再使用。 下列指示是針對繼續使用預覽與 Visual Studio 2017 的客戶提供。

> [!TIP]
> 您可以使用 Visual Studio 2019 或 Visual Studio for Mac 中的[XAML 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)程式來查看您在編輯時的畫面設計。

在啟動時，Xamarin Live Player 應用程式看起來像這樣：

![Live Player Android 應用程式螢幕擷取畫面](player-images/app-android-sml.png)

當您按下 [**配對] Visual Studio**時，請使用相機來掃描電腦上顯示的條碼：

![Android 條碼掃描器的螢幕擷取畫面](player-images/scan-android-sml.png)

如果連線成功，程式碼應該幾乎會立即在裝置上執行（例如[計算機範例](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator)：

![在裝置上執行的範例計算機應用程式](player-images/basic-calculator-sml.png)

## <a name="options"></a>選項

按下應用程式底部的資訊按鈕 **（i）** 以顯示 [**選項**] 功能表：

[![[選項] 功能表的螢幕擷取畫面](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>記錄檔

查看記錄以診斷問題。

### <a name="settings"></a>設定

- 切換顯示編譯和執行階段錯誤。
- 版本資訊。
- 傳送意見反應。

[![設定的螢幕擷取畫面](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理裝置

若要第一次連接裝置，請依照[需求 & 設定](~/tools/live-player/install.md)中的指示進行。 您可以配對多個裝置，並透過 IDE 來管理它們。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

在 Visual Studio 中，選擇 [**工具] > Xamarin Live Player > 管理裝置 ...**

![[管理裝置] 視窗](player-images/manage-tools-menu-vs.png)

此視窗可讓您執行下列動作：

- 藉由掃描程式碼來配對新裝置
- 或者，藉由輸入顯示在螢幕上的程式碼來配對裝置
- 從清單中移除現有的裝置

您也可以從裝置清單存取此視窗。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，選擇 工具  **> （Xamarin Live Player） 管理裝置**。

![[管理裝置] 視窗](player-images/manage-tools-menu.png)

此視窗可讓您執行下列動作：

- 藉由掃描程式碼來配對新裝置
- 或者，藉由輸入顯示在螢幕上的程式碼來配對裝置
- 從清單中移除現有的裝置

![[管理裝置] 視窗](player-images/manage.png)

您也可以從裝置清單存取此視窗：

![從裝置清單中選擇 Xamarin Live Player 裝置](player-images/manage-device-menu.png)

-----

如果您遇到任何問題，請參閱[限制和疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
