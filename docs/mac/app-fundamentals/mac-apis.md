---
title: 適用于 Xamarin 的 macOS Api 開發人員
description: 本檔說明如何讀取目標-C 選取器，以及如何在 Xamarin. C# Mac 應用程式中尋找其對應的方法。
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: cd427d13bb79fd31e1e814726aaaf61788ae10ec
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304986"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>適用于 Xamarin 的 macOS Api 開發人員

## <a name="overview"></a>概觀

在大部分的C#情況下，使用 Xamarin 進行開發時，您可以考慮、閱讀和撰寫，而不需要擔心基礎的目標-C api。 不過，有時候您必須閱讀 Apple 的 API 檔，將答案從 Stack Overflow 轉譯為問題的解決方案，或與現有的範例進行比較。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>讀取足夠的目標-C 是危險的

有時必須讀取目標-C 定義或方法呼叫，並將其轉譯為對等C#的方法。 讓我們看一下一個目標-C 函式定義，並細分各部分。 這個方法（目標-C 中的*選取器*）可以在 `NSTableView`上找到：

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

宣告可以從左至右讀取：

- `-` 前置詞表示它是實例（非靜態）方法。 + 表示它是類別（靜態）方法
- `(BOOL)` 是傳回型別（在中C#為 bool）
- `canDragRowsWithIndexes` 是名稱的第一個部分。
- `(NSIndexSet *)rowIndexes` 是第一個參數，且其類型為。 第一個參數的格式為： `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` 是第二個參數和其類型。 第一個後面的每個參數都是格式： `selectorPart:(Type) pararmName`
- 此訊息選取器的完整名稱為： `canDragRowsWithIndexes:atPoint:`。 請注意，`:` 在結尾，這一點很重要。
- 實際的 Xamarin. Mac C#系結為： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

此選取器調用的讀取方式可以相同：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 實例 `v` 使用傳入的兩個參數（`set` 和 `point`）來呼叫它的 `canDragRowsWithIndexes:atPoint` 選取器。
- 在C#中，方法調用看起來像這樣： `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>尋找給定C#選取器的成員

現在您已找到需要叫用的目標-C 選取器，下一個步驟是對應至對等C#成員。 有四種方法可供您嘗試（繼續 `NSTableView CanDragRows` 範例）：

1. 使用自動完成清單，快速掃描是否有相同名稱的專案。 因為我們知道它是 `NSTableView` 的實例，所以您可以輸入：

    - `NSTableView x;`
    - `x.` [ctrl + 空格鍵（如果清單未出現）。
    - `CanDrag` [enter]
    - 以滑鼠右鍵按一下方法，移至 [宣告] 以開啟 [元件瀏覽器]，您可以在其中比較 [`Export`] 屬性與 [有問題的選取器]

2. 搜尋整個類別系結。 因為我們知道它是 `NSTableView` 的實例，所以您可以輸入：

    - `NSTableView x;`
    - 以滑鼠右鍵按一下 `NSTableView`，移至 [宣告至元件瀏覽器]
    - 搜尋有問題的選取器

3. 您可以使用[XAMARIN API 線上檔](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0)。

4. Miguel 會在[這裡](https://tirania.org/tmp/rosetta.html)提供 Xamarin. Mac api 的「Rosetta 石頭」視圖，供您搜尋指定的 API。 如果您的 API 不是 AppKit 或 macOS 特有的，您可能會在該處找到。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
