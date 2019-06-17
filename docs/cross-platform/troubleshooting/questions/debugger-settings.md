---
title: 偵錯工具需要哪些專案設定？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 9a18c97ba227615ae42529424b5c22b5e144f5e5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357858"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>偵錯工具需要哪些專案設定？

為了讓偵錯工具正常運作 （叫用的中斷點，顯示偵錯記錄檔等），開發人員檢測和偵錯資訊顯示必須同時啟用。

請遵循下列步驟來檢查您的環境設定：

## <a name="visual-studio"></a>Visual Studio
1. 開啟 專案選項
2. 移至**建置 > 進階...** 若要設定偵錯資訊**完整**
3. 每個平台的設定：
   - 移至**Android 選項 > 偵錯選項**。 刻度**讓開發人員檢測設備** 方塊中。
   - 移至**iOS 偵錯 > 偵錯及檢測**。 刻度**啟用偵錯** 方塊中。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 開啟 專案選項
2. 移至**建置 > 編譯器 > 一般選項**。 若要設定偵錯資訊**完整**
3. 每個平台的設定：
  - 移至**建置 > Android 建置 > 偵錯選項**。 刻度**讓開發人員檢測設備** 方塊中。
  - 移至**建置 > iOS 偵錯**。 刻度**啟用偵錯** 方塊中。

