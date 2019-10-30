---
title: Xamarin Live Player Visual Studio 設定
description: 本檔說明如何使用 Xamarin Live Player 對執行中的應用程式進行即時編輯。
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: 3dfe63cf3cf87a99d15879a0d4791248fa06f195
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029679"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player Visual Studio 設定

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 預覽已結束。 應用程式已無法再使用。 下列指示是針對繼續使用預覽與 Visual Studio 2017 的客戶提供。

> [!TIP]
> 您可以使用 Visual Studio 2019 或 Visual Studio for Mac 中的[XAML 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)程式來查看您在編輯時的畫面設計。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您的裝置上必須已有 Xamarin Live Player 應用程式。 無法再下載。

1. 開啟**Visual Studio 2017**。
2. 移至 **工具 > 選項 ...** ，然後選取  **Xamarin > 其他** 索引標籤。
3. 勾選 **啟用 Xamarin Live Player**：

    ![勾選 [選項] 視窗中的 [啟用 Xamarin Live Player] 方塊](install-images/vs2017-options.png)

4. 建立或開啟 Xamarin 專案（或[範例](~/tools/live-player/samples.md)）。
5. 選擇裝置清單中的 [ **Live Player** ]：

    ![裝置清單包含 Xamarin Live Player 選項](install-images/devices-empty-windows.png)

    - 如果您已配對裝置，它將可做為選項。
    - 否則，系統會提示您在需要時配對裝置。

6. 按 [**執行**] 按鈕，或從 [**執行**] 或按一下滑鼠右鍵功能表中選取下列其中一個選項：

    - **啟動但不進行調試**–您可以編輯應用程式，並查看裝置上發生的變更（應用程式會在進行變更和儲存檔案時重新開機）。
    - **開始調試**程式–您可以設定中斷點和檢查變數，但是無法編輯程式碼。

    或者，選取 [**工具] > Xamarin Live Player > [即時執行] [目前視圖**]，這可讓您編輯應用程式，並查看裝置上發生的變更。 此時會顯示目前的視圖（而不是應用程式的主畫面）。

7. 如果裝置已配對，而 Xamarin Live Player 應用程式正在裝置上執行，則程式碼將會直接執行！

    如果未配對任何裝置，則會出現 QR 代碼，並提供如何配對裝置的指示：

    ![配對裝置視窗](install-images/manage-empty-windows.png)

    如果無法連絡裝置進行配對，可能會出現錯誤。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您的裝置上必須已有 Xamarin Live Player 應用程式。 無法再下載。

1. 開啟**Visual Studio for Mac**。
2. 移至**Visual Studio > 喜好設定 ...** ，然後選取  **> Xamarin Live Player （預覽）**  索引標籤的專案。
3. 勾選 **啟用 Xamarin Live Player**：

    [![勾選 [選項] 視窗中的 [啟用 Xamarin Live Player] 方塊](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. 建立或開啟 Xamarin 專案（或[範例](~/tools/live-player/samples.md)）。
5. 選擇裝置清單中的 [ **Live Player** ]。

    ![裝置清單包含 Xamarin Live Player 選項](install-images/devices.png)

    - 如果您已配對裝置，它將可做為選項。
    - 否則，系統會提示您在需要時配對裝置。
    - 選擇 [ **Xamarin Live Player 裝置**]，以管理您想要搭配 Xamarin Live Player 使用的裝置。

6. 按 [**執行**] 按鈕，或從 [**執行**] 或按一下滑鼠右鍵功能表中選取下列其中一個選項：

    ![執行功能表選項](install-images/run-menu.png)

    - **啟動但不進行調試**–您可以編輯應用程式，並查看裝置上發生的變更（應用程式會在進行變更和儲存檔案時重新開機）。
    - **開始調試**程式–您可以設定中斷點和檢查變數，但是無法編輯程式碼。
    - **即時執行目前的觀看**–您可以編輯應用程式，並查看裝置上發生的變更。 此時會顯示目前的視圖（而不是應用程式的主畫面）。

7. 如果裝置已配對，而 Xamarin Live Player 應用程式正在裝置上執行，則程式碼將會直接執行！

    如果未配對任何裝置，則會出現 QR 代碼，並提供如何配對裝置的指示：

    ![配對裝置視窗](install-images/manage-empty.png)

    如果無法連絡裝置進行配對，將會出現錯誤：

    ![無法連接到裝置錯誤訊息](install-images/error-cannot-connect.png)

-----

如果您遇到任何問題或無法連接，請參閱[限制和疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
