---
title: "Xamarin Player 即時安裝程式"
description: "編輯及測試您的 iOS 或 Android 裝置上的即時應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/22/2017
ms.openlocfilehash: 9b801af77eceaf94b0ede98b6af51d55d6029fe6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="xamarin-live-player-setup"></a>Xamarin Player 即時安裝程式

Xamarin Live 播放程式可讓您對您的應用程式的即時編輯並且讓這些變更反映即時裝置上。 在 Xamarin Live 播放器應用程式 – 不需要設定模擬器，或使用纜線來部署內執行程式碼 ！ 本文說明如何設定 Xamarin Live Player。

![預覽功能](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1.取得應用程式

### <a name="xamarin-live-player-for-android"></a>Xamarin 即時 Player for Android
Xamarin Live Player for Android 從 Google Play 有：

[ ![Google Play 上提供](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

適用於 Android 裝置不含 Google Play Xamarin Live 播放程式是可透過[HockeyApp](https://aka.ms/xlp-hockeyapp)發佈。 此外，早期預覽中建置 Android 可以直接從 Google Play 安裝藉由選擇在[開啟測試計劃](https://play.google.com/apps/testing/com.xamarin.live)

### <a name="xamarin-live-player-for-ios"></a>適用於 iOS 的 Xamarin Live Player
我們鼓勵使用者加入[Xamarin Live 播放器應用程式_iOS 預覽_](https://aka.ms/liveplayeralpha)來享受 TestFlight 透過最新的增強功能的快速存取。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="2-get-visual-studio-2017-preview-on-windows-or-for-mactabsvsmac"></a>2.取得 Visual Studio 2017 Preview Windows 上 (或[for Mac](?tabs=vsmac))

Xamarin Live Player 需要：

- Visual Studio 2017 [15.4](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/#visualstudio2017)或更新版本。
- Visual Studio 電腦與相同 WiFi 網路上的裝置

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3.使用 Xamarin Live 播放程式第一次

1. 開啟**Visual Studio 2017**。
2. 移至**工具 > 選項...**選取**Xamarin > 其他** 索引標籤。
3. 刻度**啟用 Xamarin 即時 Player**:

  ![檢查 [選項] 視窗的啟用 Xamarin Live Player 方塊](install-images/vs2017-options.png)

2. 建立或開啟 Xamarin 專案 (或[範例](~/tools/live-player/samples.md))。
3. 選擇**Live Player**在裝置清單中：

  ![裝置的清單包括 Xamarin Live Player 選項](install-images/devices-empty-windows.png)

  * 如果您已經有配對的裝置，它可做為選項。
  * 否則您將會提示您時所需的裝置進行配對。
4. 按**執行** 按鈕或選取下列其中一個選項從**執行**或以滑鼠右鍵按一下功能表：

  - **啟動但不偵錯**– 您可以編輯應用程式和裝置發生的變更，請參閱 （進行變更並儲存檔案，應用程式會重新啟動）。
  - **開始偵錯**– 您可以設定中斷點，然後檢查變數，但無法編輯的程式碼。

  或者，選取**工具 > Xamarin Live Player > 即時執行的目前檢視**，可讓您編輯應用程式和裝置發生的變更，請參閱。 （而不是應用程式的主畫面），會顯示目前的檢視。

5. 如果裝置已經配對 Xamarin Live 播放器應用程式在裝置上執行，就會立即執行程式碼 ！

  如果沒有裝置配對，QR 代碼將會出現指示配對的裝置：

    ![配對的裝置 視窗](install-images/manage-empty-windows.png)

  如果配對的無法連絡裝置，可能會出現錯誤。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="2-get-visual-studio-for-mac-or-for-windowstabsvswin"></a>2.取得 Visual Studio for Mac (或[適用於 Windows](?tabs=vswin))

Xamarin Live Player 需要：

- OS X 10.11 macOS 10.12，或更新版本
- Visual Studio for Mac
- Mac 與相同 WiFi 網路上的裝置

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3.使用 Xamarin 即時的播放程式第一次

1. 開啟**Visual Studio for Mac**。
2. 移至**Visual Studio > 喜好設定...**選取**專案 > Xamarin Live 播放程式 （預覽）**  索引標籤。
3. 刻度**啟用 Xamarin 即時 Player**:

  [![檢查 [選項] 視窗的啟用 Xamarin Live Player 方塊](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

2. 建立或開啟 Xamarin 專案 (或[範例](~/tools/live-player/samples.md))。
3. 選擇**Live Player**在裝置清單中。

  ![裝置的清單包括 Xamarin Live Player 選項](install-images/devices.png)

  * 如果您已經有配對的裝置，它可做為選項。
  * 否則您將會提示您時所需的裝置進行配對。
  * 選擇**Xamarin Player 即時裝置...**來管理的裝置，您想要使用與 Xamarin Live Player。

4. 按**執行** 按鈕或選取下列其中一個選項從**執行**或以滑鼠右鍵按一下功能表：

  ![執行功能表選項](install-images/run-menu.png)

  - **啟動但不偵錯**– 您可以編輯應用程式和裝置發生的變更，請參閱 （進行變更並儲存檔案，應用程式會重新啟動）。
  - **開始偵錯**– 您可以設定中斷點，然後檢查變數，但無法編輯的程式碼。
  - **即時執行目前的檢視**– 您可以編輯應用程式和裝置發生的變更，請參閱。 （而不是應用程式的主畫面），會顯示目前的檢視。

5. 如果裝置已經配對 Xamarin Live 播放器應用程式在裝置上執行，就會立即執行程式碼 ！

  若為不成對的任何裝置，QR 代碼將會出現如何配對的裝置上的指示：

    ![配對的裝置 視窗](install-images/manage-empty.png)

  如果配對的無法連絡裝置，則會出現錯誤：

    ![無法連線到裝置錯誤訊息](install-images/error-cannot-connect.png)


-----

如果您遇到任何問題，請參閱[限制和疑難排解](~/tools/live-player/troubleshooting.md)。


## <a name="related-links"></a>相關連結

- [限制](~/tools/live-player/limitations.md)
- [疑難排解](~/tools/live-player/troubleshooting.md)
- [Xamarin Player 即時範例](~/tools/livehttps://developer.xamarin.com/samples.md)
