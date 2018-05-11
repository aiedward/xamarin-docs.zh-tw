---
title: 協助工具選項
description: 確定您的應用程式都能由 widest 可能對象
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 48e9d760362e47d7046528c819959bfb14f71f91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="accessibility"></a>協助工具選項

_確定您的應用程式都能由 widest 可能對象_

存取範圍是指設計運作作業系統顯示和輸入協助功能，例如大型的型別，高對比，放大的應用程式使用者介面，螢幕讀取 （文字轉換語音），visual 或 haptic 意見反應提示的概念和替代的輸入的方法。

例如 iOS、 Android 和 Windows 的桌上型電腦和行動平台提供內建的 Api，可協助開發人員建立可存取的應用程式，例如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)和[Apple 的旁白](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>平台專屬的 Api

若要執行這份文件中的指導方針，使用每個平台所提供的 Api:

- [**Android 的協助工具**](~/android/app-fundamentals/accessibility.md)
- [**iOS 協助工具**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 的協助工具**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>網頁可及性檢查清單

請遵循這些提示來確保您的應用程式的存取可能最寬的對象。 簽出[Android 協助工具測試檢查清單](http://developer.android.com/training/accessibility/testing.html)和[Apple 的協助工具頁面](http://www.apple.com/accessibility/)如需詳細資訊。

### <a name="support-large-fonts-and-high-contrast"></a>支援大型的字型和高對比

應避免硬式編碼控制項維度，相反地，偏好的配置，可以調整大小以容納較大的字型大小。
測試以確保其為可讀取的高對比模式中的色彩配置。

### <a name="make-the-user-interface-self-describing"></a>讓使用者介面自我描述

標記提示與螢幕閱讀每個平台應用程式開發介面相容的描述性文字與您使用者介面的所有項的目。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>請確認映像和圖示的替代文字描述

可存取描述應該標記影像和圖示的應用程式使用者介面 （例如按鈕或指標的狀態，例如） 的一部分。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>設計具有可存取的導覽，記住的視覺化樹狀結構

使用適當的版面配置控制項或應用程式開發介面，讓控制項使用替代的輸入的法之間瀏覽遵循相同的邏輯流程做為使用觸控式螢幕。

排除不必要的項目從螢幕助讀程式 （裝飾性影像或欄位已可供存取，例如標籤）。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>不要依賴單獨的音訊或色彩提示

避免的情況下進行、 完成，或其他特定狀態的唯一指示聲音或變更色彩。 請設計包含清楚視覺提示 （使用音效和增援只色彩），或新增特定的協助工具標記的使用者介面。

選擇色彩，請試著避免很難區分使用者色盲的調色盤。

### <a name="captioning-for-video-text-for-audio"></a>視訊、 音訊文字字幕功能

提供的視訊內容和音訊內容的可讀取指令碼的標題。 它也有助於提供控制項，調整音訊或視訊內容的速度，並確定該磁碟區並矔菛/縸懫按鈕也可輕鬆地尋找和使用。

### <a name="localize"></a>Localize

協助工具描述可以 （而且也應該） 當地語系化，應用程式支援多種語言。



## <a name="related-links"></a>相關連結

- [Android 的協助工具](~/android/app-fundamentals/accessibility.md)
- [iOS 協助工具](~/ios/app-fundamentals/accessibility.md)
- [OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)
- [Xamarin.Forms 協助工具](~/xamarin-forms/app-fundamentals/accessibility/index.md)
