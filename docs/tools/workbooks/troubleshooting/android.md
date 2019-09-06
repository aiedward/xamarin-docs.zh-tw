---
title: 針對 Android 上的 Xamarin Workbooks 進行疑難排解
description: 本檔提供在 Android 上使用 Xamarin Workbooks 的疑難排解秘訣。 其中討論模擬器支援、不會載入的活頁簿，以及其他主題。
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 04f02e6136d9d489a36e994575519e579770ac3d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291162"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>針對 Android 上的 Xamarin Workbooks 進行疑難排解

## <a name="emulator-support"></a>模擬器支援

若要執行 Android 活頁簿，Android 模擬器必須可供使用。 不支援實體 Android 裝置。

如果您的電腦支援 Google 的模擬器，我們建議您搭配 HAXM。
如果您的系統上必須啟用 Hyper-v，請改為使用 Visual Studio Android Emulator。

您必須擁有執行 Android 5.0 或更新版本的模擬器。 不支援 ARM 模擬器。 僅`x86`使用`x86_64`或裝置。

如果您不熟悉此程式，請參閱[設定 Android 模擬器的檔][android-emu]。

> [!NOTE]
> 活頁簿1.1 和更早版本會嘗試（且失敗！）以使用 ARM 模擬器（如果有的話）。 若要解決這個情況，請啟動您選擇的 x86 模擬器，然後再開啟或建立 Android 活頁簿。 活頁簿一律會偏好連接到執行中的模擬器（只要它相容）。

## <a name="workbooks-wont-load"></a>活頁簿不會載入

### <a name="workbook-window-spins-forever-never-loads-windows"></a>活頁簿視窗永遠旋轉，永遠不會載入（Windows）

首先，請在模擬器的網頁瀏覽器中測試任何網站，檢查您的模擬器是否有完整運作的網路存取權。

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Visual Studio Android Emulator 無法連線到網際網路

如果您的模擬器沒有網路存取權，您可能需要遵循下列步驟來修正 Hyper-v 網路交換器。 如果您經常在 Wi-fi 網路之間切換，您可能需要定期重複此動作：

1. **請確定任何重要的網路作業都已完成，因為這可能會暫時中斷 Windows 與網際網路的連線。**
1. 關閉模擬器。
1. 開啟 `Hyper-V Manager`。
1. 在`Actions`下， `Virtual Switch Manager...`開啟。
1. 刪除所有虛擬交換器。
1. 按一下 `OK`。
1. 啟動 VS Android Emulator。 系統可能會提示您重新建立虛擬網路交換器。
1. 測試 VS Android Emulator 的瀏覽器是否可存取網際網路。

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>相關連結

- [報告錯誤](~/tools/workbooks/install.md#reporting-bugs)
