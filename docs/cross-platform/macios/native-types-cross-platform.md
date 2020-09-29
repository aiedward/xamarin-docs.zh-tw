---
title: 在跨平台應用程式中使用原生類型
description: 本文說明如何在跨平臺應用程式中使用新的 iOS Unified API 原生類型 (nint、nuint、nfloat) ，其中程式碼與 Android 或 Windows Phone 作業系統等非 iOS 裝置共用。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: davidortinau
ms.author: daortin
ms.date: 04/07/2016
ms.openlocfilehash: 4819f8fc88a8a2c730fc25215e862d68cc5bd643
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457285"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>在跨平台應用程式中使用原生類型

_本文說明如何在跨平臺應用程式中使用新的 iOS Unified API 原生類型 (nint、nuint、nfloat) ，其中程式碼與 Android 或 Windows Phone 作業系統等非 iOS 裝置共用。_

64類型的原生類型適用于 iOS 和 Mac Api。 如果您要撰寫在 Android 或 Windows 上執行的共用程式碼，您必須管理將整合型別轉換成可共用的一般 .NET 類型。

本檔將討論不同的方法，從您的共用/通用程式碼與 Unified API 交互操作。

## <a name="when-to-use-the-native-types"></a>使用原生類型的時機

Xamarin 和 Xamarin 整合 Api 仍包含 `int` 、 `uint` 和 `float` 資料類型，以及 `RectangleF` 、 `SizeF` 和 `PointF` 類型。 這些現有的資料類型應該繼續在任何共用的跨平臺程式碼中使用。 只有在呼叫支援架構感知類型的 Mac 或 iOS API 時，才應該使用新的原生資料類型。

視共用的程式碼本質而定，可能會有一些可能需要跨平臺程式碼處理 `nint` 、 `nuint` 和 `nfloat` 資料類型的情況。 例如：在先前用 `System.Drawing.RectangleF` 來在 xamarin 和 xamarin. Android 版應用程式之間共用功能的矩形資料處理轉換的程式庫，必須更新以處理 iOS 上的原生類型。

這些變更的處理方式取決於應用程式的大小和複雜度，以及已使用的程式碼共用格式，如以下各節所示。

## <a name="code-sharing-considerations"></a>程式碼共用考慮

如同 [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md) 檔中所述，有兩種主要方式可在跨平臺專案之間共用程式碼：共用專案和可移植類別庫。 已使用這兩種類型的哪一種，將會限制在跨平臺程式碼中處理原生資料類型時所擁有的選項。

### <a name="portable-class-library-projects"></a>可移植類別庫專案

便攜類別庫 (PCL) 可讓您以想要支援的平臺為目標，並使用介面來提供平臺特定的功能。

由於 pcl 專案類型會編譯為 `.DLL` ，而且沒有 Unified API 的意義，因此您必須繼續使用現有的資料類型 (`int` 、 `uint` 、 `float`) 在 pcl 原始程式碼和類型中，將對 pcl 的類別和方法的呼叫轉換為前端應用程式。 例如：

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共用的專案

共用的資產專案類型可讓您在個別的專案中組織您的原始程式碼，該專案接著會包含並編譯成個別的平臺專屬前端應用程式，並 `#if` 視需要使用編譯器指示詞來管理平臺特定需求。

在跨平臺共用資產專案中選擇支援原生資料類型的方法時，需要考慮使用共用程式碼的前端行動應用程式大小和複雜度，以及共用程式碼的大小和複雜度。

根據這些因素，可能會使用編譯器指示詞來執行下列類型的解決方案， `if __UNIFIED__ ... #endif` 以處理 Unified API 程式碼的特定變更。

#### <a name="using-duplicate-methods"></a>使用重複的方法

取得在上述矩形資料上執行轉換的程式庫範例。 如果程式庫只包含一或兩個非常簡單的方法，您可以選擇針對 Xamarin 和 Xamarin 建立這些方法的重複版本。 例如：

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

在上述程式碼中，因為 `CalculateArea` 常式非常簡單，所以我們使用條件式編譯，並建立不同的 Unified API 版本的方法。 另一方面，如果程式庫包含許多常式或數個複雜的常式，此解決方案就不可行，因為這會導致所有方法保持同步以進行修改或錯誤修正的問題。

#### <a name="using-method-overloads"></a>使用方法多載

在這種情況下，解決方案可能會使用32位的資料類型來建立方法的多載版本，因此現在會以 `CGRect` 參數和/或傳回值的形式，將該值轉換為 `RectangleF` (知道從轉換為的 `nfloat` 會造成失真 `float` 轉換) ，然後呼叫原始版本的常式來執行實際工作。 例如：

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

同樣地，這是一個不錯的解決方案，只要遺失精確度並不會影響您應用程式特定需求的結果即可。

#### <a name="using-alias-directives"></a>使用 Alias 指示詞

對於遺失精確度是問題的區域，另一個可能的解決方法是使用指示詞 `using` 來建立原生和 CoreGraphics 資料類型的別名，方法是將下列程式碼包含在共用原始程式碼檔的頂端，並將任何必要 `int` 的 `uint` 或值轉換 `float` 為 `nint` `nuint` 或 `nfloat` ：

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

因此，我們的範例程式碼會變成：

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

請注意，我們已將 `CalculateArea` 方法變更為傳回， `nfloat` 而不是標準 `float` 。 這樣做的原因是，我們不會在嘗試 _隱含_ 轉換計算 (的結果時收到編譯錯誤， `nfloat` 因為兩個值相乘都是 `nfloat`) 成為傳回值的類型 `float` 。

如果程式碼是在非 Unified API 裝置上編譯和執行，則 `using nfloat = global::System.Single;` 會將對應 `nfloat` 至， `Single` 這會將隱含轉換為， `float` 讓取用前端應用程式 `CalculateArea` 不需要修改就能呼叫方法。

#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端應用程式中使用類型轉換

如果您的前端應用程式只對您的共用程式碼程式庫進行少數呼叫，另一個解決方案可能是讓程式庫保持不變，並在呼叫現有的常式時，于 Xamarin 或 Xamarin. Mac 應用程式中進行類型轉換。 例如：

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果取用應用程式對共用程式碼程式庫進行數百次呼叫，則這可能不是理想的解決方案。

根據我們的應用程式架構，最後可能會使用一或多個上述解決方案來支援原生資料類型 (在我們的跨平臺程式碼中需要) 。

## <a name="xamarinforms-applications"></a>Xamarin. Forms 應用程式

若要使用適用于跨平臺 Ui 的 Xamarin，且也會與 Unified API 應用程式共用，您必須具備下列各項：

- 整個解決方案必須使用版本 1.3.1 (或更高版本的 Xamarin NuGet 套件) 。
- 針對任何 Xamarin. iOS 自訂轉譯，請根據 UI 程式碼共用 (共用專案或 PCL) 的方式，使用上面所提供的相同類型解決方案。

如同在標準的跨平臺應用程式中，在大部分情況下，應該在任何共用的跨平臺程式碼中使用現有的32位資料類型。 只有在呼叫支援架構感知類型的 Mac 或 iOS API 時，才應該使用新的原生資料類型。

如需詳細資訊，請參閱 [更新現有的 Xamarin 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) 檔。

## <a name="summary"></a>摘要

在本文中，我們已瞭解如何在 Unified API 應用程式中使用原生資料類型，以及其對跨平臺的影響。 我們已提供數個解決方案，可用於新的原生資料類型必須在跨平臺程式庫中使用的情況。 此外，我們也已在 Xamarin 的跨平臺應用程式中，看到支援整合 Api 的快速指南。

## <a name="related-links"></a>相關連結

- [Unified API](~/cross-platform/macios/unified/index.md)
- [原生類型](~/cross-platform/macios/nativetypes.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [程式碼共用範例](/samples/xamarin/mobile-samples/sharingcode/)