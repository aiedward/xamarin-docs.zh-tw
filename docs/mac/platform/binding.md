---
title: Xamarin.mac 的繫結 Mac 程式庫
description: 本文件所連結說明如何使用 OBJECTIVE-C 繫結在 Xamarin.Mac 應用程式，包括目標 Sharpie 和範例程式碼的指南。
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 01/13/2017
ms.openlocfilehash: fde21b2056d56cbf1c4768b287e29f559390f500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032477"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>Xamarin.mac 的繫結 Mac 程式庫

請遵循下列連結以深入了解繫結 Objective C 程式庫上 Xamarin.Mac:

- [**概觀**](~/cross-platform/macios/binding/overview.md) -
  說明繫結的運作方式。
- [**繫結 Objective C 程式庫**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何繫結 Objective C 程式庫，Xamarin 專案中使用的指示。
- [**輸入定義參考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  說明所有的驅動程式繫結產生處理序的繫結作者可以使用的屬性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是命令列工具，幫助啟動程序繫結的第一個階段。
它藉由剖析對應到公用 API 的原生程式庫的標頭檔的運作方式[繫結定義](~/cross-platform/macios/binding/binding-types-reference.md)（否則手動完成這個程序）。 目標 Sharpie 不會建立繫結本身，但它可以幫助您開始使用 ！

## <a name="examples"></a>範例

請參閱[XMBindingExample Mac 範例](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample)以了解如何建立 Mac 繫結使用繫結專案。

## <a name="related-links"></a>相關連結

- [繫結 Objective-C](~/cross-platform/macios/binding/index.md)
- [繫結 iOS 程式庫](~/ios/platform/binding-objective-c/index.md)
