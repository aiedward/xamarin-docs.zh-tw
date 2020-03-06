---
title: Xamarin 熱重新開機
description: 本文件描述如何設定和使用 Xamarin 熱重新開機，以對 iOS 應用程式進行偵錯。
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: jimmgarrido
ms.author: jigarrid
ms.date: 01/14/2020
ms.openlocfilehash: 1f87fffe99656cdc0d0bf0f0178413740a20aa75
ms.sourcegitcommit: e9d88587aafc912124b87732d81c3910247ad811
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78337276"
---
# <a name="xamarin-hot-restart-preview"></a>Xamarin 熱重新開機 (預覽)

![預覽功能](~/media/shared/preview.png)

Xamarin 熱重新開機可讓您在開發期間快速測試應用程式的變更，包括多檔案程式碼編輯、資源和參考。 它會將新變更推送至在偵錯目標上的現有應用程式套件組合，如此可達到更快速的組建和部署循環。

> [!IMPORTANT]
> Xamarin 熱重新開機目前適用於 Visual Studio 2019 16.5 Preview，並支援使用 Xamarin.Forms 的 iOS 應用程式。 Visual Studio for Mac 和非 Xamarin.Forms 應用程式的支援已在規劃中。

## <a name="requirements"></a>需求

- Visual Studio 2019 16.5 版 Preview 3
- iTunes (64 位元)
- Apple 開發人員帳戶


## <a name="initial-setup"></a>初始設定

> [!NOTE]
> 當 Xamarin 熱重新開機處於預覽狀態時，預設為停用。 您可以在 **工具 > 選項 > 環境 > 預覽功能 下啟用它，> 啟用 Xamarin 熱重新開機**。

1. 確定 iOS 專案已設定為啟始專案，且組建設定為 [ **Debug | iPhone**]。

   1. 如果這是現有的專案，請移至 [建置] > [組態管理員...] 並確定已針對 iOS 專案啟用 [部署]。

2. 選取並按一下工具列中的 [本機裝置] 以啟動安裝精靈：

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. 如果未安裝 iTunes，請按一下 [下載 iTunes] 來下載安裝程式。 iTunes 安裝完成後，請按一下 [下一步]。

4. 將 iOS 裝置連接到您的電腦。 裝置名稱會在偵測到時出現在精靈中。 按 [下一步]。

5. 輸入您的 Apple 開發人員帳戶認證，然後按一下 [下一步]。

6. 使用下拉式功能表選取開發小組，以便在專案中啟用[自動化佈建](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)。 按一下 [完成]。

> [!NOTE]
> 建議使用自動化佈建，這樣便可以輕鬆地針對部署設定額外的 iOS 裝置。 不過，如果有正確的佈建設定檔，則您可以將其停用並繼續使用手動佈建。

## <a name="use-xamarin-hot-restart"></a>使用 Xamarin 熱重新開機
初始設定之後，連線的裝置會出現在偵錯目標下拉式功能表中。 若要針對應用程式進行偵錯，請在下拉式清單中選取裝置，然後按一下 [執行] 按鈕。 您可能會在 Visual Studio 中看到一則訊息，要求您在裝置上手動啟動應用程式，以便啟動偵錯工作階段。

您可以在偵錯時對程式碼檔案進行編輯，然後按下偵錯工具列中的 [重新啟動] 按鈕，或使用 **Ctrl+Shift+F5** 來重新啟動偵錯工作階段，並套用新變更：

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

您也可以使用 `HOTRESTART` 預處理器符號，以在使用 Xamarin 熱重新開機進行偵錯工具時，防止特定程式碼執行。

## <a name="limitations"></a>限制
- 目前僅支援以 Xamarin.Forms 建置的 iOS 應用程式和 iOS 裝置。
- 不支援腳本和 XIB 檔案，如果應用程式嘗試在執行時載入這些，便可能損毀。 請使用 `HOTRESTART` 預處理器符號來防止此程式碼執行。
- 不支援靜態 iOS 程式庫和架構，如果您的應用程式嘗試載入，您可能會看到執行階段錯誤或當機。 請使用 `HOTRESTART` 預處理器符號來防止此程式碼執行。 支援動態 iOS 程式庫。
- 您無法使用 Xamarin 熱重新開機來建立用於發佈的應用程式套件組合。 您仍然需要 Mac 電腦，才能為應用程式進行完整編譯、簽署和部署到生產環境。

## <a name="troubleshoot"></a>疑難排解
- 如果 iTunes 是透過 Microsoft Store 安裝，則安裝精靈將不會偵測到 iTunes。 您必須先解除安裝該版本，然後[從 Apple下載安裝程式](https://go.microsoft.com/fwlink/?linkid=2101014)。
- 有一個已知問題，那就是啟用裝置專屬組建會導致應用程式無法進入偵錯模式。 因應措施是在 [屬性] > [iOS 組建] 下停用此功能，然後重試偵錯。 未來的版本中將會修正此問題。
- 如果應用程式已存在於裝置上，則嘗試使用熱重新開機進行部署可能會失敗，並出現 `AMDeviceStartHouseArrestService` 錯誤。 因應措施是在裝置上解除安裝應用程式，然後再次部署。
- 輸入不屬於 Apple 開發人員計畫的 Apple ID，將會產生下列錯誤： `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID`。 您必須擁有有效的 Apple 開發人員帳戶，才能在 iOS 裝置上使用 Xamarin 熱重新開機。 

若要回報其他問題，請使用意見反應工具，工具位於 [[說明] > [傳送意見反應] > [回報問題]](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem)。
