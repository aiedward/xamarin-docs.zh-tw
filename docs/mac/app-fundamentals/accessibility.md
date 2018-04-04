---
title: MacOS 上的協助工具
description: 本指南說明建立可存取的 Xamarin.Mac 應用程式的功能。
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ad04e0276c046f133a6f71abb38912343d2d86b6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-macos"></a>MacOS 上的協助工具

此頁面描述如何使用 macOS 協助工具應用程式開發介面建置應用程式根據[網頁可及性檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[Android 的協助工具](~/android/app-fundamentals/accessibility.md)和[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)其他平台應用程式開發介面的頁面。

若要了解協助工具應用程式開發介面中的運作方式 macOS （之前稱為 OS X），第一個檢閱[OS X 協助工具模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述 UI 項目

使用 AppKit`NSAccessibility`公開 Api，可協助讓使用者介面可存取的通訊協定。 這包括嘗試設定有意義的值，協助工具屬性，例如，設定按鈕的預設行為`AccessibilityLabel`。 標籤通常是一個單字或片語的控制項或檢視表的描述。

### <a name="storyboard-files"></a>分鏡腳本檔案

Xamarin.Mac 使用 Xcode 介面產生器來編輯分鏡腳本檔案。
協助工具資訊可以在中編輯**識別 inspector**時選取控制項的設計介面上 （如下面的螢幕擷取畫面所示）：

[![在 Xcode 的介面產生器中加入協助工具](accessibility-images/xcode.png "加入 Xcode 的介面產生器中的協助工具")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>程式碼

Xamarin.Mac 目前不會公開為`AccessibilityLabel`setter。  將下列 helper 方法的協助工具標籤設定：

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

這個方法可以再用在程式碼所示：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp`屬性適用於檢視的控制項功能的說明，應該只在標籤可能不會提供足夠的資訊時才新增。 說明文字應仍保持越短越好的範例 「 刪除文件 」。

某些使用者介面項目不相關的可存取的存取 （例如標籤旁邊都有自己的協助工具標籤和說明的輸入）。
在這些情況下，設定`AccessibilityElement = false`，讓這些控制項或檢視表將會略過螢幕助讀程式或其他協助工具。

Apple 提供[可及性方針](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)，說明協助工具標籤和說明文字的最佳作法。

## <a name="custom-controls"></a>自訂控制項

請參閱 Apple[指導方針可存取的自訂控制項](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)如所需的其他步驟的詳細資訊。

## <a name="testing-accessibility"></a>測試協助工具

提供 macOS**協助工具偵測器**協助測試協助工具功能。 偵測器隨附於 Xcode。

第一次啟動時，**協助工具偵測器**需要權限來控制透過 協助工具的電腦：

![要求執行權限的存取範圍 Inspector](accessibility-images/accessibility-inspector-1.png "要求執行權限的存取範圍偵測器")

解除鎖定設定畫面中 （如果有必要，請在左下方） 和刻度**協助工具偵測器**:

![若要啟用協助工具偵測器的 [設定] 畫面](accessibility-images/accessibility-inspector-2.png "啟用協助工具偵測器的 [設定] 畫面")

啟用之後，偵測器會顯示為可在螢幕上移動的浮動視窗中。 下列螢幕擷取畫面顯示旁邊範例 Mac 應用程式執行的偵測器。 由於將游標移到視窗時，偵測器會顯示所有可存取的屬性，每個控制項：

[![協助工具偵測器執行的範例](accessibility-images/accessibility-example.png "範例的協助工具偵測器正在執行")](accessibility-images/accessibility-example-large.png#lightbox)

如需詳細資訊，請參閱[測試協助工具的 OS X 指南](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。



## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 的協助工具](https://www.apple.com/accessibility/mac/)
