---
title: 我可以使用較舊版本的 Xcode 或 Xamarin
description: 本指南概述使用舊版 Xamarin 或 Xcode (的問題，而不是目前的穩定版本) 。
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 509f50d26818af09c4d664f27f2ae17a73430991
ms.sourcegitcommit: e7a5d1ec9e50a09b3b24f4c57850a4763c3406d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101087427"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>我可以使用較舊版本的 Xcode 或 Xamarin 嗎？

Xamarin 檔假設您使用的是最新的 Xcode，這是建議的作法。 不過，有些客戶會偏好使用較舊的 Xamarin 和/或 Xcode，而且想要瞭解結果的詳細資料。

版本資訊包含下列警告：

> [!WARNING]
> **使用較舊的 Xcode 版本**
>
> 使用比上述 [需求](/xamarin/ios/release-notes/12/12.8#requirements) 中所述的舊 Xcode 版本 () 通常是可行的，但有些功能可能無法使用。 此外，某些限制可能需要因應措施，例如：
>
> - 靜態註冊機構需要 Xcode 標頭檔來建立應用程式，並 [`MT0091`](../mtouch-errors.md#MT0091) [`MT4109`](../mtouch-errors.md#MT4109) 在遺漏 api 時導致或錯誤。 在大部分情況下，啟用 managed 連結器將有助於移除 API) 來 (。
> - 除非使用 Xcode 9.0 + 工具鏈，否則 tvOS 和 watchOS) 的 Bitcode 組建 (可能會無法提交至 App Store。

## <a name="further-information"></a>進一步資訊

Microsoft 強烈建議您在開發和提交應用程式時，使用最新的 Xcode 和最新的 Xamarin 版本。 Apple 需要在提交應用程式時使用最新的 Xcode。

請注意，使用最新的 Xcode 並不會防止您的應用程式以較舊的 iOS 版本為目標。 您支援的 iOS 版本是以您的 **plist** 專案和應用程式所使用的 api 為基礎。

您可以並存安裝多個版本的 Xcode，並使用不同的名稱，例如 **Xcode101. app** 和 **Xcode102。** 如果您使用多個版本，請務必使用 [Visual Studio for Mac](~/ios/troubleshooting/questions/ios-sdk.md) 設定和 [`xcode-select`](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [命令列工具](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_)來設定使用中的 Xcode。

不過，罕見的情況可能需要使用較舊的元件。 本檔將說明您使用的版本早于最新版本時可能面臨的一般挑戰。

來自 Apple 的每個版本都是獨一無二的，您可能會遇到這裡未記載的其他陷阱。

這些挑戰有時無法解決，因此請盡可能地前往最新 Xcode 和最新的 Xamarin 支援的設定。

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>使用舊的 Xamarin 搭配舊的 Xcode

不更新 Xamarin. iOS 和 Xcode （至少有一段時間）。 限制是，在某個時間點，Apple 將需要最低版本的 Xcode 來提交您的應用程式。 至此，您應該將所有元件 (macOS、Xcode 和 Xamarin) 更新為最新版本 (或 Apple 所需的最基本 Xcode 版本，以及相符的 Xamarin. iOS 版本) 。

通常可以更輕鬆地進行更新，並隨時掌握小規模的變更。 對於更新可能難以趕上的大型專案，使用已知的工作集可能會是很好的折衷方案。

## <a name="use-of-new-xamarinios-with-older-xcode"></a>使用新的 Xamarin 搭配舊版 Xcode

Xamarin 一般可盡可能地支援較舊的 Xcode 版本。 一些潛在的挑戰包括：

- 較新的 Xamarin 可能會支援所選 Xcode 中不存在的某些功能和 Api。 
- **靜態註冊機構** 需要 Xcode 標頭檔來建立應用程式，並 [`MT0091`](~/ios/troubleshooting/mtouch-errors.md#MT0091) [`MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109) 在遺漏 api 時導致或錯誤。
  - 在大多數情況下，如果未使用新的 API) 的受控系結，則啟用 managed 連結器將有助於 (。
- 除非使用 Xcode 9.0 + 工具鏈，否則 tvOS 和 watchOS) 的 Bitcode 組建 (可能會無法提交至 App Store。

## <a name="use-of-new-xcode-with-older-xamarinios"></a>使用新的 Xcode 搭配舊版的 Xamarin

此使用案例變得更困難，因為 Xamarin 無法預測新 Xcode 的變更需求。 MacOS 的更新也會造成問題，而沒有相容性修補程式的許多元件可能會受到影響。 

有一些可能發生錯誤的可能區域包括：

- 不相容 `mlaunch` ：
  - 模擬器支援可能無法正常運作 (或全) 
  - 裝置支援可能無法正常運作 (或全) 
- 未知的支援 `mtouch` 
  - 不支援新架構
  - 不支援新權利
  - 不支援新的或更新的工具
    - 這可能也會影響程式碼簽署

### <a name="new-appstore-submission-rules"></a>新的 AppStore 提交規則

Apple 隨時都有權更新 AppStore 提交規則。 這些規則變更通常會事先宣佈。 其中一些變更需要工具變更才能支援，這需要更新的 Xamarin. iOS 元件。

除了規則變更之外，Apple 通常會將額外的驗證新增至已提交的應用程式，或可強化現有的應用程式。 其中有些需要在我們的工具中進行變更 (例如) 新的封鎖符號。 其中有許多是客戶提交的第一次發生，因為沒有任何公告 (或列出規則) 。

## <a name="summary"></a>摘要

可能的話，請遵循 Apple 的指導方針，並在 App Store 上發行的最新 Xcode 進行開發並提交。

接著，請使用已發行的最新版本。 這將會追蹤可能影響已提交應用程式的最新修正程式，並符合最新的規則變更。

如果不可行，請考慮使用相符的舊版 Xcode 和 Xamarin. iOS 版。 這可能會有一段時間，但在某個時間點，Apple 會堅持使用較新的工具，因此請據以規劃。