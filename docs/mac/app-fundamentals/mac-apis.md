---
title: "Mac 應用程式開發介面"
description: "本文件說明如何讀取 Objective C 的選取器以及如何尋找對應的 C# 方法。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: 724edb7d23a5be5d790b43b81486d60c06df60bf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="mac-apis"></a>Mac 應用程式開發介面

## <a name="overview"></a>總覽

針對大部分的時間使用 Xamarin.Mac 進行開發，您可以考慮、 讀取及 C# 撰寫不太擔心基礎 OBJECTIVE-C api。 不過，有時候您會需要讀取 API 文件，向 Apple 針對您的問題，轉譯的回應堆疊溢位的方案，或比較現有的範例。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>讀取危險足夠 Objective C

您有時必須在讀取 OBJECTIVE-C 定義或方法呼叫，並將其轉譯至對等的 C# 方法。 讓我們看看 Objective C 函式定義和分解的片段。 這個方法 (*選取器*Objective C 中) 可找到`NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

宣告可以是從左至右讀取：

- `-`前置詞表示它是一個執行個體 （非靜態） 方法。 + 表示它是類別 （靜態） 方法
- `(BOOL)` 傳回型別 (C# 中為 bool)
- `canDragRowsWithIndexes` 為名稱的第一個部分。
- `(NSIndexSet *)rowIndexes` 是第一個參數，並與它有輸入。 第一個參數的格式： `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` 第二個參數，而它的型別。 第一個以外的每個參數為格式： `selectorPart:(Type) pararmName`
- 此訊息選取器的完整名稱是： `canDragRowsWithIndexes:atPoint:`。 請注意`:`結尾-很重要。
- 是實際 Xamarin.Mac C# 繫結： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

這個選取器引動過程可以讀取相同的方式：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 執行個體`v`有其`canDragRowsWithIndexes:atPoint`選取器使用兩個參數，呼叫`set`和`point`、 傳入。
- 在 C# 中，方法引動過程，看起來像這樣： `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>尋找給定選取器的 C# 成員

既然您已經找到您要叫用 Objective C 選取器下, 一個步驟對應的對等的 C# 成員。 有四種您可以嘗試 (繼續`NSTableView CanDragRows`範例):

1. 使用自動完成清單項目具有相同名稱的快速掃描。 既然我們知道它是的執行個體`NSTableView`您可以輸入：

    - `NSTableView x;`
    - `x.` [ctrl + 空間，如果清單不會出現）。
    - `CanDrag` [輸入]
    - 以滑鼠右鍵按一下方法，請移至宣告，以讓您比較的組件瀏覽器開啟`Export`屬性設定為有問題的選取器

2. 搜尋整個類別繫結。 既然我們知道它是的執行個體`NSTableView`您可以輸入：

    - `NSTableView x;`
    - 以滑鼠右鍵按一下`NSTableView`，請移至組件的瀏覽器的宣告
    - 搜尋問題的選取器

3. 您可以使用[Xamarin.Mac API 線上文件](https://developer.xamarin.com/api/root/monomac-lib/)。

4. Miguel 提供 Xamarin.Mac Api 」 獻給石 」 檢視[這裡](http://tirania.org/tmp/rosetta.html)，您可以搜尋指定的 api。 如果您的應用程式開發介面不是 AppKit 或 macOS 特定的您可能會發現它那里。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
