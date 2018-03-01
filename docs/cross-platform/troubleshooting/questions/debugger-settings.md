---
title: "偵錯工具需要何種專案設定？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 6097dc8dfdff8807137ef68be86a08e4c9e23988
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>偵錯工具需要何種專案設定？

為了讓偵錯工具可正常運作 （點擊的中斷點、 顯示偵錯記錄檔等），開發人員檢測和偵錯資訊顯示皆必須啟用。

請遵循下列步驟，檢查您的環境設定：

## <a name="visual-studio"></a>Visual Studio
1. 開啟專案選項
2. 移至**建置 > 進階...**若要設定偵錯資訊**完整**
3. 每個平台的設定：
   - 移至**Android 選項 > 偵錯選項**。 刻度**啟用開發人員檢測**方塊。
   - 移至**iOS 組建 > 偵錯選項**。 刻度**啟用偵錯**方塊。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 開啟專案選項
2. 移至**建置 > 編譯器 > 一般選項**。 若要設定偵錯資訊**完整**
3. 每個平台的設定：
  - 移至**建置 > Android 建置 > 偵錯選項**。 刻度**啟用開發人員檢測**方塊。
  - 移至**建置 > iOS 偵錯**。 刻度**啟用偵錯**方塊。

