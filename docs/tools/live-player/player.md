---
title: "Xamarin Player 即時應用程式"
description: "編輯及測試您的 iOS 或 Android 裝置上的即時應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/10/2017
ms.openlocfilehash: 6ae0439566387e22d939ede83e6b4cb5c1a5a8fe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-live-player-app"></a>Xamarin Player 即時應用程式

![預覽功能](~/media/shared/preview.png)

## <a name="get-the-app"></a>取得應用程式

Xamarin Live Player 是可從 iOS App Store 及 Google Play:

[ ![從 iOS App Store 下載](player-images/app-store-badge.svg)](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?ls=1&mt=8) [ ![Google Play 上的可用](player-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)



## <a name="using-the-app"></a>使用應用程式

一旦您已安裝應用程式在電話上，遵循[安裝指示](~/tools/live-player/install.md)連接到您的電腦。 請嘗試[範例應用程式](~/tools/live-player/samples.md)，讓它運作。

在啟動時，即時播放程式 Xamarin 應用程式看起來像這樣 (iOS 和 Android 上分別):

![即時 Player iOS 應用程式螢幕擷取畫面](player-images/app-iphone-sml.png) ![即時 Player Android 應用程式螢幕擷取畫面](player-images/app-android-sml.png)

當您按**配對 Visual studio**，使用攝影機來掃描條碼，顯示您的電腦上：

![IOS 條碼掃描器的螢幕擷取畫面](player-images/scan-iphone-sml.png) ![Android 的條碼掃描器的螢幕擷取畫面](player-images/scan-android-sml.png)

如果連接成功，應該會幾乎會立即 （例如計算機範例） 在裝置上執行的程式碼：

![範例 [小算盤] 應用程式在裝置上執行](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>選項

按下 資訊 按鈕**(i)**底部的 應用程式，以顯示**選項**功能表：

![[選項] 功能表的螢幕擷取畫面](player-images/options.png)

### <a name="logs"></a>記錄檔

檢視記錄檔來診斷問題。

### <a name="settings"></a>設定

* 切換顯示的編譯和執行階段錯誤。
* 版本資訊。
* 傳送意見反應。

![設定的螢幕擷取畫面](player-images/settings.png)

## <a name="managing-devices"></a>管理裝置

若要在第一次連接裝置，請依照下列中的指示[需求與安裝程式](~/tools/live-player/install.md)。 您可以配對 （例如 iOS 和 Android） 的多個裝置，再透過 IDE 管理它們。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，選擇 **工具 > Xamarin Live Player > 管理裝置...**

![管理裝置 視窗](player-images/manage-tools-menu-vs.png)

此視窗可讓您執行下列作業：

- 對新的裝置會掃描程式碼
- 或者配對裝置輸入其螢幕上顯示的程式碼
- 從清單中移除現有的裝置

您也可以存取此視窗從裝置清單。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 選擇**工具 > (Xamarin Live Player) 管理的裝置...**

![管理裝置 視窗](player-images/manage-tools-menu.png)

此視窗可讓您執行下列作業：

- 對新的裝置會掃描程式碼
- 或者配對裝置輸入其螢幕上顯示的程式碼
- 從清單中移除現有的裝置

![管理裝置 視窗](player-images/manage.png)

您也可以存取此視窗從裝置清單：

![從裝置清單中選擇 Xamarin Live Player 裝置](player-images/manage-device-menu.png)

-----

如果您遇到任何問題，請參閱[限制和疑難排解](~/tools/live-player/troubleshooting.md)。


## <a name="related-links"></a>相關連結

- [限制](~/tools/live-player/limitations.md)
- [疑難排解](~/tools/live-player/troubleshooting.md)
- [Xamarin Player 即時範例](~/tools/livehttps://developer.xamarin.com/samples.md)