---
title: 在 iOS 上的協助工具
description: 本文件說明的協助工具，在 iOS 中，討論各種屬性和功能，可用於才可以使用您的應用程式的使用者數目越好。
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/18/2016
ms.openlocfilehash: fa85459870211ff26c3bfdd3cc25f722a635952c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783567"
---
# <a name="accessibility-on-ios"></a>在 iOS 上的協助工具

此頁面描述如何使用 iOS 協助工具應用程式開發介面，以建置應用程式根據[網頁可及性檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[Android 的協助工具](~/android/app-fundamentals/accessibility.md)和[OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)其他平台應用程式開發介面的頁面。

## <a name="describing-ui-elements"></a>描述 UI 項目

iOS 提供`AccessibilityLabel`和`AccessibilityHint`內容開發人員將可供旁白的描述性文字的螢幕助讀員，讓控制項更容易存取。 控制項也可以標記一個或多個提供額外的內容中存取模式的特性。

某些控制項可能不需要存取 （適用於範例中，輸入文字或純為裝飾性影像上的標籤） –`IsAccessibilityElement`停用協助工具，在這些情況下。

**UI 設計工具**

**屬性板**包含可讓這些設定，可供編輯 iOS UI 設計工具中選取控制項時的協助工具 > 一節：

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

`AccessibilityIdentifier`用來設定的唯一金鑰，可以用來透過 UIAutomation API 的使用者介面項目參考。

值`AccessibilityIdentifier`永遠不會說出或顯示給使用者。

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法可讓使用者直接互動 （例如，當它們與特定控制項互動時） 之外引發的事件。

### <a name="announcement"></a>公告

通知使用者某個狀態已變更 （例如，背景作業已完成），可以從程式碼傳送通知。 這可能會伴隨使用者介面中的視覺指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

`LayoutChanged`公告用來當螢幕的配置：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>協助工具和當地語系化

協助工具提示與標籤可以當地語系化只屬性，例如使用者介面中的其他文字。

**MainStoryboard.strings**

如果在分鏡腳本中配置的使用者介面，您可以提供協助工具屬性的翻譯，方式與其他屬性相同。 在下列範例中，`UITextField`具有**當地語系化 ID**的`Pqa-aa-ury`和兩個設定以西班牙文的協助工具屬性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

這個檔案會放在**es.lproj**目錄西班牙文的內容。

**Localizable.strings**

或者，可以將翻譯加入至**Localizable.strings**檔案的當地語系化內容目錄中 （例如。 **es.lproj**西班牙文):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

可以使用這些翻譯時，在 C# 中透過`LocalizedString`方法：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

請參閱[iOS 當地語系化指南](~/ios/app-fundamentals/localization/index.md)如需詳細資訊，當地語系化內容。

<a name="testing" />

## <a name="testing-accessibility"></a>測試協助工具

中已啟用旁白**設定**應用程式瀏覽至**一般 > 協助工具 > 旁白**:

![](accessibility-images/settings-sml.png "設定的讀出速率")

**協助工具**畫面也會提供縮放、 文字大小、 色彩和對比選項、 語音設定和其他組態選項的設定。

請遵循這些[旁白指示](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)來測試 iOS 裝置上的存取範圍。


## <a name="simulator-testing"></a>模擬器測試

在模擬器中測試時**協助工具偵測器**來協助確認正確設定協助工具屬性和事件。 偵測器中開啟**設定**應用程式瀏覽至**一般 > 協助工具 > 協助工具偵測器**:

![](accessibility-images/settings-inspector-sml.png "啟用網頁可及性檢查")

啟用之後，[檢查] 視窗將滑鼠停留 iOS 螢幕隨時都能。
選取資料表的檢視資料列時，以下是輸出的範例 – 注意**標籤**包含提供內容的資料列以及，它 「 完成 」 的句子 (ie。 刻度會顯示):

![](accessibility-images/tableview-a11y-sml.png "使用存取範圍偵測器")

顯示偵測器時，使用在左上方"X"圖示，以暫時顯示和隱藏覆疊，並啟用/停用協助工具設定。



## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS (Apple) 的協助工具](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS 旁白](http://www.apple.com/accessibility/ios/voiceover/)
