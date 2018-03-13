---
title: "使用文字輸入"
ms.topic: article
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 170131a2449b37acfa411eeca54f7aa921b0d9e4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-text-input"></a>使用文字輸入

Apple Watch 不提供鍵盤使用者輸入的文字，不過它支援一些監看式易記替代項目：

- 從預先定義的文字選項清單中選取
- Siri 聽寫
- 選擇 emoji，
- Scribble 依字母手寫辨識 （watchOS 3 中引進）。

模擬器目前不支援聽寫，但您還是可以測試文字輸入控制站的其他選項，例如 Scribble，如下所示：

![](text-input-images/textinput-sml.png "測試的手繪多邊形選項")

若要接受監看式應用程式中的文字輸入：

1. 建立之預先定義選項的字串陣列。
2. 呼叫`PresentTextInputController`的陣列，還是不是，要允許 emoji 和`Action`，而呼叫時使用者完成。
3. 完成的動作，在測試輸入的結果並採取適當的動作 （可能設定標籤的文字值） 的應用程式中。

下列程式碼片段會對使用者顯示三個預先定義的選項：

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

`WKTextInputMode`列舉型別有三個值：

- 純
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>純

當設定的一般模式時，使用者可以選擇：

- 聽寫
- Scribble，或
- 從應用程式提供的預先定義清單。

[![](text-input-images/plain-scribble-sml.png "聽寫手繪多邊形，或從應用程式提供的預先定義清單")](text-input-images/plain-scribble.png#lightbox)

結果一律會傳回做為`NSObject`可轉換成`string`。

## <a name="emoji"></a>Emoji

有兩種類型的 emoji:

- 一般 Unicode emoji
- 動畫的影像

當使用者選擇 Unicode emoji 時，它會傳回為字串。

如果已選取動畫的影像 emoji`result`在完成處理常式將會包含`NSData`物件，其中包含 emoji `UIImage`。

## <a name="accepting-dictation-only"></a>只接受聽寫

使使用者直接聽寫螢幕，而不顯示任何建議 （或 Scribble 選項）：

- 將 [建議] 清單中的空陣列，
- Set `WatchKit.WKTextInputMode.Plain`.

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

在使用者說話期間，[監看式] 畫面會顯示下列畫面，其中包含文字，因為它可解讀的 （例如，「 這是測試 」）：

![](text-input-images/dictation.png "在使用者說話期間，[監看式] 畫面顯示的文字，它是了解")

一旦按下**完成**按鈕將傳回的文字。



## <a name="related-links"></a>相關連結

- [Apple 的文字和標籤的文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
