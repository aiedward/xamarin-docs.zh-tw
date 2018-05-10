---
title: 使用跨平台應用程式中的原生類型
description: 本文說明如何使用新的 iOS 原生整合應用程式開發介面的型別 （nint、 nuint、 nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統版本之共用程式碼的位置。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>使用跨平台應用程式中的原生類型

_本文說明如何使用新的 iOS 原生整合應用程式開發介面的型別 （nint、 nuint、 nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統版本之共用程式碼的位置。_


64 類型的原生型別使用 iOS 和 Mac 應用程式開發介面。 如果您正在撰寫共用也在 Android 或 Windows 執行的程式碼，您將需要管理整合類型轉換，您可以共用一般.NET 型別。

本文將討論不同的方式，與統一的 API，從您的一般共用程式碼交互操作。

## <a name="when-to-use-the-native-types"></a>使用原生類型的時機

Xamarin.iOS 和 Xamarin.Mac 統一的 Api 仍然包含`int`，`uint`和`float`資料型別，並將`RectangleF`，`SizeF`和`PointF`型別。 這些現有的資料類型應該繼續使用任何共用的跨平台程式碼中。 新的原生資料類型應該只用於其中的可感知架構的型別所需支援 Mac 或 iOS 應用程式開發介面呼叫時。

根據共用的程式碼的本質，有些時候可能會跨平台程式碼可能需要處理`nint`，`nuint`和`nfloat`資料型別。 例如： 處理先前使用的矩形資料轉換程式庫`System.Drawing.RectangleF`Xamarin.iOS 和 Xamarin.Android 版本的應用程式之間共用功能將需要更新，以處理在 iOS 上的原生類型。

如何處理這些變更相依於的大小和複雜度的應用程式，而共用的程式碼的表單已使用，我們將會看到以下幾節中。

## <a name="code-sharing-considerations"></a>程式碼共用考量

中所述[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)文件中，有兩種主要的方式，跨平台專案之間共用程式碼： 共用專案和可攜式類別庫。 這兩種類型已在使用，將會限制處理跨平台程式碼中的原生資料類型時，我們有的選項。

### <a name="portable-class-library-projects"></a>可攜式類別庫專案

可攜式類別程式庫 (PCL) 可讓您以您想要支援，及使用介面來提供特定平台功能的平台為目標。

因為 PCL 專案類型會編譯至`.DLL`和它有沒有意義的統一的 API 時，您將會強制為繼續使用現有的資料型別 (`int`， `uint`， `float`) PCL 中，原始程式碼和類型轉換 PCLs 呼叫類別和方法在前端應用程式中。 例如：

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共用的專案

共用資產專案類型可讓您將在個別的專案，然後取得包含編譯您的原始程式碼組織成個別的平台專屬前端應用程式中，並使用`#if`為必要項目來管理的編譯器指示詞特定平台需求。

大小和複雜度前端結束正在耗用共用的程式碼，以及大小和共用的程式碼的複雜度，必須選擇方法的支援原生資料類型與跨平台時加以考量的行動應用程式共用的資產專案的類型。

根據這些因素，下列類型的方案可能的實作使用`if __UNIFIED__ ... #endif`處理統一的 API 的程式碼的特定變更的編譯器指示詞。

#### <a name="using-duplicate-methods"></a>使用重複的方法

需要的程式庫所執行的動作矩形上面所列的資料轉換的範例。 如果文件庫只包含一個或兩個非常簡單的方法，您可能會選擇 Xamarin.iOS 和 Xamarin.Android 建立重複的版本，這些方法。 例如: 

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

在上述程式碼中，因為`CalculateArea`常式是非常簡單，我們已經使用條件式編譯，並建立個別、 統一的 API 版本的方法。 相反地，如果文件庫包含許多常式或數個複雜的常式，此解決方案不會可行，因為它會提供保留的所有方法修改或 bug 修正的同步處理的問題。

#### <a name="using-method-overloads"></a>使用方法多載

在此情況下，方案可能要建立的讓這些現在使用 32 位元資料類型的方法多載版本`CGRect`做為參數及/或傳回值，這個值轉換成`RectangleF`(了解從該轉換`nfloat`至`float`失真格式進行轉換)，並呼叫常式，以執行實際工作的原始版本。 例如: 

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

同樣地，這是很好的解決方案，只要遺失有效位數並不會影響您的應用程式的特定需求的結果。

#### <a name="using-alias-directives"></a>Using 別名指示詞

遺失有效位數的問題區域，對於其他可能的解決方案是使用`using`指示詞，可將下列程式碼加入共用的原始程式碼檔案的頂端，並將任何轉換來建立原生和 CoreGraphics 資料類型別名需要`int`，`uint`或`float`值`nint`，`nuint`或`nfloat`:

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

因此，我們的範例程式碼就會變成：

```csharp
using System;
using System.Drawing;

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

請注意，我們已在這裡變更`CalculateArea`方法傳回`nfloat`而不是標準`float`。 這項作業完成，因此我們不會收到編譯錯誤嘗試_隱含_轉換`nfloat`我們計算的結果 (因為這兩個要相乘的值為`nfloat`) 到`float`傳回值。

如果程式碼進行編譯和執行非統一的 API 在裝置上，`using nfloat = global::System.Single;`對應`nfloat`至`Single`其中會隱含地轉換至`float`允許使用的前端應用程式呼叫`CalculateArea`不含方法進行修改。


#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端應用程式中使用類型轉換

前端應用程式只提供少數幾個呼叫您的共用程式碼程式庫，另一個解決方案可能是將保持未變更的程式庫，呼叫現有常式時，請勿輸入轉型 Xamarin.iOS 或 Xamarin.Mac 應用程式中。 例如：

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果使用的應用程式提出數百個呼叫的共用程式碼程式庫，這同樣地，可能不是很好的解決方案。

根據我們的應用程式架構，我們最後可能會使用一或多個上述方案支援原生資料類型 （若有必要） 在我們的跨平台程式碼中。


## <a name="xamarinforms-applications"></a>Xamarin.Forms 應用程式

需要下列項目 Xamarin.Forms 用於跨平台 Ui，也能與統一的 API 應用程式共用：

- 整個方案必須使用 1.3.1 版 （或更高） 的 Xamarin.Forms NuGet 套件。
- 對於任何 Xamarin.iOS 組成的自訂轉譯器，使用相同類型的上述方案如何 UI 程式碼已共用 （共用專案或 PCL） 為基礎。

如同標準的跨平台應用程式中，現有的 32 位元資料類型應該用於任何共用的跨平台程式碼中大部分的所有情況。 新的原生資料類型應該只用於其中的可感知架構的型別所需支援 Mac 或 iOS 應用程式開發介面呼叫時。

如需詳細資訊，請參閱我們[更新現有 Xamarin.Forms 應用程式](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/)文件。

## <a name="summary"></a>總結

在這篇文章中有，請參閱當我們應該使用中的統一的 API 應用程式和其含意跨平台的原生資料類型。 我們提供數個可用於跨平台程式庫中必須使用新的原生資料類型的情況下的解決方案。 我們已看到 Xamarin.Forms 跨平台應用程式中支援統一的 Api 的快速指南。



## <a name="related-links"></a>相關連結

- [Unified API](~/cross-platform/macios/unified/index.md)
- [原生型別](~/cross-platform/macios/nativetypes.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [程式碼共用的範例](https://developer.xamarin.com/samples/mobile/SharingCode/)
