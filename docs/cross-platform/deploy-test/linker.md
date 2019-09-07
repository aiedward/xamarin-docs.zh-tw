---
title: 自訂連結器組態
description: 本文件描述用來設定連結器的 XML 檔案，以明確方式確定不會從連結的應用程式中刪除所需的程式碼。
ms.prod: xamarin
ms.assetid: F8A99E3F-2197-4399-AC81-F1DBAB5729C9
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 230fe0f168b5718c2bc91cff6dbdc078b0e6834d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765935"
---
# <a name="custom-linker-configuration"></a>自訂連結器組態

如果預設的一組選項不足以滿足需求，您可以利用描述對連結器有何需求的 XML 檔案來驅動連結程序。

您可以向連結器提供額外的定義，以確保不會將類型、方法和 (或) 欄位自應用程式中去除。 在您自己的程式碼中，慣用的方法是使用 `[Preserve]` 自訂屬性，如[在 iOS 上連結](~/ios/deploy-test/linker.md)和[在 Android 上連結](~/android/deploy-test/linker.md)指南所述。
不過，如果您需要一些來自 SDK 或產品組件的定義，則使用 XML 檔案可能是您的最佳解決方案 (與新增程式碼來確保連結器不會去除您所需的項目相比)。

若要這樣做，您需定義一個 XML 檔案，此檔案的最上層元素為 `<linker>`，其中包含 *assembly* 節點，接著包含 *type* 節點，再接著包含 *method* 和 *field* 節點。

有了這個連結器描述檔之後，請將它新增到您的專案中，然後：

- **針對 Android**將 [建置動作] 設定為 [LinkDescription]
- **針對 iOS**將 [建置動作] 設定為 [LinkDescription]

下列範例示範 XML 檔案看起來的樣子：

```xml
<linker>
        <assembly fullname="mscorlib">
                <type fullname="System.Environment">
                        <field name="mono_corlib_version" />
                        <method name="get_StackTrace" />
                </type>
        </assembly>
        <assembly fullname="My.Own.Assembly">
                <type fullname="Foo" preserve="fields">
                        <method name=".ctor" />
                </type>
                <type fullname="Bar">
                        <method signature="System.Void .ctor(System.String)" />
                        <field signature="System.String _blah" />
                </type>
                <namespace fullname="My.Own.Namespace" />
                <type fullname="My.Other*" />
        </assembly>
</linker>
```

在上述範例中，連結器將會讀取並套用 `mscorlib.dll` (隨附於 Mono for Android) 和 `My.Own.Assembly` (使用者程式碼) 組件上的指示。

第一個區段 (適用於 `mscorlib.dll`) 將可確保 `System.Environment` 類型會保留其名為 `mono_corlib_version` 的欄位及其 `get_StackTrace` 方法。
請注意，getter 和 (或) setter 方法名稱必須用來作為 IL 上的連結器工作，而不會理解 C# 屬性。

第二個區段 (適用於 `My.Own.Assembly.dll`) 將可確保 `Foo` 類型會保留其所有欄位 (亦即 `preserve="fields"` 屬性) 及其所有建構函式 (亦即 IL 中所有名為 `.ctor` 的方法)。 `Bar` 類型會針對一個建構函式 (此建構函式會接受單一字串參數) 和針對特定字串欄位 `_blah` 保留特定的簽章 (不是名稱)。
`My.Own.Namespace` 命名空間會保留其包含的所有類型。
最後，任何全名 (包括命名空間) 與萬用字元模式 "My.Other\*" 相符的類型將會保留其所有欄位和方法。 萬用字元 `*` 可以多次包含在 "type fullname" 模式中。

## <a name="related-links"></a>相關連結

- [在 iOS 上連結](~/ios/deploy-test/linker.md)
- [在 Android 上連結](~/android/deploy-test/linker.md)
