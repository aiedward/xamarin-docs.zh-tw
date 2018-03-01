---
title: "原生類型"
ms.topic: article
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: b78ade19efed92ab3b2d8ba790f2d7334472bab4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="native-types"></a>原生類型

差異的核心 Mac 和 iOS 應用程式開發介面使用永遠會在 32 位元平台上，在 64 位元平台上的 64 位元的 32 位元架構特定的資料類型。

Objective C 的對應，例如`NSInteger`資料型別`int32_t`32 位元系統上和`int64_t`64 位元系統上。

要比對這種行為，我們有統一的 API，我們要取代的先前使用`int`(在.NET 中會定義為一律`System.Int32`) 成新的資料類型： `System.nint`。  您可以將"n"視為意義 「 原生 」，因此原生整數類型的平台。

使用這些新的資料類型，相同的原始碼會編譯為 32 位元、 32 位元和 64 位元或 64 位元，視您編譯旗標而定。

## <a name="new-data-types"></a>新的資料類型

下表顯示我們的資料型別，以符合這個新 32/64 位元環境中的變更：

<table>
        <tr>
            <th>原生類型</th>
            <th>32 位元支援類型</th> 
            <th>64 位元支援類型</th>
        </tr>
        <tr>
            <td><code>System.nint</code></td>
        <td><code>System.Int32</code> (<code>int</code>)</td>
        <td><code>System.Int64</code> (<code>long</code>)</td>
        </tr>
        <tr>
            <td><code>System.nuint</code></td>
        <td><code>System.UInt32</code> (<code>uint</code>)</td>
        <td><code>System.UInt64</code> (<code>ulong</code>)</td>
        </tr>
        <tr>
            <td><code>System.nfloat</code></td>
        <td><code>System.Single</code> (<code>float</code>)</td>
        <td><code>System.Double</code> (<code>double</code>)</td>
        </tr>
    </table>

我們之所以選擇這些名稱可讓您 C# 程式碼增加或減少看起來會像這樣今天的相同方式。

### <a name="implicit-and-explicit-conversions"></a>隱含和明確轉換

新的資料類型的設計目的被為了讓單一 C# 原始程式檔自然可 32 或 64 位元儲存體，根據主機平台和編譯設定。

這需要我們設計一組隱含和明確轉換平台專屬的資料類型的.NET 點整數和浮點資料類型。

不可能發生資料遺失 （32 位元值儲存在 64 位元空間） 的時，會提供隱含轉換運算子。

當沒有資料遺失的可能性時，提供明確轉換運算子 （64 位元值儲存在 32 個潛在 32 的儲存位置）。

 `int``uint`和`float`全部都是隱含地轉換成`nint`，`nuint`和`nfloat`為 32 位元永遠符合 32 或 64 個位元。

 `nint``nuint`和`nfloat`全部都是隱含地轉換成`long`，`ulong`和`double`32 或 64 位元值一律填滿 64 位元儲存體中。

您必須使用明確的轉換，從`nint`，`nuint`和`nfloat`到`int`，`uint`和`float`因為原生型別可能會持有的儲存體的 64 位元。

您必須使用明確的轉換，從`long`，`ulong`和`double`到`nint`，`nuint`和`nfloat`因為可能只能夠保留的存放裝置的 32 位元的原生類型。

## <a name="coregraphics-types"></a>CoreGraphics 類型

搭配 CoreGraphics 點、 大小以及矩形的資料類型使用 32 或 64 位元，根據裝置執行。  當我們原本繫結 iOS 和 Mac 應用程式開發介面時我們會使用現有的資料結構以符合主機平台的大小發生 (中的資料類型`System.Drawing`)。

移至時**統一**，您必須取代的執行個體`System.Drawing`與其`CoreGraphics`對應項目下, 表所示：

<table>
        <tr>
            <th>System.Drawing 中的舊型別</th>
            <th>新的資料型別 CoreGraphics</th> 
            <th>描述</th>
        </tr>
        <tr>
        <td><code>RectangleF</code></td>
        <td><code>CGRect</code></td>
        <td>浮點數的保存點矩形資訊。  </td>
        </tr>
        <tr>
        <td><code>SizeF</code></td>
        <td><code>CGSize</code></td>
        <td>浮點數的保存點 （寬度、 高度） 的大小資訊</td>
        </tr>
        <tr>
        <td><code>PointF</code></td>
        <td><code>CGPoint</code></td>
        <td>保存浮點數、 點資訊 (X，Y)</td>
        </tr>
    </table>

新的其中一個使用舊的資料類型使用浮點數儲存的資料結構中，項目，而`System.nfloat`。

## <a name="related-links"></a>相關連結

- [使用跨平台應用程式中的原生類型](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
