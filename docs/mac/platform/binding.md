---
title: Xamarin. Mac 的系結 Mac 程式庫
description: 本檔連結的指南會說明如何在 Xamarin. Mac 應用程式中使用目標 C 系結，包括目標 Sharpie 和範例程式碼。
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 01/13/2017
ms.openlocfilehash: c478437a9c84475e8c31484523db16336f8808e6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029925"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>Xamarin. Mac 的系結 Mac 程式庫

請遵循下列連結，以瞭解如何在 Xamarin 上系結目標 C 程式庫：

- [**總覽**](~/cross-platform/macios/binding/overview.md)-
  說明系結的運作方式。
- 系結[**目標-c 程式庫**](~/cross-platform/macios/binding/objective-c-libraries.md)-
  如何系結目標 C 程式庫以在 Xamarin 專案中使用的指示。
- [**類型定義參考指南**](~/cross-platform/macios/binding/binding-types-reference.md)-
  描述系結作者用來驅動系結產生進程的所有屬性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是命令列工具，可協助啟動程式的第一次傳遞。
其運作方式是剖析原生程式庫的標頭檔，將公用 API 對應至系結[定義](~/cross-platform/macios/binding/binding-types-reference.md)（以手動方式完成的進程）。 目標 Sharpie 不會自行建立系結，但可協助您開始著手！

## <a name="examples"></a>範例

請參閱[XMBindingExample Mac 範例](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample)，以瞭解如何使用系結專案來建立 Mac 系結。

## <a name="related-links"></a>相關連結

- [繫結 Objective-C](~/cross-platform/macios/binding/index.md)
- [系結 iOS 程式庫](~/ios/platform/binding-objective-c/index.md)
