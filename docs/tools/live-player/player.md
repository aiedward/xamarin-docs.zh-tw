---
title: Xamarin Live Player 應用程式
description: 本文件說明 Xamarin Live Player 應用程式，可用來預覽程式碼變更存留在裝置。 它討論安裝程式、 範例、 記錄、 管理裝置，以及更多的設定。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 08/08/2017
ms.openlocfilehash: 89795e5df00b426c0f11c04a0844993071df1e25
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855194"
---
# <a name="xamarin-live-player-app"></a>Xamarin Live Player 應用程式

![預覽功能](~/media/shared/preview.png)

> [!NOTE]
> 只有 Visual Studio 2017 中使用播放程式的即時預覽。

當您在手機上安裝應用程式時，請遵循[安裝指示](~/tools/live-player/install.md)連線到您的電腦。 請嘗試[範例應用程式](~/tools/live-player/samples.md)讓它運作。

在啟動時，Xamarin Live Player 應用程式看起來像這樣：

![即時玩家 Android 的應用程式螢幕擷取畫面](player-images/app-android-sml.png)

當您按下**與 Visual Studio 配對**，使用相機來掃描條碼，顯示您的電腦上：

![Android 的條碼掃描器的螢幕擷取畫面](player-images/scan-android-sml.png)

如果連線成功，應該立即執行程式碼在裝置上幾乎 (例如[計算機範例](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![在裝置上執行的範例計算機應用程式](player-images/basic-calculator-sml.png)

## <a name="options"></a>選項

按下 資訊 按鈕 **(i)** 下方的 應用程式，以顯示**選項**功能表：

[![S[選項] 功能表的 creenshot](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>記錄檔

檢視記錄檔診斷問題。

### <a name="settings"></a>設定

- 切換顯示的編譯和執行階段錯誤。
- 版本資訊。
- 傳送意見反應。

[![S設定的 creenshot](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理裝置

若要第一次連接裝置，請依照下列中的指示[需求與安裝](~/tools/live-player/install.md)。 您可以配對多個裝置，然後透過 IDE 進行管理。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

在 Visual Studio 中，選擇**工具 > Xamarin Live Player > 管理的裝置...**

![管理裝置 視窗](player-images/manage-tools-menu-vs.png)

此視窗可讓您執行下列作業：

- 新的裝置配對掃描程式碼
- 或者將裝置配對中輸入其螢幕上顯示的程式碼
- 從清單中移除現有的裝置

您也可以存取此視窗從裝置清單。

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，選擇**工具 > (Xamarin Live Player) 管理的裝置...**

![管理裝置 視窗](player-images/manage-tools-menu.png)

此視窗可讓您執行下列作業：

- 新的裝置配對掃描程式碼
- 或者將裝置配對中輸入其螢幕上顯示的程式碼
- 從清單中移除現有的裝置

![管理裝置 視窗](player-images/manage.png)

您也可以存取此視窗從裝置清單：

![從裝置清單中選擇 Xamarin Live Player 裝置](player-images/manage-device-menu.png)

-----

如果您遇到任何問題，請參閱[限制與疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
- [範例搭配 Live Player](https://developer.xamarin.com/samples/xamarin-live-player/all/)
