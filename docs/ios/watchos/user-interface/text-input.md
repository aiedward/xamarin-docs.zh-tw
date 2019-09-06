---
title: 在 Xamarin 中使用 watchOS 文字輸入
description: 本檔說明 Xamarin 中的 watchOS 文字輸入。 其中討論 PresentTextInputController 方法、scribbling、純文字、emoji 和聽寫。
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 2d4b77431c6cda1b8a7718b04a35b179ff45e0ba
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291673"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>在 Xamarin 中使用 watchOS 文字輸入

Apple Watch 不會提供使用者輸入文字的鍵盤，不過，它支援一些易記的替代選項：

- 從預先定義的文字選項清單中選取，
- Siri 聽寫，
- 選擇表情，
- 自由繪製字母（英文）手寫辨識（在 watchOS 3 中引進）。

模擬器目前不支援聽寫，但您仍然可以測試文字輸入控制器的其他選項，例如「塗抹」，如下所示：

![](text-input-images/textinput-sml.png "測試塗抹選項")

接受監看式應用程式中的文字輸入：

1. 建立預先定義選項的字串陣列。
2. 使用`PresentTextInputController`陣列呼叫、是否允許表情， `Action`以及使用者完成時所呼叫的。
3. 在完成動作中，測試輸入結果，並在應用程式中採取適當動作（可能會設定標籤的文字值）。

下列程式碼片段會向使用者呈現三個預先定義的選項：

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

- 明顯
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>明顯

當設定了 [一般] 模式時，使用者可以選擇：

- 聽寫
- 自由曲線，或
- 來自應用程式提供的預先定義清單。

[![](text-input-images/plain-scribble-sml.png "聽寫、自由曲線，或來自應用程式提供的預先定義清單")](text-input-images/plain-scribble.png#lightbox)

結果一律會當做可以轉換`NSObject` `string`成的來傳回。

## <a name="emoji"></a>Emoji

有兩種類型的表情：

- 一般 Unicode 表情
- 動畫影像

當使用者選擇 Unicode 表情時，會以字串形式傳回。

如果已選取動畫圖像表情，則`result`完成處理常式中的將會`NSData`包含包含表情`UIImage`的物件。

## <a name="accepting-dictation-only"></a>僅接受聽寫

若要將使用者直接帶到聽寫畫面，而不顯示任何建議（或自由繪製選項）：

- 為建議清單傳遞空陣列，然後
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

當使用者說話時，[監看式] 畫面會顯示下列畫面，其中包含瞭解的文字（例如「這是測試」）：

![](text-input-images/dictation.png "當使用者說話時，[監看式] 畫面會顯示已瞭解的文字")

一旦按下 [**完成**] 按鈕，就會傳回文字。



## <a name="related-links"></a>相關連結

- [Apple 的文字和標籤檔](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
