---
title: Xamarin 應用程式中的協助工具
description: 本文件提供建立可存取的應用程式的祕訣。 比方說，它包含大型的字型、 高對比、 自我描述的介面，和多個相關的建議。
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ec264e0f3d381fdac46c79dd479da2bc768954f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668331"
---
# <a name="accessibility-in-xamarin-apps"></a>Xamarin 應用程式中的協助工具

_確定您的應用程式都能由最大可能對象_

協助工具是指設計適合作業系統顯示和輸入協助功能，例如大型的型別，高對比、 拉近的應用程式使用者介面、 螢幕閱讀 （文字轉換語音）、 visual 或 haptic 意見反應提示的概念和替代輸入的方法。

例如 iOS、 Android 和 Windows 的桌上型電腦和行動裝置平台提供內建 Api，協助開發人員建置可存取的應用程式，例如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)並[Apple 的 VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>平台專屬的 Api

若要執行本文件中的指導方針，使用每個平台所提供的 Api:

- [**Android 的協助工具**](~/android/app-fundamentals/accessibility.md)
- [**iOS 協助工具**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 的協助工具**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>協助工具檢查清單

請遵循下列提示以確保您的應用程式都可存取可能最多觀眾。 請參閱[Android 協助工具測試檢查清單](https://developer.android.com/training/accessibility/testing.html)並[Apple 的協助工具網頁](http://www.apple.com/accessibility/)如需詳細資訊。

### <a name="support-large-fonts-and-high-contrast"></a>支援大型的字型和高對比

避免硬式編碼的控制項維度，而是偏好使用可調整大小以容納較大的字型大小的版面配置。
測試以確保其為可讀取的高對比模式中的色彩配置。

### <a name="make-the-user-interface-self-describing"></a>讓使用者介面自我描述

標記提示與螢幕閱讀每個平台 Api 相容的描述性文字與您使用者介面的所有項的目。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>請確定影像和圖示有替代的文字描述

映像和應用程式使用者介面 （例如按鈕或狀態，如範例的指標） 的一部分的圖示應該加上標記的可存取的描述。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>設計具有可存取的導覽，記住的視覺化樹狀結構

使用適當的版面配置控制項，或 Api，以便使用替代輸入的方法的控制項之間瀏覽遵循相同的邏輯流程，與使用觸控式螢幕。

從螢幕助讀程式 （裝飾的映像或已欄位可供存取，例如標籤） 中排除不必要的元素。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>不要依賴單獨的音訊或色彩提示

避免的情況下進行，完成時或某個其他狀態的唯一徵兆聲音或色彩變更。 請設計使用者介面，包括清除視覺提示，（使用音效和色彩只使用增強式），或新增特定的存取範圍的指標。

選擇色彩，請盡量避免很難區分使用者色盲的調色盤。

### <a name="captioning-for-video-text-for-audio"></a>隱藏式輔助字幕的音訊視訊、 文字

提供影片的內容和音訊內容的可讀取指令碼的標題。 最好也提供調整音訊或視訊內容的速度的控制項，並確定該磁碟區，以及播放/暫停按鈕可輕鬆地尋找和使用。

### <a name="localize"></a>Localize

協助工具描述可以 （且應該） 當地語系化應用程式支援多國語言的地方。



## <a name="related-links"></a>相關連結

- [Android 的協助工具](~/android/app-fundamentals/accessibility.md)
- [iOS 協助工具](~/ios/app-fundamentals/accessibility.md)
- [OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)
- [Xamarin.Forms 的協助工具](~/xamarin-forms/app-fundamentals/accessibility/index.md)
