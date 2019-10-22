---
title: Xamarin 應用程式中的協助工具
description: 本檔提供建立可存取應用程式的各種秘訣。 例如，它包含有關大型字型、高對比、自我描述介面等的建議。
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 55d531036336cdd6c3ac7efa1c5ba21b09a7be9e
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70758128"
---
# <a name="accessibility-in-xamarin-apps"></a>Xamarin 應用程式中的協助工具

_確保您的應用程式可供最廣泛的可能物件使用_

協助工具是設計應用程式使用者介面的概念，可運作良好的作業系統顯示和輸入協助功能，例如大型類型、高對比、放大、螢幕閱讀（文字轉換語音）、視覺效果或 haptic 意見反應提示，以及替代的輸入法。

IOS、Android 和 Windows 等桌面和行動平臺提供內建的 Api，可協助開發人員建立可存取的應用程式，例如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)和[Apple 的 VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>平臺特定 Api

若要執行本檔中的指導方針，請使用每個平臺提供的 Api：

- [**Android 協助工具**](~/android/app-fundamentals/accessibility.md)
- [**iOS 協助工具**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 協助工具**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin. 表單**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>協助工具檢查清單

請遵循這些秘訣，以確保您的應用程式可供最廣泛的物件存取。 如需其他資訊，請參閱[Android 輔助功能測試檢查清單](https://developer.android.com/training/accessibility/testing.html)和[Apple 的協助工具頁面](http://www.apple.com/accessibility/)。

### <a name="support-large-fonts-and-high-contrast"></a>支援大型字型和高對比

請避免硬式編碼控制項維度，而是偏好可以調整大小以容納較大字型大小的版面配置。
在高對比模式中測試色彩配置，以確保它們可讀取。

### <a name="make-the-user-interface-self-describing"></a>讓使用者介面自我描述

使用與每個平臺上的螢幕讀取 Api 相容的描述性文字和提示，標記使用者介面的所有元素。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>確定影像和圖示具有替代的文字描述

屬於應用程式使用者介面（例如，按鈕或狀態指示器）一部分的影像和圖示應該以可存取的描述標記。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>設計視覺化樹狀結構並記住可存取的導覽

使用適當的版面配置控制項或 Api，以便使用替代輸入方法在控制項之間流覽，遵循與使用觸控式螢幕相同的邏輯流程。

從螢幕閱讀程式排除不必要的元素（例如，已可存取的欄位的裝飾影像或標籤）。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>不要單獨依賴音訊或色彩提示

避免只有進度、完成或其他狀態的唯一指示是音效或色彩變更的情況。 請設計使用者介面，以包含明確的視覺提示（僅限增強式的音效和色彩），或加入特定的協助工具指標。

選擇色彩時，請試著避免難以區分顏色的色板。

### <a name="captioning-for-video-text-for-audio"></a>影片的字幕、音訊文字

提供影片內容的標題，以及音訊內容的可讀取腳本。 提供調整音訊或影片內容速度的控制項也很有説明，並確保音量和播放/暫停按鈕易於尋找和使用。

### <a name="localize"></a>Localize

協助工具描述可以（且應該）當地語系化，其中應用程式支援多種語言。

## <a name="related-links"></a>相關連結

- [Android 協助工具](~/android/app-fundamentals/accessibility.md)
- [iOS 協助工具](~/ios/app-fundamentals/accessibility.md)
- [OS X 協助工具](~/mac/app-fundamentals/accessibility.md)
- [Xamarin. 表單協助工具](~/xamarin-forms/app-fundamentals/accessibility/index.md)
