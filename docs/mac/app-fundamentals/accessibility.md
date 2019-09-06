---
title: MacOS 上的協助工具
description: 本檔說明如何在 Xamarin. Mac 應用程式中使用 macOS 協助工具功能。 討論如何在分鏡腳本和程式碼、自訂控制項和測試協助工具中說明 UI 元素。
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: e92887dc54acc33e6d433aafe39ae7df9ea8ac71
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291656"
---
# <a name="accessibility-on-macos"></a>MacOS 上的協助工具

此頁面說明如何使用 macOS 協助工具 Api，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)來建立應用程式。
如需其他平臺 Api，請參閱[Android 協助工具](~/android/app-fundamentals/accessibility.md)和[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)頁面。

若要瞭解協助工具 Api 在 macOS （先前稱為 OS X）中的使用方式，請先參閱[Os x 協助工具模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述 UI 元素

AppKit 會使用`NSAccessibility`通訊協定來公開 api，讓使用者介面可供存取。 這包括預設行為，會嘗試為輔助功能屬性設定有意義的值，例如設定按鈕的`AccessibilityLabel`。 標籤通常是描述控制項或視圖的單一單字或簡短片語。

### <a name="storyboard-files"></a>分鏡腳本檔案

Xamarin 會使用 Xcode Interface Builder 來編輯分鏡腳本檔案。
在設計介面上選取控制項時，可以在身分**識別偵測器**中編輯協助工具資訊（如下列螢幕擷取畫面所示）：

[![在 Xcode 的 Interface Builder 中新增協助工具](accessibility-images/xcode.png "在 Xcode 的 Interface Builder 中新增協助工具")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>程式碼

Xamarin 目前不會以 setter 的形式`AccessibilityLabel`公開。  新增下列 helper 方法以設定 [協助工具] 標籤：

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

這個方法可以在程式碼中使用，如下所示：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp`屬性是用來說明控制項或視圖的用途，而且只有在標籤可能無法提供足夠的資訊時才會加入。 解說文字仍然應該盡可能地保持簡短，例如「刪除檔」。

某些使用者介面專案與可存取的存取無關（例如，具有自己的 [協助工具] 標籤和說明之輸入旁的標籤）。
在這些情況下， `AccessibilityElement = false`請設定，讓螢幕閱讀程式或其他協助工具工具略過這些控制項或瀏覽器。

Apple 提供[協助工具指導方針](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)，說明協助工具標籤和解說文字的最佳作法。

## <a name="custom-controls"></a>自訂控制項

如需其他必要步驟的詳細資訊，請參閱 Apple 的[可存取自訂控制項的指導方針](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)。

## <a name="testing-accessibility"></a>測試協助工具

macOS 提供協助工具偵測**器**，可協助測試協助工具功能。 此偵測器包含在 Xcode 中。

第一次啟動時，**協助工具偵測器**會要求透過協助工具控制電腦的許可權：

![協助工具檢查要求執行的許可權](accessibility-images/accessibility-inspector-1.png "協助工具檢查要求執行的許可權")

解除鎖定 [設定] 畫面（如有需要，在左下方）並勾選 [**協助工具偵測器**]：

![啟用協助工具偵測器的設定畫面](accessibility-images/accessibility-inspector-2.png "啟用協助工具偵測器的設定畫面")

啟用之後，偵測器會顯示為可在螢幕周圍移動的浮動視窗。 下列螢幕擷取畫面顯示在範例 Mac 應用程式旁執行的偵測器。 當游標移到視窗上方時，偵測器會顯示每個控制項的所有可存取屬性：

[執行![協助工具偵測器的範例]執行(accessibility-images/accessibility-example.png "協助工具偵測器的範例")](accessibility-images/accessibility-example-large.png#lightbox)

如需詳細資訊，請參閱[OS X 的測試協助工具指南](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。



## <a name="related-links"></a>相關連結

- [跨平臺協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 協助工具](https://www.apple.com/accessibility/mac/)
