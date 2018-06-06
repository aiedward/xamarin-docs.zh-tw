---
title: 疑難排解在 Android 上的 Xamarin 活頁簿
description: 本文件提供疑難排解提示使用 Xamarin Android 上的活頁簿。 它討論模擬器支援，不會載入的活頁簿和其他的主題。
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: topgenorth
ms.author: toopge
ms.openlocfilehash: b01fa5b67f8a7f5577c6598f63f8c38e8fa4cf3c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793979"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>疑難排解在 Android 上的 Xamarin 活頁簿

## <a name="emulator-support"></a>模擬器支援

若要執行 Android 的活頁簿，Android 模擬器必須可供使用。 不支援實體的 Android 裝置。

如果您的電腦支援，我們建議以及 HAXM Google 的模擬器。
如果您必須具有您的系統上啟用 HYPER-V，請改為搭配 Visual Studio Android 模擬器。

您必須擁有執行 Android 5.0 或更新版本的模擬器。 不支援的 ARM 模擬器。 使用`x86`或`x86_64`僅限裝置。

請閱讀[文件上設定 Android 模擬器][ android-emu]如果您不熟悉的程序。

> [!NOTE]
> 1.1 及更早版本的活頁簿將再試一次 （及失敗 ！） 來使用 ARM 模擬器在有提供。 若要暫時解決您的選擇，然後再開啟或建立 Android 的活頁簿，啟動 x86 模擬器。 活頁簿永遠偏好使用連線到執行中的模擬器，只要它相容。

## <a name="workbooks-wont-load"></a>無法載入活頁簿

### <a name="workbook-window-spins-forever-never-loads-windows"></a>活頁簿視窗永遠不會永遠微調負載 (Windows)

首先，檢查 [模擬器已完全運作的網路存取權，藉由在模擬器] 的 web 瀏覽器中測試任何網站。

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Visual Studio Android 模擬器無法連線到網際網路

如果您的模擬器並沒有網路存取，您可能需要依照下列步驟來修正您的 HYPER-V 網路交換器。 如果您經常 Wi-fi 網路之間切換，您可能需要定期重複此步驟：

0. **請確定任何嚴重的網路作業完成，因為這可能會暫時中斷 Windows 網際網路。**
1. 關閉模擬器。
2. 開啟 `Hyper-V Manager`。
3. 在下`Actions`，開啟`Virtual Switch Manager...`。
4. 刪除所有虛擬交換器。
5. 按一下 `OK`。
6. 啟動 VS Android 模擬器。 您可能會提示重新建立虛擬網路交換器。
7. 測試與 Android 模擬器的瀏覽器能夠存取網際網路。

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>相關連結

- [回報 Bug](~/tools/workbooks/install.md#reporting-bugs)
