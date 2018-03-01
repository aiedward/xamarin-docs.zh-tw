---
title: "確認屬性"
ms.topic: article
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cda523cd9d762c3a3c1570e2abd0acb8a264d5dd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="verify-attributes"></a>確認屬性


您通常會發現目標 Sharpie 所產生的繫結會以註解`[Verify]`屬性。 這些屬性會指出您應該_確認_目標 Sharpie 未正確的做法，藉由比較原始 C/Objective C 宣告 （這會提供繫結的宣告上方的註解） 的繫結。

驗證建議用於_所有_繫結的宣告，但很可能是_必要_宣告附註的`[Verify]`屬性。 這是因為在許多情況下，沒有足夠的中繼資料來推斷如何最產生繫結原始來源原生程式碼中。 您可能需要參考文件或程式碼註解內的標頭檔，以便做出最佳的繫結決策。

一旦確認繫結修正或已修正它才能正確，_移除_`[Verify]`從繫結的屬性。

> [!IMPORTANT]
> `[Verify]` 屬性會刻意造成 C# 編譯錯誤，讓您強制以確認繫結。 您應該移除`[Verify]`屬性時檢閱 （並可能修正） 程式碼。

## <a name="verify-hints-reference"></a>驗證提示參考

提供給屬性提示引數可以是交互參考下列文件。 任何產生的文件`[Verify]`繫結完成之後，將主控台也提供屬性。

<table>
  <thead>
  <tr>
    <th>確認提示</th>
    <th>描述</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>InferredFromPreceedingTypedef</td>
    <td>此宣告的名稱由從的常見慣例推斷立即前面<code>typedef</code>在原始的原生程式碼中。 請確認推斷的名稱正確，因為這個慣例會模稜兩可。</td>
  </tr>
  <tr>
    <td>ConstantsInterfaceAssociation</td>
    <td>沒有任何是傻瓜證明方法來判斷哪個 Objective C 介面與外部變數宣告可能會在相關聯。 這些執行個體繫結為<code>[Field]</code>完全介面部分的介面到接近依據具體介面以產生更具直覺性的 API，可能消除 '常數' 中的屬性。</td>
  </tr>
  <tr>
    <td>MethodToProperty</td>
    <td>Objective C 方法繫結為 C# 屬性，因為慣例，例如不採用任何參數和傳回值 （非 void 傳回）。 通常這類方法應該繫結為屬性來呈現沒用的 API，但是 false 誤判有時可能會發生，而且應實際繫結方法。</td>
  </tr>
  <tr>
    <td>StronglyTypedNSArray</td>
    <td>原生<code>NSArray*</code>繫結為<code>NSObject[]</code>。 可能會更強型別根據期望設定透過應用程式開發介面文件 （例如註解標頭檔） 的繫結中的陣列或藉由檢查透過測試陣列的內容。 例如，NSArray * 包含只 NSNumber * instancescan 繫結為<code>NSNumber[]</code>而不是<code>NSObject[]</code>。</td>
  </tr>
  </tbody>
</table>

您可以快速接收文件的提示，使用`sharpie verify-docs`工具，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

