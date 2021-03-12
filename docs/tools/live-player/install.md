---
title: Xamarin Live Player Visual Studio 設定
description: 本檔說明如何使用 Xamarin Live Player 對執行中的應用程式進行即時編輯。
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: cb06baad8b9666a06fdc588f6a9a8b460186a0b2
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603031"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player Visual Studio 設定

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player Preview 已結束。 應用程式已無法再使用。 下列指示適用于繼續在 Visual Studio 2017 中使用預覽版的客戶。

> [!TIP]
> 您可以使用 Visual Studio 2019 或 Visual Studio for Mac 中的 [XAML 熱重載 (~/xamarin-forms/xaml/hot-reload/index.md) ，以在編輯時查看畫面設計。

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您必須已在裝置上擁有 Xamarin Live Player 應用程式。 您無法再下載此版本。

1. 開啟 **Visual Studio 2017**。
2. 移至 [ **工具 > 選項** ]，然後選取 [ **Xamarin > 其他** ] 索引標籤。
3. 勾選 [ **啟用 Xamarin Live Player**]：

    ![勾選 [選項] 視窗中的 [啟用 Xamarin Live Player] 方塊](install-images/vs2017-options.png)

4. 建立或開啟 Xamarin 專案 (或 [範例](~/tools/live-player/samples.md)) 。
5. 選擇裝置清單中的 **Live Player** ：

    ![裝置清單包含 Xamarin Live Player 選項](install-images/devices-empty-windows.png)

    - 如果您已經配對裝置，則會以選項的形式提供。
    - 否則，系統會提示您在必要時將裝置配對。

6. 按下 [ **執行** ] 按鈕，或從 [ **執行** ] 或右鍵功能表中選取下列其中一個選項：

    - **啟動但不進行調試** 程式-您可以編輯應用程式，並查看裝置上發生的變更 (應用程式會在變更時重新開機，並將檔案儲存) 。
    - **開始調試** 程式-您可以設定中斷點並檢查變數，但無法編輯程式碼。

    或者，選取 [ **工具] > Xamarin Live Player > Live Run 目前的視圖**]，讓您可以編輯應用程式，並查看裝置上發生的變更。 目前的視圖會 (顯示，而不是應用程式的主畫面) 。

7. 如果裝置已配對且 Xamarin Live Player 應用程式在裝置上執行，程式碼將會立即執行！

    如果未配對任何裝置，將會出現 QR 代碼，內含如何配對裝置的指示：

    ![配對裝置視窗](install-images/manage-empty-windows.png)

    如果無法連絡裝置以進行配對，可能會出現錯誤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您必須已在裝置上擁有 Xamarin Live Player 應用程式。 您無法再下載此版本。

1. 開啟 **Visual Studio For Mac**。
2. 移至 [ **Visual Studio > 喜好** 設定]，然後選取 [ **Xamarin Live Player] > (預覽)** 索引標籤的專案。
3. 勾選 [ **啟用 Xamarin Live Player**]：

    [![勾選 [選項] 視窗中的 [啟用 Xamarin Live Player] 方塊](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. 建立或開啟 Xamarin 專案 (或 [範例](~/tools/live-player/samples.md)) 。
5. 在 [裝置] 清單中選擇 [ **Live Player** ]。

    ![裝置清單包含 Xamarin Live Player 選項](install-images/devices.png)

    - 如果您已經配對裝置，則會以選項的形式提供。
    - 否則，系統會提示您在必要時將裝置配對。
    - 選擇 **Xamarin Live Player 裝置 ...** 以管理您想要搭配 Xamarin live player 使用的裝置。

6. 按下 [ **執行** ] 按鈕，或從 [ **執行** ] 或右鍵功能表中選取下列其中一個選項：

    ![執行功能表選項](install-images/run-menu.png)

    - **啟動但不進行調試** 程式-您可以編輯應用程式，並查看裝置上發生的變更 (應用程式會在變更時重新開機，並將檔案儲存) 。
    - **開始調試** 程式-您可以設定中斷點並檢查變數，但無法編輯程式碼。
    - **即時執行目前的視圖** -您可以編輯應用程式，並查看裝置上發生的變更。 目前的視圖會 (顯示，而不是應用程式的主畫面) 。

7. 如果裝置已配對且 Xamarin Live Player 應用程式在裝置上執行，程式碼將會立即執行！

    如果未配對任何裝置，將會出現 QR 代碼，內含如何配對裝置的指示：

    ![配對裝置視窗](install-images/manage-empty.png)

    如果無法連絡裝置以進行配對，將會出現錯誤：

    ![無法連接到裝置的錯誤訊息](install-images/error-cannot-connect.png)

-----

如果您遇到任何問題或無法連接，請參閱 [限制和疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
