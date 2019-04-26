---
title: 適用於 iOS 和 macOS 的原生類型
description: 本文件說明 Xamarin 的統一 API 為 32 位元和 64 位元原生類型，視所對應的.NET 類型編譯目標架構為基礎。
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199625"
---
# <a name="native-types-for-ios-and-macos"></a>適用於 iOS 和 macOS 的原生類型

Mac 和 iOS Api 使用一律會在 32 位元平台和 64 位元平台上的 64 位元的 32 位元的架構特定的資料類型。

Objective C 的對應，例如`NSInteger`資料類型`int32_t`32 位元系統上以及`int64_t`64 位元系統上。

比對這種行為，我們統一的 API，我們要取代先前使用`int`(在.NET 會定義為永遠`System.Int32`) 成新的資料類型： `System.nint`。 您可以想像"n"名的意義 「 原生 」，因此輸入平台的原生的整數。

使用這些新的資料類型，相同的原始程式碼會編譯為 32 位元和 64 位元的架構，根據您的編譯旗標。

## <a name="new-data-types"></a>新的資料類型

下表顯示我們的資料型別，以符合這個新的 32/64 位元世界中的變更：

|原生類型|32 位元支援的類型|64 位元支援的類型|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

我們選擇了這些名稱，以允許您C#程式碼，以增加或減少看起來會像這樣今天的相同方式。

### <a name="implicit-and-explicit-conversions"></a>隱含和明確轉換

新的資料型別的設計目的為了允許在單一C#自然地使用 32 或 64 位元儲存體，根據主機平台和編譯設定的原始程式檔。

這需要我們能夠設計一組的隱含和明確轉換與平台特定的資料型別至.NET 整數和浮點資料類型。

不可能會發生資料遺失 （32 位元值儲存在 64 位元的空間） 時，會提供隱含的轉換運算子。

當沒有資料遺失的可能性時，提供明確轉換運算子 （64 位元值儲存在 32 個可能 32 的儲存位置）。

 `int``uint`並`float`全都隱含地轉換成`nint`，`nuint`和`nfloat`一律會以 32 或 64 位元可放入 32 位元。

 `nint``nuint`並`nfloat`全都隱含地轉換成`long`，`ulong`和`double`一律會在 64 位元儲存體中可放入 32 或 64 位元值。

您必須使用明確的轉換，從`nint`，`nuint`並`nfloat`成`int`，`uint`和`float`因為原生型別可能會佔據的儲存體的 64 位元。

您必須使用明確的轉換，從`long`，`ulong`並`double`成`nint`，`nuint`和`nfloat`因為可能僅能保留儲存體的 32 位元原生型別。

## <a name="coregraphics-types"></a>CoreGraphics 類型

可搭配 CoreGraphics 點、 大小和矩形的資料類型會使用根據裝置執行的 32 或 64 位元。  我們原先繫結的 iOS 和 Mac Api 時我們會使用現有的資料結構剛好符合主機平台的大小 (資料類型在`System.Drawing`)。

移至時**Unified**，您必須取代的執行個體`System.Drawing`與其`CoreGraphics`下表所示的對應項目：

|System.Drawing 中的舊型別|新的資料型別 CoreGraphics|描述|
|--- |--- |--- |
|`RectangleF`|`CGRect`|浮動點矩形資訊的保留。|
|`SizeF`|`CGSize`|浮點數的保存點 （width、 height） 的大小資訊|
|`PointF`|`CGPoint`|保留浮點數、 點資訊 (X，Y)|

新的其中一個使用舊的資料類型使用浮點數儲存的資料結構中，項目，而`System.nfloat`。

## <a name="related-links"></a>相關連結

- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
