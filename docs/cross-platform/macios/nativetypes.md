---
title: IOS 和 macOS 的原生類型
description: 本檔說明 Xamarin 的 Unified API 如何根據編譯目標架構, 將 .NET 類型對應到32位和64位原生類型。
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 6b6a7b8bb2efdd099d7fde6bb5397e6330258bcc
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199398"
---
# <a name="native-types-for-ios-and-macos"></a>IOS 和 macOS 的原生類型

Mac 和 iOS Api 會使用在32位平臺上一律為32位的架構專屬資料類型, 以及在64位平臺上的64位。

例如, 目標-C 會將`NSInteger`資料類型對應到`int32_t` 32 位系統上的, 以及`int64_t` 64 位系統上的。

為了符合此行為, 我們在我們的統一 API 上將先前的`int`使用 (在 .net 中定義為`System.Int32`always as) 取代為新的資料類型: `System.nint`。 您可以將 "n" 視為意義「原生」, 因此是平臺的原生整數類型。

使用這些新的資料類型時, 會根據您的編譯旗標, 針對32位和64位架構編譯相同的原始程式碼。

## <a name="new-data-types"></a>新的資料類型

下表顯示我們的資料類型變更, 以符合這個新的 32/64 位世界:

|原生類型|32位支援類型|64位支援類型|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

我們已選擇這些名稱, 讓C#您的程式碼看起來會像今天一樣更有其外觀。

### <a name="implicit-and-explicit-conversions"></a>隱含和明確轉換

新資料類型的設計主要是讓單一C#來源檔案自然地使用32或64位儲存體, 視主機平臺和編譯設定而定。

這需要我們設計一組從平臺特定資料類型到 .NET 整數和浮點資料類型的隱含和明確轉換。

當沒有資料遺失的可能性 (32 位值儲存在64位空間) 時, 就會提供隱含轉換運算子。

當可能遺失資料時, 會提供明確的轉換運算子 (64 位值會儲存在32或可能的32儲存位置)。

`int`、 `uint` `nint`和`float`全都可隱含轉換成, `nuint`而`nfloat` 32 位一律會納入32或64位。

`nint`、 `nuint` `long`和`nfloat`全都可隱含轉換成, `ulong`而且`double` 32 或64位值一律會納入64位儲存區中。

您必須使用從`nint`、 `nuint`和`nfloat`到`int`的明確轉換`uint` , `float`因為原生類型可能會保留64位的儲存區。

您必須使用從`long`、 `ulong`和`double`到`nint`的明確轉換`nuint` , `nfloat`而且因為原生類型可能只能保存32位的儲存區。

## <a name="coregraphics-types"></a>CoreGraphics 類型

與 CoreGraphics 搭配使用的點、大小和矩形資料類型, 會根據其執行所在的裝置來使用32或64位。  當我們最初系結 iOS 和 Mac Api 時, 我們使用了現有的資料結構來符合主機平臺的大小 (中`System.Drawing`的資料類型)。

移至 [**整合**] 時, 您必須將的`System.Drawing`實例取代為其`CoreGraphics`對應專案, 如下表所示:

|System 中的舊型別。繪圖|新增資料類型 CoreGraphics|描述|
|--- |--- |--- |
|`RectangleF`|`CGRect`|保存浮點矩形資訊。|
|`SizeF`|`CGSize`|保存浮點大小資訊 (寬度、高度)|
|`PointF`|`CGPoint`|保存浮點、點資訊 (X、Y)|

舊的資料類型會使用浮點數來儲存資料結構的元素, 而新的則使用`System.nfloat`。

## <a name="related-links"></a>相關連結

- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
