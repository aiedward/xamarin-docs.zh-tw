---
title: 在跨平台應用程式中使用原生類型
description: 本文說明如何在跨平臺應用程式中使用新的 iOS Unified API 原生類型（nint、nuint、nfloat），其中程式碼與非 iOS 裝置（例如 Android 或 Windows Phone Os）共用。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: davidortinau
ms.author: daortin
ms.date: 04/07/2016
ms.openlocfilehash: c86a00f325f9799b16f6244d3d1cb68de31be005
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015531"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>在跨平台應用程式中使用原生類型

_本文說明如何在跨平臺應用程式中使用新的 iOS Unified API 原生類型（nint、nuint、nfloat），其中程式碼與非 iOS 裝置（例如 Android 或 Windows Phone Os）共用。_

64類型的原生類型適用于 iOS 和 Mac Api。 如果您要撰寫在 Android 或 Windows 上執行的共用程式碼，您必須管理將整合類型轉換成可共用的一般 .NET 類型。

本檔討論從您的共用/通用程式碼與 Unified API 交互操作的不同方式。

## <a name="when-to-use-the-native-types"></a>使用原生類型的時機

Xamarin 和 Xamarin 整合 Api 仍然包含 `int`、`uint` 和 `float` 資料類型，以及 `RectangleF`、`SizeF` 和 `PointF` 類型。 這些現有的資料類型應該繼續用於任何共用的跨平臺程式碼。 只有在呼叫 Mac 或 iOS API 時，才需要使用新的原生資料類型，其中必須支援架構感知類型。

視所共用程式碼的本質而定，跨平臺程式碼可能需要處理 `nint`、`nuint` 和 `nfloat` 資料類型。 例如：一個程式庫，它會處理先前使用 `System.Drawing.RectangleF` 的矩形資料的轉換，以在您的應用程式的 iOS 和 Xamarin Android 版本之間共用功能，需要更新以處理 iOS 上的原生類型。

這些變更的處理方式取決於應用程式的大小和複雜度，以及已使用的程式碼共用形式，如下列各節所示。

## <a name="code-sharing-considerations"></a>程式碼共用考慮

如[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)檔中所述，在跨平臺專案之間共用程式碼的主要方式有兩種：共用專案和可移植的類別庫。 已使用這兩種類型的哪一個，將會限制在跨平臺程式碼中處理原生資料類型時所擁有的選項。

### <a name="portable-class-library-projects"></a>可移植的類別庫專案

可移植的類別庫（PCL）可讓您以想要支援的平臺為目標，並使用介面提供平臺特定的功能。

因為 PCL 專案類型會編譯成 `.DLL`，而且沒有 Unified API 的意義，所以您會被迫繼續使用 PCL 原始程式碼中的現有資料類型（`int`、`uint`、`float`），並將呼叫轉換成 PCL 的類別前端應用程式中的和方法。 例如:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共用的專案

共用的資產專案類型可讓您在個別的專案中組織您的原始程式碼，然後將其包含並編譯成個別的平臺特定前端應用程式，並視需要使用 `#if` 編譯器指示詞來管理平臺特定的滿足.

在跨平臺中選擇原生資料類型的支援方法時，使用共用程式碼的前端行動應用程式大小和複雜度，以及所共用程式碼的大小和複雜度，都必須納入考慮共用的資產專案。

根據這些因素，可能會使用 `if __UNIFIED__ ... #endif` 編譯器指示詞來執行下列類型的解決方案，以處理常式代碼的 Unified API 特定變更。

#### <a name="using-duplicate-methods"></a>使用重複的方法

取得程式庫的範例，其會對上述的矩形資料進行轉換。 如果程式庫只包含一或兩個非常簡單的方法，您可以選擇為 Xamarin 和 Xamarin 建立這些方法的重複版本。 例如:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

在上述程式碼中，由於 `CalculateArea` 常式非常簡單，因此我們使用了條件式編譯，並建立了不同的 Unified API 版本方法。 另一方面，如果程式庫包含許多常式或數個複雜的常式，則此解決方案不可行，因為它會出現問題，讓所有方法保持同步，以進行修改或錯誤修正。

#### <a name="using-method-overloads"></a>使用方法多載

在此情況下，解決方案可能會使用32位資料類型來建立方法的多載版本，因此現在會將 `CGRect` 做為參數和/或傳回值，將該值轉換為 `RectangleF` （瞭解從 `nfloat` 轉換成 `float`是損失真轉換），並呼叫原始版本的常式來執行實際工作。 例如:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

同樣地，這是很好的解決方案，只要遺失有效位數並不會影響應用程式特定需求的結果即可。

#### <a name="using-alias-directives"></a>使用 Alias 指示詞

對於遺失有效位數的區域是問題，另一個可能的解決方法是使用 `using` 指示詞來建立原生和 CoreGraphics 資料類型的別名，其方式是將下列程式碼包含在共用原始程式碼檔案的頂端，並轉換任何需要的 `int`、`uint` 或 `float` 值，以 `nint`、`nuint` 或 `nfloat`：

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

然後，我們的範例程式碼就會變成：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

請注意，這裡我們變更了 `CalculateArea` 方法，以傳回 `nfloat`，而不是標準 `float`。 這麼做的原因是，我們不會在嘗試_隱含地_轉換計算的 `nfloat` 結果（因為這兩個值都是 `nfloat`的類型）轉換成 `float` 傳回值時，不會收到編譯錯誤。

如果程式碼是在非 Unified API 的裝置上編譯並執行，則 `using nfloat = global::System.Single;` 會將 `nfloat` 對應至 `Single`，這會隱含地轉換成 `float`，讓使用中的前端應用程式不需要修改即可呼叫 `CalculateArea` 方法。

#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端應用程式中使用類型轉換

如果您的前端應用程式只會對共用程式碼程式庫進行少數呼叫，另一個方案可能是讓程式庫保持不變，並在呼叫現有的常式時，于 Xamarin 或 Xamarin 應用程式中進行類型轉換。 例如:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果取用應用程式對共用程式碼程式庫進行數百次呼叫，這可能不是很好的解決方案。

根據我們的應用程式架構，我們最後可能會使用一或多個上述解決方案來支援跨平臺程式碼中的原生資料類型（如有需要）。

## <a name="xamarinforms-applications"></a>Xamarin. Forms 應用程式

以下是針對跨平臺 Ui （也會與 Unified API 應用程式共用）使用 Xamarin 的必要項：

- 整個解決方案必須使用1.3.1 （或更高版本）的 Xamarin NuGet 套件。
- 針對任何 Xamarin 自訂轉譯，請根據 UI 程式碼的共用方式（共用的專案或 PCL），使用上面所述的相同方案類型。

如同在標準的跨平臺應用程式中，在大多數情況下，都應該在任何共用的跨平臺程式碼中使用現有的32位資料類型。 只有在呼叫 Mac 或 iOS API 時，才需要使用新的原生資料類型，其中必須支援架構感知類型。

如需詳細資訊，請參閱[更新現有的 Xamarin 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)檔。

## <a name="summary"></a>總結

在本文中，我們已瞭解如何在 Unified API 應用程式中使用原生資料類型，以及其跨平臺的影響。 我們已提供數個解決方案，可用於必須在跨平臺程式庫中使用新原生資料類型的情況。 此外，我們也看到了在 Xamarin 中支援統一 Api 的快速指南。表單跨平臺應用程式。

## <a name="related-links"></a>相關連結

- [Unified API](~/cross-platform/macios/unified/index.md)
- [原生型別](~/cross-platform/macios/nativetypes.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [程式碼共用範例](https://docs.microsoft.com/samples/xamarin/mobile-samples/sharingcode/)
