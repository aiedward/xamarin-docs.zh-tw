---
title: Xamarin Live Player 應用程式
description: 本檔說明 Xamarin Live Player 的應用程式，可用來在裝置上即時預覽程式碼變更。 其中討論設定、範例、記錄、設定、管理裝置等等。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d79eb9ad1ca57361e063f6ce73910d6bb149cf72
ms.sourcegitcommit: 424eaef56fd2933c98e72f1d3e7ac71730fe4835
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758067"
---
# <a name="xamarin-live-player-app"></a>Xamarin Live Player 應用程式

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 預覽版已結束。 應用程式已無法再使用。 下列指示可供客戶繼續使用預覽版搭配 Visual Studio 2017。

> [!TIP]
> 您可以使用 Visual Studio 2019 或 Visual Studio for Mac 中的 [XAML 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md) 程式來查看您在編輯時的畫面設計。

啟動時，Xamarin Live Player 應用程式看起來像這樣：

![Live Player Android 應用程式螢幕擷取畫面](player-images/app-android-sml.png)

當您按下 [ **配對] Visual Studio** 時，請使用相機掃描電腦上顯示的條碼：

![Android 條碼掃描器的螢幕擷取畫面](player-images/scan-android-sml.png)

如果連線成功，程式碼就應該幾乎立即在裝置上執行 (例如 [計算機範例](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator)：

![在裝置上執行的範例計算機應用程式](player-images/basic-calculator-sml.png)

## <a name="options"></a>選項。

按下 [資訊] 按鈕 **(我)** 在應用程式底部，以顯示 [ **選項** ] 功能表：

[![[選項] 功能表的螢幕擷取畫面](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>記錄

查看記錄以診斷問題。

### <a name="settings"></a>設定

- 切換顯示編譯和執行階段錯誤。
- 版本資訊。
- [傳送意見反應]。

[![設定的螢幕擷取畫面](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理裝置

若要第一次連接裝置，請依照 [ [需求 & 安裝](~/tools/live-player/install.md)] 中的指示執行。 您可以配對多個裝置，並透過 IDE 來管理它們。

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/windows)

在 Visual Studio 中，選擇 [ **工具] > Xamarin Live Player > 管理裝置 ...**

![管理裝置視窗](player-images/manage-tools-menu-vs.png)

此視窗可讓您執行下列作業：

- 藉由掃描程式碼來配對新的裝置
- 或者輸入在螢幕上顯示的程式碼來配對裝置
- 從清單中移除現有的裝置

您也可以從裝置清單存取此視窗。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，選擇 [ **工具] > (Xamarin Live Player) 管理裝置 ...**

![螢幕擷取畫面：顯示從 [工具] 視窗選取的 [管理裝置]。](player-images/manage-tools-menu.png)

此視窗可讓您執行下列作業：

- 藉由掃描程式碼來配對新的裝置
- 或者輸入在螢幕上顯示的程式碼來配對裝置
- 從清單中移除現有的裝置

![螢幕擷取畫面顯示 Xamarin Live Player 的視窗，以及可供預覽您應用程式的選項。](player-images/manage.png)

您也可以從裝置清單存取此視窗：

![從裝置清單中選擇 Xamarin Live Player 裝置](player-images/manage-device-menu.png)

-----

如果您遇到任何問題，請參閱 [限制和疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
