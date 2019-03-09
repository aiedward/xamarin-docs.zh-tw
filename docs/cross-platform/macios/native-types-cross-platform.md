---
title: 在跨平台應用程式中使用原生類型
description: 這篇文章說明如何使用新的 iOS Unified API 原生型別 （nint、 nuint nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統，其中共用程式碼。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 489d2a76e6eff661360b24d1872ed1343c74b85e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667213"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>在跨平台應用程式中使用原生類型

_這篇文章說明如何使用新的 iOS Unified API 原生型別 （nint、 nuint nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統，其中共用程式碼。_


64-型別原生型別是由 iOS 和 Mac Api 所使用。 如果您要撰寫共用程式碼，以及在 Android 或 Windows 上執行，您必須管理的整合型別轉換成一般的.NET 類型，您可以共用。

本文件討論不同的方式，與統一的 API，從您的共用/一般程式碼交互操作。

## <a name="when-to-use-the-native-types"></a>使用原生類型的時機

Xamarin.iOS 和 Xamarin.Mac 統一 Api 仍然包含`int`，`uint`並`float`資料類型，以及`RectangleF`，`SizeF`和`PointF`型別。 這些現有的資料型別應該繼續用於任何共用的跨平台程式碼。 其中的架構可感知的型別所支援的 Mac 或 iOS 的 API 呼叫時，應該只使用新的原生資料類型。

根據共用的程式碼性質，有時可能是跨平台程式碼可能需要應付`nint`，`nuint`和`nfloat`資料型別。 例如： 處理先前使用的矩形資料的轉換程式庫`System.Drawing.RectangleF`若要共用的應用程式，Xamarin.iOS 和 Xamarin.Android 版本之間的功能需要更新，以處理在 iOS 上的原生型別。

處理這些變更的方式而定的大小和複雜度的應用程式，且共用的程式碼的表單已用，我們會看到後續幾節。

## <a name="code-sharing-considerations"></a>程式碼共用的考量

中所述[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)文件時，有兩種主要的方式，跨平台專案間共用程式碼：專案或可攜式類別程式庫共用。 這兩種類型的已使用，將會限制處理跨平台程式碼中的原生資料類型時，我們會有的選項。

### <a name="portable-class-library-projects"></a>可攜式類別庫專案

可攜式類別庫 (PCL) 可讓您以您想要支援，並使用介面來提供特定平台功能的平台為目標。

因為 PCL 專案型別會編譯至`.DLL`它有沒有任何意義的統一的 API，您必須自行以繼續使用現有的資料型別 (`int`， `uint`， `float`) 在 PCL 中的原始程式碼和型別轉型 Pcl 的呼叫類別和方法的前端應用程式中。 例如：

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共用的專案

共用資產專案類型可讓您將您在個別的專案，然後取得包含編譯的原始程式碼組織成個別的平台特定前端應用程式，並使用`#if`編譯器指示詞為必要項目管理平台特定的需求。

大小和複雜度，前面的結尾會取用共用程式碼，以及大小和共用的程式碼的複雜度，必須選擇的方法支援原生資料類型與跨平台時，會納入考量的行動裝置應用程式共用的資產專案型別。

根據這些因素，下列幾種解決方案可能會使用實作`if __UNIFIED__ ... #endif`編譯器指示詞，可處理的統一 API 的特定變更的程式碼。

#### <a name="using-duplicate-methods"></a>使用重複的方法

看執行上述的矩形資料的轉換程式庫的範例。 如果程式庫僅包含一個或兩個非常簡單的方法，您可以選擇為 Xamarin.iOS 和 Xamarin.Android 建立重複的版本，這些方法。 例如: 

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

在上述程式碼中，由於`CalculateArea`常式非常簡單，我們使用條件式編譯，並建立個別、 統一的 API 版本的方法。 相反地，如果程式庫包含許多常式或數個複雜的常式，此解決方案不會可行，因為它會顯示保留的所有方法修改或 bug 修正的同步處理的問題。

#### <a name="using-method-overloads"></a>使用方法多載

在此情況下，解決方案可能會建立使用 32 位元資料類型，如此一來，他們現在所採取的方法多載版本`CGRect`做為參數及/或傳回值，這個值轉換成`RectangleF`(了解從該轉換`nfloat`至`float`失真的轉換)，並呼叫常式，以執行實際工作的原始版本。 例如: 

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

遺失有效位數與問題所在的區域，另一個可能的解決方案是使用`using`指示詞來建立原生和 CoreGraphics 資料類型的別名包括下列的程式碼，來共用原始碼程式碼檔案的頂端和任何轉換所需`int`，`uint`或是`float`值來`nint`，`nuint`或`nfloat`:

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

請注意，這裡我們已變更`CalculateArea`方法會傳回`nfloat`而不是標準`float`。 這項作業完成，因此我們不會產生編譯錯誤，嘗試_隱含地_轉換`nfloat`我們的計算結果 (因為這兩個要相乘的值是`nfloat`) 到`float`傳回值。

如果程式碼進行編譯和執行非統一的 API 在裝置上，`using nfloat = global::System.Single;`對應`nfloat`要`Single`它會隱含地轉換成`float`允許取用的前端應用程式以呼叫`CalculateArea`方法，而不修改。


#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端應用程式中使用類型轉換

您的前端應用程式只讓少數幾個對您的共用程式碼程式庫呼叫，另一個解決方案是為了保留不變的程式庫，並呼叫現有的常式時，執行類型轉型 Xamarin.iOS 或 Xamarin.Mac 應用程式中。 例如：

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

若使用應用程式會執行數百個呼叫共用程式碼程式庫，這同樣地，可能不是很好的解決方案。

根據我們的應用程式架構，我們最後可能會使用一或多個上述的解決方案，以支援原生資料類型 （如有需要），在我們的跨平台程式碼。


## <a name="xamarinforms-applications"></a>Xamarin.Forms 應用程式

需要下列項目也會與統一的 API 應用程式共用的跨平台 Ui 中用於 Xamarin.Forms:

- 整個解決方案必須使用 1.3.1 版 （或以上） 的 Xamarin.Forms NuGet 套件。
- 相同類型的解決方案以上所顯示的任何 Xamarin.iOS 的自訂轉譯器，根據如何 UI 程式碼已共用 （共用專案或 PCL） 的使用。

如同標準的跨平台應用程式中，現有的 32 位元資料類型應該用於任何共用的跨平台程式碼中大部分的所有情況。 其中的架構可感知的型別所支援的 Mac 或 iOS 的 API 呼叫時，應該只使用新的原生資料類型。

如需詳細資訊，請參閱我們[更新現有的 Xamarin.Forms 應用程式](https://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/)文件。

## <a name="summary"></a>總結

在本文中我們有，請參閱，當我們應該使用原生資料類型的統一 API 應用程式和其影響跨平台。 我們有提供數個可以用於跨平台程式庫中必須使用新的原生資料類型的情況下的解決方案。 此外，我們已了解 Xamarin.Forms 跨平台應用程式中支援 Unified Api 的快速指南。



## <a name="related-links"></a>相關連結

- [Unified API](~/cross-platform/macios/unified/index.md)
- [原生型別](~/cross-platform/macios/nativetypes.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [程式碼共用範例](https://developer.xamarin.com/samples/mobile/SharingCode/)
