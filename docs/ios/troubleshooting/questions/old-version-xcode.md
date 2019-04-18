---
title: 我可以使用較舊版本的 Xcode 或 Xamarin.iOS 嗎
description: 本指南概要說明與使用舊版 Xamarin.iOS 或 Xcode （比目前的穩定版本） 的問題。
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 2a208d39454a33adc849bcccc66802361693e82e
ms.sourcegitcommit: 34819671c7910d29f018bdb394ddd4a4b0cd3a31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59675884"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>我可以使用較舊版本的 Xcode 或 Xamarin.iOS 嗎？

Xamarin 文件會假設使用的最新的 Xamarin.iOS 和 Xcode，建議使用。 不過，有些客戶想要使用較舊的 Xamarin.iOS 和/或 Xcode，並想要的結果的詳細資訊。

版本資訊包含下列警告：

> [!WARNING]
> **使用較舊的 Xcode 版本**
>
> 使用較舊的 Xcode 版本 (與上面所述[需求](https://docs.microsoft.com/xamarin/ios/release-notes/12/12.8#requirements)) 通常是可行的但某些功能可能無法使用。 也有些限制需要因應措施，例如：
>
> - 靜態註冊機構需要 Xcode 標頭檔，來建置應用程式，導致[ `MT0091` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT0091)或是[ `MT4109` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT4109)如果遺漏了 Api 的錯誤。 在大部分情況下啟用受管理的連結器將協助 （藉由移除 API）。
> - 除非使用 Xcode 9.0 + 工具鏈，Bitcode 組建 （針對 tvOS 和 watchOS） 可能會失敗提交至 App Store。

## <a name="further-information"></a>進一步的資訊

Microsoft 強烈建議使用最新的 Xcode 和最新的 Xamarin.iOS 版本開發及提交應用程式時。 Apple 要求提交應用程式時，請使用最新的 Xcode。

請注意，使用最新的 Xcode 時，不會防止您從以較舊 iOS 版本為目標的應用程式。 您所支援的 iOS 版本取決於您**Info.plist**項目和您的應用程式使用的 Api。

您可安裝多個版本的 Xcode-並存，有不同的名稱，例如**Xcode101.app**並**Xcode102.app**。 如果您使用多個版本，請務必在設定使用中的 Xcode [Visual Studio for Mac 設定](~/ios/troubleshooting/questions/ios-sdk.md)且具有[ `xcode-select` ](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [命令列工具](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_)。

不過，少數的情況下可能需要使用較舊的元件。 這份文件將說明您可能會面臨時的一般難題使用早於最新版本。

從 Apple 的每個版本是唯一的並可能會遇到其他問題，此處並未記載。

這些挑戰是有時非一般若要解決，因此每當可能繼續支援的最新的 Xcode 和最新的 Xamarin.iOS 組態。

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>使用的舊的 Xamarin.iOS 與舊的 Xcode

無法更新 Xamarin.iOS 和 Xcode 是時間的可行的至少一定程度。 限制是在某個時間點，Apple 將需要的最低版本為 Xcode 來提交您的應用程式。 此時您應該更新所有元件 (macOS、 Xcode 和 Xamarin.iOS) 為最新版本 （或 Xcode Apple 和相符的 Xamarin.iOS 版本所需的新、 最小版本）。

這通常遠來逐漸更新，並保持小型變更。 針對大型的專案，其中的更新可以就很難跟上，保持使用已知的工作集可以是不錯的折衷辦法。

## <a name="use-of-new-xamarinios-with-older-xcode"></a>新的 Xamarin.iOS 與較舊的 Xcode 的使用

Xamarin.iOS 一般支援較舊的 Xcode 版本盡可能合理。 幾個潛在的挑戰包括：

- 較新的 Xamarin.iOS 可能支援某些功能，並在選取的 Xcode 中的 Api 不存在。 
- **靜態註冊機構**需要 Xcode 標頭檔，來建置應用程式，導致[ `MT0091` ](~/ios/troubleshooting/mtouch-errors.md#MT0091)或是[ `MT4109` ](~/ios/troubleshooting/mtouch-errors.md#MT4109)' 如果遺漏了 Api 的錯誤。
  - 在大部分情況下啟用受管理的連結器將會協助 （藉由移除受管理的繫結，適用於新的 API） 如果未使用。
- 除非使用 Xcode 9.0 + 工具鏈，Bitcode 組建 （針對 tvOS 和 watchOS） 可能會失敗提交至 App Store。

## <a name="use-of-new-xcode-with-older-xamarinios"></a>與舊版 Xamarin.iOS 的新 Xcode 的使用

這個使用案例是顯然更加困難，Xamarin.iOS 不可以預測的新 Xcode 多變的需求。 MacOS 的更新也導入的問題，並不相容性修補程式可能會受到影響的 Xamarin.iOS 的許多部分。 

有許多可能的區域，其中可能發生錯誤包括：

- 與不相容`mlaunch`:
  - 模擬器支援在正常 （或完全） 可能無法運作
  - 正常 （或完全），可能無法運作的裝置支援
- 未知的支援 `mtouch` 
  - 不支援新的架構
  - 不支援新的權利
  - 不支援新的或更新的工具
    - 這可能會影響程式碼以及簽署

### <a name="new-appstore-submission-rules"></a>AppStore 提交的新規則

Apple 會保留隨時更新 AppStore 提交規則的權限。 這些規則的變更只有在某些時候會預先宣告。 有些變更需要工具支援的變更，就必須更新的 Xamarin.iOS 元件。

規則的變更，除了 Apple，通常已提交的應用程式中加入額外的驗證，或可強化現有的。 其中某些需要我們的工具 （例如新列入封鎖清單的符號） 中的變更。 其中許多第一次遇到客戶提交，因為規則沒有公告 （或清單）。

## <a name="summary"></a>總結

可能的話，播放安全下列 Apple 的指導方針和開發上並提交與最新的 Xcode App Store 上發行。

接著，使用最新的 Xamarin.iOS 發行。 這將會追蹤的最新的修正程式，可能會影響已提交的應用程式，且必須符合的最新的規則變更。

在這不可行，請考慮使用相符的舊版 Xcode 和 Xamarin.iOS 版本。 這可以搭配使用的時間，但在某個時間點，Apple 會堅持較新的工具讓據此進行規劃。
