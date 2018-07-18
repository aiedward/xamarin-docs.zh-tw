---
title: 編輯 Android 虛擬裝置屬性
description: 本文說明如何使用 Android Device Manager 編輯 Android 虛擬裝置的設定檔內容。
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: 75ac85c67825e5db1b663d00f10eee6d093bfc1f
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733412"
---
# <a name="editing-android-virtual-device-properties"></a>編輯 Android 虛擬裝置屬性

_本文說明如何使用 Android Device Manager 編輯 Android 虛擬裝置的設定檔內容。_


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Android Device Manager** 支援編輯個別的 Android 虛擬裝置設定檔內容。 [新增裝置] 和 [裝置編輯] 畫面會在第一欄中列出虛擬裝置的屬性，並在第二欄中列出每個屬性的對應值 (如本範例所示)： 

[![新增裝置畫面的範例](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

當您選取屬性時，該屬性的詳細描述就會顯示在右邊。 您可以修改「硬體設定檔內容」和「AVD 內容」。 硬體設定檔屬性 (例如 `hw.ramSize` 和 `hw.accelerometer`) 會說明模擬裝置的實體特性。 這些特性包括螢幕大小、可用的 RAM 數量、加速計是否存在。 AVD 屬性會在 AVD 執行時指定其作業。 例如，您可以設定 AVD 屬性，來指定 AVD 如何使用您開發電腦的圖形卡進行轉譯。

您可以使用下列指導方針來變更屬性：

-   若要變更布林值屬性，按一下布林值屬性右邊的核取記號：

    ![變更布林值屬性](device-properties-images/win/02-boolean-value.png)

-   若要變更 *enum* (列舉) 屬性，按一下屬性右邊的向下箭頭，然後選擇新值。

    ![變更列舉屬性](device-properties-images/win/04-enum-value.png)

-   若要變更字串或整數屬性，在 [值] 欄中按兩下目前的字串或整數設定，然後輸入新值。

    ![變更整數屬性](device-properties-images/win/03-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

**Android Device Manager** 支援編輯個別的 Android 虛擬裝置設定檔內容。 [新增裝置] 和 [裝置編輯] 畫面會在第一欄中列出虛擬裝置的屬性，並在第二欄中列出每個屬性的對應值 (如本範例所示)： 

[![新增裝置畫面的範例](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

當您選取屬性時，該屬性的詳細描述就會顯示在右邊。 您可以修改「硬體設定檔內容」和「AVD 內容」。 硬體設定檔屬性 (例如 `hw.ramSize` 和 `hw.accelerometer`) 會說明模擬裝置的實體特性。 這些特性包括螢幕大小、可用的 RAM 數量、加速計是否存在。 AVD 屬性會在 AVD 執行時指定其作業。 例如，您可以設定 AVD 屬性，來指定 AVD 如何使用您開發電腦的圖形卡進行轉譯。

您可以使用下列指導方針來變更屬性：

-   若要變更布林值屬性，按一下布林值屬性右邊的核取記號：

    ![變更布林值屬性](device-properties-images/mac/02-boolean-value.png)

-   若要變更 *enum* (列舉) 屬性，按一下屬性右邊的下拉式功能表，然後選擇新值。

    ![變更列舉屬性](device-properties-images/mac/04-enum-value.png)

-   若要變更字串或整數屬性，在 [值] 欄中按兩下目前的字串或整數設定，然後輸入新值。

    ![變更整數屬性](device-properties-images/mac/03-integer-value.png)

-----

下表提供 [新裝置] 和 [裝置編輯器] 畫面中所列屬性的詳細說明：

[!include[](~/android/includes/emulator-properties.md)]

如需這些屬性的詳細資訊，請參閱[硬體設定檔屬性](https://developer.android.com/studio/run/managing-avds.html#hpproperties) \(英文\)。

