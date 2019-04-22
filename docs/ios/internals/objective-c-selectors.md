---
title: 在 Xamarin.iOS 中的 OBJECTIVE-C 選取器
description: 這份文件會討論如何從 C# 的 OBJECTIVE-C 選取器和互動。 它說明如何叫用選取器和這麼做時必須列入考量的技術考量。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: 15db59945f482728f760006095e294bc5628c8bd
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870168"
---
# <a name="objective-c-selectors-in-xamarinios"></a>在 Xamarin.iOS 中的 OBJECTIVE-C 選取器

Objective C 語言為基礎*選取器*。 選取器是可以傳送至物件的訊息，或*類別*。 [Xamarin.iOS](~/ios/internals/api-design/index.md)對應執行個體選取器來執行個體方法和類別靜態方法的選取器。

不同於一般的 C 函式 (和C++成員函式)，您無法直接叫用選取器，使用[P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/)相反地，傳送到 OBJECTIVE-C 類別選取器，或執行個體 [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
函式。

如需有關在 OBJECTIVE-C 中的訊息的詳細資訊，請查看 Apple[使用物件](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2)指南。

## <a name="example"></a>範例

假設您想要叫用 [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
上的選取器[ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring)。
（來自 Apple 的文件） 宣告為：

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

此 API 具有下列特性：

- 傳回的型別是`CGSize`統一的 api。
- `font`參數是[UIFont](xref:UIKit.UIFont) (和型別 （間接） 衍生自[NSObject](xref:Foundation.NSObject)，且對應至[System.IntPtr](xref:System.IntPtr)。
- `width`參數， `CGFloat`，會對應至`nfloat`。
- `lineBreakMode`參數， [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc)，具有已繫結中為 Xamarin.iOS [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
列舉型別。

總結，`objc_msgSend`應該符合宣告：

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

進行宣告，如下所示：

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要呼叫這個方法，使用程式碼如下所示：

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

傳回的值已經是大小小於 8 個位元組的結構 (例如較舊`SizeF`之前切換至 Unified Api 使用)，上述程式碼方式但損毀的裝置上的模擬器上執行。 若要呼叫的傳回值的大小小於 8 位元的選取器，宣告`objc_msgSend_stret`函式：

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要呼叫這個方法，使用程式碼如下所示：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>叫用選取器

叫用選取器有三個步驟：

1. 取得選取器的目標。
2. 取得選取器名稱。
3. 呼叫`objc_msgSend`搭配適當的引數。

### <a name="selector-targets"></a>選取器目標

物件執行個體或 OBJECTIVE-C 類別選取器的目標。 如果目標是執行個體，而且來自繫結的 Xamarin.iOS 型別，使用[ `ObjCRuntime.INativeObject.Handle` ](xref:ObjCRuntime.INativeObject.Handle)屬性。

如果目標類別，請使用[ `ObjCRuntime.Class` ](xref:ObjCRuntime.Class)若要取得類別執行個體的參考，然後使用[ `Class.Handle` ](xref:ObjCRuntime.Class.Handle)屬性。

### <a name="selector-names"></a>選取器名稱

選取器名稱會列在 Apple 的文件。 例如， [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc)包括[ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc)並[ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc)選取器。 內嵌和尾端冒號選取器名稱的一部分，而且不能省略。

選取器名稱之後，您可以建立[ `ObjCRuntime.Selector` ](xref:ObjCRuntime.Selector)為它的執行個體。

### <a name="calling-objcmsgsend"></a>Calling objc_msgSend

`objc_msgSend` 傳送訊息 （選擇器） 的物件。 這一系列的函式會採用兩個以上的必要引數: （執行個體或處理的類別） 的選取器目標、 選取器本身及選取器所需的任何引數。 執行個體和選取器引數必須是`System.IntPtr`，而且所有剩餘的引數必須符合選取器所預期，例如類型`nint`for `int`，或`System.IntPtr`所有`NSObject`-衍生型別。 使用 [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
屬性，以取得`IntPtr`Objective C 類型執行個體。

有一個以上`objc_msgSend`函式：

- 使用[ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)對傳回結構的選取器。 在 ARM 中，這包括所有傳回類型沒有列舉型別或任何 C 內建類型 (`char`， `short`， `int`， `long`， `float`， `double`)。 On x86 （模擬器），這個方法需要用於大於 8 個位元組大小的所有結構 (`CGSize`是 8 個位元組，但未使用`objc_msgSend_stret`在模擬器中)。 
- 使用[ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc)傳回浮點的選取器在僅限 x86 上點值。 此函式不需要在 ARM; 上使用請改用`objc_msgSend`。 
- 主[objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)函式用於其他所有的選取器。

一旦決定這`objc_msgSend`您必須呼叫的函式 （模擬器和行動裝置可能每個需要不同的方法），您可以使用一般[ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute)宣告稍後的引動過程的函式的方法。

一組預先製作`objc_msgSend`宣告可在`ObjCRuntime.Messaging`。

## <a name="different-invocations-on-simulator-and-device"></a>在模擬器和裝置上的不同引動過程

OBJECTIVE-C 如上面所述，有三種類型的`objc_msgSend`方法： 一個用於規則的引動過程，一個用於引動過程傳回浮點值 (僅限 x86)，，一個用於傳回結構值的引動過程。 後者包含尾碼`_stret`在`ObjCRuntime.Messaging`。

如果您叫用的方法，將會傳回特定的結構 （如下所述的規則），您必須叫用方法的傳回值的第一個參數為`out`值：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

使用時機的規則`_stret_`x86 和 ARM 上不同的方法。
若要讓您在模擬器和裝置上的繫結，加入程式碼如下所示：

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>使用 objc_msgSend_stret 方法

針對 ARM 建置時，使用 [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
針對任何不是列舉型別或任何列舉類型的基底類型的實值類型 (`int`， `byte`， `short`， `long`， `double`， `float`)。

針對 x86 建置時，使用 [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
針對任何不是列舉型別或任何列舉類型的基底類型的實值類型 (`int`， `byte`， `short`， `long`， `double`， `float`) 和原生大小大於 8 個位元組。

### <a name="creating-your-own-signatures"></a>建立您自己的簽章

下列[gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)可用來建立您自己的簽章，如有必要。

## <a name="related-links"></a>相關連結

- [OBJECTIVE-C 選取器](https://developer.xamarin.com/samples/mac-ios/Objective-C/)範例
