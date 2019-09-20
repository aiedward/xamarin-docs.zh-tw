---
title: 偵錯工具需要哪些專案設定？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: a3677117e30538c4df8c9b797acce0be602c3b59
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106080"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>偵錯工具需要哪些專案設定？

為了讓偵錯工具如預期般運作（命中中斷點、顯示偵錯工具記錄等），必須同時啟用開發人員檢測和偵錯工具資訊顯示。

請遵循下列步驟來檢查您的環境設定：

## <a name="visual-studio"></a>Visual Studio

1. 開啟專案選項
2. 移至**組建 > Advanced**...將 [調試資訊] 設定為 [**完整**]
3. 每個平臺的設定：
   - 移至 [ **Android 選項] > [調試選項**]。 勾選 [**啟用開發人員檢測**] 方塊。
   - 移至**IOS Debug > 調試 & 檢測**。 勾選 [**啟用調試**] 方塊。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 開啟專案選項
2. 移至**組建 > 編譯器 > 一般選項**。 將 [調試資訊] 設定為 [**完整**]
3. 每個平臺的設定：
    - 移至**組建 > Android build > 調試選項**。 勾選 [**啟用開發人員檢測**] 方塊。
    - 移至**組建 > IOS Debug**。 勾選 [**啟用調試**] 方塊。
