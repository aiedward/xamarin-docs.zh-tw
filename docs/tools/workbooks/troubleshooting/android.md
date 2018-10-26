---
title: 疑難排解 Xamarin 在 Android 上的活頁簿
description: 本文件提供使用 Xamarin 在 Android 上的活頁簿的疑難排解秘訣。 它討論模擬器支援，就不會載入的活頁簿和其他主題。
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a93288829ff99027a4b33e7720a7f849df37e9b1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112607"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>疑難排解 Xamarin 在 Android 上的活頁簿

## <a name="emulator-support"></a>模擬器支援

若要執行的 Android 活頁簿，Android 模擬器必須可供使用。 不支援實體 Android 裝置。

如果您的電腦支援，我們會建議以及 HAXM 的 Google 的模擬器。
如果您必須有您的系統上啟用 HYPER-V，請改為採用 Visual Studio Android Emulator。

您必須擁有執行 Android 5.0 或更新版本的模擬器。 不支援 ARM 模擬器。 使用`x86`或`x86_64`僅限裝置。

請閱讀[我們的文件上設定 Android 模擬器][ android-emu]如果您不熟悉的程序。

> [!NOTE]
> 1.1 版及更早版本的活頁簿會嘗試 （及失敗 ！） 來使用 ARM 模擬器可用的話。 若要解決您的選擇，然後再開啟或建立 Android 的活頁簿，啟動 x86 模擬器。 活頁簿會永遠都優先選擇連接到執行中的模擬器，只要它是相容。

## <a name="workbooks-wont-load"></a>無法載入活頁簿

### <a name="workbook-window-spins-forever-never-loads-windows"></a>活頁簿視窗會旋轉，永遠不會載入 (Windows)

首先，檢查您的模擬器已完全可運作的網路存取，藉由在模擬器 的 web 瀏覽器中測試任何網站。

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Visual Studio Android 模擬器無法連線到網際網路

如果您的模擬器並沒有網路存取，您可能需要遵循下列步驟來修正您的 HYPER-V 網路交換器。 如果您經常 Wi-fi 網路之間切換，您可能需要定期重複此步驟：

0. **請確定任何重要的網路作業已完成，因為這可能會暫時中斷 Windows 連線從網際網路。**
1. 關閉模擬器。
2. 開啟 `Hyper-V Manager`。
3. 底下`Actions`，開啟`Virtual Switch Manager...`。
4. 刪除所有虛擬交換器。
5. 按一下 `OK`。
6. 啟動 VS Android 模擬器。 您可能會提示重新建立虛擬網路交換器。
7. 測試與 Android 模擬器的瀏覽器能夠存取網際網路。

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>相關連結

- [回報 Bug](~/tools/workbooks/install.md#reporting-bugs)
