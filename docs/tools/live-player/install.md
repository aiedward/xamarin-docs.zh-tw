---
title: Xamarin Live Player Visual Studio 組態
description: 本文件說明如何使用 Xamarin Live Player 對執行中應用程式進行即時編輯。
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157740"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player Visual Studio 組態

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 預覽版已結束。 應用程式已無法使用。 下列指示可供客戶繼續使用 Visual Studio 2017 中使用預覽。

> [!TIP]
> 您可以使用[XAML 預覽程式](~/xamarin-forms/xaml/xaml-previewer/index.md)在 Visual Studio 2019 或 Visual Studio for Mac，就可以檢視您螢幕的設計，當您編輯它們。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您必須已經 Xamarin Live Player 應用程式在裝置上。 不再可供下載。

1. 開啟**Visual Studio 2017**。
2. 移至**工具 > 選項...** ，然後選取**Xamarin > 其他** 索引標籤。
3. 刻度**啟用 Xamarin Live Player**:

    ![核取 [啟用 Xamarin Live Player] 方塊中 [選項] 視窗](install-images/vs2017-options.png)

4. 建立或開啟 Xamarin 專案 (或[範例](~/tools/live-player/samples.md))。
5. 選擇**Live Player**裝置清單中：

    ![裝置清單中包含的 Xamarin Live Player 選項](install-images/devices-empty-windows.png)

    - 如果您已經有配對的裝置，它可做為選項。
    - 否則您將會提示您時所需裝置配對。

6. 按下**執行** 按鈕或選取下列其中一個選項**執行**或以滑鼠右鍵按一下功能表：

    - **啟動但不偵錯**– 您可以編輯應用程式和裝置發生的變更，請參閱 （進行變更並儲存檔案，應用程式會重新啟動）。
    - **開始偵錯**– 您可以設定中斷點，並檢查變數，但無法編輯程式碼。

    或者，選取**工具 > Xamarin Live Player > 即時執行目前檢視**，可讓您編輯的應用程式和裝置發生的變更，請參閱。 目前的檢視是顯示 （而不是應用程式的主畫面）。

7. 如果已經配對裝置和裝置上執行 Xamarin Live Player 應用程式，就會立即執行程式碼 ！

    如果沒有裝置配對，QR 代碼會指示裝置配對：

    ![配對的裝置 視窗](install-images/manage-empty-windows.png)

    如果裝置無法連絡的配對，可能會出現錯誤。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

您必須已經 Xamarin Live Player 應用程式在裝置上。 不再可供下載。

1. 開啟**Visual Studio for Mac**。
2. 移至**Visual Studio > 喜好設定...** ，然後選取**專案 > Xamarin Live Player （預覽）**  索引標籤。
3. 刻度**啟用 Xamarin Live Player**:

    [![核取 [啟用 Xamarin Live Player] 方塊中 [選項] 視窗](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. 建立或開啟 Xamarin 專案 (或[範例](~/tools/live-player/samples.md))。
5. 選擇**Live Player**裝置清單中。

    ![裝置清單中包含的 Xamarin Live Player 選項](install-images/devices.png)

    - 如果您已經有配對的裝置，它可做為選項。
    - 否則您將會提示您時所需裝置配對。
    - 選擇**Xamarin Live Player 裝置...** 來管理您想要使用 Xamarin Live Player 裝置。

6. 按下**執行** 按鈕或選取下列其中一個選項**執行**或以滑鼠右鍵按一下功能表：

    ![執行功能表選項](install-images/run-menu.png)

    - **啟動但不偵錯**– 您可以編輯應用程式和裝置發生的變更，請參閱 （進行變更並儲存檔案，應用程式會重新啟動）。
    - **開始偵錯**– 您可以設定中斷點，並檢查變數，但無法編輯程式碼。
    - **即時執行目前檢視**– 您可以編輯應用程式和裝置發生的變更，請參閱。 目前的檢視是顯示 （而不是應用程式的主畫面）。

7. 如果已經配對裝置和裝置上執行 Xamarin Live Player 應用程式，就會立即執行程式碼 ！

    如果沒有裝置配對，QR 代碼會指示裝置配對：

    ![配對的裝置 視窗](install-images/manage-empty.png)

    如果裝置無法連絡的配對，則會出現錯誤：

    ![無法連線到裝置錯誤訊息](install-images/error-cannot-connect.png)

-----

如果您遇到任何問題，或無法連線，請參閱[限制與疑難排解](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
