---
title: 使用 watchOS 在 Xamarin 中的文字輸入
description: 本文件說明在 Xamarin 中的 watchOS 文字輸入。 它討論 PresentTextInputController 方法、 徒手畫、 純文字、 emoji，以及語音輸入。
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 2092b12254008936f2c5b6a7d9dd610ff751e802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61207392"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>使用 watchOS 在 Xamarin 中的文字輸入

Apple Watch 未提供使用者輸入文字，鍵盤，不過它確實支援一些監看式易記替代項目：

- 從預先定義的文字選項清單中選取
- Siri 聽寫
- 選擇的 emoji
- Scribble 依字母手寫辨識 （watchOS 3 引入）。

模擬器目前不支援語音輸入，但您還是可以測試的文字輸入控制器的其他選項，例如 Scribble，如下所示：

![](text-input-images/textinput-sml.png "測試手繪多邊形選項")

若要接受文字輸入中監控應用程式：

1. 建立預先定義的選項的字串陣列。
2. 呼叫`PresentTextInputController`陣列中，是否允許 emoji，，和`Action`使用者已完成時呼叫。
3. 在完成動作中，測試輸入的結果，並採取適當的動作 （可能設定標籤的文字值） 的應用程式中。

下列程式碼片段會向使用者呈現三種預先定義的選項：

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

`WKTextInputMode`列舉有三個值：

- 純
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>純

設定純的模式時，使用者可以選擇：

- 語音輸入，
- Scribble，或
- 從預先定義的清單，提供應用程式。

[![](text-input-images/plain-scribble-sml.png "聽寫 Scribble，或從應用程式提供的預先定義清單")](text-input-images/plain-scribble.png#lightbox)

結果一律會傳回做為`NSObject`可轉換成`string`。

## <a name="emoji"></a>Emoji

有兩種類型的 emoji:

- 一般 Unicode emoji
- 動畫的映像

當使用者選擇 Unicode emoji 時，它會傳回為字串。

如果已選取 動畫的影像 emoji`result`在完成處理常式將會包含`NSData`物件，其中包含 emoji `UIImage`。

## <a name="accepting-dictation-only"></a>只接受聽寫

若要採取聽寫畫面直接使用者，而不會顯示您有任何建議 （或 Scribble 選項）：

- 將 [建議] 清單中的空陣列，
- 設定`WatchKit.WKTextInputMode.Plain`。

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

當使用者說時，監看畫面會顯示下列畫面，其中包含文字，它是了解 （例如，「 這是測試 」）：

![](text-input-images/dictation.png "當使用者正在讀出時，[監看式] 畫面顯示文字，它是了解")

一旦他們按下**完成**會傳回文字的按鈕。



## <a name="related-links"></a>相關連結

- [Apple 的文字和標籤的文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
