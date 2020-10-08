---
title: 適用于 Xamarin 的 macOS Api 開發人員
description: '本檔說明如何讀取目標 C 選取器，以及如何在 Xamarin 應用程式中尋找其對應的 c # 方法。'
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: efac76338af710e716decf47635652bf8de74910
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851505"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>適用于 Xamarin 的 macOS Api 開發人員

## <a name="overview"></a>概觀

在使用 Xamarin 進行開發的大部分時候，您都可以在 c # 中考慮、讀取和寫入，而不需要擔心基礎目標 C Api。 不過，有時您需要從 Apple 讀取 API 檔、將答案從 Stack Overflow 轉譯為問題的解決方案，或與現有的範例比較。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>讀取足夠的目標 C 會造成危險

有時候必須讀取目標 C 定義或方法呼叫，並將其轉譯為對等的 c # 方法。 讓我們看看目標 C 函式定義並細分各部分。 這個方法 (目標-C) 中的 *選取器* `NSTableView` ：

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

宣告可以由左至右讀取：

- `-`前置詞表示它是 (非靜態) 方法的實例。 + 表示它是 (靜態) 方法的類別
- `(BOOL)` 這是 c # 中 (bool 的傳回型別 ) 
- `canDragRowsWithIndexes` 這是名稱的第一個部分。
- `(NSIndexSet *)rowIndexes` 是第一個參數和其類型。 第一個參數的格式如下： `(Type) paramName`
- `atPoint:(NSPoint)mouseDownPoint` 這是第二個參數及其型別。 第一個參數之後的每個參數都是格式： `selectorPart:(Type) paramName`
- 此訊息選取器的完整名稱是： `canDragRowsWithIndexes:atPoint:` 。 請注意， `:` 最後一點是很重要的。
- 實際的 Xamarin c # 系結為： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

您可以用相同的方式讀取此選取器調用：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 實例 `v` 會 `canDragRowsWithIndexes:atPoint` 使用兩個參數來呼叫其選取器， `set` 並 `point` 傳入。
- 在 c # 中，方法調用看起來像這樣： `v.CanDragRows (set, point);`

<a name="finding_selector"></a>

## <a name="finding-the-c-member-for-a-given-selector"></a>尋找指定之選取器的 c # 成員

現在您已找到需要叫用的目標 C 選取器，下一步是將其對應至對等的 c # 成員。 有四種方法可供您嘗試 (繼續 `NSTableView CanDragRows` 執行範例) ：

1. 使用自動完成清單來快速掃描相同名稱的某個內容。 由於我們知道它是的實例 `NSTableView` ，您可以輸入：

    - `NSTableView x;`
    - `x.` [ctrl + 空格鍵] （如果清單未顯示) 。
    - `CanDrag` 鍵
    - 以滑鼠右鍵按一下方法，移至 [宣告] 以開啟 [元件瀏覽器]，您可以在其中將 `Export` 屬性與有問題的選取器進行比較。

2. 搜尋整個類別系結。 由於我們知道它是的實例 `NSTableView` ，您可以輸入：

    - `NSTableView x;`
    - 按一下滑鼠右鍵 `NSTableView` ，移至元件瀏覽器的宣告
    - 搜尋有問題的選取器

3. 您可以使用 [XAMARIN API 線上檔](/dotnet/api/?view=xamarinmac-3.0) 。

4. Miguel 在這裡提供 Xamarin Api 的「Rosetta 石頭」觀點，您可以在 [這裡](https://tirania.org/tmp/rosetta.html) 搜尋指定的 API。 如果您的 API 不是 AppKit 或 macOS 特定的，您可能會發現它。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
