---
title: 在 iOS 上的協助工具
description: 本文件說明在 iOS 中，討論各種屬性和功能，可讓您的應用程式可由多個使用者盡可能的協助工具。
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179550"
---
# <a name="accessibility-on-ios"></a>在 iOS 上的協助工具

此頁面說明如何使用 iOS 協助工具 Api 建置應用程式，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[Android 的協助工具](~/android/app-fundamentals/accessibility.md)並[OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)其他平台 Api 的頁面。

## <a name="describing-ui-elements"></a>描述的 UI 項目

提供 iOS`AccessibilityLabel`和`AccessibilityHint`屬性適用於開發人員新增可用來透過旁白的描述性文字畫面讀取器，讓控制項更容易存取。 控制項也可以與一或多個提供額外的內容，在存取模式的特徵標記。

有些控制項可能不需要存取 （適用於範例中，輸入文字或純為裝飾性影像中的標籤） –`IsAccessibilityElement`停用協助工具在這些情況下提供。

**UI 設計工具**

**Properties Pad**包含可讓 iOS UI 設計工具中選取控制項時，才能編輯這些設定的存取設定 區段：

![](accessibility-images/ios-designer-sml.png "協助工具設定")

**C#**

也可以直接在程式碼中設定這些屬性：

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>什麼是 AccessibilityIdentifier？

`AccessibilityIdentifier`用來設定可用來透過 UIAutomation API 的使用者介面項目參考的唯一索引鍵。

值`AccessibilityIdentifier`永遠不會說出或顯示給使用者。

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法可讓外部 （例如，當它們與特定控制項互動時） 的直接互動使用者所產生的事件。

### <a name="announcement"></a>公告

宣告可以從程式碼中傳送，通知使用者某個狀態已變更 （例如背景作業已完成）。 這可能會伴隨使用者介面中的視覺指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

`LayoutChanged`用公告時畫面版面配置：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>協助工具和當地語系化

協助工具屬性，像是可以只是當地語系化的標籤和提示，例如使用者介面中的其他文字。

**MainStoryboard.strings**

如果使用者介面版面配置使用分鏡腳本，您可以提供協助工具屬性的翻譯和其他屬性相同的方式。 在下列範例中，`UITextField`已經**當地語系化 ID**的`Pqa-aa-ury`和西班牙文所設定的兩個協助工具屬性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

這個檔案會置於**es.lproj**西班牙文的內容目錄。

**Localizable.strings**

或者，可以翻譯新增至**Localizable.strings**檔案的當地語系化內容目錄中 （例如。 **es.lproj**西班牙文):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

這些轉譯可用在C#透過`LocalizedString`方法：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

請參閱[iOS 當地語系化指南](~/ios/app-fundamentals/localization/index.md)如需詳細資訊將內容當地語系化。

<a name="testing" />

## <a name="testing-accessibility"></a>測試協助工具

中已啟用 voiceOver**設定**瀏覽至應用程式**一般 > 協助工具 > VoiceOver**:

![](accessibility-images/settings-sml.png "設定的讀出速率")

**協助工具**畫面也會提供縮放、 文字大小、 色彩和對比選項、 語音設定和其他組態選項的設定。

請遵循這些[VoiceOver 指示](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)來測試 iOS 裝置上的協助工具。


## <a name="simulator-testing"></a>模擬器測試

在模擬器中測試時**協助工具檢查**可協助確認已正確設定協助工具屬性和事件。 在 偵測器上開啟**設定**瀏覽至應用程式**一般 > 協助工具 > 協助工具檢查**:

![](accessibility-images/settings-inspector-sml.png "啟用協助工具偵測器")

一旦啟用，偵測器視窗停留 iOS 畫面在所有的時間。
選取資料表 檢視的資料列時，以下是輸出的範例，請注意**標籤**包含提供內容的資料列，也就 「 完成 」 的句子 (亦即。 刻度會顯示):

![](accessibility-images/tableview-a11y-sml.png "使用協助工具偵測器")

偵測器顯示時，使用在左上方的"X"圖示，來暫時顯示和隱藏覆疊，並啟用/停用協助工具設定。



## <a name="related-links"></a>相關連結

- [跨平台協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS (Apple) 的協助工具](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
