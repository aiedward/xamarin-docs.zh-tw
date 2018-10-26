---
title: macOS Xamarin.Mac 開發人員的 Api
description: 本文件說明如何讀取 OBJECTIVE-C 選取器以及如何在 Xamarin.Mac 應用程式中尋找對應的 C# 方法。
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/02/2017
ms.openlocfilehash: 209ce2b5fb2fbb357f23e6ccc9bb849fbfedc81d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115649"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS Xamarin.Mac 開發人員的 Api

## <a name="overview"></a>總覽

針對大部分的時間使用 Xamarin.Mac 開發，您可以考慮、 讀取及撰寫 C# 中，而不需要太擔心與基礎的 Objective C Api。 但是有時候會需要讀取的 API 文件，向 Apple 將方案從堆疊溢位的答案轉譯為您的問題，或比較現有的範例。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>讀取危險足夠 Objective C

有時候您必須在此處閱讀 OBJECTIVE-C 定義或方法呼叫，並將其轉譯至對等的 C# 方法。 讓我們看看 Objective C 函式定義，並細分項目。 這個方法 ( *selector* Objective c) 都位於`NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

宣告可以左讀到右：

- `-`前置詞表示它是一個執行個體 （非靜態） 方法。 + 表示它是類別 （靜態） 方法
- `(BOOL)` 是傳回的型別 （C# 中的布林值）
- `canDragRowsWithIndexes` 是名稱的第一個部分。
- `(NSIndexSet *)rowIndexes` 是第一個參數，並與它為類型。 第一個參數的格式： `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` 第二個參數和它的型別。 在第一個之後的每個參數是格式： `selectorPart:(Type) pararmName`
- 此訊息選取器的完整名稱是： `canDragRowsWithIndexes:atPoint:`。 請注意`:`結尾-很重要。
- 實際的 Xamarin.Mac C# 繫結是： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

此選取器引動過程可以讀取相同的方式：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 執行個體`v`有其`canDragRowsWithIndexes:atPoint`選取器使用兩個參數，呼叫`set`和`point`，傳入。
- 在 C# 中，方法引動過程看起來像這樣： `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>尋找指定的選取器的 C# 成員

既然您已經找到您要叫用 OBJECTIVE-C 選取器下, 一個步驟，對應至對等的 C# 成員。 有四種方法，您可以嘗試 (繼續`NSTableView CanDragRows`範例):

1. 使用自動完成清單項目具有相同名稱的快速掃描。 因為我們知道它是的執行個體`NSTableView`您可以輸入：

    - `NSTableView x;`
    - `x.` [ctrl + 空格如果清單不會出現）。
    - `CanDrag` [輸入]
    - 以滑鼠右鍵按一下方法，請移至宣告，以讓您比較的組件瀏覽器開啟`Export`屬性有問題的選取器

2. 搜尋整個類別繫結。 因為我們知道它是的執行個體`NSTableView`您可以輸入：

    - `NSTableView x;`
    - 以滑鼠右鍵按一下`NSTableView`，請移至宣告，以組件瀏覽器
    - 搜尋有問題的選取器

3. 您可以使用[Xamarin.Mac API 線上文件](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0)。

4. Miguel 提供 Xamarin.Mac Api"獻給石 」 檢視[此處](http://tirania.org/tmp/rosetta.html)，您可以搜尋指定的 api。 如果您的 API 不是 AppKit 或 macOS 特定的您可能會發現它那里。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
