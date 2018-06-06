---
title: 目標 Sharpie 確認屬性
description: 本文件說明目標 Sharpie 所產生的 [驗證] 屬性。 開發人員而言，他們應該手動驗證目標 Sharpie 輸出，反白顯示 [驗證] 屬性。
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 96e5bafc14c2d3aba03ccc137151a83ee8afeef9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780707"
---
# <a name="objective-sharpie-verify-attributes"></a>目標 Sharpie 確認屬性

您通常會發現目標 Sharpie 所產生的繫結會以註解`[Verify]`屬性。 這些屬性會指出您應該_確認_目標 Sharpie 未正確的做法，藉由比較原始 C/Objective C 宣告 （這會提供繫結的宣告上方的註解） 的繫結。

驗證建議用於_所有_繫結的宣告，但很可能是_必要_宣告附註的`[Verify]`屬性。 這是因為在許多情況下，沒有足夠的中繼資料來推斷如何最產生繫結原始來源原生程式碼中。 您可能需要參考文件或程式碼註解內的標頭檔，以便做出最佳的繫結決策。

一旦確認繫結修正或已修正它才能正確，_移除_`[Verify]`從繫結的屬性。

> [!IMPORTANT]
> `[Verify]` 屬性會刻意造成 C# 編譯錯誤，讓您強制以確認繫結。 您應該移除`[Verify]`屬性時檢閱 （並可能修正） 程式碼。

## <a name="verify-hints-reference"></a>驗證提示參考

提供給屬性提示引數可以是交互參考下列文件。 任何產生的文件`[Verify]`繫結完成之後，將主控台也提供屬性。

|`[Verify]` 提示|描述|
|---|---|
|InferredFromPreceedingTypedef|此宣告的名稱由從的常見慣例推斷立即前面`typedef`在原始的原生程式碼中。 請確認推斷的名稱正確，因為這個慣例會模稜兩可。|
|ConstantsInterfaceAssociation|沒有任何是傻瓜證明方法來判斷哪個 Objective C 介面與外部變數宣告可能會在相關聯。 這些執行個體繫結為`[Field]`完全介面部分的介面到接近依據具體介面以產生更具直覺性的 API，可能消除 '常數' 中的屬性。|
|MethodToProperty|Objective C 方法繫結為 C# 屬性，因為慣例，例如不採用任何參數和傳回值 （非 void 傳回）。 通常這類方法應該繫結為屬性來呈現沒用的 API，但是 false 誤判有時可能會發生，而且應實際繫結方法。|
|StronglyTypedNSArray|原生`NSArray*`繫結為`NSObject[]`。 可能會更強型別根據期望設定透過應用程式開發介面文件 （例如註解標頭檔） 的繫結中的陣列或藉由檢查透過測試陣列的內容。 例如，NSArray * 包含只 NSNumber * instancescan 繫結為`NSNumber[]`而不是`NSObject[]`。|

您可以快速接收文件的提示，使用`sharpie verify-docs`工具，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

