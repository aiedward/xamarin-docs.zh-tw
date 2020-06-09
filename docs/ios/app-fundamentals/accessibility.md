---
title: IOS 上的協助工具
description: 本檔描述 iOS 中的協助工具，討論各種可用來讓您的應用程式可供多個使用者使用的屬性和功能。
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/18/2016
ms.openlocfilehash: 2259566fc6342a40a8c0a94bacd1c146b6509d52
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574154"
---
# <a name="accessibility-on-ios"></a>IOS 上的協助工具

此頁面說明如何使用 iOS 協助工具 Api，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)來建立應用程式。
如需其他平臺 Api，請參閱[Android 協助工具](~/android/app-fundamentals/accessibility.md)和[OS X 協助工具](~/mac/app-fundamentals/accessibility.md)頁面。

## <a name="describing-ui-elements"></a>描述 UI 元素

iOS 提供 `AccessibilityLabel` 和 `AccessibilityHint` 屬性，讓開發人員新增描述性文字，可供 VoiceOver 螢幕讀取器用來使控制項更容易存取。 控制項也可以使用一或多個特性來標記，以在可存取的模式中提供其他內容。

有些控制項可能不需要存取（例如，文字輸入上的標籤或純裝飾的影像）– `IsAccessibilityElement` 提供在這些情況下停用存取範圍。

**UI 設計工具**

**Properties Pad**包含一個輔助功能區段，可讓您在 IOS UI 設計工具中選取控制項時編輯這些設定：

![](accessibility-images/ios-designer-sml.png "Accessibility Settings")

**C#**

這些屬性也可以直接在程式碼中設定：

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>什麼是 AccessibilityIdentifier？

`AccessibilityIdentifier`是用來設定唯一索引鍵，可用來透過 UIAUTOMATION API 來參考使用者介面元素。

的值永遠不會 `AccessibilityIdentifier` 讀出或向使用者顯示。

<a name="postnotification"></a>

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法可讓事件在直接互動以外的地方引發（例如，當使用者與特定控制項互動時）。

### <a name="announcement"></a>通知

您可以從程式碼傳送公告，通知使用者某個狀態已變更（例如背景作業已完成）。 這可能會伴隨使用者介面中的視覺指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

`LayoutChanged`當螢幕版面配置時，會使用此公告：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>協助工具和當地語系化

像是標籤和提示之類的協助工具屬性可以當地語系化，就像使用者介面中的其他文字。

**Mainstoryboard.storyboard 字串**

如果使用者介面配置於分鏡腳本中，您可以使用與其他屬性相同的方式提供協助工具屬性的翻譯。 在下列範例中， `UITextField` 具有的**當地語系化識別碼** `Pqa-aa-ury` ，以及在西班牙文中設定兩個協助工具屬性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

這個檔案會放在 **.lproj**目錄中，以取得西班牙文內容。

**可當地語系化的字串。**

或者，您也可以將翻譯新增至當地語系化內容目錄中的可**當地語系化字串**檔案（例如 **.lproj** for 西班牙文）：

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

這些翻譯可以透過方法在 c # 中使用 `LocalizedString` ：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

如需當地語系化內容的詳細資訊，請參閱[iOS 當地語系化指南](~/ios/app-fundamentals/localization/index.md)。

<a name="testing"></a>

## <a name="testing-accessibility"></a>測試協助工具

流覽至 **[一般] > [協助工具 > VoiceOver**]，即可在 [**設定**] 應用程式中啟用 VoiceOver：

![](accessibility-images/settings-sml.png "Setting the speaking rate")

[**協助工具**] 畫面也提供 [縮放]、[文字大小]、[色彩] & 對比選項]、[語音設定] 和其他設定選項的設定。

請遵循這些[VoiceOver 指示](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)來測試 iOS 裝置上的協助工具。

## <a name="simulator-testing"></a>模擬器測試

在模擬器中測試時，**協助工具偵測器**可供使用，以協助驗證協助工具屬性和事件已正確設定。 流覽至 **[一般] > [協助工具] [> 輔助**功能偵測器]，在 [**設定**] 應用程式中開啟檢查

![](accessibility-images/settings-inspector-sml.png "Enable Accessibility Inspector")

啟用之後，[偵測器] 視窗會隨時停留在 iOS 畫面上。
當選取資料表視圖資料列時，以下是輸出的範例-請注意，**標籤**包含的句子會提供資料列的內容，而且它也是「完成」（也就是顯示刻度）：

![](accessibility-images/tableview-a11y-sml.png "Using Accessibility Inspector")

當偵測器可見時，請使用左上角的 "X" 圖示來暫時顯示和隱藏重迭，並啟用/停用協助工具設定。

## <a name="related-links"></a>相關連結

- [跨平台協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS 協助工具（Apple）](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](https://www.apple.com/accessibility/ios/voiceover/)
