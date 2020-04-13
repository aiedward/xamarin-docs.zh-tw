---
title: Xamarin.Mac 連結器選項
description: 本文件描述 Xamarin.Mac 中的連結。 連結是一項功能強大的最佳化工具，可藉由移除不使用的程式碼來縮減您應用程式的大小。
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: f4ab94c4eede4a122ac834e075270a375bca0807
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030013"
---
# <a name="xamarinmac-linker-options"></a>Xamarin.Mac 連結器選項

_連結是一項功能強大的最佳化工具，可藉由移除不使用的程式碼來縮減您應用程式的大小。_

## <a name="overview"></a>概觀

根據您專案所使用的[目標架構](~/mac/platform/target-framework.md)，可用的連結器可能會受到限制。 這是因為連結會需要建立您應用程式所使用之每個類型的物件圖形，而由於 System.Configuration 的緣故，這在 [完整] (或 [不支援]) 選項中是不可能的 。

有四個可用的選項：

- **無** – 停用所有連結。 這是 [新式] 選項之 [偵錯] 組態及 [完整] 選項之所有組態中的預設值。
- **SDK** – 連結所有 SDK 組件，但使用者組件除外。 這是 [新式] 選項之 [發行] 組態中的預設值。 在 [完整] 選項中未提供此選項。
- **完整** – 連結所有組件。 這會要求使用者程式碼必須採取連結器防護措施，請參閱[附註](~/ios/deploy-test/linker.md)以了解詳細資訊。 在 [完整] 選項中未提供此選項。
- **平台** – 只連結 Xamarin.Mac.dll。 如需詳細資料，請參閱下文。

## <a name="platform-linking"></a>平台連結

使用 [完整] [目標架構](~/mac/platform/target-framework.md)通常不安全，但有一些案例需要非常有限形式的連結。

例如，向 macOS App Store 提交的應用程式不得參考一些已被取代的 API (例如 QTKit)，其中有些是 Xamarin.Mac 包含其繫結的 API。 即使應用程式並不會呼叫這些繫結，但引動過程將會存在於 SDK 中而遭到拒絕。

平台連結會假設應用程式和 BCL 未採取連結器防護措施，因此只會從 Xamarin.Mac.dll 中移除不使用的程式碼。 

任何未考慮 Xamarin.Mac.dll 類型的應用程式都將從移除不必要的類型獲得小幅的啟動改善。

平台連結通常僅適用於使用 [完整] 目標架構的應用程式，因為 [新式] 應用程式 可以使用功能更強大的 SDK 選項。

## <a name="setting-the-linker-configuration"></a>設定連結器組態

若要對 Xamarin.Mac 專案的連結器組態進行變更，請執行下列操作：

1. 在 Visual Studio for Mac 中開啟 Xamarin.Mac 專案。
2. 在 [方案總管]**** 中，按兩下專案檔以開啟 [專案選項]**** 對話方塊。
3. 從 [Mac 組建]**** 索引標籤中，選取符合您應用程式需求的 [連結器行為]****：

    ![選擇要使用的連結器行為](linker-images/link-behavior.png "選擇要使用的連結器行為")

4. 「完整目標架構」的平台連結必須要等到未來更新後，才會出現在 IDE 中。 在那之前，請改為將 `--linkplatform` 新增至 [其他 mmp 引數]****。
5. 按一下 [確定]**** 按鈕以儲存您的變更。

## <a name="related-links"></a>相關連結

- [在 iOS 上連結](~/ios/deploy-test/linker.md)
