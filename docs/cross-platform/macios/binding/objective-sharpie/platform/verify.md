---
title: 目標 Sharpie 驗證屬性
description: 本檔說明目標 Sharpie 所產生的 [Verify] 屬性。 [Verify] 屬性會對開發人員特別強調，他們應該在此手動驗證目標 Sharpie 的輸出。
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: eb4a992255fa26be1e83f27f93755e0fd8bda0a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765680"
---
# <a name="objective-sharpie-verify-attributes"></a>目標 Sharpie 驗證屬性

您通常會發現，目標 Sharpie 所產生的系結會以`[Verify]`屬性標注。 這些屬性工作表示您應該藉由比較系結與原始的 C/目標-C 宣告（將會在系結宣告上方的批註中提供），來_驗證_目標 Sharpie 是否執行正確的動作。

建議對_所有_系結宣告進行驗證，但最有可能_需要_針對以`[Verify]`屬性標注的宣告。 這是因為在許多情況下，原始原生原始程式碼中沒有足夠的中繼資料來推斷如何最佳產生系結。 您可能需要參考標頭檔中的檔或程式碼批註，以做出最佳的系結決策。

一旦您確認系結正確或已修正其正確，請從系結中_移除_該`[Verify]`屬性。

> [!IMPORTANT]
> `[Verify]`屬性刻意造成C#編譯錯誤，因此您必須強制驗證系結。 當您已檢查`[Verify]` （而且可能已更正）程式碼時，您應該移除屬性。

## <a name="verify-hints-reference"></a>驗證提示參考

提供給屬性的提示引數可以與下列檔交叉參考。 在完成系結`[Verify]`之後，主控台上也會提供任何所產生屬性的檔。

|`[Verify]`提示|說明|
|---|---|
|InferredFromPreceedingTypedef|這個宣告的名稱是由通用慣例從原始機器碼的正上方`typedef`推斷而來。 請確認推斷的名稱是正確的，因為此慣例不明確。|
|ConstantsInterfaceAssociation|沒有任何欺騙的方法可以判斷外部變數宣告可能會產生關聯的目標 C 介面。 這些實例會`[Field]`當做部分介面中的屬性系結到近接的具體介面，以產生更直覺化的 API，可能會完全排除 ' 常數 ' 介面。|
|MethodToProperty|因為慣例（例如不接受任何參數並C#傳回值）（非 void 傳回），所以會將目標 C 方法系結為屬性。 通常這類方法應該系結為屬性以呈現更好 API，但有時可能會發生誤報，而且系結實際上應該是方法。|
|StronglyTypedNSArray|原生`NSArray*`已系結`NSObject[]`為。 您可能可以根據 API 檔中的預期設定（例如標頭檔中的批註）或透過測試檢查陣列內容，更強地在系結中輸入陣列。 例如，僅包含 NSNumber * instancescan 的`NSNumber[]` `NSObject[]`NSArray * 會系結為，而不是。|

您也可以使用`sharpie verify-docs`工具快速接收提示的檔，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
