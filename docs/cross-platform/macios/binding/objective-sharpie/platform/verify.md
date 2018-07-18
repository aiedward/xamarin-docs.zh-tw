---
title: 目標 Sharpie 驗證屬性
description: 本文件說明目標 Sharpie 所產生的 [驗證] 屬性。 開發人員，他們應該以手動方式驗證目標 Sharpie 輸出，反白顯示 [確認] 屬性。
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4bca896afb4dfc96fd6c1d7cdf489feb6a879e31
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855025"
---
# <a name="objective-sharpie-verify-attributes"></a>目標 Sharpie 驗證屬性

您通常會發現目標 Sharpie 所產生的繫結會以註解`[Verify]`屬性。 這些屬性會指出您應該_確認_目標 Sharpie 未正確的動作，藉由比較原始的 C/Objective C 宣告 （這會在繫結宣告上方的註解中提供） 的繫結。

驗證建議_所有_繫結宣告，但很可能_必要_宣告標註的`[Verify]`屬性。 這是因為在許多情況下，沒有足夠的中繼資料推斷如何最能產生繫結原生來源原始程式碼。 您可能需要參考文件或要做出最佳的繫結決策的標頭檔內的程式碼註解。

一旦確認繫結是修正或已修正它才能正確，_移除_`[Verify]`從繫結的屬性。

> [!IMPORTANT]
> `[Verify]` 屬性會刻意造成 C# 編譯錯誤，以便您不得不確認繫結。 您應該移除`[Verify]`屬性時檢閱 （並可能已更正） 程式碼。

## <a name="verify-hints-reference"></a>驗證提示參考

提供給屬性的提示引數可以是跨參考下列文件。 針對任何產生的文件`[Verify]`繫結完成後，將主控台也提供屬性。

|`[Verify]` 提示|描述|
|---|---|
|InferredFromPreceedingTypedef|此宣告的名稱由從常見的慣例推斷立即先前`typedef`原始原生的原始程式碼中。 請確認推斷的名稱正確，因為此慣例模稜兩可。|
|ConstantsInterfaceAssociation|沒有任何笨蛋證明方法來判斷哪些 Objective C 介面外部變數宣告可能會產生關聯。 這些執行個體繫結為`[Field]`成接近-的具象介面以產生更具直覺性的 API，並可能消除 'Constants' 部分介面中的屬性完全介面。|
|MethodToProperty|OBJECTIVE-C 方法繫結為 C# 屬性因為慣例，例如不採用任何參數和傳回值 （非 void 傳回）。 通常這類的方法應該繫結為屬性來呈現到目前的 API，但有時可能會發生誤判，應實際繫結的方法。|
|StronglyTypedNSArray|原生`NSArray*`繫結為`NSObject[]`。 可能會更強的期望透過 API 文件 （例如註解標頭檔） 設定為基礎的繫結中的類型陣列或藉由檢查透過測試陣列的內容。 比方說，NSArray * 包含只 NSNumber * 做為繫結 instancescan`NSNumber[]`而不是`NSObject[]`。|

您可以快速收到提示，使用說明文件`sharpie verify-docs`工具，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

## <a name="related-links"></a>相關連結

- [Xamarin University 課程： 建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程： 建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
