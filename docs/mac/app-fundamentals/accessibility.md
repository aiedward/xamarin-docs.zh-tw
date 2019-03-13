---
title: 在 macOS 上的協助工具
description: 本文件說明如何使用 macOS 的 Xamarin.Mac 應用程式中的協助工具功能。 它討論的分鏡腳本和程式碼、 自訂控制項，以及測試協助工具中描述的 UI 項目。
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116611"
---
# <a name="accessibility-on-macos"></a>在 macOS 上的協助工具

此頁面說明如何使用 macOS 協助工具的 Api 建置應用程式，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[Android 的協助工具](~/android/app-fundamentals/accessibility.md)並[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)其他平台 Api 的頁面。

若要了解 Api 的存取範圍中的運作方式 macOS （之前稱為 OS X），先檢閱[OS X 的協助工具模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述的 UI 項目

使用 AppKit`NSAccessibility`通訊協定來公開 Api，讓使用者介面的存取。 這包括嘗試設定有意義的值，協助工具屬性，例如設定按鈕的預設行為`AccessibilityLabel`。 標籤通常是單一字詞或簡短描述之控制項或檢視表的片語。

### <a name="storyboard-files"></a>分鏡腳本檔案

Xamarin.Mac 會使用 Xcode Interface Builder 來編輯分鏡腳本檔案。
協助工具資訊可以在中編輯**身分識別偵測器**時選取控制項的設計介面上 （如以下螢幕擷取畫面所示）：

[![在 Xcode 的 Interface Builder 中加入網頁可及性](accessibility-images/xcode.png "Xcode 的 Interface Builder 中加入協助工具")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>程式碼

Xamarin.Mac 目前不會公開為`AccessibilityLabel`setter。  新增下列協助程式方法的協助工具標籤設定：

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

這個方法可以再用於程式碼所示：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp`屬性是針對說明檢視的控制項的功能，並應該只在標籤可能不會提供足夠的資訊時才新增。 說明文字應該仍保持越短越好，如範例 「 刪除文件 」。

某些使用者介面項目無關的可存取的存取權 （例如旁邊都有自己的存取範圍標籤和說明輸入標籤）。
在這些情況下，設定`AccessibilityElement = false`，讓這些控制項或檢視將會略過由螢幕助讀程式或其他協助工具。

Apple 提供[協助工具方針](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)說明的協助工具的標籤和說明文字的最佳作法。

## <a name="custom-controls"></a>自訂控制項

請參閱 Apple 的[可存取的自訂控制項的方針](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)如需有關所需的額外步驟。

## <a name="testing-accessibility"></a>測試協助工具

提供 macOS**協助工具檢查**協助測試協助工具功能。 包含與 Xcode 偵測器。

第一次啟動時，**協助工具檢查**需要權限來控制透過協助工具的電腦：

![要求執行權限的存取範圍 Inspector](accessibility-images/accessibility-inspector-1.png "協助工具檢查要求執行權限")

解除鎖定的設定 （如有必要，在左下角） 畫面和刻度**協助工具檢查**:

![若要啟用協助工具偵測器的 [設定] 畫面](accessibility-images/accessibility-inspector-2.png "來啟用協助工具偵測器的 [設定] 畫面")

一旦啟用，偵測器會顯示為可以在螢幕上移動的浮動視窗中。 以下螢幕擷取畫面顯示執行範例的 Mac 應用程式旁邊 inspector。 資料指標移動間隔時，偵測器會顯示所有可存取的屬性，每個控制項：

[![協助工具檢查執行的範例](accessibility-images/accessibility-example.png "範例的協助工具檢查執行")](accessibility-images/accessibility-example-large.png#lightbox)

如需詳細資訊，請參閱[測試協助工具的 OS X 指南](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。



## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 的協助工具](https://www.apple.com/accessibility/mac/)
